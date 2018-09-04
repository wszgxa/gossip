title: React中的函数子组件(FaCC)和高阶组件(HOC)
tags: 
- react
- javascript
layout: post
categories: JS
date: 2018-08-29
---

在接触过React项目后，大多数人都应该已经了解过或则用过了HOC(High-Order-Components)和FaCC(Functions as Child Components)，因为这两个模式在大多数react的开源库里都存在。比如react-router里面的[withRouter](https://github.com/ReactTraining/react-router/blob/master/packages/react-router/modules/withRouter.js#L9) 就是典型的高阶组件，接受一个组件返回另外一个经过增强后的组件。而react-motion中的[Motion](https://github.com/chenglou/react-motion/blob/master/src/Motion.js#L28)就是典型的FaCC的应用。

HOC和FaCC两者做的事也是非常相似的，都是类似设计模式里面的装饰者模式。都是在原有的实例或则单元上进行功能的增强。

当然不只是一些开源库中会使用，在平常的代码编写中，也有很多地方是适用于使用HOC和FaCC去封装一些逻辑。比如数据埋点，新特性的toggle，获取转换数据等。对于增强代码可读性和逻辑复用来说，非常有用的。

<!--more-->
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
接受一个组件返回一个经过包装的新组件。在我们经常使用的`withRouter`就是在原有组件`props`上面在加上`localtion`等属性。除了添加props以外高阶组件还能做到：

- 在真正调用组件前后做一些事，比如埋点数据等
- 判断组件是否该render，或则应该render其他的东西，比如出错之后render错误页面
- 传递props并增加新的props
- 不render组件，转而做一些其他的事情，比如渲染一个外部的dom

对于上面的前三点都比较好理解，解释一下第4点。比如你在render了一个页面之后，需要改变一下页面的title.这是单页应用普遍存在的一个需求，通常你可以在具体router库中使用hook去实现。当然也可以通过HOC来实现：
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

同样FaCC也是用于增强原有组件能力的一种模式，其主要功能的实现在于react的[props.children](https://reactjs.org/docs/jsx-in-depth.html#functions-as-children)可以是任何东西，包括函数。我们可以拿上面class的例子用FaCC再实现一遍：

``` jsx
const ClassNameWrapper = ({ children }) => children('demo-class')

// 使用

const HeadWithClass = (props) => (
  <ClassNameWrapper>
    {(class) => <header classNmae={class} ></header>}
  </ClassNameWrapper>
)
```

在FaCC中你也可以像HOC一样在生命周期中做很多事对原有的组件进行封装，基本上HOC能做的FaCC也都能做。我所在的项目之前都是大范围的使用HOC，再经过一番讨论后，开始大范围的转变成FaCC。

## 区别

两者都是用来增强原有组件的，具体该使用那种？那种是正确的模式？社区关于这一点也有很多讨论，比如就有人说FaCC是反模式：[Function as Child Components Are an Anti-Pattern](https://americanexpress.io/faccs-are-an-antipattern/)。他给出的理由是children并不语义化，会造成困惑，然后他提出了`Component Injection`的模式，有兴趣的同学可以读一读。


具体从几个方面做一下对比：


1. 1.组合阶段

组合阶段意思就是HOC，FaCC和要被增强的组件的组合时候。可以很明显发现，FaCC对于前后组件对接依赖信息显示的更多，相对而言更容易理解。而HOC，相互之间如何桥接，你必须得深入到HOC内部读代码才可以知道这个HOC具体干了啥。

``` js
// HOC example
import View from './View'

const DetailPage = withServerData(withNavigator(View))

```

``` js
// FaCC example

import View from './View'

const DetailPage = props => (
  <FetchServerData>
    {
      data => (
        <Navigator>
          <View data={data} {...props} />
        </Navigator>
      )
    }
  </FetchServerData>
)

```

如果在上面再增加2个HOC，上面组合的过程就变得十分难看。而FaCC相对而言，如何封装，数据源来自那里，组件接受了那些数据都比较显眼。


2. 2.性能优化

在HOC中我们能接受到宿主的prop，因为props是从HOC往下传递的，所以我们也有完整的生命周期，我们可以使用shouldComponentUpdate优化。而FaCC则不然，无法在其内部做比较props，除非在组合的时候外部在包一个组件才能进行比较props。


3. 3.灵活性

FaCC 在组合阶段相对HOC更为灵活，他并不规定被增强组件如何使用它传递下去的属性。而HOC基本上在编写完后就定死了。

另外，FaCC不会再去创建一个新的Component，而HOC会创建一个新的Component然后传递props下去。

## 总结

社区中很多开源库已经使用了两种模式，也有很多的文章进行比较。也有很多激烈讨论，当然对于最后解决问题而言，两种模式都有好处。出于不同的考虑，可能选择不一样。


参考文章：
  1. http://rea.tech/functions-as-child-components-and-higher-order-components/
  2. http://rea.tech/reactjs-real-world-examples-of-higher-order-components/#the-relationship-between-hocs-and-decorator-design-pattern
  3. https://medium.com/merrickchristensen/function-as-child-components-5f3920a9ace9
  4. http://www.ituring.com.cn/book/2007 第四章