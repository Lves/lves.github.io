![000](https://upload-images.jianshu.io/upload_images/1159872-6cc4715eb33c1ecb.jpg?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

React是一个用于构建用户界面的 JavaScript 库，起源于Facebook内部项目，13年已经开源https://github.com/facebook/react。学习ReactNative首先要了解React相关的知识（当然可以和RN同步学习），今天只是一个入门介绍但用于开发ReactNative已经足够了。

### 一、React优势

- 虚拟DOM,HTML写在JS里
- 一切皆组件，支持自定义
- 单向数据流，数据改变自动同步视图
- Write once, run everywhere

### 二、HelloWorld

废话不多说，先来个HelloWorld。

```js
<html>
  <head>
    <script src="https://unpkg.com/react@16/umd/react.development.js"></script>
    <script src="https://unpkg.com/react-dom@16/umd/react-dom.development.js"></script> 
    <script src="https://unpkg.com/babel-standalone@6.15.0/babel.min.js"></script>
  </head>
  <body>
    <div id="example"></div>
    <script type="text/babel">
      ReactDOM.render(
        <h2>Hello World</h2>,
        document.getElementById('example')
      );
    </script>
  </body>
</html>
```

浏览器中打开查看效果，看到Hello World，恭喜你已经入门了。

### 三、JSX

JSX语法简单说就是HTML写在JS里。想详细了解的可以查看官方文档https://reactjs.org/docs/introducing-jsx.html

### 四、组件Component

创建组件一共有3中方式，建议选择第一和第三种。

- 组件中只能有一个根节点
- 组件首字母必须大写，否则会被解析成html标签
- 组件类对应的标签的用法和HTML标签的用法完全一致，可以加入任意的属性
- 具有可组合、可重用、可维护的特征

#### 无状态函数式组件

无状态函数式组件形式上表现为一个只带有一个render方法的组件类，通过函数形式或者箭头函数，并且该组件是无state状态的。

```javascript
var HelloA = (props) => {
    return <h2>{props.name}</h2>
}
```

#### React.createClass 

React.createClass是react刚开始推荐的创建组件的方式，这是ES5的原生的JavaScript来实现的React组件。

```js
var HelloMessage = React.createClass({
    render: function() {
     	return (<h1>Hello</h1>);
    }
});
```

#### class

React.Component是以ES6的形式来创建react的组件的，是React目前极为推荐的创建有状态组件的方式，最终会取代React.createClass形式。

```js
class HelloM extends React.Component{
    render(){
  		return <h1>Hello {this.props.name}</h1>;
	}
};
```

### 五、组件生命周期

先来个标准的ES6组件生命周期代码

```js
class LifeComponent extends React.Component{
    constructor(props){
        super(props)
        console.log('构造函数')
        this.state={
            title:props.title
        }
    }
    componentWillMount(){
        console.log('componentWillMount');
    }
    componentDidMount(){
        console.log('componentDidMount');
    }
    shouldComponentUpdate(){
        console.log('shouldComponentUpdate');
        return true;
        }
    componentWillUpdate(){
        console.log('componentWillUpdate');
    }

    componentDidUpdate(){
        console.log('componentDidUpdate');
    }
    componnentWillReceiveProps(){
        console.log('componnentWillReceiveProps');
    }
    render(){
        console.log('render');
        return(
            <div>
                <h1>{this.state.title}</h1>
            </div>
        );
    } 
 }
LifeComponent.propTypes = {
    title:React.PropTypes.string,
}
LifeComponent.defaultProps = {
	title:'标题',
}
ReactDOM.render(
    <LifeComponent/>,
    document.getElementById('example')
);

```

组件的生命周期介绍图如下：

![图片来自http://blog.guowenfh.com/2017/02/05/2017/es6-react-life-cycle/](https://upload-images.jianshu.io/upload_images/1159872-f56d24e9dbec83a8.jpg?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)


注意：

>componentWillMount() 、componentDidMount() 、componentWillReceiveProps()  这三个函数中允许调用setState,
>
>shouldComponentUpdate、  componentWillUnmount 调用setStat无效
>
>componentWillUpdate、componentDidUpdate	调用setStat会死循环		

​			

### 六、Props 、 State对比

#### 相同

- 都是用于描述组件状态的
- 都可以改变,改变都会触发组建的重新渲染

#### 不同

- Props是由外部传入的，是父组件传递给子组件的数据流。
- State是内部定义的，代表组件的内部状态。在内部改变与外部组件没有直接联系。
- Props通常在组件外部发生变化，在内部保持不变。
- 一个组件不能改变自身的*props,* 但要负责设置子组件的 *props*。

### 七、箭头函数

ES6之前按钮点击我们可能是这样写的：

```js
class LButton extends React.Component{
        render(){
          return <button onClick={this.handleClick.bind(this)} style={this.state}>{this.props.name}</button>;
        }
        handleClick(){
            this.setState({fontSize:30,color:'white', backgroundColor:'red'});
        }
 };
```



需要在调用handleClick的地方绑定this。ES6之后使用箭头函数就不用了，请看实例：

```js
class LLButton extends React.Component{
    render(){
      return <button onClick={()=>{
            this.setState({fontSize:30,color:'white', backgroundColor:'blue'});
      }} style={this.state}>{this.props.name}</button>;
    }
};
```

或者

```js
class LLLButton extends React.Component{
    render(){
      return <button onClick={()=> this.handleClick()} style={this.state}>{this.props.name}</button>;
    }
    handleClick(){
        this.setState({fontSize:30,color:'white', backgroundColor:'yellow'});
    }
};
```

更多箭头函数介绍请访问：https://reactjs.org/docs/faq-functions.html#is-it-ok-to-use-arrow-functions-in-render-methods

### 八、获取真实的DOM节点

React提供了一个获取组件的的方式： `Refs`。

#### 使用场景：

- 处理焦点、文本选择或媒体控制。
- 触发强制动画。
- 集成第三方 DOM 库

如果可以通过声明式实现，则尽量避免使用 refs。即能用State或者Props实现的不要用Refs。

来个例子直观了解一下Refs:

```js
<!DOCTYPE html>
<html>
  <head>
    <script src="https://cdn.bootcss.com/react/15.4.2/react.min.js"></script>
	<script src="https://cdn.bootcss.com/react/15.4.2/react-dom.min.js"></script>
	<script src="https://cdn.bootcss.com/babel-standalone/6.22.1/babel.min.js"></script>
    
  </head>
  <body>
    <div id="example"></div>
    <script type="text/babel">
      class RefComponent extends React.Component{
        render(){
          return (
            <div>
                <input type="text" ref="lTextInput" />
                <input type="button" value="Focus the text input" onClick={this.handleClick.bind(this)} />
            </div>
          );
        }
        handleClick(){
            this.refs.lTextInput.focus();
        }
      };

      ReactDOM.render(
        <RefComponent />,
        document.getElementById('example')
      );
    </script>
  </body>
</html>
```

### 九、表单提交

下面看一个官网的表单提交的例子。输入框中的值变化时通过onChange保存到State中，点击提交时从State中取出提交。

```js
class NameForm extends React.Component {
    constructor(props) {
        super(props);
        this.state = {value: ''};       
    }
    render() {
        return (
            <form onSubmit={(event)=> {
                alert('A name was submitted: ' + this.state.value);
                event.preventDefault();
            }}>
            <label>
                Name:
                <input type="text" value={this.state.value} onChange={(event)=> {
                    this.setState({value: event.target.value});
                }}/>
            </label>
            <input type="submit" value="Submit" />
            </form>
        );
    }
}
ReactDOM.render(
    <NameForm />,
    document.getElementById('example')
);
```

运行效果：

![运行效果](https://upload-images.jianshu.io/upload_images/1159872-9e6231eeb6609a64.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)


### 十、何去何从

学习ReactNative掌握这些React知识基本可以开始搞了，如果你想更深入的学习React可以到React中文网站查看文档和教程https://doc.react-china.org/ 。

更多iOS、逆向、ReactNative开发文章请专注微信公众号：乐Coding









