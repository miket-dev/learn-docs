# react-hooks
##### Table of Contents
* React hooks
    * [useState](#usestate)
    * [useEffect](#useeffect)
        * [Empty array of dependencies](#empty-array-of-dependencies)
        * [Non empty array of dependencies](#non-empty-array-of-dependencies)
        * [Returning value of useEffect](#returning-value-of-useeffect)
    * [useCallback](#usecallback)
    * [useRef](#useref)
* [Redux hooks](#redux-hooks)
    * [useSelector](#useselector)
    * [useDispatch](#usedispatch)
## useState
Initializes the state of the react component
#### Class component
```
class Counter extends React.Component {
    constructor(props) {
        super(props);
        this.state = {
            clickCounter: 0
        };
    }

    onClickHandler() {
        const clickCount = this.state.clickCounter + 1;
        this.setState({
            clickCounter: clickCount
        });
    }

    render() {
        return <button onClick={() => this.onClickHandler()}>
            Click me - Clicked {this.state.clickCounter} times
        </button>
    }
}
```
#### Functional Component
```
const Counter = () => {
    const [clickCounter, setClickCounter] = useState(0);

    const onClickHandler = () => {
        const clickCount = clickCounter + 1;
        setClickCounter(clickCount);
    }
    
    return <button onClick={() => onClickHandler()}>
        Click me - Clicked {clickCounter} times
    </button>;
}
```

## useEffect
Replaces the lifecycle methods of the react component. To call the function if something was changed.
### Empty array of dependencies
It is being called on mount event.
#### Class (empty array of dependencies)
```
class MyComponent extends React.Component {
    constructor(props) {
        super(props);
    }

    componentDidMount() {
       console.log('I have been mounted!');
    }

    render() {
        return <div>Component</div>
    }
}
```
#### Function (empty array of dependencies)
```
const MyComponent = () => {
    const [clickCounter, setClickCounter] = useState(0);

    useEffect(() => {
       console.log('I have been mounted!');
    }, []) // <-- note empty array there
    
    return <div>Component</div>;
}
```
### Non-empty array of dependencies
It is being called when one of the dependencies was changed
#### Class (non empty array of dependencies)
```
class Counter extends React.Component {
    constructor(props) {
        super(props);
        this.state = {
            clickCounter: 0
        };
    }
    
    componentDidUpdate(prevProps, prevState, snapshot) {
        if (this.state.clickCounter != prevState.clickCounter) {
            console.log('Click counter has been updated!');
        }
    }

    onClickHandler() {
        const clickCount = this.state.clickCounter + 1;
        this.setState({
            clickCounter: clickCount
        });
    }

    render() {
        return <button onClick={() => this.onClickHandler()}>
            Click me - Clicked {this.state.clickCounter} times
        </button>
    }
}
```
#### Function (non empty array of dependencies)
```
const Counter = () => {
    const [clickCounter, setClickCounter] = useState(0);

    useEffect(() => {
       console.log('Click counter has been updated!');
    }, [clickCounter]); // <-- note dependency array contains clickCounter

    const onClickHandler = () => {
        const clickCount = clickCounter + 1;
        setClickCounter(clickCount);
    }
    
    return <button onClick={() => onClickHandler()}>
        Click me - Clicked {clickCounter} times
    </button>;
}
```
### Returning value of useEffect
It is a clean-up function which is being called before the dependency will change its value, or component unmounts from the screen.
#### Class (empty array of dependencies)
```
class MyComponent extends React.Component {
    constructor(props) {
        super(props);
    }

    componentWillUnmount() {
       console.log('I am going to be unmounted!');
    }

    render() {
        return <div>Component</div>
    }
}
```
#### Function (empty array of dependencies)
```
const MyComponent = () => {
    const [clickCounter, setClickCounter] = useState(0);

    useEffect(() => {
       return () => {
          console.log('I am going to be unmounted');
       }
    }, []) // <-- note empty array there
    
    return <div>Component</div>;
}
```
#### Class (non empty array of dependencies)
```
class Counter extends React.Component {
    constructor(props) {
        super(props);
        this.state = {
            clickCounter: 0
        };
    }
    
    componentDidUpdate(prevProps, prevState, snapshot) {
        if (this.state.clickCounter != prevState.clickCounter) {
            console.log(`The value was ${prevState.clickCounter}`);
            console.log('Click counter has been updated!');
        }
    }

    onClickHandler() {
        const clickCount = this.state.clickCounter + 1;
        this.setState({
            clickCounter: clickCount
        });
    }

    render() {
        return <button onClick={() => this.onClickHandler()}>
            Click me - Clicked {this.state.clickCounter} times
        </button>
    }
}
```
#### Function (non empty array of dependencies)
```
const Counter = () => {
    const [clickCounter, setClickCounter] = useState(0);

    useEffect(() => {
       console.log('Click counter has been updated!');
       return () => {
        console.log(`The value was ${clickCounter}`);
       }
    }, [clickCounter]); // <-- note dependency array contains clickCounter

    const onClickHandler = () => {
        const clickCount = clickCounter + 1;
        setClickCounter(clickCount);
    }
    
    return <button onClick={() => onClickHandler()}>
        Click me - Clicked {clickCounter} times
    </button>;
}
```
## useCallback
Creates a function that is rebuilt only if one of the dependencies was changed
#### Class Component
```
class Counter extends React.Component {
    constructor(props) {
        super(props);
        this.state = {
            clickCounter: 0
        };
    }
    
    _memoFunction(clickCount) {
        this.setState({
            clickCounter: clickCount
        });
    }
    
    onClickHandler = this._memoFunction.bind(this, this.state.clickCounter);
    
    componentDidUpdate(prevProps, prevState, snapshot) {
        if (this.state.clickCounter != prevState.clickCounter) {
            // rebuilding the handler if the value 
            this.onClickHandler = this._memoFunction.bind(this, this.state.clickCounter);
        }
    }

    render() {
        return <button onClick={() => this.onClickHandler()}>
            Click me - Clicked {this.state.clickCounter} times
        </button>
    }
}
```
#### Functional Component
```
const Counter = () => {
    const [clickCounter, setClickCounter] = useState(0);

    const onClickHandler = useCallback(() => {
        const clickCount = clickCounter + 1;
        setClickCounter(clickCount);
    }), [clickCounter]);
    
    return <button onClick={() => onClickHandler()}>
        Click me - Clicked {clickCounter} times
    </button>;
}
```

## useRef
Creates a reference to DOM element
#### Class component
```
class MyComponent extends React.Component {
    constructor(props) {
        super(props);
    }

    componentDidMount() {
       console.log(this.divRef.id); // <--- will print 'myDiv'
    }

    render() {
        return <div id="myDiv" ref={(ref) => this.divRef = ref}>Component</div>
    }
}
```

#### Functional component
```
const MyComponent = () => {
   const divRef = useRef(null);
   
   useEffect(() => {
      if (!divRef.current) return; // <--- first time it is called when divRef only created
      console.log(this.divRef.current.id);  // <--- will print 'myDiv'
   }, [divRef]);
   
   return <div id="myDiv" ref={divRef}>Component</div>
}
```

# Redux hooks
## useSelector
Selects the data from the state
#### Class component
```
class MyComponent extends React.Component {
    constructor(props) {
        super(props);
    }

    render() {
        return <div>{props.title}</div>
    }
}

const mapStateToProps = (state) => ({
    title: state.title,
});

export default connect(mapStateToProps)(MyComponent);
```
#### Functional component
```
const MyComponent = () => {
   const title = useSelector(state => state.title);
   return <div>{title}</div>
}
```
## useDispatch
Dispatches the specific action to the reducer
#### Class component
```
import { increaseClickCount } from './actions';
class Counter extends React.Component {
    constructor(props) {
        super(props);
    }

    render() {
        return <button onClick={() => props.increaseClickCount()}>
            Click me - Clicked {props.clickCount} times
        </button>
    }
}

const mapStateToProps = (state) => ({
    clickCount: state.clickCounter,
});

const mapDispatchToProps = (dispatch) => ({
    increaseClickCount: dispatch(increaseClickCount());
});

export default connect(mapStateToProps, mapDispatchToProps)(Counter);
```
#### Functional Component
```
import { increaseClickCount } from './actions';
const Counter = () => {
    const dispatch = useDispatch();
    const clickCount = useSelector(state => state.clickCounter);
    
    return <button onClick={() => dispatch(increaseClickCount())}>
        Click me - Clicked {clickCounter} times
    </button>;
}
```
