---
layout: post
title: "添加Github样式的代办事项列表"
description: ""
date: 2017-09-16 15:22:05 +0800
category: 
tags: []
---
# 本地渲染Github样式的待办事项

github上可以便捷的生成代办事项列表，例如：

输入文本：

```text
- [ ] 完善本文内容
- [x] 简要说明解决办法
```

显示：

- [ ] 完善本文内容
- [x] 简要说明解决办法

但是在本地编辑浏览markdown的文本内容时，上述格式书写的待办事项列表并不能正确渲染。
这是因为github pages使用的渲染器`kramdown`并不支持github pages自定义的这个格式。

## 解决办法

kramdown的开发者似乎并不打算解决这个问题（见参考链接2）。

[Winddweb's Log](http://blog.winddweb.info/implement-github-like-checkbox)给出了办法，修改CSS样式，将括号替换为HTML的图标样式。

具体修改如下：

`_layouts/post.html`

```html
<div class="post-content" itemprop="articleBody">
  { { content | replace: '<li>[ ]',
      '<li class="box task-list-item">
      <input type="checkbox" class="task-list-item-checkbox" disabled>' |
  replace: '<li>[x]',
      '<li class="box_done task-list-item">
      <input type="checkbox" class="task-list-item-checkbox" value="on" disabled checked>'  } }
</div>
```

`css/main.scss`

```patch
diff --git a/css/main.scss b/css/main.scss
index f2e566e..aac7b63 100644
--- a/css/main.scss
+++ b/css/main.scss
@@ -49,5 +49,6 @@ $on-laptop:        800px;
 @import
         "base",
         "layout",
-        "syntax-highlighting"
+        "syntax-highlighting",
+        "custom-styles"
 ;
```

`_sass/_custom-styles.scss`

```css
// styles for checkbox
.box,
.box_done,
.task-list-item {
    list-style-type: none;
}

.task-list-item input {
  margin: 0 0.2em 0.25em -1.6em;
  vertical-align: middle;
}

.task-list-item+.task-list-item {
  margin-top: 3px;
}
```

## 参考

1. [Implement Github Checkboxes - Yifeng Huang](http://blog.winddweb.info/implement-github-like-checkbox)
2. [add checkbox list to GFM - kramdown](<https://github.com/gettalong/kramdown/issues/172>)