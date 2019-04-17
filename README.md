# Typescript 代码整洁之道 [![Tweet](https://img.shields.io/twitter/url/http/shields.io.svg?style=social)](https://twitter.com/intent/tweet?text=Clean%20Code%20Typescript&url=https://github.com/labs42io/clean-code-typescript)

适用于 Typescript 的代码整洁之道

受到 [clean-code-javascript](https://github.com/ryanmcdermott/clean-code-javascript) 的启发。

## 目录

  1. [介绍](#介绍)
  2. [变量](#变量)
  3. [函数](#函数)
  4. [对象与数据结构](#对象与数据结构)
  5. [类](#类)
  6. [面向对象设计](#solid)
  7. [Testing](#testing)
  8. [Concurrency](#concurrency)
  9. [Error Handling](#error-handling)
  10. [Formatting](#formatting)
  11. [Comments](#comments)
  12. [Translations](#translations)

## 介绍

![Humorous image of software quality estimation as a count of how many expletives
you shout when reading code](https://www.osnews.com/images/comics/wtfm.jpg)

软件工程原理，源自 Robert C. Martin's 的书
[*Clean Code*](https://www.amazon.com/Clean-Code-Handbook-Software-Craftsmanship/dp/0132350882),
适用于 TypeScript。 这不是风格指南，它是在 TypeScript 中生成
[可读，可重用和可重构](https://github.com/ryanmcdermott/3rs-of-software-architecture) 软件的指南。

并非每个原则都必须严格遵守，普遍认同的甚至更少。这些只是准则，仅此而已，但他们却是 *Clean Code* 一书的作者根据自身多年的编程体会而提炼撰写出来的。

我们的软件工程技术只有50多年的历史，我们还有很多东西需要学习。当软件架构与架构本身一样古老时，也许那时我们会有更难遵循的规则。现在，让这些指导方针作为一个试金石，用来评估您和您的团队所生成的 Typescript 代码的质量.

除此之外：知道这些并不会立即使你成为一个更好的软件开发者，这些规则伴随你工作多年后并不意味着你就不会犯错误了。每一段代码都是作为初稿开始的，就像湿粘土被塑造成最终形状一样。最终，当我们与小伙伴们一起审查时，我们会凿掉不完美之处。不要因为这些需要改进的初稿而阻碍自己技术的提升。先干掉代码再说吧！

**[⬆ 返回顶部](#目录)**

## 变量

### 使用有意义的变量名

以这样的方式区分名称，以便读者知道他们的差异到底是什么。

**反例:**

```ts
function between<T>(a1: T, a2: T, a3: T): boolean {
  return a2 <= a1 && a1 <= a3;
}

```

**正例:**

```ts
function between<T>(value: T, left: T, right: T): boolean {
  return left <= value && value <= right;
}
```

**[⬆ 返回顶部](#目录)**

### 使用可拼读的变量名

如果你不能拼读他，你只能像个白痴一样去声明它。

**反例:**

```ts
type DtaRcrd102 = {
  genymdhms: Date;
  modymdhms: Date;
  pszqint: number;
}
```

**正例:**

```ts
type Customer = {
  generationTimestamp: Date;
  modificationTimestamp: Date;
  recordId: number;
}
```

**[⬆ 返回顶部](#目录)**

### 对于相同类型的变量只使用同一个单词

**反例:**

```ts
function getUserInfo(): User;
function getUserDetails(): User;
function getUserData(): User;
```

**正例:**

```ts
function getUser(): User;
```

**[⬆ 返回顶部](#目录)**

### 使用可搜索的名称

我们阅读的代码往往比我们要写的代码多。因此我们编写的代码的可读性和可搜索性是非常重要的。不给那些具有意义且有助于理解程序的变量命名，就会伤害了阅读代码的人。让您的名字可搜索。像 [TSLint](https://palantir.github.io/tslint/rules/no-magic-numbers/) 这样的工具可以帮助识别未命名的常量。

**反例:**

```ts
// 86400000 到底是什么？
setTimeout(restart, 86400000);
```

**正例:**

```ts
// 将它们声明为大写的命名常量。
const MILLISECONDS_IN_A_DAY = 24 * 60 * 60 * 1000;

setTimeout(restart, MILLISECONDS_IN_A_DAY);
```

**[⬆ 返回顶部](#目录)**

### 使用变量展开

**反例:**

```ts
declare const users: Map<string, User>;

for (const keyValue of users) {
  // 迭代用户地图
}
```

**正例:**

```ts
declare const users: Map<string, User>;

for (const [id, user] of users) {
  // 迭代用户地图
}
```

**[⬆ 返回顶部](#目录)**

### 避免心照不宣

显式优于隐式。  
*清晰是王道。*

**反例:**

```ts
const u = getUser();
const s = getSubscription();
const t = charge(u, s);
```

**正例:**

```ts
const user = getUser();
const subscription = getSubscription();
const transaction = charge(user, subscription);
```

**[⬆ 返回顶部](#目录)**

### 不要添加不需要的上下文

如果你的 类/类型/对象 名称中已经有说明，不要在你的变量中重复它们。

**反例:**

```ts
type Car = {
  carMake: string;
  carModel: string;
  carColor: string;
}

function print(car: Car): void {
  console.log(`${car.carMake} ${car.carModel} (${car.carColor})`);
}
```

**正例:**

```ts
type Car = {
  make: string;
  model: string;
  color: string;
}

function print(car: Car): void {
  console.log(`${car.make} ${car.model} (${car.color})`);
}
```

**[⬆ 返回顶部](#目录)**

### 使用默认参数而不是短路表达式或条件语句

默认参数通常比短路表达式更清晰。

**反例:**

```ts
function loadPages(count?: number) {
  const loadCount = count !== undefined ? count : 10;
  // ...
}
```

**正例:**

```ts
function loadPages(count: number = 10) {
  // ...
}
```

**[⬆ 返回顶部](#目录)**

## 函数

### 函数参数（理想情况下为 2 个或更少）

限制函数参数的数量非常重要，因为它使您的功能测试更容易。
超过三个导致组合爆炸，导致你必须用每个单独的参数测试大量不同的情况。

一个或两个参数是理想的情况，如果可能的话应该避免三个。应该整合除此之外的任何东西。
通常情况下，如果你有超过两个参数那么就是你的函数想要做的事情太多了。如果不是，大多数情况下，更高级别的对象就足以作为参数。

如果您发现自己需要大量参数，请考虑使用对象字面量。

为了明确函数所期望的属性，你可以使用 [解构](https://basarat.gitbooks.io/typescript/docs/destructuring.html) 语法.
他有以下几个优点:

1. 当有人查看函数名称时，会立即清楚正在使用的属性。

2. 解构并克隆参数对象中的指定原始值传递给函数。这有助于预防副作用。注：不会克隆从参数对象中解构的对象和数组。

3. TypeScript 会警告您未使用的属性，如果没有解构，这将是不可能的。

**反例:**

```ts
function createMenu(title: string, body: string, buttonText: string, cancellable: boolean) {
  // ...
}

createMenu('Foo', 'Bar', 'Baz', true);
```

**正例:**

```ts
function createMenu(options: { title: string, body: string, buttonText: string, cancellable: boolean }) {
  // ...
}

createMenu({
  title: 'Foo',
  body: 'Bar',
  buttonText: 'Baz',
  cancellable: true
});
```

You can further improve readability by using [type aliases](https://www.typescriptlang.org/docs/handbook/advanced-types.html#type-aliases):

```ts

type MenuOptions = { title: string, body: string, buttonText: string, cancellable: boolean };

function createMenu(options: MenuOptions) {
  // ...
}

createMenu({
  title: 'Foo',
  body: 'Bar',
  buttonText: 'Baz',
  cancellable: true
});
```

**[⬆ 返回顶部](#目录)**

### 函数应该只做一件事情

这是迄今为止软件工程中最重要的规则。当函数执行多个操作时，它们更难以编写，测试和推断。 当您可以将一个函数隔离成一个动作时，它们便可以轻松被重构，您的代码将更清晰。遵守这一法则，如果你没有其他错误，那么你将领先于许多开发者。

**反例:**

```ts
function emailClients(clients: Client) {
  clients.forEach((client) => {
    const clientRecord = database.lookup(client);
    if (clientRecord.isActive()) {
      email(client);
    }
  });
}
```

**正例:**

```ts
function emailClients(clients: Client) {
  clients.filter(isActiveClient).forEach(email);
}

function isActiveClient(client: Client) {
  const clientRecord = database.lookup(client);
  return clientRecord.isActive();
}
```

**[⬆ 返回顶部](#目录)**

### 函数名称应该说明它们的作用

**反例:**

```ts
function addToDate(date: Date, month: number): Date {
  // ...
}

const date = new Date();

// 从函数名称很难分辨出新增了什么
addToDate(date, 1);
```

**正例:**

```ts
function addMonthToDate(date: Date, month: number): Date {
  // ...
}

const date = new Date();
addMonthToDate(date, 1);
```

**[⬆ 返回顶部](#目录)**

### 函数应该只抽象一层

当你有多个抽象级别时，你的函数通常做太多事情了。拆分功能可以实现可重用性和更轻松的测试。

**反例:**

```ts
function parseCode(code: string) {
  const REGEXES = [ /* ... */ ];
  const statements = code.split(' ');
  const tokens = [];

  REGEXES.forEach((regex) => {
    statements.forEach((statement) => {
      // ...
    });
  });

  const ast = [];
  tokens.forEach((token) => {
    // lex...
  });

  ast.forEach((node) => {
    // parse...
  });
}
```

**正例:**

```ts
const REGEXES = [ /* ... */ ];

function parseCode(code: string) {
  const tokens = tokenize(code);
  const syntaxTree = parse(tokens);

  syntaxTree.forEach((node) => {
    // parse...
  });
}

function tokenize(code: string): Token[] {
  const statements = code.split(' ');
  const tokens: Token[] = [];

  REGEXES.forEach((regex) => {
    statements.forEach((statement) => {
      tokens.push( /* ... */ );
    });
  });

  return tokens;
}

function parse(tokens: Token[]): SyntaxTree {
  const syntaxTree: SyntaxTree[] = [];
  tokens.forEach((token) => {
    syntaxTree.push( /* ... */ );
  });

  return syntaxTree;
}
```

**[⬆ 返回顶部](#目录)**

### 删除重复代码

尽你最大努力去避免重复的代码。重复代码很糟糕，因为这意味着如果需要更改某些逻辑，则需要更改多个地方的内容。

想象一下如果你经营一家餐馆，并跟踪你的存货：你所有的番茄，洋葱，大蒜，香料等。如果你有多个清单记录他们，那么当你端上一道菜时，所有的清单都必须更新。当你端上一盘西红柿时。如果只有一个列表，那么只有一个地方需要更新！

通常，您有重复的代码，因为您有两个或多个稍微不同的地方，他们虽然有很多共同点，但是它们的不同迫使你有两个或更多的独立的功能来做很多相同的事情。删除重复的代码意味着创建一个抽象，只需一个函数/模块/类就可以处理这组不同的事情。

正确的抽象是至关重要的，这就是为什么要遵循 [SOLID](#solid) 原则。糟糕的抽象甚至比重复的代码更差劲，所以请你一定要认真对待！说到这里，如果你能做出比较好的抽象，那就大胆抽象吧！不要做重复工作，否则终有一天你会发现需要更改多个地方而仅仅只是为了改变一点东西。

**反例:**

```ts
function showDeveloperList(developers: Developer[]) {
  developers.forEach((developer) => {
    const expectedSalary = developer.calculateExpectedSalary();
    const experience = developer.getExperience();
    const githubLink = developer.getGithubLink();

    const data = {
      expectedSalary,
      experience,
      githubLink
    };

    render(data);
  });
}

function showManagerList(managers: Manager[]) {
  managers.forEach((manager) => {
    const expectedSalary = manager.calculateExpectedSalary();
    const experience = manager.getExperience();
    const portfolio = manager.getMBAProjects();

    const data = {
      expectedSalary,
      experience,
      portfolio
    };

    render(data);
  });
}
```

**正例:**

```ts
class Developer {
  // ...
  getExtraDetails() {
    return {
      githubLink: this.githubLink,
    }
  }
}

class Manager {
  // ...
  getExtraDetails() {
    return {
      portfolio: this.portfolio,
    }
  }
}

function showEmployeeList(employee: Developer | Manager) {
  employee.forEach((employee) => {
    const expectedSalary = employee.calculateExpectedSalary();
    const experience = employee.getExperience();
    const extra = employee.getExtraDetails();

    const data = {
      expectedSalary,
      experience,
      extra,
    };

    render(data);
  });
}
```

你应该对复制出来的代码持批评的态度。有时我们需要在冗余代码与通过引入不必要的抽象导致复杂度增加之间进行权衡。当来自两个不同模块的两个实现看起来相似但存在于不同的领域中时，复制也许是可以接受的，并且比提取公共代码更可取。在这种情况下，提取出来的公共代码在两个模块之间引入了间接依赖关系。

**[⬆ 返回顶部](#目录)**

### 使用 Object.assign 或者析构来设置默认对象

**反例:**

```ts
type MenuConfig = { title?: string, body?: string, buttonText?: string, cancellable?: boolean };

function createMenu(config: MenuConfig) {
  config.title = config.title || 'Foo';
  config.body = config.body || 'Bar';
  config.buttonText = config.buttonText || 'Baz';
  config.cancellable = config.cancellable !== undefined ? config.cancellable : true;

  // ...
}

createMenu({ body: 'Bar' });
```

**正例:**

```ts
type MenuConfig = { title?: string, body?: string, buttonText?: string, cancellable?: boolean };

function createMenu(config: MenuConfig) {
  const menuConfig = Object.assign({
    title: 'Foo',
    body: 'Bar',
    buttonText: 'Baz',
    cancellable: true
  }, config);

  // ...
}

createMenu({ body: 'Bar' });
```

或者，可以使用具有默认值的析构函数：

```ts
type MenuConfig = { title?: string, body?: string, buttonText?: string, cancellable?: boolean };

function createMenu({ title = 'Foo', body = 'Bar', buttonText = 'Baz', cancellable = true }: MenuConfig) {
  // ...
}

createMenu({ body: 'Bar' });
```

通过显示传递 `undefined` or `null` 这些值，能够避免副作用和一些不可预知的行为，你可以告诉 TypeScript 编译器去避免这种情况发生。详情请查看 [`--strictNullChecks`](https://www.typescriptlang.org/docs/handbook/release-notes/typescript-2-0.html#--strictnullchecks) TypeScript 中的配置项。

**[⬆ 返回顶部](#目录)**

### 不要使用 Flags 作为函数参数

Flags 告诉别人这个函数干了多件事情。而一个函数应该只做一件事。如果他们基于布尔值来决定不同的代码逻辑分支，那么请拆分你的函数吧。

**反例:**

```ts
function createFile(name: string, temp: boolean) {
  if (temp) {
    fs.create(`./temp/${name}`);
  } else {
    fs.create(name);
  }
}
```

**正例:**

```ts
function createTempFile(name: string) {
  createFile(`./temp/${name}`);
}

function createFile(name: string) {
  fs.create(name);
}
```

**[⬆ 返回顶部](#目录)**

### 避免副作用（第一部分）

如果函数执行的操作不是获取一个值并返回另一个或多个值，则会产生副作用。副作用可能是写入文件、修改某个全局变量，或者意外地将所有钱转到陌生人那里。

现在，你时常需要在程序中产生副作用。像前面的例子一样，您可能需要写入一个文件。你要做的就是集中精力做这些事情。不要使用多个函数和类来写入特定文件。他只实现一个功能。仅仅只有一个。

主要是为了避免在没有任何结构的对象之间共享状态这样的常见陷阱，使用任何东西都可以写入的可变数据类型，而不是集中在副作用发生的地方。如果你能做到这一点，你将比绝大多数其他程序员厉害。

**反例:**

```ts
// 与下面函数相关的全局变量
let name = 'Robert C. Martin';

function toBase64() {
  name = btoa(name);
}

toBase64(); 
// 如果我们有另一个使用这个名称的函数，现在它将是base64值

console.log(name); // 希望打印出 'Robert C. Martin' 但却打印出了 'Um9iZXJ0IEMuIE1hcnRpbg=='
```

**正例:**

```ts
const name = 'Robert C. Martin';

function toBase64(text: string): string {
  return btoa(text);
}

const encodedName = toBase64(name);
console.log(name);
```

**[⬆ 返回顶部](#目录)**

### 避免副作用（第二部分）

在 JavaScript 中，元组通过值传递，对象/数组通过引用传递。比如对象和数组，如果你的函数改变了购物车数组，例如，通过新增元素去购买，然后任何其他使用到 `cart` 数组的函数将受到增加的影响。这可能很棒，但是同样很糟糕。让我们想象一下这样一个糟糕的场景：

用户点击 “购买” 按钮，这个按钮将调用 `purchase` 函数生成网络请求并将 `cart` 数组发送到服务器。因为网络连接错误，这个 purchase 函数不得不继续重试链接请求。现在，如果同时用户在网络请求开始之前意外地点击了他们实际上不想要的条目上的 “添加到购物车” 按钮，该怎么办呢？如果发生这种情况，并且网络请求开始了，这个 purchase 函数将会发送意外新增的条目，因为它引用了一个购物车数组，这个数组通过增加不想要的条目来对 `addItemToCart` 函数进行修改。

一个好的解决方案是让 `addItemToCart` 函数总是克隆 `cart`，修改它，然后返回克隆副本。这样可以确保保存在购物车引用上的其他功能不会受到任何改动影响。

关于这种方法有两个注意事项：

1. 在某些情况下，您可能实际上想要修改输入对象，但是当您采用这种编程实践时，您会发现这些情况非常罕见。大多数东西都可以重构，没有副作用！ (详情请看 [pure function](https://en.wikipedia.org/wiki/Pure_function))

2. 克隆大型对象的性能可能非常昂贵。幸运的是，这在实践中并不是一个大问题，因为有很多优秀的库允许这种编程方法快速，而不像手动克隆对象和数组那样占用大量内存。

**反例:**

```ts
function addItemToCart(cart: CartItem[], item: Item): void {
  cart.push({ item, date: Date.now() });
};
```

**正例:**

```ts
function addItemToCart(cart: CartItem[], item: Item): CartItem[] {
  return [...cart, { item, date: Date.now() }];
};
```

**[⬆ 返回顶部](#目录)**

### 不要写入全局函数

在 JavaScript 中，污染全局是一种糟糕的做法，因为您可能会与另一个库发生冲突，而您的 API 用户在生产中遇到异常之前也是一脸茫然毫无觉察的。让我们来思考一下这个例子：如果你想拓展 JavaScript 的原生数组新增一个方法 `diff`，用来对比两个数组之间的不同，那如何处理呢？你可能会在 `Array.prototype` 上写一个新的原型函数，但他可能会与其他尝试做相同事情的插件库发生冲突。如果其他库仅仅使用 `diff` 去查找数组中第一个元素和最后一个元素之间的差异呢？这就是为什么只使用类并简单地扩展 `Array` 会比全局会更好的原因。

**反例:**

```ts
declare global {
  interface Array<T> {
    diff(other: T[]): Array<T>;
  }
}

if (!Array.prototype.diff) {
  Array.prototype.diff = function <T>(other: T[]): T[] {
    const hash = new Set(other);
    return this.filter(elem => !hash.has(elem));
  };
}
```

**正例:**

```ts
class MyArray<T> extends Array<T> {
  diff(other: T[]): T[] {
    const hash = new Set(other);
    return this.filter(elem => !hash.has(elem));
  };
}
```

**[⬆ 返回顶部](#目录)**

### 优先使用函数式编程而不是命令式编程

尽可能支持这种编程风格。

**反例:**

```ts
const contributions = [
  {
    name: 'Uncle Bobby',
    linesOfCode: 500
  }, {
    name: 'Suzie Q',
    linesOfCode: 1500
  }, {
    name: 'Jimmy Gosling',
    linesOfCode: 150
  }, {
    name: 'Gracie Hopper',
    linesOfCode: 1000
  }
];

let totalOutput = 0;

for (let i = 0; i < contributions.length; i++) {
  totalOutput += contributions[i].linesOfCode;
}
```

**正例:**

```ts
const contributions = [
  {
    name: 'Uncle Bobby',
    linesOfCode: 500
  }, {
    name: 'Suzie Q',
    linesOfCode: 1500
  }, {
    name: 'Jimmy Gosling',
    linesOfCode: 150
  }, {
    name: 'Gracie Hopper',
    linesOfCode: 1000
  }
];

const totalOutput = contributions
  .reduce((totalLines, output) => totalLines + output.linesOfCode, 0);
```

**[⬆ 返回顶部](#目录)**

### 封装条件语句

**反例:**

```ts
if (subscription.isTrial || account.balance > 0) {
  // ...
}
```

**正例:**

```ts
function canActivateService(subscription: Subscription, account: Account) {
  return subscription.isTrial || account.balance > 0
}

if (canActivateService(subscription, account)) {
  // ...
}
```

**[⬆ 返回顶部](#目录)**

### 避免负条件

**反例:**

```ts
function isEmailNotUsed(email: string): boolean {
  // ...
}

if (isEmailNotUsed(email)) {
  // ...
}
```

**正例:**

```ts
function isEmailUsed(email): boolean {
  // ...
}

if (!isEmailUsed(node)) {
  // ...
}
```

**[⬆ 返回顶部](#目录)**

### 避免条件语句

这似乎是一项不可能完成的任务。大多数人第一次听到这个，就说，“如果没有 `if` 语句那如何表达我想要做的事呢？” 答案是，在许多情况下，您可以使用多态性来实现相同的任务。第二个问题通常是，“很好，但我为什么要这么做？” 答案是我们以前学到的一个代码整洁之道：一个函数应该只做一件事情。当你在类或者函数中有 `if` 的表达时，你就是在告诉你的用户你的函数干了不少于一件事情。记住，只干一件事。

**反例:**

```ts
class Airplane {
  private type: string;
  // ...

  getCruisingAltitude() {
    switch (this.type) {
      case '777':
        return this.getMaxAltitude() - this.getPassengerCount();
      case 'Air Force One':
        return this.getMaxAltitude();
      case 'Cessna':
        return this.getMaxAltitude() - this.getFuelExpenditure();
      default:
        throw new Error('Unknown airplane type.');
    }
  }

  private getMaxAltitude(): number {
    // ...
  }
}
```

**正例:**

```ts
abstract class Airplane {
  protected getMaxAltitude(): number {
    // shared logic with subclasses ...
  }

  // ...
}

class Boeing777 extends Airplane {
  // ...
  getCruisingAltitude() {
    return this.getMaxAltitude() - this.getPassengerCount();
  }
}

class AirForceOne extends Airplane {
  // ...
  getCruisingAltitude() {
    return this.getMaxAltitude();
  }
}

class Cessna extends Airplane {
  // ...
  getCruisingAltitude() {
    return this.getMaxAltitude() - this.getFuelExpenditure();
  }
}
```

**[⬆ 返回顶部](#目录)**

### 避免类型检测

TypeScript 是一个严格的 JavaScript语法超集，它为语言添加了可选的静态类型检查。总是喜欢指定变量、参数和返回值的类型，以充分利用 TypeScript 的全部功能。它使重构更加容易。

**反例:**

```ts
function travelToTexas(vehicle: Bicycle | Car) {
  if (vehicle instanceof Bicycle) {
    vehicle.pedal(currentLocation, new Location('texas'));
  } else if (vehicle instanceof Car) {
    vehicle.drive(currentLocation, new Location('texas'));
  }
}
```

**正例:**

```ts
type Vehicle = Bicycle | Car;

function travelToTexas(vehicle: Vehicle) {
  vehicle.move(currentLocation, new Location('texas'));
}
```

**[⬆ 返回顶部](#目录)**

### 不要过度优化

现代浏览器在运行时会在引擎底层进行很多优化。很多时候，如果你在优化，那么你只是在浪费时间。这里有一个很好的[资源](https://github.com/petkaantonov/bluebird/wiki/Optimization-killers)可以看到缺乏优化的地方。同时瞄准那些目标，直到他们被修复。

**反例:**

```ts
// 在旧的浏览器中迭代每一项未固化的 `list.length` 代价是相当昂贵的
// 因为 `list.length` 重新计算，在现代浏览器中，它已经被优化过了
for (let i = 0, len = list.length; i < len; i++) {
  // ...
}
```

**正例:**

```ts
for (let i = 0; i < list.length; i++) {
  // ...
}
```

**[⬆ 返回顶部](#目录)**

### 移除死代码

死代码和重复代码一样糟糕。没有理由把它保存在代码库中。如果没有地方调用它，就把它扔掉！如果您仍然需要它，它在您的版本历史中仍然是安全的。

**反例:**

```ts
function oldRequestModule(url: string) {
  // ...
}

function requestModule(url: string) {
  // ...
}

const req = requestModule;
inventoryTracker('apples', req, 'www.inventory-awesome.io');
```

**正例:**

```ts
function requestModule(url: string) {
  // ...
}

const req = requestModule;
inventoryTracker('apples', req, 'www.inventory-awesome.io');
```

**[⬆ 返回顶部](#目录)**

### 使用迭代器和生成器

使用生成器和iterables处理像流一样使用的数据集合。

这里有几个好处：

- 在被调用方决定要访问多少项的意义上，将被调用方与生成器实现分离
- 延迟执行，按需传输项目
- 使用 `for of` 语法迭代项的内置支持
- iterables 允许实现优化的迭代器模式

**反例:**

```ts
function fibonacci(n: number): number[] {
  if (n === 1) return [0];
  if (n === 2) return [0, 1];

  const items: number[] = [0, 1];
  while (items.length < n) {
    items.push(items[items.length - 2] + items[items.length - 1]);
  }

  return items;
}

function print(n: number) {
  fibonacci(n).forEach(fib => console.log(fib));
}

// 打印 10 个斐波那契数列
print(10);
```

**正例:**

```ts
// 生成无穷多的斐波那契数流。
// 生成器不保留所有数字的数组。
function* fibonacci(): IterableIterator<number> {
  let [a, b] = [0, 1];

  while (true) {
    yield a;
    [a, b] = [b, a + b];
  }
}

function print(n: number) {
  let i = 0;
  for (const fib in fibonacci()) {
    if (i++ === n) break;  
    console.log(fib);
  }  
}

// 打印 10 个斐波那契数列
print(10);
```

有些库允许以与本机数组类似的方式使用 iterables，通过像 `map`，`slice`，`forEach` 等等这些链式方法。请看 [itiriri](https://www.npmjs.com/package/itiriri) 中的一个高级操作例子（或者 [itiriri-async](https://www.npmjs.com/package/itiriri-async) 异步 iterables 的操作）

```ts
import itiriri from 'itiriri';

function* fibonacci(): IterableIterator<number> {
  let [a, b] = [0, 1];
 
  while (true) {
    yield a;
    [a, b] = [b, a + b];
  }
}

itiriri(fibonacci())
  .take(10)
  .forEach(fib => console.log(fib));
```

**[⬆ 返回顶部](#目录)**

## 对象与数据结构

### 使用 getters and setters

TypeScript 支持 getter/setter 语法。使用 getter 和 setter 从封装行为的对象访问数据可能比简单地在对象上查找属性要好。“为什么呢？” 你也许会问道。是的，这有如下几个理由：

- 当您想要做的不仅仅是获得一个对象属性时，您不必查找和更改代码库中的每个访问器。
- 使添加验证在执行 `set` 时变得简单。
- 封装内部逻辑。
- 当 getting 和 setting 时更容易添加日志和报错句柄。
- 你可以懒加载你的对象属性，比如从服务器获取它。

**反例:**

```ts
type BankAccount = {
  balance: number;
  // ...
}

const value = 100;
const account: BankAccount = {
  balance: 0,
  // ...
};

if (value < 0) {
  throw new Error('无法设置负余额。');
}

account.balance = value;
```

**正例:**

```ts
class BankAccount {
  private accountBalance: number = 0;

  get balance(): number {
    return this.accountBalance;
  }

  set balance(value: number) {
    if (value < 0) {
      throw new Error('无法设置负余额。');
    }

    this.accountBalance = value;
  }

  // ...
}

// 现在的 `BankAccount` 封装了校验逻辑
// 如果有一天规格发生变化，我们需要额外的验证规则，
// 我们只需要更改 `setter` 来实现，
// 保持所有相关代码不变。
const account = new BankAccount();
account.balance = 100;
```

**[⬆ 返回顶部](#目录)**

### 让对象具有 private/protected 前缀的成员变量

TypeScript 支持 `public` *(default)*, `protected` 和 `private` 访问类的成员。

**反例:**

```ts
class Circle {
  radius: number;
  
  constructor(radius: number) {
    this.radius = radius;
  }

  perimeter() {
    return 2 * Math.PI * this.radius;
  }

  surface() {
    return Math.PI * this.radius * this.radius;
  }
}
```

**正例:**

```ts
class Circle {
  constructor(private readonly radius: number) {
  }

  perimeter() {
    return 2 * Math.PI * this.radius;
  }

  surface() {
    return Math.PI * this.radius * this.radius;
  }
}
```

**[⬆ 返回顶部](#目录)**

### 偏向于使用不变性

TypeScript 的类型系统允许你将接口/类上的单个属性标记为 *readonly*。这允许你以一种功能性的方式工作（意想不到的突变总是不好的）。

对于更高级的方案，有一个内置类型，它采用类型 `T`，并使用映射类型将其所有属性标记为只读（请查阅 [mapped types](https://www.typescriptlang.org/docs/handbook/advanced-types.html#mapped-types)）。

**反例:**

```ts
interface Config {
  host: string;
  port: string;
  db: string;
}
```

**正例:**

```ts
interface Config {
  readonly host: string;
  readonly port: string;
  readonly db: string;
}
```

**[⬆ 返回顶部](#目录)**

### 类型 vs. 接口

当你可能需要联合或交叉时你可能需要使用类型。当你西需要`继承`和`实现`时你就使用接口。虽然没有严格的使用规则，但是你至少要选择一个使用。

TypeScript 中类型和接口的不同之处，更多细节请参考[详解](https://stackoverflow.com/questions/37233735/typescript-interfaces-vs-types/54101543#54101543)。


**反例:**

```ts
interface EmailConfig {
  // ...
}

interface DbConfig {
  // ...
}

interface Config {
  // ...
}

//...

type Shape = {
  // ...
}
```

**正例:**

```ts

type EmailConfig = {
  // ...
}

type DbConfig = {
  // ...
}

type Config  = EmailConfig | DbConfig;

// ...

interface Shape {
  // ...
}

class Circle implements Shape {
  // ...
}

class Square implements Shape {
  // ...
}
```

**[⬆ 返回顶部](#目录)**

## 类

### 类应该尽可能小

类的大小应该以它的职责为衡量。遵循 *单一职责原则* 一个类应该尽可能的小。

**反例:**

```ts
class Dashboard {
  getLanguage(): string { /* ... */ }
  setLanguage(language: string): void { /* ... */ }
  showProgress(): void { /* ... */ }
  hideProgress(): void { /* ... */ }
  isDirty(): boolean { /* ... */ }
  disable(): void { /* ... */ }
  enable(): void { /* ... */ }
  addSubscription(subscription: Subscription): void { /* ... */ }
  removeSubscription(subscription: Subscription): void { /* ... */ }
  addUser(user: User): void { /* ... */ }
  removeUser(user: User): void { /* ... */ }
  goToHomePage(): void { /* ... */ }
  updateProfile(details: UserDetails): void { /* ... */ }
  getVersion(): string { /* ... */ }
  // ...
}

```

**正例:**

```ts
class Dashboard {
  disable(): void { /* ... */ }
  enable(): void { /* ... */ }
  getVersion(): string { /* ... */ }
}

// 通过将剩余的方法移动到其他类来划分职责
// ...
```

**[⬆ 返回顶部](#目录)**

### 高内聚低耦合

内聚性定义了类成员彼此之间的关联程度。理想情况下，每个方法都应该使用类中的所有字段。

然后我们说这个类是最大的内聚性。但在实践中，这并不总是可能的，甚至不可取。但是，您应该更喜欢高凝聚力。

耦合是指两个类彼此之间的关联或依赖程度。如果其中一个类中的更改不影响另一个类，则称为低耦合类。
  
好的软件设计应该是 **高内聚 high cohesion** 和 **低耦合 low coupling** 的.

**反例:**

```ts
class UserManager {
  // 反例: 每个私有变量都由一组或另一组方法使用。
  // 它清楚地证明了这个类承担着不止一项职责。
  // 如果我只需要创建服务来获取用户的事务，
  // 我仍然被迫通过 `emailsender` 的实例。
  constructor(
    private readonly db: Database,
    private readonly emailSender: EmailSender) {
  }

  async getUser(id: number): Promise<User> {
    return await db.users.findOne({ id });
  }

  async getTransactions(userId: number): Promise<Transaction[]> {
    return await db.transactions.find({ userId });
  }

  async sendGreeting(): Promise<void> {
    await emailSender.send('Welcome!');
  }

  async sendNotification(text: string): Promise<void> {
    await emailSender.send(text);
  }

  async sendNewsletter(): Promise<void> {
    // ...
  }
}
```

**正例:**

```ts
class UserService {
  constructor(private readonly db: Database) {
  }

  async getUser(id: number): Promise<User> {
    return await this.db.users.findOne({ id });
  }

  async getTransactions(userId: number): Promise<Transaction[]> {
    return await this.db.transactions.find({ userId });
  }
}

class UserNotifier {
  constructor(private readonly emailSender: EmailSender) {
  }

  async sendGreeting(): Promise<void> {
    await this.emailSender.send('Welcome!');
  }

  async sendNotification(text: string): Promise<void> {
    await this.emailSender.send(text);
  }

  async sendNewsletter(): Promise<void> {
    // ...
  }
}
```

**[⬆ 返回顶部](#目录)**

### 优先使用组合而不是继承

正如著名的[设计模式](https://en.wikipedia.org/wiki/Design_Patterns) 中说讲的那样，你应该尽可能地 **优先使用组合而不是继承**。使用继承的理由很多，使用组合的理由也很多。这条格言的要点是，如果你的思想本能地去继承，试着去思考组合是否能更好地模拟你的问题。在某些情况下是可以的。

你也许想知道，“什么时候我们应该使用继承？” 它取决于你当下遇到的问题，但是这是一个很好的例子，说明继承比组合更优雅：
  
1. 您的继承表示一个"is-a"关系，而不是一个"has-a"关系（人-动物-用户-用户-用户详细信息）

2. 您可以重用基类中的代码（人类可以像所有动物一样移动）。

3. 您希望通过更改基类对派生类进行全局更改。（改变所有动物移动时的热量消耗）。

**反例:**

```ts
class Employee {
  constructor(
    private readonly name: string,
    private readonly email: string) {
  }

  // ...
}

// 不好，因为员工"有"税务数据。EmployeeTaxData不是员工类型
class EmployeeTaxData extends Employee {
  constructor(
    name: string,
    email: string,
    private readonly ssn: string,
    private readonly salary: number) {
    super(name, email);
  }

  // ...
}
```

**正例:**

```ts
class Employee {
  private taxData: EmployeeTaxData;

  constructor(
    private readonly name: string,
    private readonly email: string) {
  }

  setTaxData(ssn: string, salary: number): Employee {
    this.taxData = new EmployeeTaxData(ssn, salary);
    return this;
  }

  // ...
}

class EmployeeTaxData {
  constructor(
    public readonly ssn: string,
    public readonly salary: number) {
  }

  // ...
}
```

**[⬆ 返回顶部](#目录)**

### 使用链式调用

这个设计模式非常有用并且在很多库中广泛使用。它可以让你的代码更有表现力，减少冗余。更多是因为，使用链式调用将让你的代码看起来更加简洁。

**反例:**

```ts
class QueryBuilder {
  private collection: string;
  private pageNumber: number = 1;
  private itemsPerPage: number = 100;
  private orderByFields: string[] = [];

  from(collection: string): void {
    this.collection = collection;
  }

  page(number: number, itemsPerPage: number = 100): void {
    this.pageNumber = number;
    this.itemsPerPage = itemsPerPage;
  }

  orderBy(...fields: string[]): void {
    this.orderByFields = fields;
  }

  build(): Query {
    // ...
  }
}

// ...

const queryBuilder = new QueryBuilder();
queryBuilder.from('users');
queryBuilder.page(1, 100);
queryBuilder.orderBy('firstName', 'lastName');

const query = queryBuilder.build();
```

**正例:**

```ts
class QueryBuilder {
  private collection: string;
  private pageNumber: number = 1;
  private itemsPerPage: number = 100;
  private orderByFields: string[] = [];

  from(collection: string): this {
    this.collection = collection;
    return this;
  }

  page(number: number, itemsPerPage: number = 100): this {
    this.pageNumber = number;
    this.itemsPerPage = itemsPerPage;
    return this;
  }

  orderBy(...fields: string[]): this {
    this.orderByFields = fields;
    return this;
  }

  build(): Query {
    // ...
  }
}

// ...

const query = new QueryBuilder()
  .from('users')
  .page(1, 100)
  .orderBy('firstName', 'lastName')
  .build();
```

**[⬆ 返回顶部](#目录)**

## 面向对象设计

### 单一功能原则 (SRP)

正如代码整洁之道里面说的，“一个类改变的原因从来不止一个”。将一个具有很多功能的类塞进包是很诱人的，就像你在航班上只能带一个手提箱一样。这方面的问题是，您的类在概念上不具有内聚性，并且会给它许多改变的原因。减少更改类所需的次数非常重要。这很重要，因为如果一个类中有太多的功能，而您修改了其中的一部分，很难理解这将如何影响代码库中的其他依赖模块。

**反例:**

```ts
class UserSettings {
  constructor(private readonly user: User) {
  }

  changeSettings(settings: UserSettings) {
    if (this.verifyCredentials()) {
      // ...
    }
  }

  verifyCredentials() {
    // ...
  }
}
```

**正例:**

```ts
class UserAuth {
  constructor(private readonly user: User) {
  }

  verifyCredentials() {
    // ...
  }
}


class UserSettings {
  private readonly auth: UserAuth;

  constructor(private readonly user: User) {
    this.auth = new UserAuth(user);
  }

  changeSettings(settings: UserSettings) {
    if (this.auth.verifyCredentials()) {
      // ...
    }
  }
}
```

**[⬆ 返回顶部](#目录)**

### 开闭原则 (OCP)

正如 Bertrand Meyer 所说，“软件实体（类，模块，函数等等）应该打开进行拓展，但是闭合进行更改。” 这意味着什么呢？这个原则基于您应该允许用户在不更改现有代码的情况下添加新功能。

**反例:**

```ts
class AjaxAdapter extends Adapter {
  constructor() {
    super();
  }

  // ...
}

class NodeAdapter extends Adapter {
  constructor() {
    super();
  }

  // ...
}

class HttpRequester {
  constructor(private readonly adapter: Adapter) {
  }

  async fetch<T>(url: string): Promise<T> {
    if (this.adapter instanceof AjaxAdapter) {
      const response = await makeAjaxCall<T>(url);
      // 转换响应与返回
    } else if (this.adapter instanceof NodeAdapter) {
      const response = await makeHttpCall<T>(url);
      // 转换响应与返回
    }
  }
}

function makeAjaxCall<T>(url: string): Promise<T> {
  // 请求与返回 promise
}

function makeHttpCall<T>(url: string): Promise<T> {
  // 请求与返回 promise
}
```

**正例:**

```ts
abstract class Adapter {
  abstract async request<T>(url: string): Promise<T>;

  // 代码分享给子类 ...
}

class AjaxAdapter extends Adapter {
  constructor() {
    super();
  }

  async request<T>(url: string): Promise<T>{
    // 请求与返回 promise
  }

  // ...
}

class NodeAdapter extends Adapter {
  constructor() {
    super();
  }

  async request<T>(url: string): Promise<T>{
    // 请求与返回 promise
  }

  // ...
}

class HttpRequester {
  constructor(private readonly adapter: Adapter) {
  }

  async fetch<T>(url: string): Promise<T> {
    const response = await this.adapter.request<T>(url);
    // 转换响应与返回
  }
}
```

**[⬆ 返回顶部](#目录)**

### 里氏替换原则 (LSP)

对于一个简单的概念来说，它正式被定义为 “如果 S 是 T 的子类型，S 类型的对象将被 T 类型的对象替换（例如：S 类型的对象可能替换 T 类型）不会改变程序的任何可描述属性（例如：正确性，任务的执行）。” 这是一个更可怕的定义。

这里最好的解释就是如果你有一个父类和一个子类，这个基类和子类可以被互换的使用而得到正确的结果。
  
This might still be confusing, so let's take a look at the classic Square-Rectangle example. Mathematically, a square is a rectangle, but if you model it using the "is-a" relationship via inheritance, you quickly get into trouble.

**反例:**

```ts
class Rectangle {
  constructor(
    protected width: number = 0,
    protected height: number = 0) {

  }

  setColor(color: string): this {
    // ...
  }

  render(area: number) {
    // ...
  }

  setWidth(width: number): this {
    this.width = width;
    return this;
  }

  setHeight(height: number): this {
    this.height = height;
    return this;
  }

  getArea(): number {
    return this.width * this.height;
  }
}

class Square extends Rectangle {
  setWidth(width: number): this {
    this.width = width;
    this.height = width;
    return this;
  }

  setHeight(height: number): this {
    this.width = height;
    this.height = height;
    return this;
  }
}

function renderLargeRectangles(rectangles: Rectangle[]) {
  rectangles.forEach((rectangle) => {
    const area = rectangle
      .setWidth(4)
      .setHeight(5)
      .getArea(); // 反例: Returns 25 for Square. Should be 20.
    rectangle.render(area);
  });
}

const rectangles = [new Rectangle(), new Rectangle(), new Square()];
renderLargeRectangles(rectangles);
```

**正例:**

```ts
abstract class Shape {
  setColor(color: string): this {
    // ...
  }

  render(area: number) {
    // ...
  }

  abstract getArea(): number;
}

class Rectangle extends Shape {
  constructor(
    private readonly width = 0,
    private readonly height = 0) {
    super();
  }

  getArea(): number {
    return this.width * this.height;
  }
}

class Square extends Shape {
  constructor(private readonly length: number) {
    super();
  }

  getArea(): number {
    return this.length * this.length;
  }
}

function renderLargeShapes(shapes: Shape[]) {
  shapes.forEach((shape) => {
    const area = shape.getArea();
    shape.render(area);
  });
}

const shapes = [new Rectangle(4, 5), new Rectangle(4, 5), new Square(5)];
renderLargeShapes(shapes);
```

**[⬆ 返回顶部](#目录)**

### Interface Segregation Principle (ISP)

ISP states that "Clients should not be forced to depend upon interfaces that they do not use.". This principle is very much related to the Single Responsibility Principle.
What it really means is that you should always design your abstractions in a way that the clients that are using the exposed methods do not get the whole pie instead. That also include imposing the clients with the burden of implementing methods that they don’t actually need.

**反例:**

```ts
interface SmartPrinter {
  print();
  fax();
  scan();
}

class AllInOnePrinter implements SmartPrinter {
  print() {
    // ...
  }  
  
  fax() {
    // ...
  }

  scan() {
    // ...
  }
}

class EconomicPrinter implements SmartPrinter {
  print() {
    // ...
  }  
  
  fax() {
    throw new Error('Fax not supported.');
  }

  scan() {
    throw new Error('Scan not supported.');
  }
}
```

**正例:**

```ts
interface Printer {
  print();
}

interface Fax {
  fax();
}

interface Scanner {
  scan();
}

class AllInOnePrinter implements Printer, Fax, Scanner {
  print() {
    // ...
  }  
  
  fax() {
    // ...
  }

  scan() {
    // ...
  }
}

class EconomicPrinter implements Printer {
  print() {
    // ...
  }
}
```

**[⬆ 返回顶部](#目录)**

### Dependency Inversion Principle (DIP)

This principle states two essential things:

1. High-level modules should not depend on low-level modules. Both should depend on abstractions.

2. Abstractions should not depend upon details. Details should depend on abstractions.

This can be hard to understand at first, but if you've worked with Angular, you've seen an implementation of this principle in the form of Dependency Injection (DI). While they are not identical concepts, DIP keeps high-level modules from knowing the details of its low-level modules and setting them up. It can accomplish this through DI. A huge benefit of this is that it reduces the coupling between modules. Coupling is a very bad development pattern because it makes your code hard to refactor.  
  
DIP is usually achieved by a using an inversion of control (IoC) container. An example of a powerful IoC container for TypeScript is [InversifyJs](https://www.npmjs.com/package/inversify)

**反例:**

```ts
import { readFile as readFileCb } from 'fs';
import { promisify } from 'util';

const readFile = promisify(readFileCb);

type ReportData = {
  // ..
}

class XmlFormatter {
  parse<T>(content: string): T {
    // Converts an XML string to an object T
  }
}

class ReportReader {

  // 反例: We have created a dependency on a specific request implementation.
  // We should just have ReportReader depend on a parse method: `parse`
  private readonly formatter = new XmlFormatter();

  async read(path: string): Promise<ReportData> {
    const text = await readFile(path, 'UTF8');
    return this.formatter.parse<ReportData>(text);
  }
}

// ...
const reader = new ReportReader();
await report = await reader.read('report.xml');
```

**正例:**

```ts
import { readFile as readFileCb } from 'fs';
import { promisify } from 'util';

const readFile = promisify(readFileCb);

type ReportData = {
  // ..
}

interface Formatter {
  parse<T>(content: string): T;
}

class XmlFormatter implements Formatter {
  parse<T>(content: string): T {
    // Converts an XML string to an object T
  }
}


class JsonFormatter implements Formatter {
  parse<T>(content: string): T {
    // Converts a JSON string to an object T
  }
}

class ReportReader {
  constructor(private readonly formatter: Formatter) {
  }

  async read(path: string): Promise<ReportData> {
    const text = await readFile(path, 'UTF8');
    return this.formatter.parse<ReportData>(text);
  }
}

// ...
const reader = new ReportReader(new XmlFormatter());
await report = await reader.read('report.xml');

// or if we had to read a json report
const reader = new ReportReader(new JsonFormatter());
await report = await reader.read('report.json');
```

**[⬆ 返回顶部](#目录)**

## Testing

Testing is more important than shipping. If you have no tests or an inadequate amount, then every time you ship code you won't be sure that you didn't break anything.
Deciding on what constitutes an adequate amount is up to your team, but having 100% coverage (all statements and branches)
is how you achieve very high confidence and developer peace of mind. This means that in addition to having a great testing framework, you also need to use a good [coverage tool](https://github.com/gotwarlost/istanbul).

There's no excuse to not write tests. There are [plenty of good JS test frameworks](http://jstherightway.org/#testing-tools) with typings support for TypeScript, so find one that your team prefers. When you find one that works for your team, then aim to always write tests for every new feature/module you introduce. If your preferred method is Test Driven Development (TDD), that is great, but the main point is to just make sure you are reaching your coverage goals before launching any feature, or refactoring an existing one.  

### The three laws of TDD

1. You are not allowed to write any production code unless it is to make a failing unit test pass.

2. You are not allowed to write any more of a unit test than is sufficient to fail; and compilation failures are failures.

3. You are not allowed to write any more production code than is sufficient to pass the one failing unit test.

**[⬆ 返回顶部](#目录)**

### F.I.R.S.T. rules

Clean tests should follow the rules:

- **Fast** tests should be fast because we want to run them frequently.

- **Independent** tests should not depend on each other. They should provide same output whether run independently or all together in any order.

- **Repeatable** tests should be repeatable in any environment and there should be no excuse for why they fail.

- **Self-Validating** a test should answer with either *Passed* or *Failed*. You don't need to compare log files to answer if a test passed.

- **Timely** unit tests should be written before the production code. If you write tests after the production code, you might find writing tests too hard.

**[⬆ 返回顶部](#目录)**

### Single concept per test

Tests should also follow the *Single Responsibility Principle*. Make only one assert per unit test.

**反例:**

```ts
import { assert } from 'chai';

describe('AwesomeDate', () => {
  it('handles date boundaries', () => {
    let date: AwesomeDate;

    date = new AwesomeDate('1/1/2015');
    assert.equal('1/31/2015', date.addDays(30));

    date = new AwesomeDate('2/1/2016');
    assert.equal('2/29/2016', date.addDays(28));

    date = new AwesomeDate('2/1/2015');
    assert.equal('3/1/2015', date.addDays(28));
  });
});
```

**正例:**

```ts
import { assert } from 'chai';

describe('AwesomeDate', () => {
  it('handles 30-day months', () => {
    const date = new AwesomeDate('1/1/2015');
    assert.equal('1/31/2015', date.addDays(30));
  });

  it('handles leap year', () => {
    const date = new AwesomeDate('2/1/2016');
    assert.equal('2/29/2016', date.addDays(28));
  });

  it('handles non-leap year', () => {
    const date = new AwesomeDate('2/1/2015');
    assert.equal('3/1/2015', date.addDays(28));
  });
});
```

**[⬆ 返回顶部](#目录)**

### The name of the test should reveal it's intention

When a test fail, it's name is the first indication of what may have gone wrong.

**反例:**

```ts
describe('Calendar', () => {
  it('2/29/2020', () => {
    // ...
  });

  it('throws', () => {
    // ...
  });
});
```

**正例:**

```ts
describe('Calendar', () => {
  it('should handle leap year', () => {
    // ...
  });

  it('should throw when format is invalid', () => {
    // ...
  });
});
```

**[⬆ 返回顶部](#目录)**

## Concurrency

### Prefer promises vs callbacks

Callbacks aren't clean, and they cause excessive amounts of nesting *(the callback hell)*.  
There are utilities that transform existing functions using the callback style to a version that returns promises
(for Node.js see [`util.promisify`](https://nodejs.org/dist/latest-v8.x/docs/api/util.html#util_util_promisify_original), for general purpose see [pify](https://www.npmjs.com/package/pify), [es6-promisify](https://www.npmjs.com/package/es6-promisify))

**反例:**

```ts
import { get } from 'request';
import { writeFile } from 'fs';

function downloadPage(url: string, saveTo: string, callback: (error: Error, content?: string) => void) {
  get(url, (error, response) => {
    if (error) {
      callback(error);
    } else {
      writeFile(saveTo, response.body, (error) => {
        if (error) {
          callback(error);
        } else {
          callback(null, response.body);
        }
      });
    }
  });
}

downloadPage('https://en.wikipedia.org/wiki/Robert_Cecil_Martin', 'article.html', (error, content) => {
  if (error) {
    console.error(error);
  } else {
    console.log(content);
  }
});
```

**正例:**

```ts
import { get } from 'request';
import { writeFile } from 'fs';
import { promisify } from 'util';

const write = promisify(writeFile);

function downloadPage(url: string, saveTo: string): Promise<string> {
  return get(url)
    .then(response => write(saveTo, response));
}

downloadPage('https://en.wikipedia.org/wiki/Robert_Cecil_Martin', 'article.html')
  .then(content => console.log(content))
  .catch(error => console.error(error));  
```

Promises supports a few helper methods that help make code more conscise:  

| Pattern                  | Description                                |  
| ------------------------ | -----------------------------------------  |  
| `Promise.resolve(value)` | Convert a value into a resolved promise.   |  
| `Promise.reject(error)`  | Convert an error into a rejected promise.  |  
| `Promise.all(promises)`  |Returns a new promise which is fulfilled with an array of fulfillment values for the passed promises or rejects with the reason of the first promise that rejects. |
| `Promise.race(promises)`|Returns a new promise which is fulfilled/rejected with the result/error of the first settled promise from the array of passed promises. |

`Promise.all` is especially useful when there is a need to run tasks in parallel. `Promise.race` makes it easier to implement things like timeouts for promises.

**[⬆ 返回顶部](#目录)**

### Async/Await are even cleaner than Promises

With `async`/`await` syntax you can write code that is far cleaner and more understandable that chained promises. Within a function prefixed with `async` keyword you have a way to tell the JavaScript runtime to pause the execution of code on the `await` keyword (when used on a promise).

**反例:**

```ts
import { get } from 'request';
import { writeFile } from 'fs';
import { promisify } from 'util';

const write = util.promisify(writeFile);

function downloadPage(url: string, saveTo: string): Promise<string> {
  return get(url).then(response => write(saveTo, response));
}

downloadPage('https://en.wikipedia.org/wiki/Robert_Cecil_Martin', 'article.html')
  .then(content => console.log(content))
  .catch(error => console.error(error));  
```

**正例:**

```ts
import { get } from 'request';
import { writeFile } from 'fs';
import { promisify } from 'util';

const write = promisify(writeFile);

async function downloadPage(url: string, saveTo: string): Promise<string> {
  const response = await get(url);
  await write(saveTo, response);
  return response;
}

// somewhere in an async function
try {
  const content = await downloadPage('https://en.wikipedia.org/wiki/Robert_Cecil_Martin', 'article.html');
  console.log(content);
} catch (error) {
  console.error(error);
}
```

**[⬆ 返回顶部](#目录)**

## Error Handling

Thrown errors are a good thing! They mean the runtime has successfully identified when something in your program has gone wrong and it's letting you know by stopping function
execution on the current stack, killing the process (in Node), and notifying you in the console with a stack trace.

### Always use Error for throwing or rejecting

JavaScript as well as TypeScript allow you to `throw` any object. A Promise can also be rejected with any reason object.  
It is advisable to use the `throw` syntax with an `Error` type. This is because your error might be caught in higher level code with a `catch` syntax.
It would be very confusing to catch a string message there and would make
[debugging more painful](https://basarat.gitbooks.io/typescript/docs/types/exceptions.html#always-use-error).  
For the same reason you should reject promises with `Error` types.

**反例:**

```ts
function calculateTotal(items: Item[]): number {
  throw 'Not implemented.';
}

function get(): Promise<Item[]> {
  return Promise.reject('Not implemented.');
}
```

**正例:**

```ts
function calculateTotal(items: Item[]): number {
  throw new Error('Not implemented.');
}

function get(): Promise<Item[]> {
  return Promise.reject(new Error('Not implemented.'));
}

// or equivalent to:

async function get(): Promise<Item[]> {
  throw new Error('Not implemented.');
}
```

The benefit of using `Error` types is that it is supported by the syntax `try/catch/finally` and implicitly all errors have the `stack` property which
is very powerful for debugging.  
There are also another alternatives, not to use the `throw` syntax and instead always return custom error objects. TypeScript makes this even easier.
Consider following example:

```ts
type Result<R> = { isError: false, value: R };
type Failure<E> = { isError: true, error: E };
type Failable<R, E> = Result<R> | Failure<E>;

function calculateTotal(items: Item[]): Failable<number, 'empty'> {
  if (items.length === 0) {
    return { isError: true, error: 'empty' };
  }

  // ...
  return { isError: false, value: 42 };
}
```

For the detailed explanation of this idea refer to the [original post](https://medium.com/@dhruvrajvanshi/making-exceptions-type-safe-in-typescript-c4d200ee78e9).

**[⬆ 返回顶部](#目录)**

### Don't ignore caught errors

Doing nothing with a caught error doesn't give you the ability to ever fix or react to said error. Logging the error to the console (`console.log`) isn't much better as often times it can get lost in a sea of things printed to the console. If you wrap any bit of code in a `try/catch` it means you think an error may occur there and therefore you should have a plan, or create a code path, for when it occurs.

**反例:**

```ts
try {
  functionThatMightThrow();
} catch (error) {
  console.log(error);
}

// or even worse

try {
  functionThatMightThrow();
} catch (error) {
  // ignore error
}
```

**正例:**

```ts
import { logger } from './logging'

try {
  functionThatMightThrow();
} catch (error) {
  logger.log(error);
}
```

**[⬆ 返回顶部](#目录)**

### Don't ignore rejected promises

For the same reason you shouldn't ignore caught errors from `try/catch`.

**反例:**

```ts
getUser()
  .then((user: User) => {
    return sendEmail(user.email, 'Welcome!');
  })
  .catch((error) => {
    console.log(error);
  });
```

**正例:**

```ts
import { logger } from './logging'

getUser()
  .then((user: User) => {
    return sendEmail(user.email, 'Welcome!');
  })
  .catch((error) => {
    logger.log(error);
  });

// or using the async/await syntax:

try {
  const user = await getUser();
  await sendEmail(user.email, 'Welcome!');
} catch (error) {
  logger.log(error);
}
```

**[⬆ 返回顶部](#目录)**

## Formatting

Formatting is subjective. Like many rules herein, there is no hard and fast rule that you must follow. The main point is *DO NOT ARGUE* over formatting. There are tons of tools to automate this. Use one! It's a waste of time and money for engineers to argue over formatting. The general rule to follow is *keep consistent formatting rules*.  

For TypeScript there is a powerful tool called [TSLint](https://palantir.github.io/tslint/). It's a static analysis tool that can help you improve dramatically the readability and maintainability of your code. There are ready to use TSLint configurations that you can reference in your projects:

- [TSLint Config Standard](https://www.npmjs.com/package/tslint-config-standard) - standard style rules

- [TSLint Config Airbnb](https://www.npmjs.com/package/tslint-config-airbnb) - Airbnb style guide

- [TSLint Clean Code](https://www.npmjs.com/package/tslint-clean-code) - TSLint rules inspired by the [Clean Code: A Handbook of Agile Software Craftsmanship](https://www.amazon.ca/Clean-Code-Handbook-Software-Craftsmanship/dp/0132350882)

- [TSLint react](https://www.npmjs.com/package/tslint-react) - lint rules related to React & JSX

- [TSLint + Prettier](https://www.npmjs.com/package/tslint-config-prettier) - lint rules for [Prettier](https://github.com/prettier/prettier) code formatter

- [ESLint rules for TSLint](https://www.npmjs.com/package/tslint-eslint-rules) - ESLint rules for TypeScript

- [Immutable](https://www.npmjs.com/package/tslint-immutable) - rules to disable mutation in TypeScript

Refer also to this great [TypeScript StyleGuide and Coding Conventions](https://basarat.gitbooks.io/typescript/docs/styleguide/styleguide.html) source.

### Use consistent capitalization

Capitalization tells you a lot about your variables, functions, etc. These rules are subjective, so your team can choose whatever they want. The point is, no matter what you all choose, just *be consistent*.

**反例:**

```ts
const DAYS_IN_WEEK = 7;
const daysInMonth = 30;

const songs = ['Back In Black', 'Stairway to Heaven', 'Hey Jude'];
const Artists = ['ACDC', 'Led Zeppelin', 'The Beatles'];

function eraseDatabase() {}
function restore_database() {}

type animal = { /* ... */ }
type Container = { /* ... */ }
```

**正例:**

```ts
const DAYS_IN_WEEK = 7;
const DAYS_IN_MONTH = 30;

const SONGS = ['Back In Black', 'Stairway to Heaven', 'Hey Jude'];
const ARTISTS = ['ACDC', 'Led Zeppelin', 'The Beatles'];

function eraseDatabase() {}
function restoreDatabase() {}

type Animal = { /* ... */ }
type Container = { /* ... */ }
```

Prefer using `PascalCase` for class, interface, type and namespace names.  
Prefer using `camelCase` for variables, functions and class members.

**[⬆ 返回顶部](#目录)**

### Function callers and callees should be close

If a function calls another, keep those functions vertically close in the source file. Ideally, keep the caller right above the callee.
We tend to read code from top-to-bottom, like a newspaper. Because of this, make your code read that way.

**反例:**

```ts
class PerformanceReview {
  constructor(private readonly employee: Employee) {
  }

  private lookupPeers() {
    return db.lookup(this.employee.id, 'peers');
  }

  private lookupManager() {
    return db.lookup(this.employee, 'manager');
  }

  private getPeerReviews() {
    const peers = this.lookupPeers();
    // ...
  }

  review() {
    this.getPeerReviews();
    this.getManagerReview();
    this.getSelfReview();

    // ...
  }

  private getManagerReview() {
    const manager = this.lookupManager();
  }

  private getSelfReview() {
    // ...
  }
}

const review = new PerformanceReview(employee);
review.review();
```

**正例:**

```ts
class PerformanceReview {
  constructor(private readonly employee: Employee) {
  }

  review() {
    this.getPeerReviews();
    this.getManagerReview();
    this.getSelfReview();

    // ...
  }

  private getPeerReviews() {
    const peers = this.lookupPeers();
    // ...
  }

  private lookupPeers() {
    return db.lookup(this.employee.id, 'peers');
  }

  private getManagerReview() {
    const manager = this.lookupManager();
  }

  private lookupManager() {
    return db.lookup(this.employee, 'manager');
  }

  private getSelfReview() {
    // ...
  }
}

const review = new PerformanceReview(employee);
review.review();
```

**[⬆ 返回顶部](#目录)**

### Organize imports

With clean and easy to read import statements you can quickly see the dependencies of current code. Make sure you apply following good practices for `import` statements:

- Import statements should be alphabetized and grouped.
- Unused imports should be removed.
- Named imports must be alphabetized (i.e. `import {A, B, C} from 'foo';`)
- Import sources must be alphabetized within groups, i.e.: `import * as foo from 'a'; import * as bar from 'b';`
- Groups of imports are delineated by blank lines.
- Groups must respect following order:
  - Polyfills (i.e. `import 'reflect-metadata';`)
  - Node builtin modules (i.e. `import fs from 'fs';`)
  - external modules (i.e. `import { query } from 'itiriri';`)
  - internal modules (i.e `import { UserService } from 'src/services/userService';`)
  - modules from a parent directory (i.e. `import foo from '../foo'; import qux from '../../foo/qux';`)
  - modules from the same or a sibling's directory (i.e. `import bar from './bar'; import baz from './bar/baz';`)

**反例:**

```ts
import { TypeDefinition } from '../types/typeDefinition';
import { AttributeTypes } from '../model/attribute';
import { ApiCredentials, Adapters } from './common/api/authorization';
import fs from 'fs';
import { ConfigPlugin } from './plugins/config/configPlugin';
import { BindingScopeEnum, Container } from 'inversify';
import 'reflect-metadata';
```

**正例:**

```ts
import 'reflect-metadata';

import fs from 'fs';
import { BindingScopeEnum, Container } from 'inversify';

import { AttributeTypes } from '../model/attribute';
import { TypeDefinition } from '../types/typeDefinition';

import { ApiCredentials, Adapters } from './common/api/authorization';
import { ConfigPlugin } from './plugins/config/configPlugin';
```

**[⬆ 返回顶部](#目录)**

### Use typescript aliases

Create prettier imports by defining the paths and baseUrl properties in the compilerOptions section in the `tsconfig.json`

This will avoid long relative paths when doing imports.

**反例:**

```ts
import { UserService } from '../../../services/UserService';
```

**正例:**

```ts
import { UserService } from '@services/UserService';
```

```js
// tsconfig.json
...
  "compilerOptions": {
    ...
    "baseUrl": "src",
    "paths": {
      "@services": ["services/*"]
    }
    ...
  }
...
```

**[⬆ 返回顶部](#目录)**

## Comments

The use of a comments is an indication of failure to express without them. Code should be the only source of truth.
  
> Don’t comment bad code—rewrite it.  
> — *Brian W. Kernighan and P. J. Plaugher*

### Prefer self explanatory code instead of comments

Comments are an apology, not a requirement. Good code *mostly* documents itself.

**反例:**

```ts
// Check if subscription is active.
if (subscription.endDate > Date.now) {  }
```

**正例:**

```ts
const isSubscriptionActive = subscription.endDate > Date.now;
if (isSubscriptionActive) { /* ... */ }
```

**[⬆ 返回顶部](#目录)**

### Don't leave commented out code in your codebase

Version control exists for a reason. Leave old code in your history.

**反例:**

```ts
type User = {
  name: string;
  email: string;
  // age: number;
  // jobPosition: string;
}
```

**正例:**

```ts
type User = {
  name: string;
  email: string;
}
```

**[⬆ 返回顶部](#目录)**

### Don't have journal comments

Remember, use version control! There's no need for dead code, commented code, and especially journal comments. Use `git log` to get history!

**反例:**

```ts
/**
 * 2016-12-20: Removed monads, didn't understand them (RM)
 * 2016-10-01: Improved using special monads (JP)
 * 2016-02-03: Added type-checking (LI)
 * 2015-03-14: Implemented combine (JR)
 */
function combine(a: number, b: number): number {
  return a + b;
}
```

**正例:**

```ts
function combine(a: number, b: number): number {
  return a + b;
}
```

**[⬆ 返回顶部](#目录)**

### Avoid positional markers

They usually just add noise. Let the functions and variable names along with the proper indentation and formatting give the visual structure to your code.  
Most IDE support code folding feature that allows you to collapse/expand blocks of code (see Visual Studio Code [folding regions](https://code.visualstudio.com/updates/v1_17#_folding-regions)).

**反例:**

```ts
////////////////////////////////////////////////////////////////////////////////
// Client class
////////////////////////////////////////////////////////////////////////////////
class Client {
  id: number;
  name: string;
  address: Address;
  contact: Contact;

  ////////////////////////////////////////////////////////////////////////////////
  // public methods
  ////////////////////////////////////////////////////////////////////////////////
  public describe(): string {
    // ...
  }

  ////////////////////////////////////////////////////////////////////////////////
  // private methods
  ////////////////////////////////////////////////////////////////////////////////
  private describeAddress(): string {
    // ...
  }

  private describeContact(): string {
    // ...
  }
};
```

**正例:**

```ts
class Client {
  id: number;
  name: string;
  address: Address;
  contact: Contact;

  public describe(): string {
    // ...
  }

  private describeAddress(): string {
    // ...
  }

  private describeContact(): string {
    // ...
  }
};
```

**[⬆ 返回顶部](#目录)**

### TODO comments

When you find yourself that you need to leave notes in the code for some later improvements,
do that using `// TODO` comments. Most IDE have special support for those kind of comments so that
you can quickly go over the entire list of todos.  

Keep in mind however that a *TODO* comment is not an excuse for bad code. 

**反例:**

```ts
function getActiveSubscriptions(): Promise<Subscription[]> {
  // ensure `dueDate` is indexed.
  return db.subscriptions.find({ dueDate: { $lte: new Date() } });
}
```

**正例:**

```ts
function getActiveSubscriptions(): Promise<Subscription[]> {
  // TODO: ensure `dueDate` is indexed.
  return db.subscriptions.find({ dueDate: { $lte: new Date() } });
}
```

**[⬆ 返回顶部](#目录)**

## Translations

There is work in progress for translating this to:

- Brazilian portuguese
- Chinese
- Japanese
- Korean

References will be added once translations are completed.  
Check this [discussion](https://github.com/labs42io/clean-code-typescript/issues/15) for more details and progress.
You can make an indispensable contribution to *Clean Code* community by translating this to your language.
