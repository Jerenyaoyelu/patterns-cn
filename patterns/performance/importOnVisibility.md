# 可见时导入

将非关键组件在它们可见于视口时加载

除了用户交互之外，我们经常有一些组件在初始页面上并不可见。一个很好的例子是懒加载图片，这些图片在视口内并不直接可见，但只有当用户向下滚动时才会加载。
[点击查看视频](https://res.cloudinary.com/ddxwdqwkr/video/upload/f_auto/v1609244229/patterns.dev/pat5_r1bjcp.mp4)

由于我们不会立即请求所有图片，因此我们可以减少初始加载时间。我们也可以对组件采取同样的策略！为了知道组件当前是否在我们的视口中，我们可以使用[IntersectionObserver API](https://developer.mozilla.org/en-US/docs/Web/API/Intersection_Observer_API)，或使用诸如 react-lazyload 或 react-loadable-visibility 之类的库，以便快速地将“按需导入”功能添加到我们的应用程序中。

> ChatInput.js

```Typescript
import React from "react";
import Send from "./icons/Send";
import Emoji from "./icons/Emoji";
import LoadableVisibility from "react-loadable-visibility/react-loadable";

const EmojiPicker = LoadableVisibility({
  loader: () => import("./EmojiPicker"),
  loading: <p id="loading">Loading</p>
});

const ChatInput = () => {
  const [pickerOpen, togglePicker] = React.useReducer(state => !state, false);

  return (
    <div className="chat-input-container">
      <input type="text" placeholder="Type a message..." />
      <Emoji onClick={togglePicker} />
      {pickerOpen && <EmojiPicker />}
      <Send />
    </div>
  );
};

console.log("ChatInput loading", Date.now());

export default ChatInput;
```

> ChatList.js

```Typescript
import React from "react";
import messages from "../data/messages";

const ChatMessage = ({ message, side }) => (
  <div className={`msg-container ${side}`}>
    <div className="chat-msg">
      <div className="msg-contents">{message}</div>
    </div>
  </div>
);

const ChatList = () => (
  <div className="chat-list">
    {messages.map(message => (
      <ChatMessage
        message={message.body}
        key={message.id}
        side={["left", "right"][Number(message.senderId === 1)]}
      />
    ))}
  </div>
);

export default ChatList;
```

> webpack.config.js

```javascript
const path = require('path');
const HTMLWebpackPlugin = require('html-webpack-plugin');

module.exports = {
  entry: './src/index.js',
  module: {
    rules: [
      {
        test: /.(js|jsx)$/,
        exclude: /node_modules/,
        use: ['babel-loader'],
      },
      {
        test: /.css$/i,
        use: ['style-loader', 'css-loader'],
      },
    ],
  },
  resolve: {
    extensions: ['*', '.js', '.jsx'],
  },
  mode: 'development',
  output: {
    path: path.resolve(__dirname, 'dist'),
    filename: '[name].bundle.js',
  },
  plugins: [
    new HTMLWebpackPlugin({
      template: path.resolve(__dirname, 'dist', 'index.html'),
    }),
  ],
  devServer: {
    contentBase: path.join(__dirname, 'dist'),
    compress: true,
    port: 9000,
  },
};
```

每当用户单击 Gif 按钮并且 EmojiPicker 被呈现到屏幕上时，react-loadable-visibility 检测到 EmojiPicker 元素应该在屏幕上可见。只有这时，它才会开始导入模块，同时用户会看到正在呈现加载组件的过程。

[点击查看视频](https://res.cloudinary.com/ddxwdqwkr/video/upload/f_auto/v1609056516/patterns.dev/import-on-visibility.mp4)

这是一个后备组件，它可以让用户知道我们的应用程序没有冻结：他们只需要等待一小段时间，等待模块被加载、解析、编译和执行！
