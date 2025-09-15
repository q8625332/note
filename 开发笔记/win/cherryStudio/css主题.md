**鎏金主题**

```
/*
 * 主题名称: 少女终末旅行 - 百万优化版
 * 描述: 一款融合了“少女终末旅行”壁纸、霞鹜文楷字体、炫彩输入框以及透明化UI的美化主题
 * 原作者: https://linux.do/t/topic/755792
 */

/* 导入“霞鹜文楷”字体 */
@import url("https://cdn.bootcdn.net/ajax/libs/lxgw-wenkai-screen-webfont/1.7.0/style.min.css");

/*
 * 动画关键帧
 */

/* 输入框流光边框动画 */
@keyframes elegantFlow {
    0% { background-position: 0 50%; }
    50% { background-position: 100% 50%; }
    100% { background-position: 0 50%; }
}

/* 输入框聚焦发光动画 */
@keyframes softGlow {
    0%, 100% { box-shadow: 0 0 10px rgba(255, 106, 1, 0.1); }
    50% { box-shadow: 0 0 15px rgba(138, 43, 226, 0.15); }
}

/* 通用点击反馈动画 */
@keyframes clickAnimation {
  0%, 30%, 60%, 100% { transform: scale(1); }
}


/*------------------------------------*\
  根元素变量与全局样式
\*------------------------------------*/

:root {
  /* 全局字体定义 */
  --font-default: "LXGW WenKai Screen", -apple-system, BlinkMacSystemFont, "Segoe UI", Roboto, Oxygen, Ubuntu, Cantarell, "Open Sans", "Helvetica Neue", sans-serif;
  
  --color-link: #a2fdb5;
  --fill-1: rgba(120 120 122 / 0.1);
  --fill-2: rgba(120 120 122 / 0.3);
  --fill-3: rgba(120 120 122 / 0.25);
  --color-black: var(--fill-1);
  --color-black-soft: var(--fill-2);
  --color-black-mute: var(--fill-3);
  --chat-background: transparent;
  --color-list-item: #5b4057;
  --color-list-item-hover: #4d4c4d;
  --button-hover-light: #79BF55;
  --button-hover-dark: #009A39;
  --input-bg-light: #E6E6E6;
  --input-bg-dark: #202020;
  --button-border-radius: 12px;
  --button-border-radius-hover: 8px;
  --button-border-radius-active: 12px;
  --background-light-new: #F3F3F3;
  --background-assistant-light-new: #E3E3E3;
  --animation: cubic-bezier(.56, -0.37, .46, 1.58);
  --short-timer: 0.2s;
  --long-timer: 0.5s;
}

/* 应用全局字体，并排除图标、公式等特殊元素 */
*:not(.katex):not(.katex *):not(.iconfont):not(.markdown code):not(.markdown pre):not(.MathJax) {
  font-family: var(--font-default) !important;
  line-height: 1.6;
  letter-spacing: 0.02em;
}

/* 继承字体粗细，保持全局一致性 */
*, *::before, *::after {
	font-weight: inherit;
}

/* “少女终末旅行”主题背景 (仅黑暗模式) */
body[theme-mode="dark"] {
  position: relative;
  z-index: 1; 
}
body[theme-mode="dark"]::before {
  content: ''; 
  background-image: url('https://i0.hdslb.com/bfs/article/e74076167cbc86a52113f388313cc24a74664ced.jpg');
  background-size: cover;
  background-position: center center;
  background-repeat: no-repeat;
  background-attachment: fixed;
  filter: blur(0px);
  position: fixed;
  top: 0;
  left: 0;
  right: 0;
  bottom: 0;
  z-index: -1;
}

/* 核心UI元素透明化 */
#root,
body[theme-mode=dark] #chat,
#content-container,
[class^=SettingGroup],
[class^=SettingContainer],
[class^=MainContainer],
[class^=MainContent],
.home-tabs, 
[class^=ProgramSection], 
[class^=IconSection], 
#messages,
[theme-mode=dark] #content-container,
[theme-mode=dark] .minapp-drawer .ant-drawer-body {
  background: transparent !important;
  background-color: transparent !important;
  backdrop-filter: none !important;
}


/*------------------------------------*\
  对话气泡样式
\*------------------------------------*/

.message-assistant .block-wrapper .markdown p {
  color: #efc3ea;
}
.bubble:not(.multi-select-mode) .message-assistant .message-content-container {
  border-radius: 15px;
  padding: 30px;
  background: rgb(76 50 68 / 30%);
  backdrop-filter: blur(15px);
}

.message-user {
	font-weight: 800 !important;
}
.message-user .block-wrapper .markdown p {
  color: #b5f8b9;
}
.bubble:not(.multi-select-mode) .message-user .message-content-container {
  border-radius: 15px;
  padding: 30px;
	backdrop-filter: blur(15px);
  background: rgba(41, 29, 34, 0.5);
}

/*  对话框的底色-- start */
.message-content-container {
  will-change: transform;
  contain: layout paint style;
  background:color-mix(
    in srgb,
    var(--color-background-soft),
    transparent 10%
  ) !important;
  -webkit-backdrop-filter: blur(16px);
  backdrop-filter: blur(16px);
  -webkit-transform: translateZ(0);
  transform: translateZ(0);
  border: none;
  border-radius: var(--radius-dynamic);
  position: relative;
  z-index: 1;
  outline: none;
  transition: background-color var(--duration-normal) var(--easing-standard),
    box-shadow var(--duration-normal) var(--easing-standard);
  margin-block: 8px;
  padding-inline: 12px;
  padding-block: 10px;
  box-shadow: 0 2px 10px rgba(0, 0, 0, 0.1);
  padding:14px !important;
}
.message-content-container::before {
  content: "";
  position: absolute;
  top: 0;
  left: 0;
  right: 0;
  bottom: 0;
  border-radius: var(--radius-dynamic);
  background: var(--metal-gradient);
  opacity: 0.4;
  pointer-events: none;
  z-index: -1;
  border: 0.3px solid var(--border-color);
  transition: opacity var(--duration-normal) var(--easing-standard);
}
.message-content-container:hover::before {
  opacity: 0.7;
  border-color: var(--border-hover-color);
}

/*  对话框的底色-- end */


/*------------------------------------*\
  输入框样式与动效
\*------------------------------------*/

#inputbar {
    position: relative;
    background: rgba(41, 29, 34, 0.5);
    margin-left: 20px;
    margin-right: 20px;
    border: 1px solid #615325;
    backdrop-filter: blur(10px);
    border-radius: 12px;
}

/* 固定输入框尺寸 */
#inputbar textarea {
    height: 120px !important;
    padding: 15px !important;
}

/* 炫彩流光边框 */
#inputbar::before {
    content: "";
    position: absolute;
    inset: -2px;
    border-radius: inherit;
    padding: 2px;
    background: linear-gradient(
        90deg,
        #ff6a01, #f8c91c, #8a2be2, #00bfff, #ff6a01
    );
    background-size: 300% 300%;
    mask:
        linear-gradient(#000 0 0) content-box,
        linear-gradient(#000 0 0);
    -webkit-mask-composite: destination-out;
    mask-composite: exclude;
    animation: elegantFlow 6s ease-in-out infinite;
    opacity: 0;
    transition: opacity 0.6s ease;
    pointer-events: none;
    z-index: -1;
}

#inputbar:focus-within::before {
    opacity: 1;
}

#inputbar:focus-within {
    animation: softGlow 3s ease-in-out infinite;
}


/*------------------------------------*\
  Markdown 内容样式
\*------------------------------------*/

/* 多级标题样式 */
.markdown h1 { font-size: 2em; color: #ffadad; border: none; padding-bottom: 0; }
.markdown h2 { font-size: 1.75em; color: #ffd6a5; border: none; padding-bottom: 0; }
.markdown h3 { font-size: 1.5em; color: #fdffb6; border: none; padding-bottom: 0; }
.markdown h4 { font-size: 1.25em; color: #caffbf; border: none; padding-bottom: 0; }
.markdown h5 { font-size: 1.1em; color: #9bf6ff; border: none; padding-bottom: 0; }
.markdown h6 { font-size: 1em; color: #a0c4ff; border: none; padding-bottom: 0; }

/* 加粗、代码块等元素样式 */
.markdown strong {
  color: #ffffaa;
}

body[theme-mode="dark"] pre div[class^="CodeContent"] {
  background-color: rgba(30, 30, 32, 0.7) !important;
}
.shiki.material-theme-darker {
  background-color: transparent !important;
  border-radius: 6px;
  position: relative;
  overflow: hidden;
  border: 0.3px solid rgba(255, 255, 255, 0.05);
}


/*------------------------------------*\
  其它界面元素与交互动画
\*------------------------------------*/

/* 部分设置弹窗的毛玻璃效果 */
.eTRMVy, .iaOCiL, .faeYdD, .deaugY, .jGVA, .lfzpcl, .etgWPh  {
    backdrop-filter: blur(15px);
}
/* 快捷助手面板的毛玻璃效果 */
.iAPqoN {
  backdrop-filter: blur(50px);
}

/* 助手设置窗口最小宽度 */
/* .ant-modal-root .ant-modal-centered .ant-modal {
  width: min(900px, 70vw) !important;
} */

/* 用户交互反馈动画 */
[class^="MessageLineContainer"] {
  border-radius: var(--button-border-radius);
}
[class^="MessageLineContainer"] [class^="MessageItem"]:active {
  animation: clickAnimation var(--long-timer) var(--animation);
}

[class^="Icon"]:hover,
[class^="ant"]:hover,
[class^="ActionButton"]:hover,
[class^="TopicListItem"]:hover,
[class^="ProviderListItem"]:hover,
[class^="MenuItem"]:hover,
[class^="MenuButton"]:hover,
[class^="EmojiBackground"]:hover {
  border-radius: var(--button-border-radius-hover) !important;
  transition: border-radius var(--short-timer) var(--animation);
}

[class^="IconItem"]:active,
[class^="ant-avatar"]:active,
[class^="ant-btn"]:active,
[class^="ant-segmented-item"]:active,
[class^="anticon"]:active,
[class^="ant-upload"]:active,
[class^="ant-divider"]:active,
[class^="ant-tooltip"]:active,
[class^="ant-message"]:active,
[class^="ActionButton"]:active,
[class^="TopicListItem"]:active,
[class^="ProviderListItem"]:active,
[class^="MenuItem"]:active,
[class^="MenuButton"]:active,
[class^="EmojiBackground"]:active,
[class~="ant-dropdown-menu-item"]:active,
[class~="ant-dropdown-menu-submenu-title"]:active,
[class~="ant-select-selector"]:active,
[class~="ant-select-item"]:active {
  border-radius: var(--button-border-radius-active) !important;
  transition: border-radius var(--short-timer) var(--animation);
  animation: clickAnimation var(--long-timer) var(--animation);
}

/* 其它兼容性样式 */
.kaRosT {
	margin-bottom: 10px;
}
.css-var-«r0».ant-tag {
	--ant-tag-default-bg: #473544;
    padding: 5px;
}
```
