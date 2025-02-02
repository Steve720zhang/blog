一个组件的测试可以分为以下几类的测试:

* 基础样式
* 模拟事件
* 异步调用

### 一些配置

`jest.config.js` 的一些常见配置属性如下:

```js
module.exports = {
  // 可以指定测试环境
  testEnviroment: 'jest-environment-node' | 'jest-enviroment-jsdom',
  // 指定模块加载目录
  moduleDirectories: ['node_modules', path.join(__dirname, 'src'), 'shared']
  // identity-obj-proxy 支持在 jest 中引入 css, 同时支持 css 的模块化
  moduleNameMapper: {
    "\\.(css|less|scss)$": "identity-obj-proxy",
  },
  // before jest is loaded(不依赖 jest)
  setupFiles: []
  // after jest is loaded(依赖 jest)
  setupTestFrameworkScriptFile: require.resolve('./test/setup-tests.js')
  // 测试覆盖率收集目录
  collectCoverageFrom: ['src/**/*.js']
  // 指定测试覆盖率需要需要达到的阈值
  coverageThreshold: {
    global: {
      statements: 80,
      branches: 80,
      lines: 80,
      functions: 80,
    }
  }
  // 增强 watch 模式体验: $ npm install --save-dev jest-watch-typeahead
  watchPlugins: [
    'jest-watch-typeahead/filename',
    'jest-watch-typeahead/testname',
  ]
}
```

[jest-emotion](https://github.com/emotion-js/emotion): 如果 css 中具体样式发生改变也要重新跑 snapshot

### jest-dom

jest-dom 封装了测试 dom 的方法。报错的信息可以更加准确。

```
import 'jest-dom/extend-expect'
```

这样子就可以使用如下方法

```js
expect(input).toHavaAttribute('type', 'number') // 是否有某个属性
expect(..).toHaveTextContent()                  // 是否有某个内容
```

### dom-test-library

`dom-test-library` 的优势。

* 增加了更多的操作, 比如根据 label 找对应的节点;
* 支持正则匹配;

```js
import { queries } from 'dom-testing-library'
```

### @testing-library/react 的使用

`@testing-library/react` 在 `dom-test-library` 的基础上查找 React 组件。

```js
import 'react-testing-library/cleanup-after-each' // 自动完成每次的回收
```

* Debug the DOM state during tests using react-testing-library’s debug function,

```js
test('...', () => {
  const { debug } = render(<Component />)
  debug()
  // or
  debug(<SomeComponent />)
})
```

* Test React Component Event Handlers with fireEvent from react-testing-library

```js
import { fireEvent } from 'react-testing-library'

fireEvent.change()
```

* Assert rendered text with react-testing-library

以下罗列几种断言方式

* 方式一: expect(container).toHaveTextContent(/the number is invalid/i)
* 方式二: getByText(/the number is invalid/i)
* 方式三: expect(getByText(/the number is invalid/i)).toBeTruthy()
* 方式四: 配合 `data-testid` 属性可以使用 expect(getByTestId('...')).toHaveTextContent(/the number is invalid/i)

* Test prop updates with react-testing-library

```js
test('...', () => {
  const { rerender } = render(<Component />)
  rerender(<SomeComponent />)
})
```

* `getByLabelText`       (form inputs)
* `getByPlaceholderText` (only if your input doesn’t have a label — less accessible!)
* `getByText`            (buttons and headers)
* `getByAltText`         (images)
* `getByTestId`          (use this for things like dynamic text or otherwise odd elements you want to test)

上述每一个方法都有对应的 `queryByFoo` 替代方法。以 `query` 开头的方法找不到的话会返回 null, 以 `get` 开头的方法找不到的话会 throw。

如果这些都不会让你确切地知道你在找什么, render 方法也会返回映射到 container 属性的 DOM 元素，所以你可以像 `container.querySelector('body #root')` 一样使用它。

* Mock HTTP Requests with jest.mock in React Component Tests

```js
import { render, fireEvent, wait } from 'react-testing-library'
import {loadGreeting as mockLoadGreeting} from '../api'

jest.mock('../api', () => {
  return {
    loadGreeting: jest.fn(subject =>
      Promise.resolve({data: {greeting: `Hi ${subject}`}}),
    ),
  }
})

test('loads greetings on click', () => {
  const {getByLabelText, getByText, getByTestId} = render(<GreetingLoader />)
  const nameInput = getByLabelText(/name/i)
  const loadButton = getByText(/load/i)
  nameInput.value = 'Mary'
  fireEvent.click(loadButton)
  await wait(() => expect(getByTestId('greeting')).toHaveTextContent())
  expect(mockLoadGreeting).toHaveBeenCalledTimes(1)
  expect(mockLoadGreeting).toHaveBeenCalledWith('Mary')
})
```

* Mock react-transition-group in React Component Tests with jest.mock

比如 react-transition-group 动画库也是存在异步库, 它会在 1 s 后将 Children 隐藏, 这时候可以使用 `mock` 来处理。

```js
jest.mock('react-transition-group', () => {
  return {
    CSSTransition: props => (props.in ? props.children : null),
  }
})
```

* 将 `console.error()` mock 掉

```js
beforeEach(() => {
  jest.spyOn(console, 'error').mockImplementation(() => {})
})

afterEach(() => {
  console.error.mockRestore()
})
```

```js
// componentDidCatch 里的两个参数
const error = expect.any(Error)
const info = {componentStack: expect.stringContaining('Bomb')}
expect(mockReportError).toHaveBeenCalledWith(error, info)
```