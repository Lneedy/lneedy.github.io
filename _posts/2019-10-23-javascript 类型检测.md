# Javascript 类型检查(Lodash 篇)

## 原始类型

_大部分原始类型都可以采用 `typeof` 做判断_

### 字符串 _String_

- 描述: 字符串是一串表示文本值的字符序列

- 判断条件: 需要注意的创建可以采用 **字面量形式**(包括 通过 `'`和 `"`定义, 直接调用`String()`), **内置函数`new String()`**创建, 注意: 直接使用 Object.prototype.toString 会大大的影响性能, 所以需要尽量多的判断筛选部分数据,以下其他函数都有如此判断!

- 代码实现:

```javascript
// result
// isString('abc')  // true
// isString(new String('abc')) // true

function isString(value) {
  const type = typeof value
  const toString = Object.prototype.toString.call
  // !Array.isArray()的判断可以筛选掉具有length的数组格式
  return (
    type === 'string' ||
    (type === 'object' &&
      value != null &&
      !Array.isArray(value) &&
      toString(value) === '[object String]')
  )
}
```

### 数值 _Number_

- 描述: 双精度 64 位二进制格式 IEEE 754 值(-(263 - 1) ~ (263 - 1)), 包括 `NaN`, `(+|-)Infinity`

- 判断条件:创建可以采用**字面量形式**, **内置函数`new Number()`**创建

- 代码实现:

```javascript
// result
// isNumber(1) // true
// isNumber(Infinity) // true
// isNumber(+Infinity) // true
// isNumber(new Number()) // true
// isNumbe(NaN) // true

function isNumber(value) {
  const type = typeof value
  const toString = Object.prototype.toString.call
  return (
    type === 'number' ||
    (type === 'object' &&
      value !== null &&
      toString(value) === '[object Number]')
  )
}
```

### 未定义 _Undefined_

- 描述: 一个声明未定义的变量的初始值，或没有实际参数的形式参数

- 判断条件: 直接采用 `===` 严格运算符判断是否等于`undefined`,(注: null == undefined 为 true)

- 代码实现:

```javascript
// result
// let foo; // 声明并未定义
// isUndefined(foo) // true
// isUndefined(undefined) // true

function isUndefined(value) {
  const type = typeof value
  return value === undefined || type === 'undefined'
}
```

### 空值 _Null_

- 描述: `null` 是一个字面量, 表示缺少的标识，指示变量未指向任何对象, 在 APIs 中`null` 常在返回类型是对象，但没关联值的地方使用。

- 判断条件: 直接采用 `===` 严格运算符判断是否等于`Null`,(注: null == undefined 为 true), 不可以用 `typeof`来判断,会返回 `object` 而不是 `null`

- 代码实现:

```javascript
// result
// let foo = null
// isNull(foo) // true
// isNull(null)

function isNull(value) {
  return value === null
}
```

### 布尔值 _Boolean_

- 描述: 布尔值是逻辑数据类型，只能具有`true`或值`false`

- 判断条件: 建可以采用**字面量形式**(可以直接赋值 `true` | `false`), **内置函数`new Boolean()`**创建

- 代码实现:

```javascript
// result
// isBoolean(true) // true
// isBoolean(false) // true
// isBoolean(new Boolean()) // true

function isBoolean(value) {
  const type = typeof value
  const toString = Object.prototype.toString.call
  return (
    value === true ||
    value === false ||
    type === 'boolean' ||
    (type === 'object' &&
      value !== null &&
      toString(value) === '[object Boolean]')
  )
}
```

## 复杂类型

_复杂类型通常指包含多种基础类型_

### 函数 _Function_

- 描述: 是一个可以被其他`diamante`或其自身调用的代码片段

- 判断条件: 函数也是一个对象,包括 `异步`相关函数, `Promise`相关函数, `Generator` 相关函数, `Proxy`相关函数,

- 代码实现:

```javascript
// result
// isFunction(async () => {}) // true => AsyncFunction
// isFunction(async function () {}()) // true => Promise
// isFunction(() => {}) // true => Function
// isFunction(function* () {}) // true => GeneratorFunction
// isFunction() // true => Proxy

function isFunction(value) {
  const type = typeof value
  const toString = Object.prototype.toString.call
  // 判断是否是对象
  if (!(value != null && (type === 'object' || type === 'function')))
    return false

  return (
    toString(value) === '[object Function]' &&
    toString(value) === '[object AsyncFunction]' &&
    toString(value) === '[object GeneratorFunction]' &&
    toString(value) === '[object Proxy]' &&
    toString(value) === '[object Promise]'
  )
}
```

### 数组 _Array_

- 描述: 数组对象是一个有序的数据（数据可以是 **原始类型** 或 **对象类型**）集合。相对于变量，数组可用于在一个变量中存储多个变量值。

- 判断条件: 可以直接使用内置方法 `Array.isArray`, 也可以直接使用 `toString`方法, 也可以采用 `.constructor`指向是否为 `Array`对象来判断

- 代码实现:

```javascript

function isArray (value) {
    const type = typeof value
    const toString = Object.prototype.toString.call
    return Array.isArray(value) || toString(value) === '[object Array]' || value.constructor === Array
}

```

### 对象 _Object_

- 描述: **Object** 构造函数为给定值创建一个对象包装器。如果给定值是 null 或 undefined，将会创建并返回一个空对象，否则，将返回一个与给定值对应类型的对象。

- 判断条件: 可以通过 `new Object()`, `Object.create()`, 或者字面量标记(初始化标记)来创建 

- 代码实现:

```javascript
// result
// isObject({}) // true
// isObject(Function) // true
// isObject([1,2,3]) // true 

function isObject (value) {
    const type = typeof value
    return value != null || (type === 'object' || type === 'function')
}

```

### 键值对结构数组 _Map_

- 描述: 对象保存键值对。任何值(对象或者原始值) 都可以作为一个键或一个值。

- 判断条件: Map只可以通过`new Map`创建, 所以只要判断`toString`是否正确就可以了, lodash 采用先判断是否支持Nodejs的`util.types.isMap`来判断,因为要求是node10+以上版本, 所以这里就删除不展示在代码中

- 代码实现: 

```javascript
// result
// isMap(new Map) // true

function isMap (value) {
    const type = typeof value
    const toString = Object.prototype.toString.call
    return type === 'object' && value !== null && toString(value) === '[object Map]'
}

```

### 唯一值数组 _Set_

- 描述: 对象是值的集合，你可以按照插入的顺序迭代它的元素。 Set中的元素只会出现一次，即 Set 中的元素是唯一的。

- 判断条件:  Map只可以通过`new Set`创建, 所以只要判断`toString`是否正确就可以了, lodash 采用先判断是否支持Nodejs的`util.types.isSet`再使用原生提供的判断方法,因为要求是node10+以上版本, 所以这里就删除不展示在代码中

- 代码实现: 

```javascript
// result
// isSet(new Set) // true

function isSet (value) {
    const type = typeof value
    const toString = Object.prototype.toString.call
    return type === 'object' && value !== null && toString(value) === '[object Set]'
}

```

### 唯一实例 _Symbol_

- 描述: 该类型的性质在于这个类型的值可以用来创建匿名的对象属性。

- 判断条件: 该数据类型不提供构造方法创建,只允许静态方法创建!

- 代码实现: 

```javascript
// result
// isSet(Symbol.iterator) // true
// isSet(Symbol.search) // true

function isSymbol (value) {
    const type = typeof value
    const toString = Object.prototype.toString.call
    return type === 'object' && value !== null && toString(value) === '[object Symbol]'
}

```


### 临时存储键值对结构数组 _WeakMap_

- 描述: 对象是一组键/值对的集合，其中的键是弱引用的。其键必须是对象，而值可以是任意的。

- 判断条件: WeakMap只可以通过`new WeakMap`创建, 所以只要判断`toString`是否正确就可以了, lodash 采用先判断是否支持Nodejs的`util.types.isWeakMap`来判断,因为要求是node10+以上版本, 所以这里就删除不展示在代码中

- 代码实现: 

```javascript
// result
// isWeakMap(new WeakMap) // true

function isWeakMap (value) {
    const type = typeof value
    const toString = Object.prototype.toString.call
    return type === 'object' && value !== null && toString(value) === '[object WeakMap]'
}

```

### 临时存储唯一值数组 _WeakSet_

- 描述: WeakSet 对象允许你将弱保持对象存储在一个集合中。

- 判断条件: WeakSet只可以通过`new WeakSet`创建, 所以只要判断`toString`是否正确就可以了, lodash 采用先判断是否支持Nodejs的`util.types.isWeakSet`来判断,因为要求是node10+以上版本, 所以这里就删除不展示在代码中

- 代码实现: 

```javascript
// result
// isWeakSet(new WeakSet) // true

function isWeakSet (value) {
    const type = typeof value
    const toString = Object.prototype.toString.call
    return type === 'object' && value !== null && toString(value) === '[object WeakSet]'
}

```

## 拓展类型

### 参数类数组对象 _Arguments_

- 判断条件: 与 Object 相似, 并且`toString`为专属的 `[object Arguments]`

- 代码实现:

```javascript
// result 
// isArguments(function () {return arguments}())

function isArguments (value) {
    const toString = Object.prototype.toString.call
    const type = typeof value
    return type === 'object' && value !== null && toString(value) === '[object Arguments]'
}
```

### 原始无限数值 _Infinity_

- 判断条件: 等于其自身

- 代码实现:

```javascript
// result 
// isInfinity(Infinity) // true

function isInfinity (value) {
    return value === Infinity
}
```

### 日期时间 _Date_

- 判断条件: 与 Object 相似, 并且`toString`为专属的 `[object Date]`

- 代码实现:

```javascript
// result 
// isDate(new Date)

function isDate (value) {
    const toString = Object.prototype.toString.call
    const type = typeof value
    return type === 'object' && value !== null && toString(value) === '[object Date]'
}
```

### 正则对象 _RegExp_

- 判断条件: 字面量, 构造函数和工厂符号都是可以的, 并且`toString`为专属的 `[object RegExp]`

- 代码实现:

```javascript
// result 
// isRegExp(new Reg) // true
// isRegExp(/?:/) // true
// isRegExp(Reg()) // true

function isRegExp (value) {
    const toString = Object.prototype.toString.call
    const type = typeof value
    return type === 'object' && value !== null && toString(value) === '[object RegExp]'
}
```

### 错误对象 _Error_

- 判断条件: 首先, Error是一个拥有 “ EvalError”，“ RangeError”，“ ReferenceError”，“ SyntaxError”，“ TypeError”或“ URIError”对象。 也包括由`DOMException`接口调用方法或访问Web Api 属性时产生的异常事件,有些包含(`message`与`name`)的非普通对象也被称为异常或者错误, 一般又调用抛出, 也可以通过构造函数和工厂符号创建!

- 代码实现:

```javascript
// result 
// isError(EvalError()) // true
// isError(Error()) // true
// isError(RangeError()) // true
// isError(ReferenceError()) // true
// isError(SyntaxError()) // true
// isError(TypeError()) // true
// isError(URIError()) // true
// isError(DOMException()) // true

function isError (value) {
    const toString = Object.prototype.toString.call
    const type = typeof value
    if (!(type === 'object' && value !== null)) return false
    return toString(value) === '[object Error]' || toString(value) === '[object DOMException]' || (typeof value.message === 'string' && typeof value.name === 'string' && !isPlainObject(value))
}

// 判断普通对象
function isPlainObject (value) {
    const toString = Object.prototype.toString.call
    const type = typeof value
    if (!(type === 'object' && value !== null) || toString(value) !== '[object Object]') return false
    if (Object.getPrototypeOf(value) === null) return true
    let proto = value
    while (Object.getPrototypeOf(proto) !== null) proto = Object.getPrototypeOf(proto)
    return Object.getPrototypeOf(value) === proto
}

```

### 二进制数据缓冲区类数组视图 _TypeArray_

- 描述: 描述一个底层的二进制数据缓存区的一个类似数组(array-like)视图, 拥有`Int8Array`, `Uint8Array`, `Uint8ClampedArray`,`Int16Array`, `Uint16Array`,`Int32Array`, `Uint32Array`, `Float32Array`, `Float64Array` 视图对象!

- 判断条件: 通过构造函数, 并且`toString`需要判断是否为 以上所有类型! 比较消耗性能`

- 代码实现:

```javascript
// result 
// isTypeArray() // true
// isRegExp(/?:/) // true
// isRegExp(Reg()) // true

function isTypeArray (value) {
    const reg = /^\[object (?:Float(?:32|64)|(?:Int|Uint)(?:8|16|32)|Uint8Clamped)Array\]$/
    const toString = Object.prototype.toString.call
    const type = typeof value
    const test = reg.test
    return type === 'object' && value !== null && test(toString(value))
}
```

### 类型化数组 _ArrayBuffer_

- 描述: 对象用来表示通用的、固定长度的原始二进制数据缓冲区.

- 判断条件: 可以通过构造函数的方式创建一个指定字节长度的ArrayBuffer 对象

```javascript
// result
// isArrayBuffer (new ArrayBuffer)  // true

function isArrayBuffer (value) {
    const type = typeof value
    const toString = Object.prototype.toString.call
    return type === 'object' && value !== null && toString(value) === '[object ArrayBuffer]'
}

```

### Dom 元素 _Element_

- 描述: 文档对象模型（DOM）中的一部分， 文档对象模型是（DOM）被浏览器展示为页面。

- 判断条件: 不是普通对象, 必须拥有nodeType 并且必须等于 1

```javascript
// isElement(document.body) // true
//         

function isElement(value) {
    const type = typeof value
    return type === 'object' && value !== null && value.nodeType === 1 && !isPlaninObject(value)
}

// 判断普通对象
function isPlainObject (value) {
    const toString = Object.prototype.toString.call
    const type = typeof value
    if (!(type === 'object' && value !== null) || toString(value) !== '[object Object]') return false
    if (Object.getPrototypeOf(value) === null) return true
    let proto = value
    while (Object.getPrototypeOf(proto) !== null) proto = Object.getPrototypeOf(proto)
    return Object.getPrototypeOf(value) === proto
}

```

## 相关基础

### "==" 的转换规则

> 1. 布尔值(Boolean)转为数值(Number)；
> 2. 字符串(String)与数值(Number)相比，将字符串(String)转为数值(Number)；
> 3. 对象(Object)，调用 `valueOf()` 得到结果后按照前面的规则转换；
> 4. `null` 与 `undefined` 是相等的；
> 5. `null` 与 `undefined` 在比较相等性时不可以转换为任何值。

### typeOf 可以检测 Function对象的类型为 Function?

> 在一些浏览器上`typeof` 检测 `Function`的值并不一致, 例如 **safari 9** 就是返回 `object`

## 引用来源

> https://developer.mozilla.org/zh-CN/ << Mozilla 开发者网络文档>>
> https://lodash.com/docs/4.17.15 `<<Lodash 函数库文档>>` 
> https://segmentfault.com/a/1190000019912961?utm_source=tag-newest `<<Lodash 是如何做类型检测的>>` 
> https://www.cnblogs.com/sungg/p/6796718.html?utm_source=itdadao&utm_medium=referral `《饿了么大前端 Node.js 进阶教程》—Javascript 基础问题—类型判断`
