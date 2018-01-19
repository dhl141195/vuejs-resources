## Table Of Contents

- [Dynamic Route Matching](#dynamic-route-matching)
   - [Reacting to Params Changes](#reacting-to-params-changes)
- [Nested Routes](#nested-routes)
- [Programmatic Navigation](#programmatic-navigation)
  - [router.push(location, onComplete?, onAbort?)](#routerpushlocation-oncomplete-onabort)
  - [router.replace(location, onComplete?, onAbort?)](#routerreplacelocation-oncomplete-onabort)
  - [router.go(n)](#routergon)
- [Named Views](#named-views)
- [Redirect and Alias](#redirect-and-alias)
- [Passing Props to Route Components](#passing-props-to-route-components)
- [HTML5 History Mode](#html5-history-mode)
- [Navigation Guards](#navigation-guards)
  - [The Full Navigation Resolution Flow](#the-full-navigation-resolution-flow)
- [Route Meta Fields](#route-meta-fields)
- [Non-daily topics](#non-daily-topics)
  - [Scroll Behavior](#scroll-behavior)
  - [Lazy Load](#lazy-load)

### Dynamic Route Matching

```js
const User = {
   template: '<div>User {{ $route.params.id }}</div>'
}

const router = new VueRouter({
  routes: [
    // dynamic segments start with a colon
    { path: '/user/:id', component: User }
  ]
})
```

#### Reacting to Params Changes

Dùng ```watch```

```js
const User = {
  template: '...',
  watch: {
    '$route' (to, from) {
      // react to route changes...
    }
  }
}
```

Dùng ```beforeRouteUpdate```

```js
const User = {
  template: '...',
  beforeRouteUpdate (to, from, next) {
    // react to route changes...
    // don't forget to call next()
  }
}
```

### Nested Routes

Khai báo mount point cho nested routes

```js
const User = {
  template: `
    <div class="user">
      <h2>User {{ $route.params.id }}</h2>
      <router-view></router-view>
    </div>
  `
}
```

Khai báo nested routes (children props)

```js
const router = new VueRouter({
  routes: [
    {
      path: '/user/:id',
      component: User,
      children: [
        {
          path: '',
          component: UserHome
        },
        {
          path: 'profile',
          component: UserProfile
        },
        {
          path: 'posts',
          component: UserPosts
        }
      ]
    }
  ]
})
```

### Programmatic Navigation

#### router.push(location, onComplete?, onAbort?)

> ```router.push(...)``` tương đương với ```<router-link :to="...">```

```js
// literal string path
router.push('home')

// object
router.push({ path: 'home' })

// named route
router.push({ name: 'user', params: { userId: 123 }})

// with query, resulting in /register?plan=private
router.push({ path: 'register', query: { plan: 'private' }})
```

Note: ```path``` và ```params``` không dùng chung được với nhau -> phải dùng ```name``` + ```params``` hoặc full path

#### router.replace(location, onComplete?, onAbort?)

> Khác so với push là thay vì push route mới vào history thì replace route hiện tại
>
> ```router.replace(...)``` tương đương với ```<router-link :to="..." replace>```

#### router.go(n)

> Navigate n steps forwards or backwards in the history stack

```js
// go forward by one record, the same as history.forward()
router.go(1)

// go back by one record, the same as history.back()
router.go(-1)

// go forward by 3 records
router.go(3)

// fails silently if there aren't that many records.
router.go(-100)
router.go(100)
```

### Named Views

Dùng để render nhiều component trên cùng 1 route

Khai báo mount point

```html
<!-- default view -->
<router-view></router-view>

<!-- view 1 -->
<router-view name="view-1"></router-view>

<!-- view 2 -->
<router-view name="view-2"></router-view>
```

Dùng ```components``` thay vì ```component```

```js
const router = new VueRouter({
  routes: [
    {
      path: '/',
      components: {
        default: Foo,
        'view-1': Bar,
        'view-2: Baz
      }
    }
  ]
})
```

Ví dụ: <https://router.vuejs.org/en/essentials/named-views.html>

### Redirect and Alias

Too lazy so just drop the link [here](https://router.vuejs.org/en/essentials/redirect-and-alias.html)

### Passing Props to Route Components

Không nên dùng ```$route``` vì sẽ khiến component tight coupling với route

-> Dùng ```props``` property của route (3 mode: boolean mode, object mode, funciton mode)

<https://router.vuejs.org/en/essentials/passing-props.html>

### HTML5 History Mode

[Fix deploy error](https://router.vuejs.org/en/essentials/history-mode.html)

### Navigation Guards

<https://router.vuejs.org/en/advanced/navigation-guards.html>

#### The Full Navigation Resolution Flow

- Navigation triggered.
- Call leave guards in deactivated components.
- Call global **beforeEach** guards.
- Call **beforeRouteUpdate** guards in reused components
- Call **beforeEnter** in route configs.
- Resolve async route components.
- Call **beforeRouteEnter** in activated components.
- Call global **beforeResolve** guards.
- Navigation confirmed.
- Call global **afterEach** hooks.
- DOM updates triggered.
- Call callbacks passed to next in **beforeRouteEnter** guards with instantiated instances.

### Route Meta Fields

Ví dụ: check authen bằng meta field

```js
const router = new VueRouter({
  routes: [
    {
      path: '/foo',
      component: Foo,
      children: [
        {
          path: 'bar',
          component: Bar,
          meta: { requiresAuth: true }
        }
      ]
    }
  ]
})
```

Bởi vì route có thể lồng nhau nên khi check meta field phải check tất cả các field match

```js
router.beforeEach((to, from, next) => {
  if (to.matched.some(record => record.meta.requiresAuth)) {
    if (!auth.loggedIn()) {
      next({
        path: '/login',
        query: { redirect: to.fullPath }
      })
    } else {
      next()
    }
  } else {
    next()
  }
})
```

### Non-daily topics

#### [Scroll Behavior](https://router.vuejs.org/en/advanced/scroll-behavior.html)

#### [Lazy Load](https://router.vuejs.org/en/advanced/lazy-loading.html)