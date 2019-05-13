# keyboard-license-plate
vue车牌号模拟键盘

[online demo](https://dongj0316.github.io/keyboard-license-plate/demo/)

## Installation

## Use

```bash
$ npm install keyboard-license-plate -S
```

```js
import Vue from 'vue'
import KeyboardLicensePlate from 'keyboard-license-plate'

Vue.use(KeyboardLicensePlate)
```

``` html
<keyboard-license-plate ref="keyboard" v-model="licensePlateNumber"/>
```

## API

#### component attributes:
| Attr. Name | Type | Description | Required | Default Value |
|-----|-----|-----|-----|-----|
| value | String | 绑定值 | N | - |
| placeholder | String | 占位文本 | N | 车牌号码 |
| default-province | String | 车牌号首位默认值 | N | 湘 |
| z-index | Number | 键盘z-index样式值 | N | 5000 |
| disabled-keys | Array | 禁用按钮值 | N | ['I', 'O'] |

#### instance methods:
- `focus()` 获取焦点
- `blur()` 取消焦点

#### instance attributes:
- `valid` 布尔值，表示车牌号码格式是否正确

## Local setup

```
npm install
npm run serve
```

## License

[MIT](https://opensource.org/licenses/MIT)