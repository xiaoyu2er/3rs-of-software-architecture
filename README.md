# 软件体系结构的 3 大特性 (可读性、可复用性、可重构性)

![software architecture pyramid](public/software-architecture-pyramid.png)

## Software Architecture 软件体系结构
在软件工程存在了50多年之后，我们还没有确定软件体系结构的精确定义。毕竟，它是计算机科学中的一门艺术——坚持不懈地逃避定义它。尽管如此，软件体系结构对我们的行业和应用程序结构至关重要，它不可能被忽视。


尽管我们缺乏共识，但是有很多定义可以帮助我们更接近于软件架构的形式化。其中最引人注目的可能来自IEEE：


> “体系结构是系统的基本组织，体现在其组件、组件之间的关系、与环境的关系以及指导其设计和发展的原则上。”[IEEE 1471]



虽然这个定义和其他定义可以使构成体系结构的元素变得清晰，但它并没有给我们在开发应用程序时使用的心智模型。然而，这个项目的目的正是为了实现这一点。通过观察3个“特性”（可读性、可重用性和可重构性），我们可以形成体系结构属性的层次结构，从而为我们提供一个思考系统代码和体系结构的框架。它本身不会给您一个体系结构，但它会引导您思考什么体系结构适合您的应用程序。
## What is This Project? 本项目是什么


本项目是一个试图分析软件体系结构的3个“特性”（可读性、可重用性和可重构性）的指南，并展示我们如何通过分层思考这些概念来形成更好的代码。这个项目是为任何技能水平的任何开发人员，但如果你刚刚开始，你会发现更多的价值比一个经验丰富的从业人员。


我们将看到的代码是一个用 JavaScript 编写的非常简单的购物车应用程序，它使用了生态系统中的两个主要库：React 和 Redux。JavaScript 和上述工具绝不是构造任何特定应用程序的唯一方法。它们碰巧被许多新加入这个行业的人使用，也被许多老手使用，因此它们的使用频率使它们成为讨论代码质量的一种极好的公共语言。我们将一次开发一个应用程序，并在3r层次结构的每个步骤中查看坏版本和好版本。您可以在src目录中找到所有的代码，关于如何构建和开发它的说明在本自述文件的底部。


还有一点需要重申：这个项目不是看待软件的唯一方式，它当然不能给你一个架构，但它有希望指导你的思考，就像它指导我的一样。


不用再多说了，我们开始吧！
## 1. Readability 可读性
可读性是评估代码质量的最简单方法，也是最容易修复的方法。当你打开一段代码时，你能看到最明显的东西，它通常包括：


- 格式化
- 变量名
- 函数名
- 函数参数的数量
- 函数长度（行数）
- 嵌套级别



这些不是唯一要考虑的事情，但它们是直接的危险信号。幸运的是，有一些简单的规则可以用来解决与上述问题相关的问题：


- 敲定一个自动格式化程序。找到一个你的团队同意的，并将其集成到你的构建过程中。没有什么比格式化参数更浪费时间和金钱了。找一个格式化程序，永远不要回头！在这个项目中，我们将使用更漂亮。
- 使用有意义且可发音的变量/函数名。代码是为人编写的，只是顺便为计算机编写的。命名是传达代码背后含义的最重要的事情。
- 将函数参数限制在 1-3 之间。0参数表示您正在改变状态或依赖于来自调用者以外的其他地方的状态。3个以上的参数很难读取，重构也很困难，因为函数的参数越多，它就有很多条路径。
- 函数的行数没有固定的限制，因为这取决于所用的特定语言。要点是你的函数应该做一件事，而且只能做一件事。如果你的函数，计算一个项目的税后价格，首先要连接到数据库，查找项目，得到税收数据，然后进行计算，那么它显然做了不止一件事。长函数通常表示发生的事情太多。
- 两个以上级别的嵌套可能意味着性能差（在循环中），而且在长条件中尤其难以读取。考虑将嵌套逻辑提取到单独的函数中。



让我们看一下购物车应用程序的第一部分，看看可读性差是什么样子：


```typescript
// src/1-readable/bad/index.js
import React, { Component } from 'react';

// Inventory
class inv extends Component
{
  constructor()
  {
    super();

    // State
    this.state =
    {
      c: 'usd', // currency
      i: [ // inventory
        {
          product: 'Flashlight',
          img: '/flashlight.jpg',
          desc: "A really great flashlight",
          price: 100,
          id: 1,
          c: 'usd'
        },
        {
          product: 'Tin can',
          img: '/tin_can.jpg',
          desc: "Pretty much what you would expect from a tin can",
          price: 32,
          id: 2,
          c: 'usd'
        },
        {
          product: 'Cardboard Box',
          img: '/cardboard_box.png',
          desc: "It holds things",
          price: 5,
          id: 3,
          c: 'usd'
        }
      ]
    }
  }

  render () {
    return (
      <table style={{width: '100%'}}>
      <tbody>
      <tr>
      <th>
        Product
      </th>

      <th>
        Image
      </th>

      <th>
        Description
      </th>

      <th>
        Price
      </th>
      </tr>
        {this.state.i.map(function(i, idx) {
          return (
            <tr key = {idx}>
              <td>
                {i.product}
              </td>

              <td>
              <img src={i.img} alt=""/>
              </td>

              <td>
              {i.desc}
              </td>

              <td>
                {i.price}
              </td>
            </tr>
          );
        })}
    </tbody>
    </table>
    );
  }
}

export default inv;
```


我们可以马上看到一些问题：


- 格式不一致且令人不快
- 命名错误的变量
- 杂乱无章的数据结构（未由IDs设置密钥的清单）
- 不必要的注释，或者是一个好的变量名所需要的注释



我们来看看如何改进它：


```typescript
// src/1-readable/good/index.js
import React, { Component } from 'react';

export default class Inventory extends Component {
  constructor() {
    super();
    this.state = {
      localCurrency: 'usd',
      inventory: {
        1: {
          product: 'Flashlight',
          img: '/flashlight.jpg',
          desc: 'A really great flashlight',
          price: 100,
          currency: 'usd',
        },
        2: {
          product: 'Tin can',
          img: '/tin_can.jpg',
          desc: 'Pretty much what you would expect from a tin can',
          price: 32,
          currency: 'usd',
        },
        3: {
          product: 'Cardboard Box',
          img: '/cardboard_box.png',
          desc: 'It holds things',
          price: 5,
          currency: 'usd',
        },
      },
    };
  }

  render() {
    return (
      <table style={{ width: '100%' }}>
        <tbody>
          <tr>
            <th>
              Product
            </th>

            <th>
              Image
            </th>

            <th>
              Description
            </th>

            <th>
              Price
            </th>
          </tr>
          {Object.keys(this.state.inventory).map(itemId => (
            <tr key={itemId}>
              <td>
                {this.state.inventory[itemId].product}
              </td>

              <td>
                <img src={this.state.inventory[itemId].img} alt="" />
              </td>

              <td>
                {this.state.inventory[itemId].desc}
              </td>

              <td>
                {this.state.inventory[itemId].price}
              </td>
            </tr>
          ))}
        </tbody>
      </table>
    );
  }
}
```


改进后的代码现在具有以下特性：


- 它使用自动格式化程序 Prettier 进行一致格式化
- 名字更具描述性
- 正确组织数据结构。在这种情况下，清单是由ID键控的。不好的可读性可能意味着性能不好。如果我们想从我们的坏代码示例中的库存中获取一个项目，我们会有一个O（n）查找时间，但是对于ID键控的库存，我们会得到一个O（1）查找，这对于大库存来说要快得多。
- 注释不再需要了，因为好的命名可以澄清代码的含义。当业务逻辑复杂且需要文档时，需要注释。
## 2. Reusability 可重用性
可重用性是您能够阅读此代码、与陌生人在线交流甚至编程的唯一原因。可重用性使我们能够用过去的小片段来表达新的想法。


这就是为什么可重用性是指导软件体系结构的一个重要概念。我们通常认为可重用性是干的（不要重复你自己）。这是它的一个方面——如果可以正确地抽象代码，就不要有重复的代码。不过，可重用性远不止于此。它是关于制作干净、简单的api，让你的编程伙伴说，“是的，我知道它到底做什么！”可重用性使您的代码更易于使用，这意味着您可以更快地发布特性。


我们将查看前面的示例，并通过添加货币转换器对其进行扩展，以处理我们在多个国家/地区的库存定价：


```javascript
// src/2-reusable/bad/index.js
import React, { Component } from 'react';

export default class Inventory extends Component {
  constructor() {
    super();
    this.state = {
      localCurrency: 'usd',
      inventory: {
        1: {
          product: 'Flashlight',
          img: '/flashlight.jpg',
          desc: 'A really great flashlight',
          price: 100,
          currency: 'usd',
        },
        2: {
          product: 'Tin can',
          img: '/tin_can.jpg',
          desc: 'Pretty much what you would expect from a tin can',
          price: 32,
          currency: 'usd',
        },
        3: {
          product: 'Cardboard Box',
          img: '/cardboard_box.png',
          desc: 'It holds things',
          price: 5,
          currency: 'usd',
        },
      },
    };

    this.currencyConversions = {
      usd: {
        rupee: 66.78,
        yuan: 6.87,
        usd: 1,
      },
    };

    this.currencySymbols = {
      usd: '$',
      rupee: '₹',
      yuan: '元',
    };
  }

  onSelectCurrency(e) {
    this.setState({
      localCurrency: e.target.value,
    });
  }

  convertCurrency(amount, fromCurrency, toCurrency) {
    const convertedCurrency = amount *
      this.currencyConversions[fromCurrency][toCurrency];
    return this.currencySymbols[toCurrency] + convertedCurrency;
  }

  render() {
    return (
      <div>
        <label htmlFor="currencySelector">Currency:</label>
        <select
          className="u-full-width"
          id="currencySelector"
          onChange={this.onSelectCurrency.bind(this)}
          value={this.state.localCurrency}
        >
          <option value="usd">USD</option>
          <option value="rupee">Rupee</option>
          <option value="yuan">Yuan</option>
        </select>
        <table style={{ width: '100%' }}>
          <tbody>
            <tr>
              <th>
                Product
              </th>

              <th>
                Image
              </th>

              <th>
                Description
              </th>

              <th>
                Price
              </th>
            </tr>

            {Object.keys(this.state.inventory).map(itemId => (
              <tr key={itemId}>
                <td>
                  {this.state.inventory[itemId].product}
                </td>

                <td>
                  <img src={this.state.inventory[itemId].img} alt="" />
                </td>

                <td>
                  {this.state.inventory[itemId].desc}
                </td>

                <td>
                  {this.convertCurrency(
                    this.state.inventory[itemId].price,
                    this.state.inventory[itemId].currency,
                    this.state.localCurrency,
                  )}
                </td>
              </tr>
            ))}
          </tbody>
        </table>
      </div>
    );
  }
}
```


这段代码是有效的，但仅仅有效并不是代码的重点。这就是为什么我们需要用一个更强有力的视角来看待这个问题，而不仅仅是分析它是否有效和可读。我们得看看它是否可以重复使用。你注意到什么问题了吗？


上面的代码有三个主要问题：


- 货币选择器 耦合到库存组件
- 货币转换器耦合到库存组件
- 库存数据是在库存组件中显式定义的，在API中不会提供给组件。



每个函数和模块应该只做一件事，否则在查看源代码时很难弄清楚发生了什么。库存组件应该只用于显示库存，而不是转换和选择货币。让模块和函数做一件事的好处是，它们更容易测试，也更容易重用。如果我们想在应用程序的另一部分中使用货币转换器，就必须包含整个库存组件。如果我们只需要兑换货币，那就没有意义了。


让我们看看更多可重用组件的情况：


```javascript
// src/2-reusable/good/currency-converter.js
export default class CurrencyConverter {
  constructor(currencyConversions) {
    this.currencyConversions = currencyConversions;
    this.currencySymbols = {
      usd: '$',
      rupee: '₹',
      yuan: '元',
    };
  }

  convert(amount, fromCurrency, toCurrency) {
    const convertedCurreny = amount *
      this.currencyConversions[fromCurrency][toCurrency];
    return this.currencySymbols[toCurrency] + convertedCurreny;
  }
}
```


```javascript
// src/2-reusable/good/currency-selector.js

import React, { Component } from 'react';

class CurrencySelector extends Component {
  constructor(props) {
    super();
    this.props = props;
    this.state = {
      localCurrency: props.localCurrency.currency,
    };

    this.setGlobalCurrency = props.setGlobalCurrency;
  }

  onSelectCurrency(e) {
    const currency = e.target.value;

    this.setGlobalCurrency(currency);

    this.setState({
      localCurrency: currency,
    });
  }

  render() {
    return (
      <div>
        <label htmlFor="currencySelector">Currency:</label>
        <select
          className="u-full-width"
          id="currencySelector"
          onChange={this.onSelectCurrency.bind(this)}
          value={this.state.localCurrency}
        >
          <option value="usd">USD</option>
          <option value="rupee">Rupee</option>
          <option value="yuan">Yuan</option>
        </select>
      </div>
    );
  }
}

CurrencySelector.propTypes = {
  setGlobalCurrency: React.PropTypes.func.isRequired,
  localCurrency: React.PropTypes.string.isRequired,
};

export default CurrencySelector;
```


```javascript
// src/2-reusable/good/inventory.js

import React, { Component } from 'react';

class Inventory extends Component {
  constructor(props) {
    super();
    this.state = {
      localCurrency: props.localCurrency,
      inventory: props.inventory,
    };

    this.CurrencyConverter = props.currencyConverter;
  }

  componentWillReceiveProps(nextProps) {
    this.setState({
      localCurrency: nextProps.localCurrency,
    });
  }

  render() {
    return (
      <div>
        <table style={{ width: '100%' }}>
          <tbody>
            <tr>
              <th>
                Product
              </th>

              <th>
                Image
              </th>

              <th>
                Description
              </th>

              <th>
                Price
              </th>
            </tr>

            {Object.keys(this.state.inventory).map(itemId => (
              <tr key={itemId}>
                <td>
                  {this.state.inventory[itemId].product}
                </td>

                <td>
                  <img src={this.state.inventory[itemId].img} alt="" />
                </td>

                <td>
                  {this.state.inventory[itemId].desc}
                </td>

                <td>
                  {this.CurrencyConverter.convert(
                    this.state.inventory[itemId].price,
                    this.state.inventory[itemId].currency,
                    this.state.localCurrency,
                  )}
                </td>
              </tr>
            ))}
          </tbody>
        </table>
      </div>
    );
  }
}

Inventory.propTypes = {
  inventory: React.PropTypes.object.isRequired,
  currencyConverter: React.PropTypes.object.isRequired,
  localCurrency: React.PropTypes.string.isRequired,
};

export default Inventory;
```


```javascript
// src/2-reusable/good/index.js

import React, { Component } from 'react';
import CurrencyConverter from './currency-converter';
import Inventory from './inventory';
import CurrencySelector from './currency-selector';

export default class ReusableGood extends Component {
  constructor() {
    super();

    this.inventory = {
      1: {
        product: 'Flashlight',
        img: '/flashlight.jpg',
        desc: 'A really great flashlight',
        price: 100,
        currency: 'usd',
      },
      2: {
        product: 'Tin can',
        img: '/tin_can.jpg',
        desc: 'Pretty much what you would expect from a tin can',
        price: 32,
        currency: 'usd',
      },
      3: {
        product: 'Cardboard Box',
        img: '/cardboard_box.png',
        desc: 'It holds things',
        price: 5,
        currency: 'usd',
      },
    };

    // Most likely we would fetch this from an external source if this were a real app
    this.currencyConversions = {
      usd: {
        rupee: 66.78,
        yuan: 6.87,
        usd: 1,
      },
    };

    this.state = {
      localCurrency: 'usd',
    };

    this.setGlobalCurrency = (currency) => {
      this.setState({
        localCurrency: currency,
      });
    };
  }

  render() {
    return (
      <div>
        <CurrencySelector
          setGlobalCurrency={this.setGlobalCurrency}
          localCurrency={this.state.localCurrency}
        />
        <Inventory
          inventory={this.inventory}
          currencyConverter={new CurrencyConverter(this.currencyConversions)}
          localCurrency={this.state.localCurrency}
        />
      </div>
    );
  }
}
```


这段代码改进了很多。现在我们有单独的货币选择和转换模块。此外，我们现在可以向库存组件提供库存数据。例如，这意味着我们可以下载清单数据，并将其提供给清单组件，而无需修改其源代码。这种解耦是依赖反转原理，它是创建可重用代码的一种强大方法。


现在，是时候小心谨慎了。在深入研究并使所有东西都可重用之前，重要的是要认识到重用性要求您有一个好的API供其他人使用。如果你不这样做，那么当你去更新它的时候，使用你的API的人可能会受到伤害，因为你意识到它没有被考虑得足够好。那么，什么时候代码不能重用呢？


- 如果您还不能定义一个好的API，就不要创建一个单独的模块。复制胜于不良的基础。
- 您不希望在不久的将来重用您的函数或模块。



## 3. Refactorability 可重构性


可重构的代码是您可以毫无恐惧地更改的代码。您可以在周五晚上部署这些代码，并在周一早上返回，而不必担心您的用户遇到运行时错误。


可重构性是关于整个系统的。它是关于你的可重用模块如何像乐高积木一样连接在一起。如果您更改了Employee模块，但它以某种方式破坏了您的报告模块，那么您就知道您有一些可重构性问题。可重构性是3r层次结构的最高部分，也是最难实现和维护的。任何人类系统都会有问题，代码也不例外。然而，我们可以做一些事情来使代码可重构。那么，它们是什么？


- 单独的副作用
- 测试
- 静态类型



我们使用的是JavaScript，而不是TypeScript之类的类型化替代，因此我们无法看到静态类型有何帮助。只需说一句，当您的代码有类型（如下面所示）时，您知道没有人可以向您的代码传递不正确的值，这限制了您的应用程序可能遇到的错误数：


```typescript
// We can't get passed arrays, strings, objects, or any type other than a number
function add(a : number, b : number) {
  return a + b;
}
```


我强烈建议在大型应用程序中使用JavaScript的静态类型替代方法。类型给你额外的信心超出你的测试所能提供的。但是类型并不是万能的，你仍然需要隔离你的副作用并测试你的代码。


你可能想知道，隔离你的副作用意味着什么？你可能会问什么是副作用？


副作用是当函数或模块在其自身范围之外修改数据时。如果将数据写入磁盘、更改全局变量，甚至将某些内容打印到终端，都会产生副作用。现在，如果你的程序没有任何副作用，那么它就是一个黑匣子。程序是计算机执行的指令，它接收数据并输出数据。如果没有数据输出，那么程序就没用了。但是，对于一个生成数据的程序来说，它必须修改外部世界中的某些东西。出于这个原因，我们需要副作用，但我们也需要隔离它们。


为什么我们要隔离副作用？


- 副作用使我们的代码很难测试，因为如果一个函数的执行修改了另一个函数依赖的一些数据，那么我们就不能确定一个函数总是用相同的输入给出相同的输出。
- 副作用在其他可重用模块之间引入耦合。如果模块A修改了模块B依赖的某个全局状态，那么A必须在B之前运行。
- 副作用使我们的系统无法预测。如果任何函数或模块都可以操纵应用程序的状态，那么我们就不能确定更新一个模块将如何影响整个系统。



我们如何隔离副作用？答案是通过创建一个中心位置来更新应用程序的全局状态。对于客户端JavaScript应用程序，有很多很好的方法可以做到这一点，但是对于这个项目，我们将使用 Redux。


我们将修改现有代码以合并购物车。让我们看看这段新代码，看看为什么它不可重构：


```javascript
// src/3-refactorable/bad/inventory.js
import React, { Component } from 'react';

class Inventory extends Component {
  constructor(props) {
    super();
    this.state = {
      localCurrency: props.localCurrency,
      inventory: props.inventory,
    };

    this.CurrencyConverter = props.currencyConverter;
    this.cart = window.cart;
  }

  componentWillReceiveProps(nextProps) {
    this.setState({
      localCurrency: nextProps.localCurrency,
    });
  }

  onAddToCart(itemId) {
    this.cart.push(itemId);
  }

  render() {
    return (
      <div>
        <table style={{ width: '100%' }}>
          <tbody>
            <tr>
              <th>
                Product
              </th>

              <th>
                Image
              </th>

              <th>
                Description
              </th>

              <th>
                Price
              </th>

              <th>
                Cart
              </th>
            </tr>

            {Object.keys(this.state.inventory).map(itemId => (
              <tr key={itemId}>
                <td>
                  {this.state.inventory[itemId].product}
                </td>

                <td>
                  <img src={this.state.inventory[itemId].img} alt="" />
                </td>

                <td>
                  {this.state.inventory[itemId].desc}
                </td>

                <td>
                  {this.CurrencyConverter.convert(
                    this.state.inventory[itemId].price,
                    this.state.inventory[itemId].currency,
                    this.state.localCurrency,
                  )}
                </td>

                <td>
                  <button onClick={() => this.onAddToCart(itemId)}>
                    Add to Cart
                  </button>
                </td>
              </tr>
            ))}
          </tbody>
        </table>
      </div>
    );
  }
}

Inventory.propTypes = {
  inventory: React.PropTypes.object.isRequired,
  localCurrency: React.PropTypes.string.isRequired,
  currencyConverter: React.PropTypes.object.isRequired,
};

export default Inventory;
```


```javascript
// src/3-refactorable/bad/cart.js
import React, { Component } from 'react';

class Cart extends Component {
  constructor(props) {
    super();
    this.state = {
      cart: window.cart,
      localCurrency: props.localCurrency,
      inventory: props.inventory,
    };

    // Repeatedly sync global cart to local cart
    this.watcher = window.setInterval(
      () => {
        this.setState({
          cart: window.cart,
        });
      },
      1000,
    );
    this.CurrencyConverter = props.currencyConverter;
  }

  componentWillReceiveProps(nextProps) {
    this.setState({
      localCurrency: nextProps.localCurrency,
    });
  }

  componentWillUnmount() {
    window.clearInterval(this.watcher);
  }

  render() {
    return (
      <div>
        <h2>Cart</h2>
        {this.state.cart.length === 0
          ? <p>Nothing in the cart</p>
          : <table style={{ width: '100%' }}>
              <tbody>
                <tr>
                  <th>
                    Product
                  </th>

                  <th>
                    Price
                  </th>
                </tr>
                {this.state.cart.map((itemId, idx) => (
                  <tr key={idx}>
                    <td>
                      {this.state.inventory[itemId].product}
                    </td>

                    <td>
                      {this.CurrencyConverter.convert(
                        this.state.inventory[itemId].price,
                        this.state.inventory[itemId].currency,
                        this.state.localCurrency,
                      )}
                    </td>
                  </tr>
                ))}
              </tbody>
            </table>}
      </div>
    );
  }
}

Cart.propTypes = {
  inventory: React.PropTypes.object.isRequired,
  localCurrency: React.PropTypes.string.isRequired,
  currencyConverter: React.PropTypes.object.isRequired,
};

export default Cart;
```


这里我们有一个新的购物车模块，它显示了购物车中当前的库存项目。这段代码中有两个非常有问题的东西，它们是什么？




上述代码的两个主要问题是：


- 购物车被写入全局变量：`window.cart`
- 通过不断读取全局 `window.cart`，它将耦合引入定时。



尽管我们的模块是可重用和可读的，但通过写入全局变量，我们使整个系统变得非常脆弱。我们引入的任何第三方库都可能覆盖我们的 `window.cart` 用别的东西破坏我们的应用程序。此外，我们编写的任何模块都可以访问和修改它，而无需任何保护措施或集中的更新方式。


你可能会说，“是的，我从来不会这样构造我的应用程序。”太好了！但是请记住，尽管这有点夸张，关键是购物车的更新和读取方式并不是集中式的。如果您使用的不是全局变量和setInterval，而是消息传递模块，那么这也可能会使您的代码难以理解和大规模重构，因为很难隔离状态并找出一个模块如何影响另一个模块。


我们将使用 Redux 集中我们的状态管理。如果您以前没有使用过Redux，[请查看教程](http://redux.js.org/docs/basics/)。


让我们看看这段更具可重构性的代码是什么样子的：


```javascript
// src/3-refactorable/good/containers/inventory.js
import React from 'react';
import { connect } from 'react-redux';
import { addToCartAction } from '../actions';

import CurrencyConverter from '../lib/currency-converter';
import Inventory from '../components/inventory';

const InventoryContainer = (
  {
    inventory,
    currencies,
    localCurrency,
    addToCart,
  },
) => (
  <Inventory
    currencyConverter={new CurrencyConverter(currencies)}
    inventory={inventory}
    localCurrency={localCurrency}
    addToCart={productId => addToCart(productId)}
  />
);

InventoryContainer.propTypes = {
  currencies: React.PropTypes.object.isRequired,
  inventory: React.PropTypes.object.isRequired,
  localCurrency: React.PropTypes.string.isRequired,
  addToCart: React.PropTypes.func.isRequired,
};

const mapStateToProps = state => ({
  inventory: state.inventory,
  currencies: state.currencies,
  localCurrency: state.localCurrency,
});

export default connect(mapStateToProps, {
  addToCart: addToCartAction,
})(InventoryContainer);
```


```javascript
// src/3-refactorable/good/containers/cart.js
import React from 'react';
import { connect } from 'react-redux';

import CurrencyConverter from '../lib/currency-converter';
import Cart from '../components/cart';

const CartContainer = ({ cart, inventory, currencies, localCurrency }) => (
  <Cart
    cart={cart}
    currencyConverter={new CurrencyConverter(currencies)}
    localCurrency={localCurrency}
    inventory={inventory}
  />
);

CartContainer.propTypes = {
  currencies: React.PropTypes.object.isRequired,
  cart: React.PropTypes.array.isRequired,
  inventory: React.PropTypes.object.isRequired,
  localCurrency: React.PropTypes.string.isRequired,
};

const mapStateToProps = state => ({
  cart: state.cart,
  inventory: state.inventory,
  currencies: state.currencies,
  localCurrency: state.localCurrency,
});

export default connect(mapStateToProps, {})(CartContainer);
```


```javascript
// src/3-refactorable/good/actions/index.js
import * as types from '../constants/action-types';

export const addToCartAction = productId => ({
  type: types.ADD_TO_CART,
  productId,
});
```


```javascript
// src/3-refactorable/good/reducers/cart.js
import { ADD_TO_CART } from '../constants/action-types';

const initialState = [];

export default (state = initialState, action) => {
  switch (action.type) {
    case ADD_TO_CART:
      return [...state, parseInt(action.productId, 10)];
    default:
      return state;
  }
};
```


这个改进的代码将我们的副作用集中到一个 action 函数中，该函数接收 productId 并将其传递给我们的reducer，reducer 创建了一个全新的 cart，其中添加了这个产品。这个新的购物车被放置在我们的商店中，一旦发生这种情况，所有从商店的特定部分派生状态的组件都将被 react redux 通知新的数据，并更新它们的状态。React 将智能地重新呈现每个更新的组件，仅此而已！


这个流可以确保应用程序的状态只能以一种方式更新，即通过 action-> reducer -> store -> component 管道。没有可修改的全局状态，没有可传递和跟踪的消息，也没有我们的模块可以产生的不受控制的副作用。最好的部分是，我们可以跟踪应用程序的整个状态，这样调试和 QA 就变得容易多了，因为我们可以及时获得整个应用程序的精确快照。


需要注意的一点是：在这个项目的示例应用程序中，您可能不需要 Redux，但是如果我们要扩展这个代码，使用Redux 作为状态管理解决方案将变得更容易，而不是将所有内容都放在顶级控制器中索引 .js. 我们可以在那里隔离应用程序的状态，并通过每个模块向下传递相应的数据修改操作函数。问题是，在规模上，我们将有大量的行动要传递，大量的数据将生活在一个巨大的空间中索引 .js 控制器。通过早期承诺适当的集中化状态，我们不需要在应用程序开发时做太多更改。


我们最不需要看的是测试。测试给我们信心，我们可以改变一个模块，它仍然会做它打算做的事情。我们将查看购物车和库存组件的测试：


```javascript
// src/test/cart.test.js
import React from 'react';
import { shallow } from 'enzyme';
import Cart from '../src/3-refactorable/good/components/cart';

const props = {
  localCurrency: 'usd',
  cart: [1, 1],
  inventory: {
    1: {
      product: 'Flashlight',
      img: '/flashlight.jpg',
      desc: 'A really great flashlight',
      price: 100,
      currency: 'usd',
    },
  },
  currencyConverter: {
    convert: jest.fn(),
  },
};

it('should render Cart without crashing', () => {
  const cartComponent = shallow(<Cart {...props} />);
  expect(cartComponent);
});

it('should show all cart data in cart table', () => {
  props.currencyConverter.convert = function () {
    return `$${props.inventory[1].price}`;
  };

  const cartComponent = shallow(<Cart {...props} />);
  let tr = cartComponent.find('tr');
  expect(tr.length).toEqual(3);

  props.cart.forEach((item, idx) => {
    let td = cartComponent.find('td');
    let product = td.at(2 * idx);
    let price = td.at(2 * idx + 1);

    expect(product.text()).toEqual(props.inventory[item].product);
    expect(price.text()).toEqual(props.currencyConverter.convert());
  });
});
```


```javascript
// src/test/inventory.test.js
import React from 'react';
import { shallow } from 'enzyme';
import Inventory from '../src/3-refactorable/good/components/inventory';

const props = {
  localCurrency: 'usd',
  inventory: {
    1: {
      product: 'Flashlight',
      img: '/flashlight.jpg',
      desc: 'A really great flashlight',
      price: 100,
      currency: 'usd',
    },
  },
  addToCart: jest.fn(),
  changeCurrency: jest.fn(),
  currencyConverter: {
    convert: jest.fn(),
  },
};

it('should render Inventory without crashing', () => {
  const inventoryComponent = shallow(<Inventory {...props} />);
  expect(inventoryComponent);
});

it('should show all inventory data in table', () => {
  props.currencyConverter.convert = function () {
    return `$${props.inventory[1].price}`;
  };

  const inventoryComponent = shallow(<Inventory {...props} />);
  let tr = inventoryComponent.find('tr');
  expect(tr.length).toEqual(2);

  let td = inventoryComponent.find('td');
  let product = td.at(0);
  let image = td.at(1);
  let desc = td.at(2);
  let price = td.at(3);

  expect(product.text()).toEqual('Flashlight');
  expect(image.html()).toEqual('<td><img src="/flashlight.jpg" alt=""/></td>');
  expect(desc.text()).toEqual('A really great flashlight');
  expect(price.text()).toEqual('$100');
});

it('should have Add to Cart button work', () => {
  const inventoryComponent = shallow(<Inventory {...props} />);
  let addToCartBtn = inventoryComponent.find('button').first();
  addToCartBtn.simulate('click');
  expect(props.addToCart).toBeCalled();
});
```


这些测试确保购物车和库存组件：

- 显示他们应该显示的数据
- 维护一致的API
- 可以通过调用给定的操作函数来修改状态



## Final Thoughts 最后一点想法


软件体系结构是很难改变的东西，所以在一个可读的、可重用的、可重构的基础上进行早期投资。以后很难到达那里。通过遵循3R，你的用户会感谢你，你的开发者会感谢你，你也会感谢你自己。

--------------------------------------------------------------------------------
## Development
Thank you for reading this! If you wish to expand on this project or contribute, run the following commands to install everything you need:

```
npm install -g create-react-app
npm install
npm run start
```

Open a browser and see the app running at `http://localhost:3000/`

## Contributing
Thank you for your contributions!

Before opening a friendly Pull Request, make sure you run the following and resolve any errors noted by the linter:

```
npm run fmt
npm run lint
```

Finally, change any relevant code examples in this `README.md` to reflect your changes.

## Translation

- ![en](https://raw.githubusercontent.com/gosquared/flags/master/flags/flags/shiny/24/United-Kingdom.png) **English**: [ryanmcdermott/3rs-of-software-architecture](https://github.com/ryanmcdermott/3rs-of-software-architecture)
- ![tr](https://raw.githubusercontent.com/gosquared/flags/master/flags/flags/shiny/24/Turkey.png) **Turkish**: [bsonmez/3rs-of-software-architecture](https://github.com/bsonmez/3rs-of-software-architecture)
- ![zh](https://raw.githubusercontent.com/gosquared/flags/master/flags/flags/shiny/24/China.png) **Chinese**: [xiaoyu2er/3rs-of-software-architecture-zh](https://github.com/xiaoyu2er/3rs-of-software-architecture-zh)


## License
MIT
