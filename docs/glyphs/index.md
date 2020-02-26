该文档给出了FreeType遵循的用于管理字体和字形数据的核心约定. 对于任何需要理解数字排印的开发者, 特别是对于想要将FreeType2库使用于你的项目中的开发者来说是必读内容.

### [I. 数字排印基本概念](glyphs-1.md)

1. [字体文件, 格式与信息](glyphs-1.md#1._字体文件,_格式与信息)

2. [字符图像与字符表](glyphs-1.md#2._字符图像与字符表)

3. [字符与字体指标](glyphs-1.md#3._字符与字体指标)

### [II. Glyph Outlines](file:///media/snake-for-safety/数据/下载/freetype-2.10.1/docs/glyphs/glyphs-2.html)

- ​              [1. Pixels, points and                 device resolutions](file:///media/snake-for-safety/数据/下载/freetype-2.10.1/docs/glyphs/glyphs-2.html#section-1)            
- ​              [2. Vectorial                 representation](file:///media/snake-for-safety/数据/下载/freetype-2.10.1/docs/glyphs/glyphs-2.html#section-2)            
- ​              [3. Hinting and bitmap                 rendering](file:///media/snake-for-safety/数据/下载/freetype-2.10.1/docs/glyphs/glyphs-2.html#section-3)            

### [III. Glyph Metrics](file:///media/snake-for-safety/数据/下载/freetype-2.10.1/docs/glyphs/glyphs-3.html)

- ​              [1. Baseline, pens and                 layouts](file:///media/snake-for-safety/数据/下载/freetype-2.10.1/docs/glyphs/glyphs-3.html#section-1)            
- ​              [2. Typographic metrics                 and bounding boxes](file:///media/snake-for-safety/数据/下载/freetype-2.10.1/docs/glyphs/glyphs-3.html#section-2)            
- ​              [3. Bearings and               advances](file:///media/snake-for-safety/数据/下载/freetype-2.10.1/docs/glyphs/glyphs-3.html#section-3)            
- ​              [4. The effects of                 grid-fitting](file:///media/snake-for-safety/数据/下载/freetype-2.10.1/docs/glyphs/glyphs-3.html#section-4)            
- ​              [5. Text widths and                 bounding box](file:///media/snake-for-safety/数据/下载/freetype-2.10.1/docs/glyphs/glyphs-3.html#section-5)            

### [IV. Kerning](file:///media/snake-for-safety/数据/下载/freetype-2.10.1/docs/glyphs/glyphs-4.html)

- ​              [1. Kerning pairs](file:///media/snake-for-safety/数据/下载/freetype-2.10.1/docs/glyphs/glyphs-4.html#section-1)            
- ​              [2. Applying                 kerning](file:///media/snake-for-safety/数据/下载/freetype-2.10.1/docs/glyphs/glyphs-4.html#section-2)            

### [V. Text Processing](file:///media/snake-for-safety/数据/下载/freetype-2.10.1/docs/glyphs/glyphs-5.html)

- ​              [1. Writing simple text                 strings](file:///media/snake-for-safety/数据/下载/freetype-2.10.1/docs/glyphs/glyphs-5.html#section-1)            
- ​              [2. Sub-pixel                 positioning](file:///media/snake-for-safety/数据/下载/freetype-2.10.1/docs/glyphs/glyphs-5.html#section-2)            
- ​              [3. Simple kerning](file:///media/snake-for-safety/数据/下载/freetype-2.10.1/docs/glyphs/glyphs-5.html#section-3)            
- ​              [4. Right-to-left                 layouts](file:///media/snake-for-safety/数据/下载/freetype-2.10.1/docs/glyphs/glyphs-5.html#section-4)            
- ​              [5. Vertical                 layouts](file:///media/snake-for-safety/数据/下载/freetype-2.10.1/docs/glyphs/glyphs-5.html#section-5)            

### [VI. FreeType Outlines](file:///media/snake-for-safety/数据/下载/freetype-2.10.1/docs/glyphs/glyphs-6.html)

- ​              [1. FreeType outline                 description and structure](file:///media/snake-for-safety/数据/下载/freetype-2.10.1/docs/glyphs/glyphs-6.html#section-1)            
- ​              [2. Bounding and                 control box computations](file:///media/snake-for-safety/数据/下载/freetype-2.10.1/docs/glyphs/glyphs-6.html#section-2)            
- ​              [3. Coordinates,                 scaling, and grid-fitting](file:///media/snake-for-safety/数据/下载/freetype-2.10.1/docs/glyphs/glyphs-6.html#section-3)            

### [VII. FreeType Bitmaps](file:///media/snake-for-safety/数据/下载/freetype-2.10.1/docs/glyphs/glyphs-7.html)

- ​              [1. Vectorial versus                 pixel coordinates](file:///media/snake-for-safety/数据/下载/freetype-2.10.1/docs/glyphs/glyphs-7.html#section-1)            
- ​              [2. The `FT_Bitmap`                 descriptor](file:///media/snake-for-safety/数据/下载/freetype-2.10.1/docs/glyphs/glyphs-7.html#section-2)            
- ​              [3. Converting outlines                 into bitmaps and pixmaps](file:///media/snake-for-safety/数据/下载/freetype-2.10.1/docs/glyphs/glyphs-7.html#section-3)              