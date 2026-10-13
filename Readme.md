# Fourier Shape Drawing

Watch spinning circles draw a shape. The page breaks a closed outline into a Fourier series, then
redraws it with chains of rotating circles, one term at a time, until the drawing almost exactly
matches the original.

It was inspired by [this matharium post](https://x.com/matharium/status/2109692067026960411), which
draws an order-4 Moore curve the same way.

## Running it

The whole page is one file with no dependencies and no build step. Open `index.html` in any modern
browser (Chrome, Edge, Firefox or Safari).

## Features

- **Two ways to draw.** By default, one chain of circles controls x and another controls y, and the
  pen sits where their guide lines cross. **Single chain** switches to one chain centred on the shape
  that draws with its tip. Both draw exactly the same curve.
- **Built-in shapes.** The Moore curve (4×4 up to 32×32), Koch snowflake, star, heart and square.
- **Your own shapes.** Upload an SVG, or a picture (PNG, JPG and so on), and the page finds the outline
  for you. Separate pieces can be joined into one loop with thin bridges.
- **Two animation modes.** In the default mode the pen traces the shape once for each N, and N grows
  after every loop. **Animate shape** instead shows the finished drawing for each N and adds one term at
  a time, so you can watch the shape sharpen.
- **See the maths.** **Show series** lists every term in use. The **N** and **error** readouts show how
  many terms are in use and how far the drawing is from the real shape at its worst point.
- **Zoom in on the tiny circles.** The mouse wheel zooms, and the animation slows down as you zoom in.
  Middle-button drag pans the view, and **Follow tip** keeps the pen centred.
- **Built-in help.** **Help** explains Fourier series, how the page works and what every control does,
  with links for further reading.

## Controls

| Control | What it does |
| --- | --- |
| Pause / Play, **Space**, left-click the drawing | Pause or resume |
| Restart | Go back to N = 1 |
| Single chain | Switch between separate x/y chains and one chain centred on the shape |
| Animate shape | Show the finished drawing for each N instead of tracing it |
| Show series | List the terms in use (pauses while open) |
| Reset view / Follow tip | Undo zoom and pan / keep the pen centred |
| Help | Open the help (pauses while open) |
| Shape, Upload SVG / image… | Choose a built-in shape or load your own |
| Join pieces, Threshold | Upload options: connect separate outlines; choose which pixels count as the shape (pictures only) |
| N slider | Pick how many terms to use (this stops N growing on its own) |
| Speed | 0.01× to 1× on a logarithmic slider |
| Mouse wheel / middle-button drag | Zoom around the pointer / pan |

## How it works

1. **The shape becomes a loop of points** joined by straight lines, walked at a steady speed with a
   parameter `t` that runs from 0 to 2π.
2. **The terms are calculated exactly.** Because the loop is made of straight segments, its second
   derivative is zero everywhere except at the corners. Integrating by parts twice turns each Fourier
   coefficient into a sum over the corners, with no numerical integration:

   `c_k = −1/(2πk²) · Σ_j Δslope_j · e^(−ik·t_j)`

   This gives a real series for each axis: `x(t) = x̄ + Σ r_k cos(kt + φ_k)`, and the same for y.
3. **Single chain mode** uses the same coefficients. Each x/y pair is turned into two circles spinning
   at the same speed in opposite directions, using `x + iy = Σ c_n e^(int)` with n from −N to N, so
   the drawing is identical.
4. **Symmetric shapes skip terms.** If every even-numbered term comes out as zero, the page skips
   those terms. This happens for shapes that look the same rotated 180°, such as the Moore curve.
5. **The error** is the largest distance between the drawing and the true loop, measured at 2,048
   points and given as a percentage of the shape's longer side.

### Uploads

- **SVG**: the browser's `getTotalLength()` and `getPointAtLength()` sample every path and basic shape,
  with transforms applied. A path is split into separate pieces wherever it jumps (at a move-to).
- **Pictures**: the image is shrunk to at most 320 px. Pixels count as the shape if they aren't
  transparent, or, when the image has no transparency, if they're darker than a threshold. The starting
  threshold is chosen with Otsu's method. The page then follows the pixel edges into closed outlines,
  holes included, and smooths them.
- **Joining**: each piece is connected to the nearest point of the loop so far with a bridge that is
  travelled out and back. The result is resampled to 1,024 points and given 255 terms per axis.

The `samples/` folder has a few pictures to try uploading.

## Adding a built-in shape

Add an entry to `BUILTIN` in `index.html`: a function that returns a shape made from a list of `[x, y]`
points. Use `generic(points)` to scale and centre it automatically. The loop closes itself, so don't
repeat the first point at the end. The new shape also needs an `<option>` in the Shape dropdown.

```js
triangle: () => generic([[0, 0], [1, 0], [0.5, 0.87]]),
```

## Further reading

- [But what is a Fourier series? (3Blue1Brown)](https://www.youtube.com/watch?v=r6sGWTCMz2k)
- [An Interactive Introduction to Fourier Transforms](https://www.jezzamon.com/fourier/)
- [Fourier series (Wikipedia)](https://en.wikipedia.org/wiki/Fourier_series)
- [Moore curve (Wikipedia)](https://en.wikipedia.org/wiki/Moore_curve)
