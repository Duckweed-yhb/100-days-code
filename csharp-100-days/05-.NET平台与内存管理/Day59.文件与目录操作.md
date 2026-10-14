# Day 59 · 文件与目录操作

## 今日目标
- 掌握 `File` / `Directory` / `Path` 常用 API
- 会读写文本文件
- 理解相对路径与绝对路径

## 学习内容

### Path：处理路径字符串

```csharp
using System.IO;

string full = Path.Combine("folder", "sub", "a.txt");
// Windows: folder\sub\a.txt

Path.GetFileName(full);       // a.txt
Path.GetDirectoryName(full);   // folder\sub
Path.GetExtension(full);      // .txt
Path.ChangeExtension(full, ".md");   // a.md
```

> 拼路径用 `Path.Combine`，别自己拼斜杠，跨平台才稳。

### File：读写小文件

```csharp
// 写文本
File.WriteAllText("note.txt", "你好\n世界");

// 追加
File.AppendAllText("note.txt", "\n新的一行");

// 读文本
string content = File.ReadAllText("note.txt");
Console.WriteLine(content);

// 按行读
string[] lines = File.ReadAllLines("note.txt");
foreach (string line in lines) Console.WriteLine(line);

// 按行写
File.WriteAllLines("todo.txt", new[] { "买菜", "做饭", "洗碗" });
```

### Directory：目录操作

```csharp
Directory.CreateDirectory("data");          // 递归建目录
string[] files = Directory.GetFiles("data");  // 所有文件
string[] dirs = Directory.GetDirectories("data");
Directory.Exists("data");                     // 是否存在
Directory.Delete("data", recursive: true);    // 删除
```

### 判断存在

```csharp
if (File.Exists("note.txt"))
{
    string s = File.ReadAllText("note.txt");
}
```

### 例子：遍历目录统计

```csharp
string dir = @"C:\Windows";
var info = new DirectoryInfo(dir);
foreach (FileInfo f in info.EnumerateFiles("*.log", SearchOption.AllDirectories))
{
    Console.WriteLine($"{f.FullName}  {f.Length} 字节");
}
```

## 核心重点
- Path.Combine 拼路径，别手写斜杠
- File.ReadAllText / WriteAllText 适合小文件
- ReadAllLines / WriteAllLines 按行
- Directory.Exists / File.Exists 先判再操作
- 路径用 `@"..."` 原始字符串避免转义

## 练习/任务
1. 写一个程序：把用户输入的若干行写入 `todo.txt`
2. 读回 `todo.txt` 并打印每行带行号
3. 列出某个目录下所有 .md 文件及大小
4. （选做）写一个简易日志器：按日期写日志文件

## 今日刷题
- **LeetCode 1 两数之和（复习）**：Dictionary O(n)。
- **LeetCode 202 快乐数（复习）**：HashSet 检测循环。

## 打卡清单
- [ ] 会用 Path.Combine 拼路径
- [ ] 会读写文本文件
- [ ] 会遍历目录
- [ ] 已完成至少一道今日刷题
