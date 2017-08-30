## React设计思想
- transformation: UI只是把数据通过映射关系转换为另外一种形式的数据，而且同样的输入必定有同样的输出
- abstraction: 将UI抽象成隐藏内部细节，可复用的函数
- 组合 composition: 将两个及以上的抽象函数合并为一个
- state
- memoization: 对于纯函数，使用相同参数时没有重复执行的必要
- 延续 continuations : 推迟部分函数的执行，进而将一些样板代码移出业务逻辑

## React生命周期
```js
// MOUNTING
getDefaultProps
getInitialState
componentWillMount // 调用setState不触发re-render
render
componentDidMount // 父组件在子组件componentDidMount调用后调用

mountComponent: function(transaction, hostParent, hostContainerInfo, context) {
  this._context = context;
  var updateQueue = transaction.getUpdateQueue();
  var initialState = inst.state;
  if (initialState === undefined) {
    inst.state = initialState = null;
  }
  this._pendingStateQueue = null;

  if (inst.componentWillMount) {
    inst.componentWillMount();
    // When mounting, calls to `setState` by `componentWillMount` will set
    // `this._pendingStateQueue` without triggering a re-render.
    if (this._pendingStateQueue) {
      inst.state = this._processPendingState(inst.props, inst.context);
    }
  }

  if (inst.componentDidMount) {
    transaction.getReactMountReady().enqueue(inst.componentDidMount, inst);
  }
}

// RECEIVE PROPS
componentWillReceiveProps // 调用setState不触发re-render, 且在shouldCompoentUpdate, componentWillUpdate中无法获取到更新后的state
shouldCompoentUpdate
componentWillUpdate
render
componentDidUpdate // 父组件在子组件componentDidUpdate调用后调用

updateComponent: function(transaction,prevParentElement,nextParentElement,prevUnmaskedContext,nextUnmaskedContext) {
  // An update here will schedule an update but immediately set
  // _pendingStateQueue which will ensure that any state updates gets
  // immediately reconciled instead of waiting for the next batch.
  if (willReceive && inst.componentWillReceiveProps) {
    const beforeState = inst.state;
    inst.componentWillReceiveProps(nextProps, nextContext);
    const afterState = inst.state;

    if (beforeState !== afterState) {
      inst.state = beforeState;
      inst.updater.enqueueReplaceState(inst, afterState);
    }
  }

  var nextState = this._processPendingState(nextProps, nextContext);
  var shouldUpdate = true;
  if (!this._pendingForceUpdate) {
    var prevState = inst.state;
    shouldUpdate = willReceive || nextState !== prevState;
    if (inst.shouldComponentUpdate) {
      shouldUpdate = inst.shouldComponentUpdate(nextProps, nextState, nextContext);
    }
  }
  
  if (inst.componentWillUpdate) {
    inst.componentWillUpdate(nextProps, nextState, nextContext);
  }
    
  if (shouldUpdate) {
    this._pendingForceUpdate = false;
    // Will set `this.props`, `this.state` and `this.context`.
    this._performComponentUpdate(...);
  }
}

// UNMOUNTING
componentWillUnmount

unmountComponent: function(safely, skipLifecycle) {
  if (!this._renderedComponent) return;
  var inst = this._instance;

  if (inst.componentWillUnmount && !inst._calledComponentWillUnmount) {
    inst._calledComponentWillUnmount = true;

    if (safely) {
      if (!skipLifecycle) {
        var name = this.getName() + '.componentWillUnmount()';
        ReactErrorUtils.invokeGuardedCallbackAndCatchFirstError(
          name,
          inst.componentWillUnmount,
          inst,
        );
      }
    } else {
      inst.componentWillUnmount();
    }
  }

  this._pendingStateQueue = null;
  this._context = null;
},

## setState

```js
// ReactBaseClasses.js
ReactComponent.prototype.setState = function(partialState, callback) {
  this.updater.enqueueSetState(this, partialState, callback, 'setState');
};

// ReactUpdateQueue.js
enqueueSetState: function(publicInstance,　partialState,　callback,　callerName) {
  var internalInstance = getInternalInstanceReadyForUpdate(publicInstance);

  if (!internalInstance) return;
  
  // mainly add _pendingStateQueue to this
  var queue =　internalInstance._pendingStateQueue ||　(internalInstance._pendingStateQueue = []);
  queue.push(partialState);

  callback = callback === undefined ? null : callback;
  if (callback !== null) {
    if (internalInstance._pendingCallbacks) {
      internalInstance._pendingCallbacks.push(callback);
    } else {
      internalInstance._pendingCallbacks = [callback];
    }
  }

  enqueueUpdate(internalInstance);
}

// ReactCompositeComponent.js
performUpdateIfNecessary: function(transaction) {
  // so that's why `this.setState` trigger componet re-render
  if (this._pendingStateQueue !== null || this._pendingForceUpdate) {
    this.updateComponent(
      transaction,
      this._currentElement,
      this._currentElement,
      this._context,
      this._context,
    );
  }
}
```

## React性能优化

```jsx
// PureChild is re-rendered wastefully when Parent is re-rendered with an unchanged props.dataList
class Parent extends Component {
  render () {
    const dataList = this.props.data.filter(d => (d.count > 0));
    return <PureChild dataList={dataList} />;
  }
}

//PureChild is re-rendered wastefully because arrow functions and Function.prototype.bind return new function instances, which tricks PureChild into thinking that the onClick handler has changed
class Parent extends Component {
  render () {
    return <PureChild onClick={() => { dosomething(); }} />;
  }
}

class Parent extends Component {
  render () {
    return <PureChild {...this.props} />;
  }
}
```
