# Day 81 · 模板渲染

## 今日目标
- 学会用 html/template 渲染 HTML
- 理解模板继承与变量
- 知道何时用模板、何时纯 JSON API

## 学习内容

### 标准库 html/template

Go 用 `html/template` 渲染服务端 HTML，自动转义防 XSS。

### 渲染一个页面

```go
t := template.Must(template.ParseFiles("index.html"))
r.GET("/page", func(c *gin.Context) {
    t.Execute(c.Writer, gin.H{"Title": "首页"})
})
```

### index.html

```html
<!DOCTYPE html>
<html>
<head><title>{{.Title}}</title></head>
<body>
  <h1>{{.Title}}</h1>
  <ul>
    {{range .Items}}
      <li>{{.}}</li>
    {{end}}
  </ul>
</body>
</html>
```

### gin 渲染

```go
r.LoadHTMLGlob("templates/*")
r.GET("/", func(c *gin.Context) {
    c.HTML(200, "index.html", gin.H{"Title": "首页"})
})
```

### 模板语法

- `{{.Field}}` 取变量
- `{{range .Items}}` 遍历
- `{{if .Cond}}` 条件

### API 还是模板

现代前后端分离：后端只返回 JSON，前端用 React/Vue 渲染。模板渲染适合简单页面或后台。

## 核心重点
- html/template 服务端渲染
- {{.Field}} 取数据，{{range}} 遍历
- gin LoadHTMLGlob + c.HTML
- 自动 HTML 转义防 XSS
- 前后端分离时后端只发 JSON

## 练习/任务
1. 写一个显示用户列表的 HTML 页面
2. 用 range 遍历切片渲染表格
3. 加 if 条件控制显示
4. 对比纯 JSON API 与模板渲染

## 今日刷题

### 1. LeetCode 20 有效的括号（复习）
- 思路：栈。
- 代码提示：map。
- Go 要点：栈。

### 2. LeetCode 21 合并两个有序链表（复习）
- 思路：哑节点。
- 代码提示：谁小接谁。
- Go 要点：链表。

## 打卡清单
- [ ] 会用 html/template
- [ ] 会用 range/if
- [ ] 理解前后端分离
- [ ] 复习括号与链表
