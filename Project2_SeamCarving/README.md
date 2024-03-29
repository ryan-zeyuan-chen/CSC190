# Project 2: Seam Carving

Seam carving is a content-aware image resizing technique where the image is reduced in size by one pixel of width (or height) at a time. A vertical seam in an image is a path of pixels connected from the top to the bottom with one pixel in each row. Unlike standard content-agnostic resizing techniques (such as cropping and scaling), seam carving preserves the most interesting features of the image. Although the [underlying algorithm] is simple and elegant, is was not discovered until 2007.

The objective of this project is to create a data type that resizes a H-by-W image using the seam carving technique.

Images `3x4.png` and `6x5.png` are used for validation and the completed algorithm is tested using `HJoceanSmall.bmp`.

<p align="center">
  <img src="HJoceanSmall.bmp" alt="HJoceanSmall.bmp"/>
</p>

To accomplish this task, the following items will have to be completed:

* Energy Calculation
* Seam Identification
* Seam Removal

**_Notation_**. In image processing, pixel (y, x) refers to the pixel in column x and row y, with pixel (0, 0) at the upper-left corner and pixel (H-1, W-1) at the lower-right corner:

|   (0, 0)   |   (0, 1)   |   (0, 2)   |
|:----------:|:----------:|:----------:|
| **(1, 0)** | **(1, 1)** | **(1, 2)** |
| **(2, 0)** | **(2, 1)** | **(2, 2)** |
| **(3, 0)** | **(3, 1)** | **(3, 2)** |

**_Energy Calculation_**. The first step is to calculate the energy of a pixel, which is the measure of its perceptual importance. The higher the energy, the less likely that the pixel will be included as part of a seam. The dual-gradient energy function is utilized in this project for energy calculation. The energy is high for pixels in the image where there is a rapid colour gradient. The seam carving technique avoids removing such high-energy pixels.

**_Seam Identification_**. The next step is to find a vertical seam of minimum total energy. The seam is a path through pixels from top to bottom such that the sum of the energies of the pixels is minimal. The minimum-energy seam is identified using dynamic programming in this project.

**_Seam Removal_**. The final step is removing all pixels along the identified vertical seam from the image.

The following struct is utilized for this project:
```
struct rgb_img{
    uint8_t *raster;
    size_t height;
    size_t width;
};
```

`c_img.c` includes basic image manipulation tools as a groundwork for the project. `png2bin.py` includes Python scripts for file conversion between PNG images and BIN files. Development of the seam carving algorithm is implemented in `seamcarving.c`.

## Part 1: Dual-Gradient Energy Function

```
void calc_energy(struct rgb_img *im, struct rgb_img **grad);
```

The function computes the dual-gradient energy, and places it in `struct rgb_img *grad`.

The energy of pixel (y, x) is:

 **(R<sub>x</sub>(y, x)<sup>2</sup> + G<sub>x</sub>(y, x)<sup>2</sup> + B<sub>x</sub>(y, x)<sup>2</sup> + R<sub>y</sub>(y, x)<sup>2</sup> + G<sub>y</sub>(y, x)<sup>2</sup> + B<sub>y</sub>(y, x)<sup>2</sup>)<sup>1/2</sup>**

 Where **R<sub>x</sub>**, **R<sub>y</sub>**, **G<sub>x</sub>**, **G<sub>y</sub>**, **B<sub>x</sub>**, **B<sub>y</sub>** are the differences in the red, green, and blue components of pixels surrounding the central pixel, along the x and y-axis.

 For example: **R<sub>x</sub>(y, x) = (y, x + 1)<sub>red</sub> - (y, x - 1)<sub>red</sub>**

 To store the dual-gradient energy in `struct rgb_img`, the original energy value is divided by 10 and casted to `uint8_t`. For each pixel, the r, g, and b channels are set to the same energy value.

For example, the resultant dual-gradient energy of the image `3x4.png` is:
```
14      22      14
14      22      14
14      22      14
14      22      14
```

## Part 2: Cost Array

```
void dynamic_seam(struct rgb_img *grad, double **best_arr);
```

The function allocates and computes the dynamic array `*best_arr`.

`(*best_arr)[i*width+j]` contains the minimum cost of a seam from the top of grad to the point (i, j).

For `6x5.png`, the dual-gradient energy of the image is:
```
24      22      30      15      18      19
12      23      15      23      10      15
11      13      22      13      21      14
13      15      17      28      19      21
17      17      7       27      20      19
```

The best array is:
```
24.000000       22.000000       30.000000       15.000000       18.000000       19.000000
34.000000       45.000000       30.000000       38.000000       25.000000       33.000000
45.000000       43.000000       52.000000       38.000000       46.000000       39.000000
56.000000       58.000000       55.000000       66.000000       57.000000       60.000000
73.000000       72.000000       62.000000       82.000000       77.000000       76.000000
```

## Part 3: Seam Recovery

```
void recover_path(double *best, int height, int width, int **path);
```

The function allocates a `path` through the minimum seam as defined by `best`.

For `6x5.png`, the `path` is `[3, 4, 3, 2, 2]`.

## Part 4: Seam Removal

```
void remove_seam(struct rgb_img *src, struct rgb_img **dest, int *path);
```

The function creates the destination image `struct rgb_img *dest` based on the source image `struct rgb_img *src`, with the seam removed.

## Part 5: Visualization

The algorithm is executed repeatedly in `main.c` to remove seams from the `HJoceanSmall.bmp` image. The process is visualized below:

<p align="center">
  <img src="HJoceanSmall.gif" alt="HJoeanSmall.gif"/>
</p>

[underlying algorithm]: https://www.youtube.com/watch?v=6NcIJXTlugc
