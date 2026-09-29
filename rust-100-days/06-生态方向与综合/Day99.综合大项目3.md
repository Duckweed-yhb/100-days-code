# Day99 综合大项目 3

> 目标：给核心项目补上测试、错误日志、README、CI 流水线，完成打包发布，交出一份能给别人看的成品。

## 能跑不等于能用

Day98 你的功能跑通了，但还差「工程化」这一层。今天把它打磨成一个别人 clone 下来就能跑、敢发到 GitHub 的项目。

## 第一步：补测试

至少覆盖核心路径：

```rust
#[cfg(test)]
mod tests {
    use super::*;

    #[tokio::test]
    async fn save_and_lookup() {
        let store = MemoryStore::new();
        let code = store.save("https://example.com").await.unwrap();
        let got = store.lookup(&code).await.unwrap();
        assert_eq!(got, "https://example.com");
    }

    #[tokio::test]
    async fn lookup_missing_returns_none() {
        let store = MemoryStore::new();
        assert!(store.lookup("不存在").await.is_none());
    }
}
```

跑测试：

```powershell
cargo test
```

再加两个质量命令，养成习惯：

```powershell
cargo clippy      # 静态检查，揪出坏味道
cargo fmt --check # 检查格式
```

## 第二步：错误处理与日志

别让程序 panic 就退出。用 `tracing` 打结构化日志：

```rust
use tracing::{info, error};

// main 里初始化
tracing_subscriber::fmt::init();

// handler 里
match store.lookup(&code).await {
    Ok(Some(url)) => (StatusCode::FOUND, [(header::LOCATION, url)]),
    Ok(None) => (StatusCode::NOT_FOUND, "短码不存在").into_response(),
    Err(e) => {
        error!(?e, "查库失败");
        (StatusCode::INTERNAL_SERVER_ERROR, "内部错误").into_response()
    }
}
```

## 第三步：写 README

别人 clone 下来第一件事就是看 README。写清四样：

1. **这是什么**：一句话
2. **怎么跑**：编译命令、需要什么依赖
3. **怎么用**：一个 curl 例子
4. **截图/示例输出**（可选）

## 第四步：加 CI（GitHub Actions）

在仓库里放 `.github/workflows/ci.yml`，每次 push 自动跑测试：

```yaml
name: CI
on: [push, pull_request]

jobs:
  test:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v4
      - name: 安装 Rust
        uses: dtolnay/rust-toolchain@stable
      - name: 编译
        run: cargo build --verbose
      - name: 跑测试
        run: cargo test --verbose
      - name: clippy
        run: cargo clippy -- -D warnings
```

这样你的 PR 会自动检查能不能编译、测试过没过。

## 第五步：打包发布

```powershell
# release 模式编译，产物在 target/release/
cargo build --release
```

如果是库，发布到 crates.io：

```powershell
cargo login <你的token>
cargo publish
```

## 验收清单

- [ ] `cargo test` 全绿
- [ ] `cargo clippy` 无警告
- [ ] `cargo fmt` 格式正确
- [ ] README 写清怎么跑
- [ ] GitHub Actions CI 绿了
- [ ] release 包能在干净机器上跑

## 今日小结

- 补单元测试核心路径，养成 `cargo test/clippy/fmt` 三连
- 用 tracing 打日志，错误别裸 panic
- README 写清是什么、怎么跑、怎么用
- GitHub Actions 自动跑测试
- 最后按验收清单过一遍再发布

## 动手任务

1. 给你的 store 和核心路由补至少 3 个测试
2. 加一个 GitHub Actions CI，确认 push 后变绿
3. 写好 README，把项目 push 到 GitHub，给自己点个赞

**配合阅读**：[GitHub Actions 文档](https://docs.github.com/zh/actions)
