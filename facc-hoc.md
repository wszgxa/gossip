title: React中的函数子组件(FaCC)和高阶子组件(HOC)
tags: 
- react
- javascript
layout: post
categories: JS
date: 2018-08-29
---

在接触过react项目后，大多数人都应该已经了解过或则用过了HOC（High-Order-Components）和FaCC（Functions as Child Components），因为这两个模式在大多数react的开源库里都存在。比如react-router里面的[withRouter](https://github.com/ReactTraining/react-router/blob/master/packages/react-router/modules/withRouter.js#L9) 就是典型的高阶组件，接受一个组件返回另外一个经过封装后的组件。而react-motion中的[Motion](https://github.com/chenglou/react-motion/blob/master/src/Motion.js#L28)就是典型的FaCC的应用。
HOC和FaCC两者做的事也是非常相似的，都是类似设计模式里面的装饰者模式。都是在原有的实例或则单元上进行功能的增强。

当然不只是一些开源库中会使用，在平常的代码编写中，也有很多地方是适用于使用HOC和FaCC去封装一些逻辑。比如数据埋点，新特性的toggle，获取转换数据等。对于增强代码可读性和逻辑复用来说，学习一下还是很不错的。


## HOC

高阶函数我们都用过，就是接受一个函数然后返回一个经过封装的函数：

``` js
const plus = first => second => (first + second)
plus(1)(2) // 3
```
而高阶组件就是高阶函数的概念应用到高阶组件上:
``` js

const withClassName = ComposedComponent => props => (
   <ComposedComponent {...props} className='demo-class' />
)

// 使用
const Header = text => (<header>{text}</header>)
const headerWitheClass = withClassName(Header)
```
接受一个组件返回一个经过包装的新组件。在我们经常使用的`withRouter`就是在原有组件上面在加上`localtion`等属性。除了添加props以外高阶组件还能做到：

1. 在真正调用组件前后做一些事，比如埋点数据等
2. 判断组件是否该render，或则应该render其他的东西，比如出错之后render错误页面
3. 传递props并增加新的props
4. 不render组件，转而做一些其他的事情，比如渲染一个外部的dom

对于上面的1，2，3都比较好理解，解释一下第4个。比如你在render了一个页面之后，需要改变一下页面的title.这是单页应用普遍存在的一个需求，通常你可以在具体router库中使用hook去实现。当然也可以通过HOC来实现：
``` js

const withTitleChange = ComposedComponent => {
  return class extends React.Component {
    componentDidMount () {
      const { title } = this.props
      document.title = title
    }
    render () {
      const props = this.props
      return <ComposedComponent {...props} />
    }
  }
}

```

## FaCC

