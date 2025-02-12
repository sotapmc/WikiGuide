# 指令格式

在编写 Wiki 内容时，可能会用到一些指令示例。为了避免叙述的混乱，我们需要先约定好指令的格式。在编写时，按照格式进行编写，阅读时一旦掌握规律便可以轻松理解。

例如，在正常情况下，玩家并不会系统性地描述一个指令，因此我们引入了「指令结构」这一概念。有些指令的参数分为选填和必填，因此我们引入了「必填参数」和「可选参数」两个概念。等等，都是为了语言上的方便。

!> 这些指令格式目前来说并没有以一个确切的标准进行普及，因此也许你可能无法在 SoTap 领域外看到类似的格式，或者即使看到了含义也不大一样。

## 指令结构

通常一个指令可以长这样：

```minecraft
/command <required> [optional]
```

其中，`command` 叫做这个指令的**指令头**，代表着这个指令的用途；紧随其后，以空格为分隔的，叫做**参数（Argument）**。参数分为 **可选参数（Optional Argument）** 和 **必填参数（Required Argument）**。可选参数使用符号 `[参数名]` 表示，必填参数使用 `<参数名>` 表示。

举个例子：

```minecraft
/tpa <玩家名称>
```

在这里，我们都知道 `/tpa` 指令后面要跟玩家名称。那么如果没有跟呢？指令会执行失败。因此这个参数是必填的。

```minecraft
/sethome [家名称]
```

这里无论我们有没有填家的名称，均能够「设置一个家」，因此这个参数确实是可选的。

当然也有混合的

```minecraft
/lp <object-type> <controller> <action> [boolean] [context]
```

了解一下就可以，这是一个较为复杂的概括性指令。

?> 💡 选填参数遵循**就近转化原则**。当多个选择参数连在一起，且需要使用倒数第一个参数时，前面的所有参数将变为必填类型。例如 `/example [A] [B] [C]`，当要使用参数 C 时，前面两个 A、B 均变为必填。

### 选择参数

在后面的内容，你将会看到下面这种用法：

```minecraft
/res pset <领地名称> <玩家名称> <权限名称> <true|false>
```

你会发现，有一个必填参数里出现了很多个 `|`，分隔了不同的单词。这就是我们现在要讲的**选择参数（Choice Based Argument）**。

选择参数的符号是 `参数 A|参数 B`，需要注意的是，中间的横线两侧不能有空格，否则将导致无法获得高亮。你可以简单地将 `|` 理解为汉字「或」，这样意思就很明显了。

选择参数的存在几乎是必然的。因为如果我们仔细追究 `/res` 这个指令，那么我们可能会发现它也有一个**可怕的**选择参数：

```minecraft
/res <auto|check|confirm|contract|expand|info|kick|limits|listall|lset|market|rt|show|sublist|tool|tpconfirm|version|...> ...
```

这种情况的可读性很差，所以在后文中会对选择参数的每一个项进行讲解，请不要担心。

### 无参指令

顾名思义，就是没有参数的指令。常见的有 `/bal`、`/fly` 等。

?> 🤔 `/bal` 在 OP 权限下可以添加一个玩家名作为参数，因此它在此时并不是一个无参指令。

需要注意的是，`/home` 等并不是无参指令，但如果没有添加参数仍然可以执行成功，这是因为它有**默认行为（Default Behavior）**。

### 默认行为

默认行为是指令的一种自动补全。当你丢失某个选填指令时，指令会直接执行预设好的默认行为。举个例子，`/home` 指令的默认行为是传送到名为 `home` 的家。也就是说当你直接输入此指令而不加上任何参数时，你将会被传送到一个固定的地点。

`/sethome` 亦然，其默认行为是设置一个名为 `home` 的家。因此刚进服并不会指令的玩家也可以轻松使用这两个指令定点。但若要设置更多的家，则需要加上一个参数，也就是 `[家名称]`。

### 条件参数

!> 本条目内容因过于复杂，已很少使用。

**条件参数（Conditional Argument）**是指只有在一定条件下才有需要的参数，所以它本质上是一个可选参数。符号为 `[参数名称](条件)`。

比如，也是在后文中有一个例子：

```minecraft
/res lset <领地名称> <ignorelist|blacklist|Info> [物品](!Info)
```

「物品」就是一个条件参数，它的条件为 `!Info`，乍一看可能有点奇怪。别着急，我们会用表格来表达条件的含义：

|           符号          |              含义               |
| :----------------------:|:------------------------------: |
|         `&参数`         |   必须**含有**此参数时才必填    |
|         `!参数`         |   必须**没有**此参数时才必填    |
|   `&参数A, 参数B, ...`  |必须**含有所有**列出参数时才必填 |
|   `!参数A, 参数B, ...`  |必须**没有任何**列出参数时才必填 |
| `&参数A \| 参数B \| ...`|必须**含有其中一个**参数时才必填 |
| `!参数A \| 参数B \| ...`|必须**没有其中一个**参数时才必填 |
|           `?`           |            条件未知             |

那么上述指令的意思就是，当参数中不含有 Info 时，「物品」才是必填的。反过来说，「物品」在参数为 Info 的时候不需要填写。