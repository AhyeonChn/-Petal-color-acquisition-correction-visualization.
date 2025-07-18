This study focuses not on quantitative analysis of color, but rather on visually clear presentation of petal color variation across developmental stages. Accordingly, color correction was performed using a linear transformation method sufficient to preserve key hues without introducing distortion, and ROI-based color selection was designed to ensure both representativeness and visual interpretability.
Please feel free to contact us if you have any questions regarding the method.

## Color Processing Pipelines

This repository contains two MATLAB-based pipelines:

1. **Color Correction from Patches**
2. **Custom Colormap Extraction from ROIs**

---

## 1. Color Correction from Patches

This pipeline performs color correction on an image using six manually selected color patches.

### Workflow

1. **Image Input**

   - The user is prompted to enter the filename (e.g., `sample_image.tif`).
   - The image is loaded without preprocessing (e.g., no blurring applied).

2. **Manual ROI Selection**

   - The image is displayed.
   - The user manually selects **6 rectangular regions (patches)** in the order: **Left → Right**, then **Top → Bottom**.
   - Each patch is assumed to represent a known color.
   - The mean RGB value of each patch is calculated.

3. **Reference Color Definition**

   - The script defines a 6×3 matrix of **reference RGB values** (i.e., true target colors).
   - These represent the desired RGB values for the selected patches.

4. **Color Correction Matrix**

   - A **3×3 transformation matrix (T)** is computed using least-squares:
     ```
     measuredRGB × T ≈ referenceRGB
     ```
   - This matrix is used to map all pixels in the image from the measured color space to the target space.

5. **Apply Correction**

   - The transformation matrix is applied to every pixel in the image.
   - All values are clamped between 0 and 255 and converted back to 8-bit format.

6. **Output**

   - The corrected image is displayed and saved as:
     ```
     corrected_noblur_<original_filename>
     ```

### Requirements

- MATLAB (tested on R2021b or later)
- Manual input support (via `getrect`)

### Notes

- This pipeline assumes a **linear color distortion** model.
- For best results, use under **controlled lighting** and ensure **accurate ROI selection**.
- No filtering or blurring is applied in this version.

---

## 2. Custom Colormap Extraction from ROIs

This pipeline generates a custom colormap by extracting dominant colors from user-defined ROIs based on HSV brightness and hue.

### Workflow

1. **Image Loading**

   - The user is prompted to select an image file (`.jpg`, `.png`, or `.tif`).
   - The selected image is loaded into MATLAB.

2. **Preprocessing (Gaussian Blur)**

   - A Gaussian blur (sigma = 1) is applied to the image to reduce noise and smooth transitions.

3. **Manual ROI Selection**

   - The user selects **30 polygonal ROIs** using the mouse.
   - Each ROI is expected to represent a meaningful area of color in the image.

4. **Color Extraction Per ROI**

   - For each ROI:
     - Pixels are extracted and filtered to include only those with **Value (V) ≥ 0.5** in the HSV color space (i.e., brighter pixels).
     - Unique RGB values are counted and sorted by frequency.
     - The top 30 most frequent colors are stored.

5. **Color Sorting**

   - All collected colors (up to 400 total) are converted to HSV and sorted by **Hue**.
   - This results in a smooth color transition in the final colormap.

6. **Output**

   - The colormap is saved in three formats:
     - `custom_colormap.mat`: MATLAB variable
     - `custom_colormap.csv`: RGB in [0, 1] scale
     - `top400_RGB_raw_sorted.csv`: Integer RGB values with headers

7. **Visualization**

   - A colormap visualization is displayed using `imagesc`, allowing users to inspect the final color palette.

### Requirements

- MATLAB (R2021b or newer recommended)
- Image Processing Toolbox

### Notes

- Only bright pixels (V ≥ 0.5) are used to avoid including dark or low-saturation background colors.
- ROI selection is manual; accurate placement leads to better palette representation.
- Hue sorting helps organize the final palette aesthetically.

### File Summary

- `colormap_by_20ROIs_HSVsorted_Vfilter.m`: Main script
- `custom_colormap.mat`: Final colormap (MATLAB format)
- `custom_colormap.csv`: Normalized RGB values
- `top400_RGB_raw_sorted.csv`: Raw RGB values with header

