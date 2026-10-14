# Day 93 · 简易文本编辑器

## 今日目标
- 实现一个行编辑器
- 支持打开文件、显示、保存
- 练习字符串与文件操作

## 学习内容

### 功能

- 打开一个文本文件
- 显示所有行（带行号）
- 修改某一行
- 追加一行
- 保存

### 数据结构：动态字符串数组

```c
typedef struct {
    char **lines;
    int size;
    int cap;
} TextDoc;
```

### 打开文件

```c
void doc_load(TextDoc *doc, const char *path) {
    FILE *fp = fopen(path, "r");
    if (!fp) return;
    char buf[256];
    while (fgets(buf, sizeof(buf), fp)) {
        // 去掉换行
        int len = strlen(buf);
        if (len > 0 && buf[len-1] == '\n') buf[len-1] = '\0';
        // 动态扩容并复制
        if (doc->size == doc->cap) {
            int nc = doc->cap == 0 ? 4 : doc->cap * 2;
            char **tmp = realloc(doc->lines, nc * sizeof(char*));
            doc->lines = tmp;
            doc->cap = nc;
        }
        doc->lines[doc->size] = malloc(strlen(buf)+1);
        strcpy(doc->lines[doc->size], buf);
        doc->size++;
    }
    fclose(fp);
}
```

### 显示

```c
void doc_show(const TextDoc *doc) {
    for (int i = 0; i < doc->size; i++) {
        printf("%4d: %s\n", i+1, doc->lines[i]);
    }
}
```

### 保存

```c
void doc_save(const TextDoc *doc, const char *path) {
    FILE *fp = fopen(path, "w");
    if (!fp) return;
    for (int i = 0; i < doc->size; i++) {
        fprintf(fp, "%s\n", doc->lines[i]);
    }
    fclose(fp);
}
```

## 核心重点
- 动态字符串数组
- fgets 逐行读
- 每行单独 malloc
- 保存时加 \n
- 两层指针两层释放

## 练习/任务
1. 实现打开和显示
2. 实现保存
3. 加"修改第 N 行"功能
4. （选做）加"追加一行"

## 今日刷题
- 今天项目为主，不安排算法题。

## 打卡清单
- [ ] 会动态字符串数组
- [ ] 能打开保存文本文件
- [ ] 显示带行号
- [ ] 正确释放内存
