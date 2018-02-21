## Table Of Contents

  - [Vue Instance](#vue-instance)
    - [Data and Methods](#data-and-methods)
    - [Instance Lifecycle Hooks](#instance-lifecycle-hooks)
  - [Template Syntax](#template-syntax)
    - [Text](#text)
    - [Raw HTML](#raw-html)
    - [Attributes](#attributes)
    - [Using JavaScript Expressions](#using-javascript-expressions)
    - [Directives](#directives)
    - [Arguments](#arguments)
    - [Modifiers](#modifiers)
    - [Shorthands](#shorthands)
  - [Computed Properties and Watchers](#computed-properties-and-watchers)
    - [Computed Properties](#computed-properties)
    - [Computed Setter](#computed-setter)
    - [Computed Caching vs Methods](#computed-caching-vs-methods)
    - [Watchers](#watchers)
  - [Class and Style Bindings](#class-and-style-bindings)
    - [Binding HTML Classes](#binding-html-classes)
    - [Binding Inline Styles](#binding-inline-styles)
  - [Conditional Rendering](#conditional-rendering)
    - [v-if, v-else-if, v-else](#v-if-v-else-if-v-else)
    - [Conditional Groups with ```v-if``` on ```<template>```](#conditional-groups-with-v-if-on-template)
    - [Controlling Reusable Elements with key](#controlling-reusable-elements-with-key)
    - [v-show](#v-show)
    - [v-if with v-for](#v-if-with-v-for)
  - [List Rendering](#list-rendering)
    - [v-for](#v-for)
    - [v-of](#v-of)
    - [Key](#key)
    - [Array Change Detection](#array-change-detection)
    - [Object Change Detection](#object-change-detection)
    - [v-for with a Range](#v-for-with-a-range)
    - [v-for on a ```<template>```](#v-for-on-a-template)
    - [v-for with v-if](#v-for-with-v-if)
  - [Event Handling](#event-handling)
  - [Form Input Bindings](#form-input-bindings)
  - [Components](#components)
    - [What are Components](#what-are-components)
    - [Using Components](#using-components)
      - [Global](#global)
      - [Local](#local)
      - [DOM Template Parsing Caveats](#dom-template-parsing-caveats)
      - [data Must Be a Function](#data-must-be-a-function)
    - [Props](#props)
      - [One-Way Data Flow](#one-way-data-flow)
      - [Prop Validation](#prop-validation)
      - [Non-Prop Attributes](#non-prop-attributes)
    - [Custom Events](#custom-events)
      - [Binding Native Events to Components](#binding-native-events-to-components)
      - [```.sync``` Modifier](#sync-modifier)
      - [Custom input component](#custom-input-component)
      - [```model``` option](#model-option)
      - [Non Parent-Child Communication](#non-parent-child-communication)
    - [Content Distribution with Slots](#content-distribution-with-slots)
      - [Single Slot](#single-slot)
      - [Named Slots](#named-slots)
      - [Scoped Slots](#scoped-slots)
    - [Dynamic Components](#dynamic-components)
    - [Other stuffs](#other-stuffs)
    - [Note](#note)
  - [Reactivity in Depth](#reactivity-in-depth)
  - [Some best practices I might forget](#some-best-practices-i-might-forget)
    - [Avoid ```v-if``` with ```v-for```](#avoid-v-if-with-v-for)
    - [Private property names](#private-property-names)
    - [Tightly coupled component names](#tightly-coupled-component-names)
    - [Order of words in component names](#order-of-words-in-component-names)
    - [Prop name casing](#prop-name-casing)
    - [Simple computed properties](#simple-computed-properties)
    - [Component/instance options order](#componentinstance-options-order)
    - [Element attribute order](#element-attribute-order)

## Vue Instance

```js
var vm = new Vue({
  // options
})
```

### Data and Methods

Khi Vue instance được tạo, các props trong **data** của nó sẽ được thêm vào **reactivity system**. Khi có thay đổi, view sẽ được cập nhật lại theo giá trị mới

```js
var data = { a: 1 }

var vm = new Vue({
  data: data
})

vm.a === data.a // => true

vm.a = 2
data.a // => 2

data.a = 3
vm.a // => 3
```

Note:

- Chỉ các props có trong **data** tại thời điểm tạo instance mới được thêm vào **reactivity system**
- Có thể dùng ```Object.freeze(data)``` để ngăn không cho thay đổi
- Ngoài các **data props**, Vue instance còn có các built-in props khác, các props này được prefix bằng dấu **$** để phân biệt với các user-defined props. Ví dụ như: ```$el```, ```$watch```...
- Không dùng arrow function trong các option props hoặc callback của options props vì parent context k phải Vue instance

### Instance Lifecycle Hooks

![lifecycle image](./images/lifecycle.png)

## Template Syntax

### Text

```html
<span>Message: {{ msg }}</span>
```

Chỉ bind, không track update:

```html
<span v-once>This will never change: {{ msg }}</span>
```

### Raw HTML

```html
<span v-html="rawHtml"></span>
```

Note: chỉ dùng được với **components**

### Attributes

```html
<div v-bind:id="dynamicId"></div>
```

### Using JavaScript Expressions

```html
{{ number + 1 }}

{{ ok ? 'YES' : 'NO' }}

{{ message.split('').reverse().join('') }}

<div v-bind:id="'list-' + id"></div>
```

### Directives

Directive nhận vào 1 JavaScript expression (ngoại trừ ```v-for```)

```html
<p v-if="seen">Now you see me</p>
```

### Arguments

Một số directive có thể nhận vào argument (đằng sau dấu ```:```)

```html
<a v-bind:href="url"> ... </a>
<a v-on:click="doSomething"> ... </a>
```

### Modifiers

```.prevent``` modifier trong ví dụ sau cho ```v-on``` directive biết là cần gọi ```event.preventDefault()``` khi trigger event

```html
<form v-on:submit.prevent="onSubmit"> ... </form>
```

### Shorthands

v-bind:

```html
<!-- full syntax -->
<a v-bind:href="url"> ... </a>

<!-- shorthand -->
<a :href="url"> ... </a>
```

v-on:

```html
<!-- full syntax -->
<a v-on:click="doSomething"> ... </a>

<!-- shorthand -->
<a @click="doSomething"> ... </a>
```

## Computed Properties and Watchers

### Computed Properties

> Đúng như tên gọi. Computed Properties dùng để định nghĩa các props cần được tính toán. Sử dụng giống như data props thông thường.

```js
var vm = new Vue({
  el: '#demo',
  data: {
    firstName: 'Foo',
    lastName: 'Bar'
  },
  computed: {
    fullName: function () {
      return this.firstName + ' ' + this.lastName
    }
  }
})
```

### Computed Setter

```js
computed: {
  fullName: {
    // getter
    get: function () {
      return this.firstName + ' ' + this.lastName
    },
    // setter
    set: function (newValue) {
      var names = newValue.split(' ')
      this.firstName = names[0]
      this.lastName = names[names.length - 1]
    }
  }
}
```

### Computed Caching vs Methods

Vue sẽ track các dependencies mà Computed Props sử dụng. Khi các dependencies này thay đổi thì Computed Props cũng sẽ được cập nhật lại. Còn không, nó sẽ trả về giá trị cũ

Còn method thì sẽ luôn được thực thi lại mỗi khi re-render

```js
// in template
<p>Reversed message: "{{ reverseMessage() }}"</p>

// in component
methods: {
  reverseMessage: function () {
    return this.message.split('').reverse().join('')
  }
}
```

### Watchers

Bên cạnh Computed Props, Vue còn cung cấp 1 cách khác để *observe and react to data changes*: **watch properties**

```js
var vm = new Vue({
  el: '#demo',
  data: {
    firstName: 'Foo',
    lastName: 'Bar',
    fullName: 'Foo Bar'
  },
  watch: {
    firstName: function (val) {
      this.fullName = val + ' ' + this.lastName
    },
    lastName: function (val) {
      this.fullName = this.firstName + ' ' + val
    }
  }
})
```

Note: Thông thường nên sử dụng Computed Props. Chỉ sử dụng Watchers khi Computed Props không đáp ứng được yêu cầu. Watchers thường được dùng khi cần thực hiện async c

## Class and Style Bindings

### Binding HTML Classes

Directive ```v-bind``` khi dùng cho ```class``` sẽ hỗ trợ kết hợp 2 loại syntax: Object và Array

Object:

```html
<div v-bind:class="{ active: isActive }"></div>
```

Array:

```html
<div v-bind:class="[activeClass, errorClass]"></div>
```

Hoặc Object trong Array

```html
<div v-bind:class="[{ active: isActive }, errorClass]"></div>
```

Pattern phổ biến là dùng Object và Computed Props

```js
// in template
<div v-bind:class="classObject"></div>

// in option object
data: {
  isActive: true,
  error: null
},
computed: {
  classObject: function () {
    return {
      active: this.isActive && !this.error,
      'text-danger': this.error && this.error.type === 'fatal'
    }
  }
}
```

### Binding Inline Styles

```v-bind:style``` tương tự ```v-bind:class```

## Conditional Rendering

### v-if, v-else-if, v-else
```html
<div v-if="type === 'A'">
  A
</div>
<div v-else-if="type === 'B'">
  B
</div>
<div v-else-if="type === 'C'">
  C
</div>
<div v-else>
  Not A/B/C
</div>
```

Note: ```v-else-if```, ```v-else``` element phải được viết ngay sau ```v-if``` element

### Conditional Groups with ```v-if``` on ```<template>```

Khi muốn toggle nhiều element có thể gom lại bằng ```<template>```. ```<template>``` sẽ k được thêm vào DOM thật

```html
<template v-if="ok">
  <h1>Title</h1>
  <p>Paragraph 1</p>
  <p>Paragraph 2</p>
</template>
```

### Controlling Reusable Elements with key

Vue sẽ cố gắng reuse nhiều nhất có thể để tăng hiệu năng

```html
<template v-if="loginType === 'username'">
  <label>Username</label>
  <input placeholder="Enter your username">
</template>
<template v-else>
  <label>Email</label>
  <input placeholder="Enter your email address">
</template>
```

Trong ví dụ trên khi ```loginType``` thay đổi, ```<label>``` và ```<input>``` đều được reuse vì element type không đổi. Do đó Vue sẽ chỉ cập nhật value bên trong ```<label>``` và placeholder của ```<input>```. Value mà người dùng đã nhập vào ```<input>``` sẽ được giữ nguyên

Nếu không muốn reuse, phải thêm ```key``` để Vue biết đó là 2 element khác nhau, cần replace

```html
<template v-if="loginType === 'username'">
  <label>Username</label>
  <input placeholder="Enter your username" key="username-input">
</template>
<template v-else>
  <label>Email</label>
  <input placeholder="Enter your email address" key="email-input">
</template>
```

### v-show

```html
<h1 v-show="ok">Hello!</h1>
```

Khác với ```v-if```, element được gắn ```v-show``` luôn được render, chỉ toggle css display để ẩn hiện

### v-if with v-for

Khi dùng chung ```v-if``` với ```v-for```, ```v-for``` sẽ có độ ưu tiên cao hơn

## List Rendering

### v-for

Array:

```html
<ul id="example-2">
  <li v-for="(item, index) in items">
    {{ parentMessage }} - {{ index }} - {{ item.message }}
  </li>
</ul>
```

Object:

```html
<div v-for="(value, key, index) in object">
  {{ index }}. {{ key }}: {{ value }}
</div>
```

### v-of

Tương tự v-for

### Key

Khi render một list các element Vue sử dụng **in-place patch strategy**. Khi thứ tự của các element thay đổi, thay vì di chuyển các element, Vue sẽ update từng index, tạo mới hoàn toàn element (nếu cần) để khớp với VDOM. Khác với React, Vue cho rằng điều này là tốt cho hiệu năng (FIXME). Nhưng sẽ không phù hợp nếu muốn giữ lại state của các component -> phải dùng ```key``` để Vue có thể reuse, reorder các element.

Source: <https://vuejs.org/v2/guide/list.html#key>

### Array Change Detection

Các mutation method của các array nằm trong **reactivity system** sẽ được Vue wrap lại và tự động trigger view update. Ví dụ như: ```push()```,  ```pop()```, ```shift()```...

Đối với các non-mutating method như ```filter()```, ```map()```... thì ta phải gán lại array cũ bằng kết quả trả về của các method này

```js
example.items = example.items.filter(function (item) {
  return item.message.match(/Foo/)
})
```

Vue không detect array thay đổi khi:

- Mutate trực tiếp: ```vm.items[indexOfItem] = newValue``` -> phải dùng ```Vue.set()``` hoặc ```splice()```
- Cập nhật length: ```vm.items.length = newLength``` -> phải dùng ```splice()```

### Object Change Detection

Vue không cho phép thêm reactive props ở root level sau khi đã khởi tạo. Chỉ có thể thêm props cho nested object. Sử dụng 1 trong 2 cách sau:

Mutate:

```js
Vue.set(object, key, value)

// hoặc
vm.$set(this.userProfile, 'age', 27)
```

Non-mutate:

```js
this.userProfile = Object.assign({}, this.userProfile, {
  age: 27,
  favoriteColor: 'Vue Green'
})
```

### v-for with a Range

Loop 10 lần:

```html
<div>
  <span v-for="n in 10">{{ n }} </span>
</div>
```

### v-for on a ```<template>```

Tương tự với ```v-if```, có thể dùng ```<template>``` với ```v-for``` để render nhiều element

```html
<ul>
  <template v-for="item in items">
    <li>{{ item.msg }}</li>
    <li class="divider"></li>
  </template>
</ul>
```

### v-for with v-if

Nếu ```v-for``` và ```v-if``` được gắn trên cùng 1 node thì ```v-for``` sẽ có độ ưu tiên cao hơn. Nghĩa là loop qua từng item và check điều kiện trong ```v-if``` đối với mỗi item, nếu true thì render, false thì skip

## Event Handling

```html
<button v-on:click="counter += 1">Add 1</button>

<!-- Hoặc method name -->
<button v-on:click="greet">Greet</button>

<!-- Truyền tham số -->
<button v-on:click="say('hi')">Say hi</button>

<!-- Truyền event object -->
<button v-on:click="say('hi', $event)">Say hi</button>
```

Event Modifiers: <https://vuejs.org/v2/guide/events.html#Event-Modifiers>

## Form Input Bindings

Source: <https://vuejs.org/v2/guide/forms.html>

## Components

### What are Components

> Vue componen cũng là Vue instance vì vậy nó cũng nhận vào option object (ngoại trừ 1 số root-specific option như ```el```) và có chung lifecycle hook

### Using Components

#### Global

```js
Vue.component('my-component', {
  // options
})
```

Note: Phải khai báo component trước root instance muốn sử dụng component

#### Local

Khai báo Component trong phạm vị nội bộ của 1 instance hoặc component khác

```js
var Child = {
  template: '<div>A custom component!</div>'
}

new Vue({
  // ...
  components: {
    // <my-component> will only be available in parent's template
    'my-component': Child
  }
})
```

#### DOM Template Parsing Caveats

HTML template sẽ được parse, normalize bởi browser trước khi chuyển sang cho Vue xử lý. Vì vậy HTML template phải hợp lệ. Để tránh lỗi như kiểu bên trong ```<table>``` phải là ```tr``` hoặc ```th```, Vue cung cấp work around là dùng attribute ```is```

```html
<table>
  <tr is="my-row"></tr>
</table>
```

#### data Must Be a Function

```data``` props của option trong component phải là **function**. Trong doc bảo là do để tránh data bị share khi reuse component

### Props

```js
Vue.component('child', {
  // camelCase in JavaScript
  props: ['myMessage'],
  template: '<span>{{ myMessage }}</span>'
})
```

```html
<!-- kebab-case in HTML -->
<child my-message="hello!"></child>
```

Note: Nếu muốn truyền props là số thì phải dùng ```v-bind```

#### One-Way Data Flow

NOTE: Object và array pass by reference. Vì vậy mutate props là object hoặc array sẽ mutate state của thằng cha -> SIDA 

#### Prop Validation

```js
Vue.component('example', {
  props: {
    // basic type check (`null` means accept any type)
    propA: Number,
    // multiple possible types
    propB: [String, Number],
    // a required string
    propC: {
      type: String,
      required: true
    },
    // a number with default value
    propD: {
      type: Number,
      default: 100
    },
    // object/array defaults should be returned from a
    // factory function
    propE: {
      type: Object,
      default: function () {
        return { message: 'hello' }
      }
    },
    // custom validator function
    propF: {
      validator: function (value) {
        return value > 10
      }
    }
  }
})
```

#### Non-Prop Attributes

Các props mà không được định nghĩa trong component sẽ được tự động thêm vào root element của component. Nó sẽ replace default value attribute đó của root element. Ngoại trừ ```style``` và ```class``` sẽ được merge thay vì replace

### Custom Events

Vue child component sẽ **emit event** ngược lên cho parent component để implement one-way data flow

```html
<div id="counter-event-example">
  <p>{{ counter }}</p>
  <button-counter v-on:increase="onIncreaseCounter"></button-counter>
</div>
```

```js
// child
Vue.component('button-counter', {
  template: '<button v-on:click="onIncrease">+</button>',
  methods: {
    onIncrease: function() {
      this.$emit('increase');
    }
  }
})
```

```js
// parent
new Vue({
  el: '#counter-event-example',
  data: {
    counter: 0,
  },
  methods: {
    onIncreaseCounter: function() {
      this.counter++;
    }
  }
});
```

Note: Vẫn có thể dùng callback function. Dùng ```v-bind``` để pass function xuống cho component

#### Binding Native Events to Components

Bind event vào root element của component

```html
<my-component v-on:click.native="doTheThing"></my-component>
```

#### ```.sync``` Modifier

Shortcut pass value và bind event update parent data

```html
<comp :prop-a.sync="parentData"></comp>

<!-- Tương đương với -->
<comp :prop-a="parentData" @update:propA="val => parentData = val"></comp>
```

```js
// in child component
this.$emit('update:propA', newValue)
```

#### Custom input component

```v-model``` thực chất chỉ là syntatic sugar syntax:

```html
<input v-model="something">

<!-- Tương đương với -->
<input
  v-bind:value="something"
  v-on:input="something = $event.target.value"
>
```

Tương tự với component

```html
<custom-input v-model="something" />

<!-- Tương đương với -->
<custom-input
  v-bind:value="something"
  v-on:input="value => { something = value }"
/>
```

-> Để custom component dùng được ```v-model``` thì component đó phải nhận vào prop value và emit event ```input``` với value mới

```js
Vue.component('x2-input', {
  template: `
      <input :value="value" @input="onChange($event.target.value)" />
  `,
  props: ['value'],
  methods: {
      onChange(newValue) {
          this.$emit('input', newValue + newValue);
      }
  }
})
```

#### ```model``` option

```v-model``` mặc định sử dụng ```value``` prop và ```input``` event. Nhưng có thể dùng ```model``` option để customize

```js
Vue.component('my-checkbox', {
  model: {
    prop: 'checked',
    event: 'change'
  },
  props: {
    checked: Boolean,
    // this allows using the `value` prop for a different purpose
    value: String
  },
  // ...
})
```

#### Non Parent-Child Communication

Event bus: dùng để communicate giữa các component k có quan hệ cha con

```js
var bus = new Vue()
const vm = new Vue({
    el: '#counter-event-example',
    components: {
        'comp-a': {
            template: `<div>{{msg}}</div>`,
            data: function() {
                return {
                    msg: '',
                };
            },
            created: function() {
                bus.$on('hey', () => {
                    this.data = 'event fired'
                });
            }
        },
        'comp-b': {
            template: `<button @click="bus.$emit('hey')">emit</button>`
        }
    }
});
```

Thông thường sẽ k dùng cách này (anti pattern) mà sẽ dùng ```state-management pattern``` như là ```Vuex```

### Content Distribution with Slots

#### Single Slot

```html
<my-component>Hello VueJS</my-component>
```

```js
new Vue({
  el: '#slot-example',
  components: {
    'my-component': {
        template: `
            <div>
                <slot>Fallback content</slot>
            </div>
        `,
    },
  }
})
```

#### Named Slots

```html
<my-component>
  <h1 slot="header">Hello VueJS</h1>
  <h3 slot="content">I'm DHL</h3>
  <h4>I'm learning VueJS</h4>
</my-component>
```

```js
const vm = new Vue({
  el: '#slot-example',
  components: {
      'my-component': {
          template: `
              <div>
                  <slot name="header">Fallback header</slot>
                  <div style="color: red">
                      <slot name="content">Fallback content</slot>
                  </div>
                  <div style="color: blue">
                      <slot>CATCH ALL CONTENT WIHOUT SLOT ATTRS</slot>
                  </div>
              </div>
          `,
      },
  }
});
```

#### Scoped Slots

Dùng để render children data trên template của parent

```html
<my-component>
  <template slot-scope="propsFromChild"> // có thể dùng destructuring
    <div>Hello from parent</div>
    <div>{{propsFromChild.message}}</div>
  </template>
</my-component>
```

```js
const vm = new Vue({
  el: '#slot-example',
  components: {
      'my-component': {
          template: `
              <div>
                  <slot message="Hello from child"></slot>
              </div>
          `,
      },
  }
});
```

### Dynamic Components

```html
<div id="example">
  <button v-on:click="switchComponent">Switch component</button>
  <component v-bind:is="currentComponent"></component>
</div>
```

```js
const vm = new Vue({
    el: '#example',
    data: {
        currentComponent: 'comp-a'
    },
    components: {
        'comp-a': {
            template: `<div>Component A</div>`,
        },
        'comp-b': {
            template: `<div>Component B</div>`,
        },
    },
    methods: {
        switchComponent() {
            this.currentComponent = this.currentComponent === 'comp-a' ? 'comp-b' : 'comp-a';
        }
    }
});
```

Note: Có thể wrap ```<component>``` bằng ```<keep-alive>``` để giữ state của component hoặc tránh re-render

### Other stuffs

- [Async Components](https://vuejs.org/v2/guide/components.html#Async-Components)
- [Recursive Components](https://vuejs.org/v2/guide/components.html#Recursive-Components)
- [Circular References Between Components](https://vuejs.org/v2/guide/components.html#Circular-References-Between-Components)
- [Inline Templates](https://vuejs.org/v2/guide/components.html#Inline-Templates)
- [X-Templates](https://vuejs.org/v2/guide/components.html#X-Templates)
- [One-time Compile Components](https://vuejs.org/v2/guide/components.html#Cheap-Static-Components-with-v-once)

### Note

- Self-closing custom element (```<my-component/>```) chỉ dùng được trong string template vì nó không phải là 1 valid HTML element

## Reactivity in Depth

Khi truyền 1 object vào **data** option của Vue instance, Vue sẽ dùng [Object.defineProperty](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Object/defineProperty) để convert các prop của object thành các getter/setter. Các getter/setter này cho Vue khả năng **dependency-tracking (inside getter)** và  **change-notification (inside setter)**.

```js
Object.defineProperty(obj, 'a', {
  get() {
    console.log('I have been touched');
    return a;
  },
  set(value) {
    console.log('I have been set');
    a = value;
  }
});
```

Vue chỉ convert các root prop của **data** option một lần duy nhất khi init instance. Vì vậy phải định nghĩa trước các root prop cần dùng. Còn đối với nested prop thì có thể dùng ```Vue.set()``` hoặc ```vm.$set()``` để add thêm -> ```set()``` wrap ```Object.defineProperty()```

Mỗi một Vue instance có một **watcher** tương ứng. Thay vì trigger re-render trực tiếp khi data thay đổi, Vue sử dụng các **watcher** này để tăng hiệu năng. Khi render, **watcher** sẽ lưu lại các data cần sử dụng (dependency) thông qua các getter. Và khi các dependency thay đổi (setter được gọi), **watcher** sẽ trigger re-render. Như vậy chỉ có các data cần sử dụng mới được track.

![data change](../images/data.png)

## Some best practices I might forget

### [Avoid ```v-if``` with ```v-for```](https://vuejs.org/v2/style-guide/#Avoid-v-if-with-v-for-essential)

Dùng computed props hoặc wrap v-for bằng v-if

### [Private property names](https://vuejs.org/v2/style-guide/#Private-property-names-essential)

Naming convention cho custom private properties khi define plugin, mixin: prefix bằng ```$_```

### [Tightly coupled component names](https://vuejs.org/v2/style-guide/#Tightly-coupled-component-names-strongly-recommended)

Prefix tên component con bằng tên component cha

### [Order of words in component names](https://vuejs.org/v2/style-guide/#Order-of-words-in-component-names-strongly-recommended)

### [Prop name casing](https://vuejs.org/v2/style-guide/#Prop-name-casing-strongly-recommended)

### [Simple computed properties](https://vuejs.org/v2/style-guide/#Simple-computed-properties-strongly-recommended)

### [Component/instance options order](https://vuejs.org/v2/style-guide/#Component-instance-options-order-recommended)

### [Element attribute order](https://vuejs.org/v2/style-guide/#Element-attribute-order-recommended)
