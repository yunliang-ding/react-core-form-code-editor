---
order: 1
title: CodeEditor 核心编辑器
toc: menu
---

<Alert>

- 基于 monaco-editor 二次封装
- 使用 @monaco-editor/loader 优化加载
- 更多用法 参考 [monaco](https://microsoft.github.io/monaco-editor/playground.html)

</Alert>

## 依赖 cdn

```html
<!-- 仅 less 模式需要引入 -->
<script src="https://lyr-cli-oss.oss-cn-beijing.aliyuncs.com/cdn/less.min.js"></script>
<!-- 仅 function 模式需要引入 -->
<script src="https://lyr-cli-oss.oss-cn-beijing.aliyuncs.com/cdn/babel-standalone.min.js"></script>
<!-- Format With Prettier -->
<script src="https://lyr-cli-oss.oss-cn-beijing.aliyuncs.com/cdn/prettier-standalone.min.js"></script>
<script src="https://lyr-cli-oss.oss-cn-beijing.aliyuncs.com/cdn/prettier-parser-typescript.min.js"></script>
<!-- 自定义主题色 -->
<script src="https://lyr-cli-oss.oss-cn-beijing.aliyuncs.com/prism.min.js"></script>
```

## 基本使用

```tsx
import React from 'react';
import { CodeEditor } from 'lyr-code-editor';

export default () => {
  const codeRef = React.useRef({});
  React.useEffect(() => {
    // monacoInstance 需要异步获取
    codeRef.current.getMonacoInstance().then((res) => {
      console.log(res);
    });
  }, []);
  return (
    <CodeEditor
      codeRef={codeRef}
      style={{ height: 400 }}
      value={`<!DOCTYPE html>
<html>
  <head>
    <meta charset="utf-8" />
    <meta
      name="viewport"
      content="width=device-width, initial-scale=1, maximum-scale=1, minimum-scale=1, user-scalable=no"
    />
    <meta name="revised" content="0.0.18" />
    <link rel="stylesheet" href="/umi.css" />
    <script>
      window.routerBase = "/";
    </script>
    <script>
      //! umi version: 3.5.35
    </script>
    <script>
      !(function () {
        var e =
            navigator.cookieEnabled && void 0 !== window.localStorage
              ? localStorage.getItem("dumi:prefers-color")
              : "auto",
          o = window.matchMedia("(prefers-color-scheme: dark)").matches,
          t = ["light", "dark", "auto"];
        document.documentElement.setAttribute(
          "data-prefers-color",
          e === t[2] ? (o ? t[1] : t[0]) : t.indexOf(e) > -1 ? e : t[0]
        );
      })();
    </script>
  </head>
  <body>
    <div id="root"></div>

    <script src="/umi.js"></script>
  </body>
</html>
`}
      onChange={(v) => {
        console.log('onChange', v);
      }}
      // 仅存在一个编辑器的时候可用，多个存在会有问题
      // onSave={(v) => {
      //   console.log('onSave', v);
      // }}
      language="html"
    />
  );
};
```

## 改动对比

```jsx
import React from 'react';
import { CodeEditor } from 'lyr-code-editor';

export default () => {
  return (
    <CodeEditor
      mode="diff"
      style={{ width: '100%', height: 500 }}
      originalValue={`import ReactDom from 'react-dom'
import { Button } from 'antd';
`}
      value={`import { Button } from '@arco-design/web-react';
import ReactDom from 'react-dom'
`}
    />
  );
};
```

## 使用 JSON 模式

```tsx
/**
 * background: '#fff'
 */
import React from 'react';
import { CodeEditor } from 'lyr-code-editor';

export default () => {
  let value = [
    {
      label: '选项1',
      value: 1,
    },
    {
      label: '选项2',
      value: 2,
    },
    {
      label: '选项3',
      value: 3,
    },
  ];
  return (
    <>
      <h3>请查看控制台打印、当内容发生改变</h3>
      <CodeEditor
        mode="json"
        value={value}
        onChange={(code) => {
          console.log(code);
        }}
      />
    </>
  );
};
```

## 使用 Function 模式

```tsx
/**
 * background: '#fff'
 */
import React from 'react';
import { Button } from '@arco-design/web-react';
import { CodeEditor } from 'lyr-code-editor';

export default () => {
  const codeRef = React.useRef({});
  const runApi = async () => {
    const fn = codeRef.current.getModuleDefault();
    await fn();
  };
  return (
    <>
      <Button type="primary" onClick={runApi}>
        点击打开控制台查看
      </Button>
      <br />
      <br />
      <CodeEditor
        mode="function"
        codeRef={codeRef}
        style={{ width: '100%', height: 300 }}
        value={`export default async () => {
  console.log('导出默认函数')
}`}
      />
    </>
  );
};
```

## 导出默认对象

```tsx
/**
 * background: '#fff'
 */
import React from 'react';
import { Button } from '@arco-design/web-react';
import { CodeEditor } from 'lyr-code-editor';

export default () => {
  const codeRef = React.useRef({});
  const runApi = async () => {
    const obj = codeRef.current.getModuleDefault();
    console.log(obj);
  };
  return (
    <>
      <Button type="primary" onClick={runApi}>
        点击打开控制台查看
      </Button>
      <br />
      <br />
      <CodeEditor
        codeRef={codeRef}
        mode="function"
        style={{ width: '100%', height: 300 }}
        value={`export default {
  options: {
    style: {}
  }
}`}
      />
    </>
  );
};
```

## 导出多个对象

```tsx
/**
 * background: '#fff'
 */
import React from 'react';
import { Button } from '@arco-design/web-react';
import { CodeEditor } from 'lyr-code-editor';

export default () => {
  const codeRef = React.useRef({});
  const runApi = async () => {
    const obj = codeRef.current.getModule();
    console.log(obj);
  };
  return (
    <>
      <Button type="primary" onClick={runApi}>
        点击打开控制台查看
      </Button>
      <br />
      <br />
      <CodeEditor
        mode="function"
        codeRef={codeRef}
        style={{ width: '100%', height: 300 }}
        value={`export const user1 = {
  name: 'Test1',
  age: 90
};
export const user2 = {
  name: 'Test2',
  age: 90
}`}
      />
    </>
  );
};
```

## 使用第三方依赖包

```tsx
/**
 * background: '#fff'
 */
import React from 'react';
import { Button } from '@arco-design/web-react';
import axios from 'axios';
import { CodeEditor } from 'lyr-code-editor';

export default () => {
  const codeRef = React.useRef({});
  const runApi = async () => {
    const fns = codeRef.current.getModule();
    console.log(fns);
  };
  return (
    <>
      <Button type="primary" onClick={runApi}>
        点击打开控制台查看
      </Button>
      <br />
      <br />
      <CodeEditor
        mode="function"
        style={{ width: '100%', height: 300 }}
        codeRef={codeRef}
        require={{
          request: axios,
        }}
        value={`import request from 'request';

export const getList = () => {
  console.log('is getList', request)
};
export const add = () => {
  console.log('is add')
};`}
      />
    </>
  );
};
```

## Es6 => Es5

```jsx
import React from 'react';
import { Grid, Button } from '@arco-design/web-react';
import { CodeEditor } from 'lyr-code-editor';

export default () => {
  const codeRef1 = React.useRef({});
  const codeRef2 = React.useRef({});
  const runApi = async () => {
    (await codeRef2.current.getMonacoInstance()).setValue(
      codeRef1.current.getEs5Code(),
    );
  };
  React.useEffect(() => {
    runApi();
  }, []);
  return (
    <>
      <Button type="primary" onClick={runApi}>
        运行
      </Button>
      <br />
      <br />
      <Grid.Row>
        <div id="test-demo" style={{ display: 'none' }} />
        <Grid.Col span={12}>
          <CodeEditor
            mode="function"
            codeRef={codeRef1}
            style={{ width: '100%', height: 500 }}
            value={`import ReactDom from 'react-dom'
import { Button } from '@arco-design/web-react';

const App = () => {
  return <Button>hello</Button>
};
ReactDom.render(<App />, document.getElementById('test-demo'))
`}
          />
        </Grid.Col>
        <Grid.Col span={12}>
          <CodeEditor
            readOnly
            codeRef={codeRef2}
            style={{ width: '100%', height: 500 }}
          />
        </Grid.Col>
      </Grid.Row>
    </>
  );
};
```

## less => css

```jsx
import React from 'react';
import { Grid, Button } from '@arco-design/web-react';
import { CodeEditor } from 'lyr-code-editor';

export default () => {
  const codeRef1 = React.useRef({});
  const codeRef2 = React.useRef({});
  const runApi = async () => {
    (await codeRef2.current.getMonacoInstance()).setValue(
      await codeRef1.current.getCssCode(),
    );
  };
  React.useEffect(() => {
    runApi();
  }, []);
  return (
    <>
      <Button type="primary" onClick={runApi}>
        运行
      </Button>
      <br />
      <br />
      <Grid.Row>
        <div id="test-demo" style={{ display: 'none' }} />
        <Grid.Col span={12}>
          <CodeEditor
            mode="less"
            codeRef={codeRef1}
            style={{ width: '100%', height: 500 }}
            value={`.app{
  .header{
    .title{
      font-size: 12px;
    }
  }
  .body{
    font-size: 12px;
  }
}
            `}
          />
        </Grid.Col>
        <Grid.Col span={12}>
          <CodeEditor
            readOnly
            codeRef={codeRef2}
            language="css"
            style={{ width: '100%', height: 500 }}
          />
        </Grid.Col>
      </Grid.Row>
    </>
  );
};
```

## 一分钟搭建 PlayGround

```tsx
/**
 * background: '#fff'
 */
import React from 'react';
import ReactDom from 'react-dom';
import { Button } from '@arco-design/web-react';
import axios from 'axios';
import { CodeEditor } from 'lyr-code-editor';

export default () => {
  const codeRef = React.useRef({});
  const runApi = async () => {
    const fns = codeRef.current.getModuleDefault();
    ReactDom.render(fns(), document.querySelector('#__result__'));
  };
  React.useEffect(() => {
    runApi();
  }, []);
  return (
    <>
      <Button type="primary" onClick={runApi}>
        运行
      </Button>
      <br />
      <br />
      <div style={{ width: '100%', display: 'flex' }}>
        <CodeEditor
          mode="function"
          style={{ width: '50%', height: 300 }}
          codeRef={codeRef}
          value={`export default () => {
  return <div>hello world</div>
};`}
        />
        <div
          id="__result__"
          style={{ width: '50%', height: 300, border: '1px solid #ddd' }}
        />
      </div>
    </>
  );
};
```

## API

<API src="../../src/code-editor/index.tsx" hideTitle></API>
