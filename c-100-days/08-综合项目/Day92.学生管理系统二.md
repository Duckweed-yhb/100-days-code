# Day 92 · 学生管理系统二

## 今日目标
- 加文件持久化
- 加删除与修改
- 完成菜单循环

## 学习内容

### 保存到文件

```c
void db_save(const StudentDB *db, const char *path) {
    FILE *fp = fopen(path, "wb");
    if (!fp) { perror("save"); return; }
    fwrite(db->data, sizeof(Student), db->size, fp);
    fclose(fp);
}

void db_load(StudentDB *db, const char *path) {
    FILE *fp = fopen(path, "rb");
    if (!fp) return;
    Student buf[1000];
    int n = fread(buf, sizeof(Student), 1000, fp);
    fclose(fp);
    for (int i = 0; i < n; i++) db_add(db, buf[i].name, buf[i].score);
}
```

### 删除

```c
void db_remove(StudentDB *db, int idx) {
    if (idx < 0 || idx >= db->size) return;
    for (int i = idx; i < db->size - 1; i++) {
        db->data[i] = db->data[i+1];
    }
    db->size--;
}
```

### 修改

```c
void db_update(StudentDB *db, int idx, int new_score) {
    if (idx < 0 || idx >= db->size) return;
    db->data[idx].score = new_score;
}
```

### 主菜单

```c
int main(void) {
    StudentDB db;
    db_init(&db);
    db_load(&db, "students.dat");

    int op;
    do {
        printf("\n1.添加 2.列出 3.查找 4.删除 5.保存 0.退出\n> ");
        scanf("%d", &op);
        if (op == 1) {
            char name[20]; int score;
            printf("姓名 成绩: ");
            scanf("%19s %d", name, &score);
            db_add(&db, name, score);
        } else if (op == 2) {
            db_list(&db);
        } else if (op == 5) {
            db_save(&db, "students.dat");
        }
    } while (op != 0);

    db_save(&db, "students.dat");
    db_free(&db);
    return 0;
}
```

## 核心重点
- 二进制文件持久化
- 删除靠前移
- 菜单循环交互
- 退出时自动保存
- 完整 CRUD

## 练习/任务
1. 完成文件保存加载
2. 加删除和修改
3. 完整菜单跑通
4. （选做）加排序功能（按成绩）

## 今日刷题
- 今天项目为主，不安排算法题。

## 打卡清单
- [ ] 完成学生管理系统
- [ ] 数据持久化
- [ ] 完整 CRUD
- [ ] 菜单交互友好
