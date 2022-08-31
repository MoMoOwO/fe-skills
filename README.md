# 前端技巧总结

## CSS

1. 文本不换行，宽度不够显示省略号。

    + 代码

      ``` CSS
      .box {
        overflow: hidden;
        text-overflow: ellipsis;
        white-space: nowrap;
        /* direction: rtl; */ /* 改变文本方向从右到左，显示前置省略号 */
      }
      ```

    + 解析：`white-space: nowrap;` 强制文本内容不换行，`overflow: hidden;` 用于将文本超出部分隐藏，`text-overflow: ellipsis;` 将文本超出容器宽度部分显示为“...”。

2. 滚动条样式。

    + 代码

      ``` CSS
      ::-webkit-scrollbar {
        width: 10px; /* 垂直滚动条宽度 */
        height: 10px; /* 水平滚动条高度 */
      }
      ::-webkit-scrollbar-button {
        width: 0;
        height: 0;
      }
      ::-webkit-scrollbar-button {
        min-height: 28px;
        background-color: rgba(0, 0, 0, 0.2);
        -webkit-background-clip: padding-box;
        background-clip: padding-box;
        border-radius: 5px;
        -webkit-box-shadow: inset 1px 1px 0 rgb(0 0 0 / 10%), inset 0 -1px 0 rgb(0 0 0 /7%);
      }
      ```

    + 解析：

      | 选择器                            | 说明                                                         |
      | :-------------------------------- | :----------------------------------------------------------- |
      | `::-webkit-scrollbar`             | 整个滚动条                                                   |
      | `::-webkit-scrollbar-button`      | 滚动条上的按钮（上下箭头）                                   |
      | `::-webkit-scrollbar-thumb`       | 滚动条上的滚动滑块                                           |
      | `::-webkit-scrollbar-track`       | 滚动条轨道                                                   |
      | `::-webkit-scrollbar-track-piece` | 滚动条没有滑块的轨道部分                                     |
      | `::-webkit-scrollbar-corner`      | 当同时有垂直滚动条和水平滚动条时交汇的部分                   |
      | `::-webkit-resizer`               | 某些元素的 corner 部分的部分样式（例:textarea 的可拖动按钮） |

3. `outline` 轮廓与 `border` 边框。

    + 比较：`outline` 是元素外围的轮廓（浏览器自带），当元素设置了 `tabindex` 且处于当前 tabindex 时（或该元素处于 `foucus` 状态），则元素外侧会以虚线轮廓强调该元素，提高使用表单的用户体验。`border` 则是元素的边框样式（用户自定义），用于提高元素的美观度。`outline` 虽然不会影响元素的尺寸或位置，但是其对圆角的支持度不好，所以可以通过隐藏轮廓线 `outline: 0/none;` 和自定义边框样式 `border: 1px solid red; border-radius: 2px;` 的方式来美化元素的呈现与交互。
    + 案例：巧用 `outline` 与 `:before`、`:after` 来实现凹凸形状。

      ``` CSS
      .ao, .tu {
        display: inline-block;
        width: 0px;
      }
      .tu {
        margin-left: 100px;
      }
      .ao:before {
        content: 'love你love'; /* 利用连续英文单词不换行特性 */
        outline: 2px solid #cd0000;
        color: transparent;
      }
      .tu:after {
        content: '你love你';
        outline: 2px solid #cd0000;
        color: transparent;
      }
      ```

4. IE8 下 `background` 或 `background-color` 不生效问题。

    + 问题原因：是由于 IE 在渲染网页前会应用一些滤镜（`filter`），所以可以通过同时设置这两个属性来避免背景色不生效的情况。

    + 解决方法：

      ``` CSS
      .box {
        width: 100px;
        height: 100px;
        background: red;
        filter: none !important;
      }
      ```

5. IE8 下提示 `console` 未定义错误。

    + 代码调试过程中会常用到 `console` 来进行一些信息的输出，但是需要注意的是：在 IE8 浏览器未打开开发人员工具（F12）的情况下使用 `console` 对象来查看对象信息时，会报“`console` 未定义”的错误。
    + 解决方法：删除或注释掉 `console` 的代码，工程化的项目可以借助插件 `babel-plugin-transform-remove-console`）；运行项目时打开开发人员调式工具；或者通过一下方法在 js 代码中为 `window` 对象手动绑定一个 `console` 方法。

      ``` JavaScript
      window.console = window.console || (function(){
        var c = {};
        c.log = c.warn = c.debug = c.info = c.error = c.time = c.dir = c.profile
              = c.clear = c.exception = c.trace = c.assert = function(){};
        return c;
      })();
      ```

6. Chrome、Firefox 浏览器各版本下载：

    + [Chrome 浏览器](https://www.chromedownloads.net/)
    + [Firefox 浏览器](https://ftp.mozilla.org/pub/firefox/releases/)

7. `pointer-events` 属性：

    + 属性值：`auto | none`。
    + 当属性值为 `auto` 时，DOM 元素正常响应鼠标事件；当属性值为 `none` 时，DOM 元素不响应鼠标事件，此时元素“虚化”，鼠标可以穿透该元素与该元素下方的元素进行交互。
    + 应用场景：如需要对页面弹窗后的元素进行交互，可以给弹窗或弹窗的遮罩层添加 `pointer-events: none` 属性。

## JavaScript

1. 巧用 `!!` 运算

    + 解析：可以理解为两次取反操作，第一次将变量值做逻辑取反操作，经过两次取反则可以理解为将变量转换为布尔类型，常常用来做类型判断，判断变量 `a` 是否非空。

    + 应用：

      ``` JavaScript
      let a;
      if (a != null && a != undefined && a != '') {
        // 变量 a 有真实变量值的时候执行的操作
      }

      // !! 运算符简化判断
      if (!!a) {
        // 变量 a 有真实变量值的时候执行的操作
      }
      ```

2. 前后端通信中的特殊字符转义

    + HTTP 为了避免歧义，一些特殊字符在传入后台时需要进行转义，如 `" -> &22`、`- -> %23`、`% -> %25`、`& -> %26`、`+ -> %2B`，此时通常会用到 `escape()`、`encodeURI()`、`encoudeURIComponent()`，它们对应的解码方法分别是 `unescape()`、`decodeURI()`、`decodeURIComponent()`，使用哪个方法则需要了解它们各自能够编码哪些特殊字符。

    + 总结：

    | 方法                   | 不能编码的字符                                                                                                          | 统计 |
    | :--------------------- | :---------------------------------------------------------------------------------------------------------------------- | :--- |
    | `escape()`             | `*`、`+`、`-`、`.`、`/`、`@`、`_`、`0-9`、`a-z`、`A-Z`                                                                  | 69   |
    | `encodeURI()`          | `!`、`#`、`$`、`&`、`'`、`(`、`)`、`*`、`+`、`,`、`-`、`.`、`/`、`:`、`;`、`=`、`?`、`@`、`_`、`~`、`0-9`、`a-z`、`A-Z` | 82   |
    | `encodeURIComponent()` | `!`、`'`、`(`、`)`、`*`、`-`、`.`、`_`、`~`、`0-9`、`a-z`、`A-Z`                                                        | 72   |

3. 监听 `input`、`textarea` 元素输入值变化的方案

    + Document 对象的 `input` 输入框、`textarea` 输入框上面绑定有 `input` 和 `propertychange` 两个事件，监听输入值改变得最佳方案是同时监听 `oninput` `onpropertychange` 这两个事件。
    + 为何不使用 `onkeydown`、`onkeypress`、`onkeyup` 这几个事件呢？因为这个键盘事件监听不了右键菜单中的复制、剪贴和粘贴。
    + `oninput` 是 HTML5 定义的标准事件，可以在内容修改后立即被触发，不想 `onchange` 事件需要失去焦点才触发。`oninput` 事件在 IE9 以下的版本是不支持的，此时需要使用 IE 特有的 `onpropertychange` 事件代替，这个事件在用户界面改变或者使用脚本直接修改内容两种情况下都会触发。

4. `preventDefault()`、`stopPropagation()` 与 `stopImmediatePropagation()`：如果事件是可取消的则可通过 `event.preventDefault()` 方法来取消事件，这意味着属于该事件的默认操作将不会发生，该方法并不会阻止事件通过 DOM 进一步传播。倘若需要阻止事件冒泡，则需要通过 `event.stopPropagation()` 方法来实现。`event.stopImmediatePropagation()` 方法用于阻止监听统一事件的其他事件监听器被调用。

5. 常用正则表达式

    | reg                                                                                           | 说明         |
    | :-------------------------------------------------------------------------------------------- | :----------- |
    | `/^\w+[-+.]\w+)*@\w+([-.]\w+)*\.\w+([-.]\w+)*$/`                                              | 验证邮箱     |
    | `/^http://([\w-]+\.)+[\w-]+(/[\w-./?%&=]*)?$ ；^[a-zA-z]+://(w+(-w+)*)(.(w+(-w+)*))*(?S*)?$/` | 验证网站 URL |
    | `/^1[3578]\\d{9}$/`                                                                           | 验证手机号   |

6. 全屏、退出全屏的接口

    + 提醒：不同浏览器存在差异，需要分开处理。
    + 实例：

      ``` JavaScript
      /**
      * 切换全屏状态
      * @param {Object} el 需要全的 DOM 元素对象
      * @param {Boolean} isFull 当是否全屏
      * @returns 改变后的全屏状态标志Boolean
      */
      toggleFullScreen(el, isFull) {
        if (isFull) { // 全屏状态 -> 退出全屏
          if (el.exitFullScreen) {  // HTML W3C 提议
            document.exitFullScreen();
          } else if (el.msExitFullscreen) { // IE11
            document.msExitFullscreen();
          } else if (el.webkitRequestFullScreen) {  // Webkit (works in Safari5.1 and Chrome 15)
            document.webkitCancelFullScreen();
          } else if (el.mozRequestFullScreen) {  // Firefox (works in nightly)
            document.mozCancelFullScreen();
          }
        } else { // 非全屏状态 -> 全屏状态
          if (el.requestFullScreen) {
            el.requestFullScreen();
          } else if (el.msRequestFullscreen) {
            el.msRequestFullscreen();
          } else if (el.webkitRequestFullScreen) {
            el.webkitRequestFullScreen();
          } else if (el.mozRequestFullScreen) {
            el.mozRequestFullScreen();
          }
        }
        return !isFull;
      },
      ```

7.
