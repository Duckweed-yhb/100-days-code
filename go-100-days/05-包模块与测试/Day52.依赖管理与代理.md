# Day 52 · 依赖管理与代理

## 今日目标
- 学会配置国内代理加速下载
- 理解语义化版本与升级降级
- 掌握 go get 版本操作

## 学习内容

### 国内代理

国内直连 proxy.golang.org 较慢，配置七牛或阿里云代理：

```powershell
go env -w GOPROXY=https://goproxy.cn,direct
```

`direct` 表示代理失败时直连源站。

### 查看与设置环境变量

```powershell
go env                 # 看所有 Go 环境变量
go env GOPROXY
```

### 语义化版本

依赖版本格式 `vMAJOR.MINOR.PATCH`：

- v1.9.1 → 主版本 1、次版本 9、补丁 1
- 升级补丁：小改动
- 升级次版本：向后兼容新功能
- 升级主版本：可能不兼容

### 升级与降级

```powershell
go get github.com/gin-gonic/gin@v1.9.1     # 指定版本
go get -u ./...                            # 升级所有依赖到最新次版本
go get -u github.com/gin-gonic/gin@latest # 升级单个
```

### 替换依赖 replace（少见）

本地调试时可在 go.mod 加：

```
replace github.com/foo/bar => ../local-bar
```

把依赖指向本地目录。

### 清理模块缓存

```powershell
go clean -modcache
```

## 核心重点
- GOPROXY 国内设 goproxy.cn
- 语义化版本 vMAJOR.MINOR.PATCH
- go get pkg@version 切版本
- go get -u 升级
- replace 用于本地调试

## 练习/任务
1. 配置 GOPROXY 为 goproxy.cn
2. 拉取一个第三方包，再升级到 latest
3. 用 go list -m all 看依赖树
4. （选做）用 replace 把一个依赖指向本地目录

## 今日刷题

### 1. LeetCode 70 爬楼梯
- 思路：DP，f(n)=f(n-1)+f(n-2)。
- 代码提示：两个变量滚动。
- Go 要点：斐波那契。

### 2. LeetCode 118 杨辉三角
- 思路：每行首尾 1，中间 = 上一行相邻之和。
- 代码提示：二维切片构造。
- Go 要点：基础 DP。

## 打卡清单
- [ ] 配置好 GOPROXY
- [ ] 会用 go get 切版本
- [ ] 理解语义化版本
- [ ] 写完爬楼梯与杨辉三角
