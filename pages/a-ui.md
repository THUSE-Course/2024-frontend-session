# Appendix: 组件库

　

React 中 `<div>`、`<span>`、`<a>` 等标签事实上就是 HTML 原生标签
- 随着浏览器的实现变化而变化，无法保证 UI 跨平台适配
- 仅仅保证功能性，在美观和交互上并不突出，可能导致*设计语言不统一*

为了解决这些问题，现代 Web 开发往往会使用组件库来实现美观、交互自然、跨平台统一、设计语言统一的 UI，其中常用的一个就是 [AntDesign](https://ant.design/index-cn)。

这是一个按钮和一个文本框：

<a class="button" onclick="alert('Whoa')">按钮</a>
<input type="text" placeholder="文本框" />

这也是一个按钮和一个文本框：

<a-flex gap="small">
  <a-button @click="message.info('Whoa!')">按钮</a-button>
  <a-input placeholder="文本框" />
</a-flex>

当然，还有不少用原生 HTML + CSS 不好写的组件：

<a-popover title="弹出框">
  <template #content>
    <a-flex gap="large">
      <a-badge :count="count">
        <a-avatar shape="square" size="large" />
      </a-badge>
      <a-button-group>
        <a-button @click="decline">
          <minus-outlined />
        </a-button>
        <a-button @click="increase">
          <plus-outlined />
        </a-button>
      </a-button-group>
    </a-flex>
    <br />
    <a-segmented v-model:value="value2" :options="data2" />
  </template>
  <a-button style="background-color: green; color: white" loading>Hover me</a-button>
</a-popover>

<script setup>
import { reactive, ref } from 'vue';

import {
  Avatar as AAvatar,
  Badge as ABadge,
  Button as AButton,
  ButtonGroup as AButtonGroup,
  Flex as AFlex,
  Input as AInput,
  Popover as APopover,
  Segmented as ASegmented,
  message
} from 'ant-design-vue';

import { MinusOutlined, PlusOutlined } from '@ant-design/icons-vue';

const count = ref(5);
const decline = () => {
  if (count.value >= 1) {
    count.value--;
  }
};

const increase = () => {
  count.value++;
};

const data = reactive(['Map', 'Transit', 'Satellite']);
const data2 = reactive([
  'Daily',
  { value: 'Weekly', disabled: true },
  'Monthly',
  { value: 'Quarterly', disabled: true },
  // 'Yearly',
]);
const value = ref(data[0]);
const value2 = ref('Daily');

</script>

<style>
.button {
  -webkit-border-radius: 5px;
  -moz-border-radius: 5px;
  border-radius: 5px;
  background-image: -webkit-gradient(linear, left bottom, left top, color-stop(0.16, rgb(207, 207, 207)), color-stop(0.79, rgb(252, 252, 252)));
  background-image: -moz-linear-gradient(center bottom, rgb(207, 207, 207) 16%, rgb(252, 252, 252) 79%);
  background-image: linear-gradient(to top, rgb(207, 207, 207) 16%, rgb(252, 252, 252) 79%); 
  padding: 3px;
  border: 1px solid #000;
  color: black;
  text-decoration: none;
}

input[type=text] {
  background-color: #ffffff;
  border: 1px solid #cccccc;
  -webkit-border-radius: 3px;
     -moz-border-radius: 3px;
          border-radius: 3px;
  -webkit-box-shadow: inset 0 1px 1px rgba(0, 0, 0, 0.075);
     -moz-box-shadow: inset 0 1px 1px rgba(0, 0, 0, 0.075);
          box-shadow: inset 0 1px 1px rgba(0, 0, 0, 0.075);
  -webkit-transition: border linear 0.2s, box-shadow linear 0.2s;
     -moz-transition: border linear 0.2s, box-shadow linear 0.2s;
      -ms-transition: border linear 0.2s, box-shadow linear 0.2s;
       -o-transition: border linear 0.2s, box-shadow linear 0.2s;
          transition: border linear 0.2s, box-shadow linear 0.2s;
}

input[type=text]:focus {
  border-color: rgba(82, 168, 236, 0.8);
  outline: 0;
  outline: thin dotted \9;
  /* IE6-9 */

  -webkit-box-shadow: inset 0 1px 1px rgba(0, 0, 0, 0.075), 0 0 8px rgba(82, 168, 236, 0.6);
     -moz-box-shadow: inset 0 1px 1px rgba(0, 0, 0, 0.075), 0 0 8px rgba(82, 168, 236, 0.6);
          box-shadow: inset 0 1px 1px rgba(0, 0, 0, 0.075), 0 0 8px rgba(82, 168, 236, 0.6);
}
</style>

---

## 组件库的使用

使用组件库是简单的，引入组件库后，使用其中的组件和引用自定义组件完全一致。

首先，我们需要通过包管理器安装组件库：

```bash
yarn add antd
```

之后 `import` 组件库中的组件后，即可在 TSX 中使用：

```tsx {*} twoslash
import React from 'react';
// ---cut---
import { Button } from 'antd';

const DemoScreen = () => {
    return (
        <Button onClick={() => {}}>
            A test button!
        </Button>
    );
};
```

这些组件往往有着更多可自定义的设置，这些就留给你阅读[官方文档](https://ant-design.antgroup.com/components/overview-cn/)并探索了。
