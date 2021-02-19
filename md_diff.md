# Proposed changes to ISO/IEC 14496-22 (Amendment 2)

Changes to the following sections of ISO/IEC 14496-22:2019 Open Font Format
(OFF) are proposed:

- [4.3 Data types](#changes-to-off-43-data-types)
- [5.7.11 COLR – Color Table](#changes-to-off-5711---color-table)
- <ins class="ins">[5.7.12 CPAL – Palette Table](#changes-to-off-5712---palette-table)
- [7.2.1 Overview (Font variations common table formats)](#changes-to-off-721-overview-font-variations-common-table-formats)
-</ins> [7.2.3 Item variation stores](#changes-to-off-723-item-variation-stores)
- [Bibliography](#changes-to-off-bibliography)

## Changes to OFF 4.3 Data types

_Replace the table defining data types with the following (added row for Offset24):_

| Data Types | Description |
|-|-|
| uint8 | 8-bit unsigned integer. |
| int8 | 8-bit signed integer. |
| uint16 | 16-bit unsigned integer. |
| int16 | 16-bit signed integer. |
| uint24 | 24-bit unsigned integer. |
| uint32 | 32-bit unsigned integer. |
| int32 | 32-bit signed integer. |
| Fixed | 32-bit signed fixed-point number (16.16) |
| FWORD | int16 that describes a quantity in font design units. |
| UFWORD | uint16 that describes a quantity in font design units. |
| F2DOT14 | 16-bit signed fixed number with the low 14 bits of fraction (2.14). |
| LONGDATETIME | Date and time represented in number of seconds since 12:00 midnight, January 1, 1904. The value is represented as a signed 64-bit integer. |
| Tag | Array of four uint8s (length = 32 bits) used to identify a table, design-variation axis, script, language system, feature, or baseline |
| Offset16 | Short offset to a table, same as uint16, NULL offset = 0x0000 |
| Offset24 | 24-bit offset to a table, same as uint24, NULL offset = 0x000000 |
| Offset32 | Long offset to a table, same as uint32, NULL offset = 0x00000000 |

## Changes to OFF 5.7.11 - Color Table

_Replace the content of clause 5.7.11 with the following:_

The COLR table adds support for multi-colored glyphs in a manner that integrates
with the rasterizers of existing text engines and that is designed to be easy to
support with current OpenType font files.

The COLR table defines color presentations for glyphs. The color presentation of
a glyph is specified as a graphic composition using other glyphs, such as a
layered arrangement of glyphs, each with a different color. The term “color
glyph” is used informally to refer to such a graphic composition defined in the
COLR table; and the term “base glyph” is used to refer to a glyph for which a
color glyph is provided. Processing of the COLR table is done on glyph sequences
after text layout processing is completed and prior to final presentation of
glyphs. Typically, a base glyph is a glyph that may occur in a sequence that
results from the text layout process. In some cases, a base glyph may be a
virtual glyph defined within this table as a re-usable color composition.

For example, the Unicode character U+1F600 is the grinning face emoji. Suppose
in an emoji font the 'cmap' table maps U+1F600 to glyph ID 718. Assuming no
glyph substitutions, glyph ID 718 would be considered the base glyph. Suppose
the COLR table has data describing a color presentation for this using a layered
arrangement of other glyphs with different colors assigned: that description and
its presentation result would be considered the corresponding color glyph.

Two versions of the COLR table are defined.

Version 0 allows for a simple composition of colored elements: a linear sequence
of glyphs that are stacked vertically as layers in bottom-up z-order. Each layer
combines a glyph outline from the &#39;glyf&#39;, CFF or CFF2 table (referenced
by glyph ID) with a solid color fill. These capabilities are sufficient to
define color glyphs such as those illustrated in figure 5.6.

![Three emoji glyphs that use layered shapes with solid color fills.](images/colr_v0_emoji_sample.png)

**Figure 5.6 Examples of the graphic capabilities of COLR version 0**

Version 1 supports additional graphic capabilities. In addition to solid colors,
gradient fills can be used, as well as more complex fills using other graphic
operations, including affine transformations and various blending modes. Version
1 capabilities allow for color glyphs such as those illustrated in figure 5.7:

![Three emoji glyphs that use gradient fills and other effects.](images/colr_v1_emoji_sample.png)

**Figure 5.7 Examples of the graphic capabilities of COLR version 1**

Version 1 also extends capabilities in variable fonts. A COLR version 0 table
can be used in variable fonts with glyph outlines being variable, but no other
aspect of the color composition being variable. In version 1, all of the new
constructs for which it could be relevant have been designed to be variable; for
example, the placement of color stops in a gradient, or the alpha values applied
to colors. The graphic capabilities supported in version 0 and in version 1 are
described in more detail below.

The COLR table is used in combination with the CPAL table (5.7.12): all color
values are specified as entries in color palettes defined in the CPAL table. If
the COLR table is present in a font but no CPAL table exists, then the COLR
table is ignored.

**5.7.11.1 Graphic Compositions**

The graphic compositions in a color glyph definition use a set of 2D graphic
concepts and constructs:

* Shapes (or *geometries*)
* Fills (or *shadings*)
* Layering—a *z-order*—of elements
* Composition and blending modes—different ways that the content of a layer is
combined with the content of layers above or below it
* Affine transformations

For both version 0 and version 1, shapes are obtained from glyph outlines in the
&#39;glyf&#39;, &#39;CFF &#39; or CFF2 table, referenced by glyph ID. Colors
used in fills are obtained from the CPAL table.

The simplest color glyphs use just a few of the concepts above: shapes, solid
color fills, and layering. This is the set of capabilities provided by version 0
of the COLR table. In version 0, a base glyph record specifies the color glyph
for a given base glyph as a sequence of layers. Each layer is specified in a
layer record and has a shape (a glyph ID) and a solid color fill (a CPAL palette
entry). The filled shapes in the layer stack are composed using only alpha
blending.

Figure 5.8 illustrates the version 0 capabilities: three shapes are in a layered
stack: a blue square in the bottom layer, an opaque green circle in the next
layer, and a red triangle with some transparency in the top layer.

![Blue square, partially overlapped by an opaque green circle, both partially overlapped by a translucent red triangle.](images/colr_v0_layering.png)

**Figure 5.8 Basic graphic capabilities of COLR version 0**

The basic concepts also apply to color glyphs defined using the version 1
formats: shapes <del class="del">are</del> <ins class="ins">have fills and can be</ins> arranged in <del class="del">layers and have fills.</del> <ins class="ins">layers.</ins> But the additional
formats of version 1 support much richer capabilities. In a version 1 color
glyph, graphic constructs and capabilities are represented primarily in *Paint*
tables, which are linked together in a *directed, acyclic graph*. Several
different Paint formats are defined, each describing a particular type of
graphic operation:

* A PaintColrLayers table provides a layering structure used for creating a
color glyph from layered elements. A PaintColrLayers table can be used at the
root of the graph, providing a base layering structure for the entire color
glyph definition. A PaintColrLayers table can also be nested within the graph,
providing a set of layers to define some graphic sub-component within the color
glyph.

* The PaintSolid, <ins class="ins">PaintVarSolid,</ins> PaintLinearGradient, <ins class="ins">PaintVarLinearGradient,
PaintRadialGradient, PaintVarRadialGradient, PaintSweepGradient,</ins> and <del class="del">PaintRadialGradient</del>
<ins class="ins">PaintVarSweepGradient</ins> tables provide basic fills, using color entries from the
CPAL table.

* The PaintGlyph table provides glyph outlines as the basic shapes.

* The <del class="del">PaintTransformed table is</del> <ins class="ins">PaintTransform and PaintVarTransform tables are</ins> used to apply an affine
transformation matrix to a sub-graph of paint tables, and the graphic operations
they represent. The PaintTranslate, <del class="del">PaintRotate</del> <ins class="ins">PaintVarTranslate, PaintRotate,
PaintVarRotate, PaintSkew,</ins> and <del class="del">PaintSkew</del> <ins class="ins">PaintVarSkew</ins> tables support specific
transformations.

* The PaintComposite table supports alternate compositing and blending modes for
two sub-graphs.

* The PaintColrGlyph table allows a color glyph definition, referenced by a base
glyph ID, to be re-used as a sub-graph within multiple color glyphs.

<ins class="ins">NOTE: Some paint formats come in *Paint\** and *PaintVar\** pairs. In these
cases, the latter format supports variations in variable fonts, while the former
provides a more compact representation for the same graphic capability but
without variation capability.</ins>

In a simple color glyph description, a PaintGlyph table might be linked to a
PaintSolid table, for example, representing a glyph outline filled using a basic
solid color fill. But the PaintGlyph table could instead be linked to a much
more complex sub-graph of Paint tables, representing a shape that gets filled
using the more-complex set of operations described by the sub-graph of Paint
tables.

The graphic capabilities are described in more detail in 5.7.11.1.1 –
5.7.11.1.9. The formats used for each are specified 5.7.11.2.

**5.7.11.1.1 Colors and solid color fills**

All colors are specified as a base zero index into CPAL (5.7.12) palette
entries. A font can define alternate palettes in its CPAL table; it is up to the
application to determine which palette is used. A palette entry index value of
0xFFFF is a special case indicating that the text foreground color (defined by
the application) should be used, and shall not be treated as an actual index
into the CPAL ColorRecord array.

The CPAL color data includes alpha information, as well as RGB values. In the
COLR version 0 formats, a color reference is made in LayerRecord as a palette
entry index alone. In the formats added for COLR version 1, a color reference is
made in a <del class="del">ColorIndex</del> <ins class="ins">color index</ins> record, which includes a palette entry index and a separate
alpha value. Separation of alpha from palette entries in version 1 allows use of
transparency in a color glyph definition independent of the choice of palette.
The alpha value in the <del class="del">ColorIndex</del> <ins class="ins">color index</ins> record is multiplied into the alpha value
given in the CPAL color entry.

<ins class="ins">Two color index record formats are defined: ColorIndex, and VarColorIndex. The
latter can be used in variable fonts to make the alpha value variable.</ins>

In version 1, a solid color fill is specified using a <ins class="ins">PaintVarSolid or</ins>
PaintSolid <del class="del">table.</del> <ins class="ins">table, with or without variation support, respectively. See
5.7.11.2.5.2 for format details.</ins>

See 5.7.11.1.3 for details on how <del class="del">a PaintSolid fill is</del> <ins class="ins">fills are</ins> applied to a shape.

**5.7.11.1.2 Gradients**

COLR version 1 supports <del class="del">two</del> <ins class="ins">three</ins> types of gradients: linear gradients, <del class="del">and</del> radial
<ins class="ins">gradients, and sweep</ins> gradients. <del class="del">Both types</del> <ins class="ins">For each type, non-variable and variable formats
are defined. Each type</ins> of gradient <del class="del">are defined</del> <ins class="ins">is specified</ins> using a color line.

**5.7.11.1.2.1 Color Lines**

A color line is a function that maps real numbers to color values to define a
one-dimensional gradation of colors, to be used in the definition of <del class="del">linear</del> <ins class="ins">linear,
radial,</ins> or
<del class="del">radial</del> <ins class="ins">sweep</ins> gradients. A color line is defined as a set of one or more
color stops, each of which maps a particular real number to a specific color.

On its own, a color line has no positioning, orientation or size within a design
grid. The definition of a <del class="del">linear</del> <ins class="ins">linear, radial,</ins> or <del class="del">radial</del> <ins class="ins">sweep</ins> gradient will reference a
color line and map it onto the design grid by specifying positions in the design
grid that correspond to the real values 0 and 1 in the color line. The
specification for
<del class="del">linear and</del> <ins class="ins">linear,</ins> radial <ins class="ins">and sweep</ins> gradients also include rules for
where to draw interpolated colors of the color line, following from the
placement of 0 and 1.

A color stop is defined by a real number, the *stop offset*, and a color. A
color line is defined with at least one color stop within the interval [0, 1].
Additional color stops can be specified within or outside the interval [0, 1].
(Stop offsets are represented using F2DOT14 values, therefore color stops can
only be specified within the range <del class="del">2, 2). See 5.7.11.2.4 for format details.)
If only one color stop is specified, that color is used for the entire color
line; at least two color stops are needed to create color gradation.

Color gradation is defined over the interval from the first color stop, through
the successive color stops, to the last color stop. Between <del class="del">adjacent</del> <ins class="ins">numerically-adjacent</ins>
color stops, color values are linearly interpolated.

<del class="del">> **_TBD: Does interpolation</del> <ins class="ins">See _Interpolation</ins> of
<ins class="ins">Colors_ in 5.7.12 for requirements on how</ins> colors <del class="del">need further specification?_**</del> <ins class="ins">are interpolated.

For example, a gradient color line could be defined with two color stops at 0.2
and 1.5. The gradient color line is positioned in the design grid by aligning
stop offsets 0 and 1 to design grid positions, as defined for each gradient
type, using an extrapolated color of stop offset 0 at one position and an
interpolated color of stop offset 1 at the other position. Colors for offsets
between 0.5 and 1.5 are interpolated. Colors for offsets above 1.5 and below 0.2
are defined and determined by the color line’s *extend mode*, described below.</ins>

If there are multiple color stops defined for the same stop offset, the first
one is used for computing color values on the color line below that stop offset,
and the last one is used for computing color values at or above that stop
offset. All other color stops for that stop offset are ignored.

While the color gradation is specified over a defined interval, the color line
continues indefinitely outside that interval in both directions. The color
pattern outside the defined interval is repeated according to the color line’s
<del class="del">*extend mode*.</del>
<ins class="ins">extend mode.</ins> Three extend modes are supported:

* **Pad:** outside the defined interval, the color of the closest color stop is
used. Using a sequence of letters as an analogy, given a sequence “ABC”, it is
extended to “…*AA* ABC *CC*…”.

* **Repeat:** The color line is repeated over repeated multiples of the defined
interval. For example, if color stops are specified for defined interval of [0,
0.7], then the pattern is repeated above the defined interval for intervals
[0.7, 1.4], [1.4, 2.1], etc.; and also repeated below the defined interval for
intervals <del class="del">0.7, 0], <del class="del">1.4, -0.7], etc. In each repeated interval, the first
color is that of the farthest defined color stop. By analogy, given a sequence
“ABC”, it is extended to “…*ABC* ABC *ABC*…”.

* **Reflect:** The color line is repeated over repeated intervals, as for the repeat
mode. However, in each repeated interval, the ordering of color stops is the
reverse of the adjacent interval. By analogy, given a sequence “ABC”, it is
extended to “…*ABC CBA* ABC *CBA ABC*…”.

Figures 5.9 – 5.11 illustrate the different color line extend modes. The figures
show the color line extended over a limited interval, but the extension is
unbounded in either direction.

![Yellow-to-red color <del class="del">gradition,</del> <ins class="ins">gradation,</ins> extended to the left with yellow and extended to the right with red.](images/colr_gradient_extend_pad.png)

**Figure 5.9 Color gradation extended using pad mode**

![Yellow-to-red color <del class="del">gradition,</del> <ins class="ins">gradation,</ins> extended by repeating the gradation patterns to the left and right.](images/colr_gradient_extend_repeat.png)

**Figure 5.10 Color gradation extended using repeat mode**

![Yellow-to-red color <del class="del">gradition,</del> <ins class="ins">gradation,</ins> extended by repeating alternating mirrors of the gradation pattern to the left and right.](images/colr_gradient_extend_reflect.png)

**Figure 5.11 Color gradation extended using reflect mode**

NOTE: The extend modes are the same as the <del class="del">[spreadMethod][30]</del> <ins class="ins">[spreadMethod][31]</ins> attribute used for
linear and radial gradients in the [Scalable Vector Graphics (SVG) 1.1 (Second
<del class="del">Edition)][31]</del>
<ins class="ins">Edition)][32]</ins> specification.

When combining a color line with the geometry of a particular gradient
definition, one might want to achieve a certain number of repetitions of the
gradient pattern over a particular geometric range. Assuming that geometric
range will correspond to placement of stop offsets 0 and 1, the following steps
can be used:

* In order to get a certain number of repetitions of the gradient pattern
(without reflection), divide 1 by the number of desired repetitions, use the
result as the maximum stop offset for specified color stops, and set the extend
mode to *repeat*.
* In order to get a certain number of repetitions of the
reflected gradient pattern, divide 1 by two times the number of desired
repetitions, use the result as the maximum stop offset for specified color
stops, and set the extend mode to *reflect*.

<del class="del">**5.7.11.1.2.2 Linear gradients**

A linear gradient provide gradation of colors along a straight line. The
gradient is defined by two points, p₀</del>

<ins class="ins">NOTE: Special considerations apply to color line extend modes for sweep
gradients. See 5.7.11.1.2.4 for details.

Color lines are specified using color line tables, which contain arrays of color
stop records. Two color line table and two color stop record formats are
defined:

* ColorLine table and ColorStop record
* VarColorLine table and VarColorStop record

The VarColorLine and VarColorStop formats can be used in variable fonts and
allow for stop offsets to be variable. The VarColorStop record also uses the
VarColorIndex record, allowing the alpha to be variable. The ColorLine and
ColorStop formats provide a more compact representation when variation is not
required. See 5.7.11.2.4 for format details.

**5.7.11.1.2.2 Linear gradients**

A linear gradient provides gradation of colors along a straight line. The
gradient is defined by three points, p₀, p₁</ins> and <del class="del">p₁,</del> <ins class="ins">p₂,</ins> plus a color <del class="del">line,</del> <ins class="ins">line. The color
line is positioned in the design grid</ins> with stop offset 0 aligned to p₀ and stop
offset 1.0 aligned to p₁. <del class="del">Colors between</del> <ins class="ins">(The line passing through</ins> p₀ and p₁ <ins class="ins">will be referred
to as line p₀p₁.) Colors at each position on line p₀p₁</ins> are interpolated using
the color line.

<del class="del">An</del> <ins class="ins">For each position along line p₀p₁, the color at that position is
projected on other side of the line.

The</ins> additional point, p₂, is <del class="del">also</del> used to rotate the gradient orientation in the
space on either side of the line <del class="del">defined by p₀ and p₁.</del> <ins class="ins">p₀p₁.</ins> The <del class="del">vector from</del> <ins class="ins">line passing through points</ins> p₀ <del class="del">to</del> <ins class="ins">and</ins> p₂
<del class="del">can be referred to as the *rotation vector*. If the rotation vector is colinear
with</del>
<ins class="ins">(line p₀p₂) determines</ins> the <del class="del">line p₀p₁, there is no rotation: colors</del> <ins class="ins">direction</ins> in <del class="del">the space</del> <ins class="ins">which colors are projected</ins> on either
side of the <ins class="ins">color line. That is, for each position on</ins> line <del class="del">p₀p₁ extend in the perpendicular direction. But if the rotation vector
is not colinear, the gradient is drawn skewed by</del> <ins class="ins">p₀p₁,</ins> the <del class="del">angle between</del> <ins class="ins">line that
passes through that position on line</ins> p₀p₁ and
<del class="del">p₀p₂.

If the dot-product (p₁ - p₀) · (p₂ - p₀)</del> <ins class="ins">that</ins> is <del class="del">zero (or near-zero for an
implementation-defined definition) then</del> <ins class="ins">parallel to line p₀p₂ will
have the color for that position on line p₀p₁.

NOTE: For convenience, point p₂ can be referred to as the _rotation point_, and
the vector from p₀ to p₂ can be referred to as the _rotation vector_. However,
neither the magnitude of the vector nor the direction (from p₀ to p₂, versus
from p₂ to p₀) has significance.

If either point p₁ or p₂ is the same as point p₀, the gradient is ill-formed and
shall not be rendered. 

If line p₀p₂ is parallel to line p₀p₁ (or near-parallel for an
implementation-determined definition), then</ins> the gradient is ill-formed and shall
not be rendered.

<ins class="ins">NOTE: An implementation can derive a single vector, from p₀ to a point p₃, by
computing the orthogonal projection of the vector from p₀ to p₁ onto a line
perpendicular to line p₀p₂ and passing through p₀ to obtain point p₃. The linear
gradient defined using p₀, p₁ and p₂ as described above is functionally
equivalent to a linear gradient defined by aligning stop offset 0 to p₀ and
aligning stop offset 1.0 to p₃, with each color projecting on either side of
that line in a perpendicular direction. This specification uses three points,
p₀, p₁ and p₂, as that provides greater flexibility in controlling the placement
and rotation of the gradient, as well as variations thereof.</ins>

Figures 5.12 – 5.14 illustrate linear gradients using the three different color
line extend modes. Each figure illustrates linear gradients with two different
rotation vectors. In each case, three color stops are specified: red at 0.0,
yellow at 0.5, and <del class="del">red</del> <ins class="ins">blue</ins> at 1.0.

![Linear gradients with different <del class="del">rotation vectors</del> <ins class="ins">rotations</ins> using the pad extend mode.](images/colr_linear_gradients_pad.png)

**Figure 5.12 Linear gradients with different <del class="del">rotation vectors</del> <ins class="ins">rotations</ins> using the pad extend mode.**

![Linear gradients with different <del class="del">rotation vectors</del> <ins class="ins">rotations</ins> using the repeat extend mode.](images/colr_linear_gradients_repeat.png)

**Figure 5.13 Linear gradients with different <del class="del">rotation vectors</del> <ins class="ins">rotations</ins> using the repeat extend mode.**

![Linear gradients with different <del class="del">rotation vectors</del> <ins class="ins">rotations</ins> using the reflect extend mode.](images/colr_linear_gradients_reflect.png)

**Figure 5.14 Linear gradients with different <del class="del">rotation vectors</del> <ins class="ins">rotations</ins> using the reflect extend mode.**

<del class="del">**5.7.11.1.2.3 Radial gradients**

A radial gradient provides gradation of colors along</del>

<ins class="ins">NOTE: When</ins> a <del class="del">cylinder defined by two
circles. The</del> <ins class="ins">linear</ins> gradient is <del class="del">defined by circles with center c₀ and radius r₀, and</del> <ins class="ins">combined</ins> with <del class="del">center c₁ and radius r₁, plus</del> a <del class="del">color line. The color line aligns with</del> <ins class="ins">transformation (see 5.7.11.1.5),</ins>
the
<del class="del">two circles by associating stop offset 0 with</del> <ins class="ins">appearance will be the same as if the gradient were defined using the
transformed positions of points p₀, p₁ and p₂.

Linear gradients are specified using a PaintVarLinearGradient or
PaintLinearGradient table, with or without variation support, respectively. See
5.7.11.2.5.3 for format details.

See 5.7.11.1.3 for details on how fills are applied to a shape.

**5.7.11.1.2.3 Radial gradients**

A radial gradient provides gradation of colors along a cylinder defined by two
circles. The gradient is defined by circles with center c₀ and radius r₀, and
with center c₁ and radius r₁, plus a color line. The color line aligns with the
two circles by associating stop offset 0 with</ins> the first circle (with center c₀)
and aligning stop offset 1.0 with the second circle (with center c₁).

NOTE: The term “radial gradient” is used in some contexts for more limited
capabilities. In some contexts, the type of gradient defined here is referred to
as a “two point conical” gradient.

The drawing algorithm for radial gradients follows the [HTML WHATWG Canvas
specification for <del class="del">createRadialGradient()][32],</del> <ins class="ins">createRadialGradient()][33],</ins> but adapted with with alternate
color line extend modes, as described in 5.7.11.1.2.1. Radial gradients shall be
rendered with results that match the results produced by the following steps.

With circle center points c₀ and c₁ defined as c₀ = (x₀, y₀) and c₁ = (x₁, y₁):

1. If c₀ = c₁ and r₀ = r₁ then paint nothing and return.
2. For real values of ω:<br>
   Let x(ω) = (x₁-x₀)ω + x₀<br>
   Let y(ω) = (y₁-y₀)ω + y₀<br>
   Let r(ω) = (r₁-r₀)ω + r₀<br>
   Let the color at ω be the color at position ω on the color line.
3. For all values of ω where r(ω) > 0, starting with the value of ω nearest to
   positive infinity and ending with the value of ω nearest to negative
   infinity, draw the <del class="del">circumference of the ellipse resulting from translating
   a circle</del> <ins class="ins">circular line</ins> with radius r(ω) centered at position
   (x(ω), y(ω)), with the color at ω, but only painting on the parts of the
   bitmap that have not yet been painted on <del class="del">by earlier circles</del> in this step <del class="del">for this rendering</del> of the
   <del class="del">gradient.</del> <ins class="ins">algorithm for
   earlier values of ω.</ins>

The algorithm provides results in various cases as follows:

* When <ins class="ins">the circles are identical, then nothing is painted.
* When</ins> both radii are 0 (r₀ = r₁ = 0), then r(ω) is always 0 and nothing is
painted.
* If the centers of the circles are distinct, the radii of the circles are
different, and neither circle is entirely contained within the radius of the
other circle, then the resulting shape resembles a cone that is open to one
side. The surface outside the cone is not painted. (See figures 5.15 – 5.17.)
* If the centers of the circles are distinct but the radii are the same, and
neither circle is contained within the other, then the result will be a strip,
similar to the flattened projection of a circular cylinder. The surface outside
the strip is not painted. (See figures 5.18 – 5.20.)
* If the radii of the circles are different but one circle is entirely contained
within the radius of the other circle, the gradient will radiate in all
directions from the inner circle, and the entire surface will be painted. (See
figures 5.24 – 5.26.)

<del class="del">> **_TBD: What should the expected behaviour be in the case of both circles exactly overlapping with r > 0? (Only the extensions get painted, but how?)_**</del>

Figures 5.15 – 5.17 illustrate radial gradients using the three different color
line extend modes. The color line is defined with stops for the interval [0, 1]:
red at 0.0, yellow at 0.5, and blue at 1.0. Note that the circles that define
the gradient are not stroked as part of the gradient itself. Stroked circles
have been overlaid in the figure to illustrate the color line and the region
that is painted in relation to the two circles.

![Radial gradient using pad extend mode.](images/colr_radial_gradients_pad.png)

**Figure 5.15 Radial gradient using pad extend mode.**

![Radial gradient using repeat extend mode.](images/colr_radial_gradients_repeat.png)

**Figure 5.16 Radial gradient using repeat extend mode.**

![Radial gradient using reflect extend mode.](images/colr_radial_gradients_reflect.png)

**Figure 5.17 Radial gradient using reflect extend mode.**

Figures 5.18 – 5.20 illustrate the case in which the circles have distinct centers but
the same radii, and neither circle is contained within the other, giving the
appearance of a strip. The color stops are as in the previous figures.

![Radial gradient with same-size circles appearing as a string, using pad extend mode.](images/colr_radial_gradients_strip_pad.png)

**Figure 5.18 Radial gradient with same-size circles appearing as a string, using pad extend mode.**

![Radial gradient with same-size circles appearing as a string, using repeat extend mode.](images/colr_radial_gradients_strip_repeat.png)

**Figure 5.19 Radial gradient with same-size circles appearing as a string, using repeat extend mode.**

![Radial gradient with same-size circles appearing as a string, using reflect extend mode.](images/colr_radial_gradients_strip_reflect.png)

**Figure 5.20 Radial gradient with same-size circles appearing as a string, using reflect extend mode.**

Because the rendering algorithm progresses ω in a particular direction, from
positive infinity to negative infinity, and because pixels are not re-painted as
ω progresses, the appearance will be affected by which circle is considered
circle 0 and which is circle 1. This is illustrated in figures 5.21 – 5.23. The
gradient in figure 5.21 is the same as that in figure 5.15, using the pad extend
mode. In this gradient, circle 0 is the small circle, on the left. In figure
5.22, the start and end circles are reversed: circle 0 is the large circle, on
the right. The color line is kept the same, and so the red end starts at circle
0, now on the right. In figure 5.23, the order of stops in the color line is
also reversed to put red on the left. The key difference to notice between the
gradients in these figures is the way that colors are painted in the interior:
when the two circles are not overlapping, the arcs of constant color bend in the
same direction as the near side of circle 1.

NOTE: This difference does not exist if one circle is entirely contained within
the other: in that case, the arcs of constant color are complete circles.

![Cone-shaped radial gradient with circle 0 on the left.](images/colr_radial_gradients_direction_1.png)

**Figure 5.21 Cone-shaped radial gradient with circle 0 on the left.**

![Cone-shaped radial gradient with start and end circles swapped.](images/colr_radial_gradients_direction_2.png)

**Figure 5.22 Cone-shaped radial gradient with start and end circles swapped.**

![Cone-shaped radial gradient with start and end circles swapped and color line reversed.](images/colr_radial_gradients_direction_3.png)

**Figure 5.23 Cone-shaped radial gradient with start and end circles swapped and color line reversed.**

When one circle is contained within the other, the extension of the gradient
beyond the larger circle will fill the entire surface. Colors in the areas
inside the inner circle and outside the outer circle are determined by the
extend mode. Figures 5.24 – 5.26 illustrate this for the different extend modes.

![Radial gradient with one circle contained within the other, pad extend mode.](images/colr_radial_gradients_circle_within_circle_pad.png)

**Figure 5.24 Radial gradient with one circle contained within the other, pad extend mode.**

![Radial gradient with one circle contained within the other, repeat extend mode.](images/colr_radial_gradients_circle_within_circle_repeat.png)

**Figure 5.25 Radial gradient with one circle contained within the other, repeat extend mode.**

![Radial gradient with one circle contained within the other, reflect extend mode.](images/colr_radial_gradients_circle_within_circle_reflect.png)

**Figure 5.26 Radial gradient with one circle contained within the other, reflect extend mode.**

NOTE: <ins class="ins">When a radial gradient is combined with a transformation (see 5.7.11.1.5),
the appearance will be the same as if the geometry of the two circles were
transformed and step 3 of the algorithm were performed by interpolating the
shapes derived from the two transformed circles. For the condition r(ω) > 0, the
pre-transformation values of r(ω) can be used.

NOTE:</ins> A scale transformation <del class="del">(see 5.7.11.1.5)</del> can flatten shapes to resemble lines. If a radial
gradient is nested in the child sub-graph of a transformation that flattens the
circles so that they are nearly lines, the centers <del class="del">may</del> <ins class="ins">could</ins> still be separated by
some distance. In that case, <del class="del">a</del> <ins class="ins">the</ins> radial gradient would appear as a strip or a
cone filled with a linear gradient.

<del class="del">> **_TBD: We still need to specify required behaviour for the case</del>

<ins class="ins">If a radial gradient is nested</ins> in <del class="del">which</del> the <del class="del">transform really</del> <ins class="ins">sub-graph of a transformation that</ins>
flattens the <del class="del">two circles, and the centers, to</del> <ins class="ins">circles so that they form</ins> a <del class="del">line._**

**5.7.11.1.3 Filling shapes**

All basic shapes used in</del> <ins class="ins">single line (or nearly</ins> a <del class="del">color glyph are obtained from glyph outlines,
referenced using a glyph ID.</del> <ins class="ins">line, for an
implementation-determined definition), with both centers on that line, then the
resulting gradient is degenerate and shall not be rendered.

NOTE: As seen in the figures above, the gradient fills the space when one circle
is contained within the other, but not when neither circle is contained within
the other.</ins> In a <del class="del">color glyph description, a PaintGlyph table is
used to represent</del> <ins class="ins">variable font, if the placement or radii of the circles vary,
then</ins> a <del class="del">basic shape. 

NOTE: Shapes</del> <ins class="ins">sharp transition</ins> can <del class="del">also be derived using PaintGlyph tables</del> <ins class="ins">occur if the variation results</ins> in <del class="del">combination</del> <ins class="ins">one circle being
contained</ins> with <ins class="ins">the</ins> other <del class="del">tables, such as PaintTransformed (see 5.7.11.1.5) or PaintComposite (see
5.7.11.1.6).

The PaintGlyph table has a field</del> for <ins class="ins">some instances but not for other instances. This
transition will occur when</ins> the <del class="del">glyph ID, plus an offset to a child
paint table that is used as</del> <ins class="ins">inner circle just touches the outer circle (i.e.,
they have exactly one point in common). In this case,</ins> the <ins class="ins">gradient will</ins> fill <del class="del">for</del>
<ins class="ins">exactly one half of</ins> the <del class="del">shape. The glyph outline</del> <ins class="ins">space. This</ins> is <del class="del">not
rendered; only</del> <ins class="ins">illustrated in figure 5.27 using</ins> the <del class="del">fill is rendered.

Any of</del> <ins class="ins">pad
extend mode.

![Radial gradient with inner circle just touching</ins> the <del class="del">basic fill formats, PaintSolid, PaintLinearGradient,</del> <ins class="ins">outer circle, pad extend mode](images/colr_radial_gradients_inner_circle_touching_outer_pad.png)

**Figure 5.27 Radial gradient with inner circle just touching the outer circle, pad extend mode.**

When the repeat</ins> or
<del class="del">PaintRadialGradient, can be used as</del> <ins class="ins">reflect extend modes are used, having</ins> the <del class="del">child paint table.</del> <ins class="ins">two circles in very
close proximity results in very high spatial-frequency transitions that can lead
to Moiré patterns or other display artifacts.</ins> This is illustrated in figure <del class="del">5.27: a PaintGlyph table has a glyph ID for an outline in</del>
<ins class="ins">5.28, which shows</ins> the <del class="del">shape</del> <ins class="ins">display result, for one particular rendering context,</ins> of a <del class="del">triangle,</del>
<ins class="ins">radial gradient defined using nearly-identical circles</ins> and <del class="del">it links to a child PaintLinearGradient table. The combination
is used to represent a triangle filled with</del> the <del class="del">linear gradient.

![PaintGlyph</del> <ins class="ins">reflect extend
mode.

![Radial gradient defined using nearly-identical circles</ins> and <del class="del">PaintLinearGradient tables are used to fill a triangle shape</del> <ins class="ins">reflect extend mode, displaying</ins> with <del class="del">a linear gradient.](images/colr_shape_gradient.png)</del> <ins class="ins">interference patterns](images/colr_radial_gradients_interference_patterns_reflect.png)</ins>

**Figure <del class="del">5.27 PaintGlyph</del> <ins class="ins">5.28 Radial gradient defined using nearly-identical circles</ins> and <del class="del">PaintLinearGradient tables used to fill a shape</del> <ins class="ins">reflect extend mode, displaying</ins> with <ins class="ins">interference patterns**

The artifacts seen can be affected by</ins> a <del class="del">linear gradient.**

Another way to describe the relationship between a PaintGlyph table</del> <ins class="ins">combination of several factors, such as
image scaling, sub-pixel rendering, display technology,</ins> and <del class="del">its
child paint table is that</del> <ins class="ins">limitations in
software implementation or display capabilities. For this reason,</ins> the <del class="del">child provides a fill, and</del> <ins class="ins">appearance
can be very different in different situations. Font designers should exercise
caution if</ins> the <del class="del">glyph outline
defines</del> <ins class="ins">circles are in close proximity (either in</ins> a <del class="del">bounds,</del> <ins class="ins">static design</ins> or <del class="del">*clip region*, for the fill. The child</del> for <del class="del">a PaintGlyph
table is</del>
<ins class="ins">some variable font instances), and should</ins> not <del class="del">limited</del> <ins class="ins">rely on these display artifacts</ins> to <del class="del">only the basic fill formats. In general, the child can
be the root of</del>
<ins class="ins">obtain</ins> a <del class="del">sub-graph that describes some graphic composition that
comprises the fill for the shape. Or, in the alternate view, the glyph outline
defines</del> <ins class="ins">particular pattern.

Radial gradients are specified using</ins> a <del class="del">clip region that is</del> <ins class="ins">PaintVarRadialGradient or
PaintRadialGradient table, with or without variation support, respectively. See
5.7.11.2.5.4 for format details.

See 5.7.11.1.3 for details on how fills are</ins> applied to <del class="del">the composition.

To illustrate this, the example in figure 5.27 is extended in figure 5.28 so</del> <ins class="ins">a shape.

**5.7.11.1.2.4 Sweep gradients**

A sweep gradient provides a gradation of colors</ins> that <ins class="ins">sweep around</ins> a <del class="del">PaintGlyph table links to</del> <ins class="ins">center
point. For</ins> a <del class="del">second PaintGlyph</del> <ins class="ins">given color on a color line,</ins> that <del class="del">links to</del> <ins class="ins">color projects as</ins> a
<del class="del">PaintLinearGradient:</del> <ins class="ins">ray from</ins> the <del class="del">parent PaintGlyph will clip the filled shape described
by</del>
<ins class="ins">center point in a given direction. This is illustrated in figure 5.29.

NOTE: The following figures illustrate sweep gradients clipped to a circular
region. Sweep gradients are not bounded, however, and fill</ins> the <del class="del">child sub-graph.</del> <ins class="ins">entire space.</ins>

![A <del class="del">PaintGlyph table defines</del> <ins class="ins">sweep gradient](images/colr_conic_gradient.png)

**Figure 5.29 Sweep gradient**

NOTE: In some contexts, this type of gradient is referred to as</ins> a <del class="del">clip region for the composition</del> <ins class="ins">“conic”
gradient, or as an “angular” gradient.

A sweep gradient is</ins> defined by <del class="del">its child sub-graph.](images/colr_shape_shape_gradient.png)

**Figure 5.28 A PaintGlyph table defines</del> a <del class="del">clip region for</del> <ins class="ins">center point, starting and ending angles, and a
color line. The angles are expressed in counter-clockwise degrees from</ins> the <del class="del">composition defined by its child sub-graph.**

A PaintGlyph table</del>
<ins class="ins">direction of the positive x-axis</ins> on <del class="del">its own does not add content: if there is no child paint
table, then</del> the <del class="del">graph</del> <ins class="ins">design grid.

The color line</ins> is <del class="del">not well formed. See 5.7.11.1.9 for details regarding
well-formedness</del> <ins class="ins">aligned to a circular arc around the center point, with
arbitrary radius, with stop offset 0 aligned with the starting angle,</ins> and <del class="del">validity of</del> <ins class="ins">stop
offset 1 aligned with</ins> the <del class="del">graph.

**5.7.11.1.4 Layering**

Layering of visual elements was introduced above, in</del> <ins class="ins">ending angle. The color line progresses from</ins> the <del class="del">introduction</del> <ins class="ins">start
angle</ins> to
<del class="del">5.7.11.1. Both version 0</del> <ins class="ins">the end angle in the counter-clockwise direction; for example, if the
start</ins> and <del class="del">version 1 support use</del> <ins class="ins">end angles are both 0°, then stop offset 0.1 is at 36°
counter-clockwise from the direction</ins> of <del class="del">multiple layers, though in
different ways.</del> <ins class="ins">the positive x-axis.</ins> For <del class="del">version 0, layers are fundamental: they are</del> <ins class="ins">each position
along</ins> the <del class="del">sole way</del> <ins class="ins">circular arc, from start to end</ins> in <del class="del">which separate
elements are composed into</del> <ins class="ins">the counter-clockwise direction,</ins> a <del class="del">color glyph. An array of LayerRecords</del>
<ins class="ins">ray from the center outward</ins> is <del class="del">created,</del> <ins class="ins">painted</ins> with <del class="del">each LayerRecord specifying a glyph ID and a CPAL entry (a shape</del> <ins class="ins">the color of the color line at the
point where the ray passes through the arc.

The color line may be defined using color stops outside the range [0, 1],</ins> and <del class="del">solid</del>
color <del class="del">fill). Each</del> <ins class="ins">stops outside the range [0, 1] can be used to interpolate</ins> color <del class="del">glyph definition is a slice from that array (that is, a
contiguous sub-sequence), specified in a BaseGlyphRecord</del> <ins class="ins">values
within the range [0, 1], but only color values</ins> for <del class="del">a particular base
glyph. Within a given slice,</del> the <del class="del">first record specifies</del> <ins class="ins">range [0, 1] are painted.
If</ins> the <del class="del">content of</del> <ins class="ins">specified color stops cover</ins> the
<del class="del">bottom layer,</del> <ins class="ins">entire [0, 1] range (or beyond), then the
extend mode is not relevant</ins> and <del class="del">each subsequent record specifies content that overlays</del> <ins class="ins">may be ignored. If</ins> the
<del class="del">preceding content(increasing z-order). A single array</del> <ins class="ins">specified color stops do
not cover the entire [0, 1] range, the extend mode</ins> is used <del class="del">for defining all</del> <ins class="ins">to determine</ins> color <del class="del">glyphs. The LayerRecord slices</del>
<ins class="ins">values</ins> for <del class="del">two base glyphs may overlap, though
often will not overlap.

Figure 5.29 illustrates layers using version 0 formats.

![Version 0: Color glyphs are defined by slices of a layer records array.](images/colr_layers_v0.png)

**Figure 5.29 Version 0: Color glyphs are defined by slices of a layer records array.**

When using version 1 formats, use</del> <ins class="ins">the remainder</ins> of <del class="del">multiple layers is supported but is
optional.</del> <ins class="ins">that range.</ins> For example, <ins class="ins">if</ins> a <del class="del">simple glyph description need not use any layering, as
illustrated in figure 5.30:

![Complete</del> color <del class="del">glyph definition without use of layers.](images/colr_color_glyph_without_layers.png)

**Figure 5.30 Complete</del> <ins class="ins">line is
specified with two</ins> color <del class="del">glyph definition without use of layers.**

The version 1 formats define a</del> <ins class="ins">stops, red at stop offset 0.3 and yellow at stop offset
0.6, and the pad extend mode is specified, then the extend mode is used to
derive</ins> color <del class="del">glyph as</del> <ins class="ins">values from 0.0 to 0.3 (red), and from 0.6 to 1.0 (yellow).

Because</ins> a <del class="del">directed, acyclic graph of paint
tables,</del> <ins class="ins">sweep gradient is defined using start</ins> and <ins class="ins">end angles,</ins> the <del class="del">concept of layering corresponds roughly</del> <ins class="ins">gradient
does not need</ins> to <ins class="ins">cover a full 360° sweep around</ins> the <del class="del">number of
distinct leaf nodes</del> <ins class="ins">center. This is illustrated</ins>
in <del class="del">the graph. (See 5.7.11.1.9.) The basic fill
formats—PaintSolid, PaintLinearGradient</del> <ins class="ins">figure 5.30:

![A sweep gradient, from red to yellow, with start angle of 30°</ins> and <del class="del">PaintRadialGradient—do not have
child paint tables</del> <ins class="ins">an end angle of 150°.](images/colr_conic_gradient_start_stop_angles.png)

**Figure 5.30 A sweep gradient, from red to yellow, with start angle of 30°</ins> and <del class="del">so</del> <ins class="ins">an end angle of 150°.**

Start and end angle values</ins> can <del class="del">only</del> be <del class="del">leaf nodes in</del> <ins class="ins">outside</ins> the <del class="del">graph. Some paint
tables, such</del> <ins class="ins">range [0, 360), but are
interpreted</ins> as <del class="del">the PaintGlyph table, have only a single child, so can be used</del> <ins class="ins">values</ins> within <ins class="ins">that range by applying</ins> a <del class="del">layer but do not provide any means of adding additional layers.
Increasing</del> <ins class="ins">modulus operation. For
example, an angle -60° is treated</ins> the <del class="del">number of layers requires paint tables that have two or more
children, creating</del> <ins class="ins">same as 300°; an angle 480° is treated the
same as 120°. As</ins> a <del class="del">fork in</del> <ins class="ins">consequence,</ins> the <del class="del">graph.

The version 1 formats include two paint formats that have two or more children,</del> <ins class="ins">[0, 1] range of the color line covers at
most one full rotation around the center, never more.

If the starting</ins> and <del class="del">so</del> <ins class="ins">ending angle are the same, a sharp color transition</ins> can <del class="del">increase</del>
<ins class="ins">occur if</ins> the <del class="del">number of layers</del> <ins class="ins">colors at stop offsets 0 and 1 are different. This is illustrated</ins>
in <del class="del">the graph:

* The PaintComposite table allows two sub-graphs to be composed together using
different compositing or blending modes.

* The PaintColrLayers table supports defining</del> <ins class="ins">figure 5.31, showing</ins> a <del class="del">sequence of several layers.

NOTE: The PaintColrGlyph table provides</del> <ins class="ins">gradient from red to yellow that starts and stops at
0°.

![A sweep gradient, from red to yellow, with</ins> a <del class="del">means of incorporating</del> <ins class="ins">sharp transition at</ins> the <del class="del">graph of
one color glyph as</del> <ins class="ins">start/end angle 0°.](images/colr_conic_gradient_sharp_transition.png)

**Figure 5.31 A sweep gradient, from red to yellow, with</ins> a <del class="del">sub-graph in</del> <ins class="ins">sharp transition at</ins> the <del class="del">definition of another color glyph. In this
way, PaintColrGlyph provides an indirect means of introducing additional layers
into</del> <ins class="ins">start/end angle 0°.**

To avoid such</ins> a <ins class="ins">sharp transition, the stop offsets 0 and 1 on the</ins> color <del class="del">glyph definition: forks in</del> <ins class="ins">line
need to have</ins> the <del class="del">resulting graph do not come</del> <ins class="ins">same color value. Figure 5.32 illustrates a sweep gradient that
transitions</ins> from <del class="del">the
PaintColrGlyph table itself, but can come</del> <ins class="ins">red at stop offset 0, to yellow at stop offset 0.5, and back to
red at stop offset 1.0.

![A sweep gradient,</ins> from <del class="del">PaintColrLayers or PaintComposite
tables that are nested in the incorporated sub-graph. See 5.7.11.1.7.3 for</del> <ins class="ins">red to yellow to red, with</ins> a
<del class="del">description of the PaintColrGlyph table.

While</del> <ins class="ins">smooth transition at</ins> the <del class="del">PaintComposite table only combines two sub-graphs, other
PaintComposite tables can be nested</del> <ins class="ins">start/end angle 0°.](images/colr_conic_gradient_rotation-0.png)

**Figure 5.32 A sweep gradient, from red</ins> to <del class="del">provide additional layers. The primary
purpose of PaintComposite is</del> <ins class="ins">yellow</ins> to <del class="del">support compositing or blending modes other than
simple alpha blending. The PaintComposite table is covered in more detail in
5.7.11.1.6. The remainder of this clause will focus on</del> <ins class="ins">red, with a smooth transition at</ins> the <del class="del">PaintColrLayers
table.

The PaintColrLayers table</del> <ins class="ins">start/end angle 0°.**

NOTE: When a sweep gradient</ins> is <del class="del">used to define</del> <ins class="ins">combined with</ins> a <del class="del">bottom-up z-order sequence of
layers. Similar to version 0, it defines a layer set as a slice in an array, but
in this case the array is an array of offsets to paint tables, contained in a
LayerV1List table. Each referenced paint table is</del> <ins class="ins">transformation (see 5.7.11.1.5),</ins>
the <del class="del">root of a sub-graph of
paint tables that specifies a graphic composition to</del> <ins class="ins">appearance will</ins> be <del class="del">used</del> <ins class="ins">the same</ins> as <ins class="ins">if</ins> a <del class="del">layer. Within
a given slice, the first offset provides</del> <ins class="ins">circular arc of some non-zero radius
were computed from</ins> the <del class="del">content for</del> <ins class="ins">start and end angles;</ins> the <del class="del">bottom layer,</del> <ins class="ins">center point</ins> and
<del class="del">each subsequent offset provides content that overlays</del> <ins class="ins">arc
transformed;</ins> the <del class="del">preceding content.
Definition of a layer set—a slice within</del> <ins class="ins">color line aligned to</ins> the <del class="del">layer list—is given in</del> <ins class="ins">transformed arc; and then</ins> a
<del class="del">PaintColorLayers table.

Figure 5.31 illustrates the organizational relationship between PaintColorLayers
tables,</del> <ins class="ins">gradient
derived from</ins> the <del class="del">LayerV1List, and referenced paint tables that are roots of
sub-graphs.

![Version 1: PaintColrLayers tables specify slices within</del> <ins class="ins">result, with rays from</ins> the <del class="del">LayerV1List, providing a layering of content defined in sub-graphs.](images/colr_layers_v1.png)

**Figure 5.31 Version 1: PaintColrLayers tables specify slices within</del> <ins class="ins">transformed center point passing
through</ins> the <del class="del">LayerV1List, providing a layering of content defined in sub-graphs.**

NOTE: Paint table offsets in</del> <ins class="ins">transformed color arc. When aligning</ins> the <del class="del">LayerV1List table are only used in conjuction
with PaintColrLayers tables. If a paint table does not need</del> <ins class="ins">color line</ins> to <ins class="ins">the
transformed arc, stop offset 0 would</ins> be <del class="del">referenced via
a PaintColrLayers table, its</del> <ins class="ins">aligned to the transformed point derived
from the start angle, with stop</ins> offset <del class="del">does not need</del> <ins class="ins">1 aligned</ins> to <del class="del">be included in</del> the
<del class="del">LayerV1List array.

A PaintColorLayers table can be used as</del> <ins class="ins">transformed point
derived from</ins> the <del class="del">root of</del> <ins class="ins">end angle. Thus,</ins> a <ins class="ins">transform can result in the</ins> color <del class="del">glyph definition,
providing</del> <ins class="ins">line
progressing in</ins> a <del class="del">base layering structure</del> <ins class="ins">clockwise rather than counter-clockwise direction.

Sweep gradients are specified using a PaintVarSweepGradient or
PaintSweepGradient table, with or without variation support, respectively. See
5.7.11.2.5.5</ins> for <del class="del">the</del> <ins class="ins">format details.

See 5.7.11.1.3 for details on how fills are applied to a shape.

**5.7.11.1.3 Filling shapes**

All basic shapes used in a</ins> color <del class="del">glyph. In this usage, the
PaintColrLayers table is</del> <ins class="ins">glyph are obtained from glyph outlines,</ins>
referenced <del class="del">by</del> <ins class="ins">using</ins> a <del class="del">BaseGlyphV1Record, which specifies the
root of the graph of</del> <ins class="ins">glyph ID. In</ins> a color glyph <del class="del">definition for</del> <ins class="ins">description,</ins> a <del class="del">given base glyph. This is
illustrated in figure 5.32.

![PaintColrLayers</del> <ins class="ins">PaintGlyph</ins> table <ins class="ins">is</ins>
used <ins class="ins">to represent a basic shape. 

NOTE: Shapes can also be derived using PaintGlyph tables in combination with
other tables, such</ins> as <del class="del">the root of</del> <ins class="ins">PaintTransform (see 5.7.11.1.5) or PaintComposite (see
5.7.11.1.6).

The PaintGlyph table has</ins> a <del class="del">color</del> <ins class="ins">field for the</ins> glyph <del class="del">definition.](images/colr_PaintColrLayers_as_root.png)

**Figure 5.32 PaintColrLayers</del> <ins class="ins">ID, plus an offset to a child
paint</ins> table <ins class="ins">that is</ins> used as the <del class="del">root of a color</del> <ins class="ins">fill for the shape. The</ins> glyph <del class="del">definition.**

A PaintColorLayers table</del> <ins class="ins">outline is not
rendered; only the fill is rendered.

Any of the basic fill formats (PaintSolid, PaintVarSolid, PaintLinearGradient,
PaintVarLinearGradient, PaintRadialGradient, PaintVarRadialGradient,
PaintSweepGradient, PaintVarSweepGradient)</ins> can <del class="del">also</del> be <del class="del">nested more deeply within</del> <ins class="ins">used as</ins> the <del class="del">graph,
providing</del> <ins class="ins">child paint table.
This is illustrated in figure 5.33:</ins> a <del class="del">layer structure to define some component within</del> <ins class="ins">PaintGlyph table has</ins> a <del class="del">larger color</del> glyph
<del class="del">definition. (See 5.7.11.1.7.2</del> <ins class="ins">ID</ins> for <del class="del">more information.) The ability</del> <ins class="ins">an
outline in the shape of a triangle, and it links</ins> to <del class="del">nest</del> a
<del class="del">PaintColrLayers table within</del> <ins class="ins">child PaintLinearGradient
table. The combination is used to represent</ins> a <del class="del">graph creates</del> <ins class="ins">triangle filled with</ins> the <del class="del">potential</del> <ins class="ins">linear
gradient.

![PaintGlyph and PaintLinearGradient tables are used</ins> to <del class="del">introduce</del> <ins class="ins">fill</ins> a <del class="del">cycle
within the graph, which would be invalid (see 5.7.11.1.9).

**5.7.11.1.5 Transformations**

A PaintTransform table can be used within</del> <ins class="ins">triangle shape with</ins> a <del class="del">color glyph description</del> <ins class="ins">linear gradient.](images/colr_shape_gradient.png)

**Figure 5.33 PaintGlyph and PaintLinearGradient tables used</ins> to <del class="del">apply an
affine transformation matrix. Transformations supported by</del> <ins class="ins">fill</ins> a <del class="del">matrix can be</del> <ins class="ins">shape with</ins> a
<del class="del">combination of scale, skew, mirror, rotate, or translate.</del> <ins class="ins">linear gradient.**</ins>

The <del class="del">transformation is
applied to all nested paints in the</del> child <del class="del">sub-graph.

The effect</del> of a <del class="del">PaintTransform</del> <ins class="ins">PaintGlyph</ins> table is <del class="del">illustrated in figure 5.33:</del> <ins class="ins">not, however, limited to one of the basic
fill formats. Rather, the child can be the root of</ins> a
<del class="del">PaintTransform</del> <ins class="ins">sub-graph that describes
some graphic composition that</ins> is used <ins class="ins">as a fill. Another way</ins> to <del class="del">specify</del> <ins class="ins">describe the
relationship between</ins> a <del class="del">rotation,</del> <ins class="ins">PaintGlyph table</ins> and <del class="del">both</del> <ins class="ins">its child sub-graph is that</ins> the
glyph outline <del class="del">and
gradient in the sub-graph are rotated.

![A rotation transformation rotates the fill content defined</del> <ins class="ins">specified</ins> by the <del class="del">child sub-graph.](images/colr_transform_glyph_gradient.png)

**Figure 5.33 A rotation transformation rotates</del> <ins class="ins">PaintGlyph table defines a bounds, or *clip
region*, that is applied to</ins> the fill <del class="del">content</del> <ins class="ins">composition</ins> defined by the child <del class="del">sub-graph.**

If another PaintTransform table occurs within the child sub-graph of the first
PaintTransform table, then the other PaintTransform also applies to its child</del> sub-graph. <del class="del">For the sub-sub-graph, the two transformations are combined.</del>

To illustrate this, the example in figure 5.33 is extended in figure 5.34 <del class="del">by
inserting</del> <ins class="ins">so
that</ins> a <del class="del">mirroring transformation between the</del> PaintGlyph <del class="del">and
PaintLinearGradient tables:</del> <ins class="ins">table links to a second PaintGlyph that links to a
PaintLinearGradient:</ins> the <del class="del">glyph outline is rotated as before, but</del> <ins class="ins">parent PaintGlyph will clip</ins> the
<del class="del">gradient is mirrored in its (pre-rotation) y-axis as well as being rotated.
Notice that both visible elements—the</del> <ins class="ins">filled</ins> shape <del class="del">and the gradient fill—are affected</del> <ins class="ins">described</ins>
by the <del class="del">rotation, but only</del> <ins class="ins">child sub-graph.

![A PaintGlyph table defines a clip region for</ins> the <del class="del">gradient is affected</del> <ins class="ins">composition defined</ins> by <del class="del">the mirroring.

![Combined effects of a transformation nested within the</del> <ins class="ins">its</ins> child <del class="del">sub-graph of another transformation.](images/colr_transform_glyph_transform_gradient.png)</del> <ins class="ins">sub-graph.](images/colr_shape_shape_gradient.png)</ins>

**Figure 5.34 <del class="del">Combined effects of</del> <ins class="ins">A PaintGlyph table defines</ins> a <del class="del">transformation nested within</del> <ins class="ins">clip region for</ins> the <ins class="ins">composition defined by its</ins> child <del class="del">sub-graph of another transformation.**

The affine transformation is specified in a PaintTransform</del> <ins class="ins">sub-graph.**

A PaintGlyph</ins> table <del class="del">as matrix
elements.</del> <ins class="ins">on its own does not add content: if there is no child paint
table, then the graph is not well formed.</ins> See <del class="del">5.7.11.2.5.7</del> <ins class="ins">5.7.11.1.9</ins> for <del class="del">format details.

Whereas</del> <ins class="ins">details regarding
well-formedness and validity of</ins> the <del class="del">PaintTransformed table supports several types</del> <ins class="ins">graph.

**5.7.11.1.4 Layering**

Layering</ins> of <del class="del">transforms,</del> <ins class="ins">visual elements was introduced above, in</ins> the
<del class="del">PaintTranslate, PaintRotate</del> <ins class="ins">introduction to
5.7.11.1. Both version 0</ins> and <del class="del">PaintSkew tables</del> <ins class="ins">version 1</ins> support <del class="del">specific
transformations: translation, rotation and skew. The PaintTranslate table
provides a more compact representation for this common transform. The
significant difference</del> <ins class="ins">use</ins> of <ins class="ins">multiple layers, though in
different ways.

For version 0, layers are fundamental: they are</ins> the <del class="del">PaintRotate</del> <ins class="ins">sole way in which separate
elements are composed into a color glyph. An array of LayerRecords is created,
with each LayerRecord specifying a glyph ID</ins> and <del class="del">PaintSkew formats</del> <ins class="ins">a CPAL entry (a shape and solid
color fill). Each color glyph definition</ins> is <ins class="ins">a slice from</ins> that
<del class="del">rotations and skews are</del> <ins class="ins">array (that is, a
contiguous sub-sequence),</ins> specified <del class="del">as angles,</del> in <del class="del">counter-clockwise degrees.

NOTE: Specifying the rotation or skew as an angle can have</del> a <del class="del">signficant benefit
in variable fonts if an angle of skew or rotation needs to vary, since it is
easier to implement variation of angles when specified directly rather than as
matrix elements. This is because the matrix elements</del> <ins class="ins">BaseGlyphRecord</ins> for a <del class="del">rotation or skew are
the sine, cosine or tangent of</del> <ins class="ins">particular base
glyph. Within a given slice,</ins> the <del class="del">rotation angle, which do not change in linear
proportion to</del> <ins class="ins">first record specifies</ins> the <del class="del">angle. To achieve a linear variation</del> <ins class="ins">content</ins> of <del class="del">rotation using matrix
elements would require approximating</del> the <del class="del">variation using multiple delta sets.

**5.7.11.1.6 Compositing</del>
<ins class="ins">bottom layer,</ins> and <del class="del">blending**

When a color glyph has overlapping</del> <ins class="ins">each subsequent record specifies</ins> content <del class="del">in two layers, the pixels in</del> <ins class="ins">that overlays</ins> the
<ins class="ins">preceding content (increasing z-order). A single array is used for defining all
color glyphs. The LayerRecord slices for</ins> two <ins class="ins">base glyphs may overlap, though
often will not overlap.

Figure 5.35 illustrates</ins> layers <del class="del">must be combined in some way. If the content in the top layer has full
opacity, then normally the pixels from that</del> <ins class="ins">using version 0 formats.

![Version 0: Color glyphs are defined by slices of a</ins> layer <ins class="ins">records array.](images/colr_layers_v0.png)

**Figure 5.35 Version 0: Color glyphs</ins> are <del class="del">shown, occluding
overlapping pixels from lower layers. If the top</del> <ins class="ins">defined by slices of a</ins> layer <del class="del">has some transparency
(some portion has alpha less than 1.0), then blending</del> <ins class="ins">records array.**

When using version 1 formats, use</ins> of <del class="del">colors for overlapping
pixels occurs by default. The default interaction between</del> <ins class="ins">multiple</ins> layers <del class="del">uses</del> <ins class="ins">is supported but is
optional. For example, a</ins> simple
<del class="del">alpha compositing,</del> <ins class="ins">glyph description need not use any layering,</ins> as <del class="del">described</del>
<ins class="ins">illustrated</ins> in <del class="del">[Compositing and Blending Level 1][1].

A PaintComposite table can be used to get other compositing or blending effects.</del> <ins class="ins">figure 5.36:

![Complete color glyph definition without use of layers.](images/colr_color_glyph_without_layers.png)

**Figure 5.36 Complete color glyph definition without use of layers.**</ins>

The <del class="del">PaintComposite table combines content defined by two sub-graphs: a *source*
sub-graph; and</del> <ins class="ins">version 1 formats define</ins> a <del class="del">destination, or *backdrop*, sub-graph. First, the</del> <ins class="ins">color glyph as a directed, acyclic graph of</ins> paint
<del class="del">operations for</del>
<ins class="ins">tables, and</ins> the <del class="del">backdrop sub-graph are executed, then</del> <ins class="ins">concept of layering corresponds roughly to</ins> the <del class="del">drawing operations
for</del> <ins class="ins">number of
distinct leaf nodes in</ins> the <del class="del">source sub-graph are executed</del> <ins class="ins">graph. (See 5.7.11.1.9.) The basic fill formats
(PaintSolid, PaintVarSolid, PaintLinearGradient, PaintVarLinearGradient,
PaintRadialGradient, PaintVarRadialGradient, PaintSweepGradient,
PaintVarSweepGradient) do not have child paint tables</ins> and <del class="del">combined with backdrop using</del> <ins class="ins">so can only be leaf
nodes in the graph. Some paint tables, such as the PaintGlyph table, have only</ins> a
<del class="del">specified compositing</del>
<ins class="ins">single child, so can be used within a layer but do not provide any means of
adding additional layers. Increasing the number of layers requires paint tables
that have two</ins> or <del class="del">blending mode. The available modes are given</del> <ins class="ins">more children, creating a fork</ins> in the
<del class="del">CompositionModes enumeration (see 5.7.11.2.5.11).</del> <ins class="ins">graph.</ins>

The <del class="del">effect</del> <ins class="ins">version 1 formats include two paint formats that have two or more children,</ins>
and <del class="del">processing rule</del> <ins class="ins">so can increase the number</ins> of <del class="del">each mode are specified</del> <ins class="ins">layers</ins> in <del class="del">[Compositing and Blending Level 1][1].</del> <ins class="ins">the graph:

*</ins> The <del class="del">available modes fall into</del> <ins class="ins">PaintComposite table allows</ins> two <del class="del">general types: compositing modes, also
referred</del> <ins class="ins">sub-graphs</ins> to <del class="del">as “Porter-Duff” modes; and</del> <ins class="ins">be composed together using
different compositing or</ins> blending modes. <del class="del">In rough terms, the
Porter-Duff modes determine how much effect pixels from the source and</del>

<ins class="ins">* The PaintColrLayers table supports defining a sequence of several layers.

NOTE: The PaintColrGlyph table provides a means of incorporating</ins> the
<del class="del">backdrop each contribute</del> <ins class="ins">graph of
one color glyph as a sub-graph</ins> in the <del class="del">result, while blending modes determine how</del> <ins class="ins">definition of another</ins> color
<del class="del">values for pixels from the source and backdrop are combined. These are
illustrated with examples in figures 5.35 and 5.36: in each case, red and blue
rectangles are the source and backdrop content.

Figure 5.35 shows the effect of a Porter-Duff mode, “XOR”, which has the effect
that only non-overlapping pixels contribute to the result.

![Two content elements combined using the Porter-Duff XOR mode.](images/colr_porter-duff_xor.png)

**Figure 5.35 Two content elements combined using the Porter-Duff XOR mode.**

Figure 5.36 shows the effect</del> <ins class="ins">glyph. In this
way, PaintColrGlyph provides an indirect means</ins> of <ins class="ins">introducing additional layers
into</ins> a <del class="del">“lighten” blending mode, which has the effect
that the R, G, and B</del> color <del class="del">components for each pixel</del> <ins class="ins">glyph definition: forks</ins> in the <del class="del">result is the
greater of</del> <ins class="ins">resulting graph do not come from</ins> the <del class="del">R, G, and B values</del>
<ins class="ins">PaintColrGlyph table itself, but can come</ins> from <del class="del">corresponding pixels</del> <ins class="ins">PaintColrLayers or PaintComposite
tables that are nested</ins> in the <del class="del">source and
backdrop.

![Two content elements combined using the lighten blending mode.](images/colr_blend_lighten.png)

**Figure 5.36 Two content elements combined using the lighten blending mode.**

For complete details on each</del> <ins class="ins">incorporated sub-graph. See 5.7.11.1.7.3 for a
description</ins> of the <del class="del">Porter-Duff and blending modes, see the
[Compositing and Blending Level 1][1] specification.

Figure 5.37 illustrates how</del> <ins class="ins">PaintColrGlyph table.

While</ins> the PaintComposite table <ins class="ins">only combines two sub-graphs, other
PaintComposite tables can be nested to provide additional layers. The primary
purpose of PaintComposite</ins> is <del class="del">used in combination with
content sub-graphs</del> to <del class="del">implement an alternate</del> <ins class="ins">support</ins> compositing <del class="del">effect.</del> <ins class="ins">or blending modes other than
simple alpha blending.</ins> The <del class="del">source
sub-graph defines a green capital A;</del> <ins class="ins">PaintComposite table is covered in more detail in
5.7.11.1.6. The remainder of this clause will focus on</ins> the <del class="del">backdrop sub-graph defines a black
circle.</del> <ins class="ins">PaintColrLayers
table.</ins>

The <del class="del">compositing mode used</del> <ins class="ins">PaintColrLayers table</ins> is <del class="del">“source out”, which has the effect that the
source content punches out</del> <ins class="ins">used to define</ins> a <del class="del">hole in the backdrop. (For this mode, the fill
color</del> <ins class="ins">bottom-up z-order sequence</ins> of <del class="del">the source is irrelevant;</del>
<ins class="ins">layers. Similar to version 0, it defines</ins> a <del class="del">black or yellow "A" would have the same
effect.) A red rectangle is included</del> <ins class="ins">layer set</ins> as a <del class="del">lower layer to show that the backdrop
has been punched out by</del> <ins class="ins">slice in an array, but
in this case</ins> the <del class="del">source, making that portion</del> <ins class="ins">array is an array</ins> of <del class="del">the lower layer
visible.

![A color glyph using a PaintComposite table</del> <ins class="ins">offsets</ins> to <del class="del">punch out</del> <ins class="ins">paint tables, contained in</ins> a <del class="del">shape from</del>
<ins class="ins">LayerV1List table. Each referenced paint table is</ins> the <del class="del">fill</del> <ins class="ins">root</ins> of a <del class="del">circle.](images/colr_PaintCompositeGraph.png)

**Figure 5.37 A color glyph using</del> <ins class="ins">sub-graph of
paint tables that specifies</ins> a <del class="del">PaintComposite table</del> <ins class="ins">graphic composition</ins> to <del class="del">punch out</del> <ins class="ins">be used as</ins> a <del class="del">shape from the fill of</del> <ins class="ins">layer. Within</ins>
a <del class="del">circle.**

NOTE: In figure 5.37, the “A” is filled with green to illustrate that</del> <ins class="ins">given slice,</ins> the <del class="del">color
of</del> <ins class="ins">first offset provides</ins> the <del class="del">fill has no affect</del> <ins class="ins">content</ins> for the <del class="del">source out composite mode. Because</del> <ins class="ins">bottom layer, and
each subsequent offset provides content</ins> that <del class="del">is the
case,</del> <ins class="ins">overlays</ins> the <del class="del">black or red PaintSolid could have been re-used instead</del> <ins class="ins">preceding content.
Definition</ins> of <del class="del">adding</del> a
<del class="del">separate PaintSolid table. See 5.7.11.1.7.1 for more information on re-use of
paint tables for such situations.

[Scalable Vector Graphics (SVG)][31] supports alpha channel masking using</del> <ins class="ins">layer set—a slice within</ins> the
<del class="del">&lt;mask&gt; element. The same effects can be implemented</del> <ins class="ins">layer list—is given</ins> in <del class="del">COLR version 1
using a PaintComposite table by setting</del> a <del class="del">pattern of alpha values in</del>
<ins class="ins">PaintColorLayers table.

Figure 5.37 illustrates</ins> the <del class="del">source
sub-graph</del> <ins class="ins">organizational relationship between PaintColorLayers
tables, the LayerV1List,</ins> and <del class="del">selecting</del> <ins class="ins">referenced paint tables that are roots of
sub-graphs.

![Version 1: PaintColrLayers tables specify slices within</ins> the <del class="del">source</del> <ins class="ins">LayerV1List, providing a layering of content defined</ins> in <del class="del">composite mode. This is illustrated</del> <ins class="ins">sub-graphs.](images/colr_layers_v1.png)

**Figure 5.37 Version 1: PaintColrLayers tables specify slices within the LayerV1List, providing a layering of content defined</ins> in
<del class="del">figure 5.38.

![A PaintComposite</del> <ins class="ins">sub-graphs.**

NOTE: Paint</ins> table <del class="del">using</del> <ins class="ins">offsets in</ins> the <del class="del">source</del> <ins class="ins">LayerV1List table are only used</ins> in <del class="del">mode</del> <ins class="ins">conjunction
with PaintColrLayers tables. If a paint table does not need</ins> to <del class="del">implement an alpha mask.](images/colr_gradient_mask.png)

**Figure 5.38 An alpha mask implemented using</del> <ins class="ins">be referenced via</ins>
a <del class="del">PaintComposite</del> <ins class="ins">PaintColrLayers table, its offset does not need to be included in the
LayerV1List array.

A PaintColorLayers</ins> table <del class="del">and</del> <ins class="ins">can be used as</ins> the <del class="del">source in mode.**

**5.7.11.1.7 Re-usable components**

Within</del> <ins class="ins">root of</ins> a color <del class="del">font, many</del> <ins class="ins">glyph definition,
providing a base layering structure for the</ins> color <del class="del">glyphs might share components in common. For example, in emoji fonts, many different “smilies” or clock faces share</del> <ins class="ins">glyph. In this usage, the
PaintColrLayers table is referenced by</ins> a <del class="del">common background.</del> <ins class="ins">BaseGlyphV1Record, which specifies the
root of the graph of a color glyph definition for a given base glyph.</ins> This <del class="del">can be seen</del> <ins class="ins">is
illustrated</ins> in figure <del class="del">5.39, which shows</del> <ins class="ins">5.38.

![PaintColrLayers table used as the root of a</ins> color <del class="del">glyphs for three emoji clock faces.

![Emoji clock faces for 12 o’clock, 1 o’clock and 2 o’clock.](images/colr_clocks-12-1-2.png)</del> <ins class="ins">glyph definition.](images/colr_PaintColrLayers_as_root.png)</ins>

**Figure <del class="del">5.39 Emoji clock faces for 12 o’clock, 1 o’clock and 2 o’clock.**

Several components are shared between these color glyphs:</del> <ins class="ins">5.38 PaintColrLayers table used as</ins> the <del class="del">entire face, with
a gradient background and dots at the 3, 6, 9 and 12 positions; the minute hand
pointing to the 12 position; and the circles in the center. Also, note that the
four dots have the same shape and fill, and differ only in their position. In
addition, the hour hands have the same shape and fill, and differ only in their
orientation.

There are several ways in which elements of</del> <ins class="ins">root of</ins> a color glyph <del class="del">description</del> <ins class="ins">definition.**

A PaintColorLayers table</ins> can <ins class="ins">also</ins> be
<del class="del">re-used:

* Reference to shared subtables
* Use of</del> <ins class="ins">nested more deeply within the graph,
providing</ins> a <del class="del">PaintColrLayers table
* Use of</del> <ins class="ins">layer structure to define some component within</ins> a <del class="del">PaintColrGlyph table</del> <ins class="ins">larger color glyph
definition. (See 5.7.11.1.7.2 for more information.)</ins> The <ins class="ins">ability to nest a</ins>
PaintColrLayers <del class="del">and PaintColrGlyph</del> table <del class="del">formats create</del> <ins class="ins">within</ins> a <ins class="ins">graph creates the</ins> potential <del class="del">for
introducing cycles</del> <ins class="ins">to introduce a cycle</ins>
within the <del class="del">graph of a color glyph,</del> <ins class="ins">graph,</ins> which would be invalid (see 5.7.11.1.9).

<del class="del">**5.7.11.1.7.1 Re-use by referencing shared subtables**

Several of the paint table formats link to a child paint table using a forward
offset within the file: PaintGlyph, PaintComposite, PaintTransformed,
PaintRotate, and PaintSkew.</del>

<ins class="ins">**5.7.11.1.5 Transformations**</ins>

A <del class="del">child subtable</del> <ins class="ins">2 × 3 transformation matrix</ins> can be <del class="del">shared by several tables of
these formats. For example, several PaintGlyph tables might link</del> <ins class="ins">used within a color glyph description</ins> to <del class="del">the same
PaintSolid table, or</del>
<ins class="ins">apply an affine transformation</ins> to <del class="del">the same node for</del> a sub-graph <del class="del">describing</del> <ins class="ins">in the color glyph description.
Affine transformations supported by</ins> a <del class="del">more complex
fill.</del> <ins class="ins">matrix can be a combination of scale,
skew, mirror, rotate, or translate.</ins> The <del class="del">only limitation</del> <ins class="ins">transformation</ins> is <del class="del">that</del> <ins class="ins">applied to all nested
paints in the</ins> child <del class="del">paint tables are referenced</del> <ins class="ins">sub-graph.

A transformation matrix is specified</ins> using a
<del class="del">forward offset from the start of the referencing</del> <ins class="ins">PaintVarTransform or PaintTransform</ins>
table, <del class="del">so</del> <ins class="ins">with or without variation support, respectively. See 5.7.11.2.5.8 for
format details.

The effect of</ins> a <del class="del">re-used paint</del> <ins class="ins">transformation is illustrated in figure 5.39: a PaintTransform</ins>
table
<del class="del">can only occur later</del> <ins class="ins">is used to specify a rotation, and both the glyph outline and gradient</ins> in
the <del class="del">file than any of</del> <ins class="ins">sub-graph are rotated.

![A rotation transformation rotates</ins> the <del class="del">paint tables that use it.

The clock faces shown in figure</del> <ins class="ins">fill content defined by the child sub-graph.](images/colr_transform_glyph_gradient.png)

**Figure</ins> 5.39 <del class="del">provide an example of how PaintRotate
tables can be combined with re-use</del> <ins class="ins">A rotation transformation rotates the fill content defined by the child sub-graph.**

If the sub-graph</ins> of a <ins class="ins">transformation table contains another nested
transformation table, then the second transformation also applies to its child</ins>
sub-graph. <del class="del">As noted above,</del> <ins class="ins">For</ins> the <del class="del">hour
hands have</del> <ins class="ins">sub-sub-graph,</ins> the <del class="del">same shape and fill, but have</del> <ins class="ins">two transformations are combined. To
illustrate this, the example in figure 5.39 is extended in figure 5.40 by
inserting</ins> a <del class="del">different orientation. The</del> <ins class="ins">mirroring transformation between the PaintGlyph and
PaintLinearGradient tables: the</ins> glyph outline <del class="del">could point to</del> <ins class="ins">is rotated as before, but</ins> the <del class="del">12 position, then</del>
<ins class="ins">gradient is mirrored</ins> in <del class="del">color glyph descriptions for
other times, PaintRotate tables could link to the same glyph/fill sub-graph,
re-using</del> <ins class="ins">its (pre-rotation) y-axis as well as being rotated.
Notice</ins> that <del class="del">component</del> <ins class="ins">both visible elements—the shape and the gradient fill—are affected
by the rotation,</ins> but <del class="del">rotated as needed.

This</del> <ins class="ins">only the gradient</ins> is <del class="del">illustrated in</del> <ins class="ins">affected by</ins> the <del class="del">figures 5.40 and 5.41. Figure</del> <ins class="ins">mirroring.

![Combined effects of a transformation nested within the child sub-graph of another transformation.](images/colr_transform_glyph_transform_gradient.png)

**Figure</ins> 5.40 <del class="del">shows</del> <ins class="ins">Combined effects of</ins> a <ins class="ins">transformation nested within the child</ins> sub-graph
<del class="del">defining</del> <ins class="ins">of another transformation.**

While</ins> the <del class="del">hour hand,</del> <ins class="ins">PaintTransform and PaintVarTransform tables support several types of
transforms, addition paint formats are defined to support specific
transformations:

* PaintVarTranslate and PaintTranslate support translation only,</ins> with <del class="del">upright orientation, using a PaintGlyph</del> <ins class="ins">or without
variation support, respectively. See 5.7.11.2.5.9 for format details.

* PaintVarRotate</ins> and <del class="del">a
PaintSolid table. Example file offsets</del> <ins class="ins">PaintRotate support rotation only, with or without
variation support, respectively. See 5.7.11.2.5.10 for format details.

* PaintVarSkew and PaintSkew support skew only, with or without variation
support, respectively. See 5.7.11.2.5.11</ins> for <ins class="ins">format details.

When only one of these specific types of transformation is required, these
formats provide a more compact representation than</ins> the <del class="del">tables are indicated.

![A PaintGlyph</del> <ins class="ins">PaintTransform or
PaintVarTransform formats. Another significant difference of the rotation</ins> and <del class="del">PaintSolid table are used to define</del>
<ins class="ins">skew formats is that</ins> the <del class="del">clock hour hand pointing to 12.](images/colr_hour-hand-component.png)

**Figure 5.40 A PaintGlyph</del> <ins class="ins">rotations</ins> and <del class="del">PaintSolid table</del> <ins class="ins">skews</ins> are <del class="del">used to define the clock hour hand pointing to 12.**

Figure 5.41 shows this sub-graph of paint tables being re-used,</del> <ins class="ins">specified as angles,</ins> in <del class="del">some cases
linked from PaintRotate tables that rotate</del>
<ins class="ins">counter-clockwise degrees.

NOTE: Specifying</ins> the <del class="del">hour hand</del> <ins class="ins">rotation or skew as an angle can have a signficant benefit
in variable fonts if an angle of skew or rotation needs</ins> to <del class="del">point</del> <ins class="ins">vary, since it is
easier</ins> to <del class="del">different
clock positions</del> <ins class="ins">implement variation of angles when specified directly rather than</ins> as <del class="del">needed. All</del>
<ins class="ins">matrix elements. This is because the matrix elements for a rotation or skew are
the sine, cosine or tangent</ins> of the <del class="del">paint tables that reference this sub-graph
occur earlier</del> <ins class="ins">rotation angle, which do not change</ins> in <ins class="ins">linear
proportion to</ins> the <del class="del">file.

![The sub-graph for</del> <ins class="ins">angle. To achieve a linear variation of rotation using matrix
elements would require approximating</ins> the <del class="del">hour hand is re-used with PaintRotate tables to point to different hours.](images/colr_reuse-hour-hand-rotated.png)

**Figure 5.41</del> <ins class="ins">variation using multiple delta sets.</ins>

The <del class="del">sub-graphs for the hour hand are re-used with PaintRotate tables to point to different hours.**

**5.7.11.1.7.2 Re-use</del> <ins class="ins">rotations and skews specified</ins> using <del class="del">PaintColrLayers**

As described above (see 5.7.11.1.4),</del> <ins class="ins">PaintRotate, PaintVarRotate, PaintSkew,
or PaintVarSkew tables can also be representated as</ins> a <del class="del">PaintColrLayers table defines</del> <ins class="ins">matrix using</ins> a <del class="del">set of
paint sub-graphs arranged in bottom-up z-order layers, and an example was given
of</del>
<ins class="ins">PaintTransform or PaintVarTransform table. If</ins> a <del class="del">PaintColrLayers</del> <ins class="ins">PaintRotate, PaintVarRotate,
PaintSkew, or PaintVarSkew</ins> table <ins class="ins">is</ins> used <del class="del">as the root of</del> <ins class="ins">in combination with</ins> a <del class="del">color glyph definition. A
PaintColrLayers table can also</del> <ins class="ins">PaintTransform or
PaintVarTransform table, the combined behavior shall</ins> be <del class="del">nested more deeply within</del> the <del class="del">graph of a color
glyph. One purpose for doing this is to reference a re-usable component defined</del> <ins class="ins">same</ins> as <del class="del">a contiguous set of layers in</del> <ins class="ins">if</ins> the <del class="del">LayersV1List table.

This is readily explained</del>
<ins class="ins">rotation or skew were represented</ins> using <ins class="ins">an equivalent matrix. See 5.7.11.2.5.10
for details regarding</ins> the <del class="del">clock faces</del> <ins class="ins">matrix equivalent for a rotation expressed</ins> as an <del class="del">example. As described
above, each clock face shares several elements</del>
<ins class="ins">angle; and see 5.7.11.2.5.11 for similar details</ins> in <del class="del">common. Some of these form</del> <ins class="ins">relation to skews.

**5.7.11.1.6 Compositing and blending**

When</ins> a
<del class="del">contiguous set of layers. Suppose four sub-graphs for shared clock face elements
are given</del> <ins class="ins">color glyph has overlapping content</ins> in <del class="del">the LayerV1List as contiguous</del> <ins class="ins">two</ins> layers, <del class="del">as shown</del> <ins class="ins">the pixels</ins> in <del class="del">figure 5.42. (For
brevity,</del> the <del class="del">visual result for each sub-graph is shown, but not</del> <ins class="ins">two
layers must be combined in some way. If</ins> the <del class="del">paint
details.)

![Common clock face elements given as a slice within</del> <ins class="ins">content in</ins> the <del class="del">LayerV1List table.](images/colr_clock_common.png)

**Figure 5.42 Common clock face elements given as a slice within the LayerV1List table.**

A PaintColrLayers table can reference any contiguous slice of layers in</del> <ins class="ins">top layer has full
opacity, then normally</ins> the
<del class="del">LayerV1List table. Thus,</del> <ins class="ins">pixels from that layer are shown, occluding
overlapping pixels from lower layers. If</ins> the <del class="del">set</del> <ins class="ins">top layer has some transparency
(some portion has alpha less than 1.0), then blending</ins> of <del class="del">layers shown in figure 5.42 can be
referenced</del> <ins class="ins">colors for overlapping
pixels occurs</ins> by <del class="del">PaintColrLayers tables anywhere in the graph of any color glyph.
In this way, this set of</del> <ins class="ins">default. The default interaction between</ins> layers <ins class="ins">uses simple
alpha compositing, as described in [Compositing and Blending Level 1][1].

A PaintComposite table</ins> can be <del class="del">re-used in multiple clock face color
glyph definitions.

This is illustrated in figure 5.43:</del> <ins class="ins">used to get other compositing or blending effects.</ins>
The <del class="del">color glyph definition for the one
o’clock emoji has a PaintColrLayers</del> <ins class="ins">PaintComposite</ins> table <del class="del">as its root, referencing a slice of
three layers in the LayerV1List table. The upper</del> <ins class="ins">combines content defined by</ins> two <del class="del">layers are the hour hand,
which is specific to this color glyph;</del> <ins class="ins">sub-graphs: a *source*
sub-graph;</ins> and <ins class="ins">a destination, or *backdrop*, sub-graph. First,</ins> the <del class="del">cap over the pivot</del> <ins class="ins">paint
operations</ins> for the <del class="del">minute
and hour hands, which is common to other clock emoji but in a layer that is not
contiguous with other common layers. The bottom layer of these three layers is</del> <ins class="ins">backdrop sub-graph are executed, then</ins> the <del class="del">composition</del> <ins class="ins">drawing operations</ins>
for <del class="del">all</del> the <del class="del">remaining common layers. It is represented</del> <ins class="ins">source sub-graph are executed and combined with backdrop</ins> using a
<del class="del">nested PaintColrLayers table that references</del>
<ins class="ins">specified compositing or blending mode. The available modes are given in</ins> the <del class="del">slice within</del>
<ins class="ins">CompositionModes enumeration (see 5.7.11.2.5.12). The effect and processing rule
of each mode are specified in [Compositing and Blending Level 1][1].

The available modes fall into two general types: compositing modes, also
referred to as “Porter-Duff” modes; and blending modes. In rough terms,</ins> the <del class="del">LayerV1List
for</del>
<ins class="ins">Porter-Duff modes determine how much effect pixels from</ins> the <del class="del">common clock face elements shown</del> <ins class="ins">source and the
backdrop each contribute</ins> in <del class="del">figure 5.42.

![A PaintColrLayers table is used to reference a set of layers that define a shared clock face composition.](images/colr_reuse_clock-face_PaintColrLayers.png)

**Figure 5.43 A PaintColrLayers table is used to reference a set of layers that define a shared clock face composition.**

The</del> <ins class="ins">the result, while blending modes determine how</ins> color <del class="del">glyphs</del>
<ins class="ins">values</ins> for <del class="del">other clock face emoji could be structured</del> <ins class="ins">pixels from the source and backdrop are combined. These are
illustrated with examples</ins> in <del class="del">exactly</del> <ins class="ins">figures 5.41 and 5.42: in each case, red and blue
rectangles are</ins> the
<del class="del">same way, using a nested PaintColrLayers table to re-use</del> <ins class="ins">source and backdrop content.

Figure 5.41 shows</ins> the <del class="del">layer composition</del> <ins class="ins">effect</ins> of <del class="del">the common clock face elements.

**5.7.11.1.7.3 Re-use using PaintColrGlyph**

A third way to re-use components in color glyph definitions is to use a nested
PaintColrGlyph table. This format references</del> a <del class="del">base glyph ID,</del> <ins class="ins">Porter-Duff mode, *XOR*,</ins> which <del class="del">is used</del> <ins class="ins">has the effect
that only non-overlapping pixels contribute</ins> to
<del class="del">access a corresponding BaseGlyphV1Record. That record will provide</del> the <del class="del">offset</del> <ins class="ins">result.

![Two content elements combined using the Porter-Duff XOR mode.](images/colr_porter-duff_xor.png)

**Figure 5.41 Two content elements combined using the Porter-Duff *XOR* mode.**

Figure 5.42 shows the effect</ins> of a <del class="del">paint table</del> <ins class="ins">*lighten* blending mode, which has the effect</ins>
that <del class="del">is</del> the <del class="del">root of a graph for a color glyph definition. That
graph can potentially be used as an independent</del> <ins class="ins">R, G, and B</ins> color <del class="del">glyph, but it can also
define a shared composition that gets re-used</del> <ins class="ins">components for each pixel</ins> in <del class="del">multiple color glyphs. Each
time</del> the <del class="del">shared composition is to be re-used, it</del> <ins class="ins">result</ins> is <del class="del">referenced by its base glyph
ID using a PaintColrGlyph table. The graph</del> <ins class="ins">the
greater</ins> of the <del class="del">referenced color glyph is
thereby incorporated into</del> <ins class="ins">R, G, and B values from corresponding pixels in</ins> the <del class="del">graph of</del> <ins class="ins">source and
backdrop.

![Two content elements combined using</ins> the <del class="del">PaintColrGlyph table as its child
sub-graph.

The glyph ID used may be that for a glyph outline, if there is an appropriate
glyph outline that corresponds to this composition. But</del> <ins class="ins">*lighten* blending mode.](images/colr_blend_lighten.png)

**Figure 5.42 Two content elements combined using</ins> the <del class="del">glyph ID may also be
greater than</del> <ins class="ins">*lighten* blending mode.**

For complete details on each of</ins> the <del class="del">last glyph ID used for outlines—that is, greater than or equal
to</del> <ins class="ins">Porter-Duff and blending modes, see</ins> the <del class="del">numGlyphs value in</del>
<ins class="ins">[Compositing and Blending Level 1][1] specification.

Figure 5.43 illustrates how</ins> the <del class="del">&#39;maxp&#39;</del> <ins class="ins">PaintComposite</ins> table <del class="del">(5.2.6). Such virtual base
glyph IDs</del> <ins class="ins">is used</ins> in <ins class="ins">combination with
content sub-graphs to implement an alternate compositing effect. The source
sub-graph defines a green capital A;</ins> the <del class="del">COLR table are only used within</del> <ins class="ins">backdrop sub-graph defines</ins> a <del class="del">PaintColrGlyph table, and are
not related to glyph IDs</del> <ins class="ins">black
circle. The compositing mode</ins> used <del class="del">in any other tables.

When a PaintColrGlyph table</del> is <del class="del">used, a BaseGlyphV1Record with</del> <ins class="ins">*Source Out*, which has</ins> the <del class="del">specified
glyph ID is expected. If no BaseGlyphV1Record with</del> <ins class="ins">effect</ins> that <del class="del">glyph ID is found,</del> the
<del class="del">color glyph is not well formed. See 5.7.11.1.9 for details regarding
well-formedness and validity of the graph.

The example from 5.7.11.1.7.2 is modified to illustrate use of a PaintColrGlyph
table. In figure 5.44, a PaintColrLayers table references</del>
<ins class="ins">source content punches out</ins> a <del class="del">slice within the
LayerV1List that defines</del> <ins class="ins">hole in</ins> the <del class="del">shared component. Now, however,</del> <ins class="ins">backdrop. (For</ins> this
<del class="del">PaintColrLayers table is treated as</del> <ins class="ins">mode,</ins> the <del class="del">root</del> <ins class="ins">fill
color</ins> of <ins class="ins">the source is irrelevant;</ins> a <del class="del">color glyph definition for
base glyph ID 63163. The color glyph for</del> <ins class="ins">black or yellow “A” would have</ins> the <del class="del">one o’clock emoji</del> <ins class="ins">same
effect.) A red rectangle</ins> is <del class="del">defined with
three layers,</del> <ins class="ins">included</ins> as <del class="del">before, but now the bottom layer uses</del> a <del class="del">PaintColrGlyph table</del> <ins class="ins">lower layer to show</ins> that <del class="del">references</del> the <ins class="ins">backdrop
has been punched out by the source, making that portion of the lower layer
visible.

![A</ins> color glyph <del class="del">definition for glyph ID 63163.

![A PaintColrGlyph</del> <ins class="ins">using a PaintComposite</ins> table <del class="del">is used</del> to <del class="del">reference</del> <ins class="ins">punch out a shape from</ins> the <del class="del">shared clock face composition via</del> <ins class="ins">fill of</ins> a <del class="del">glyph ID.](images/colr_reuse_clock-face_PaintColrGlyph.png)</del> <ins class="ins">circle.](images/colr_PaintCompositeGraph.png)</ins>

**Figure <del class="del">5.44</del> <ins class="ins">5.43</ins> A <del class="del">PaintColrGlyph</del> <ins class="ins">color glyph using a PaintComposite</ins> table <del class="del">is used</del> to <del class="del">reference the shared clock face composition via</del> <ins class="ins">punch out</ins> a <del class="del">glyph ID.**

While</del> <ins class="ins">shape from</ins> the <del class="del">PaintColrGlyph and PaintColrLayers tables are similar in being able to
reference a layer set as</del> <ins class="ins">fill of</ins> a <del class="del">re-usable component, they could be handled
differently in implementations.</del> <ins class="ins">circle.**

NOTE:</ins> In <del class="del">particular, an implementation could process
and cache</del> <ins class="ins">figure 5.43,</ins> the <del class="del">result of</del> <ins class="ins">“A” is filled with green to illustrate that</ins> the color <del class="del">glyph description</del>
<ins class="ins">of the fill has no affect</ins> for <del class="del">a given base glyph ID.
In</del> <ins class="ins">the *Source Out* composite mode. Because</ins> that <ins class="ins">is the</ins>
case, <del class="del">subsequent references to that base glyph ID using a PaintColrGlyph
table would not require</del> the <del class="del">corresponding graph</del> <ins class="ins">black or red PaintSolid could have been re-used instead of adding a
separate PaintSolid table. See 5.7.11.1.7.1 for more information on re-use</ins> of
paint tables <del class="del">to</del> <ins class="ins">for such situations.

[Scalable Vector Graphics (SVG)][32] supports alpha channel masking using the
&lt;mask&gt; element. The same effects can</ins> be
<del class="del">re-processed. As</del> <ins class="ins">implemented in COLR version 1
using</ins> a <del class="del">result,</del> <ins class="ins">PaintComposite table by setting a pattern of alpha values in the source
sub-graph and selecting the *Source In* composite mode. This is illustrated in
figure 5.44.

![A PaintComposite table using the *Source In* mode to implement an alpha mask.](images/colr_gradient_mask.png)

**Figure 5.44 An alpha mask implemented</ins> using a <del class="del">PaintColrGlyph for re-used graphic components
could provide performance benefits.

**5.7.11.1.8 Glyph metrics</del> <ins class="ins">PaintComposite table</ins> and <del class="del">boundedness**

**5.7.11.1.8.1 Metrics for</del> <ins class="ins">the *Source In* mode.**

**5.7.11.1.7 Re-usable components**

Within a color font, many</ins> color glyphs <del class="del">using version 0 formats**</del> <ins class="ins">might share components in common.</ins> For <ins class="ins">example, in emoji fonts, many different “smilies” or clock faces share a common background. This can be seen in figure 5.45, which shows</ins> color glyphs <del class="del">using version 0 formats, the advance width of glyphs used</del> for
<del class="del">each layer shall be</del> <ins class="ins">three emoji clock faces.

![Emoji clock faces for 12 o’clock, 1 o’clock and 2 o’clock.](images/colr_clocks-12-1-2.png)

**Figure 5.45 Emoji clock faces for 12 o’clock, 1 o’clock and 2 o’clock.**

Several components are shared between these color glyphs:</ins> the <del class="del">same as</del> <ins class="ins">entire face, with
a gradient background and dots at</ins> the <del class="del">advance width of</del> <ins class="ins">3, 6, 9 and 12 positions;</ins> the <del class="del">base glyph. If</del> <ins class="ins">minute hand
pointing to</ins> the <del class="del">font
has vertical metrics,</del> <ins class="ins">12 position; and</ins> the <del class="del">glyphs used for each layer shall also</del> <ins class="ins">circles in the center. Also, note that the
four dots</ins> have the same
<del class="del">advance height</del> <ins class="ins">shape</ins> and <del class="del">vertical Y origin as</del> <ins class="ins">fill, and differ only in their position. In
addition,</ins> the <del class="del">base glyph.

**5.7.11.1.8.2 Metrics</del> <ins class="ins">hour hands have the same shape</ins> and <del class="del">boundedness</del> <ins class="ins">fill, and differ only in their
orientation.

There are several ways in which elements</ins> of <ins class="ins">a</ins> color <del class="del">glyphs using version 1 formats**

For color glyphs using version 1 formats, the advance width of the base</del> glyph
<del class="del">shall</del> <ins class="ins">description can</ins> be <del class="del">used as the advance width</del>
<ins class="ins">re-used:

* Reference to shared subtables
* Use of a PaintColrLayers table
* Use of a PaintColrGlyph table

The PaintColrLayers and PaintColrGlyph table formats create a potential</ins> for
<ins class="ins">introducing cycles within</ins> the <ins class="ins">graph of a</ins> color <del class="del">glyph. If the font has vertical
metrics, the advance height and vertical Y origin</del> <ins class="ins">glyph, which would be invalid
(see 5.7.11.1.9).

**5.7.11.1.7.1 Re-use by referencing shared subtables**

Several</ins> of the <del class="del">base glyph shall be
used for</del> <ins class="ins">paint table formats link to a child paint table using a forward
offset within</ins> the <del class="del">color glyph. The advance width and height of glyphs referenced</del> <ins class="ins">file: 

* PaintGlyph
* PaintComposite
* PaintTransform, PaintVarTransform
* PaintTranslate, PaintVarTranslate
* PaintRotate, PaintVarRotate
* PaintSkew, PaintVarSkew

A child subtable can be shared</ins> by <ins class="ins">several tables of these formats. For example,
several</ins> PaintGlyph tables <del class="del">are not required</del> <ins class="ins">might link</ins> to <del class="del">be</del> the same <del class="del">as that of</del> <ins class="ins">PaintSolid table, or to</ins> the <del class="del">base glyph and
are ignored.</del>
<ins class="ins">same node for a sub-graph describing a more complex fill.</ins> The <del class="del">bounding box</del> <ins class="ins">only limitation is
that child paint tables are referenced using a forward offset from the start</ins> of
the <del class="del">base glyph is used as</del> <ins class="ins">referencing table, so a re-used paint table can only occur later in</ins> the <del class="del">bounding box</del> <ins class="ins">file
than any</ins> of the <del class="del">color
glyph. A &#39;glyf&#39; entry with two points at diagonal extrema is sufficient
to define the bounding box.

NOTE:</del> <ins class="ins">paint tables that use it.</ins>

The <del class="del">bounding box</del> <ins class="ins">clock faces shown in figure 5.45 provide an example</ins> of <del class="del">the base glyph</del> <ins class="ins">how PaintRotate
tables</ins> can be <del class="del">used to allocate</del> <ins class="ins">combined with re-use of</ins> a <del class="del">drawing
surface without needing to traverse</del> <ins class="ins">sub-graph. As noted above,</ins> the <del class="del">graph of</del> <ins class="ins">hour
hands have</ins> the <del class="del">color glyph definition.

A valid color glyph definition shall define a bounded region—that is, it shall
paint within a region for which a finite bounding box could be defined. The
different paint formats</del> <ins class="ins">same shape and fill, but</ins> have <ins class="ins">a</ins> different <del class="del">boundedness characteristics:

* PaintGlyph is inherently bounded.
* PaintSolid, PaintLinearGradient and PaintRadialGradient are inherently unbounded.
* PaintColrLayers is bounded *if and only if* all referenced sub-graphs are
bounded.
* PaintColrGlyph is bounded *if and only if*</del> <ins class="ins">orientation. The glyph
outline could point to</ins> the <ins class="ins">12 position, then in</ins> color glyph <del class="del">definition</del> <ins class="ins">descriptions</ins> for <del class="del">the
referenced base glyph ID is bounded.
* PaintTransformed, PaintTranslate,</del>
<ins class="ins">other times,</ins> PaintRotate <del class="del">and PaintSkew are bounded *if
and only if* the referenced sub-graph is bounded.
* PaintComposite is either bounded or unbounded, according</del> <ins class="ins">tables could link</ins> to the <del class="del">composite mode
used and the boundedness of the referenced sub-graphs. See 5.7.11.2.5.11 for
details.

Applications shall confirm</del> <ins class="ins">same glyph/fill sub-graph,
re-using</ins> that <del class="del">a color glyph definition</del> <ins class="ins">component but rotated as needed.

This</ins> is <del class="del">bounded,</del> <ins class="ins">illustrated in the figures 5.46</ins> and <del class="del">shall
not render</del> <ins class="ins">5.47. Figure 5.46 shows</ins> a <del class="del">color glyph if the</del> <ins class="ins">sub-graph</ins>
defining <del class="del">graph is not bounded.

**5.7.11.1.9 Color glyphs as a directed acyclic graph**

When</del> <ins class="ins">the hour hand, with upright orientation,</ins> using <del class="del">version 1 formats,</del> a <del class="del">color glyph is defined by</del> <ins class="ins">PaintGlyph and</ins> a <del class="del">directed, acyclic
graph of linked paint tables. For each BaseGlyphV1Record,</del>
<ins class="ins">PaintSolid table. Example file offsets for</ins> the <del class="del">paint</del> <ins class="ins">tables are indicated.

![A PaintGlyph and PaintSolid</ins> table
<del class="del">referenced by that record is</del> <ins class="ins">are used to define</ins> the <del class="del">root of a graph defining a color glyph
composition.

The graph for a given color glyph is made up</del> <ins class="ins">clock hour hand pointing to 12.](images/colr_hour-hand-component.png)

**Figure 5.46 A PaintGlyph and PaintSolid table are used to define the clock hour hand pointing to 12.**

Figure 5.47 shows this sub-graph</ins> of <del class="del">all</del> paint tables <del class="del">reachable</del> <ins class="ins">being re-used, in some cases
linked</ins> from <ins class="ins">PaintRotate tables that rotate the hour hand to point to different
clock positions as needed. All of</ins> the <del class="del">BaseGlyphV1Record. The BaseGlyphV1Record and several</del> paint <del class="del">table formats use
direct links;</del> <ins class="ins">tables</ins> that <del class="del">is, they include a forward offset</del> <ins class="ins">reference this sub-graph
occur earlier in the file.

![The sub-graph for the hour hand is re-used with PaintRotate tables to point to different hours.](images/colr_reuse-hour-hand-rotated.png)

**Figure 5.47 The sub-graphs for the hour hand are re-used with PaintRotate tables to point</ins> to <ins class="ins">different hours.**

**5.7.11.1.7.2 Re-use using PaintColrLayers**

As described above (see 5.7.11.1.4),</ins> a <del class="del">paint subtable. Two
paint formats make indirect links:

* A</del> PaintColrLayers table <del class="del">references</del> <ins class="ins">defines</ins> a <del class="del">slice</del> <ins class="ins">set</ins> of <del class="del">offsets within the LayerV1List.
The</del>
paint <del class="del">tables referenced by those offsets are considered to be linked within
the graph as children</del> <ins class="ins">sub-graphs arranged in bottom-up z-order layers, and an example was given</ins>
of <del class="del">the</del> <ins class="ins">a</ins> PaintColrLayers <del class="del">table.

* A PaintColrGlyph</del> table <del class="del">references a base glyph ID, for which a corresponding
BaseGlyphV1Record is expected. That record points to</del> <ins class="ins">used as</ins> the root of a <ins class="ins">color glyph definition. A
PaintColrLayers table can also be nested more deeply within the</ins> graph <del class="del">that is</del> <ins class="ins">of</ins> a <del class="del">complete</del> color <del class="del">glyph definition on its own. But when referenced in</del>
<ins class="ins">glyph. One purpose for doing</ins> this <del class="del">way by
a PaintColrGlyph table, that entire graph</del> is <del class="del">considered</del> to <del class="del">be</del> <ins class="ins">reference</ins> a <del class="del">child sub-graph</del> <ins class="ins">re-usable component defined
as a contiguous set</ins> of <ins class="ins">layers in</ins> the <del class="del">PaintColrGlyph table, and</del> <ins class="ins">LayersV1List table.

This is readily explained using the clock faces as an example. As described
above, each clock face shares several elements in common. Some of these form</ins> a <del class="del">continuation</del>
<ins class="ins">contiguous set</ins> of <ins class="ins">layers. Suppose four sub-graphs for shared clock face elements
are given in</ins> the <del class="del">graph of which</del> <ins class="ins">LayerV1List as contiguous layers, as shown in figure 5.48. (For
brevity,</ins> the
<del class="del">PaintColrGlyph table is a part.

The graph</del> <ins class="ins">visual result</ins> for <del class="del">a color glyph</del> <ins class="ins">each sub-graph</ins> is <del class="del">a combination of paint table using any of</del> <ins class="ins">shown, but not</ins> the paint <del class="del">table formats. The simplest color glyph definition would consist of</del>
<ins class="ins">details.)

![Common clock face elements given as</ins> a
<del class="del">PaintGlyph table linked to</del> <ins class="ins">slice within the LayerV1List table.](images/colr_clock_common.png)

**Figure 5.48 Common clock face elements given as</ins> a <del class="del">basic fill table (PaintSolid, PaintLinearGradient
or PaintRadialGradient). But</del> <ins class="ins">slice within</ins> the <del class="del">graph</del> <ins class="ins">LayerV1List table.**

A PaintColrLayers table</ins> can <del class="del">be arbitrarily complex, with an
arbitrary depth</del> <ins class="ins">reference any contiguous slice</ins> of <del class="del">paint nodes (to</del> <ins class="ins">layers in</ins> the <del class="del">limits inherent</del>
<ins class="ins">LayerV1List table. Thus, the set of layers shown in figure 5.48 can be
referenced by PaintColrLayers tables anywhere</ins> in the <del class="del">formats).

The</del> graph <ins class="ins">of any color glyph.
In this way, this set of layers</ins> can <del class="del">define a visual element</del> <ins class="ins">be re-used</ins> in <del class="del">a single layer, or many elements</del> <ins class="ins">multiple clock face color
glyph definitions.

This is illustrated</ins> in
<del class="del">many layers.</del> <ins class="ins">figure 5.49:</ins> The <del class="del">concept of layers, as distinct visual elements stacked in</del> <ins class="ins">color glyph definition for the one
o’clock emoji has</ins> a
<del class="del">z-order, is not precisely defined</del> <ins class="ins">PaintColrLayers table as its root, referencing a slice of
three layers</ins> in <del class="del">relation to</del> the <del class="del">complexity of</del> <ins class="ins">LayerV1List table. The upper two layers are</ins> the <del class="del">graph.
Each separate visual element requires a leaf node, but nodes in</del> <ins class="ins">hour hand,
which is specific to this color glyph; and</ins> the <del class="del">graph,
including leaf nodes, can be re-used (see 5.7.11.1.7). Also, each separate
visual element requires a fork in</del> <ins class="ins">cap over</ins> the <del class="del">graph,</del> <ins class="ins">pivot for the minute</ins>
and <del class="del">a separate root-to-leaf path,</del> <ins class="ins">hour hands, which is common to other clock emoji</ins> but <del class="del">not all paths necessarily result</del> in a <del class="del">distinct visual element. For example,
a gradient mask effect can be created with a gradient</del> <ins class="ins">layer that is not
contiguous</ins> with <del class="del">gradation</del> <ins class="ins">other common layers. The bottom layer</ins> of <del class="del">alpha
values, and then</del> <ins class="ins">these three layers is
the composition for all the remaining common layers. It is represented</ins> using <ins class="ins">a
nested PaintColrLayers table</ins> that <del class="del">as</del> <ins class="ins">references</ins> the <del class="del">source of a PaintComposite with</del> <ins class="ins">slice within</ins> the <del class="del">*source
in* compositing mode. In that case,</del> <ins class="ins">LayerV1List
for</ins> the <del class="del">leaf has a visual affect but does not
result in a distinct visual element. This was illustrated</del> <ins class="ins">common clock face elements shown</ins> in figure <del class="del">5.38,
repeated here as figure 5.45: the PaintLinearGradient is a leaf node in the
graph and creates a masking effect but does not add a distinct visual element.</del> <ins class="ins">5.48.</ins>

![A <del class="del">PaintLinearGradient used as a compositing mask</del> <ins class="ins">PaintColrLayers table</ins> is <ins class="ins">used to reference</ins> a <del class="del">leaf node in the graph but does not add</del> <ins class="ins">set of layers that define</ins> a <del class="del">distinct visual element.](images/colr_gradient_mask.png)</del> <ins class="ins">shared clock face composition.](images/colr_reuse_clock-face_PaintColrLayers.png)</ins>

**Figure <del class="del">5.45 Graph with</del> <ins class="ins">5.49 A PaintColrLayers table is used to reference</ins> a <del class="del">leaf node</del> <ins class="ins">set of layers</ins> that <del class="del">isn't</del> <ins class="ins">define</ins> a <del class="del">distinct visual element.**

Thus, the generalization that can</del> <ins class="ins">shared clock face composition.**

The color glyphs for other clock face emoji could</ins> be <del class="del">made regarding the relationship between</del> <ins class="ins">structured in exactly</ins> the
<del class="del">number of layers and</del>
<ins class="ins">same way, using a nested PaintColrLayers table to re-use</ins> the <del class="del">nature</del> <ins class="ins">layer composition</ins>
of the <del class="del">graph</del> <ins class="ins">common clock face elements.

**5.7.11.1.7.3 Re-use using PaintColrGlyph**

A third way to re-use components in color glyph definitions</ins> is <del class="del">that</del> <ins class="ins">to use a nested
PaintColrGlyph table. This format references a base glyph ID, which is used to
access a corresponding BaseGlyphV1Record. That record will provide</ins> the <del class="del">number</del> <ins class="ins">offset</ins> of <del class="del">distinct
root-to-leaf paths will be greater than or equal to</del>
<ins class="ins">a paint table that is</ins> the <del class="del">number</del> <ins class="ins">root</ins> of <del class="del">layers.

The following are necessary</del> <ins class="ins">a graph</ins> for <del class="del">the</del> <ins class="ins">a color glyph definition. That</ins>
graph <del class="del">to</del> <ins class="ins">can potentially</ins> be <del class="del">well-formed and valid:

* All subtable links shall satisfy the following criteria:
  * Forward offsets are within the COLR table bounds.
  * If</del> <ins class="ins">used as an independent color glyph, but it can also
define</ins> a <del class="del">PaintColrLayers table</del> <ins class="ins">shared composition that gets re-used in multiple color glyphs. Each
time the shared composition</ins> is <del class="del">present, then a LayersV1List</del> <ins class="ins">to be re-used, it</ins> is <del class="del">also present,
and</del> <ins class="ins">referenced by its base glyph
ID using a PaintColrGlyph table. The graph of</ins> the referenced <del class="del">slice</del> <ins class="ins">color glyph</ins> is <del class="del">within</del>
<ins class="ins">thereby incorporated into</ins> the <del class="del">length</del> <ins class="ins">graph</ins> of the <del class="del">LayersV1List.
  * If a</del> PaintColrGlyph table <del class="del">is present,</del> <ins class="ins">as its child
sub-graph.

The glyph ID used may be that for a glyph outline, if</ins> there is <del class="del">a BaseGlyphV1Record for</del> <ins class="ins">an appropriate
glyph outline that corresponds to this composition. But</ins> the
<del class="del">referenced base</del> glyph <del class="del">ID.
* The graph shall</del> <ins class="ins">ID may also</ins> be <del class="del">acyclic.

NOTE: These constraints imply that all leaf nodes will be one of PaintSolid,
PaintLinearGradient or PaintRadialGradient.

For</del>
<ins class="ins">greater than</ins> the <del class="del">graph</del> <ins class="ins">last glyph ID used for outlines—that is, greater than or equal</ins>
to <del class="del">be acyclic, no paint</del> <ins class="ins">the numGlyphs value in the &#39;maxp&#39;</ins> table <del class="del">shall have</del> <ins class="ins">(5.2.6). Such virtual base
glyph IDs in the COLR table are only used within a PaintColrGlyph table, and are
not related to glyph IDs used in</ins> any <del class="del">child or descendent
paint</del> <ins class="ins">other tables.

When a PaintColrGlyph</ins> table <ins class="ins">is used, a BaseGlyphV1Record with the specified
glyph ID is expected. If no BaseGlyphV1Record with</ins> that <ins class="ins">glyph ID</ins> is <del class="del">also its parent or ancestor within</del> <ins class="ins">found, the
color glyph is not well formed. See 5.7.11.1.9 for details regarding
well-formedness and validity of</ins> the graph.

<ins class="ins">The example from 5.7.11.1.7.2 is modified to illustrate use of a PaintColrGlyph
table.</ins> In <del class="del">particular,
because the</del> <ins class="ins">figure 5.50, a</ins> PaintColrLayers <del class="del">and PaintColrGlyph tables use indirect child</del> <ins class="ins">table</ins> references <del class="del">rather than forward offsets, they provide</del> a <del class="del">possibility for
introducing cycles. Applications should track paint tables</del> <ins class="ins">slice</ins> within <del class="del">a path in</del> the
<del class="del">graph, checking whether any paint table was already encountered within</del>
<ins class="ins">LayerV1List</ins> that
<del class="del">path. The following pseudo-code algorithm can be used:

```
    // called initially with</del> <ins class="ins">defines</ins> the <del class="del">root paint and an empty set pathPaints
    function paintIsAcyclic(paint, pathPaints)
        if paint</del> <ins class="ins">shared component. Now, however, this
PaintColrLayers table</ins> is <del class="del">in pathPaints
          return false // cycle detected
        add paint to pathPaints
        for each childPaint referenced by paint</del> <ins class="ins">treated</ins> as <del class="del">a child subtable
          call paintIsAcyclic(childPaint, pathPaints)
        remove paint from pathPaints
```

For</del> the <del class="del">graph to be valid, it shall also be visually bounded, as described in
5.7.11.1.8.2.

NOTE: Implementations can combine testing</del> <ins class="ins">root of a color glyph definition</ins> for <del class="del">cycles and other well-formedness
or validity requirements together with other processing for rendering the</del>
<ins class="ins">base glyph ID 63163. The</ins> color
<del class="del">glyph.

If</del> <ins class="ins">glyph for</ins> the <del class="del">graph contains a cycle or</del> <ins class="ins">one o’clock emoji</ins> is <del class="del">otherwise not well formed or valid,</del> <ins class="ins">defined with
three layers, as before, but now</ins> the
<del class="del">paint</del> <ins class="ins">bottom layer uses a PaintColrGlyph</ins> table <del class="del">at which the error occurs should be ignored, that sub-graph should
not be rendered, and</del>
that <del class="del">node in</del> <ins class="ins">references</ins> the <del class="del">graph should be considered to be visually
bounded. The application should attempt</del> <ins class="ins">color glyph definition for glyph ID 63163.

![A PaintColrGlyph table is used</ins> to <del class="del">render the remainder of the graph, if
well formed and valid.

Future minor version updates of</del> <ins class="ins">reference</ins> the <del class="del">COLR table could introduce new paint
formats. If</del> <ins class="ins">shared clock face composition via</ins> a <del class="del">paint</del> <ins class="ins">glyph ID.](images/colr_reuse_clock-face_PaintColrGlyph.png)

**Figure 5.50 A PaintColrGlyph</ins> table <del class="del">with an unrecognized format</del> is <del class="del">encountered, it and its
sub-graph should similarly be ignored, the node should be considered to be
visually bounded, and the application should attempt</del> <ins class="ins">used</ins> to <del class="del">render</del> <ins class="ins">reference</ins> the <del class="del">remainder of</del> <ins class="ins">shared clock face composition via a glyph ID.**

While</ins> the <del class="del">graph.

If an application is not</del> <ins class="ins">PaintColrGlyph and PaintColrLayers tables are similar in being</ins> able to <del class="del">recover from errors while traversing</del>
<ins class="ins">reference a layer set as a re-usable component, they could be handled
differently in implementations. In particular, an implementation could process
and cache</ins> the <del class="del">graph,
it may ignore</del> <ins class="ins">result of</ins> the color glyph <del class="del">entirely. If the</del> <ins class="ins">description for a given</ins> base glyph <del class="del">ID has an outline,</del> <ins class="ins">ID.
In</ins> that <del class="del">may be rendered as a non-color</del> <ins class="ins">case, subsequent references to that base</ins> glyph <del class="del">instead.

**5.7.11.2 COLR table formats**

Various</del> <ins class="ins">ID using a PaintColrGlyph</ins>
table <del class="del">and record formats are defined</del> <ins class="ins">would not require the corresponding graph of paint tables to be
re-processed. As a result, using a PaintColrGlyph</ins> for <del class="del">COLR</del> <ins class="ins">re-used graphic components
could provide performance benefits.

**5.7.11.1.8 Glyph metrics and boundedness**

**5.7.11.1.8.1 Metrics for color glyphs using</ins> version 0 <del class="del">and</del> <ins class="ins">formats**

For color glyphs using</ins> version <del class="del">1.
Several values contained within</del> <ins class="ins">0 formats,</ins> the <del class="del">version 1 formats are variable. These use
various record formats that combine a basic data type with a variation delta-set
index: VarFWord, VarUFWord, VarF2Dot14, and VarFixed. These are described in
7.2.3.1.

All table offsets are from</del> <ins class="ins">advance width of glyphs used for
each layer shall be</ins> the <del class="del">start</del> <ins class="ins">same as the advance width</ins> of the <del class="del">parent table in which</del> <ins class="ins">base glyph. If</ins> the <del class="del">offset is
given, unless otherwise indicated.

**5.7.11.2.1 COLR header**

The COLR table begins with a header. Two versions</del> <ins class="ins">font
has vertical metrics, the glyphs used for each layer shall also</ins> have <del class="del">been defined. Offsets in</del> the <del class="del">header are from</del> <ins class="ins">same
advance height and vertical Y origin as</ins> the <del class="del">start</del> <ins class="ins">base glyph.

**5.7.11.1.8.2 Metrics and boundedness</ins> of <del class="del">the table.

**5.7.11.2.1.1 COLR version 0**

*COLR version 0:*

| Type | Field name | Description |
|-|-|-|
| uint16 | version | Table</del> <ins class="ins">color glyphs using</ins> version <del class="del">number—set to 0. |
| uint16 | numBaseGlyphRecords | Number of BaseGlyph records. |
| Offset32 | baseGlyphRecordsOffset | Offset to baseGlyphRecords array. |
| Offset32 | layerRecordsOffset | Offset to layerRecords array. |
| uint16 | numLayerRecords | Number of Layer records. |

NOTE:</del> <ins class="ins">1 formats**</ins>

For <del class="del">fonts that use COLR</del> <ins class="ins">color glyphs using</ins> version <del class="del">0, some early Windows implementations</del> <ins class="ins">1 formats, the advance width</ins> of the <del class="del">COLR table require</del> <ins class="ins">base</ins> glyph <del class="del">ID 1 to</del>
<ins class="ins">shall</ins> be <ins class="ins">used as</ins> the <del class="del">.null</del> <ins class="ins">advance width for the color</ins> glyph.

<del class="del">**5.7.11.2.1.2 COLR version 1**

*COLR version 1:*

| Type | Field name | Description |
|-|-|-|
| uint16 | version | Table version number—set to 1. |
| uint16 | numBaseGlyphRecords | Number</del> <ins class="ins">If the font has vertical
metrics, the advance height and vertical Y origin</ins> of <del class="del">BaseGlyph records; may be 0 in a version 1 table. |
| Offset32 | baseGlyphRecordsOffset | Offset to baseGlyphRecords array (may be NULL). |
| Offset32 | layerRecordsOffset | Offset to layerRecords array (may</del> <ins class="ins">the base glyph shall</ins> be <del class="del">NULL). |
| uint16 | numLayerRecords | Number</del>
<ins class="ins">used for the color glyph. The advance width and height</ins> of <del class="del">Layer records; may be 0 in a version 1 table. |
| Offset32 | baseGlyphV1ListOffset | Offset to BaseGlyphV1List table. |
| Offset32 | layersV1Offset | Offset to LayerV1List table (may be NULL). |
| Offset32 | itemVariationStoreOffset | Offset</del> <ins class="ins">glyphs referenced by
PaintGlyph tables are not required</ins> to <del class="del">ItemVariationStore (may</del> be <del class="del">NULL). |

The BaseGlyphV1List</del> <ins class="ins">the same as that of the base glyph</ins> and <del class="del">its subtables</del>
are <del class="del">only used in COLR version 1.</del> <ins class="ins">ignored.</ins>

The <del class="del">LayersV1List</del> <ins class="ins">bounding box of the base-glyph contours</ins> is <del class="del">only</del> used <del class="del">in conjunction with</del> <ins class="ins">as</ins> the <del class="del">BaseGlyphV1List and,
specifically,</del> <ins class="ins">bounding box of the
color glyph. A &#39;glyf&#39; entry</ins> with <del class="del">PaintColrLayers tables (5.7.11.2.5.1); it</del> <ins class="ins">two points at diagonal extrema</ins> is <del class="del">not required if
no color glyphs use a PaintColrLayers table. If not used, set layersV1Offset</del>
<ins class="ins">sufficient</ins> to
<del class="del">NULL.</del> <ins class="ins">define the bounding box.

NOTE:</ins> The <del class="del">ItemVariationStore is</del> <ins class="ins">bounding box of the base glyph can be</ins> used <del class="del">in conjunction with</del> <ins class="ins">to allocate</ins> a <del class="del">BaseGlyphV1List and its
subtables, but only in variable fonts. If it is not used, set
itemVariationStoreOffset</del> <ins class="ins">drawing
surface without needing</ins> to <del class="del">NULL.

**5.7.11.2.1.3 Mixing version 0 and version 1 formats**</del> <ins class="ins">traverse the graph of the color glyph definition.</ins>

A <del class="del">font that uses COLR version 1 and that includes</del> <ins class="ins">valid color glyph definition shall define</ins> a <del class="del">BaseGlyphV1List can also
include BaseGlyph and Layer records</del> <ins class="ins">bounded region—that is, it shall
paint within a region</ins> for <del class="del">compatibility with applications that
only support COLR version 0.

Color glyphs that can</del> <ins class="ins">which a finite bounding box could</ins> be <del class="del">implemented in COLR version 0 using BaseGlyph</del> <ins class="ins">defined. The
different paint formats have different boundedness characteristics:

* PaintGlyph is inherently bounded.
* PaintSolid, PaintVarSolid, PaintLinearGradient, PaintVarLinearGradient,
PaintRadialGradient, PaintVarRadialGradient, PaintSweepGradient,</ins> and <del class="del">Layer
records can also be implemented using</del>
<ins class="ins">PaintVarSweepGradient are inherently unbounded.
* PaintColrLayers is bounded *if and only if* all referenced sub-graphs are
bounded.
* PaintColrGlyph is bounded *if and only if*</ins> the <del class="del">version 1 BaseGlyphV1List</del> <ins class="ins">color glyph definition for the
referenced base glyph ID is bounded.
* PaintTransform, PaintVarTransform, PaintTranslate, PaintVarTranslate,
PaintRotate, PaintVarRotate, PaintSkew,</ins> and
<del class="del">subtables. Thus, a font that uses</del> <ins class="ins">PaintVarSkew are bounded *if and
only if*</ins> the <del class="del">version 1 formats does not need</del> <ins class="ins">referenced sub-graph is bounded.
* PaintComposite is either bounded or unbounded, according</ins> to <del class="del">use</del> the
<del class="del">version 0 BaseGlyph</del> <ins class="ins">composite mode
used</ins> and <del class="del">Layer records. However, a font may use</del> the <del class="del">version 1
structures for some base glyphs and</del> <ins class="ins">boundedness of</ins> the <del class="del">version 0 structures</del> <ins class="ins">referenced sub-graphs. See 5.7.11.2.5.12</ins> for <del class="del">other base
glyphs. A font may also include</del>
<ins class="ins">details.

Applications shall confirm that</ins> a <del class="del">version 1</del> color glyph definition <del class="del">for</del> <ins class="ins">is bounded, and shall
not render</ins> a <del class="del">given
base</del> <ins class="ins">color</ins> glyph <del class="del">ID that</del> <ins class="ins">if the defining graph</ins> is <del class="del">equivalent to a version 0 definition, though this should
never be needed. 

A font may define</del> <ins class="ins">not bounded.

To ensure that rendering implementations do not clip any part of</ins> a color <ins class="ins">glyph,
the bounding box of the base</ins> glyph <del class="del">for</del> <ins class="ins">needs to be large enough to encompass the
entire color glyph composition. In</ins> a <del class="del">given base</del> <ins class="ins">variable font,</ins> glyph <del class="del">ID using version 0
formats, and also define</del> <ins class="ins">outlines can vary, but
transformations in</ins> a <del class="del">different</del> color glyph <ins class="ins">description can also vary, affecting the
portions of the design grid to be painted. For example, a filled rectangle that
is wide but not tall</ins> for <ins class="ins">one variation instance can be variably rotated to be
tall but not wide for other instances. The bounding box of</ins> the <del class="del">same</del> base glyph <del class="del">ID
using version 1 formats. Applications that support COLR version 1</del> <ins class="ins">either</ins>
should <del class="del">give
preference</del> <ins class="ins">be large enough</ins> to <ins class="ins">encompass</ins> the <del class="del">version 1</del> color <del class="del">glyph.

For applications</del> <ins class="ins">glyph for all instances, or should
itself vary such</ins> that <del class="del">support COLR version 1,</del> <ins class="ins">each instance bounding box encompasses</ins> the <del class="del">application should search for</del> <ins class="ins">instance color
glyph.

**5.7.11.1.9 Color glyphs as</ins> a <del class="del">base glyph ID first in the BaseGlyphV1List. Then, if not found, search in the
baseGlyphRecords array, if present.

**5.7.11.2.2 BaseGlyph and Layer records**

BaseGlyph and Layer records are required for COLR version 0, but optional for</del> <ins class="ins">directed acyclic graph**

When using</ins> version <del class="del">1. (See 5.7.11.2.1.3.)

A BaseGlyph record is used to map</del> <ins class="ins">1 formats,</ins> a <del class="del">base</del> <ins class="ins">color</ins> glyph <del class="del">to</del> <ins class="ins">is defined by</ins> a <del class="del">sequence</del> <ins class="ins">directed, acyclic
graph</ins> of <del class="del">layer records</del> <ins class="ins">linked paint tables. For each BaseGlyphV1Record, the paint table
referenced by</ins> that <del class="del">define</del> <ins class="ins">record is</ins> the <del class="del">corresponding</del> <ins class="ins">root of a graph defining a</ins> color <del class="del">glyph.</del> <ins class="ins">glyph
composition.</ins>

The <del class="del">BaseGlyph record includes</del> <ins class="ins">graph for</ins> a <del class="del">base</del> <ins class="ins">given color</ins> glyph <del class="del">index, an index into the layerRecords array, and the number</del> <ins class="ins">is made up</ins> of <del class="del">layers.

*BaseGlyph record:*

| Type | Field name | Description |
|-|-|-|
| uint16 | glyphID | Glyph ID of the base glyph. |
| uint16 | firstLayerIndex | Index (base 0) into</del> <ins class="ins">all paint tables reachable from</ins>
the <del class="del">layerRecords array. |
| uint16 | numLayers | Number of color layers associated with this glyph. |</del> <ins class="ins">BaseGlyphV1Record.</ins> The <del class="del">glyph ID shall be less than the numGlyphs value in the &#39;maxp&#39;</del> <ins class="ins">BaseGlyphV1Record and several paint</ins> table
<del class="del">(5.2.6).

The BaseGlyph records shall be sorted in increasing glyphID order. It is assumed</del> <ins class="ins">formats use
direct links;</ins> that <ins class="ins">is, they include</ins> a <del class="del">binary search can be used</del> <ins class="ins">forward offset</ins> to <del class="del">find</del> a <del class="del">matching BaseGlyph record for</del> <ins class="ins">paint subtable. Two
paint formats make indirect links:

* A PaintColrLayers table references</ins> a
<del class="del">specific glyphID.</del> <ins class="ins">slice of offsets within the LayerV1List.</ins>
The <del class="del">color glyph for a given base glyph is defined</del> <ins class="ins">paint tables referenced</ins> by <ins class="ins">those offsets are considered to be linked within</ins>
the <del class="del">consecutive records in</del> <ins class="ins">graph as children of</ins> the <del class="del">layerRecords array</del> <ins class="ins">PaintColrLayers table.

* A PaintColrGlyph table references a base glyph ID,</ins> for <ins class="ins">which a corresponding
BaseGlyphV1Record is expected. That record points to</ins> the <del class="del">specified number</del> <ins class="ins">root</ins> of <del class="del">layers, starting with the
record indicated by firstLayerIndex. The first record</del> <ins class="ins">a graph that is
a complete color glyph definition on its own. But when referenced</ins> in this <del class="del">sequence</del> <ins class="ins">way by
a PaintColrGlyph table, that entire graph</ins> is <ins class="ins">considered to be a child sub-graph
of</ins> the
<del class="del">bottom layer in the z-order,</del> <ins class="ins">PaintColrGlyph table,</ins> and <del class="del">each subsequent layer is stacked on top</del> <ins class="ins">a continuation</ins> of the
<del class="del">previous layer.</del> <ins class="ins">graph of which the
PaintColrGlyph table is a part.</ins>

The <del class="del">layer record sequences</del> <ins class="ins">graph</ins> for <del class="del">two different base glyphs may overlap, with some
layer records used in multiple</del> <ins class="ins">a</ins> color glyph <del class="del">definitions.

The Layer record specifies the glyph used as the graphic element for</del> <ins class="ins">is</ins> a <del class="del">layer and
the solid color fill.

*Layer record:*

| Type | Field name | Description |
|-|-|-|
| uint16 | glyphID | Glyph ID</del> <ins class="ins">combination of paint tables using any</ins> of the
<ins class="ins">paint table formats. The simplest color</ins> glyph <del class="del">used for</del> <ins class="ins">definition would consist of</ins> a <del class="del">given layer. |
| uint16 | paletteIndex | Index (base 0) for</del>
<ins class="ins">PaintGlyph table linked to</ins> a <del class="del">palette entry in</del> <ins class="ins">basic fill table (PaintSolid, PaintVarSolid,
PaintLinearGradient, PaintVarLinearGradient, PaintRadialGradient,
PaintVarRadialGradient, PaintSweepGradient, PaintVarSweepGradient). But</ins> the <del class="del">CPAL table. |

The glyphID in a Layer record shall</del>
<ins class="ins">graph can</ins> be <del class="del">less than</del> <ins class="ins">arbitrarily complex, with an arbitrary depth of paint nodes (to</ins> the <del class="del">numGlyphs value</del>
<ins class="ins">limits inherent</ins> in the
<del class="del">&#39;maxp&#39; table. That is, it shall be</del> <ins class="ins">formats).

The graph can define</ins> a <del class="del">valid glyph with outline data</del> <ins class="ins">visual element</ins> in
<del class="del">the &#39;glyf&#39; (5.3.4), &#39;CFF &#39; (5.4.2)</del> <ins class="ins">a single layer,</ins> or <del class="del">CFF2 (5.4.3) table. See
5.7.11.1.8.2 for requirements regarding glyph metrics of referenced glyphs.</del> <ins class="ins">many elements in
many layers.</ins> The <del class="del">paletteIndex value shall be less than the numPaletteEntries value</del> <ins class="ins">concept of layers, as distinct visual elements stacked in a
z-order, is not precisely defined</ins> in <ins class="ins">relation to</ins> the
<del class="del">CPAL table (5.7.12). A paletteIndex value</del> <ins class="ins">complexity</ins> of <del class="del">0xFFFF is a special case,
indicating that</del> the <del class="del">text foreground color (as determined by</del> <ins class="ins">graph.
Each separate visual element requires a leaf node, but nodes in</ins> the <del class="del">application) is
to</del> <ins class="ins">graph,
including leaf nodes, can</ins> be <del class="del">used.

**5.7.11.2.3 BaseGlyphV1List and LayerV1List**

The BaseGlyphV1List table is, conceptually, similar to the baseGlyphRecords
array</del> <ins class="ins">re-used (see 5.7.11.1.7). Also, each separate
visual element requires a fork</ins> in <del class="del">COLR version 0, providing records that map a base glyph to a color
glyph definition. The color glyph definition is significantly different,
however—see 5.7.11.1.

*BaseGlyphV1List table:*

| Type | Name | Description |
|-|-|-|
| uint32 | numBaseGlyphV1Records |  |
| BaseGlyphV1Record | baseGlyphV1Records[numBaseGlyphV1Records] | |

*BaseGlyphV1Record:*

| Type | Name | Description |
|-|-|-|
| uint16 | glyphID | Glyph ID of</del> the <del class="del">base glyph. |
| Offset32 | paintOffset | Offset to</del> <ins class="ins">graph, and</ins> a <del class="del">Paint table. |

The glyph ID is</del> <ins class="ins">separate root-to-leaf path,
but</ins> not <del class="del">limited to the numGlyphs value in the &#39;maxp&#39; table
(5.2.6). See 5.7.11.1.7.3 for more information.

The records in the baseGlyphV1Records array shall be sorted</del> <ins class="ins">all paths necessarily result</ins> in <del class="del">increasing
glyphID order. It is intended that</del> a <del class="del">binary search</del> <ins class="ins">distinct visual element. For example,
a gradient mask effect</ins> can be <del class="del">used to find</del> <ins class="ins">created with</ins> a
<del class="del">matching BaseGlyphV1Record for</del> <ins class="ins">gradient with gradation of alpha
values, and then using that as the source of</ins> a <del class="del">specific glyphID.

The paint</del> <ins class="ins">PaintComposite</ins> table <del class="del">referenced by the BaseGlyphV1Record is</del> <ins class="ins">with</ins> the <del class="del">root of</del>
<ins class="ins">*Source In* compositing mode. In that case,</ins> the <del class="del">graph for</del> <ins class="ins">leaf has</ins> a <del class="del">color glyph definition.

NOTE: Often</del> <ins class="ins">visual affect but
does not result in a distinct visual element. This was illustrated in figure
5.44, repeated here as figure 5.51:</ins> the <del class="del">paint table that</del> <ins class="ins">PaintLinearGradient</ins> is <del class="del">the root of</del> <ins class="ins">a leaf node in</ins>
the graph <del class="del">for the color glyph
definition will be</del> <ins class="ins">and creates</ins> a <del class="del">PaintColrLayers table, though this is</del> <ins class="ins">masking effect but does</ins> not <del class="del">required. See
5.7.11.1.9 more information regarding</del> <ins class="ins">add a distinct visual
element.

![A PaintLinearGradient used as a compositing mask is a leaf node in</ins> the graph <del class="del">of a color glyph, and 5.7.11.1.4
for background information regarding the PaintColrLayers table.

A LayerV1List table is used in conjuction</del> <ins class="ins">but does not add a distinct visual element.](images/colr_gradient_mask.png)

**Figure 5.51 Graph</ins> with <del class="del">PaintColrLayers tables to
represent layer structures. A single LayerV1List is defined and</del> <ins class="ins">a leaf node that isn’t a distinct visual element.**

Thus, the generalization that</ins> can be <del class="del">used by
multiple PaintColrLayer tables, each</del> <ins class="ins">made regarding the relationship between the
number</ins> of <del class="del">which references a slice</del> <ins class="ins">layers and the nature</ins> of the <del class="del">layer
list.

*LayerV1List table:*

| Type | Field name | Description |
|-|-|-|
| uint32 | numLayers |  |
| Offset32 | paintOffsets[numLayers] | Offsets to Paint tables. |

The sequence</del> <ins class="ins">graph is that the number</ins> of <del class="del">offsets to paint tables corresponds</del> <ins class="ins">distinct
root-to-leaf paths will be greater than or equal</ins> to <del class="del">a bottom-up z-order
layering</del> <ins class="ins">the number</ins> of <ins class="ins">layers.

The following are necessary for</ins> the <del class="del">graphic compositions defined by</del> <ins class="ins">graph to be well-formed and valid:

* All subtable links shall satisfy</ins> the <del class="del">sub-graph of each referenced
paint</del> <ins class="ins">following criteria:
  * Forward offsets are within the COLR</ins> table <del class="del">graph. For</del> <ins class="ins">bounds.
  * If</ins> a <del class="del">given slice of</del> <ins class="ins">PaintColrLayers table is present, then a LayersV1List is also present,
and</ins> the <del class="del">list,</del> <ins class="ins">referenced slice is within</ins> the <del class="del">sub-graph</del> <ins class="ins">length</ins> of the <del class="del">first
paint</del> <ins class="ins">LayersV1List.
  * If a PaintColrGlyph</ins> table <del class="del">defines the element at</del> <ins class="ins">is present, there is a BaseGlyphV1Record for</ins> the <del class="del">bottom</del>
<ins class="ins">referenced base glyph ID.
* The graph shall be acyclic.

NOTE: These constraints imply that all leaf nodes will be one</ins> of <ins class="ins">PaintSolid,
PaintVarSolid, PaintLinearGradient, PaintVarLinearGradient, PaintRadialGradient,
PaintVarRadialGradient, PaintSweepGradient, or PaintVarSweepGradient.

For</ins> the <del class="del">z-order, and the sub-graph
of each subsequent</del> <ins class="ins">graph to be acyclic, no paint table shall have any child or descendent</ins>
paint table <del class="del">defines an element</del> that is <del class="del">layered on top of</del> <ins class="ins">also its parent or ancestor within</ins> the
<del class="del">previous element. As each element is a composition defined in a sub-graph, one
of these elements may itself be multi-layered.</del> <ins class="ins">graph.</ins> In <del class="del">that case,</del> <ins class="ins">particular,
because</ins> the <del class="del">layers of this
element are stack above all previous layers,</del> <ins class="ins">PaintColrLayers</ins> and <del class="del">layers of following elements
are stacked above the top layer of this element.

Offsets</del> <ins class="ins">PaintColrGlyph tables use indirect child
references rather than forward offsets, they provide a possibility</ins> for
<ins class="ins">introducing cycles. Applications should track</ins> paint tables <del class="del">not referenced by</del> <ins class="ins">within a path in the
graph, checking whether</ins> any <del class="del">PaintColrLayers</del> <ins class="ins">paint</ins> table <del class="del">should not</del> <ins class="ins">was already encountered within that
path. The following pseudo-code algorithm can</ins> be <del class="del">included in</del> <ins class="ins">used:

```
    // called initially with</ins> the <del class="del">paintOffsets array.

**5.7.11.2.4 ColorIndex, ColorStop</del> <ins class="ins">root paint</ins> and <del class="del">ColorLine**

Colors are used</del> <ins class="ins">an empty set pathPaints
    function paintIsAcyclic(paint, pathPaints)
        if paint is</ins> in <del class="del">solid color fills</del> <ins class="ins">pathPaints
          return false // cycle detected
        add paint to pathPaints</ins>
        for <del class="del">graphic elements, or</del> <ins class="ins">each childPaint referenced by paint</ins> as <del class="del">*stops* in a
color line used to define</del> a <del class="del">gradient. Colors are defined by reference</del> <ins class="ins">child subtable
          call paintIsAcyclic(childPaint, pathPaints)
        remove paint from pathPaints
```

For the graph</ins> to <del class="del">palette
entries</del> <ins class="ins">be valid, it shall also be visually bounded, as described</ins> in
<ins class="ins">5.7.11.1.8.2.

NOTE: Implementations can combine testing for cycles and other well-formedness
or validity requirements together with other processing for rendering</ins> the <del class="del">CPAL table (5.7.12). While CPAL entries include an alpha
component,</del> <ins class="ins">color
glyph.

If the graph contains</ins> a <del class="del">ColorIndex record</del> <ins class="ins">cycle or</ins> is <del class="del">defined here</del> <ins class="ins">otherwise not well formed or valid, the
paint table at which the error occurs should be ignored,</ins> that <del class="del">includes a separate alpha
specification</del> <ins class="ins">sub-graph should
not be rendered, and</ins> that <del class="del">supports</del> <ins class="ins">node in the graph should be considered to be visually
bounded. The application should attempt to render the remainder of the graph, if
well formed and valid.

Future minor version updates of the COLR table could introduce new paint
formats. If a paint table with an unrecognized format is encountered, it and its
sub-graph should similarly be ignored, the node should be considered to be
visually bounded, and the application should attempt to render the remainder of
the graph.

If an application is not able to recover from errors while traversing the graph,
it may ignore the color glyph entirely. If the base glyph ID has an outline,
that may be rendered as a non-color glyph instead.

**5.7.11.2 COLR table formats**

Various table and record formats are defined for COLR version 0 and version 1.
Several values contained within the version 1 formats are variable. These use
various record formats that combine a basic data type with a</ins> variation <ins class="ins">delta-set
index: VarFWord, VarUFWord, VarF2Dot14, and VarFixed. These are described</ins> in
<ins class="ins">7.2.3.1.

All table offsets are from the start of the parent table in which the offset is
given, unless otherwise indicated.

**5.7.11.2.1 COLR header**

The COLR table begins with</ins> a <del class="del">variable font.

*ColorIndex record:*</del> <ins class="ins">header. Two versions have been defined. Offsets in
the header are from the start of the table.

**5.7.11.2.1.1 COLR version 0**

*COLR version 0:*</ins>

| Type | Name | Description |
|-|-|-|
| uint16 | <del class="del">paletteIndex | Index for a CPAL palette entry.</del> <ins class="ins">version</ins> | <ins class="ins">Table version number—set to 0.</ins> | <del class="del">VarF2Dot14</del>
| <del class="del">alpha</del> <ins class="ins">uint16</ins> | <del class="del">Variable alpha value.</del> <ins class="ins">numBaseGlyphRecords</ins> |

<del class="del">A paletteIndex value of 0xFFFF is a special case, indicating that the text
foreground color (as determined by the application) is to be used.

The alpha.value is always set explicitly. Values for alpha outside the range
[0., 1.] (inclusive) are reserved; values outside this range shall be clipped. A
value of zero means no opacity (fully transparent); 1.0 means fully opaque (no
transparency). The alpha indicated in this record is multiplied with the alpha
component of the CPAL entry (converted to float—divide by 255). Note that the
resulting alpha value can be combined with and does not supersede alpha or
opacity attributes set in higher-level, application-defined contexts.

See 5.7.11.1.1 for more information regarding color references and solid color
fills.

Gradients are defined using a color line. A color line is a mapping</del> <ins class="ins">Number</ins> of <del class="del">real
numbers to color values, defined using color stops. See 5.7.11.1.2.1 for an
overview and additional details.

*ColorStop record:*</del> <ins class="ins">BaseGlyph records.</ins> | <del class="del">Type</del>
| <del class="del">Name</del> <ins class="ins">Offset32</ins> | <del class="del">Description</del> <ins class="ins">baseGlyphRecordsOffset</ins> |
<del class="del">|-|-|-|</del> <ins class="ins">Offset to baseGlyphRecords array.</ins> | <del class="del">VarF2Dot14</del>
| <del class="del">stopOffset</del> <ins class="ins">Offset32</ins> | <del class="del">Position on a color line; variable.</del> <ins class="ins">layerRecordsOffset</ins> | <ins class="ins">Offset to layerRecords array.</ins> | <del class="del">ColorIndex</del>
| <del class="del">color</del> <ins class="ins">uint16</ins> | <ins class="ins">numLayerRecords</ins> |

<del class="del">A color line is defined by an array</del> <ins class="ins">Number</ins> of <del class="del">ColorStop records plus an extend mode.

*ColorLine table:*</del> <ins class="ins">Layer records. |

NOTE: For fonts that use COLR version 0, some early Windows implementations of
the COLR table require glyph ID 1 to be the .null glyph.

**5.7.11.2.1.2 COLR version 1**

*COLR version 1:*</ins>

| Type | Name | Description |
|-|-|-|
| <del class="del">uint8</del> <ins class="ins">uint16</ins> | <del class="del">extend</del> <ins class="ins">version</ins> | <del class="del">An Extend enum value.</del> <ins class="ins">Table version number—set to 1.</ins> |
| uint16 | <del class="del">numStops</del> <ins class="ins">numBaseGlyphRecords</ins> | Number of <del class="del">ColorStop records. |
| ColorStop | colorStops[numStops] | |

Applications shall apply the colorStops in increasing stopOffset order. The
stopOffset value uses a variable structure and, with a variable font, the
relative orderings of ColorStop records along the color line can change as a
result of variation. With a variable font, the colorStops shall</del> <ins class="ins">BaseGlyph records; may</ins> be <del class="del">ordered after
the instance values for the stop offsets have been derived.

A color line defines stops for only certain positions along the line, but the
color line extends infinitely</del> <ins class="ins">0</ins> in <del class="del">either direction. The extend field is used</del> <ins class="ins">a version 1 table. |
| Offset32 | baseGlyphRecordsOffset | Offset</ins> to
<del class="del">indicate how the color line is extended. The same behavior is used for extension
in both directions. The extend field uses the following enumeration:

*Extend enumeration:*</del> <ins class="ins">baseGlyphRecords array (may be NULL).</ins> | <del class="del">Value</del>
| <del class="del">Name</del> <ins class="ins">Offset32</ins> | <del class="del">Description</del> <ins class="ins">layerRecordsOffset</ins> |
<del class="del">|-|-|-|</del> <ins class="ins">Offset to layerRecords array (may be NULL). |
| uint16 | numLayerRecords</ins> | <ins class="ins">Number of Layer records; may be</ins> 0 <ins class="ins">in a version 1 table.</ins> | <del class="del">EXTEND_PAD</del>
| <del class="del">Use nearest color stop.</del> <ins class="ins">Offset32</ins> | <ins class="ins">baseGlyphV1ListOffset</ins> | <del class="del">1</del> <ins class="ins">Offset to BaseGlyphV1List table.</ins> | <del class="del">EXTEND_REPEAT</del>
| <del class="del">Repeat from farthest color stop.</del> <ins class="ins">Offset32</ins> | <ins class="ins">layersV1Offset</ins> | <del class="del">2</del> <ins class="ins">Offset to LayerV1List table (may be NULL).</ins> | <del class="del">EXTEND_REFLECT</del>
| <del class="del">Mirror color line from nearest end.</del> <ins class="ins">Offset32 | itemVariationStoreOffset | Offset to ItemVariationStore (may be NULL).</ins> |

The <del class="del">extend mode behaviors</del> <ins class="ins">BaseGlyphV1List and its subtables</ins> are <del class="del">described</del> <ins class="ins">only used</ins> in <del class="del">detail</del> <ins class="ins">COLR version 1.

The LayersV1List is only used</ins> in <del class="del">5.7.11.1.2.1. If</del> <ins class="ins">conjunction with the BaseGlyphV1List and,
specifically, with PaintColrLayers tables (5.7.11.2.5.1); it is not required if
no color glyphs use</ins> a
<del class="del">ColorLine</del> <ins class="ins">PaintColrLayers table. If not used, set layersV1Offset to
NULL.

The ItemVariationStore is used</ins> in <del class="del">a</del> <ins class="ins">conjunction with a BaseGlyphV1List and its
subtables, but only in variable fonts. If it is not used, set
itemVariationStoreOffset to NULL.

**5.7.11.2.1.3 Mixing version 0 and version 1 formats**

A</ins> font <del class="del">has an unrecognized extend value, applications should use
EXTEND_PAD by default.

**5.7.11.2.5 Paint tables**

Paint tables are used</del> <ins class="ins">that uses COLR version 1 and that includes a BaseGlyphV1List can also
include BaseGlyph and Layer records</ins> for <ins class="ins">compatibility with applications that
only support COLR version 0.

Color glyphs that can be implemented in</ins> COLR version <ins class="ins">0 using BaseGlyph and Layer
records can also be implemented using the version</ins> 1 <del class="del">color glyph definitions. Eleven Paint
table formats are defined (formats</del> <ins class="ins">BaseGlyphV1List and
subtables. Thus, a font that uses the version</ins> 1 <ins class="ins">formats does not need</ins> to <del class="del">11), each providing</del> <ins class="ins">use the
version 0 BaseGlyph and Layer records. However,</ins> a <del class="del">different graphic
capability for defining</del> <ins class="ins">font may use</ins> the <del class="del">composition</del> <ins class="ins">version 1
structures</ins> for <del class="del">a color glyph. The graphic
capability of each format</del> <ins class="ins">some base glyphs</ins> and the <del class="del">manner in which they are combined to represent</del> <ins class="ins">version 0 structures for other base
glyphs. A font may also include</ins> a <ins class="ins">version 1</ins> color glyph <del class="del">has been described above—see 5.7.11.1.

Each paint table format has</del> <ins class="ins">definition for</ins> a <del class="del">format field as the first field. When parsing font
data, the format field can be read first to determine the format of the table.

**5.7.11.2.5.1 Format 1: PaintColrLayers**

Format 1</del> <ins class="ins">given
base glyph ID that</ins> is <del class="del">used</del> <ins class="ins">equivalent</ins> to <ins class="ins">a version 0 definition, though this should
never be needed. 

A font may</ins> define a <del class="del">vector of layers. The layers are</del> <ins class="ins">color glyph for</ins> a <del class="del">slice of layers
from the LayerV1List table. The first layer is the bottom of the z-order,</del> <ins class="ins">given base glyph ID using version 0
formats,</ins> and
<del class="del">subsequent layers are composited on top</del> <ins class="ins">also define a different color glyph for the same base glyph ID</ins>
using <ins class="ins">version 1 formats. Applications that support COLR version 1 should give
preference to</ins> the <del class="del">COMPOSITE_SRC_OVER composition
mode (see 5.7.11.2.5.10).</del> <ins class="ins">version 1 color glyph.</ins>

For <del class="del">general information on</del> <ins class="ins">applications that support COLR version 1,</ins> the <del class="del">PaintColrLayers table, see 5.7.11.1.4. For
information about its use</del> <ins class="ins">application should search</ins> for <del class="del">shared, re-usable components, see 5.7.11.1.7.2.

*PaintColrLayers table (format 1):*</del>
<ins class="ins">a base glyph ID first in the BaseGlyphV1List. Then, if not found, search in the
baseGlyphRecords array, if present.

**5.7.11.2.2 BaseGlyph and Layer records**

BaseGlyph and Layer records are required for COLR version 0, but optional for
version 1. (See 5.7.11.2.1.3.)

A BaseGlyph record is used to map a base glyph to a sequence of layer records
that define the corresponding color glyph. The BaseGlyph record includes a base
glyph index, an index into the layerRecords array, and the number of layers.

*BaseGlyph record:*</ins>

| Type | <del class="del">Field name</del> <ins class="ins">Name</ins> | Description |
|-|-|-|
| <del class="del">uint8 | format | Set to 1. |
| uint8</del> <ins class="ins">uint16</ins> | <del class="del">numLayers</del> <ins class="ins">glyphID</ins> | <del class="del">Number</del> <ins class="ins">Glyph ID</ins> of <del class="del">offsets to paint tables to read from
LayerV1List.</del> <ins class="ins">the base glyph.</ins> |
| <del class="del">uint32</del> <ins class="ins">uint16</ins> | firstLayerIndex | Index (base 0) into the <del class="del">LayerV1List.</del> <ins class="ins">layerRecords array. |
| uint16</ins> |

<del class="del">NOTE: An 8-bit value is used for</del> numLayers <del class="del">to minimize size for common
scenarios. If more than 256</del> <ins class="ins">| Number of color</ins> layers <del class="del">are needed, then two or more PaintColrLayers
tables can</del> <ins class="ins">associated with this glyph. |

The glyph ID shall</ins> be <del class="del">combined</del> <ins class="ins">less than the numGlyphs value</ins> in <del class="del">a tree using a PaintComposite</del> <ins class="ins">the &#39;maxp&#39;</ins> table <del class="del">or another
PaintColrLayers table to combine them.

**5.7.11.2.5.2 Format 2: PaintSolid**

Format 2</del>
<ins class="ins">(5.2.6).

The BaseGlyph records shall be sorted in increasing glyphID order. It</ins> is <ins class="ins">assumed
that a binary search can be</ins> used to <del class="del">specify a solid color fill. For general information about
specifying color values, see 5.7.11.1.1. For information about applying</del> <ins class="ins">find</ins> a <del class="del">fill
to</del> <ins class="ins">matching BaseGlyph record for</ins> a <del class="del">shape, see 5.7.11.1.3.

*PaintSolid table (format 2):*

| Type | Field name | Description |
|-|-|-|
| uint8 | format | Set to 2. |
| ColorIndex |</del>
<ins class="ins">specific glyphID.

The</ins> color <del class="del">| ColorIndex record</del> <ins class="ins">glyph</ins> for <ins class="ins">a given base glyph is defined by</ins> the <del class="del">solid color fill. |</del> <ins class="ins">consecutive records in
the layerRecords array for the specified number of layers, starting with the
record indicated by firstLayerIndex.</ins> The <del class="del">ColorIndex</del> <ins class="ins">first</ins> record <del class="del">format</del> <ins class="ins">in this sequence</ins> is <del class="del">specified</del> <ins class="ins">the
bottom layer</ins> in <del class="del">5.7.11.2.4.

**5.7.11.2.5.3 Format 3: PaintLinearGradient**

Format 3</del> <ins class="ins">the z-order, and each subsequent layer</ins> is <ins class="ins">stacked on top of the
previous layer.

The layer record sequences for two different base glyphs may overlap, with some
layer records</ins> used <del class="del">to specify a linear gradient fill. For general information
about linear gradients, see 5.7.11.1.2.2.</del> <ins class="ins">in multiple color glyph definitions.</ins>

The <del class="del">PaintLinearGradient table has</del> <ins class="ins">Layer record specifies the glyph used as the graphic element for</ins> a <del class="del">ColorLine subtable. The ColorLine table
format is specified in 5.7.11.2.4. For background information on</del> <ins class="ins">layer and</ins>
the <ins class="ins">solid</ins> color <del class="del">line,
see 5.7.11.1.2.1.

For information about applying a fill to a shape, see 5.7.11.1.3.

*PaintLinearGradient table (format 3):*</del> <ins class="ins">fill.

*Layer record:*</ins>

| Type | <del class="del">Field name</del> <ins class="ins">Name</ins> | Description |
|-|-|-|
| <del class="del">uint8</del> <ins class="ins">uint16</ins> | <del class="del">format</del> <ins class="ins">glyphID</ins> | <del class="del">Set to 3.</del> <ins class="ins">Glyph ID of the glyph used for a given layer.</ins> |
| <del class="del">Offset24</del> <ins class="ins">uint16</ins> | <del class="del">colorLineOffset</del> <ins class="ins">paletteIndex</ins> | <del class="del">Offset to ColorLine</del> <ins class="ins">Index (base 0) for a palette entry in the CPAL</ins> table. |

<ins class="ins">The glyphID in a Layer record shall be less than the numGlyphs value in the
&#39;maxp&#39; table. That is, it shall be a valid glyph with outline data in
the &#39;glyf&#39; (5.3.4), &#39;CFF &#39; (5.4.2) or CFF2 (5.4.3) table. See
5.7.11.1.8.2 for requirements regarding glyph metrics of referenced glyphs.

The paletteIndex value shall be less than the numPaletteEntries value in the
CPAL table (5.7.12). A paletteIndex value of 0xFFFF is a special case,
indicating that the text foreground color (as determined by the application) is
to be used.

**5.7.11.2.3 BaseGlyphV1List and LayerV1List**

The BaseGlyphV1List table is, conceptually, similar to the baseGlyphRecords
array in COLR version 0, providing records that map a base glyph to a color
glyph definition. The color glyph definitions each refer to are significantly
different, however—see 5.7.11.1.

*BaseGlyphV1List table:*</ins>

| <del class="del">VarFWord | x0</del> <ins class="ins">Type</ins> | <del class="del">Start point x coordinate.</del> <ins class="ins">Name</ins> | <ins class="ins">Description</ins> | <del class="del">VarFWord</del>
<ins class="ins">|-|-|-|</ins>
| <del class="del">y0</del> <ins class="ins">uint32</ins> | <del class="del">Start point y coordinate.</del> <ins class="ins">numBaseGlyphV1Records</ins> |  | <del class="del">VarFWord</del>
| <del class="del">x1</del> <ins class="ins">BaseGlyphV1Record</ins> | <del class="del">End point x coordinate.</del> <ins class="ins">baseGlyphV1Records[numBaseGlyphV1Records]</ins> | | <del class="del">VarFWord</del>

<ins class="ins">*BaseGlyphV1Record:*</ins>

| <del class="del">y1</del> <ins class="ins">Type</ins> | <del class="del">End point y coordinate.</del> <ins class="ins">Name</ins> | <ins class="ins">Description</ins> | <del class="del">VarFWord</del>
<ins class="ins">|-|-|-|</ins>
| <del class="del">x2</del> <ins class="ins">uint16</ins> | <del class="del">Rotation vector end point x coordinate.</del> <ins class="ins">glyphID</ins> | <ins class="ins">Glyph ID of the base glyph.</ins> | <del class="del">VarFWord</del>
| <del class="del">y2</del> <ins class="ins">Offset32</ins> | <del class="del">Rotation vector end point y coordinate.</del> <ins class="ins">paintOffset</ins> |

<del class="del">**5.7.11.2.5.4 Format 4: PaintRadialGradient**

Format 4 is used</del> <ins class="ins">Offset</ins> to <del class="del">specify a radial gradient fill. For general information
about radial gradients supported in COLR version 1, see 5.7.11.1.2.3. 

The PaintRadialGradient table has</del> a <del class="del">ColorLine subtable.</del> <ins class="ins">Paint table. |</ins>

The <del class="del">ColorLine table
format</del> <ins class="ins">glyph ID</ins> is <del class="del">specified</del> <ins class="ins">not limited to the numGlyphs value</ins> in <del class="del">5.7.11.2.4. For background information on</del> the <ins class="ins">&#39;maxp&#39; table
(5.2.6). See 5.7.11.1.7.3 for more information.

The records in the baseGlyphV1Records array shall be sorted in increasing
glyphID order. It is intended that a binary search can be used to find a
matching BaseGlyphV1Record for a specific glyphID.

The paint table referenced by the BaseGlyphV1Record is the root of the graph for
a</ins> color <del class="del">line,
see 5.7.11.1.2.1.

For</del> <ins class="ins">glyph definition.

NOTE: Often the paint table that is the root of the graph for the color glyph
definition will be a PaintColrLayers table, though this is not required. See
5.7.11.1.9 for more</ins> information <del class="del">about applying</del> <ins class="ins">regarding the graph of</ins> a <del class="del">fill</del> <ins class="ins">color glyph, and
5.7.11.1.4 for background information regarding the PaintColrLayers table.

A LayerV1List table is used in conjunction with PaintColrLayers tables</ins> to
<ins class="ins">represent layer structures. A single LayerV1List is defined and can be used by
multiple PaintColrLayer tables, each of which references</ins> a <del class="del">shape, see 5.7.11.1.3.

*PaintRadialGradient table (format 4):*</del> <ins class="ins">slice of the layer
list.

*LayerV1List table:*</ins>

| Type | <del class="del">Field name</del> <ins class="ins">Name</ins> | Description |
|-|-|-|
| <del class="del">uint8</del> <ins class="ins">uint32</ins> | <del class="del">format</del> <ins class="ins">numLayers</ins> | <del class="del">Set to 4.</del>  |
| <del class="del">Offset24</del> <ins class="ins">Offset32</ins> | <del class="del">colorLineOffset</del> <ins class="ins">paintOffsets[numLayers]</ins> | <del class="del">Offset</del> <ins class="ins">Offsets</ins> to <del class="del">ColorLine table. |
| VarFWord | x0 | Start circle center x coordinate. |
| VarFWord | y0 | Start circle center y coordinate. |
| VarUFWord | radius0 | Start circle radius. |
| VarFWord | x1 | End circle center x coordinate. |
| VarFWord | y1 | End circle center y coordinate. |
| VarUFWord | radius1 | End circle radius.</del> <ins class="ins">Paint tables.</ins> |

<del class="del">**5.7.11.2.5.5 Format 5: PaintGlyph**

Format 5 is used</del>

<ins class="ins">The sequence of offsets to paint tables corresponds</ins> to <del class="del">specify</del> a <del class="del">glyph outline to use as a shape to be filled or,
equivalently, a clip region. The outline sets a clip region that constrains</del> <ins class="ins">bottom-up z-order
layering of</ins> the
<del class="del">content</del> <ins class="ins">graphic compositions defined by the sub-graph</ins> of <ins class="ins">each referenced
paint table graph. For</ins> a <del class="del">separate</del> <ins class="ins">given slice of the list, the sub-graph of the first</ins>
paint <del class="del">subtable</del> <ins class="ins">table defines the element at the bottom of the z-order,</ins> and the sub-graph <del class="del">linked from</del>
<ins class="ins">of each subsequent paint table defines an element</ins> that
<del class="del">subtable.

For information about applying a fill to</del> <ins class="ins">is layered on top of the
previous element. As each element is</ins> a <del class="del">shape, see 5.7.11.1.3.

*PaintGlyph table (format 5):*

| Type | Field name | Description |
|-|-|-|
| uint8 | format | Set to 5. |
| Offset24 | paintOffset | Offset to</del> <ins class="ins">composition defined in</ins> a <del class="del">Paint table. |
| uint16 | glyphID | Glyph ID for the source outline. |

The glyphID value shall</del> <ins class="ins">sub-graph, one
of these elements may itself</ins> be <del class="del">less than</del> <ins class="ins">multi-layered. In that case,</ins> the <del class="del">numGlyphs value in</del> <ins class="ins">layers of this
element are stacked above all previous layers, and layers of following elements
are stacked above</ins> the <del class="del">&#39;maxp&#39;</del> <ins class="ins">top layer of this element.

Offsets for paint tables not referenced by any PaintColrLayers</ins> table <del class="del">(5.2.6). That is, it shall</del> <ins class="ins">should not</ins>
be <del class="del">a valid glyph with outline data</del> <ins class="ins">included</ins> in the
<del class="del">&#39;glyf&#39; (5.3.4), &#39;CFF &#39; (5.4.2) or CFF2 (5.4.3) table.

**5.7.11.2.5.6 Format 6: PaintColrGlyph**

Format 6 is</del> <ins class="ins">paintOffsets array.

**5.7.11.2.4 ColorIndex, ColorStop and ColorLine**

Colors are</ins> used <del class="del">to allow</del> <ins class="ins">in solid color fills for graphic elements, or as *stops* in</ins> a
color <del class="del">glyph definition from the BaseGlyphV1List</del> <ins class="ins">line used</ins> to
<del class="del">be</del> <ins class="ins">define</ins> a <del class="del">re-usable component that can be incorporated into multiple color glyph
definitions. See 5.7.11.1.7.3 for more information.

*PaintColrGlyph table (format 6):*

| Type | Field name | Description |
|-|-|-|
| uint8 | format | Set to 6. |
| uint16 | glyphID | Virtual glyph ID for a BaseGlyphV1List base glyph. |

The glyphID value shall be a glyphID found in a BaseGlyphV1Record within the
BaseGlyphV1List. It may be a *virtual* glyph ID, greater than or equal</del> <ins class="ins">gradient. Colors are defined by reference</ins> to <del class="del">the
numGlyph value</del> <ins class="ins">palette
entries</ins> in the <del class="del">&#39;maxp&#39;</del> <ins class="ins">CPAL</ins> table <del class="del">(5.2.6). The BaseGlyphV1Record
provides</del> <ins class="ins">(5.7.12). While CPAL entries include</ins> an <del class="del">offset to a paint table; that paint table and the graph linked from
it</del> <ins class="ins">alpha
component, color-index records for referencing palette entries</ins> are <del class="del">incorporated as</del> <ins class="ins">defined here
that includes</ins> a <del class="del">child sub-graph of the PaintColrGlyph table within the
current color glyph definition.

**5.7.11.2.5.7 Format 7: PaintTransformed**

Format 7 is used</del> <ins class="ins">separate alpha specification</ins> to <del class="del">apply an affine transformation</del> <ins class="ins">allow different graphic elements</ins>
to <del class="del">a sub-graph. The paint
table that is</del> <ins class="ins">use</ins> the <del class="del">root</del> <ins class="ins">same color but with different alpha values, and to allow for
variation</ins> of the <del class="del">sub-graph is linked as a child. See 5.7.11.1.5 for
general information regarding transformations</del> <ins class="ins">alpha</ins> in <del class="del">a color glyph definition.

*PaintTransformed table (format 7):*</del> <ins class="ins">variable fonts.

Two color-index record formats are defined: one that allows for variation of
alpha, and one that does not.

*ColorIndex record:*</ins>

| Type | <del class="del">Field name</del> <ins class="ins">Name</ins> | Description |
|-|-|-|
| <del class="del">uint8</del> <ins class="ins">uint16</ins> | <del class="del">format</del> <ins class="ins">paletteIndex</ins> | <del class="del">Set to 7.</del> <ins class="ins">Index for a CPAL palette entry.</ins> |
| <del class="del">Offset24</del> <ins class="ins">F2DOT14</ins> | <del class="del">paintOffset</del> <ins class="ins">alpha</ins> | <del class="del">Offset to a Paint subtable.</del> <ins class="ins">Alpha value.</ins> |

<ins class="ins">*VarColorIndex record:*</ins>

| <del class="del">Affine2x3</del> <ins class="ins">Type</ins> | <del class="del">transform</del> <ins class="ins">Name</ins> | <del class="del">An Affine2x3 record (inline).</del> <ins class="ins">Description</ins> |
<ins class="ins">|-|-|-|
| uint16 | paletteIndex | Index for a CPAL palette entry. |
| VarF2Dot14 | alpha | Variable alpha value. |

A paletteIndex value of 0xFFFF is a special case, indicating that the text
foreground color (as determined by the application) is to be used.</ins>

The <del class="del">affine transformation is defined by a 2×3 matrix, specified in an Affine2x3
record. The 2×3 matrix supports scale, skew, reflection, rotation, and
translation transformations.</del> <ins class="ins">alpha value (alpha.value for VarF2Dot14) is always set explicitly. Values
for alpha outside the range [0., 1.] (inclusive) are reserved; values outside
this range shall be clipped. A value of zero means no opacity (fully
transparent); 1.0 means fully opaque (no transparency).</ins> The <del class="del">matrix elements use VarFixed records, allowing</del> <ins class="ins">alpha indicated in
this record is multiplied with</ins> the <del class="del">transform definition</del> <ins class="ins">alpha component of the CPAL entry (converted</ins>
to <ins class="ins">float—divide by 255). Note that the resulting alpha value can</ins> be <del class="del">variable</del> <ins class="ins">combined
with and does not supersede alpha or opacity attributes set</ins> in <ins class="ins">higher-level,
application-defined contexts.

See 5.7.11.1.1 for more information regarding color references and solid color
fills.

Gradients are defined using</ins> a <del class="del">variable font.

*Affine2x3</del> <ins class="ins">color line. A color line is a mapping of real
numbers to color values, defined using color stops. See 5.7.11.1.2.1 for an
overview and additional details.

Two color-stop record formats are defined: one that allows for variation of
stop offset position or of alpha, and one that does not.

*ColorStop</ins> record:*

| Type | Name | Description |
|-|-|-|
| <del class="del">VarFixed</del> <ins class="ins">F2DOT14</ins> | <del class="del">xx</del> <ins class="ins">stopOffset</ins> | <del class="del">x-component of transformed x-basis vector</del> <ins class="ins">Position on a color line.</ins> |
| <del class="del">VarFixed</del> <ins class="ins">ColorIndex</ins> | <del class="del">yx</del> <ins class="ins">color</ins> | <del class="del">y-component of transformed x-basis vector</del> |

<ins class="ins">*VarColorStop record:*</ins>

| <del class="del">VarFixed</del> <ins class="ins">Type</ins> | <del class="del">xy</del> <ins class="ins">Name</ins> | <del class="del">x-component of transformed y-basis vector</del> <ins class="ins">Description</ins> |
<ins class="ins">|-|-|-|</ins>
| <del class="del">VarFixed</del> <ins class="ins">VarF2Dot14</ins> | <del class="del">yy</del> <ins class="ins">stopOffset</ins> | <del class="del">y-component of transformed y-basis vector |
| VarFixed | dx | Translation in x direction.</del> <ins class="ins">Position on a color line; variable.</ins> |
| <del class="del">VarFixed</del> <ins class="ins">VarColorIndex</ins> | <del class="del">dy</del> <ins class="ins">color</ins> | <del class="del">Translation in y direction.</del> |

<del class="del">For a pre-transformation position *(x, y)*,  The post-transformation position *(x&#x2032;, y&#x2032;)* is calculated as follows:

*x&#x2032;* = *xx \* x + xy \* y + dx*  
*y&#x2032;* = *yx \* x + yy \* y + dy*

NOTE: It</del>

<ins class="ins">A color line</ins> is <del class="del">helpful to understand linear transformations by their effect
on *x-* and *y-basis* vectors _î = (1, 0)_ and _ĵ = (0, 1)_. The transform
described</del> <ins class="ins">defined</ins> by <del class="del">the Affine2x3 record maps the basis vectors to _î&#x2032; = (xx,
yx)_ 
and _ĵ&#x2032; = (xy, yy)_, and translates the origin to _(dx, dy)_.

When the transformed composition from the referenced paint</del> <ins class="ins">an array of ColorStop records plus an extend mode.

Two color-line</ins> table <del class="del">(and its
sub-graph) is composed into the destination (represented by the parent</del> <ins class="ins">formats are defined: one that allows for variation</ins> of <del class="del">this
table), the source design grid origin is aligned to the destination design grid
origin. The transform can translate the source such</del> <ins class="ins">color
stop offsets positions or of alpha values, and one</ins> that <del class="del">a pre-transform
position (0,0) is moved elsewhere. The *post-transform* origin, (0,0), is
aligned to the destination origin.

**5.7.11.2.5.8 Format 8: PaintTranslate**

Format 8 is used to apply a translation to a sub-graph. The</del> <ins class="ins">does not. Different</ins>
paint table <del class="del">that is</del> <ins class="ins">formats for gradients use one or</ins> the <del class="del">root</del> <ins class="ins">other</ins> of the <del class="del">sub-graph is linked as a child.

See 5.7.11.1.5 for general information regarding transformations in a</del> color
<del class="del">glyph definition.

*PaintTranslate table (format 8):*</del> <ins class="ins">line
formats.

*ColorLine table:*</ins>

| Type | <del class="del">Field name</del> <ins class="ins">Name</ins> | Description |
|-|-|-|
| uint8 | <del class="del">format | Set to 8. |
| Offset24 | paintOffset</del> <ins class="ins">extend</ins> | <del class="del">Offset to a Paint subtable.</del> <ins class="ins">An Extend enum value.</ins> |
| <del class="del">VarFixed</del> <ins class="ins">uint16</ins> | <del class="del">dx</del> <ins class="ins">numStops</ins> | <del class="del">Translation in x direction.</del> <ins class="ins">Number of ColorStop records.</ins> |
| <del class="del">VarFixed</del> <ins class="ins">ColorStop</ins> | <del class="del">dy</del> <ins class="ins">colorStops[numStops]</ins> | <del class="del">Translation in y direction.</del> |

<del class="del">NOTE: Pure translation can also be represented using the PaintTransformed table
by setting _xx_ = 1, _yy_ = 1, _xy_ and _yx_ = 0, and setting _dx_ and _dy_ to
the translation values. The PaintTranslate table provides a more compact
representation when only translation is required.

The translation will result in the pre-transform position (0,0) being moved
elsewhere. See 5.7.11.2.5.7 regarding alignment of the transformed content with
the destination.

**5.7.11.2.5.9 Format 9: PaintRotate**

Format 9 is used to apply a rotation to a sub-graph. The paint table that is the
root of the sub-graph is linked as a child. The amount of rotation is expressed
directly as an angle, and X and Y coordinates can be provided for the center of
rotation.

See 5.7.11.1.5 for general information regarding transformations in a color
glyph definition.

*PaintRotate table (format 9):*</del>

<ins class="ins">*VarColorLine table:*</ins>

| Type | <del class="del">Field name</del> <ins class="ins">Name</ins> | Description |
|-|-|-|
| uint8 | <del class="del">format | Set to 9. |
| Offset24 | paintOffset | Offset to a Paint subtable. |
| VarFixed | angle</del> <ins class="ins">extend</ins> | <del class="del">Rotation angle, in counter-clockwise degrees.</del> <ins class="ins">An Extend enum value.</ins> |
| <del class="del">VarFixed</del> <ins class="ins">uint16</ins> | <del class="del">centerX</del> <ins class="ins">numStops</ins> | <del class="del">x coordinate for the center</del> <ins class="ins">Number</ins> of <del class="del">rotation.</del> <ins class="ins">ColorStop records.</ins> |
| <del class="del">VarFixed</del> <ins class="ins">VarColorStop</ins> | <del class="del">centerY</del> <ins class="ins">colorStops[numStops]</ins> | <del class="del">y coordinate</del> <ins class="ins">Allows</ins> for <del class="del">the center of rotation.</del> <ins class="ins">variations.</ins> |

<del class="del">NOTE: Pure rotation can also be represented using the PaintTransformed table.
For rotation about</del>

<ins class="ins">Applications shall apply</ins> the <del class="del">origin, this could be done by setting matrix values as
follows for angle &theta;: 

* _xx_ = cos(&theta;)
* _yx_ = sin(&theta;)
* _xy_ = -sin(&theta;)
* _yy_ = cos(&theta;)
* _dx_ = _dy_ = 0</del> <ins class="ins">colorStops in increasing stopOffset order.</ins> The <del class="del">important difference</del>
<ins class="ins">stopOffset value uses a variable structure and, with a variable font, the
relative orderings</ins> of <ins class="ins">ColorStop records along</ins> the <del class="del">PaintRotate table is in allowing an angle to be
specified directly in degrees, rather than</del> <ins class="ins">color line can change</ins> as <del class="del">changes to basis vectors. In
variable fonts, if</del> a <del class="del">rotation angle needs to vary, it is easier to get smooth
variation if an angle is specified directly than when using trigonometric
functions to derive matrix elements.

A rotation can</del>
result <del class="del">in the pre-transform position (0, 0) being moved
elsewhere. See 5.7.11.2.5.7 regarding alignment</del> of <ins class="ins">variation. With a variable font,</ins> the <del class="del">transformed content with</del> <ins class="ins">colorStops shall be ordered after</ins>
the <del class="del">destination.

**5.7.11.2.5.10 Format 10: PaintSkew**

Format 10</del> <ins class="ins">instance values for the stop offsets have been derived.

A color line defines stops for only certain positions along the line, but the
color line extends infinitely in either direction. The extend field</ins> is used to <del class="del">apply a skew to a sub-graph. The paint table that is the
root of</del>
<ins class="ins">indicate how</ins> the <del class="del">sub-graph</del> <ins class="ins">color line</ins> is <del class="del">linked as a child.</del> <ins class="ins">extended.</ins> The <del class="del">amount of skew in the X or Y
direction</del> <ins class="ins">same behavior</ins> is <del class="del">expressed directly as angles, and X and Y coordinates can be
provided for the center of rotation.

See 5.7.11.1.5</del> <ins class="ins">used</ins> for <del class="del">general information regarding transformations</del> <ins class="ins">extension</ins>
in <del class="del">a color
glyph definition.

*PaintSkew table (format 10):*</del> <ins class="ins">both directions. The extend field uses the following enumeration:

*Extend enumeration:*</ins>

| <del class="del">Type</del> <ins class="ins">Value</ins> | <del class="del">Field name</del> <ins class="ins">Name</ins> | Description |
|-|-|-|
| <del class="del">uint8</del> <ins class="ins">0</ins> | <del class="del">format</del> <ins class="ins">EXTEND_PAD</ins>     | <del class="del">Set to 10.</del> <ins class="ins">Use nearest color stop.</ins> |
| <del class="del">Offset24</del> <ins class="ins">1</ins> | <del class="del">paintOffset</del> <ins class="ins">EXTEND_REPEAT</ins>  | <del class="del">Offset to a Paint subtable.</del> <ins class="ins">Repeat from farthest color stop.</ins> |
| <del class="del">VarFixed</del> <ins class="ins">2</ins> | <del class="del">xSkewAngle</del> <ins class="ins">EXTEND_REFLECT</ins> | <del class="del">Angle of skew</del> <ins class="ins">Mirror color line from nearest end. |

The extend mode behaviors are described in detail in 5.7.11.1.2.1. If a
ColorLine in a font has an unrecognized extend value, applications should use
EXTEND_PAD by default.

**5.7.11.2.5 Paint tables**

Paint tables are used for COLR version 1 color glyph definitions. Twenty Paint
table formats are defined (formats 1 to 20). Some formats come</ins> in <ins class="ins">non-variable
and variable pairs, but otherwise, each provides different graphic capability
for defining</ins> the <del class="del">direction</del> <ins class="ins">composition for a color glyph. The graphic capability</ins> of <ins class="ins">each
format and</ins> the <del class="del">x-axis,</del> <ins class="ins">manner</ins> in <del class="del">counter-clockwise degrees. |
| VarFixed | ySkewAngle | Angle</del> <ins class="ins">which they are combined to represent a color glyph has
been described above—see 5.7.11.1.

Each paint table format has a one-byte format field as the first field. When
parsing font data, the format field can be read first to determine the format</ins> of <del class="del">skew in</del>
the <del class="del">direction</del> <ins class="ins">table.

**5.7.11.2.5.1 Format 1: PaintColrLayers**

Format 1 is used to define a vector of layers. The layers are a slice</ins> of <ins class="ins">layers
from</ins> the <del class="del">y-axis, in counter-clockwise degrees. |
| VarFixed | centerX | x coordinate for</del> <ins class="ins">LayerV1List table. The first layer is</ins> the <del class="del">center</del> <ins class="ins">bottom</ins> of <del class="del">rotation. |
| VarFixed | centerY | y coordinate for</del> the <del class="del">center of rotation. |

NOTE: Pure skews can also be represented</del> <ins class="ins">z-order, and
subsequent layers are composited on top</ins> using the <del class="del">PaintTransformed table.</del> <ins class="ins">COMPOSITE_SRC_OVER composition
mode (see 5.7.11.2.5.12).</ins>

For
<del class="del">skews about</del> <ins class="ins">general information on</ins> the <del class="del">origin, this could be done by setting matrix values as follows
for _x_ skew angle &phi; and _y_ skew angle &psi;:

* _xx_ = _yy_ = 1
* _yx_ = tan(&psi;)
* _xy_ = -tan(&phi;)
* _dx_ = _dy_ = 0

The important difference of the PaintSkew table is in being able to specify skew
as an angle, rather than as changes to basis vectors. In variable fonts, if a
skew angle needs to vary, it is easier to get smooth variation if an angle is
specified directly than when using trigonometric functions to derive matrix
elements.

A skew can result in the pre-transform position (0, 0) being moved elsewhere.
See 5.7.11.2.5.7 regarding alignment of the transformed content with the
destination.

**5.7.11.2.5.11 Format 11: PaintComposite**

Format 11 is used to combine two layered compositions, referred to as *source*
and *backdrop*, using different compositing or blending modes. The available
compositing and blending modes are defined in an enumeration. See 5.7.11.1.6 for
general</del> <ins class="ins">PaintColrLayers table, see 5.7.11.1.4. For</ins>
information <del class="del">and examples.

NOTE: The backdrop is also referred to as the “destination”.

*PaintComposite</del> <ins class="ins">about its use for shared, re-usable components, see 5.7.11.1.7.2.

*PaintColrLayers</ins> table (format <del class="del">11):*</del> <ins class="ins">1):*</ins>

| Type | <del class="del">Field name</del> <ins class="ins">Name</ins> | Description |
|-|-|-|
| uint8 | format | Set to <del class="del">11. |
| Offset24 | sourcePaintOffset | Offset to a source Paint table.</del> <ins class="ins">1.</ins> |
| uint8 | <del class="del">compositeMode | A CompositeMode enumeration value.</del> <ins class="ins">numLayers</ins> | <ins class="ins">Number of offsets to paint tables to read from LayerV1List.</ins> | <del class="del">Offset24</del>
| <del class="del">backdropPaintOffset</del> <ins class="ins">uint32</ins> | <del class="del">Offset to a backdrop Paint table, from start of PaintComposite table.</del> <ins class="ins">firstLayerIndex</ins> |

<del class="del">The compositionMode value must be one of the values defined in</del> <ins class="ins">Index (base 0) into</ins> the <del class="del">CompositeMode enumeration. If an unrecognized</del> <ins class="ins">LayerV1List. |

NOTE: An 8-bit</ins> value is <del class="del">encountered, COMPOSITE_CLEAR</del> <ins class="ins">used for numLayers to minimize size for common
scenarios. If more than 256 layers are needed, then two or more PaintColrLayers
tables can be combined in a tree using a PaintComposite table or another
PaintColrLayers table to combine them.

**5.7.11.2.5.2 Formats 2 and 3: PaintSolid, PaintVarSolid**

Formats 2 and 3 are used to specify a solid color fill. Format 3 allows for
variation of alpha in a variable font; format 2 provides a more compact
representation when variation is not required. Format 3</ins> shall <ins class="ins">not</ins> be <del class="del">used.

*CompositeMode enumeration:*</del> <ins class="ins">used in
non-variable fonts or if the COLR table does not have an ItemVariationStore
subtable.

For general information about specifying color values, see 5.7.11.1.1. For
information about applying a fill to a shape, see 5.7.11.1.3.

*PaintSolid table (format 2):*</ins>

| <del class="del">Value</del> <ins class="ins">Type</ins> | Name | Description |
|-|-|-|
| <ins class="ins">uint8</ins> | <del class="del">*Porter-Duff modes* |</del> <ins class="ins">format</ins> | <ins class="ins">Set to 2.</ins> | <del class="del">0</del>
| <del class="del">COMPOSITE_CLEAR</del> <ins class="ins">ColorIndex</ins> | <del class="del">See [Clear][2]</del> <ins class="ins">color</ins> | <ins class="ins">ColorIndex record for the solid color fill.</ins> | <del class="del">1</del>

<ins class="ins">*PaintVarSolid table (format 3):*</ins>

| <del class="del">COMPOSITE_SRC</del> <ins class="ins">Type</ins> | <del class="del">See [Copy][3]</del> <ins class="ins">Name</ins> | <ins class="ins">Description</ins> | <del class="del">2</del>
<ins class="ins">|-|-|-|</ins>
| <del class="del">COMPOSITE_DEST</del> <ins class="ins">uint8</ins> | <del class="del">See [Destination][4]</del> <ins class="ins">format</ins> | <ins class="ins">Set to 3.</ins> | <del class="del">3</del>
| <del class="del">COMPOSITE_SRC_OVER</del> <ins class="ins">VarColorIndex</ins> | <del class="del">See [Source Over][5]</del> <ins class="ins">color</ins> | <ins class="ins">VarColorIndex record for the solid color fill.</ins> |

<ins class="ins">For the ColorIndex and VarColorIndex record formats, see 5.7.11.2.4.

**5.7.11.2.5.3 Formats</ins> 4 <del class="del">| COMPOSITE_DEST_OVER | See [Destination Over][6] |
|</del> <ins class="ins">and 5: PaintLinearGradient, PaintVarLinearGradient**

Formats 4 and</ins> 5 <ins class="ins">are used to specify a linear gradient fill. Format 4 allows for
variation of color stop positions or of alpha in a variable font; format 5
provides a more compact representation when variation is not required. Format 5
shall not be used in non-variable fonts or if the COLR table does not have an
ItemVariationStore subtable.

For general information about linear gradients, see 5.7.11.1.2.2. For
information about applying a fill to a shape, see 5.7.11.1.3.

The PaintLinearGradient and PaintVarLinearGradient tables have a ColorLine and
VarColorLine subtable, respectively. For the ColorLine and VarColorLine table
formats, see 5.7.11.2.4. For background information on the color line, see
5.7.11.1.2.1.

*PaintLinearGradient table (format 4):*</ins>

| <del class="del">COMPOSITE_SRC_IN | See [Source In][7] |
| 6 | COMPOSITE_DEST_IN | See [Destination In][8] |
| 7 | COMPOSITE_SRC_OUT | See [Source Out][9] |
| 8 | COMPOSITE_DEST_OUT | See [Destination Out][10] |
| 9 | COMPOSITE_SRC_ATOP | See [Source Atop][11] |
| 10 | COMPOSITE_DEST_ATOP | See [Destination Atop][12] |
| 11 | COMPOSITE_XOR | See [XOR][13] |</del> <ins class="ins">Type</ins> | <ins class="ins">Name</ins> | <del class="del">*Separable color blend modes:*</del> <ins class="ins">Description</ins> |
<ins class="ins">|-|-|-|</ins>
| <ins class="ins">uint8</ins> | <del class="del">12</del> <ins class="ins">format</ins> | <del class="del">COMPOSITE_SCREEN</del> <ins class="ins">Set to 4.</ins> | <del class="del">See [screen blend mode][14]</del>
| <ins class="ins">Offset24</ins> | <del class="del">13</del> <ins class="ins">colorLineOffset</ins> | <del class="del">COMPOSITE_OVERLAY</del> <ins class="ins">Offset to ColorLine table.</ins> | <del class="del">See [overlay blend mode][15]</del>
| <ins class="ins">FWORD</ins> | <del class="del">14</del> <ins class="ins">x0</ins> | <del class="del">COMPOSITE_DARKEN</del> <ins class="ins">Start point (p₀) x coordinate.</ins> | <del class="del">See [darken blend mode][16]</del>
| <ins class="ins">FWORD</ins> | <del class="del">15</del> <ins class="ins">y0</ins> | <del class="del">COMPOSITE_LIGHTEN</del> <ins class="ins">Start point (p₀) y coordinate.</ins> | <del class="del">See [lighten blend mode][17]</del>
| <ins class="ins">FWORD</ins> | <del class="del">16</del> <ins class="ins">x1</ins> | <del class="del">COMPOSITE_COLOR_DODGE</del> <ins class="ins">End point (p₁) x coordinate.</ins> | <del class="del">See [color-dodge blend mode][18]</del>
| <ins class="ins">FWORD</ins> | <del class="del">17</del> <ins class="ins">y1</ins> | <del class="del">COMPOSITE_COLOR_BURN</del> <ins class="ins">End point (p₁) y coordinate.</ins> | <del class="del">See [color-burn blend mode][19]</del>
| <ins class="ins">FWORD</ins> | <del class="del">18</del> <ins class="ins">x2</ins> | <del class="del">COMPOSITE_HARD_LIGHT</del> <ins class="ins">Rotation point (p₂) x coordinate.</ins> | <del class="del">See [hard-light blend mode][20]</del>
| <ins class="ins">FWORD</ins> | <del class="del">19</del> <ins class="ins">y2</ins> | <del class="del">COMPOSITE_SOFT_LIGHT</del> <ins class="ins">Rotation point (p₂) y coordinate.</ins> | <del class="del">See [soft-light blend mode][21]</del>

<ins class="ins">*PaintVarLinearGradient table (format 5):*</ins>

| <ins class="ins">Type</ins> | <del class="del">20</del> <ins class="ins">Name</ins> | <del class="del">COMPOSITE_DIFFERENCE</del> <ins class="ins">Description</ins> | <del class="del">See [difference blend mode][22]</del>
<ins class="ins">|-|-|-|</ins>
| <ins class="ins">uint8</ins> | <del class="del">21</del> <ins class="ins">format</ins> | <del class="del">COMPOSITE_EXCLUSION</del> <ins class="ins">Set to 5.</ins> | <del class="del">See [exclusion blend mode][23]</del>
| <ins class="ins">Offset24</ins> | <del class="del">22</del> <ins class="ins">colorLineOffset</ins> | <del class="del">COMPOSITE_MULTIPLY</del> <ins class="ins">Offset to VarColorLine table.</ins> | <del class="del">See [multiply blend mode][24]</del>
| <ins class="ins">VarFWord</ins> | <ins class="ins">x0</ins> | <del class="del">*Non-separable color blend modes:*</del> <ins class="ins">Start point (p₀) x coordinate. |
| VarFWord</ins> | <ins class="ins">y0</ins> | <ins class="ins">Start point (p₀) y coordinate. |
| VarFWord | x1 | End point (p₁) x coordinate. |
| VarFWord | y1 | End point (p₁) y coordinate. |
| VarFWord | x2 | Rotation point (p₂) x coordinate. |
| VarFWord | y2 | Rotation point (p₂) y coordinate. |

**5.7.11.2.5.4 Formats 6 and 7: PaintRadialGradient, PaintVarRadialGradient**

Format 6 and 7 are used to specify a radial gradient fill. Format 7 allows for
variation of color stop positions or of alpha in a variable font; format 6
provides a more compact representation when variation is not required. Format 7
shall not be used in non-variable fonts or if the COLR table does not have an
ItemVariationStore subtable.

For general information about radial gradients supported in COLR version 1, see
5.7.11.1.2.3. For information about applying a fill to a shape, see 5.7.11.1.3.

The PaintRadialGradient and PaintVarRadialGradient tables have a ColorLine and
VarColorLine subtable, respectively. For the ColorLine and VarColorLine table
formats, see in 5.7.11.2.4. For background information on the color line, see
5.7.11.1.2.1.

*PaintRadialGradient table (format 6):*

| Type | Name | Description |
|-|-|-|
| uint8 | format | Set to 6. |
| Offset24 | colorLineOffset | Offset to ColorLine table. |
| FWORD | x0 | Start circle center x coordinate. |
| FWORD | y0 | Start circle center y coordinate. |
| UFWORD | radius0 | Start circle radius. |
| FWORD | x1 | End circle center x coordinate. |
| FWORD | y1 | End circle center y coordinate. |
| UFWORD | radius1 | End circle radius. |

*PaintVarRadialGradient table (format 7):*

| Type | Name | Description |
|-|-|-|
| uint8 | format | Set to 7. |
| Offset24 | colorLineOffset | Offset to VarColorLine table. |
| VarFWord | x0 | Start circle center x coordinate. |
| VarFWord | y0 | Start circle center y coordinate. |
| VarUFWord | radius0 | Start circle radius. |
| VarFWord | x1 | End circle center x coordinate. |
| VarFWord | y1 | End circle center y coordinate. |
| VarUFWord | radius1 | End circle radius. |

**5.7.11.2.5.5 Formats 8 and 9: PaintSweepGradient, PaintVarSweepGradient**

Format 8 and 9 are used to specify a sweep gradient fill. Format 9 allows for
variation of color stop positions or of alpha in a variable font; format 8
provides a more compact representation when variation is not required. Format 9
shall not be used in non-variable fonts or if the COLR table does not have an
ItemVariationStore subtable.

For general information about sweep gradients, see 5.7.11.1.2.4. For information
about applying a fill to a shape, see 5.7.11.1.3.

The PaintSweepGradient and PaintVarSweepGradient table have a ColorLine and
VarColorLine subtable, respectively. For the ColorLine and VarColorLine table
formats, see 5.7.11.2.4. For background information on the color line, see
5.7.11.1.2.1.

*PaintSweepGradient table (format 8):*

| Type | Name | Description |
|-|-|-|
| uint8 | format | Set to 8. |
| Offset24 | colorLineOffset | Offset to ColorLine table. |
| FWORD | centerX | Center x coordinate. |
| FWORD | centerY | Center y coordinate. |
| Fixed | startAngle | Start of the angular range of the gradient. |
| Fixed | endAngle | End of the angular range of the gradient. |

*PaintVarSweepGradient table (format 9):*

| Type | Name | Description |
|-|-|-|
| uint8 | format | Set to 9. |
| Offset24 | colorLineOffset | Offset to VarColorLine table. |
| VarFWord | centerX | Center x coordinate. |
| VarFWord | centerY | Center y coordinate. |
| VarFixed | startAngle | Start of the angular range of the gradient. |
| VarFixed | endAngle | End of the angular range of the gradient. |

Angles are expressed in counter-clockwise degrees from the direction of the
positive x-axis in the design grid.

**5.7.11.2.5.5 Format 10: PaintGlyph**

Format 10 is used to specify a glyph outline to use as a shape to be filled or,
equivalently, a clip region. The outline sets a clip region that constrains the
content of a separate paint subtable and the sub-graph linked from that
subtable.

For information about applying a fill to a shape, see 5.7.11.1.3.

*PaintGlyph table (format 10):*

| Type | Name | Description |
|-|-|-|
| uint8 | format | Set to 10. |
| Offset24 | paintOffset | Offset to a Paint table. |
| uint16 | glyphID | Glyph ID for the source outline. |

The glyphID value shall be less than the numGlyphs value in the &#39;maxp&#39;
table (5.2.6). That is, it shall be a valid glyph with outline data in the
&#39;glyf&#39; (5.3.4), &#39;CFF &#39; (5.4.2) or CFF2 (5.4.3) table. Only that
outline data is used. In particular, if this glyph ID has a description in the
COLR table (glyphID appears in a COLR BaseGlyph record or the BaseGlyphV1List),
that COLR data is not relevant for purposes of the PaintGlyph table.

**5.7.11.2.5.7 Format 11: PaintColrGlyph**

Format 7 is used to allow a color glyph definition from the BaseGlyphV1List to
be a re-usable component that can be incorporated into multiple color glyph
definitions. See 5.7.11.1.7.3 for more information.

*PaintColrGlyph table (format 11):*

| Type | Name | Description |
|-|-|-|
| uint8 | format | Set to 11. |
| uint16 | glyphID | Virtual glyph ID for a BaseGlyphV1List base glyph. |

The glyphID value shall be a glyphID found in a BaseGlyphV1Record within the
BaseGlyphV1List. It may be a *virtual* glyph ID, greater than or equal to the
numGlyph value in the &#39;maxp&#39; table (5.2.6). The BaseGlyphV1Record
provides an offset to a paint table; that paint table and the graph linked from
it are incorporated as a child sub-graph of the PaintColrGlyph table within the
current color glyph definition.

**5.7.11.2.5.8 Formats 12 and 13: PaintTransform, PaintVarTransform**

Formats 12 and 13 are used to apply an affine transformation to a sub-graph. The
paint table that is the root of the sub-graph is linked as a child.

Format 13 allows for variation of the transformation in a variable font; format
12 provides a more compact representation when variation is not required. Format
13 shall not be used in non-variable fonts or if the COLR table does not have an
ItemVariationStore subtable.

For general information regarding transformations in a color glyph definition,
see 5.7.11.1.5.

*PaintTransform table (format 12):*

| Type | Name | Description |
|-|-|-|
| uint8 | format | Set to 12. |
| Offset24 | paintOffset | Offset to a Paint subtable. |
| Affine2x3 | transform | An Affine2x3 record (inline). |

*PaintVarTransform table (format 13):*

| Type | Name | Description |
|-|-|-|
| uint8 | format | Set to 13. |
| Offset24 | paintOffset | Offset to a Paint subtable. |
| VarAffine2x3 | transform | A VarAffine2x3 record (inline). |

The affine transformation is defined by a 2×3 matrix, specified in an Affine2x3
or VarAffine2x3 record. The 2×3 matrix supports scale, skew, reflection,
rotation, and translation transformations. The matrix elements in the
VarAffine2x3 record use VarFixed records, allowing the transform definition to
be variable in a variable font.

*Affine2x3 record:*

| Type | Name | Description |
|-|-|-|
| Fixed | xx | x-component of transformed x-basis vector |
| Fixed | yx | y-component of transformed x-basis vector |
| Fixed | xy | x-component of transformed y-basis vector |
| Fixed | yy | y-component of transformed y-basis vector |
| Fixed | dx | Translation in x direction. |
| Fixed | dy | Translation in y direction. |

*VarAffine2x3 record:*

| Type | Name | Description |
|-|-|-|
| VarFixed | xx | x-component of transformed x-basis vector |
| VarFixed | yx | y-component of transformed x-basis vector |
| VarFixed | xy | x-component of transformed y-basis vector |
| VarFixed | yy | y-component of transformed y-basis vector |
| VarFixed | dx | Translation in x direction. |
| VarFixed | dy | Translation in y direction. |

For a pre-transformation position *(x, y)*, the post-transformation position
*(x&#x2032;, y&#x2032;)* is calculated as follows:

*x&#x2032;* = *xx \* x + xy \* y + dx*  
*y&#x2032;* = *yx \* x + yy \* y + dy*

NOTE: It is helpful to understand linear transformations by their effect on *x-*
and *y-basis* vectors _î = (1, 0)_ and _ĵ = (0, 1)_. The transform described by
the Affine2x3 or VarAffine2x3 record maps the basis vectors to _î&#x2032; = (xx,
yx)_ and _ĵ&#x2032; = (xy, yy)_, and translates the origin to _(dx, dy)_.

When the transformed composition from the referenced paint table (and its
sub-graph) is composed into the destination (represented by the parent of this
table), the source design grid origin is aligned to the destination design grid
origin. The transform can translate the source such that a pre-transform
position (0,0) is moved elsewhere. The *post-transform* origin, (0,0), is
aligned to the destination origin.

**5.7.11.2.5.9 Formats 14 and 15: PaintTranslate, PaintVarTranslate**

Format 14 and 15 are used to apply a translation to a sub-graph. The paint table
that is the root of the sub-graph is linked as a child.

Format 15 allows for variation of the translation in a variable font; format 14
provides a more compact representation when variation is not required. Format 15
shall not be used in non-variable fonts or if the COLR table does not have an
ItemVariationStore subtable.

For general information regarding transformations in a color glyph definition, 
see 5.7.11.1.5.

*PaintTranslate table (format 14):*

| Type | Name | Description |
|-|-|-|
| uint8 | format | Set to 14. |
| Offset24 | paintOffset | Offset to a Paint subtable. |
| Fixed | dx | Translation in x direction. |
| Fixed | dy | Translation in y direction. |

*PaintVarTranslate table (format 15):*

| Type | Name | Description |
|-|-|-|
| uint8 | format | Set to 15. |
| Offset24 | paintOffset | Offset to a Paint subtable. |
| VarFixed | dx | Translation in x direction. |
| VarFixed | dy | Translation in y direction. |

NOTE: Pure translation can also be represented using the PaintTransform or
PaintVarTransform table by setting _xx_ = 1, _yy_ = 1, _xy_ and _yx_ = 0, and
setting _dx_ and _dy_ to the translation values. The PaintTranslate or
PaintVarTranslate table provides a more compact representation when only
translation is required.

The translation will result in the pre-transform position (0,0) being moved
elsewhere. See 5.7.11.2.5.8 regarding alignment of the transformed content with
the destination.

**5.7.11.2.5.10 Formats 16 and 17: PaintRotate, PaintVarRotate**

Formats 16 and 17 are used to apply a rotation to a sub-graph. The paint table
that is the root of the sub-graph is linked as a child. The amount of rotation
is expressed directly as an angle, and X and Y coordinates can be provided for
the center of rotation.

Format 17 allows for variation of the rotation in a variable font; format 16
provides a more compact representation when variation is not required. Format 17
shall not be used in non-variable fonts or if the COLR table does not have an
ItemVariationStore subtable.

For general information regarding transformations in a color glyph definition, 
see 5.7.11.1.5.

*PaintRotate table (format 16):*

| Type | Name | Description |
|-|-|-|
| uint8 | format | Set to 16. |
| Offset24 | paintOffset | Offset to a Paint subtable. |
| Fixed | angle | Rotation angle, in counter-clockwise degrees. |
| Fixed | centerX | x coordinate for the center of rotation. |
| Fixed | centerY | y coordinate for the center of rotation. |

*PaintVarRotate table (format 17):*

| Type | Name | Description |
|-|-|-|
| uint8 | format | Set to 17. |
| Offset24 | paintOffset | Offset to a Paint subtable. |
| VarFixed | angle | Rotation angle, in counter-clockwise degrees. |
| VarFixed | centerX | x coordinate for the center of rotation. |
| VarFixed | centerY | y coordinate for the center of rotation. |

NOTE: Pure rotation about a point can also be represented using the
PaintTransform or PaintVarTransform table. For rotation about the origin, this
could be done by setting matrix values as follows for angle &theta;:

* _xx_ = cos(&theta;)
* _yx_ = sin(&theta;)
* _xy_ = -sin(&theta;)
* _yy_ = cos(&theta;)
* _dx_ = _dy_ = 0

The important difference of the PaintRotate and PaintVarRotate tables is in
allowing an angle to be specified directly in degrees, rather than as changes to
basis vectors. In variable fonts, if a rotation angle needs to vary, it is
easier to get smooth variation if an angle is specified directly than when using
trigonometric functions to derive matrix elements.

When combining the transform effect of a PaintRotate or PaintVarRotate table
with other transforms, the result shall be the same as if the rotation were
represented using an equivalent matrix.

A rotation can result in the pre-transform position (0, 0) being moved
elsewhere. See 5.7.11.2.5.8 regarding alignment of the transformed content with
the destination.

**5.7.11.2.5.11 Formats 18 and 19: PaintSkew, PaintVarSkew**

Formats 18 and 19 are used to apply a skew to a sub-graph. The paint table that
is the root of the sub-graph is linked as a child. The amount of skew in the X
or Y direction is expressed directly as angles, and X and Y coordinates can be
provided for the center of rotation.

Format 19 allows for variation of the rotation in a variable font; format 18
provides a more compact representation when variation is not required. Format 19
shall not be used in non-variable fonts or if the COLR table does not have an
ItemVariationStore subtable.

For general information regarding transformations in a color glyph definition, 
see 5.7.11.1.5.

*PaintSkew table (format 18):*

| Type | Name | Description |
|-|-|-|
| uint8 | format | Set to 18. |
| Offset24 | paintOffset | Offset to a Paint subtable. |
| Fixed | xSkewAngle | Angle of skew in the direction of the x-axis, in counter-clockwise degrees. |
| Fixed | ySkewAngle | Angle of skew in the direction of the y-axis, in counter-clockwise degrees. |
| Fixed | centerX | x coordinate for the center of rotation. |
| Fixed | centerY | y coordinate for the center of rotation. |

*PaintVarSkew table (format 19):*

| Type | Name | Description |
|-|-|-|
| uint8 | format | Set to 19. |
| Offset24 | paintOffset | Offset to a Paint subtable. |
| VarFixed | xSkewAngle | Angle of skew in the direction of the x-axis, in counter-clockwise degrees. |
| VarFixed | ySkewAngle | Angle of skew in the direction of the y-axis, in counter-clockwise degrees. |
| VarFixed | centerX | x coordinate for the center of rotation. |
| VarFixed | centerY | y coordinate for the center of rotation. |

NOTE: Pure skews about a point can also be represented using the PaintTransform
or PaintVarTransform table. For skews about the origin, this could be done by
setting matrix values as follows for _x_ skew angle &phi; and _y_ skew angle
&psi;:

* _xx_ = _yy_ = 1
* _yx_ = tan(&psi;)
* _xy_ = -tan(&phi;)
* _dx_ = _dy_ = 0

The important difference of the PaintSkew and PaintVarSkew tables is in being
able to specify skew as an angle, rather than as changes to basis vectors. In
variable fonts, if a skew angle needs to vary, it is easier to get smooth
variation if an angle is specified directly than when using trigonometric
functions to derive matrix elements.

When combining the transform effect of a PaintSkew or PaintVarSkew table with
other transforms, the result shall be the same as if the skew were represented
using an equivalent matrix.

A skew can result in the pre-transform position (0, 0) being moved elsewhere.
See 5.7.11.2.5.8 regarding alignment of the transformed content with the
destination.

**5.7.11.2.5.12 Format 20: PaintComposite**

Format 20 is used to combine two layered compositions, referred to as *source*
and *backdrop*, using different compositing or blending modes. The available
compositing and blending modes are defined in an enumeration. For general 
information and examples, see 5.7.11.1.6.

NOTE: The backdrop is also referred to as the “destination”.

*PaintComposite table (format 20):*

| Type | Name | Description |
|-|-|-|
| uint8 | format | Set to 20. |
| Offset24 | sourcePaintOffset | Offset to a source Paint table. |
| uint8 | compositeMode | A CompositeMode enumeration value. |
| Offset24 | backdropPaintOffset | Offset to a backdrop Paint table. |

The compositionMode value shall be one of the values defined in the CompositeMode
enumeration, which are taken from the W3C [Compositing and Blending Level 1][1]
(C&B Level 1) specification. If an unrecognized value is encountered,
COMPOSITE_CLEAR shall be used.

*CompositeMode enumeration:*

| Value | Name | Description |
|-|-|-|
| | *Porter-Duff modes* | |
| 0 | COMPOSITE_CLEAR | See [Clear][2] in C&B Level 1 |
| 1 | COMPOSITE_SRC | See [Copy][3] in C&B Level 1 |
| 2 | COMPOSITE_DEST | See [Destination][4] in C&B Level 1 |
| 3 | COMPOSITE_SRC_OVER | See [Source Over][5] in C&B Level 1 |
| 4 | COMPOSITE_DEST_OVER | See [Destination Over][6] in C&B Level 1 |
| 5 | COMPOSITE_SRC_IN | See [Source In][7] in C&B Level 1 |
| 6 | COMPOSITE_DEST_IN | See [Destination In][8] in C&B Level 1 |
| 7 | COMPOSITE_SRC_OUT | See [Source Out][9] in C&B Level 1 |
| 8 | COMPOSITE_DEST_OUT | See [Destination Out][10] in C&B Level 1 |
| 9 | COMPOSITE_SRC_ATOP | See [Source Atop][11] in C&B Level 1 |
| 10 | COMPOSITE_DEST_ATOP | See [Destination Atop][12] in C&B Level 1 |
| 11 | COMPOSITE_XOR | See [XOR][13] in C&B Level 1 |
| 12 | COMPOSITE_PLUS | See [PLUS][14] in C&B Level 1 |
| | *Separable color blend modes:* | |
| 13 | COMPOSITE_SCREEN | See [screen blend mode][15] in C&B Level 1 |
| 14 | COMPOSITE_OVERLAY | See [overlay blend mode][16] in C&B Level 1 |
| 15 | COMPOSITE_DARKEN | See [darken blend mode][17] in C&B Level 1 |
| 16 | COMPOSITE_LIGHTEN | See [lighten blend mode][18] in C&B Level 1 |
| 17 | COMPOSITE_COLOR_DODGE | See [color-dodge blend mode][19] in C&B Level 1 |
| 18 | COMPOSITE_COLOR_BURN | See [color-burn blend mode][20] in C&B Level 1 |
| 19 | COMPOSITE_HARD_LIGHT | See [hard-light blend mode][21] in C&B Level 1 |
| 20 | COMPOSITE_SOFT_LIGHT | See [soft-light blend mode][22] in C&B Level 1 |
| 21 | COMPOSITE_DIFFERENCE | See [difference blend mode][23] in C&B Level 1 |
| 22 | COMPOSITE_EXCLUSION | See [exclusion blend mode][24] in C&B Level 1 |
| 23 | COMPOSITE_MULTIPLY | See [multiply blend mode][25] in C&B Level 1 |
| | *Non-separable color blend modes:* | |
| 24 | COMPOSITE_HSL_HUE | See [hue blend mode][26] in C&B Level 1 |
| 25 | COMPOSITE_HSL_SATURATION | See [saturation blend mode][27] in C&B Level 1 |
| 26 | COMPOSITE_HSL_COLOR | See [color blend mode][28] in C&B Level 1 |
| 27 | COMPOSITE_HSL_LUMINOSITY | See [luminosity blend mode][29] in C&B Level 1 |

The graphic compositions are defined by the source and backdrop paint tables
and their respective sub-graphs. Conceptually, they are rendered into bitmaps,
and the source is composited or blended into the backdrop using the specified
composite mode. Details on each mode, including specifications of the required
calculations using pixel color and alpha values, are provided in the
Compositing and Blending Level 1 specification.

While color values obtained from the CPAL table are represented in sRGB using
the non-linear transfer function defined in the sRGB specification, the
compositing and blending calculations are done after applying the inverse
transfer function to derive linear-light RGB values. For more information
regarding the non-linear and linear-light representations for sRGB, see
_Interpolation of Colors_ in 5.7.12.

As mentioned in 5.7.11.1.8.2, a color glyph definition shall be bounded. A
sub-graph that has PaintComposite as its root is either bounded or unbounded,
depending on the mode used and the boundedness of the source and backdrop
sub-graphs. For each mode, boundedness is determined by the boundedness of the
source and backdrop as follows:

* Always bounded:
  * COMPOSITE_CLEAR
* Bounded *if and only if* the source is bounded:
  * COMPOSITE_SRC
  * COMPOSITE_SRC_OUT
* Bounded *if and only if* the backdrop is bounded:
  * COMPOSITE_DEST
  * COMPOSITE_DEST_OUT
* Bounded *if and only if* either the source *or* backdrop is bounded:
  * COMPOSITE_SRC_IN
  * COMPOSITE_DEST_IN
* Bounded *if and only if* both the source *and* backdrop are bounded:
  * All other modes

**5.7.11.3 COLR version 1 rendering algorithm**

The various graphic concepts represented by COLR version 1 formats were
individually described in 5.7.11.1, and the various formats were described in
5.7.11.2. Together, these provide most of the necessary details regarding how a
color glyph is rendered. The following provides a comprehensive description of
the rendering process, considering the graph as a whole.

The following algorithm can be used to render color glyphs defined using version
1 formats. Applications are not required to implement rendering using this
algorithm, but shall produce equivalent results.

NOTE: Checks for well-formedness and validity, as described in 5.7.11.1.9, are
not repeated here. Actual implementations can integrate such checks with
rendering processing.

1. Start with an initial drawing surface. As mentioned in 5.7.11.1.8.2, the
bounding box of the base glyph can be used to determine the size.
1. Traverse the graph of a color glyph definition, starting with the root paint
table referenced by a BaseGlyphV1Record, using the following pseudo-code
function.

```
// render a paint table and its sub-graph
function renderPaint(paint)

    if format 1: // PaintColrLayers
        for each referenced child paint table, in bottom-up z-order:
            // for ordering, see 5.7.11.1.4, 5.7.11.2.5.1
            call renderPaint() passing the child paint table

            compose the returned graphic onto the surface using simple
            alpha blending

    if format 2 or 3: // PaintSolid, PaintVarSolid
        paint the specified color onto the surface

    if format 4, 5, 6, 7, 8 or 9:
        // PaintLinearGradient, PaintVarLinearGradient
        // PaintRadialGradient, PaintVarRadialGradient
        // PaintSweepGradient, PaintVarSweepGradient
        paint the gradient onto the surface following the gradient
        algorithm

    if format 10: // PaintGlyph
        apply the outline of the referenced glyph to the clip region
            // take the intersection of clip regions—see 5.7.11.1.3

        call renderPaint() passing the child paint table

        restore the previous clip region

    if format 11: // PaintColrGlyph
        call renderPaint() passing the paint table referenced by the base
        glyph ID

    if format 12, 13, 14, 15, 16, 17, 18 or 19:
        // PaintTransform, PaintVarTransform
        // PaintTranslate, PaintVarTranslate
        // PaintRotate, PaintVarRotate
        // PaintSkew, PaintVarSkew
        apply the specified transform
            // compose the transform with the current transform state—see
            // 5.7.11.1.5

        call renderPaint() passing the child paint table

        restore the previous transform state

    if format 20: // PaintComposite

        // render backdrop sub-graph
        call renderPaint() passing the backdrop child paint table and save
        the result

        // render source sub-graph
        call renderPaint() passing the source child paint table and save
        the result

        // compose source and backdrop
        compose the source and backdrop using the specified composite mode

        // compose final result
        compose the result of the above composition onto the surface using
        simple alpha blending
```

**5.7.11.4 COLR table and OFF Font Variations**

The COLR table can be used in variable fonts. For color glyphs defined using
version 0 formats, the glyph outlines can be variable, but no other aspect of
the color glyph is variable. For color glyphs defined using version 1 formats,
items that can be variable include the glyph outlines plus other aspects of the
color glyph definition:

* Alpha values
* Color stop offsets in gradient color lines
* Placement of gradients onto the design grid
* The arguments of transformations (matrix elements, angles, etc.)

Variation data is provided in an Item Variation Store table (7.2.3) contained
within the COLR table.

Each value within the COLR version 1 formats that can be variable is represented
using a record that combines a field for the default value together with fields
for a delta-set index. The delta-set index is used to reference the variation
data within the Item Variation Store. The record formats used include:

* VarFWord
* VarUFWord 
* VarF2Dot14
* VarFixed

These are described in 7.2.3.1. They all follow a simple pattern: For a field
type *SomeType* (hypothetical), the record format is as follows:</ins>

| <del class="del">23</del> <ins class="ins">Type</ins> | <del class="del">COMPOSITE_HSL_HUE</del> <ins class="ins">Name</ins> | <del class="del">See [hue blend mode][25]</del> <ins class="ins">Description</ins> |
<ins class="ins">|-|-|-|</ins>
| <del class="del">24</del> <ins class="ins">*SomeType*</ins> | <del class="del">COMPOSITE_HSL_SATURATION</del> <ins class="ins">value</ins> | <del class="del">See [saturation blend mode][26]</del> |
| <del class="del">25</del> <ins class="ins">uint16</ins> | <del class="del">COMPOSITE_HSL_COLOR</del> <ins class="ins">varOuterIndex</ins> | <del class="del">See [color blend mode][27]</del> |
| <del class="del">26</del> <ins class="ins">uint16</ins> | <del class="del">COMPOSITE_HSL_LUMINOSITY</del> <ins class="ins">varInnerIndex</ins> | <del class="del">See [luminosity blend mode][28]</del> |

The <del class="del">supported modes</del> <ins class="ins">value field of these records provides the default value for a given item.
The remaining fields provide index values for a particular ItemVariationData
subtable and DeltaSet record—the two-level organizational hierarchy used within
the Item Variation Store.

If the COLR table does not contain an Item Variation Store subtable, the index
fields of these records shall be ignored by applications, and should be set to
zero. The value field is read directly without any variation calculation.

If the COLR table contains an Item Variation Store subtable, the index fields
shall be used to obtain a delta value that is combined with the value of the
value field. In this case, the index fields of the VarFWord, VarUFWord,
VarF2Dot14 and VarFixed records shall always be set with specific values. The
indices</ins> are <del class="del">taken from</del> <ins class="ins">base 0, therefore 0x0000 cannot be used as an ignorable default. To
indicate that an item has no variation data,</ins> the <del class="del">W3C [Compositing and Blending Level 1][1]
specification.</del> <ins class="ins">index fields shall be set to
0xFFFF/0xFFFF. (See 7.2.3.2.)</ins>

For <del class="del">details on each mode, including specifications</del> <ins class="ins">variable fonts that use COLR version 1 formats, special considerations apply
to the effect</ins> of <ins class="ins">variation on</ins> the
<del class="del">required behaviors,</del> <ins class="ins">bounding box. See 5.7.11.1.8.2 for details.

For general information on OFF font variations,</ins> see <ins class="ins">7.1.

## Changes to OFF 5.7.12 - Palette Table

_After</ins> the <del class="del">W3C specification.

The graphic compositions defined by the source and backdrop paint tables (and
their respective sub-graphs) are rendered into bitmaps, and</del> <ins class="ins">first paragraph, insert</ins> the <del class="del">source</del> <ins class="ins">following paragraph. (The referenced IEC
standard</ins> is
<del class="del">blended into the backdrop using the specified composite mode.

As mentioned</del> <ins class="ins">already included</ins> in <del class="del">5.7.11.1.8.2,</del> <ins class="ins">the normative references.)_

Palettes are defined by</ins> a <ins class="ins">set of</ins> color <del class="del">glyph definition shall be bounded. A
sub-graph that has PaintComposite as its root</del> <ins class="ins">records. Each color record specifies a
color in the sRGB color space using 8-bit BGRA (blue, green, red, alpha)
representation. The sRGB color space</ins> is <del class="del">either bounded or unbounded,
depending</del> <ins class="ins">specified in IEC 61966-2-1. Details</ins> on
the <del class="del">mode used and</del> <ins class="ins">specification for</ins> the <del class="del">boundedness of</del> <ins class="ins">sRGB color space, including</ins> the <del class="del">source</del> <ins class="ins">color primaries</ins> and <del class="del">backdrop
sub-graphs. For each mode, boundedness is determined by</del>
<ins class="ins">“gamma” transfer function, are also provided in [CSS Color Module Level 4,
section 10.2](https://www.w3.org/TR/css-color-4/#predefined).

_Insert</ins> the <del class="del">boundedness</del> <ins class="ins">following paragraphs at the end</ins> of <ins class="ins">5.7.12 with</ins> the
<del class="del">source and backdrop</del> <ins class="ins">heading,
“Interpolation of colours”. (This heading should have the same heading level</ins> as <del class="del">follows:

* Always bounded:
  * COMPOSITE_CLEAR
* Bounded *if</del>
<ins class="ins">the earlier heading, “Palette Entries</ins> and <del class="del">only if*</del> <ins class="ins">Color Records”.):_

**Interpolation of Colors**

The SVG table and version 1 of</ins> the <del class="del">source</del> <ins class="ins">COLR table both support color gradient fills.
The gradients are defined using color stops to specify color values at specific
positions along a color line, with color values for other positions on the color
line derived by interpolation.

When interpolating color values, linear interpolation between color stop
positions</ins> is <del class="del">bounded:
  * COMPOSITE_SRC
  * COMPOSITE_SRC_OUT
* Bounded *if</del> <ins class="ins">used. For example, suppose adjacent color stops are specified for
positions 0.5</ins> and <del class="del">only if* the backdrop is bounded:
  * COMPOSITE_DEST
  * COMPOSITE_DEST_OUT
* Bounded *if</del> <ins class="ins">0.9 on a color line,</ins> and <del class="del">only if* either the source *or* backdrop</del> <ins class="ins">a color value</ins> is <del class="del">bounded:
  * COMPOSITE_SRC_IN
  * COMPOSITE_DEST_IN
* Bounded *if and only if* both the source *and* backdrop are bounded:
  * All other modes

**5.7.11.3 COLR version 1 rendering algorithm**</del> <ins class="ins">being calculated for
position 0.8.</ins> The <del class="del">various graphic concepts represented by COLR version 1 formats were individually described in 5.7.11.1,</del> <ins class="ins">color value of the first color stop will contribute 75% of the
value ((0.8 - 0.5) / (0.9 - 0.5)),</ins> and the <del class="del">various formats were described in 5.7.11.2. Together, these provide most</del> <ins class="ins">color value</ins> of the <del class="del">necessary details regarding how a</del> <ins class="ins">second</ins> color <del class="del">glyph is rendered. The following provides a comprehensive description</del> <ins class="ins">stop
will contribute 25%</ins> of the <del class="del">rendering process, considering</del> <ins class="ins">value. Interpolated values at each position of</ins> the <del class="del">graph as a whole. 

The following algorithm can be used to render</del>
color <del class="del">glyphs defined using version 1 formats. Applications</del> <ins class="ins">line</ins> are <del class="del">not required to implement rendering using</del> <ins class="ins">computed in</ins> this <del class="del">algorithm, but shall produce equivalent results.

NOTE: Checks</del> <ins class="ins">way</ins> for <del class="del">well-formedness</del> <ins class="ins">each of the R, G</ins> and <del class="del">validity,</del> <ins class="ins">B color components.

When interpolating color values, specific aspects of the representation of
colors</ins> as <del class="del">described in 5.7.11.1.9,</del> <ins class="ins">well as handling of alpha need to be considered.

Representations of sRGB color values</ins> are <del class="del">not repeated here. Actual implementations can integrate such checks with rendering processing.

1. Start</del> <ins class="ins">expressed as levels of red, green and
blue color “primaries”</ins> with <del class="del">an initial drawing surface. As mentioned</del> <ins class="ins">specific, absolute chromaticity values, which are
defined</ins> in <del class="del">5.7.11.1.8.2, the bounding box of</del> the <del class="del">base glyph</del> <ins class="ins">sRGB specification. Color-primary levels</ins> can <ins class="ins">potentially</ins> be <del class="del">used</del>
<ins class="ins">expressed using a linear-light scale that correlates directly</ins> to <del class="del">determine the size.
1. Traverse the graph</del> <ins class="ins">light energy.
(On a linear-light scale, for example, a doubling</ins> of a color <del class="del">glyph definition, starting with the root paint table referenced by</del> <ins class="ins">value would
correspond to</ins> a <del class="del">BaseGlyphV1Record,</del> <ins class="ins">doubling of display luminance.) For sRGB, however, standard
practice is to represent levels</ins> using <del class="del">the following pseudo-code function.
 
```
// render</del> a <del class="del">paint table and its sub-graph</del> <ins class="ins">scale defined by a non-linear transfer
function, sometimes referred to as “gamma”. This transfer</ins> function <del class="del">renderPaint(paint)

    if format 1: // PaintColrLayers
        for each referenced child paint table,</del> <ins class="ins">is also
defined</ins> in <del class="del">bottom-up z-order:
            // for ordering, see 5.7.11.1.4, 5.7.11.2.5.1
            call renderPaint() passing the child paint table
            compose the returned graphic onto</del> the <del class="del">surface using simple alpha blending

    if format 2: // PaintSolid
        paint</del> <ins class="ins">sRGB specification. (See [CSS Color Module Level 4, section
10.2](https://www.w3.org/TR/css-color-4/#predefined) for details.) In</ins> the <del class="del">specified</del> <ins class="ins">CPAL
table, sRGB</ins> color <del class="del">onto the surface

    if format 3: // PaintLinearGradient
    or if format 4: // PaintRadialGradient
        paint the gradient onto the surface following the gradient algorithm

    if format 5: // PaintGlyph
        apply the outline</del> <ins class="ins">values are always specified in terms</ins> of the <del class="del">referenced glyph to the clip region
            // take the intersection</del> <ins class="ins">non-linear, sRGB
transfer function. 

NOTE: An advantage</ins> of <del class="del">clip regions—see 5.7.11.1.3
        call renderPaint() passing the child paint table
        restore</del> <ins class="ins">representing colours using a non-linear scale is that it
allows more effective use of limited bit depth when color-primary levels are
represented as integers: smaller differences in light energy can be represented
for lower levels than for higher levels. This is beneficial since</ins> the <del class="del">previous clip region</del> <ins class="ins">human
visual system is more sensitive to differences at low luminance levels than to
differences at high luminance levels.

When interpolating colors, different results will be obtained</ins> if <del class="del">format 6: // PaintColrGlyph
        call renderPaint() passing the paint table referenced by</del> the <del class="del">base glyph ID

    if format 7: // PaintTransformed
    or if format 8: // PaintTranslate
    or if format 9: // PaintRotate
    or if format 10: // PaintSkew
        apply</del>
<ins class="ins">interpolation is computed using</ins> the <del class="del">specified transform
            // compose</del> <ins class="ins">non-linear scale for color levels than if
using</ins> the <del class="del">transform with</del> <ins class="ins">linear-light scale. For interoperable results, whether</ins> the <del class="del">current transform state—see 5.7.11.1.5
        call renderPaint() passing</del> <ins class="ins">non-linear
or linear-light scale is to be used needs to be specified.

For gradient color values in</ins> the <del class="del">child paint table
        restore</del> <ins class="ins">SVG table,</ins> the <del class="del">previous transform state

    if format 11: // PaintComposite

        // render backdrop sub-graph
        call renderPaint() passing</del> <ins class="ins">required interpolation behavior
is defined in</ins> the <del class="del">backdrop child paint table and save</del> <ins class="ins">SVG 1.1 specification:</ins> the <del class="del">result

        // render source sub-graph
        call renderPaint() passing</del> <ins class="ins">[‘color-interpolation’
property](https://www.w3.org/TR/SVG11/painting.html#ColorInterpolationProperty)
can be used in an SVG document to declare whether interpolation is done using</ins>
the <del class="del">source child paint table and save</del> <ins class="ins">non-linear sRGB scale (the default), or using a linear-light scale by
applying</ins> the <del class="del">result

        // compose source and backdrop
        compose</del> <ins class="ins">inverse sRGB transfer function.

For gradient color values in</ins> the <del class="del">source and backdrop</del> <ins class="ins">COLR table, interpolation shall be computed</ins>
using <ins class="ins">linear-light values (i.e., after applying</ins> the <del class="del">specified composite mode

        // compose final result
        compose</del> <ins class="ins">inverse sRGB transfer
function).

After an interpolated color value is computed, whether or not</ins> the <del class="del">result</del> <ins class="ins">non-linear
sRGB transfer function needs to be re-applied is determined by the requirements</ins>
of the <del class="del">above composition onto</del> <ins class="ins">implementation context.

For both</ins> the <del class="del">surface using simple alpha blending
```

**5.7.11.4</del> COLR <del class="del">table</del> and <del class="del">OFF Font Variations**

The COLR table can</del> <ins class="ins">SVG tables, interpolation shall</ins> be <del class="del">used</del> <ins class="ins">done with alpha
pre-multiplied into each linearized R, G and B component. For alpha specified</ins> in <del class="del">variable fonts.</del>
<ins class="ins">a CPAL ColorRecord, the value is converted to a floating value in the range [0,
1.0] by dividing by 255, then multiplied into each R, G and B component.</ins> For <del class="del">color glyphs defined using
version 0 formats,</del>
<ins class="ins">ColorIndex records in</ins> the <del class="del">glyph outlines can be variable, but no other aspect of</del> <ins class="ins">COLR table,</ins> the <del class="del">color glyph</del> <ins class="ins">alpha value from the ColorIndex record
(with variation, in a variable font)</ins> is <del class="del">variable. For color glyphs defined</del> <ins class="ins">multiplied into the R, G and B
components as well. Interpolated values are then calculated by linear
interpolation</ins> using <del class="del">version 1 formats,
items that</del> <ins class="ins">these pre-multiplied, linear-light R, G and B values.

NOTE: Alpha components use a linear scale and</ins> can be <del class="del">variable include</del> <ins class="ins">directly interpolated apart
from</ins> the <del class="del">glyph outlines plus other aspects</del> <ins class="ins">R, G and B components without any linearlization step.

Once interpolation</ins> of the
<del class="del">color glyph definition:

* Alpha</del> <ins class="ins">pre-multiplied red, green and blue</ins> values
<del class="del">* Color stop offsets in gradient color lines
* Placement</del> <ins class="ins">and</ins> of <del class="del">gradients onto</del> the <del class="del">design grid
* The arguments of transformations (matrix elements, angles, etc.)

Variation data</del>
<ins class="ins">alpha value</ins> is <del class="del">provided in an Item Variation Store table (7.2.3) contained
within</del> <ins class="ins">complete,</ins> the <del class="del">COLR table.

Each</del> <ins class="ins">red, green and blue results are then
un-premultiplied by dividing each interpolated</ins> value <del class="del">within</del> <ins class="ins">by</ins> the <del class="del">COLR version 1 formats that can</del> <ins class="ins">corresponding
interpolated alpha.

While color values are specified as 8-bit integers, the interpolation
computations will require greater precision in each of the linearization,
pre-multiply, and interpolation steps. Also, when rendered results are to</ins> be <del class="del">variable is represented
using a record that combines</del>
<ins class="ins">presented on</ins> a <del class="del">field for the default value together</del> <ins class="ins">imaging device</ins> with <del class="del">fields
for</del> <ins class="ins">known characteristics, visual banding
artifacts in</ins> a <del class="del">delta-set index. The delta set index is used to reference</del> <ins class="ins">gradient can be minimized by taking full advantage of</ins> the <del class="del">variation
data within</del> <ins class="ins">color
bit depth supported by</ins> the <del class="del">Item Variation Store. The record formats used include:

* VarFWord
* VarUFWord 
* VarF2Dot14
* VarFixed

These are described in 7.2.3.1. They all follow a simple pattern:</del> <ins class="ins">device.</ins> For <ins class="ins">instance, if</ins> a <del class="del">field
type *SomeType* (hypothetical),</del> <ins class="ins">display supports 10- or
12-bit quantization per color channel, then ideally</ins> the <del class="del">record format is as follows:

| Type | Name | Description |
|-|-|-|
| *SomeType* | value | |
| uint16 | varOuterIndex | |
| uint16 | varInnerIndex | |

The value field</del> <ins class="ins">ramp</ins> of <del class="del">this record provides</del> <ins class="ins">color values in
a gradient would use that level of quantization. Other factors from</ins> the <del class="del">default value</del>
<ins class="ins">presentation context may, however, also affect the available capabilities.
Therefore, no minimum level of precision is specified as a requirement.

## Changes to OFF 7.2.1 Overview (Font variations common table formats)

_In the list</ins> for the <del class="del">stop offset.
The remaining fields provide index values</del> <ins class="ins">fifth paragraph ("A variable font includes..."), delete the
sixth list item, "Deltas</ins> for <del class="del">a particular ItemVariationData
subtable and DeltaSet record—the two-level organizational hierarchy used within</del> <ins class="ins">baseline metrics..."_

_At</ins> the <del class="del">Item Variation Store.

The index fields</del> <ins class="ins">end</ins> of the <del class="del">VarFWord, VarUFWord, VarF2Dot14 and VarFixed records
shall always be set with specific values. The indices</del> <ins class="ins">list for the fifth paragraph, add the following list item:_

— Deltas for values in other tables</ins> are <del class="del">base 0, therefore
0x0000 cannot be used as an ignorable default. To indicate that an item has no
variation data,</del> <ins class="ins">stored in</ins> the <del class="del">index fields shall be set to 0xFFFF/0xFFFF. (See 7.2.3.2.)

For general information on OFF font variations, see 7.1.</del> <ins class="ins">respective table: deltas
for baseline metrics in the 'BASE' table and for various items in the 'COLR'
table are stored in each table.</ins>

## Changes to OFF 7.2.3 Item variation stores

_Delete the fourth paragraph, "Variation data is comprised..."._

_Add a new sub-clause 7.2.3.1 after the third paragraph ("The item variation
store formats..."), with text as follows:_

**7.2.3.1 Associating target items to variation data**

Variation data is comprised of delta adjustment values that apply to particular
target items. Some mechanism is needed to associate delta values with target
items. In the item variation store, a block of delta values has an implicit
delta-set index, and separate data outside the item variation store is provided
that indicates the delta-set index associated with a given target item.
Depending on the parent table in which an item variation store is used,
different means are used to provide these associations:

* In the MVAR table, an array of records identifies target data items in various
other tables, along with the delta-set index for each respective item.
* In the HVAR and VVAR tables, the target data items are glyph metric arrays in
the &#39;hmtx&#39; and &#39;vmtx&#39; tables. Subtables in the HVAR and VVAR
tables provide the mapping between the target data items and delta-set indices.
* For the BASE, GDEF, GPOS, and JSTF tables, a target data item is associated
with a delta-set index using a related VariationIndex table <del class="del">(see 6.2.8)</del> <ins class="ins">(6.2.8)</ins> within the
same subtable that contains the target item.
* In the COLR table, target data items are specified in structures that combine
a basic data type, such FWORD, with a delta-set index.

The structures used in the COLR table currently are used only in that table but
may be used in other tables in future versions, and so are defined here as
common formats. Structures are defined to wrap the FWORD, UFWORD, F2DOT14 and
Fixed basic types.

Note: as described below, each delta-set index is represented as two index
components, an *outer* index and an *inner* index, corresponding to a two-level
organizational hierarchy. This is described in detail below.

#### VarFWord

The FWORD type is used to represent coordinates in the glyph design grid. The
VarFWord record is used to represent a coordinate that can be variable.

| Type | Name | Description |
|-|-|-|
| FWORD | coordinate | |
| uint16 | varOuterIndex | |
| uint16 | varInnerIndex | |

#### VarUFWord

The <del class="del">UFWord</del> <ins class="ins">UFWORD</ins> type is used to represent distances in the glyph design grid. The
VarUFWord record is used to represent a distance that can be variable.

| Type | Name | Description |
|-|-|-|
| UFWORD | distance | |
| uint16 | varOuterIndex | |
| uint16 | varInnerIndex | |

#### VarF2Dot14

The F2DOT14 type is typically used to represent values that are inherently
limited to a range of <del class="del">1, 1], or a range of [0, 1]. The VarF2Dot14 record is
used to represent such a value that can be variable.

| Type | Name | Description |
|-|-|-|
| <del class="del">F2Dot14</del> <ins class="ins">F2DOT14</ins> | value | |
| uint16 | varOuterIndex | |
| uint16 | varInnerIndex | |

In general, variation deltas are (logically) signed 16-bit integers, and in most
cases, they are applied to signed 16-bit values (FWORDs) or unsigned 16-bit
values (UFWORDs). When scaled deltas are applied to F2DOT14 values, the F2DOT14
value is treated like a 16-bit integer. (In this sense, the delta and the
F2DOT14 value can be viewed as integer values in units of 1/16384ths.)

If the context in which the VarF2Dot14 is used constrains the valid range for the
default value, then any variations by applying deltas are clipped to that range.

#### VarFixed

The Fixed type is intended for floating values, such as variation-space
coordinates. The VarFixed record is used to represent such a value that can be
variable.

| Type | Name | Description |
|-|-|-|
| Fixed | value | |
| uint16 | varOuterIndex | |
| uint16 | varInnerIndex | |

While in most cases deltas are applied to 16-bit types, Fixed is a 32-bit
(16.16) type and requires 32-bit deltas. The DeltaSet record used in the
ItemVariationData subtable format can accommodate deltas that are, logically,
either 16-bit or 32-bit. See the description of the <del class="del">[ItemVariationData
subtable](#itemvariationdata-subtable), below,</del> <ins class="ins">ItemVariationData subtable
(7.2.3.4)</ins> for details.

When scaled deltas are applied to Fixed values, the Fixed value is treated like
a 32-bit integer. (In this sense, the delta and the Fixed value can be viewed as
integer values in units of 1/65536ths.)

_Insert a sub-clause heading, "7.2.3.2 Variation data", after the newly-inserted
text above, and before the paragraph beginning, "The ItemVariationStore table
includes a variation region list..." Re-number subsequent sub-clauses
accordingly._

_In the fifth paragraph that follows the figure in (now) 7.2.3.2, delete the
first sentence, "A complete delta set index... within that subtable." Before
that pragraph, insert the following paragraph:_

A complete delta-set index involves an outer-level index into the
ItemVariationData subtable array, plus an inner-level index to a delta-set row
within that subtable. A special meaning is assigned to a delta-set index
0xFFFF/0xFFFF (that is, outer-level and inner-level portions are both 0xFFFF):
this is used to indicate that there is no variation data for a given item.
Functionally, this would be equivalent to referencing delta-set data consisting
of only deltas of 0 for all regions.

_In 7.2.3.3 (previously, 7.2.3.1), "Variation regions", in the table for the
VariationRegionList structure, add the following sentence to the end of the
description for the regionCount field._

Shall be less than 32,736.

_After the table for the VariationRegionList structure, add the following
paragraph:_

The high-order bit of the regionCount field is reserved for future use, and
shall be cleared.

_In 7.2.3.4 (previously 7.2.3.2), "Item variation store and item variation data
tables", in the paragraph that follows the table for the ItemVariationStore
structure, delete the first sentence, "The item variation store includes an
array of offsets to item variation data subtables. Before that paragraph, insert
the following paragraph and note:_

The item variation store includes an offset to a variation region list and an
array of offsets to item variation data subtables. <ins class="ins">A NULL offset in the array
indicates that there is no item variation data subtable for that index into the
array.</ins>

NOTE: Indices into the itemVariationDataOffsets array are stored in parent
tables as delta-set “outer” indices with each such index having a corresponding
“inner” index. If the outer index points to a NULL offset, then any inner index
will be <del class="del">invalid. The itemVariationDataOffsets array should</del> <ins class="ins">invalid and can be ignored: items associated with this index do</ins> not <del class="del">include</del>
<ins class="ins">have</ins> any <del class="del">NULL
offsets.</del> <ins class="ins">variation.</ins>

_In 7.2.3.4, in the table for the ItemVariationData subtable structure, replace
the field name "shortDeltaCount" with "wordDeltaCount", and replace the
description of that field with the following:"_

A packed field: the high bit is a flag—see details below.

_Following the table for the ItemVariationData subtable structure, replace the
remainder of 7.2.3.4 (including the table for the DeltaSet record structure)
with the following:_

The wordDeltaCount field contains a packed value that includes a flag and a
“word” delta count. The format of this value is as follows:

| Mask | Name | Description |
|-|-|-|
| 0x8000 | LONG_WORDS | Flag indicating that “word” deltas are long (int32) |
| 0x7FFF | WORD_DELTA_COUNT_MASK | Count of “word” deltas |

The representation of delta values uses a mix of long types (“words”) and short
types. If the LONG_WORDS flag is set, deltas are represented using a mix of
int32 and int16 values. This representation is only used for deltas that are to
be applied to data items of Fixed or 32-bit integer types. If the flag is not
set, deltas are presented using a mix of int16 and int8 values. See the
description of the DeltaSet record below for additional details.

The count value indicated by WORD_DELTA_COUNT_MASK is a count of the number of
deltas that use the long (“word”) representation, and shall be less than or
equal to regionIndexCount.

The deltaSets array represents a logical two-dimensional table of delta values
with itemCount rows and regionIndexCount columns. Rows in the table provide sets
of deltas for particular target items, and columns correspond to regions of the
variation space. Each DeltaSet record in the array represents one row of the
delta-value table — one delta set.

*DeltaSet record:*

| Type | Name | Description |
|-|-|-|
| int16 and int8<br>*or*<br>int32 and int16 | deltaData&#x200B;[regionIndexCount] | Variation delta values. |

Logically, each DeltaSet record has regionIndexCount number of elements. The
elements are represented using long and short types, as described above. These
are either int16 and int8, or int32 and int16, according to whether the
LONG_WORDS flag <del class="del">was</del> <ins class="ins">is</ins> set. The delta array has a sequence of deltas using the long
type followed by <ins class="ins">a</ins> sequence of deltas using the short type. The count of deltas
using the long type is derived using WORD_DELTA_COUNT_MASK. The remaining
elements use the short type. The length of the data for each row, in bytes, is
regionIndexCount + (wordDeltaCount && WORD_DELTA_COUNT_MASK) if the LONG_WORDS
flag is not set, or 2 x that amount if the flag is set.

NOTE: Delta values are each represented directly. They are not packed as in the
tuple variation store.

## Changes to OFF Bibliography

_Add <del class="del">two</del> <ins class="ins">three</ins> new entries as follows:_

- HTML Living Standard, 4.12.5, The canvas element. https://html.spec.whatwg.org/multipage/canvas.html#the-canvas-element

- Compositing and Blending Level 1. W3C Candidate Recommendation, 13 January 2015. https://www.w3.org/TR/compositing-1/

<ins class="ins">- CSS Color Module Level 4. W3C Working Draft, 12 November 2020. https://www.w3.org/TR/css-color-4/</ins>


[1]: https://www.w3.org/TR/compositing-1/
[2]: https://www.w3.org/TR/compositing-1/#porterduffcompositingoperators_clear
[3]: https://www.w3.org/TR/compositing-1/#porterduffcompositingoperators_src
[4]: https://www.w3.org/TR/compositing-1/#porterduffcompositingoperators_dst
[5]: https://www.w3.org/TR/compositing-1/#porterduffcompositingoperators_srcover
[6]: https://www.w3.org/TR/compositing-1/#porterduffcompositingoperators_dstover
[7]: https://www.w3.org/TR/compositing-1/#porterduffcompositingoperators_srcin
[8]: https://www.w3.org/TR/compositing-1/#porterduffcompositingoperators_dstin
[9]: https://www.w3.org/TR/compositing-1/#porterduffcompositingoperators_srcout
[10]: https://www.w3.org/TR/compositing-1/#porterduffcompositingoperators_dstout
[11]: https://www.w3.org/TR/compositing-1/#porterduffcompositingoperators_srcatop
[12]: https://www.w3.org/TR/compositing-1/#porterduffcompositingoperators_dstatop
[13]: https://www.w3.org/TR/compositing-1/#porterduffcompositingoperators_xor
[14]: <del class="del">https://www.w3.org/TR/compositing-1/#blendingscreen</del> <ins class="ins">https://www.w3.org/TR/compositing-1/#porterduffcompositingoperators_plus</ins>
[15]: <del class="del">https://www.w3.org/TR/compositing-1/#blendingoverlay</del> <ins class="ins">https://www.w3.org/TR/compositing-1/#blendingscreen</ins>
[16]: <del class="del">https://www.w3.org/TR/compositing-1/#blendingdarken</del> <ins class="ins">https://www.w3.org/TR/compositing-1/#blendingoverlay</ins>
[17]: <del class="del">https://www.w3.org/TR/compositing-1/#blendinglighten</del> <ins class="ins">https://www.w3.org/TR/compositing-1/#blendingdarken</ins>
[18]: <del class="del">https://www.w3.org/TR/compositing-1/#blendingcolordodge</del> <ins class="ins">https://www.w3.org/TR/compositing-1/#blendinglighten</ins>
[19]: <del class="del">https://www.w3.org/TR/compositing-1/#blendingcolorburn</del> <ins class="ins">https://www.w3.org/TR/compositing-1/#blendingcolordodge</ins>
[20]: <del class="del">https://www.w3.org/TR/compositing-1/#blendinghardlight</del> <ins class="ins">https://www.w3.org/TR/compositing-1/#blendingcolorburn</ins>
[21]: <del class="del">https://www.w3.org/TR/compositing-1/#blendingsoftlight</del> <ins class="ins">https://www.w3.org/TR/compositing-1/#blendinghardlight</ins>
[22]: <del class="del">https://www.w3.org/TR/compositing-1/#blendingdifference</del> <ins class="ins">https://www.w3.org/TR/compositing-1/#blendingsoftlight</ins>
[23]: <del class="del">https://www.w3.org/TR/compositing-1/#blendingexclusion</del> <ins class="ins">https://www.w3.org/TR/compositing-1/#blendingdifference</ins>
[24]: <del class="del">https://www.w3.org/TR/compositing-1/#blendingmultiply</del> <ins class="ins">https://www.w3.org/TR/compositing-1/#blendingexclusion</ins>
[25]: <del class="del">https://www.w3.org/TR/compositing-1/#blendinghue</del> <ins class="ins">https://www.w3.org/TR/compositing-1/#blendingmultiply</ins>
[26]: <del class="del">https://www.w3.org/TR/compositing-1/#blendingsaturation</del> <ins class="ins">https://www.w3.org/TR/compositing-1/#blendinghue</ins>
[27]: <del class="del">https://www.w3.org/TR/compositing-1/#blendingcolor</del> <ins class="ins">https://www.w3.org/TR/compositing-1/#blendingsaturation</ins>
[28]: <del class="del">https://www.w3.org/TR/compositing-1/#blendingluminosity</del> <ins class="ins">https://www.w3.org/TR/compositing-1/#blendingcolor</ins>
[29]: <del class="del">https://www.w3.org/TR/compositing-1/#blendingnormal</del> <ins class="ins">https://www.w3.org/TR/compositing-1/#blendingluminosity</ins>
[30]: <del class="del">https://www.w3.org/TR/2011/REC-SVG11-20110816/pservers.html#LinearGradientElementSpreadMethodAttribute</del> <ins class="ins">https://www.w3.org/TR/compositing-1/#blendingnormal</ins>
[31]: <del class="del">https://www.w3.org/TR/SVG11/</del> <ins class="ins">https://www.w3.org/TR/2011/REC-SVG11-20110816/pservers.html#LinearGradientElementSpreadMethodAttribute</ins>
[32]: <ins class="ins">https://www.w3.org/TR/SVG11/
[33]:</ins> https://html.spec.whatwg.org/multipage/canvas.html#dom-context-2d-createradialgradient

<style>
    .del,.ins{ display: inline-block; margin-left: 0.5ex; }
    .del     { background-color: #fcc; }
         .ins{ background-color: #cfc; }
</style>

