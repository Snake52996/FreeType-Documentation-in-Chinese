### II. 字形轮廓

这一节将介绍字形图像, 又被称作轮廓, 的可缩放表述法是如何被FreeType以及客户端应用所使用的.

#### 1. 像素, 点与设备分辨率

在计算机图像程序的领域中, 一个给定的像素（无论是对于屏幕上还是对于打印机来说）的物理尺寸并不是一个精确的正方形是一个基本的假设. 通常, 输出设备在水平和垂直方向上均会表现出不同的分辨率, 因此必须在渲染文本的时候注意处理.

所以普遍用以两个数来表示的*dpi*（每英寸中的点数）来定义设备的这种特征. 例如一个解析度为300×600dpi的打印机在水平方向上每英寸有300个像素点, 而在垂直方向上每英寸的像素点则有600个. 一个典型的计算机显示器根据其尺寸而有所不同（例如同有1024×768个像素点的10英寸和25英寸的显示器显然并不具有同样的像素尺寸）, 同时也显然与图像显示模式的分辨率有关.

因此, 文本的大小一般是通过*点*给定的, 而不是依具体设备有所不同的像素. 点是一个物理单位, 在数字排印中一点等于七十二分之一英寸. 例如绝大多数使用拉丁字母的书籍在印刷时使用的主体部分文本大小在10至14点之间.

由于存在这种关系, 我们可以利用如下的公式来从以点为单位的文本大小计算得出以像素为单位的文本大小:
$$
\text{像素大小}=\frac{\text{点数大小}\times\text{解析度}}{72}
$$
其中的解析度是通过*dpi*表述的. 由于水平与垂直的解析度可能有所不同, 由一个点数定义大小的文本通常并不具有相同的像素宽度和高度.

*并不符合直觉的, 文本的像素大小和字符被显示或打印时的实际尺寸之间并不具有直接的关系. 这两个概念之间的关系要更加复杂一点, 而且取决于字体设计者在设计时的一些选择.  关于这一点, 在下个小节中会进行更细致的介绍(参见对[TODO]的解释)This is described in more detail in the next sub-section (see the explanations on the EM square).*

#### 2. Vectorial representation

The source format of outlines is a collection of closed paths called            *contours*.  Each contour delimits an outer or            inner *region* of the glyph, and can be made of            either *line segments* or *Bézier            arcs*.

The arcs are defined through *control points*, and            can be either second-order (these are *conic*            Béziers) or third-order (*cubic*            Béziers) polynomials, depending on the font format.            Note that conic Béziers are usually called            *quadratic* Béziers in the literature.            Hence, FreeType associates each point of the outline with            flags to indicate its type (normal or control point).  As            a consequence, scaling the points will scale the whole            outline.

Each glyph's original outline points are located on a            grid of indivisible units.  The points are usually stored            in a font file as 16-bit integer grid coordinates, with            the grid's origin being at (0,0); they thus range from            -32768 to 32767.  (Even though point coordinates can            be floats in other formats such as Type 1, we will            restrict our analysis to integer values for            simplicity).

*The grid is always oriented like the traditional              mathematical two-dimensional plane, i.e.,              the \*X* axis goes from the left to the right,              and the \*Y* axis from bottom to top.*

In creating the glyph outlines, a type designer uses an            imaginary square called the *EM square*.            Typically, the EM square can be thought of as a tablet on            which the characters are drawn.  The square's size, i.e.,            the number of grid units on its sides, is very important            for two reasons:

- It is the reference size used to scale the outlines                to a given text dimension.  For example, a size of                12pt at 300×300 dpi corresponds to                12*300/72 = 50 pixels.  This is the                size the EM square would appear on the output device                if it was rendered directly.  In other words, scaling                from grid units to pixels uses the formula:

  ​                `pixel_size = point_size * resolution / 72`
  ​                `pixel_coord = grid_coord * pixel_size / EM_size`              

  Another acronym used for the pixel size              is *ppem* (pixel per EM); this value can be              fractional also.  Note that fractional ppem values are              not supported everywhere.

- The greater the EM size is, the larger resolution the                designer can use when digitizing outlines.  For                example, in the extreme example of an EM size of                4 units, there are only 25 point positions                available within the EM square which is clearly not                enough.  Typical TrueType fonts use an EM size of                2048 units; Type 1 or CFF PostScript fonts                traditionally use an EM size of 1000 grid units                (but point coordinates can be expressed as floating                values).

Note that glyphs can freely extend beyond the EM square            if the font designer wants so.  The EM square is thus just            a convention in traditional typography.

Grid units are very often called *font units*            or *EM units*.

*As said before, `pixel_size` computed in              the above formula does not directly relate to the size              of characters on the screen.  It simply is the size of              the EM square if it was to be displayed.  Each font              designer is free to place its glyphs as it pleases him              within the square.  This explains why the letters of the              following text have not the same height, even though              they are displayed at the same point size with distinct              fonts:*

​            ![字体高度之间的比较](body_comparison.png)          

As one can see, the glyphs of the Courier family are            smaller than those of Times New Roman, which themselves            are slightly smaller than those of Arial, even though            everything is displayed or printed at a size of            16 points.  This only reflects design choices.

### 3. Hinting and Bitmap rendering

The outline as stored in a font file is called the            ‘master’ outline, as its point coordinates are            expressed in font units.  Before it can be converted into            a bitmap, it must be scaled to a given size and            resolution.  This is done with a very simple            transformation, but especially at small sizes undesirable            artifacts can appear, in particular stems of different            width or height in letters like ‘E’ or            ‘H’ can occur.

As a consequence, proper glyph rendering needs the scaled            points to be aligned along the target device pixel grid,            through an operation called *grid-fitting* (often            called *hinting*).  One of its main purposes is to            ensure that important widths and heights are respected            throughout the whole font (for example, it is very often            desirable that the ‘I’ and the ‘T’            glyphs have their central vertical line of the same pixel            width), as well as to manage features like stems and            overshoots, which can cause problems at small pixel            sizes.

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