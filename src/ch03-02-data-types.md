## 数据类型

Rust 的每个值都有确切的**数据类型**（*data type*），该类型告诉 Rust 数据是被指定成哪类数据，从而让 Rust 知道如何使用该数据。在本节中，我们将介绍两种数据类型：标量类型和复合类型。

请记住 Rust 是一种**静态类型**（*statically typed*）的语言，这意味着它必须在编译期知道所有变量的类型。编译器通常可以根据值和使用方式推导出我们想要使用的类型。在类型可能是多种情况时，例如在第 2 章的[“比较猜测的数字和秘密数字”][comparing-the-guess-to-the-secret-number]<!-- ignore -->中当我们使用 `parse` 将`String` 转换成数值类型时，我们必须加上一个类型标注，如下所示：

```rust
let guess: u32 = "42".parse().expect("Not a number!");
```

如果我们在这里不添加类型标注的话，Rust 将显示以下错误，意思是编译器需要我们提供更多信息来确定我们到底想用什么类型：

```console
{{#include ../listings/ch03-common-programming-concepts/output-only-01-no-type-annotations/output.txt}}
```

对于其他数据类型，你将看到不同的类型标注。

### 标量类型

**标量**（*scalar*）类型表示单个值。Rust 有 4 个标量类型：整型、浮点型、布尔型和字符。你可能从其他语言了解过这些类型。下面我们深入了解它们在 Rust 中的用法。

#### 整数类型

**整数**（*integer*）是没有是没有小数部分的数字。我们在第 2 章使用过一个整数类型，即 `u32` 类型。此类型声明表明了与其相关的值应为 32 位系统的无符号整型（`i` 是英文单词 *integer* 的首字母，与之相反的是 `u`，代表无符号 `unsigned` 类型）。表 3-1 显示了 Rust 中的内置的整数类型。在有符号和和无符号的列中（例如 *i16*）的每个定义形式都可用于声明整数类型。

<span class="caption">表 3-1: Rust 中的整数类型</span>

| 长度       | 有符号类型 | 无符号类型 |
|------------|---------|---------|
| 8 位       | `i8`    | `u8`    |
| 16 位      | `i16`   | `u16`   |
| 32 位      | `i32`   | `u32`   |
| 64 位      | `i64`   | `u64`   |
| 128-位     | `i128`  | `u128`  |
| 视架构而定   | `isize` | `usize` |

每个定义形式都可以是有符号类型或无符号类型，且带有一个显式的大小。**有符号**和**无符号**表示数字能否取负数或是只能取正数；也就是说，数字是否需要带一个符号（有符号类型），或数字只能表示正数故不需要包括符号（无符号类型）。就像在纸上写数字一样：当要强调符号时，数字前面可以带上正号或负号；然而，当很明显确定数字为正数时，就不需要加上正号了。有符号数字以[二进制补码](https://en.wikipedia.org/wiki/Two%27s_complement)形式存储。

每个有符号类型规定的数字范围是  -(2<sup>n - 1</sup>) ~ 2<sup>n -
1</sup> - 1，其中 `n` 是该定义形式的位长度。所以 `i8` 可存储数字范围是 -(2<sup>7</sup>) ~ 2<sup>7</sup> - 1，即 -128 ~ 127。无符号类型可以存储的数字范围是 0 ~ 2<sup>n</sup> - 1，所以 `u8` 能够存储的数字为 0 ~ 2<sup>8</sup> - 1，即 0 ~ 255。

此外，`isize` 和 `usize` 类型取决于程序运行的计算机类型：64 位（如果使用 64 位架构系统）和 32 位（如果使用 32 位架构系统）。

整型的字面量可以可以写成下表 3-2 中任意一种。注意，除了字节字面量之外的所有的数字字面量都允许使用类型后缀，例如 `57u8`，还有可以使用 `_` 作为可视分隔符，如 `1_000`。

<span class="caption">表 3-2: Rust 的整型字面量</span>

| 数字字面量         | 示例          |
|------------------|---------------|
| 十进制            | `98_222`      |
| 十六进制          | `0xff`        |
| 八进制            | `0o77`        |
| 二进制            | `0b1111_0000` |
| 字节 (仅限于 `u8`) | `b'A'`        |

那么该使用哪种类型的整型呢？如果不确定，Rust 的默认值通常是个不错的选择，整型默认是 `i32`：这通常是最快的，即便在 64 位系统上也是。`isize` 和 `usize` 的主要应用场景是用作某些集合的索引。

> ##### 整型溢出
>
> 比方说有一个 `u8` ，它可以存放从 0 到 255 的值。那么当你将其修改为范围之外的值，比如 256，则会发生**整型溢出**（*integer overflow*）。关于这一行为 Rust 有一些有趣的规则。当在 debug 模式编译时，Rust 会检查整型溢出若存在这些问题则使程序在编译时 *panic*。Rust 使用这个术语来表明程序因错误而退出。第 9 章 [“`panic!` 与不可恢复的错误”][unrecoverable-errors-with-panic]<!-- ignore -->会详细介绍 panic。
>
> 在当使用 `--release` 参数进行 release 模式构建时，Rust **不**检测溢出。相反当检测到整型溢出时，Rust 会进行一种被称为二进制补码包裹（*two’s complement wrapping*）的操作。简而言之，大于该类型最大值的数值会被“包裹”成该类型能够支持的对应数字的最小值。比如在 `u8` 的情况下，256 变成 0，257 变成 1，依此类推。程序不会 panic，但是该变量的值可能不是你期望的值。依赖整型溢出包裹的行为不是一种正确的做法。
>
> 要显式处理溢出的可能性，可以使用标准库针对原始数字类型提供的以下的一系列方法：
>
> - 使用 `wrapping_*` 方法在所有模式下进行包裹，例如 `wrapping_add`
> - 如果使用 `checked_*` 方法时发生溢出，则返回 `None` 值
> - 使用 `overflowing_*` 方法返回该值和一个指示是否存在溢出的布尔值
> - 使用 `saturating_*` 方法使值达到最小值或最大值

#### 浮点类型

**浮点类型数字** 是带有小数点的数字，在 Rust 中浮点类型数字也有两种基本类型： `f32` 和 `f64`，分别为 32 位和 64 位大小。默认浮点类型是 `f64`，因为在现代的 CPU 中它的速度与 `f32` 几乎相同，但精度更高。

下面是一个演示浮点数的示例：

<span class="filename">文件名：src/main.rs</span>

```rust
{{#rustdoc_include ../listings/ch03-common-programming-concepts/no-listing-06-floating-point/src/main.rs}}
```

浮点数根据 IEEE-754 标准表示。`f32` 类型是单精度浮点型，`f64` 为双精度。

#### 数字运算

Rust 支持所有数字类型的基本数学运算：加法、减法、乘法、除法和取模运算。下面代码演示了各使用一条 `let` 语句来说明相应运算的用法：

<span class="filename">文件名：src/main.rs</span>

```rust
{{#rustdoc_include ../listings/ch03-common-programming-concepts/no-listing-07-numeric-operations/src/main.rs}}
```

这些语句中的每个表达式都使用了数学运算符，并且计算结果为一个值，然后绑定到一个变量上。[附录 B][appendix_b]<!-- ignore -->  给出了 Rust 提供的所有运算符的列表。

#### 布尔类型

和大多数编程语言一样，Rust 中的布尔类型也有两个可能的值：`true` 和 `false`。布尔值的大小为 1 个字节。Rust 中的布尔类型使用 `bool` 指定。例如：

<span class="filename">文件名：src/main.rs</span>

```rust
{{#rustdoc_include ../listings/ch03-common-programming-concepts/no-listing-08-boolean/src/main.rs}}
```

用到布尔值的地方主要是条件语句，如 `if` 语句。我们将会在[“控制流”][control-flow]<!-- ignore -->章节中介绍 `if` 在 Rust 中的用法。

#### 字符类型

到目前为止，我们只在用数字，不过 Rust 同样也支持字母。Rust 的 `char` （字符）类型是该语言最原始的字母类型，下面代码展示了使用它的一种方式（注意，`char` 字面量是用单引号括起来，而字符串字面量是用双引号扩起来）：

<span class="filename">文件名：src/main.rs</span>

```rust
{{#rustdoc_include ../listings/ch03-common-programming-concepts/no-listing-09-char/src/main.rs}}
```

Rust 的字符类型表示的是一个 Unicode 值，这意味着它可以表示的不仅仅是 ASCII。标音字母，中文/日文/韩文的表意文字，emoji，还有零宽空格(zero width space)在 Rust 中都是合法的字符类型。Unicode 值的范围从 `U+0000`~`U+D7FF` 和 `U+E000`~`U+10FFFF`。不过“字符”并不是 Unicode 中的一个概念，所以人在直觉上对“字符”的理解和 Rust 的字符概念并不一致。我们将在第 8 章[“使用字符串存储 UTF-8 编码的文本”][strings]<!-- ignore -->中详细讨论这个主题。

### 复合类型

**复合类型**（*compound type*）可以将其他类型的多个值合在一块组成一个类型。Rust 有两种基本的复合类型：元组（tuple）和数组（array）。

#### 元组类型

元组是将多种类型的多个值组合到一个复合类型中的一种基本方式。元组的长度是固定的：声明后，它们就无法增长或缩小。

我们通过在括号内写入以逗号分隔的值列表来创建一个元组。元组中的每个位置都有一个类型，并且元组中不同值的类型不要求是相同的。我们在下面示例中添加了可选的类型标注：

<span class="filename">文件名：src/main.rs</span>

```rust
{{#rustdoc_include ../listings/ch03-common-programming-concepts/no-listing-10-tuples/src/main.rs}}
```

变量 `tup` 绑定到整个元组，因为元组被认作是单个复合元素。 想从元组中获取个别值，我们可以使用模式匹配来解构（destructure）元组的一个值，如下所示：

<span class="filename">文件名：src/main.rs</span>

```rust
{{#rustdoc_include ../listings/ch03-common-programming-concepts/no-listing-11-destructuring-tuples/src/main.rs}}
```

该程序首先创建一个元组并将其绑定到变量 `tup` 上。 然后它借助 `let` 来使用一个模式匹配 `tup`，并将它分解成三个单独的变量 `x`、`y` 和 `z`。 这过程称为**解构**（*destructuring*），因为它将单个元组分为三部分。 最后，程序打印出 `y` 值，为 `6.4`。

除了通过模式匹配进行解构外，我们还可以使用一个句点（`.`）连上要访问的值的索引来直接访问元组元素。例如：

<span class="filename">文件名：src/main.rs</span>

```rust
{{#rustdoc_include ../listings/ch03-common-programming-concepts/no-listing-12-tuple-indexing/src/main.rs}}
```

该程序创建一个元组 `x`，然后通过使用它们的索引为每个元素创建新的变量。和大多数编程语言一样，元组中的第一个索引为 0。

#### 数组

将多个值的组合在一起另一种方式就是使用**数组**（*array*）。与元组不同，数组的每个元素必须具有相同的类型。Rust 中的数组与某些其他语言中的数组不同，因为 Rust 中的数组具有固定长度，跟元组一样。

在 Rust 中，数组内的值将以逗号分隔的列表形式写入方括号内的：

<span class="filename">文件名：src/main.rs</span>

```rust
{{#rustdoc_include ../listings/ch03-common-programming-concepts/no-listing-13-arrays/src/main.rs}}
```

当你希望将数据分配到栈（stack）而不是堆（heap）时（我们将在第 4 章中进一步讨论栈和堆），或者当你希望确保始终具有固定数量的元素时，数组特别有用。但它们不像 vector （译注：中文字面翻译为“向量”，在 Rust 中意义为“动态数组，可变数组”）类型那么灵活。vector 类型类似于标准库中提供的集合类型，其的大小允许增长或缩小。如果您不确定是使用数组还是 vector，那就应该使用一个 vector：第 8 章更详细地讨论 vector。

举个例子，在需要知道一年中各个月份名称的程序中，您很可能希望使用的是数组而不是 vector。这样的程序不太可能需要添加或删除月份，所以可以使用数组，因为你知道它总是包含 12 个元素：

```rust
let months = ["January", "February", "March", "April", "May", "June", "July",
              "August", "September", "October", "November", "December"];
```

可以像这样编写数组的类型：在方括号中包含每个元素的类型，后跟分号，再后跟数组元素的数量。

```rust
let a: [i32; 5] = [1, 2, 3, 4, 5];
```

这里，`i32` 是每个元素的类型。分号之后，数字 `5` 表明该数组包含五个元素。

以这种方式编写数组的类型看起来类似于初始化数组的另一种语法：如果要为每个元素创建包含相同值的数组，可以指定初始值，后跟分号，然后在方括号中指定数组的长度，如下所示：

```rust
let a = [3; 5];
```

变量名为 `a` 的数组将包含 `5` 个元素，这些元素的值初始化为 `3`。这种写法与 `let a = [3, 3, 3, 3, 3];` 效果相同，但更简洁。

##### 访问数组元素

数组是在栈上分配的单个内存块。可以使用索引访问数组的元素，如下所示：

<span class="filename">文件名：src/main.rs</span>

```rust
{{#rustdoc_include ../listings/ch03-common-programming-concepts/no-listing-14-array-indexing/src/main.rs}}
```

在这个例子中，名为 `first` 的变量将获得值 `1`，因为它是数组中索引 `[0]` 处的值。名为 `second` 的变量将从数组中的索引 `[1]` 中获取得 `2`。

##### 无效的数组元素访问

如果尝试访问超出数组末尾的数组元素，会发生什么？ 假如你将示例更改为以下内容，使用类似于第 2 章猜数字游戏的代码那样从用户获取数组索引：

<span class="filename">文件名：src/main.rs</span>

```rust,ignore,panics
{{#rustdoc_include ../listings/ch03-common-programming-concepts/no-listing-15-invalid-array-access/src/main.rs}}
```

此代码编译成功。如果使用 `cargo run` 来运行此代码并输入 0、1、2、3 或 4，则程序将打印数组对应索引的值。如果您输入的是超出数组末尾的数字，例如10，则会看到类似以下的输出：

<!-- manual-regeneration
cd listings/ch03-common-programming-concepts/no-listing-15-invalid-array-access
cargo run
10
-->

```console
thread 'main' panicked at 'index out of bounds: the len is 5 but the index is 10', src/main.rs:19:19
note: run with `RUST_BACKTRACE=1` environment variable to display a backtrace
```

该程序在索引操作中使用无效值时导致**运行时**（*runtime*）错误。程序退出并显示错误消息，未执行后面的 `println!` 语句。当你尝试使用索引访问元素时，Rust 将检查你指定的索引是否小于数组长度。如果索引大于或等于数组长度，Rust会出现 `panic`。这种检查必须在运行时进行，尤其是在这种情况下，因为编译器可能无法知道用户之后运行代码时将输入什么值。

这是 Rust 在实践中安全原则的第一个例子。在很多低级语言中，并不进行这种检查，而且在你使用不正确的索引时，可以访问无效的内存。Rust 通过立即退出来的方式防止这种错误，而不是允许内存访问并继续运行程序。 第 9 章将进一步讨论 Rust 的错误处理。
