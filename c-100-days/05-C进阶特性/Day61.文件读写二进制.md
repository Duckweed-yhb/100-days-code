# Day 61 · 文件读写二进制

## 今日目标
- 理解文本文件与二进制文件区别
- 掌握 fread / fwrite
- 能读写结构体数组

## 学习内容

### 文本 vs 二进制

- 文本文件：人可读，换行、数字都按字符编码
- 二进制文件：按内存原样写，节省空间，读写快，但人不可读

图片、音频、压缩包都是二进制文件。

### fread / fwrite

```c
size_t fwrite(const void *ptr, size_t size, size_t nmemb, FILE *fp);
size_t fread(void *ptr, size_t size, size_t nmemb, FILE *fp);
```

参数：要写/读的内存地址、每个元素大小、元素个数、文件指针。

### 写结构体数组

```c
typedef struct {
    char name[20];
    int  score;
} Student;

Student class[3] = {
    {"张三", 90}, {"李四", 85}, {"王五", 95}
};

FILE *fp = fopen("students.dat", "wb");
fwrite(class, sizeof(Student), 3, fp);
fclose(fp);
```

`"wb"` = 二进制写模式。

### 读回来

```c
Student loaded[3];
FILE *fp = fopen("students.dat", "rb");
fread(loaded, sizeof(Student), 3, fp);
fclose(fp);

for (int i = 0; i < 3; i++) {
    printf("%s %d\n", loaded[i].name, loaded[i].score);
}
```

### 随机访问：fseek / ftell

```c
fseek(fp, 0, SEEK_END);     // 移到文件末尾
long size = ftell(fp);       // 当前位置 = 文件大小
fseek(fp, 0, SEEK_SET);     // 回到开头
```

- `SEEK_SET`：文件开头
- `SEEK_CUR`：当前位置
- `SEEK_END`：文件末尾

### 为什么用二进制

- 紧凑：int 4 字节，文本模式可能要写 "12345" 5 个字符
- 快：不用转换格式
- 精确：浮点数不会因为文本转换丢精度

缺点：跨平台可能不兼容（字节序、结构体对齐）。

## 核心重点
- 二进制模式 "rb"/"wb"
- fread/fwrite 按块读写
- 直接写结构体数组
- fseek/ftell 随机访问
- 二进制紧凑快，但跨平台要注意

## 练习/任务
1. 把一个 int 数组写入二进制文件
2. 读回来验证
3. 把 Student 结构体数组写入文件
4. （选做）用 fseek 读取文件大小

## 今日刷题
- 今天文件操作为主，不安排算法题。

## 打卡清单
- [ ] 区分文本与二进制
- [ ] 会用 fread/fwrite
- [ ] 能读写结构体数组
- [ ] 会用 fseek/ftell
