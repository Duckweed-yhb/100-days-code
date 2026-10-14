# Day 91 · 学生管理系统一

## 今日目标
- 实现学生管理系统数据层
- 完成添加、列出、查找
- 用动态数组存学生

## 学习内容

### 数据结构

```c
typedef struct {
    char name[20];
    int  score;
} Student;

typedef struct {
    Student *data;
    int size;
    int cap;
} StudentDB;
```

### 初始化与添加

```c
void db_init(StudentDB *db) {
    db->data = NULL;
    db->size = db->cap = 0;
}

void db_add(StudentDB *db, const char *name, int score) {
    if (db->size == db->cap) {
        int new_cap = db->cap == 0 ? 4 : db->cap * 2;
        Student *tmp = realloc(db->data, new_cap * sizeof(Student));
        if (!tmp) return;
        db->data = tmp;
        db->cap = new_cap;
    }
    Student *s = &db->data[db->size++];
    strncpy(s->name, name, 19);
    s->name[19] = '\0';
    s->score = score;
}
```

### 列出所有

```c
void db_list(const StudentDB *db) {
    for (int i = 0; i < db->size; i++) {
        printf("%d. %s %d\n", i+1, db->data[i].name, db->data[i].score);
    }
}
```

### 按姓名查找

```c
int db_find(const StudentDB *db, const char *name) {
    for (int i = 0; i < db->size; i++) {
        if (strcmp(db->data[i].name, name) == 0) return i;
    }
    return -1;
}
```

### 销毁

```c
void db_free(StudentDB *db) {
    free(db->data);
    db->data = NULL;
    db->size = db->cap = 0;
}
```

## 核心重点
- 动态数组存学生
- 添加时自动扩容
- 查找用 strcmp
- 销毁要 free data
- 数据层与界面分离

## 练习/任务
1. 实现 StudentDB 数据层
2. 添加 3 个学生并列出
3. 按姓名查找
4. （选做）按成绩查找不及格学生

## 今日刷题
- 今天项目为主，不安排算法题。

## 打卡清单
- [ ] 实现动态数组存储
- [ ] 完成添加列出查找
- [ ] 正确释放内存
- [ ] 数据层与界面分离
