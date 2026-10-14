# Day 60 · 文件流与异步IO

## 今日目标
- 理解流（Stream）的概念
- 会用 `FileStream`、`StreamReader/Writer` 处理大文件
- 会用异步 IO 读写文件

## 学习内容

### 小文件 vs 大文件

`File.ReadAllText` 一把全读进内存，小文件没事。读 1GB 日志就炸了。大文件要用流，边读边处理。

### StreamReader / StreamWriter

```csharp
using (var reader = new StreamReader("big.txt"))
{
    string? line;
    while ((line = reader.ReadLine()) != null)
    {
        // 一行行处理，不一次性全进内存
        if (line.Contains("error")) Console.WriteLine(line);
    }
}

using (var writer = new StreamWriter("out.txt"))
{
    await writer.WriteLineAsync("第一行");
    await writer.WriteLineAsync("第二行");
}
```

### 异步 IO

```csharp
async Task<string> ReadAllAsync(string path)
{
    using var reader = new StreamReader(path);
    return await reader.ReadToEndAsync();
}
```

异步 IO 在等磁盘时不占线程，Web 服务里尤其重要。

### 复制文件

```csharp
await using var source = new FileStream("a.txt", FileMode.Open);
await using var dest = new FileStream("b.txt", FileMode.Create);
await source.CopyToAsync(dest);
```

### 二进制文件

```csharp
await using var fs = new FileStream("data.bin", FileMode.Create);
byte[] bytes = { 1, 2, 3, 4, 5 };
await fs.WriteAsync(bytes);
```

图片、压缩包、Excel 都是二进制文件，用 byte[] 读写。

## 核心重点
- 大文件用流，别 ReadAllText
- StreamReader 一行行读，省内存
- using 保证流关闭
- 异步 IO 用 ReadLineAsync / WriteAsync
- 二进制用 FileStream + byte[]

## 练习/任务
1. 用 StreamReader 逐行读一个日志文件，统计含 "error" 的行数
2. 用异步方法复制一个大文件
3. 写一个程序：把一个文本文件内容转成大写后写到新文件
4. （选做）用 FileStream 写一个简单的二进制格式：先写长度，再写字符串内容

## 今日刷题
- **LeetCode 205 同构字符串**：两个字符串字符能否一一映射。思路：两个 Dictionary 互查。
- **LeetCode 290 单词规律**：模式与单词串是否同构。思路：同上，注意空格切分。

## 打卡清单
- [ ] 会用 StreamReader 逐行读
- [ ] 会用 using 释放流
- [ ] 会用异步读写
- [ ] 已完成至少一道今日刷题
