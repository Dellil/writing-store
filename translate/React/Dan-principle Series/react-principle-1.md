# Principle 1: Don't Stop the Data Flow
### 2020/07/27
##### *이 글 써진 건 좀 옛날인 거 같은데 직접 보고 번역한 건 2020년이라 괴리감이 있을 수도 있음*
멈추지않는 데이터 흐름은 필수입니다. 렌더링, 사이드이펙트, 최적화와 같은 곳은 말이죠
## 멈추지않고 데이터를 계속 흐르게하라 - 렌더링
제가 만든 컴포넌트를 누군가 쓸 때, 그는 한 가지 희망을 품습니다.
시간이 지날 때마다 다른 props를 전달 할 수 있을거라구요. 그리고 그 컴포넌트가 변경 사항들을 반영할 거 라고 말이죠
```JSX
// isOK는 상태를 기반으로 하고 있으며 언제든지 바뀔수 있을겁니다.
<Button color={isOk ? ‘blue’ : ‘red’} />
```

보통, 리액트는 기본작동방식은 이렇습니다.

만약 Button 컴포넌트 안에다 color prop을 사용하게 된다면, 위에서 받아온 값을 렌더할 때 볼 수 있게됩니다.
```JSX
function Button({ color, children }) {

return (
// ✅ `color` is always fresh!
	<button className={‘Button-’ + color}>
		{children}
	</button>
	);
}
```
하지만 리액트를 배울때 하는 실수는 props를 state 안에다 복사하는 것입니다.
```JSX
class  Button  extends  React.Component {
	state = {
		color: this.props.color
	};

	render() {
		const { color } = this.state; // 🔴 `color` is stale!
		return (
			<button className={'Button-' + color}>
				{this.props.children}
			</button>
		);
	}
}
```
리액트 말고 일반 클래스를 사용하여 JS로 코딩한 사람이 이걸 보면 직관적으로 보일지 모르겠습니다만, prop을 state로 복사를 하는 것은 prop와 state에 관한 모든 업데이트를 무시하게 되는 셈입니다.
```JSX
// 🔴 No longer works for updates with the above implementation
<Button color={isOk ? 'blue' : 'red'} />
```
의도적으로 이 동작을 하고싶다면, initialColor 혹은 defaultColor로 prop을 호출하여 변경사항이 변경되는지 확인해보세요.

하지만 컴포넌트안에 바로 props을 읽고싶다면 state에 바로 props를 복사하는건 피하세요.
```JSX
function Button({ color, children }) {
	return (
	// ✅ `color` is always fresh!
		<button className={‘Button-’ + color}>
			{children}
		</button>
	);
}
```
가끔 사람들이 다른 이유로 state안에다 props를 복사하려고 합니다. (계산된 값때문에)

button text color는 “인자로 넘어온 background color”로 하는 매우매우 비싼 계산을 기반으로 결정된다고 예를 들어보겠습니다.
```JSX
class  Button  extends  React.Component {
	state = {
		textColor: slowlyCalculateTextColor(this.props.color)
	};

	render() {
		return (
			<button className={
			'Button-' + this.props.color +
			' Button-text-' + this.state.textColor 
			// 🔴 Stale on `color` prop updates
			}>
				{this.props.children}
			</button>
		);
	}
}
```
위 컴포넌트는 color prop이 변경되도, state의 textColor는 재계산 되지 않습니다.

이걸 가장 쉽게 고치는 법은 textColor 계산을 render method 아래로 옮기면서 PureComponent로 만들어주는 것입니다.
```JSX
class  Button  extends  React.PureComponent {
	render() {
		const textColor = slowlyCalculateTextColor(this.props.color);
		return (
			<button className={
			'Button-' + this.props.color +
			' Button-text-' + textColor // ✅ Always fresh
			}>
				{this.props.children}
			</button>
		);
	}
}
```
props가 변경되면 textColor가 재계산되므로 문제가 해결되었습니다.

하지만 우린 같은 props의 비싼 계산은 피할 것이기때문에, 이 컴포넌트를 좀 더 최적화 하고싶습니다.

만약 children prop가 변경된거라면? 컴포넌트가 불쌍해 보입니다. textColor를 재계산 해야되니까요

따라서 우리의 두번째 시도는 componentDidUpdate에 계산을 호출하는 것입니다.
```JSX
class  Button  extends  React.Component {
	state = {
		textColor: slowlyCalculateTextColor(this.props.color)
	};
	componentDidUpdate(prevProps) {
		if (prevProps.color !== this.props.color) {
		// 😔 Extra re-render for every update
			this.setState({
				textColor: slowlyCalculateTextColor(this.props.color),
			});
		}
	}
	render() {
		return (
			<button className={
			'Button-' + this.props.color +
			' Button-text-' + this.state.textColor 
			// ✅ Fresh on final render
			}>
				{this.props.children}
			</button>
		);
	}
}
```
그런데 이건 변경 될때마다 다시 렌더링을 수행합니다. 이것까지 최적화하기에는 좀 그렇네요.

최적화를 위해 componentWillReceiveProps 라이프사이클 legacy를 사용할 수도 있겠습니다만, 사람들이 거기에 side effect를 집어넣는 경향이 있습니다.
#####  *(최적화를 위해 사용되는 것 말고도, 다른 동작을 하는 함수를 넣는 사람들이 있다는 의미)*

이렇게 되면, 곧 업데이트 될 time slicing과 suspense와 같은 동시 렌더링에 문제가 생깁니다.
그리고 안전한 getDerivedStateFromProps 메소드는 투박합니다.

잠시 뒤로 물러나봅시다. 실제로 우리가 원하는 것은 memoization입니다. 어떤 입력(input)이 있고, 입력이 변경되지 않는 한 출력(output)이 변경되지 않았으면 합니다.

클래스라면 헬퍼([helper](https://reactjs.org/blog/2018/06/07/you-probably-dont-need-derived-state.html?ck_subscriber_id=929826843#what-about-memoization))를 사용 할 수 있습니다만, hooks는 비싼 계산을 memoize해주는 방법을 빌트인으로 제공해주는 방향으로 더 발전했습니다.
```JSX
function  Button({ color, children }) {
	const textColor = useMemo(
		() => slowlyCalculateTextColor(color),
		[color] // ✅ Don’t recalculate until `color` changes
	);
	return (
		<button className={'Button-' + color + ' Button-text-' + textColor}>
			{children}
		</button>
	);
}
```
이러면 끝입니다.

클래스 컴포넌트는 memoize-one같은 헬퍼를 사용할 수 있으며, 함수형 컴포넌트에서 useMemo훅으로 비슷한 기능을 제공합니다.

지금까지 우리는 비싼 계산을 최적화 하는 것 조차도 props를 state안에 복사하는 짓은 좋은 이유가 되지 못 했다는 걸 봤습니다.
우리의 렌더링 결과는 props의 변경에 따라 달라질 것입니다,

## 멈추지않고 데이터를 계속 흐르게하라 - 사이드이펙트
여태까지 우리는 다음과 같은 얘기들을 나눴습니다. 어떻게 하면 prop의 변경과 일치한 렌더링 결과를 유지 할 수 있는지를 말이죠….

props를 state안에다 복사하는 행동을 피하는 것은 prop변경에 따른 렌더링 결과를 유지하는 방법들 중 한 방법일 뿐입니다.
하지만 데이터 흐름에 있어서 사이드 이펙트 또한 중요합니다.

이런 리액트 컴포넌트가 있다고 가정해보겠습니다.
```JSX
class  SearchResults  extends  React.Component {
	state = {
		data: null
	};
	componentDidMount() {
		this.fetchResults();
	}
	fetchResults() {
		const url = this.getFetchUrl();
		// Do the fetching...
	}
	getFetchUrl() {
		return  'http://myapi/results?query' + this.props.query;
	}
	render() {
		// ...
	}
}
```
많은 리액트 컴포넌트가 이렇게 생겼을 겁니다. 그리고 조금 더 세밀하게 보면, 버그가 있다는 걸 깨닫게 됩니다.

fetchResults 메소드는 데이터 패칭을 위한 query prop에 사용됩니다.
```JSX
getFetchUrl() {
	return  'http://myapi/results?query' + this.props.query;
}
```
여기서 query prop이 바뀌게 되면 어떻게 될까요? 그래도, 컴포넌트에서는 아무 일도 일어나지 않습니다.

이 말인 즉슨, 우리의 컴포넌트의 사이드이펙트가 props의 변경사항을 반영하지 않는 다는걸 의미합니다.
이러한 유형은 리액트 앱에서 일어나는 흔한 버그중 하나입니다.

  

우리의 컴포넌트를 고치기 위해서 우리가 취해야 될 행동은 이렇습니다.
-   componentDidMount를 사용하고 모든 메소드가 이걸 호출하는 방법
--   실제로 고치게 된다면 fetchResults와 getFetchUrl가 호출을 하게 되겠습니다.  
-   이 메소드에 사용되는 모든 props와 state를 기록하는 방법
--   this.props.query를 기록하게됩니다
-   props가 변경될 때마다, 사이드이펙트 재실행하기
--   componentDidUpdate 메소드를 추가하면 할 수 있습니다
```JSX
class  SearchResults  extends  React.Component {
	state = {
		data: null
	};
	componentDidMount() {
		this.fetchResults();
	}
	componentDidUpdate(prevProps) {
		if (prevProps.query !== this.props.query) { 
		// ✅ Refetch on change
		this.fetchResults();
		}
	}
	fetchResults() {
		const url = this.getFetchUrl();
		// Do the fetching...
	}
	getFetchUrl() {
		return  'http://myapi/results?query' + this.props.query;
		// ✅ Updates are handled
	}
	render() {
		// ...
	}
}
```
이제 우리 코드는 props에 관한 모든 변경사항이 반영 됩니다. 사이드이펙트까지도요.

하지만, 이런 걸 지키기 위해 기억하는 건 엄청난 고난입니다.
##### *(이렇게 코딩하는 방식을 기억하는 것이 귀찮다는 뜻 같습니다. 이렇게 코딩을 하게 된다고 가정을 했을 때, local state나 props가 계속해서 추가된다면 이런 것들을 계속해서 반복해 나가야 되기 때문에 귀찮다~ 라는 의미 같습니다.)*
currentPage를 local state에 추가하고 이것을 getFetchUrl에서 사용하고자 하는걸로 예를 들어보겠습니다.
```JSX
class  SearchResults  extends  React.Component {
	state = {
		data: null,
		currentPage: 0,
	};
	componentDidMount() {
		this.fetchResults();
	}
	componentDidUpdate(prevProps) {
		if (prevProps.query !== this.props.query) {
			this.fetchResults();
		}
	}
	fetchResults() {
		const url = this.getFetchUrl();
		// Do the fetching...
	}
	getFetchUrl() {
		return (
			'http://myapi/results?query' + this.props.query +
			'&page=' + this.state.currentPage // 🔴 Updates are ignored
		);
	}
	render() {
	// ...
	}
}
```
사이드 이펙트가 currentPage의 변경사항을 반영하지 않았기에, 또 다시 버그가 일어나게 됩니다.  

Props와 state는 리액트의 데이터 흐름의 부분중 하나입니다.
렌더링과 사이드 이펙트는 이 두개의 변경된 데이터 흐름을 반영해야 됩니다. 무시하지 말고요!

그래서 이 문제를 고치려면 다음과 같은 절차를 반복하면 됩니다.

우리의 컴포넌트를 고치기 위해서 우리가 취해야 될 행동은 이렇습니다.
-   componentDidMount를 사용하고 모든 메소드가 이걸 호출하는 방법
--   실제로 고치게 된다면 fetchResults와 getFetchUrl가 호출을 하게 되겠습니다.
-   이 메소드에 사용되는 모든 props와 state를 기록하는 방법
--   this.props.query와 this.state.currentPage를 기록하게됩니다
-   props가 변경될 때마다, 사이드이펙트 재실행하기
--   componentDidUpdate 메소드를 추가하면 할 수 있습니다

그러면 이제 고쳐보도록 하겠습니다.

```JSX
class  SearchResults  extends  React.Component {
	state = {
		data: null,
		currentPage: 0,
	};
	componentDidMount() {
		this.fetchResults();
	}
	componentDidUpdate(prevProps, prevState) {
		if (
			prevState.currentPage !== this.state.currentPage ||
			// ✅ Refetch on change
			prevProps.query !== this.props.query
		) {
			this.fetchResults();
		}
	}
	fetchResults() {
		const url = this.getFetchUrl();
		// Do the fetching...
	}
	getFetchUrl() {
		return (
			'http://myapi/results?query' + this.props.query +
			'&page=' + this.state.currentPage // ✅ Updates are handled
		);
	}
	render() {
		// ...
	}
}
```
음 근데 이런 실수(사이드이펙트에 props와 state의 상태변경을 반영해주는 걸 까먹는 실수)를 자동으로 잡아내면 개쩔지 않을까요? 아마도 린터가 우리를 도와줄 수 있지않을까요?

불행히도 클래스형 컴포넌트의 일관성을 자동으로 확인하는 건 너무 어려워요.
일단 모든 메소드는 다른 메소드들을 호출 할 수 있어야 돼요.
또한, componentDidMount와 componentDidUpdate와 같은 호출을 정적으로 분석하면 오탐지할 수도 있구요.

어 그런데, 한가지 설계 가능한 API가 있는데 이걸로 일관성을 정적분석할 수 있습니다.
useEffect Hook을 사용하면 됩니다.
```JSX
function  SearchResults({ query }) {
	const [data, setData] = useState(null);
	const [currentPage, setCurrentPage] = useState(0);
	useEffect(() => {
		function  fetchResults() {
			const url = getFetchUrl();
			// Do the fetching...
		}
		function  getFetchUrl() {
			return (
				'http://myapi/results?query' + query +
				'&page=' + currentPage
			);
		}
		fetchResults();
	}, [currentPage, query]); // ✅ Refetch on change
	// ...
}
```
effect안에다 로직을 넣어서 리액트 데이터 흐름이 의존하고 있는 값들을 더욱 보기 쉽게 만들어졌습니다. 이 값들을 “dependencies’라 부르며 우리의 예제에서는 currentPage와 query 해당이 되죠.![Demo of exhaustive-deps lint rule](https://lh5.googleusercontent.com/ccFbHpBeHvC35Kqs4hkrCDwGoJrFJJ41vR6I0_xueaMhdkRv-jNayIAtj8DGrGIveVEeeWdbyHHXlAY_2NOrMIETgyF3zmg7vZZ_yW7FVhKSURWMpJSbc0e9e_37g8YEo5JWWf_C)
아무튼 이 이메일을 요약하자면 데이터 흐름을 절대 멈추지 마세요! ~~*(체질이란게 바뀝니다.)*~~

props와 state를 사용할 때마다, 얘네들이 바뀔 때 무슨 일이 일어나는지 항상 생각하세요.
대부분의 상황에서 컴포넌트는 초기 렌더와 업데이트 렌더를 다르게 취급해서 안됩니다. 이렇게 되면 로직의 변경사항에 대해 resilient하게 됩니다.

class로 리액트 코딩할 때, 라이프 사이클 메소드로 props와 state를 다루다 보니까 업데이트에 관해 잊어버리기 쉽상인데 hooks는 이를 방지해 줍니다. (하지만 hooks에 익숙하지 않은 경우, 시행착오가 있을 수 있습니다.)

다음 이메일은 제 2법칙: 항상 렌더 준비가 되어 있어라 입니다.

그럼 화이팅~

Dan