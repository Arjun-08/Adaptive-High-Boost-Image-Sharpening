# Adaptive High-Boost Image Sharpening


This project implements and analyzes **high-boost image sharpening** using a Laplacian high-pass filter. The notebook compares conventional sharpening with a **constant gain factor** against an **adaptive gain mechanism**, where the sharpening strength varies according to the local edge strength of the image.

The main objective is to investigate how high-boost filtering can be used to enhance image details and how an **adaptive gain** can provide different sharpening strengths depending on the local high-frequency content of an image.

The conventional high-boost operation uses a fixed gain, while the adaptive approach modifies the gain according to the magnitude of the high-pass filter response.


### High-Pass Filtering

A high-pass filter emphasizes high-frequency components such as:

* Edges
* Fine details
* Texture
* Rapid intensity transitions

In this project, a **3 × 3 Laplacian filter** is used:

$$
h =
\begin{bmatrix}
-1 & -1 & -1 \\
-1 & 8 & -1 \\
-1 & -1 & -1
\end{bmatrix}
$$

The filtered image is obtained through convolution:

$$
H(m,n) = f(m,n) * h(m,n)
$$

where:

* \(f(m,n)\) is the input image
* \(h(m,n)\) is the high-pass filter
* \(*\) denotes convolution
* \(H(m,n)\) is the high-pass filter response

The magnitude of this response represents the strength of local high-frequency information.


## High-Boost Filtering

High-boost filtering combines the original image with its high-frequency component.

The sharpened image is given by:

$$
g(m,n) = f(m,n) + k(m,n)\left[f(m,n) * h(m,n)\right]
$$

where:

* \(g(m,n)\) is the sharpened image
* \(f(m,n)\) is the original image
* \(h(m,n)\) is the high-pass filter
* \(k(m,n)\) is the gain factor
* \(*\) represents convolution

The gain determines how strongly the high-frequency information is added back to the original image.



## Constant Gain

In the conventional approach, the gain is fixed for every pixel:

$$
k(m,n)=k
$$

The notebook uses:

$$
k=1.0
$$

Therefore:

$$
g(m,n) = f(m,n) + k(m,n)\left[f(m,n) * h(m,n)\right]
$$

A constant gain applies the same sharpening strength throughout the image, regardless of whether a pixel belongs to a strong edge, weak edge, or relatively smooth region.



## Adaptive Gain

Instead of using a fixed gain, the adaptive approach calculates a spatially varying gain from the magnitude of the high-pass filter response.

Let:

$$
M(m,n) = \left|f(m,n) * h(m,n)\right|
$$

where \(M(m,n)\) represents the local edge or high-frequency strength.

The adaptive gain implemented in the notebook is:


$$
k(m,n) = k_{\max}\left(\frac{M(m,n)}{P}\right)
\exp\left(1-\frac{M(m,n)}{P}\right)
$$

with a minimum gain constraint:

$$
k(m,n)=\max(k(m,n),0.3)
$$

where:

* \(k_{\max}\) is the maximum gain parameter
* \(P\) is the peak point controlling where the gain reaches its maximum
* \(M(m,n)\) is the magnitude of the high-pass response
* \(0.3\) is the minimum allowed gain

The implementation uses:

| Parameter                 | Value |
| ------------------------- | ----: |
| Maximum gain \(k_{\max}\) |   2.5 |
| Peak point \(P\)          |    30 |
| Minimum gain              |   0.3 |

The resulting sharpened image is:

$$
g(m,n) = f(m,n) + k(m,n)H(m,n)
$$

### Why Adaptive Gain?

The adaptive gain changes the amount of sharpening according to the local high-frequency response.

This allows the sharpening operation to respond differently to different image regions rather than applying one fixed gain everywhere.


## Adaptive Gain Curve

The notebook also plots the adaptive gain as a function of the high-pass response magnitude.

The curve is generated for:

$$
0 \leq M \leq 100
$$

The gain initially increases with edge strength, reaches a maximum around the specified peak point, and then decreases for larger response magnitudes.

The minimum gain constraint prevents the gain from becoming too small.

The resulting curve helps visualize how the adaptive sharpening mechanism responds to different levels of edge strength.



## Experiment Flow

The overall experimental procedure is:

1. Load the input images.
2. Define the Laplacian high-pass filter.
3. Apply the high-pass filter to each image.
4. Compute the high-frequency response.
5. Perform high-boost sharpening using a constant gain.
6. Compute the adaptive gain from the filter response.
7. Perform high-boost sharpening using the adaptive gain.
8. Clip the resulting pixel values to the valid range \(0\)–\(255\).
9. Compare the original, constant-gain, and adaptive-gain images.
10. Plot the adaptive gain curve.


## Parameters and Cases

The notebook evaluates the following main cases:

| Case                | Gain Strategy          | Parameter        |
| ------------------- | ---------------------- | ---------------- |
| Original            | No sharpening          | —                |
| Constant Gain       | Fixed spatial gain     | \(k=1.0\)        |
| Adaptive Gain       | Spatially varying gain | \(k_{\max}=2.5\) |
| Adaptive Gain Curve | Gain vs. edge strength | Peak point = 30  |

The adaptive approach uses the local magnitude of the high-pass filter output to determine the gain at each spatial location.


## Output and Results

The notebook generates visual results for multiple input images.

For each image, the output comparison contains:

1. **Original Image**
2. **Constant Gain Sharpening**
3. **Adaptive Gain Sharpening**

The plots allow direct visual comparison of how the two sharpening strategies modify image details and edges.

### Results

The generated result plots are:


### Image Sharpening Comparison
<img width="1291" height="490" alt="image" src="https://github.com/user-attachments/assets/dedc111f-9ee5-48c4-a739-304d90b7a38b" />

<img width="1310" height="490" alt="image" src="https://github.com/user-attachments/assets/10c8be63-1b7e-4ceb-a8a0-833e2b1f99f2" />

<img width="1489" height="312" alt="image" src="https://github.com/user-attachments/assets/300a9914-f6f6-4661-8537-8be225e0d747" />



### Adaptive Gain Curve

<img width="846" height="471" alt="image" src="https://github.com/user-attachments/assets/8bb067e0-1649-4d79-a603-4b689c4ca402" />




## Key Observations

| Observation              | Key Finding                                                                                                                           |
| ------------------------ | ------------------------------------------------------------------------------------------------------------------------------------- |
| **High-pass filtering**  | The Laplacian filter emphasizes high-frequency image components such as edges and fine details.                                       |
| **High-boost filtering** | Adding the high-pass response to the original image enhances image details.                                                           |
| **Constant gain**        | A fixed gain applies the same sharpening strength throughout the image.                                                               |
| **Adaptive gain**        | The sharpening strength varies according to the magnitude of the high-pass response.                                                  |
| **Gain curve**           | The adaptive gain increases toward the specified peak point and decreases for larger response magnitudes.                             |
| **Minimum gain**         | A lower bound of 0.3 prevents the adaptive gain from becoming excessively small.                                                      |
| **Image comparison**     | Comparing the original, constant-gain, and adaptive-gain outputs provides a visual assessment of the different sharpening strategies. |



## Pixel Value Handling

Image processing is performed using floating-point values to avoid unwanted numerical effects during filtering and gain multiplication.

After sharpening, the output is restricted to the valid 8-bit image range:

$$
g(m,n)=(g(m,n),0,255)
$$

The final result is converted back to `uint8` for image display and storage.
