# 静态导入

含义是导入被另一个模块导出的代码

import 关键字允许我们导入由其他模块导出的代码。默认情况下，所有我们静态导入的模块都会被添加到初始捆绑包中。使用默认的 ES2015 导入语法(import module from 'module')导入的模块是静态导入的。

[点击查看视频](https://res.cloudinary.com/ddxwdqwkr/video/upload/f_auto/v1609244287/patterns.dev/saticimport1_cgh9h6.mp4)

让我们看一个例子！一个简单的聊天应用包含一个 Chat 组件，在该组件中，我们静态导入并渲染了三个组件：UserProfile、ChatList 和 ChatInput，用于输入和发送消息！在 ChatInput 模块中，我们静态导入了一个 EmojiPicker 组件，以便在用户切换表情符号时向用户显示表情符号选择器。

```typescript
import React from 'react';

// Statically import Chatlist, ChatInput and UserInfo
import UserInfo from './components/UserInfo';
import ChatList from './components/ChatList';
import ChatInput from './components/ChatInput';

import './styles.css';

console.log('App loading', Date.now());

const App = () => (
  <div className="App">
    <UserInfo />
    <ChatList />
    <ChatInput />
  </div>
);

export default App;
```

模块在引擎到达导入它们的代码行时立即执行。当您打开控制台时，可以看到模块加载的顺序！

由于这些组件是静态导入的，Webpack 将这些模块捆绑到了初始捆绑包中。我们可以在构建应用程序后看到 Webpack 创建的捆绑包：
![](../../assets/static_import_1.png)

我们的聊天应用程序源代码被捆绑成一个捆绑包：main.bundle.js。大的捆绑包大小可能会显着影响用户设备和网络连接的加载时间。在 App 组件能够将其内容呈现到用户屏幕之前，它首先必须加载和解析所有模块。

幸运的是，有很多方法可以加快加载时间！我们不必总是一次性导入所有模块：也许有一些模块只应该根据用户交互进行渲染，比如在这种情况下的 EmojiPicker，或者在页面下方进行渲染。我们可以在 App 组件呈现其内容并且用户能够与我们的应用程序交互之后动态地导入模块，而不是静态导入所有组件。
