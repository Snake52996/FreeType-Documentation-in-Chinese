## IV. Module Classes

We will now try to explain more precisely            the *types* of modules that FreeType 2 is            capable of managing.

- *Renderer* modules manage scalable glyph                images.  This means *transforming* them,                computing their *bounding box*,                and *converting* them to                either *monochrome* or *anti-aliased*                bitmaps.

  Note that FreeType 2 is capable of dealing                with *any* kind of glyph images, as long as a                renderer module is provided for it.  The library comes                by default with two renderers.

  | `raster` | Supports the conversion of vectorial outlines                    (described by                    an [`FT_Outline`](../reference/ft2-outline_processing.html#ft_outline)                    object) to *monochrome* bitmaps. |
  | -------- | ------------------------------------------------------------ |
  | `smooth` | Supports the conversion of the same outlines to                    *anti-aliased* pixmaps (using 256 levels of                    gray).  Note that this renderer also supports                    direct span generation, this is, it provides a                    hook into the engine so that the application can                    manipulate the rendering results itself, instead                    of letting the rasterizer fill a pixmap.                    See [this                     tutorial demo file](../tutorial/example4.cpp) for an example. |

- *Font driver* modules support one or more                specific font formats.  Here is a list with the most                important ones.

  | `truetype` | TrueType fonts.                                              |
  | ---------- | ------------------------------------------------------------ |
  | `type1`    | Postscript Type 1 fonts, both in binary                      (`.pfb`) or ASCII (`.pfa`)                      formats, including Multiple Master fonts. |
  | `cid`      | Postscript CID-keyed fonts.                                  |
  | `cff`      | OpenType CFF and CFF2, bare CFF, and CEF fonts                      (CEF is a derivative of CFF used by Adobe in its                      SVG viewer). |
  | `winfonts` | Windows bitmap fonts (i.e., `.fon` and                      `.fnt`). |

  Note that font drivers can support bitmapped or                scalable glyph images.  A given font driver that                supports Bézier outlines                through `FT_Outline` can also provide its own                hinter, or rely on FreeType's `autofit`                module for auto-hinting.

- *Helper* modules are used to hold shared code                that is often used by several font drivers, or even                other modules.  The most important are as follows.

  | `sfnt`    | Support for font formats based on                      the `SFNT` storage scheme: TrueType,                      OpenType, as well as other variants (like                      TrueType fonts that only contain embedded                      bitmaps). |
  | --------- | ------------------------------------------------------------ |
  | `psnames` | Various useful functions related to glyph name                      ordering and Postscript encodings and charsets.                      For example, this module is capable of                      automatically synthetizing a Unicode charmap                      from a Type 1 glyph name dictionary. |
  | `psaux`   | Auxiliary functions related to Postscript                      charstring decoding, as needed by                      the `type1`, `cid`,                      and `cff` drivers. |

- Finally, the *auto-hinter* module                (`autofit`) has a specific role in                FreeType 2, as it can be used automatically                during glyph loading to process individual glyph                outlines when a font driver doesn't provide its own                hinting engine.

  A paper published in the EuroTeX 2003 proceedings,                titled [*Real-Time                 Grid Fitting of Typographic Outlines*](http://www.tug.org/TUGboat/tb24-3/lemberg.pdf), gives                further insight into the auto-hinting system's inner                workings.

We will now study how modules are described, then managed            by the library.

### 1. The `FT_Module_Class`            Structure

Here is the definition of `FT_Module_Class`, with            some explanations.  The following code is taken from            `ftmodapi.h`.

```
typedef struct  FT_Module_Class_
{
  FT_ULong               module_flags;
  FT_Int                 module_size;
  const FT_String*       module_name;
  FT_Fixed               module_version;
  FT_Fixed               module_requires;

  const void*            module_interface;

  FT_Module_Constructor  module_init;
  FT_Module_Destructor   module_done;
  FT_Module_Requester    get_interface;

} FT_Module_Class;
```

A description of its fields.

| `module_flags`     | A set of bit flags to describe the module's                  category.  Valid values are listed below.                                                       `FT_MODULE_FONT_DRIVER` if the module is a                    font driver                                                        `FT_MODULE_RENDERER` if the module is a                    renderer                                                        `FT_MODULE_HINTER` if the module is an                    auto-hinter                                                        `FT_MODULE_DRIVER_SCALABLE` if the module                    is a font driver supporting scalable glyph formats                                                        `FT_MODULE_DRIVER_NO_OUTLINES` if the                    module is a font driver supporting scalable glyph                    formats that *cannot* be described by                    an `FT_Outline` object                                                        `FT_MODULE_DRIVER_HAS_HINTER` if the module                    is a font driver that provides its own hinting                    scheme/algorithm                                                        `FT_MODULE_DRIVER_HINTS_LIGHTLY` if the                    module is a font driver that generates                    ‘light’ hints (this is, only along the                    vertical axis). |
| ------------------ | ------------------------------------------------------------ |
| `module_size`      | An integer that gives the size in *bytes* of                  a given module object.  This should *never*                  be less than `sizeof(FT_ModuleRec)`, but can                  be more if the module needs to sub-class the                  base `FT_ModuleRec` class. |
| `module_name`      | The module's internal name, coded as a simple ASCII                  C string.  There can't be two modules with the                  same name registered in a given `FT_Library`                  object.  However, `FT_Add_Module` uses                  the `module_version` field to detect module                  upgrades and perform them cleanly, even at                  run-time. |
| `module_version`   | A 16.16 fixed-point number giving the module's                  major and minor version numbers.  It is used to                  determine whether a module needs to be upgraded when                  calling `FT_Add_Module`. |
| `module_requires`  | A 16.16 fixed-point number giving the version of                  FreeType 2 that is required to install this                  module.  The default value is 0x20000 for FreeType                  version 2.x |
| `module_interface` | Most modules support one or more                  ‘interfaces’, i.e., tables of function                  pointers.  This field points to the module's main                  interface, if there is one.  It is a short-cut that                  prevents users of the module to                  call `get_interface` each time they need to                  access one of the object's common entry points.                 Note that it is optional, and can be set to NULL.                  Other interfaces can also be accessed through                  the `get_interface` field. |
| `module_init`      | A pointer to a function to initialize the fields of                  a fresh new `FT_Module` object.  It is                  called *after* the module's base fields have                  been set by the library, and is generally used to                  initialize the fields of `FT_ModuleRec`                  subclasses.                 Most module classes set it to NULL to indicate that                  no extra initialization is necessary. |
| `module_done`      | A pointer to a function to finalize the fields of a                  given `FT_Module` object.  Note that it is                  called *before* the library unsets the                  module's base fields, and is generally used to                  finalize the fields of `FT_ModuleRec`                  subclasses.                 Most module classes set it to NULL to indicate that                  no extra finalization is necessary |
| `get_interface`    | A pointer to a function to request the address of a                  given module interface.  Set it to NULL if you don't                  need to support additional interfaces but the main                  one. |

### 2. The `FT_Module` Type

The `FT_Module` type is a handle (i.e., a pointer)            to a given module object or instance, whose base structure            is given by the internal `FT_ModuleRec` type.  We            will intentionally *not* describe this structure            here, as there is no point to look so far into the            library's design.

When `FT_Add_Module` is called, it first allocates            a new module instance, using the `module_size`            class field to determine its byte size.  The function            initializes the root `FT_ModuleRec` field, then            calls the class-specific initializer `module_init`            when this field is not set to NULL.

Note that the library defines several sub-classes of            `FT_ModuleRec`.

- `FT_Renderer` for renderer modules
- `FT_Driver` for font driver modules
- `FT_AutoHinter` for the auto-hinter

Helper modules use the base `FT_ModuleRec`            type.