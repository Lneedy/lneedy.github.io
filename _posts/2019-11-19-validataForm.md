# vue 环境下的 纠错 方法

```javascript
/*
使用方法
let sfs = validateForm({
	// 需要验证的表单的值
	form: {
		club_name: 'lin'
	},
	// 更form中的值一一对应
	rules:{
		club_name: {
	      rule: ['required', () => false, 'tel'],
	      message: '',
	      passed: false
	    }
	},
	// 可配置验证方法
	config: {
		rules: {
			// 自定义 替换本来内容
			require: (value) => value !== '',
			// 自定义 新增的内容
			requireNum: (value) => value !== null
		}
	}
})
console.log(sfs)

// 返回的参数

{
	error: <Boolean>, // 是否验证有错
	rules: {
		key: {
			passed: <Boolean>,
			// 可应用的验证规则 如果需要传递参数 用"|"分隔
			rule: ['required', () => true, 'tel', 'between|1|2']
			message: ''
		}
	}
}

*/

export const validateForm = _validateForm

function _validateForm({ form = {}, rules = {}, config = {} }) {
  if (typeof form !== 'object') {
    throw new ReferenceError('the form is not Object')
  }

  if (typeof rules !== 'object') {
    throw new ReferenceError('the rules is not Object')
  }

  if (typeof config !== 'object') {
    throw new ReferenceError('the config is not Object')
  }
  const toString = value => Object.prototype.toString.call(value)
  const notEmail = value =>
    !/\w+([-+.]\w+)*@\w+([-.]\w+)*\.\w+([-.]\w+)*/.test(value)
  const notLessThanMax = (value, max) => !(value < max)
  const notGreaterThanMin = (value, min) => !(value > min)
  const notBetween = (value, before, after) =>
    !(before < value && value < after)
  const notTel = value =>
    !/^(0|86|17951)?(13[0-9]|15[0-9]|166|17[3678]|18[0-9]|14[57])[0-9]{8}$/.test(
      value
    )
  const notEmpty = value => isEmpty(value)
  function isEmpty(value) {
    // undefined and null
    if (value == null) return true
    // string
    if (typeof value === 'string') {
      if (value.replace(/\s+/g, '') === '') return true
    }
    // object
    if (typeof value === 'object') {
      // array
      if (toString(value) === '[object Array]') {
        if (value.length === 0) return true
      } else {
        if (Object.keys(value).length === 0) return true
      }
    }
    return false
  }

  const _form = Object.assign({}, form)
  const _rules = Object.assign({}, config.rules)
  const Rules = new Map()
  Rules.set('required', notEmpty)
  Rules.set('max', notGreaterThanMin)
  Rules.set('min', notLessThanMax)
  Rules.set('between', notBetween)
  Rules.set('tel', notLessThanMax)
  Rules.set('email', notEmail)

  // 如果用户自定义 rules规则
  if (_rules !== undefined) {
    // 替换同名规则
    if (typeof _rules !== 'object') {
      throw new ReferenceError('rules is not Object')
    }
    let key = ''
    for (let i = 0; i < Object.keys(_rules).length; i++) {
      key = Object.keys(_rules)[i]
      Rules.set(key, _rules[key])
    }
  }

  const _item = {
    rule: [],
    passed: false,
    message: ''
  }
  let _formData = {}
  let result = {}
  let count = 0
  const keys = Object.keys(_form)
  for (let i = 0; i < keys.length; i++) {
    _formData[keys[i]] = Object.assign({}, _item, rules[keys[i]], {
      value: _form[keys[i]]
    })
  }

  // 进行验证
  //
  for (let i = 0; i < Object.keys(_formData).length; i++) {
    let key = Object.keys(_formData)[i]
    if (toString(_formData[key].rule) === '[object Array]') {
      for (let j = 0; j < _formData[key].rule.length; j++) {
        if (typeof _formData[key].rule[j] === 'string') {
          let runRules = _formData[key].rule[j].split('|')
          let args = []
          for (let m = 0; m < runRules.length; m++) {
            if (m !== 0) {
              args.push(runRules[m])
            }
          }

          if (Rules.get(runRules[0])(_formData[key].value, ...args)) {
            _formData[key].passed = true
          }
        } else if (toString(rules[key].rule[j]) === '[object Function]') {
          // 用户自定义
          if (rules[key].rule[j]()) {
            _formData[key].passed = true
          }
        }
        // 如果错误  自增
        if (_formData[key].passed) count++
      }
    }
  }

  for (let i = 0; i < Object.keys(_formData).length; i++) {
    let key = Object.keys(_formData)[i]
    delete _formData[key].value
  }
  result = Object.assign({}, _formData)
  return {
    rules: result,
    error: count > 0
  }
}
```
