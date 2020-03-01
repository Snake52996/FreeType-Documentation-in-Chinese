### II. 字形轮廓

这一节将介绍字形图像, 又被称作轮廓, 的可缩放表述法是如何被FreeType以及客户端应用所使用的.

#### 1. 像素, 点与设备分辨率

在计算机图像程序的领域中, 一个给定的像素（无论是对于屏幕上还是对于打印机来说）的物理尺寸并不是一个精确的正方形是一个基本的假设. 通常, 输出设备在水平和垂直方向上均会表现出不同的分辨率, 因此必须在渲染文本的时候注意处理.

所以普遍用以两个数来表示的*dpi*（每英寸中的点数）来定义设备的这种特征. 例如一个解析度为300×600dpi的打印机在水平方向上每英寸有300个像素点, 而在垂直方向上每英寸的像素点则有600个. 一个典型的计算机显示器根据其尺寸而有所不同（例如同有1024×768个像素点的10英寸和25英寸的显示器显然并不具有同样的像素尺寸）, 同时也显然与图像显示模式的分辨率有关.

因此, 文本的大小一般是通过*点*给定的, 而不是依具体设备有所不同的像素. 点是一个物理单位, 在数字排印中一点等于七十二分之一英寸. 例如绝大多数使用拉丁字母的书籍在印刷时使用的主体部分文本大小在10至14点之间.

由于存在这种关系, 我们可以利用如下的公式来从以点为单位的文本大小计算得出以像素为单位的文本大小:`像素大小 = 点数大小 * 解析度 / 72`

其中的解析度是通过*dpi*表述的. 由于水平与垂直的解析度可能有所不同, 由一个点数定义大小的文本通常并不具有相同的像素宽度和高度.

*并不符合直觉的, 文本的像素大小和字符被显示或打印时的实际尺寸之间并不具有直接的关系. 这两个概念之间的关系要更加复杂一点, 而且取决于字体设计者在设计时的一些选择.  关于这一点, 在下个小节中会进行更细致的介绍（参见对EM方格的解释）*

#### 2. 向量表示法

轮廓的原始格式是一组被称为*轮廓线*的闭合路径. 每个轮廓线, 可以是线段或*贝塞尔弧*, 界定字形的一个外部或内部区域.

这些弧通过控制点定义, 既可以是二次（*圆锥贝塞尔*）也可以是三次（*三次方贝塞尔*）多项式, 取决于具体的字体格式. 其中圆锥贝塞尔在书面上也常被称为*二次贝塞尔*. 因此, FreeType将轮廓上的每一个点和一个用来确定它的类型（普通点或者控制点）的标记相关联. 所以缩放这些点将会缩放整个轮廓.

每个字形的原始轮廓点定位在一个不可分割单元组成的网格上. 这些点通常在字体文件中存储为16位整数表示的网格坐标, 其中网格的原点表示为(0, 0); 这些坐标因此被限制在-32768至32767的范围以内. （即使点的坐标在某些其它的格式中（如Type 1）可以为浮点数, 出于简单考虑, 我们仍然将我们的分析限制在整数值中）.

*这些网格的正方向总是像传统的数学二位平面一样, 即X轴从左指向右, Y轴从下指向上.*

在创建这些字形轮廓的时候, 字体设计者使用一种被称为*EM方格*的假想方格. 通常, EM方格可以被想象为用来将字符画在上面的平板. 这个方格的大小, 即其中的网格单元的数目, 由于以下两个原因而非常重要:

- 这个大小是将轮廓缩放到给定的文本尺寸时使用的参照大小. 例如, 一个大小为12pt(译注: pt表示点数大小)在300×300dpi下相当于12\*300/72=50像素. 若直接进行渲染, 这就是输出设备上应该出现的EM方格的大小. 换句话说, 使用如下公式从网格单位缩放到像素单位:

  `像素大小 = 点数大小 * 解析度 / 72`

  `像素坐标 = 网格坐标 * 像素大小 / EM方格大小`

  *ppem*（每EM方格中的像素）是关于像素大小的另一个缩写; 这个值也可以是分数. 不过并非所有地方都支持分数的ppem值.

- EM方格的大小越大, 字体的设计者就可以在坐标化轮廓时使用越大的分辨率. 举一个极端的例子, 若EM方格的大小只有4个单位(即4\*4的网格), 也就是只有25个可用的点, 这显然并不够用. 一个典型的TrueType字体使用的EM方格大小为2048个单位; Type1或CFF PostScript字体则传统上使用1000网格单元的EM方格(不过其中的点坐标可能使用浮点数来表示).

注意如果设计者希望的话, 字形是可以自由伸展至超出EM方格的. EM方格只是传统字体排印中的惯例.

网格单位也常常被称作*字体单位*或*EM单位*.

*正如之前所说, 通过上述的公式计算的`像素大小`和字符在屏幕上的大小并不直接相关. 它只是（如果显示的话）EM方格的大小. 对于任何一个字体设计者来说, 他可以自由的依照自己的意愿将字形安置在方格中. 这也就解释了为什么为什么下面的不同字体的文本们虽然显示在同样的点数大小下, 却并不具有相同的高度.*

​            ![字体高度之间的比较](body_comparison.png)          

如你所见, Courier家族的字形相比Times New Roman的要小, 同时Times New Roman的又略微比Arial的小一些, 即使它们被显示或打印的大小都是16点. 这只体现了设计中的选择.

### 3. 字体微调与位图渲染

被存储在字体文件中的轮廓被称为“主要”轮廓, 其点坐标通过字体单位表达的. 在被转换为位图之前, 它必须先被缩放到给定的大小和分辨率. 这一步通过一个非常简单的变形完成, 不过尤其是在字符的(目标)尺寸很小的时候, 可能会出现不受欢迎的失真, 这在像“E”或者“H”的字母不同宽度或高度的特定字干(stem)中尤其常见.

因此, 进行准确的字形渲染需要将缩放后的点对齐到目标设备的像素网格上, 这一步操作被称为*网格拟合*(也被称为*微调*). 这步操作的主要目的之一是确保重要的宽度和高度在整个字体中被一致的遵循（例如通常上“I”和“T”的字形的中央垂直线具有相同的像素宽度是令人愉悦的）, 同时也用来处理一些特征, 例如在小像素尺寸下可能造成问题的字干与overshoot过程(译注: 即为了使得视觉上显示出相同的高度, 将圆或尖角的字母(如“O”, “A”)伸展至高于扁平的字符(如“X”, “H”))

There are several ways to perform grid-fitting properly;            most scalable formats associate some control data or            programs with each glyph outline.  Here is an            overview:

- explicit grid-fitting

  The TrueType format defines a stack-based virtual                machine, for which programs (also                called *bytecode*) can be written with the help                of more than 200 operators, most of them related                to geometrical operations.  Each glyph is thus made of                both an outline and a control program to perform the                actual grid-fitting in the way defined by the font                designer.

- implicit grid-fitting (also called hinting)

  The Type 1, CFF, and CFF2 formats take a much                simpler approach: Each glyph is made of an outline as                well as several pieces called *hints* which are                used to describe some important features of the glyph,                like the presence of stems, some width regularities,                and the like.  There aren't a lot of hint types, and                it is up to the final renderer to interpret the hints                in order to produce a fitted outline.

- automatic grid-fitting

  Some formats include no control information with each                glyph outline, apart from font metrics like the                advance width and height.  It is then up to the                renderer to ‘guess’ the more interesting                features of the outline in order to perform some                decent grid-fitting.

The following table summarizes the pros and cons of each            scheme.

| **grid-fitting scheme** | **advantages**                                               | **disadvantages**                                            |
| ----------------------- | ------------------------------------------------------------ | ------------------------------------------------------------ |
| **explicit**            | **Quality.**  Excellent results at small sizes                    are possible.  This is very important for screen                    display.                   **Consistency.**  All renderers produce the                    same glyph bitmaps (at least in theory). | **Speed.**  Interpreting bytecode can be slow                    if the glyph programs are complex.                   **Size.**  Glyph programs can be long.                                  **Technical difficulty.**  It is extremely                    difficult to write good hinting programs.  Very                    few tools available. |
| **implicit**            | **Size.**  Hints are usually much smaller than                    explicit glyph programs.                   **Speed.**  Grid-fitting is usually a fast                    process. | **Quality.**  Often questionable at small                    sizes.  Better with anti-aliasing though.                   **Inconsistency.**  Results can vary between                    different renderers, or even distinct versions of                    the same engine. |
| **automatic**           | **Size.**  No need for control information,                    resulting in smaller font files.                                  **Speed.**  Depends on the grid-fitting                    algorithm.  Usually faster than explicit                    grid-fitting. | **Quality.**  Often questionable at small                    sizes.  Better with anti-aliasing though.                   **Speed.**  Depends on the grid-fitting                    algorithm.                          **Inconsistency.**  Results can vary between                    different renderers, or even distinct versions                    of the same engine. |