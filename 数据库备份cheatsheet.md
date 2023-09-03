当你需要备份和恢复MySQL数据库时，mysqldump是一个非常有用的工具。以下是一个详尽的mysqldump cheatsheet，包括常用的mysqldump命令和选项，以及一些常见的用例示例。

**1. 基本语法：**
```bash
mysqldump -u [用户名] -p[密码] [数据库名] > [输出文件名].sql
```

- `-u`：指定用户名。
- `-p`：在不输入密码的情况下提示输入密码。
- `[数据库名]`：要备份的数据库名称。
- `[输出文件名].sql`：将备份输出到的文件名。

**2. 备份整个数据库：**
```bash
mysqldump -u [用户名] -p[密码] [数据库名] > [输出文件名].sql
```

**3. 备份多个数据库：**
```bash
mysqldump -u [用户名] -p[密码] --databases [数据库1] [数据库2] > [输出文件名].sql
```

**4. 备份所有数据库：**
```bash
mysqldump -u [用户名] -p[密码] --all-databases > [输出文件名].sql
```

**5. 仅备份数据库结构（无数据）：**
```bash
mysqldump -u [用户名] -p[密码] --no-data [数据库名] > [输出文件名].sql
```

**6. 仅备份数据库数据（无结构）：**
```bash
mysqldump -u [用户名] -p[密码] --no-create-info [数据库名] > [输出文件名].sql
```

**7. 备份特定表：**
```bash
mysqldump -u [用户名] -p[密码] [数据库名] [表名] > [输出文件名].sql
```

**8. 添加CREATE DATABASE语句到备份文件：**
```bash
mysqldump -u [用户名] -p[密码] --databases [数据库名] --add-drop-database > [输出文件名].sql
```

**9. 压缩备份文件：**
```bash
mysqldump -u [用户名] -p[密码] [数据库名] | gzip > [输出文件名].sql.gz
```

**10. 恢复数据库：**
```bash
mysql -u [用户名] -p[密码] [数据库名] < [备份文件].sql
```

**11. 导出特定表到CSV文件：**
```bash
mysqldump -u [用户名] -p[密码] --tab=[输出目录] [数据库名] [表名]
```

**12. 仅备份存储过程和函数：**
```bash
mysqldump -u [用户名] -p[密码] --routines [数据库名] > [输出文件名].sql
```

**13. 备份远程MySQL服务器：**
```bash
mysqldump -h [远程主机] -u [用户名] -p[密码] [数据库名] > [输出文件名].sql
```