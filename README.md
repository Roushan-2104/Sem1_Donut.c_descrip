
# donut.c: The Mathematics of the Spinning Torus

This repository contains an explanation and analysis of the famous **donut.c** code, originally written by [Andy Sloane](https://www.a1k0n.net/2011/07/20/donut-math.html) in 2006. It is a masterpiece of obfuscated C programming that renders a spinning 3D torus (donut) using ASCII characters.

## 🚀 Quick Start

To see the donut in action, compile and run the code using a C compiler (like `gcc`).

1. **Save the code** into a file named `donut.c`.
2. **Compile** (ensure you link the math library with `-lm`):
```bash
gcc donut.c -lm -o donut

```


3. **Run**:
```bash
./donut

```



---

## 🧠 How It Works

At a high level, the program performs 3D rendering logic usually reserved for Graphics Processing Units (GPUs), but it does so entirely in the CPU using text characters.

The process can be broken down into five distinct steps within the main infinite loop:

### 1. The Geometry (The Torus)

A torus is essentially a circle rotated around an axis. The code uses two loop variables, `i` and `j`, to sweep across the surface of the torus.

* **`i` ():** Goes from 0 to . This draws the cross-sectional circle of the donut.
* **`j` ():** Goes from 0 to . This rotates that circle around the central axis of the donut.

### 2. The Rotation (Spinning)

To make the donut spin, the code applies a 3D rotation matrix to every point  on the torus surface.

* **`A`**: The angle of rotation around the X-axis.
* **`B`**: The angle of rotation around the Z-axis.
* These variables are incremented at the end of every frame (`A+=0.08; B+=0.04`), causing the animation.

### 3. 3D to 2D Projection

The computer screen is 2D, but the math is 3D. To render this, the code performs **perspective projection**.

* It calculates the distance of the point from the viewer.
* It moves points that are further away closer to the center of the screen, creating the illusion of depth.
* The variable `D` represents `1/z` (inverse depth), which is used to scale the  and  coordinates.

### 4. The Z-Buffer (Depth Handling)

The screen is represented by a buffer array `b[]` (characters) and a Z-buffer array `z[]` (depth data).

* The code calculates thousands of points. Many points will overlap (e.g., the front of the donut overlaps the back).
* **The Logic:** Before drawing a character, the code checks `z[]`. If the new point is closer to the camera than what is currently there, it overwrites it. If it is further away (behind), it skips it.

### 5. Luminance (ASCII Shading)

How does the donut look 3D? By lighting.

* The code calculates the **Surface Normal** for every point (a vector perpendicular to the surface).
* It calculates the dot product of the surface normal and a light direction vector (fixed lighting).
* The result, `N`, determines how bright that pixel is.
* `N` is mapped to a string of characters sorted by pixel density:
```c
".,-~:;=!*#$@"

```


( `.` is dim/shadow, `@` is bright/lit).

---

## 🔍 De-obfuscating the Variables

The code is "obfuscated," meaning it is intentionally written to be hard to read (and shaped like a donut). Here is a translation of the single-letter variables:

| Variable | Meaning |
| --- | --- |
| **`A`, `B**` | The global rotation angles (animation state). |
| **`i`, `j**` | Loop iterators for the torus angles ( and ). |
| **`c`, `d`, `e`, `f`, `g`...** | Pre-calculated sines and cosines. e.g., `c=sin(i)`, `d=cos(j)`. This optimizes performance. |
| **`D`** | . Used for perspective projection (things get smaller as they get farther). |
| **`x`, `y**` | The final 2D coordinates on your terminal screen. |
| **`o`** | The offset/index in the screen buffer (calculating `y * width + x`). |
| **`N`** | Luminance index (0 to 11). Determines which ASCII character to use. |
| **`z[]`** | The Z-buffer (stores the depth of every pixel). |
| **`b[]`** | The frame buffer (stores the characters to be printed). |

---

## 📐 The Math in a Nutshell

If you were to write this in standard mathematical notation, the projection formula used in the code looks roughly like this:

Where:

*  is the point after rotation.
*  is the field of view constant.
*  is the distance from the camera to the donut.

The code combines the rotation matrices and projection into one condensed block of algebra to save space:

```c
// The "Magic" Formula in the code
D = 1 / (c * h * e + f * g + 5); 
int x = 40 + 30 * D * (l * h * m - t * n);
int y = 12 + 15 * D * (l * h * n + t * m);

```

---

## 📝 Credits

* **Original Author:** Andy Sloane
* **Source:** [The IOCCC (International Obfuscated C Code Contest)](https://www.ioccc.org/) inspired structure.
* **Original Blog Post:** [Donut Math](https://www.a1k0n.net/2011/07/20/donut-math.html) - Highly recommended for a deep dive into the derivation of the surface normals.

