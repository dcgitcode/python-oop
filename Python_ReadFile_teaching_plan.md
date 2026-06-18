# Python 程序设计 教学方案

**课题**：Python 文件操作 — 读文本文件  
**课时**：2节（共80分钟）  
**授课对象**：软件2503班

---

## 一、教学目标

**【知识目标】**
1. 掌握 `read()` 函数的三种用法：读全部、读指定字符数、逐字符读；
2. 掌握 `readline()` 逐行读取文件的方法；
3. 掌握 `readlines()` 一次读取所有行、返回列表的方法；
4. 理解文件位置指针的概念，掌握指针到达末尾后返回空串 `""` 的特征；
5. 掌握用 `strip("\n")` 去除换行符，配合 `split()` 提取字段。

**【技能目标】**
1. 能独立编写文件读取函数并做异常处理；
2. 能从 `students.txt`（上节课生成）中读出学生信息并格式化输出、按条件查询。

**【素养目标】**
培养"数据前后一致性"意识——写入格式决定读取逻辑，规范写入才能顺利读取。

---

## 二、教学重难点

**【重点】**
- `read()`、`readline()`、`readlines()` 三种方法的语法与区别；
- 文件位置指针到达末尾后返回空字符串 `""` 的行为；
- `strip("\n")` + `split()` 处理真实数据的流程。

**【难点】**
- `read(n)` 读取指定字符数时指针的移动，以及超出文件长度时的行为；
- `readlines()` 返回列表，每个元素末尾含 `\n`，需要手动去除；
- 用循环逐行处理读取结果，提取有效信息。

---

## 三、教学内容与时间安排（共80分钟）

---

### 【第一节】读文本文件操作（40分钟）

#### 1. 导入新课（5分钟）

**场景引入：**

> 上节课我们把5位同学的信息写进了 `students.txt`。  
> 现在辅导员要查王五的手机号——文件打开了，怎么把里面的内容"读"出来？

在黑板上写出上节课的写文件代码（3行）：

```python
f = open(r"D:\students.txt", "w")
f.write("2024001  张三   13800138000\n")
f.close()
```

提问：**"写文件用的是 `'w'` 模式，那读文件用什么模式？"**  
→ 引出 `"r"`（read）模式，自然过渡到今天的内容。

---

#### 2. 文件位置指针（3分钟）

打开文件后，Python 内部有一个**位置指针**，记录当前读取到了文件的哪个位置。

| 操作 | 指针变化 |
|------|----------|
| `open()` 打开文件 | 指针在文件**开头第一个字符**的位置 |
| `read()` 读取全部 | 指针移到文件**末尾** |
| `read(n)` 读取n个字符 | 指针向后移动n位 |
| 指针已到**文件末尾** | 再次读取，返回**空字符串 `""`** |

> 就像在书上用手指跟着文字一行行读——从第一个字开始，读完往后移，读到最后一个字之后再往后，什么都没有了，返回 `""`。

---

#### 3. read() 函数——读取字符（20分钟）

> **约定：** 以下每段代码都先用 3 行写文件（无需异常处理），再用函数读取，重点放在读取逻辑上。

---

**（1）read() 读取全部内容**

> 上节课辅导员把签到情况写进了文件，现在他想把文件内容一次性全部打印出来——用 `read()` 最方便。

```python
# 写文件（3行，简单准备数据）
f = open(r"D:\hello.txt", "w")
f.write("Hello!\nPython!")
f.close()

def read_file():
    f = None
    try:
        f = open(r"D:\hello.txt", "r")
        content = f.read()          # 读取全部内容，返回一个字符串
        print(content)
        print("字符串长度：", len(content))
    except Exception as e:
        print("读取失败：", e)
    finally:
        if f:
            f.close()

read_file()
```

输出：
```
Hello!
Python!
字符串长度：14
```

---

**（2）read(n) 读取指定字符数**

> 班长检查同学提交的文字作业，他不想一次读完，每次只先看前几个字判断对不对——用 `read(n)` 每次只读 n 个字符，**指针会移动到第 n 个字符之后**，下次接着读。
>
> 下面用动画演示文件指针如何随 `read(n)` 一步步移动（课件中点击"下一步"可逐步查看）：

```python
# 写文件
f = open(r"D:\hello.txt", "w")
f.write("Hello!\nPython!")
f.close()

def read_file():
    f = None
    try:
        f = open(r"D:\hello.txt", "r")
        part1 = f.read(3)
        print("前3个字符：", part1, "  长度：", len(part1))
        part2 = f.read(4)
        print("再读4个字符：", part2, "  长度：", len(part2))
        rest = f.read(999)          # 超过剩余长度，只返回剩余内容
        print("剩余内容：", rest,   "  长度：", len(rest))
        empty = f.read(5)           # 指针已到末尾，返回空串
        print("末尾再读：", repr(empty), "  长度：", len(empty))
    except Exception as e:
        print("读取失败：", e)
    finally:
        if f:
            f.close()

read_file()
```

输出：
```
前3个字符：  Hel   长度：  3
再读4个字符：  lo!\n   长度：  4
剩余内容：  Python!   长度：  7
末尾再读：  ''   长度：  0
```

> `read(999)` 超过文件剩余字符数时，**不会报错**，只返回剩余所有内容；  
> 指针到达末尾后再读，返回空字符串 `""`，`len()` 为 0。

---

**（3）read(1) 读取单个字符**

> 老师在线上平台批改作文，程序每次只读一个字符，遇到错别字就标记——这就是 `read(1)` 的使用场景。

```python
# 写文件
f = open(r"D:\hello.txt", "w")
f.write("Hello!\nPython!")
f.close()

def read_file():
    f = None
    try:
        f = open(r"D:\hello.txt", "r")
        char = f.read(1)
        while char:                 # char 为空串 "" 时循环结束
            print(char, end="")
            char = f.read(1)
    except Exception as e:
        print("读取失败：", e)
    finally:
        if f:
            f.close()

read_file()
```

---

#### 4. readline() 函数——读取一行（10分钟）

> 值日生每天逐行核对宿舍卫生记录：念第一行，记下来；再念第二行，记下来……——这就是 `readline()` 的逻辑，每次只读一行。

`readline()` 每次读取一行（包括行末的 `\n`），读到文件末尾时返回空字符串 `""`。

**先拼接所有行，最后再一次性输出：**

```python
# 写文件
f = open(r"D:\hello.txt", "w")
f.write("Hello!\nPython!")
f.close()

def read_file():
    f = None
    try:
        f = open(r"D:\hello.txt", "r")
        result = ""
        line = f.readline()
        while line:                 # line 为 "" 时循环结束
            result = result + line
            line = f.readline()
        print(result)
    except Exception as e:
        print("读取失败：", e)
    finally:
        if f:
            f.close()

read_file()
```

> `readline()` 读到的每行末尾都含 `\n`，第一节演示阶段直接拼接输出即可；  
> 第二节处理真实学生数据时，才需要用 `strip("\n")` 去掉换行。

---

#### 🎯 课堂互动：改错题（5分钟）

**展示下面一段代码，请同学找出并说明2处错误：**

```python
f = open(r"D:\hello.txt", "r")
content = f.read()
print(content)
# 想再读一次
content2 = f.read()
print("第二次读取：", content2)
```

**让学生思考30秒，然后点名"陈浩同学，你来说说第一处错误在哪里？"**

> 💡 **讲解要点**：学生回答后，老师口头讲解正确写法，把指针和异常处理的知识点串联起来。

---

#### 5. readlines() 函数——读取所有行（7分钟）

> 辅导员开学拿到一份打印好的全班花名册，上面每行一个同学——他想把所有行都存到程序里，随时查找——这就是 `readlines()` 的场景：**一次性把所有行读进列表**。

`readlines()` 一次性把文件所有行读出来，返回一个**列表**，每个元素是一行（含 `\n`）。

```python
# 写文件
f = open(r"D:\hello.txt", "w")
f.write("Hello!\nPython!")
f.close()

def read_file():
    f = None
    try:
        f = open(r"D:\hello.txt", "r")
        lines = f.readlines()       # 返回列表，每个元素是一行（含\n）
        print(f"共读取 {len(lines)} 行")
        print(lines)                # 直接打印列表，可看到每行末尾有\n
        for line in lines:
            print(line, end="")
    except Exception as e:
        print("读取失败：", e)
    finally:
        if f:
            f.close()

read_file()
```

---

**三种读取方法对比总结：**

| 方法 | 返回类型 | 一次读多少 | 末尾判断 | 适合场景 |
|------|----------|-----------|----------|---------|
| `read()` | 字符串 | 全部内容 | 返回 `""` | 文件较小，一次性处理 |
| `read(n)` | 字符串 | n 个字符 | 返回 `""` | 逐字符/分段读 |
| `readline()` | 字符串 | 一行（含`\n`） | 返回 `""` | 逐行处理 |
| `readlines()` | 列表 | 全部行（每行含`\n`） | 列表为空 | 需要对所有行做集合操作 |

---

### 随堂练习1（第一节）

1. `f.read()` 不带参数时，返回的是（  ）。  
   A. 文件第一行  B. 文件全部内容字符串  C. 文件的字节数  D. 文件名  
   **答案：B**

2. 下列说法正确的是（  ）。  
   A. `readline()` 每次读一个字符  B. `readlines()` 返回一个字符串  
   C. `read(5)` 读取前5个字符  D. `readlines()` 不含换行符  
   **答案：C**

3. `readlines()` 读取文件后，列表中每个元素（  ）。  
   A. 末尾含 `\n`  B. 末尾不含 `\n`  C. 只有文字无标点  D. 是整数  
   **答案：A**

4. 判断：`readline()` 读到文件末尾时，返回 `None`。  
   **答案：错误。返回空字符串 `""`，不是 `None`。**

5. 判断：`f.read(999)` 中999超过文件实际字符数，程序会报错。  
   **答案：错误。只返回文件剩余的所有内容，不报错。**

6. 填空：班长想一次性把 `students.txt` 的所有行读入变量 `lines`，应该写：`lines = f.______()`  
   **答案：readlines**

7. 编程题：定义 `read_file()` 函数，读取 `D:\hello.txt`，用 `readline()` 循环把每行拼接到 `result`，最后一次性输出。

---

### 【第二节】案例：从文件中读出学生信息（35分钟）

#### 6. 案例背景回顾（3分钟）

**场景：** 上节课我们把5位同学的信息存进了 `D:\students.txt`，现在辅导员要查名单、找手机号，需要把文件内容读出来。

> （课堂上可以点名"陈浩同学，上节课的学生信息文件你存好了吗？"——用这个钩子拉回注意力。）

**students.txt 内容：**

```
学号      姓名   手机号
-----------------------------------
2024001  张三   13800138000
2024002  李四   13900139000
2024003  王五   15000150000
2024004  赵六   18600186000
2024005  孙七   13700137000
```

> **为什么这里要 `strip("\n")`？**  
> 本节需要对每行做 `split()` 拆出字段——如果行末带着 `\n`，拆出来的最后一个字段也会带 `\n`，数据就脏了。  
> 所以**处理真实数据时，先 `strip("\n")` 再 `split()`**。

---

#### 7. 案例一：readlines() 读取并输出所有学生信息（12分钟）

> 辅导员早上拿着学生名单，逐行念出每位同学的名字和手机号——用 `readlines()` 一次性读进列表，再逐行处理。

```python
def read_students():
    f = None
    try:
        f = open(r"D:\students.txt", "r")
        lines = f.readlines()
        print("===== 学生信息 =====")
        for line in lines:
            line = line.strip("\n")   # 去掉每行末尾的换行符
            print(line)
        print(f"共 {len(lines)} 条记录")
    except Exception as e:
        print("读取失败：", e)
    finally:
        if f:
            f.close()

read_students()
```

**讲解要点：**
- `strip("\n")` 只去掉 `\n`；`strip()` 不加参数可去掉首尾所有空白，更常用；
- `len(lines)` 统计总行数（包括表头和分隔线）。

---

#### 8. 案例二：readline() 逐行读取并跳过分隔线（10分钟）

> 值日生逐行核对宿舍卫生记录表，遇到分隔线直接跳过——用 `readline()` 逐行读，加条件判断跳过不需要的行。

```python
def read_students():
    f = None
    try:
        f = open(r"D:\students.txt", "r")
        line = f.readline()
        while line:
            line = line.strip("\n")
            if line and not line.startswith("-"):  # 跳过分隔线
                print(line)
            line = f.readline()
    except Exception as e:
        print("读取失败：", e)
    finally:
        if f:
            f.close()

read_students()
```

---

#### 9. 案例三：提取字段，按姓名查询（15分钟）

> 辅导员说"帮我查一下王五的手机号"，程序从文件里自动找出来——需要把每行拆成字段，存进字典，再按姓名检索。

```python
def read_students():
    """解析 students.txt，返回学生字典列表"""
    f = None
    students = []
    try:
        f = open(r"D:\students.txt", "r")
        lines = f.readlines()
        for line in lines:
            line = line.strip("\n")
            if line.startswith("学号") or line.startswith("-") or line == "":
                continue
            parts = line.split()      # 按空白字符分割
            if len(parts) >= 3:
                students.append({"id": parts[0], "name": parts[1], "phone": parts[2]})
    except Exception as e:
        print("读取失败：", e)
    finally:
        if f:
            f.close()
    return students

def query_student(students, target_name):
    """按姓名查询学生手机号"""
    for s in students:
        if s["name"] == target_name:
            print(f"找到了！{target_name} 的手机号是：{s['phone']}")
            return
    print(f"未找到：{target_name}")

# 主程序
students = read_students()
for s in students:
    print(f"学号：{s['id']}  姓名：{s['name']}  手机：{s['phone']}")
print(f"共 {len(students)} 名学生")

query_student(students, "王五")
```

**讲解要点：**
1. `strip("\n")` 去掉行末换行符后，再 `split()` 拆字段，防止字段末尾带 `\n`；
2. `split()` 不加参数，自动以任意空白字符为分隔符；
3. `if len(parts) >= 3` 防止空行越界；
4. 读取和查询拆成两个函数，职责分离。

---

### 随堂练习2（第二节）

1. `strip("\n")` 的作用是（  ）。  
   A. 删除字符串所有 `n`  B. 删除字符串首尾的换行符 `\n`  
   C. 把换行符替换成空格  D. 删除字符串中所有空格  
   **答案：B**

2. 判断：`lines = f.readlines()` 后，`lines[0]` 末尾不含 `\n`。  
   **答案：错误。`readlines()` 读取的每行末尾都包含 `\n`。**

3. 填空：要把 `"2024001  张三   13800138000\n"` 按空白字符分割成列表，应该写：`parts = line.______()`  
   **答案：split（不加参数）**

4. 判断：用 `readlines()` 读取后，跳过表头行的方法是 `if line.startswith("学号"): continue`。  
   **答案：正确。**

5. 编程题：定义 `count_students()`，从 `D:\students.txt` 读取，统计学生总人数（不含表头和分隔线），打印出来。

6. 编程题：定义 `find_by_phone_prefix(prefix)`，找出手机号以指定前缀开头的学生，打印其姓名和手机号。

---

## 四、课后作业

**1. 填空：** `readlines()` 返回的是一个____，每个元素是文件的____行内容（含`\n`）。  
**答案：列表；一**

**2. 单选：** `f.read()` 读取全部内容后，再调用一次 `f.read()`，返回的是（  ）。  
A. 全部内容  B. 文件第一行  C. 空字符串  D. 报错  
**答案：C**

**3. 编程题：** 定义函数 `print_students()`：读取 `D:\students.txt`，跳过表头和分隔线，按格式打印每名学生：`张三：13800138000`，要求做异常处理。

```python
# 参考答案
def print_students():
    f = None
    try:
        f = open(r"D:\students.txt", "r")
        lines = f.readlines()
        for line in lines:
            line = line.strip("\n")
            if line and not line.startswith("学号") and not line.startswith("-"):
                parts = line.split()
                if len(parts) >= 3:
                    print(f"{parts[1]}：{parts[2]}")
    except Exception as e:
        print("读取失败：", e)
    finally:
        if f:
            f.close()

print_students()
```

**4. 综合编程题：** 图书馆有一个借阅记录文件 `D:\borrow_log.txt`，每行格式为：`学号,姓名,书名,日期`。  
定义两个函数：
- `write_borrow_log()`：向文件写入3条借阅记录；
- `read_borrow_log()`：读取文件，按格式输出每条记录。

```python
# 参考答案
def write_borrow_log():
    f = open(r"D:\borrow_log.txt", "w")
    f.write("2024001,张三,Python程序设计,2025-09-10\n")
    f.write("2024002,李四,数据结构,2025-09-11\n")
    f.write("2024003,王五,算法导论,2025-09-12\n")
    f.close()

def read_borrow_log():
    f = None
    try:
        f = open(r"D:\borrow_log.txt", "r")
        lines = f.readlines()
        print("===== 借阅记录 =====")
        for line in lines:
            line = line.strip("\n")
            if line:
                parts = line.split(",")
                if len(parts) >= 4:
                    print(f"学号：{parts[0]}  姓名：{parts[1]}  书名：{parts[2]}  日期：{parts[3]}")
    except Exception as e:
        print("读取失败：", e)
    finally:
        if f:
            f.close()

write_borrow_log()
read_borrow_log()
```

---

## 五、课堂小结（5分钟）

| 方法 | 语法 | 返回值 | 注意事项 |
|------|------|--------|---------|
| `read()` | `f.read()` | 全部内容字符串 | 末尾再读返回 `""` |
| `read(n)` | `f.read(10)` | 前n个字符 | 超出不报错；`len()` 查长度 |
| `readline()` | `f.readline()` | 一行（含`\n`） | 末尾返回 `""` |
| `readlines()` | `f.readlines()` | 所有行的列表 | 处理数据时需 `strip` |

**核心口诀：**
- 读文件用 `"r"` 模式；
- 位置指针从头开始，读多少移多少，到末尾返回 `""`；
- 处理真实数据时，先 `strip("\n")` 再 `split()`；
- 打开放 `try`，关闭放 `finally`！

---

## 六、教学备注

1. **指针可视化**：现场用 `repr(line)` 展示含 `\n` 的原始字符串，帮助学生"看见"指针读到的内容；
2. `split()` 不加参数的功能可和 `split(" ")` 对比（多个空格时的差异）；
3. **课堂互动**：导入环节点名"陈浩同学"作为代入角色（上节课存了学生信息），PPT课件中统一用"同学"，不出现真实姓名；改错题环节再次点名，双重互动设计。
