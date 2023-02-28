# 基于路由的代码分割

根据当前路由动态加载组件

## 基于路由的分割

我们通过增加基于路由的代码分割，可以请求仅仅是特定路由所需要的资源。通过`React Suspense`或者`oadable-components`和类似`react-router`的库，我们可以基于当前路由动态的加载组件。

```javascript
import React, { lazy, Suspense } from 'react';
import { render } from 'react-dom';
import { Switch, Route, BrowserRouter as Router } from 'react-router-dom';

const App = lazy(() => import(/* webpackChunkName: "home" */ './App'));
const Overview = lazy(() =>
  import(/* webpackChunkName: "overview" */ './Overview')
);
const Settings = lazy(() =>
  import(/* webpackChunkName: "settings" */ './Settings')
);

render(
  <Router>
    <Suspense fallback={<div>Loading...</div>}>
      <Switch>
        <Route exact path="/">
          <App />
        </Route>
        <Route path="/overview">
          <Overview />
        </Route>
        <Route path="/settings">
          <Settings />
        </Route>
      </Switch>
    </Suspense>
  </Router>,
  document.getElementById('root')
);

module.hot.accept();
```

通过按照路由来懒加载组件，我们只请求包含对当前路由所需的代码的 bundle。由于大部分人习惯于重定向的时候会需要一定的加载时间，因此这是一个完美的时机去懒加载组件。
