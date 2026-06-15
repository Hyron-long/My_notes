

### 📋 MySQL 终端命令速查表

| 操作类别       | 命令语法/示例                                                | 简要说明                                                     |
| :------------- | :----------------------------------------------------------- | :----------------------------------------------------------- |
| **连接与退出** | `mysql -u root -p`<br>`mysql -h 127.0.0.1 -P 3306 -u root -p`<br>`EXIT;` 或 `QUIT;` | 连接本地或远程数据库<br>退出MySQL命令行                      |
| **数据库操作** | `SHOW DATABASES;`<br>`CREATE DATABASE db_name;`<br>`USE db_name;`<br>`DROP DATABASE db_name;` | 查看所有数据库<br>创建新数据库<br>切换到指定数据库<br>删除数据库 |
| **表操作**     | `SHOW TABLES;`<br>`DESC table_name;`<br>`CREATE TABLE ...`<br>`DROP TABLE table_name;` | 查看当前库中所有表<br>查看表结构<br>创建数据表<br>删除数据表 |
| **数据操作**   | `SELECT * FROM table_name;`<br>`INSERT INTO ...`<br>`UPDATE ...`<br>`DELETE FROM ...` | 查询数据<br>插入新数据<br>更新数据<br>删除数据               |
| **用户与权限** | `CREATE USER 'name'@'host' IDENTIFIED BY 'pwd';`<br>`GRANT ALL ON *.* TO 'name'@'host';`<br>`FLUSH PRIVILEGES;`<br>`SHOW GRANTS FOR 'name'@'host';` | 创建用户<br>授予权限<br>使权限立即生效<br>查看用户权限       |
| **备份与恢复** | `mysqldump -u root -p db_name > backup.sql`<br>`mysql -u root -p db_name < backup.sql` | 备份数据库到文件<br>从文件恢复数据库                         |
| **服务管理**   | `sudo systemctl start mysql` (Linux)<br>`net start MySQL` (Windows) | 启动MySQL服务<br>启动MySQL服务                               |
| **实用命令**   | `STATUS;`<br>`SELECT VERSION();`<br>`SELECT DATABASE();`<br>`SOURCE /path/to/file.sql;` | 查看MySQL状态信息<br>查看数据库版本<br>查看当前使用的数据库<br>执行SQL脚本文件 |

---

### 🔧 环境准备与连接

在输入命令前，需要先确保环境正确并能连上数据库。

1.  **配置环境变量 (特别重要)**
    这是让你在任意目录下都能直接使用 `mysql` 命令的关键一步。否则，你每次都要先进入MySQL安装目录下的 `bin` 文件夹，非常麻烦。
    *   **Windows**: 将MySQL的 `bin` 目录路径（例如 `C:\Program Files\MySQL\MySQL Server 8.0\bin`）添加到系统环境变量 `Path` 中。
    *   **macOS / Linux**: 通常安装后会自动配置。如果不行，可以编辑 `~/.bash_profile` 或 `~/.zshrc` 文件，添加 `export PATH=$PATH:/usr/local/mysql/bin`。

2.  **连接到MySQL服务器**
    *   **连接本地数据库**（最常用）: 打开终端（Windows上是CMD或PowerShell），输入 `mysql -u root -p`，然后回车输入你的密码即可。
    *   **连接远程数据库**: 如果需要连接公司服务器或云数据库，需要指定IP和端口：
        ```bash
        mysql -h 192.168.1.100 -P 3306 -u admin -p
        ```
        *   `-h`: 数据库服务器的IP地址
        *   `-P`: 端口号（默认为3306，注意是大写的P）
        *   `-u`: 用户名
        *   `-p`: 密码（直接在后面输入，如 `-p123456`，但**为了安全，强烈建议不写，让系统交互式地询问你**）

3.  **安全提示**
    不要在命令行中直接明文输入密码（如 `mysql -uroot -p123456`）。这会让密码留在历史记录里，很不安全。正确方式是只输入 `-p` 然后回车，在提示下输入。

---

### 📚 核心操作实战

连接成功后，你会看到 `mysql>` 提示符。现在就可以输入命令了。**注意：每条SQL命令必须以英文分号 `;` 或 `\g` 结尾**。

#### 1. 基础“逛一逛”
登录后，先别急着操作，用几个命令熟悉一下环境：
```sql
-- 查看当前用户是谁
SELECT USER();
-- 查看数据库版本
SELECT VERSION();
-- 查看服务器上有哪些数据库
SHOW DATABASES;
```
通常你会看到 `information_schema`, `mysql`, `performance_schema`, `sys` 这几个系统数据库。**千万别随意改动它们**，这是MySQL自己的核心库。

#### 2. 数据库与表的核心操作
我们来动手创建一个属于自己的数据库和数据表：
```sql
-- 创建一个名为 'my_test_db' 的数据库，并指定utf8mb4字符集（支持emoji和更多字符）
CREATE DATABASE my_test_db CHARACTER SET utf8mb4 COLLATE utf8mb4_unicode_ci;

-- 选择（进入）这个数据库
USE my_test_db;

-- 看看当前数据库里是不是空的
SHOW TABLES;

-- 创建一个 'students' 表，包含id，姓名和年龄
-- INT是整数，VARCHAR是字符串，PRIMARY KEY是主键（唯一标识）
CREATE TABLE students (
    id INT AUTO_INCREMENT PRIMARY KEY,
    name VARCHAR(50) NOT NULL,
    age INT
);

-- 再次查看表，这次应该看到 students 了
SHOW TABLES;

-- 查看 students 表的结构（有哪些列，什么类型）
DESC students;
```

#### 3. 数据的增删改查 (CRUD)
这是和业务数据打交道的最常用操作：
```sql
-- 插入数据 (Create)
INSERT INTO students (name, age) VALUES ('Alice', 20);
INSERT INTO students (name, age) VALUES ('Bob', 22), ('Charlie', 21); -- 一次插入多条

-- 查询数据 (Read)
-- 查询所有
SELECT * FROM students;
-- 查询特定列和行
SELECT name, age FROM students WHERE age > 20;

-- 更新数据 (Update)
UPDATE students SET age = 23 WHERE name = 'Bob';

-- 删除数据 (Delete)
DELETE FROM students WHERE name = 'Charlie';
```

#### 4. 用户与权限管理
作为管理员，需要学会创建不同权限的用户：
```sql
-- 创建一个新用户 'app_user'，只允许从本地连接，密码为 'secure_pass'
CREATE USER 'app_user'@'localhost' IDENTIFIED BY 'secure_pass';

-- 给这个用户授予对 'my_test_db' 数据库下所有表的查询和插入权限
GRANT SELECT, INSERT ON my_test_db.* TO 'app_user'@'localhost';

-- 记得刷新权限，使设置生效
FLUSH PRIVILEGES;

-- 查看该用户拥有的权限
SHOW GRANTS FOR 'app_user'@'localhost';
```

#### 5. 数据库的备份与恢复
这是日常维护的关键：
*   **备份**：在**操作系统的终端**（不是 `mysql>` 里）执行：
    ```bash
    # 将 my_test_db 数据库备份到当前目录下的 my_test_db_backup.sql 文件
    mysqldump -u root -p my_test_db > my_test_db_backup.sql
    ```
*   **恢复**：同样在操作系统终端执行：
    ```bash
    # 将备份文件恢复到 my_test_db 数据库
    mysql -u root -p my_test_db < my_test_db_backup.sql
    ```
    *小技巧：你也可以登录MySQL后，在 `mysql>` 提示符下使用 `SOURCE /path/to/file.sql;` 命令来执行SQL脚本。*

---

### 💡 实用技巧

*   **提高可读性**: 用 `\G` 代替分号来结束查询，结果会按列垂直显示，特别适合字段很多的表。例如：`SELECT * FROM students WHERE id = 1\G` 。
*   **取消输入**: 如果你输错了一长串命令，可以输入 `\c` 来取消当前输入，然后重新开始。
*   **自动补全**: MySQL命令行支持 `Tab` 键自动补全保留字和表名，可以多试试。
*   **查看帮助**: 在 `mysql>` 提示符下输入 `HELP;` 或 `\h` 可以查看帮助文档。

### ⚠️ 常见问题排查
*   **`mysql: command not found` / ‘mysql’ 不是内部或外部命令**：这个错误很常见，说明环境变量没配置好。按上面“环境准备”部分的步骤，把MySQL的 `bin` 目录加到系统PATH里即可。
*   **`ERROR 1045 (28000): Access denied for user...`**：用户名或密码错误。可以试试初始密码，或者用 `sudo` 权限重置密码。
*   **`ERROR 2003 (HY000): Can't connect to MySQL server...`**：服务没启动。Linux用 `systemctl start mysql`，Windows用管理员权限运行 `net start mysql` 。
*   **忘记密码怎么办？**：可以跳过授权表启动MySQL来重置，这是另一个稍微复杂的主题，可以之后再深入了解。
