# Muse to QMarkdown 翻译规则

将 `.muse` 文件翻译为 `.qmd` 文件

## 翻译规则

- 大标题翻 `#title` 译为一级标题 `#`
  - `"#title\s+(.+)$" => "# $1"`

```md
#title 高精度运算
# 高精度运算
```

- 删除 `<contents>` 标签

- 成对的 `<cite>` 标签转化为 qmd 引用

```md
<cite>shuzhifenxi</cite>
[@shuzhifenxi]
```

- `<index>` 改用代码标记+ tex 索引

```md
<index>高精度运算</index>
`高精度运算`\index{高精度运算}
```

- 多级标题增加一级，映射到 md 标题
  - `"^\*\s+(.+)$" => "## $1"`
  - `"^\*\*\s+(.+)$" => "### $1"`
  - `"^\*\*\*\s+(.+)$" => "#### $1"`
  - `"^\*\*\*\*\s+(.+)$" => "##### $1"`
  - 以此类推

```md
* 整数
## 整数
** 进制转换
### 进制转换
*** 商为一位数的除法
#### 商为一位数的除法
```

- `<latex>` 标签改为 md 公式

```md
<latex>
\begin{align*}
  F&=7x^7+2x^6-3x^5-3x^3+x+5,\\
G&=9x^5-3x^4-4x^2+7x+7,
\end{align*}
</latex>

$$
\begin{align*}
  F&=7x^7+2x^6-3x^5-3x^3+x+5,\\
G&=9x^5-3x^4-4x^2+7x+7,
\end{align*}
$$
```

## `##` 引用

Muse 引用 `##` 改为 qmd 链接

以下引用可以直接改为 qmd 链接：

```md
##th:multiply1
@thm-multiply1
##le:numbound
@lem-numbound
##cor:squarefree1
@cor-squarefree1
##de:machin
@def-machin
##re:division1
@rem-division1

##eq:norm
@eq:norm
##factor1
@factor1
```

以下引用，加上特定前缀后，改为 qmd 链接：

```md
##pr:karatsuba
@def-pr:karatsuba
##pr:toom
@def-pr:toom
##example:factorization1
@def-example:factorization1
##ex:2order
@def-ex:2order

##al:conversion1
@def-al:conversion1
##alg:leadingSmith
@def-alg:leadingSmith
```


## 特殊环境

以下环境直接对应到 qmd 的环境，示例参见 theorem/definition 环境。

- `<theorem>`
- `<lemma>`
- `<corollary>`
- `<definition>`
- `<example>`
- `<remark>`: 名称前缀去掉 `re:` 改为 `rem-`
- `<proof>`

以下环境对应到特定环境

- `<problem>` 改用 `definition` 环境
- `<algorithm>` 改用 `definition` 环境

### theorem

- `<theorem>` 对应使用 `.theorem` 环境。
  对应的 `label` 标签的前缀统一为 `thm-`，去掉原来的 `th:` 前缀；
  对应的 `name` 标签改为 md 标题，如果没有则留空。

输入 muse：

```muse
<theorem label="th:multiply1">
已知两个次数小于$n$的多项式的系数表示分别为$$A(x)=\sum_{k=0}^{n-1}a_kx^k,\quad B(x)=\sum_{k=0}^{n-1}b_kx^k,$$设乘积的系数表示为$C(x)=\sum\limits_{k=0}^{2n-2}c_kx^k$,那么$$c_k=\sum_{i+j=k}a_ib_j.$$
</theorem>
```

输出 qmd：

```md
::: {#thm-multiply1  .theorem}
已知两个次数小于$n$的多项式的系数表示分别为$$A(x)=\sum_{k=0}^{n-1}a_kx^k,\quad B(x)=\sum_{k=0}^{n-1}b_kx^k,$$设乘积的系数表示为$C(x)=\sum\limits_{k=0}^{2n-2}c_kx^k$,那么$$c_k=\sum_{i+j=k}a_ib_j.$$
:::
```

### definition

- `<definition>` 对应使用 `definition` 环境
  - 如果有 `label` 属性：将其值去掉可能的前缀 (`de:`, `def:`)，再加上前缀 `def-`，作为新的 label
  - 如果有 `name` 属性，将其值作为 md 标题；
- 闭合标签 `</definition>` 替换为环境结束符 `:::`

示例：

1. 仅标签，无属性。
  RE `"<definition>" => "::: {.definition}"`

2. 有 `name` 属性

    ```muse
    <definition name="原根">
    ```

    ```md
    ::: {.definition}
    ## 原根
    ```

3. 有 `label` 属性
    这里把 `label` 改为 `def-conditioning`

    ```muse
    <definition label="conditioning" name="矩阵的条件数">
    ```

    ```md
    ::: {#def-conditioning  .definition}
    ## 矩阵的条件数
    ```

4. 有 `label` 属性，`label` 带有前缀
    这里把 `label` 去掉前缀，改为 `def-machin`

    ```muse
    <definition name="Machin型公式" label="de:machin">
    ```

    ```md
    ::: {#def-machin  .definition}
    ## Machin 型公式
    ```

### problem

- `<problem>` 改为改用 `definition` 环境
  - 对应的 `label` 标签加上前缀 `def-`；
  - 对应的 `name` 字段，
    - 为空则使用 `问题` 作为标题。
    - 如果 `label` 以 `pr:` 开头，`name` 字段的内容加上 `问题：` 后作为 md 标题
    - 如果 `label` 以 `ex:`,`example:` 开头，`name` 字段的内容加上 `示例：` 后作为 md 标题

输入 muse：

```muse
<problem label="pr:karatsuba">
设$$A(x)=a_1x+a_0,\quad B(x)=b_1x+b_0,$$选取插值点组为$x_0=-1,x_1=0,x_2=\infty$,则$A(x),B(x)$的点值表示分别为$\{(-1,a_0-a_1),(0,a_0),(\infty,a_1)\}$,$\{(-1,b_0-b_1),(0,b_0),(\infty,b_1)\}$,如果$C(x)=A(x)\cdot B(x)$,那么$C(-1)=(a_0-a_1)\cdot(b_0-b_1)$,$C(0)=a_0\cdot b_0$,$C(\infty)=a_1\cdot b_1$,利用简单的多项式插值算法,可以计算出$C(x)$的系数表示$$(c_0,c_1,c_2)=(C(0),C(0)+C(\infty)-C(-1),C(\infty)),$$即$$C(x)=a_1b_1x^2+(a_0b_0+a_1b_1-(a_0-a_1)(b_0-b_1))x+a_0b_0.$$
</problem>
```

输出 qmd：

```md
::: {#def-pr:karatsuba  .definition}
## 问题

设$$A(x)=a_1x+a_0,\quad B(x)=b_1x+b_0,$$选取插值点组为$x_0=-1,x_1=0,x_2=\infty$,则$A(x),B(x)$的点值表示分别为$\{(-1,a_0-a_1),(0,a_0),(\infty,a_1)\}$,$\{(-1,b_0-b_1),(0,b_0),(\infty,b_1)\}$,如果$C(x)=A(x)\cdot B(x)$,那么$C(-1)=(a_0-a_1)\cdot(b_0-b_1)$,$C(0)=a_0\cdot b_0$,$C(\infty)=a_1\cdot b_1$,利用简单的多项式插值算法,可以计算出$C(x)$的系数表示$$(c_0,c_1,c_2)=(C(0),C(0)+C(\infty)-C(-1),C(\infty)),$$即$$C(x)=a_1b_1x^2+(a_0b_0+a_1b_1-(a_0-a_1)(b_0-b_1))x+a_0b_0.$$
:::
```

### algorithm

- `<algorithm>` 改为改用 `definition` 环境
  - 如果有 `label` 属性，则将其值加上前缀 `def-`，作为新的 label
  - 如果有 `name` 属性，加上 `算法：` 后作为标题；
  - 如果没有 `name` 属性，则使用 `算法` 作为标题。
- 闭合标签 `</algorithm>` 替换为环境结束符 `:::`

输入 muse：

```muse
<algorithm  name="在$B$进制下除以$B'$" label="al:conversion1">
```

输出 qmd：

```md
::: {#def-al:conversion1  .definition}
## 算法：在 $B$ 进制下除以 $B'$
```
