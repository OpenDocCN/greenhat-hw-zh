# 第二十章：D

Windows 命令行解释器

![](img/chapterart.png)

微软的 MASM（微软汇编语言工具）主要是通过 Windows 命令行使用的工具。因此，为了正确使用 MASM（至少是本书中的所有示例），你需要熟悉 Windows 命令行解释器（CLI）。

附录 C 显示了如何设置 Windows CLI 以便你能够使用它。本附录简要描述了一些你将在 CLI 中使用的常见命令。

## D.1 命令行语法

一个基本的 Windows CLI 命令的格式是

```
`command options` 
```

其中 `command` 是一个内置 CLI 命令、一个磁盘上的可执行程序（通常带有 *.exe* 文件后缀）或一个批处理文件名（带有 *.bat* 后缀），`options` 是该命令的零个或多个选项，选项是特定于命令的。

本书中最常见的命令行可执行程序示例可能是 *ml64.exe* 程序（MASM 汇编器）。微软的链接器 (*link.exe*)、库文件管理器 (*lib.exe*)、nmake (*nmake.exe*) 和 MSVC 编译器 (*cl.exe*) 也是你可能从命令行运行的可执行程序示例。

本书中的所有示例程序也是你可以从命令行运行的命令。例如，下面的命令执行 *build.bat* 批处理文件，以构建 *listing2-1.exe* 可执行文件（来自第二章）：

```
build listing2-1
```

在构建 *listing2-1.exe* 可执行文件后，你可以从命令行运行它。以下是命令及其产生的输出：

```
C:\>**listing2-1**
Calling Listing 2-1:
i=1, converted to hex=1
j=123, converted to hex=7b
k=456789, converted to hex=6f855
Listing 2-1 terminated
```

*listing2-1.exe* 可执行文件不支持任何命令行选项。如果你在命令行中输入 `listing2-1` 命令后面跟着任何内容，*listing2-1.exe* 程序将忽略这些文本。

虽然大多数选项是特定于命令的，但你可以将某些命令行选项应用于你从命令行运行的大多数程序：特别是 *I/O 重定向*。许多控制台应用程序将数据写入 *标准输出设备*（控制台窗口）。例如，本书中所有的 `print` 和 `printf` 函数调用都会将数据写入标准输出设备。通常，所有发送到标准输出设备的输出都会作为文本显示在命令行（控制台）窗口中。

然而，你可以通过使用 *输出重定向选项* 告诉 Windows 将数据发送到一个文件（甚至是另一个设备）。输出重定向选项的格式是

```
`command options` >`filename more_options`
```

其中 `command` 是命令名称，`options` 和 `more_options` 是零个或多个命令行选项（不包含输出重定向选项），`filename` 是你希望将 `command` 的输出发送到的文件名。请考虑以下命令行：

```
listing2-1 >listing2-1.txt
```

执行此命令不会产生任何显示输出。然而，你会发现该命令在磁盘上创建了一个新的文本文件。该文本文件将包含 *listing2-1.exe* 程序的输出（如前所示）。

Windows CLI 还支持使用以下语法进行*标准输入重定向*。

```
`command options` <`filename more_options`
```

其中，`command`是命令名称，`options`和`more_options`是零个或多个命令行选项（不包含输入重定向选项），`filename`是`command`将从中读取输入的文件名。

输入重定向使得一个通常从用户（键盘，即标准输入设备）读取数据的程序，转而从文本文件中读取数据。例如，假设你之前执行了`listing2-1`命令，并将输出重定向到*listing2-1.txt*输出文件。考虑以下命令（来自第一章），该命令从用户读取一行文本（在这个特定的例子中，我输入了`hello`来响应程序的输入请求）：

```
C:\>**build listing1-8**
C:\>**echo off**
 Assembling: listing1-8.asm
c.cpp
C:\>**listing1-8**
Calling Listing 1-8:
Enter a string: **hello**
User entered: 'hello'
Listing 1-8 terminated
```

现在考虑以下命令：

```
C:\>**listing1-8 <listing2-1.txt**
Calling Listing 1-8:
Enter a string: User entered: 'Calling Listing 2-1:'
Listing 1-8 terminated
```

在这个例子中，输入从先前执行的*listing2-1.exe*生成的*listing2-1.txt*文件重定向。*listing1-8.exe*程序将该文件的第一行作为输入读取（而不是从键盘读取一行文本）。程序不会回显从文件读取的文本（包括换行符）；这就是为什么`User entered: 'Calling Listing 2-1:'`文本出现在与`Enter a string:`提示相同的一行上的原因。当实际从键盘读取数据时，系统会将数据回显到显示屏上（包括换行符）。而在从文件重定向输入时则不会发生这种情况。

文件包含多行文本。然而，*listing1-8.exe*只读取一行文本，因此它忽略了*listing2-1.txt*文件中的其余行。

你可以在同一个命令中同时重定向标准输入和标准输出。考虑以下情况：

```
C:\>listing1-8 <listing2-1.txt >listing1-8.txt
```

这段代码从*listing2-1.txt*文件读取数据，并将所有输出发送到*listing1-8.txt*文件。

当将程序的输出重定向到文本文件时，如果输出文件已经存在，Windows 会在写入标准输出文本之前删除该文件。你还可以通过使用以下输出重定向语法（使用两个大于符号）指示 Windows 将命令的输出附加到现有文件中：

```
`command options` >>`filename more_options`
```

除了重定向选项外，命令行选项通常是文件名（例如，`ml64 mySource.asm`）或控制命令行为的选项（例如，ml64 的`/c`或`/Fl`命令行选项，在本书中将多次出现）。按照惯例，大多数 Windows 命令行界面（CLI）命令在实际选项前使用斜杠字符（`/`）作为前缀（而不是文件名）。这是一个惯例，而非硬性要求。

一些命令，例如，使用 Unix 约定的破折号或连字符（`-`）来代替（或附加在）斜杠字符。这实际上是一个特定应用程序的选择。请查阅你正在使用的特定程序的文档以了解详细信息。所有内置的 CLI 命令以及大多数微软的 CLI 程序都使用斜杠字符来指定选项。

## D.2 目录名称和驱动器字母

许多命令接受或要求文件或目录的路径名作为命令行选项。路径名由两个主要部分组成：驱动器字母和目录或文件的路径名。驱动器字母是一个字母（A 到 Z），后跟一个冒号；例如：

```
A: B: C: etc.
```

驱动器字母不区分大小写。`A:` 等同于命令行中的 `a:`。Windows 为软盘驱动器保留了 A: 和 B: 字母。由于现代机器上不常见软盘驱动器，所以你通常不会使用这些驱动器字母。然而，如果你有一台非常旧的机器……

C: 是启动驱动器的默认驱动器字母。如果你的机器只有一个硬盘（或固态硬盘），Windows 很可能会将 C: 关联到该驱动器。书中出现的所有示例假设你正在使用 C: 驱动器（尽管这并非强制要求）。

如果你有多个驱动器（无论是多个物理驱动器单元，还是你将硬盘划分为多个逻辑驱动器），Windows 通常会将连续的驱动器字母（D:, E: 等）与这些附加驱动器关联。如果你愿意，可以重新分配驱动器字母，因此无法保证所有驱动器字母在字母表中是连续的。

你可以通过在命令行中输入字母和冒号来切换默认驱动器。例如，

```
D:
```

会将默认驱动器切换到 D:，前提是该驱动器存在。如果该驱动器不存在，Windows 会报错并表示无法找到指定的驱动器，同时不会更改默认驱动器。

通常情况下（你可以更改此设置），Windows 会将当前的驱动器字母显示为命令行提示符的一部分（默认情况下，它也会显示默认的路径名）。例如，典型的 Windows 命令行提示符看起来像这样：

```
C:\>
```

命令提示符中出现的 `\` 字符表示当前（默认）目录。在这种情况下，单独的 `\` 表示 C: 驱动器上的根（或主）目录。如果当前目录是其他目录，Windows 会在驱动器字母后列出该目录。例如，如果当前目录是 `\WINDOWS`，CLI 会将以下内容显示为命令行提示符：

```
C:\WINDOWS>
```

正如你可能知道的，Windows 有一个层次化的文件系统，允许在（子）目录内部创建子目录。反斜杠字符用于分隔完整路径名中的目录名称。你常常会在 Windows 中看到两种路径形式：完整路径名和部分路径名*。*

*完整路径名*以反斜杠（`\`）字符开始，并从根目录开始。*部分路径名*不以反斜杠开始，路径从当前（默认）目录开始（部分路径名中的第一个子目录必须出现在当前默认子目录中）。

空格通常用于分隔命令行上的选项。如果路径名中包含空格，你必须用引号将整个路径名括起来；例如：

```
"\This\Path name\has\a\space"
```

CLI 支持路径名中的一对*通配符*字符。星号字符（`*`）将匹配零个或多个字符。问号字符（`?`）将匹配零个或一个字符。

命令必须明确支持通配符字符；Windows CLI 命令支持通配符选项，大多数 Microsoft 工具也支持（例如，*ml64.exe*）。然而，并非所有可执行文件都支持文件名中的通配符。通配符字符可以用于目录名和文件名，但它们不会替代路径名中的反斜杠字符（`\`）。

## D.3 一些有用的内建命令

Windows CLI 包含许多内建命令（这些命令是 *cmd.exe* 程序的一部分，无需单独的 *.exe* 或 *.bat* 文件）。内建命令太多，无法一一列举（而且你也不会使用大部分命令）；因此，本节只介绍最常用的一小部分命令。

### D.3.1 cd 和 chdir 命令

`cd`（*change directory*）命令将默认目录切换到你作为命令行选项指定的目录。请注意，`chdir` 是 `cd` 的同义词。其语法是：

```
cd `directory_name`
```

其中，`directory_name` 是新目录的完整或部分路径名。例如：

```
cd \masm32\examples
```

即使你在路径名中指定了驱动器字母，`cd` 命令通常不会改变默认的驱动器字母。例如，如果当前驱动器字母是 D:，则以下命令不会直接改变默认的驱动器字母和路径名：

```
D:\>cd C:\masm32\examples
D:\>
```

请注意，`cd` 命令执行后，命令提示符仍然是 `D:\>`。然而，如果你切换到 C: 驱动器（使用 `C:` 命令），Windows 会根据之前的命令设置默认目录：

```
D:>C:
C:\masm32\examples>
```

如你所见，默认目录与驱动器字母相关联（每个驱动器字母都有自己的默认目录）。

如果你想用 `cd` 命令同时切换驱动器字母和路径名，只需在路径名前添加 `/d` 选项：

```
D:\>cd /d C:\masm32\examples
C:\masm32\examples
```

切记，如果路径名中包含空格，使用 `cd` 命令时必须将路径名用引号括起来：

```
cd /d "C:\program files"
```

以下内容显示有关 `cd` 命令的帮助信息：

```
cd /?
```

如果你单独使用 `cd` 命令（没有命令行参数），此命令会显示当前（默认）路径名。

### D.3.2 cls 命令

`cls` 命令清除屏幕（至少是命令窗口）。当你在编译之前希望清屏，并且只想看到与该次编译相关的消息时，这非常有用。

### D.3.3 `copy` 命令

`copy` 命令将一个或多个文件复制到其他位置。通常，使用此命令可以在当前目录中创建文件的备份副本，或将文件复制到其他子目录中。该命令的语法如下：

```
copy `source_filename destination_filename`
```

该命令复制由 `source_filename` 指定的文件，并将其副本命名为 `destination_filename`。这两个名称可以是完整的或部分的路径名。

`copy` 命令支持多个命令行选项（除了源文件和目标文件名）。你可能不会经常使用这些选项。要了解更多详细信息，可以执行以下帮助命令：

```
copy /?
```

### D.3.4 `date` 命令

`date` 命令本身会显示当前的系统日期，并提示你输入一个新的日期（该操作将永久设置系统日期——使用时请小心！）。若使用 `/t` 命令行选项，此命令只会显示日期，而不会要求你更改日期。示例如下：

```
C:\>date /t
Sat 02/23/2019
```

像往常一样，`date /?` 会显示此命令的帮助信息。

### D.3.5 `del`（`erase`）命令

`del` 命令（`erase` 是 `del` 的同义词）将删除你指定的文件（或文件们），这些文件是作为命令行选项提供的。其语法为：

```
del `options files_to_delete`
```

其中，`options` 是以斜杠开头的命令行选项，`files_to_delete` 是要删除的文件名（路径名）列表，文件名之间用空格或逗号分隔。此命令支持通配符字符；例如，以下命令会删除当前目录中所有的 `*.obj` 文件：

```
del *.obj
```

不用说，使用此命令时要非常小心，尤其是在使用通配符字符时。例如，考虑以下命令（这可能是一个拼写错误）：

```
del * .obj
```

该命令删除当前目录中的所有文件，然后尝试删除名为 `*.obj` 的文件（该文件在命令删除子目录中的所有文件后将不存在）。

此命令与一些有用的命令行选项相关联。使用 `/?` 选项了解它们：

```
C:\>del /?
```

### D.3.6 `dir` 命令

`dir`（*目录*）命令是最有用的命令行工具之一。它显示目录列表（即目录中的文件列表）。

如果没有任何命令行选项，该命令将显示当前目录下的所有文件。如果作为参数仅提供一个驱动器字母（加冒号），该命令将显示指定驱动器上默认目录中的所有文件。如果提供了指向子目录的路径名，该命令将显示指定目录中的所有文件。如果提供了指向单个文件名的路径名，该命令将显示该文件的目录信息。

像往常一样，该命令支持多个以斜杠字符开头的命令行选项。使用 `dir /?` 可以查看此命令的帮助信息。

### D.3.7 `more` 命令

`more` 命令一次显示文本文件中的一屏内容。显示完一屏内容后，程序会等待用户按下回车或空格键。按下空格键会显示下一屏的内容；按下回车键会显示下一行内容。按下 Q 键则终止程序。

`more` 命令在命令行中需要指定一个或多个文件名作为参数。如果您指定了两个或更多文件，`more` 将按顺序显示输出。`more` 命令还支持多个命令行选项。您可以使用以下命令了解它们：

```
more /?
```

### D.3.8 `move` 命令

`move` 命令将文件从一个位置移动到另一个位置（可能在移动的过程中重命名文件）。它类似于 `copy`，但 `move` 会在移动后删除源位置的文件。此命令的基本语法如下：

```
move `original_file new_file`
```

像往常一样，`/?` 命令行选项提供该命令的帮助信息。

### D.3.9 `ren` 和 `rename` 命令

`ren` 命令（`rename` 是其同义词）用于更改文件名。其语法为

```
ren `original_filename new_filename`
```

其中（显然）`original_filename` 是您希望更改的旧文件名，`new_filename` 是您希望使用的新文件名。新旧文件必须位于同一目录中。如果您希望在重命名文件的同时将其移动到新目录，请使用 `move` 命令。

### D.3.10 `rd` 和 `rmdir` 命令

`rd` 命令（`rmdir` 是其同义词）用于删除（移除）一个目录。在使用此命令之前，目录必须为空（尽管 `/s` 选项可以覆盖此要求）。此命令的基本语法是

```
rd `directory_path`
```

其中 `directory_path` 是您希望删除的目录的路径。使用 `rd /?` 命令获取帮助信息。

### D.3.11 `time` 命令

如果没有参数，`time` 命令会显示当前系统时间并提示您更改时间。使用 `/t` 命令行参数，`time` 只会显示当前时间。使用 `/?` 显示该命令的帮助信息。

## D.4 获取更多信息

本附录仅提供了 Windows 命令行解释器的最基础介绍——足以使用 MASM 编译和运行汇编语言程序。CLI 支持数十个内置命令（可能超过一百个）。了解这些命令的一个地方是 [`docs.microsoft.com/en-us/windows-server/administration/windows-commands/cmd/.`](https://docs.microsoft.com/en-us/windows-server/administration/windows-commands/cmd/.)
