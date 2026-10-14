# Day 98 · Docker容器化部署

## 今日目标
- 学会写 Dockerfile 打包 Go 应用
- 理解多阶段构建
- 生成小镜像

## 学习内容

### 为什么 Docker

Go 编译成单个二进制，天然适合 Docker。多阶段构建能做到镜像只有几 MB。

### Dockerfile

```dockerfile
# 阶段1：构建
FROM golang:1.24 AS builder
WORKDIR /app
COPY go.mod go.sum ./
RUN go mod download
COPY . .
RUN CGO_ENABLED=0 go build -o server .

# 阶段2：运行
FROM alpine:latest
WORKDIR /app
COPY --from=builder /app/server .
EXPOSE 8080
CMD ["./server"]
```

### 构建与运行

```powershell
docker build -t todo-api .
docker run -p 8080:8080 todo-api
```

### CGO_ENABLED=0

禁用 CGO 编译纯静态二进制，才能在 alpine 里跑。

### .dockerignore

```
*.db
.git
README.md
```

## 核心重点
- 多阶段构建：builder 编译、alpine 运行
- CGO_ENABLED=0 静态编译
- 镜像小、启动快
- EXPOSE 声明端口
- .dockerignore 排除无关文件

## 练习/任务
1. 给项目写 Dockerfile
2. docker build 出镜像
3. docker run 跑起来
4. 看镜像大小，对比是否够小

## 今日刷题

### 1. LeetCode 146 LRU 缓存（复习）
- 思路：map+链表。
- 代码提示：list.List。
- Go 要点：设计。

### 2. LeetCode 200 岛屿数量（复习）
- 思路：DFS。
- 代码提示：沉没。
- Go 要点：网格。

## 打卡清单
- [ ] 写好 Dockerfile
- [ ] 镜像构建成功
- [ ] 容器跑起来
- [ ] 复习 LRU 与岛屿
