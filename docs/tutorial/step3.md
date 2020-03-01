## III. Examples

For completeness, here again a link to            the [example](example1.c) used and explained in            the [first part of the             tutorial](step1.md).

[Erik Möller](mailto:erik@timetrap.se)            contributed a very nice C++ example that shows renderer            callbacks in action to draw a coloured glyph with a            differently coloured outline.  The source code can be            found [here](example2.cpp).

[Another example](example3.cpp) demonstrates            how to use FreeType's stand-alone B/W rasterizer,            `ftraster.c`.  You need files from FreeType version            2.3.10 or newer.

[Róbert Márki](mailto:gsmiko@gmail.com)            contributed a small            [Qt demonstration program](example4.cpp)            (together with its [qmake file](example4.pro))            that shows both direct rendering with a callback and            rendering with a buffer, yielding the same result.  You            need FreeType 2.4.3 or newer.

[Here](example5.cpp) is some simple C++ code            (contributed            by [Static Jobs             LLC](https://www.staticjobs.com)) that            uses [`FT_Outline_Decompose`](../reference/ft2-outline_processing.md#ft_outline_decompose)            to convert a glyph outline to the SVG format.  As an            example, here is the [resulting               file](example5.svg) of the call

```
example5 LiberationSerif-Bold.ttf @ > example5.svg
```

(you can find the Liberation font            family [here](https://fedorahosted.org/liberation-fonts/)).