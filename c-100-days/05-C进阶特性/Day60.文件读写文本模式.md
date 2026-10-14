# Day 60 · 文件读写文本模式

## 今日目标
- 掌握 fgetc / fputc 逐字符读写
- 掌握 fgets / fputs 逐行读写
- 学会 fprintf / fscanf

## 学习内容

### 逐字符写

```c
FILE *fp = fopen("hello.txt", "w");
fputc('H', fp);
fputc('i', fp);
fputc('\n', fp);
fclose(fp);
```

### 逐字符读

```c
FILE *fp = fopen("hello.txt", "r");
int c;
while ((c = fgetc(fp)) != EOF) {
    putchar(c);
}
fclose(fp);
```

注意 `c` 要用 `int` 不能用 `char`，因为 EOF 是 -1，char 可能是无符号。

### 逐行读写

```c
// 写一行
FILE *fp = fopen("lines.txt", "w");
fputs("第一行\n", fp);
fputs("第二行\n", fp);
fclose(fp);

// 读
char buf[256];
FILE *fp2 = fopen("lines.txt", "r");
while (fgets(buf, sizeof(buf), fp2) != NULL) {
    printf("%s", buf);   // buf 自带 \n
}
fclose(fp2);
```

`fgets` 读到换行或满了就读，第二参数是缓冲区大小，防溢出。

### fprintf / fscanf

像 printf/scanf 一样格式化读写文件：

```c
FILE *fp = fopen("data.txt", "w");
fprintf(fp, "%s %d %.2f\n", "张三", 18, 92.5);
fclose(fp);

FILE *fp2 = fopen("data.txt", "r");
char name[20]; int age; double score;
fscanf(fp2, "%s %d %lf", name, &age, &score);
printf("%s %d %.2f\n", name, age, score);
fclose(fp2);
```

### EOF

读到文件末尾时，fgetc/fgets 返回 EOF 或 NULL。不要用 `feof` 判断，直接看返回值。

## 核心重点
- fgetc/fputc 逐字符，c 用 int 存
- fgets 逐行，第二参数防溢出
- fprintf/fscanf 格式化读写
- EOF 表示文件结束
- 读完记得 fclose

## 练习/任务
1. 写一个程序把 "Hello File" 写入文件
2. 再读回来打印
3. 写 3 个学生信息到文件，再读出来
4. （选做）实现一个文件复制程序

## 今日刷题
- 今天文件操作为主，不安排算法题。

## 打卡清单
- [ ] 会逐字符读写
- [ ] 会逐行读写
- [ ] 会用 fprintf/fscanf
- [ ] 知道 EOF 判断
