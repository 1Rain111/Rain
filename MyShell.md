# shell

## shell解析器

 查看linux所有解析器

` cat /etc/shells`



默认解析器

` /bin/bash`

` echo $SHELL` #查看默认解析器



## shell文本规范

### 首行规范

首行设置shell解析器类型，语法

```shell
#!/bin/bash
```







## 脚本语言的常用执行方式

1.sh解析器执行

语法：` sh 脚本文件`

2.bash解析器执行

语法：` bash 脚本文件`

3.仅路径执行方式

语法：` ./脚本文件`

需要有可执行执行权限











1. -eq //equals等于

2. ​

   -ne //no equals不等于

3. ​

   -gt //greater than 大于

4. ​

   -lt //less than小于

5. ​

   -ge //greater equals大于等于

6. ​

   -le //less equals小于等于




`sed`（stream editor）是一种流编辑器，它能够对文本和数据进行处理。`sed`的工作方式是通过读取输入流（文件或管道），应用一系列的编辑命令，然后将结果输出到标准输出（通常是屏幕，但也可以是文件）。`sed`非常适合于执行文本替换、删除、新增、插入等操作。

### 基本语法

```
bash复制代码

sed [选项]... '{脚本}' [输入文件]...
```

- **选项**：`sed` 命令可以接受多个选项，但常用的不多。例如，`-i` 选项用于直接修改文件内容（而不是输出到标准输出）。
- **脚本**：这是 `sed` 命令的核心，它包含了一系列的编辑命令，这些命令被大括号 `{}` 包围（但在很多情况下，如果只有一条命令，大括号可以省略）。
- **输入文件**：`sed` 命令处理的文件。如果没有指定输入文件，`sed` 会从标准输入读取数据。

### 常用命令

- 替换

  ：

  ```
  s/原字符串/新字符串/标志
  ```

  - 标志可以是 `g`（全局替换），`i`（忽略大小写）等。
  - 示例：`sed 's/old/new/g' file.txt` 将 `file.txt` 中所有的 "old" 替换为 "new"。

- 删除

  ：

  ```
  d
  ```

  - 示例：`sed '3d' file.txt` 删除 `file.txt` 的第三行。
  - 示例：`sed '/pattern/d' file.txt` 删除包含 "pattern" 的所有行。

- 插入

  ：

  ```
  i
  ```

  - 示例：`sed '2i\This is a new line.' file.txt` 在 `file.txt` 的第二行之前插入 "This is a new line."。

- 追加

  ：

  ```
  a
  ```

  - 示例：`sed '$a\This is a new line.' file.txt` 在 `file.txt` 的末尾追加 "This is a new line."。

- 修改

  ：

  ```
  c
  ```

  - 示例：`sed '2c\This is the new second line.' file.txt` 将 `file.txt` 的第二行替换为 "This is the new second line."。

### 示例

- **直接修改文件**（使用 `-i` 选项）：

  ```
  bash复制代码

  sed -i 's/old/new/g' file.txt
  ```

  这会将 `file.txt` 中所有的 "old" 替换为 "new"，并直接修改文件内容。

- **删除包含特定模式的行**：

  ```
  bash复制代码

  sed '/pattern/d' file.txt
  ```

  这会删除 `file.txt` 中所有包含 "pattern" 的行，并将结果输出到标准输出。

- **打印特定行**（虽然这不是 `sed` 的主要目的，但可以通过 `p` 命令实现）：

  ```
  bash复制代码

  sed -n '3p' file.txt
  ```

  使用 `-n` 选项和 `p` 命令可以仅打印 `file.txt` 的第三行。

`sed` 是一个非常强大的文本处理工具，掌握它可以大大提高文本处理的效率。不过，由于 `sed` 的命令和选项非常多，这里只介绍了其中的一小部分。建议通过实践和学习来进一步掌握 `sed` 的高级用法。











