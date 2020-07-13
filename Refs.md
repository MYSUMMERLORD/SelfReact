**React.createRef()**

概述：    引用（Refs）提供了一个获得DOM节点或者创建在render方法中的React元素的方法；    在典型的React数据流中，props是唯一的父组件与它们的子元素的通信方式。更改子元素，你需要使用新的props去重新渲染子元素。但是在一些情况下你现在典型数据流之外强制的更改元素。被更改的子元素可能是一个React组件的实例，或者是一个DOM元素。对所有这些情况，React提供了一种特殊方法：Refs；

**（一）什么时候使用Refs：**

管理焦点、文本选择、媒体回放触发必要动画；整合第三方DOM库    避免对任何可以声明式解决的问题使用Refs；（比如相对于暴露一个对话框组件的open()、close()方法，请使用isOpen prop！）

**（二）不要过度使用Refs！** 

你的第一个倾向可能是使用Refs去实现一些APP中的东西。在这种情况下，请停下来，仔细想想state应该存在的组件层次。经常地，我们都知道应该由更高的层次去拥有state。

**（三）创建Refs：**  

可以通过React.createRef()创建Refs并通过ref属性联系到React组件。Refs通常当组件被创建时被分配给实例变量，这样它们就能在组件中被引用。
```
class MyComponent extends React.Component {
  constructor(props) {
    super(props);
    this.myRef = React.createRef();
  }
  render() {
    return <div ref={this.myRef} />;
  }
}
```




**（四）访问Refs：**  

当一个ref通过render放入一个元素中，一个对节点的引用可以通过ref的current属性得到；
`const node = this.myRef.current;`

ref的值根据节点类型的不同而不同：    当ref属性用于HTML元素，在构造器中通过React.createRef()函数创建的ref接收底层DOM元素作为它的current属性；    当ref属性用于传统的类组件，ref对象接收挂载好的组件实例作为它的current；    你不能将ref属性用于函数式组件上，因为他们并没有实例（instance）！    下面是对应于不同ref的例子1）DOM元素：
```    

class CustomTextInput extends React.Component {
  constructor(props) {
    super(props);
    // 创建一个ref去储存textInput DOM元素
    this.textInput = React.createRef();
    this.focusTextInput = this.focusTextInput.bind(this);
  }
  
  focusTextInput() {
    // 很明显的，让text input获得焦点使用了原生的DOM API
    // 注意：我们通过current去获得DOM节点
    this.textInput.current.focus();
  }
  
  render() {
    // 告诉React我们想要将<input>的ref和构造器中创建的textInput联系起来
    return (
      <div>
        <input
          type="text"
          ref={this.textInput} />
  
        <input
          type="button"
          value="Focus the text input"
          onClick={this.focusTextInput}
        />
      </div>
    );
  }
}

```


   React将会在组件挂载时将DOM元素分配给current属性，并且在组件被卸载时，将current属性重置为null。ref将会在componentDidMount和componentDidUpdate生命周期钩子前被更新2）类组件：    如果我们想要包装上面的CustomTextInput，模仿挂载后被点击。我们可以通过ref得到自定义的Input组件，手动调用它的focusTextInput函数。（注意！只有当CustomTextInput被声明为类的时候才有用！）
 ```
class AutoFocusTextInput extends React.Component {
  constructor(props) {
    super(props);
    this.textInput = React.createRef();
  }
  
  componentDidMount() {
    this.textInput.current.focusTextInput();
  }
  
  render() {
    return (
      <CustomTextInput ref={this.textInput} />
    );
  }
}
```



3）函数式组件（没有用的！）
```
function MyFunctionalComponent() {
  return <input />;
}
  
class Parent extends React.Component {
  constructor(props) {
    super(props);
    this.textInput = React.createRef();
  }
  render() {
    // 这样没用！函数式组件根本就没有实例！
    return (
      <MyFunctionalComponent ref={this.textInput} />
    );
  }
}
```




但是，你可以在函数式组件中使用ref属性，就像你引用DOM元素和类组件一样。
```
function CustomTextInput(props) {
  // textInput 必须被声明在这里——ref才能适用于它
  let textInput = React.createRef();
  
  function handleClick() {
    textInput.current.focus();
  }
  
  return (
    <div>
      <input
        type="text"
        ref={textInput} />
  
      <input
        type="button"
        value="Focus the text input"
        onClick={handleClick}
      />
    </div>
  );
}
```


 **（五）向父组件暴露DOM引用（Refs）**   
 
 在很罕见的情况下，你也许想要从父组件访问到子元素的DOM节点。通常来说我们不建议这样做，因为这样破坏了组件的封装性，但是在某些情况下对于类似：触发聚焦、改变子元素DOM节点的大小、位置等情况非常有用。    你可以向子组件增加ref（就像上面说的），但是这并不是一个完美的解决方案——你只会获得一个组件实例而不是DOM节点。更糟糕的是，它对函数式组件没用！    如果你使用React 16.3或者更高的版本，我们建议你在这些情况下使用ref forwarding，Ref fprwarding让组件可以选择去暴露子组件的ref作为他们自己的。你可以在ref forwarding文档中找到更全面的例子——怎样暴露子元素的DOM节点给父元素。    如果你使用React 16.2或者更低的版本，或者你需要比ref forwarding所能提供的更多的灵活性。你可以使用替代方法，并且显式的传入一个ref当做一个不同命名的prop。    如果可能，我们不建议暴露DOM节点，但是在一些情况下还是非常有用的。注意，这种方法需要你去在子组件中增加一些代码，如果你完全没有对于子组件实现的控制，你最后的选择是使用findDOMNode()方法，当然，也只能这样了。
 
 **（六）回调Refs**  
 
 React同样支持另一种名为“回调refs”的方法去设置refs——它可以给我们对refs创建和销毁更细粒度的控制。    放入一个函数，而不是一个由createRef()创建的ref属性。这个函数接受React组件实例、或者HTML DOM元素作为参数——可以被储存并且在其他地方被访问。    下面的例子实现了一个通常的模式：使用ref回调储存一个DOM节点的应用在实例变量中：
 ```
class CustomTextInput extends React.Component {
  constructor(props) {
    super(props);
  
    this.textInput = null;
  
    this.setTextInputRef = element => {
      this.textInput = element;
    };
  
    this.focusTextInput = () => {
      // 通过原生DOM API聚焦文本
      if (this.textInput) this.textInput.focus();
    };
  }
  
  componentDidMount() {
    // 在挂载时自动聚焦
    this.focusTextInput();
  }
  
  render() {
    // 使用'ref'回调去在一个实例域中储存文本输入DOM元素的引用(比如, this.textInput).
    return (
      <div>
        <input
          type="text"
          ref={this.setTextInputRef}
        />
        <input
          type="button"
          value="Focus the text input"
          onClick={this.focusTextInput}
        />
      </div>
    );
  }
}

```



   React将会在组件挂载时使用DOM元素调用ref回调，在组件卸载时使用null调用ref回调。ref回调都会在componentDidMount或者componentDidUpdate生命周期钩子之前被调用。    你可以在组件之间传递回调refs，就像你可以对通过React.createRef()创建的对象refs一样：
```
function CustomTextInput(props) {
  return (
    <div>
      <input ref={props.inputRef} />
    </div>
  );
}
  
class Parent extends React.Component {
  render() {
    return (
      <CustomTextInput
        inputRef={el => this.inputElement = el}
      />
    );
  }、
}

```

  在上面的例子中，Parent组件将他的ref回调作为inputRef这个属性（props）传入CustomTextInput组件，接着CustomTextInput组件将同样的函数作为一个特殊的ref属性（attribute）传给<input>。从结果来看，Parent组件中的this.inputElement将会被放在与在CustomTextInput组件的<input>元素相关的DOM节点中。
    
 **（七）历史遗留的API：字符串Refs **  
 
 不用管，以后都要移除（见到this.refs.textInput的形式，就使用React.createRef()或者回调模式代替）。
 
 **（八）关于回调refs的警告 **  
 
 如果ref回调被定义为一个行内函数，当组件更新时会被调用两次——第一次被null调用、而后被DOM元素调用。这是因为函数的新实例会在每次渲染的时候创建，所以React需要清除老的ref然后生成一个新的。你可以通过在class中定义一个绑定的ref回调方法避免这个问题，但是注意，这种问题在大多数情况下都没什么影响~


## 扩展链接

* https://www.cnblogs.com/crazycode2/p/10023493.html

