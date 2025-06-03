# Muse to QMarkdown 翻译规则

将 `.muse` 文件翻译为 `.qmd` 文件


## 大标题 `#title`

大标题翻 `#title` 译为一级标题 `#`

- `"^#title\s+(.+)$" => "# $1"`

```md
#title 高精度运算
# 高精度运算
```

## `<contents>`

删除 `<contents>` 标签

- `"<contents>" => ""`

## 注释 `;`

忽略注释标记 `;`

- `"^; " => ""`

## `<cite>` 标签

成对的 `<cite>` 标签转化为 qmd 引用

- `"<cite>" => "[@"`
- `"</cite>" => "]"`

```md
<cite>shuzhifenxi</cite>
[@shuzhifenxi]
<cite>fkq03</cite>,<cite>nlzdss00</cite>
[@fkq03],[@nlzdss00]
<cite>riesel</cite>，<cite>cohen</cite>和<cite>pei02</cite>
[@riesel]，[@cohen] 和 [@pei02]
```

## `<index>` 标签

`<index>` 改用代码标记并加上 index 索引。

示例：

1. 仅标签，无属性。
    使用内容作为索引。
    ```"<index>(.+)</index>" => "`$1`\index{$1}"```

    ```md
    <index>高精度运算</index>
    `高精度运算`\index{高精度运算}
    ```

2. 如果闭合标签没有内容，则仅加上 index 索引，不显示文本。
3. 有 `name` 属性。保留标签内容作为文本，`name` 属性的值作为索引名。
    ```"<index name="(.+)">(.+)</index>" => "`$2`\index{$1}"```

    ```md
    <index name="Fermat检测">Fermat合性检测</index>
    `Fermat合性检测`\index{Fermat检测}
    <index name="Lehmer $N-1$型检测"></index>
    \index{Lehmer $N-1$型检测}
    ```

4. 有 `name` 属性和 `sub` 属性。
    保留标签内容作为文本，`name` 属性和 `sub` 的值作为索引名，即添加两个不同的 index。
    ```"<index name="(.+)" sub="(.+)">(.+)</index>" => "`$3`\index{$1!$2}"```
  
    ```md
    <index name="伪素数" sub="Camichael数">Camichael数</index>
    `Camichael数`\index{伪素数!Camichael数}
    <index name="Fermat小定理" sub="Lehmer的逆定理!放宽版本"></index>
    \index{Fermat小定理!Lehmer的逆定理!放宽版本}
    <index name="Fermat小定理" sub="二次域中的"></index>
    \index{Fermat小定理!二次域中的}
    ```

## 多级标题 `*`

多级标题增加一级，映射到 md 标题

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

## `<latex>` 标签

`<latex>` 标签改为 md 公式

- `"<latex>" => "$$"`
- `"</latex>" => "$$"`

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
- 闭合标签 `</theorem>` 替换为环境结束符 `:::`

示例：

1. 仅标签，无属性。
    - `"<theorem>" => "::: {.theorem}"`
    - `"</theorem>" => ":::"`

2. 有 `name` 属性。
    `name` 字段作为标题

    ```md
    <theorem  name="Davenport">
    ::: {.theorem}
    ## Davenport
    ```

3. 有 `label` 属性
    这里统一了 `label` 的前缀，改为 `thm-multiply1`

    ```md
    <theorem label="th:multiply1">
    ::: {#thm-multiply1  .theorem}
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

    ```md
    <definition name="原根">
    ::: {.definition}
    ## 原根
    ```

3. 有 `label` 属性
    这里把 `label` 改为 `def-conditioning`

    ```md
    <definition label="conditioning" name="矩阵的条件数">
    ::: {#def-conditioning  .definition}
    ## 矩阵的条件数
    ```

4. 有 `label` 属性，`label` 带有前缀
    这里把 `label` 去掉前缀，改为 `def-machin`

    ```md
    <definition name="Machin型公式" label="de:machin">
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

```md
<problem label="pr:karatsuba">
::: {#def-pr:karatsuba  .definition}
## 问题
```

### algorithm

- `<algorithm>` 改为改用 `definition` 环境
  - 如果有 `label` 属性，则将其值加上前缀 `def-`，作为新的 label
  - 如果有 `name` 属性，加上 `算法：` 后作为标题；
  - 如果没有 `name` 属性，则使用 `算法` 作为标题。
- 闭合标签 `</algorithm>` 替换为环境结束符 `:::`

```md
<algorithm  name="在$B$进制下除以$B'$" label="al:conversion1">
::: {#def-al:conversion1  .definition}
## 算法：在 $B$ 进制下除以 $B'$
```
