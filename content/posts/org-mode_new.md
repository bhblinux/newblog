+++
title = "org-mode_new"
description = "Short description"
date = 2019-07-18T16:20:39+08:00
draft = false
+++

## org-mode {#org-mode}


### 基础操作 {#基础操作}


#### 1. 标题 {#1-dot-标题}

文章中的标题可以通过#+TITLE: 来设置.如

`#+TITLE: org-mode`

正文中的标题可以通过\*,后面要空格,然后输入标题即可,一个\*表示一级,最多10个


#### 2. 段落 {#2-dot-段落}

开始新的段落需要回车两次,当你回车一次,发布时,只相当于中间有一个空格,还有一种就是需要空格的地方输入<br />

段落一

段落二<br />
段落三


#### 3. 字体 {#3-dot-字体}

**粗体**<br />
_斜体_<br />
~~删除线~~<br />
<span class="underline">下划线</span> <br />
下标: H\_2 O


#### 4.列表 {#4-dot-列表}

1.  有序列表和无序列表2钟
    1.  无序列表以'-','+'或者'\*'开头
    2.  有序列表以'1.','1)'开头
    3.  描述列表用'::'
2.  注意事项
    1.  列表符号后面都要有空格
    2.  统计别的列表缩进要相同
    3.  如果想要加入统计的列表,可以M-RET
    4.  空2行之后列表结束,如果空一行执行M-RET,实际上还是输入统计项


#### 5. 示例 {#5-dot-示例}

-    Lord of  the rings

    my favorite scenes are(in this order)

    1.  the
    2.  sfdf
        -   1111
        -   2222
    3.  peter

        -   on

        he makessfdf


#### 列表 {#列表}


#### 嵌入元数据 {#嵌入元数据}

-    内容元数据

    org-mode中有以下几种

    ```text
    s #+begin_src ... #+end_src
    e #+begin_example ... #+end_example : 单行的例子以冒号开头
    q #+begin_quote ... #+end_quote 通常用于引用,与默认格式相比左右都会留出缩进
    v 默认内容不换行,需要留出空格才能换行
    c center
    l latex
    h html
    a  ascii
    i line
    I
    ```

    example

    ```text
    e
    aaaaa
    #aaaaa
    #bbbb
    ```

    quote

    sdfdsfdsfd

    > q
    > usedo
    > s
    > fsd
    > f
    > sdf

    verse

    aaaaa<br />
    bbbb<br />
    ccc<br />

    html

-    文档元数据

    `快捷键C-c C-e t`

-    表格

    <div class="table-caption">
      <span class="table-number">Table 1</span>:
      表格的标题
    </div>

    | 1 | one                |
    |---|--------------------|
    | 2 | two                |
    | 3 | this a a long text |
    | 4 | foure              |

    -   整体区域

    | 操作   | 说明            |
    |------|---------------|
    | C-c 竖线 | 创建或者转化成表格 |
    | TAB    | 移动到下一区域,必要时新建一行 |
    | S-TAB  | 移动到上一区域  |
    | RET    | 移动到下一行,必要时新建一行 |

    -   编辑行和列

    | 快捷键          | 说明             |
    |--------------|----------------|
    | M-LEFT/RIGHT    | 移动列           |
    | M-UP/DOWN       | 移动行           |
    | M-S-LIEFT/RIGHT | 删除/插入列      |
    | M-S-UP/DOWN     | 删除行/插入行    |
    | C-c -           | 添加水平分割线   |
    | C-c RET         | 添加生平分割线并跳到下一行 |
    | C-c ^           | 根据当前列排序,可以选择排序方式 |

-    表格中的计算

    在第二行第三列中输入'=$1\*$2',

    | 单价 | 数量 | 总额 |
    |----|----|----|
    | 3.45 | 2  | 6.9 |
    | 1    | 3  | 3   |
    | 4    | 2  | 8   |
    | 5    | 3  | 15  |
    | 1.1  | 20 | 22. |

    输入'C-u C-c  C-c'强制计算

-    连接

    ```text
    [[url][连接内容]]
    ```

    <div class="table-caption">
      <span class="table-number">Table 2</span>:
      编辑连接
    </div>

    | 快捷键  | 说明      |
    |------|---------|
    | C-c C-l | 编辑连接(小写L) |
    | C-c C-o | 打开连接  |

    [baidu](http://www.baidu.com)

-    显示图片

    使用如下标签插入图片,使用C-c C-x C-v inline方式显示图片,或者使用C-c C-o打开连接件
    ![zhilongshanren](https://zilongshanren.com/ox-hugo/category.png)