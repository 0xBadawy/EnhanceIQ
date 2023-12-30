# Histogram Equalization

histogram equalization is a method in image proccessing  baesd on histogram of an image. it is used especially when the image is represented by a narrow range of intensity values.
its main functionality is to make the image's pixels have better  distributed on the histogram.




# difference of gaussian (DOG) 

In order to explain the DOG filter, we should first explain gaussian filter.

Gassian filter is based on the mathematical concept of the Gaussian function, which is a bell-shaped curve.



●π is the mathematical constant pi (approximately 3.14159).
●σ is the standard deviation of the Gaussian distribution, controlling the spread of the curve.
●e is the mathematical constant Euler's number (approximately 2.71828).
●G(x,y) is the value of the 2D Gaussian function at position (x,y)

The primary purpose of applying a Gaussian filter to an image is to reduce noise by smoothing the image 

Now let us explain GOG filter:
The Difference of Gaussians (DoG) filter is used in image processing to enhance edges and identify features at different scales.
It is obtained by subtracting one blurred version of an image from another, where each version is created using a Gaussian filter with a different standard deviation.

#  MSR Filter 
The Multiscale Retinex algorithm is an image enhancement technique that aims 
to improve the contrast of an image by normalizing pixel values across different scales. 
The algorithm operates by applying a series of operations at multiple scales.

1- define some parameters for applying the algorithm 
	sigma: the standard deviation for the Gaussian filter.
	scales: the scales at which the MSR algorithm operates.
	filt_size: the size of the Gaussian filter.
	enhanced: a matrix filled with zeros which will accumulates the Retinex components of the image.

2- iterate through each scale specified in scales and do the following:
	- apply the Gaussian Filter to the image at the current scale (Which was explained in the DOG filter).
	- calculate the Retinex component for the current scale.
	- accumulate the Retinex component to %enhanced%

3- Normalize the enhanced image to the range [0, 255] and convert it back to uint8



# PSNR
The PSNR block computes the peak signal-to-noise ratio, in decibels, between two images. This ratio is used as a quality measurement between the original and a compressed image. The higher the PSNR, the better the quality of the compressed, or reconstructed image.

PSNR = 10log10((L-1)^2/MSE

Here, L is the number of maximum possible intensity levels (minimum intensity level suppose to be 0) in an image.

MSE = 1/m*n ΣΣ(O(i,j)-D(i,j))^2
Where, O represents the matrix data of original image. D represents the matrix data of degraded image. m represents the numbers of rows of pixels and i represents the index of that row of the image. n represents the number of columns of pixels and j represents the index of that column of the image.



# SSIM 
Structural Similarity Index is a metric used to measure the similarity between two images. SSIM takes into account luminance, contrast, and structure, which are important aspects of human perception. The index produces a value between -1 and 1, where 1 indicates perfect similarity, -1 indicates perfect dissimilarity, and 0 means no similarity.
SSIM is widely used in image processing and computer vision to evaluate the quality of compressed images, assess the impact of image processing algorithms, or compare the similarity between an original image and a processed one. It provides a more comprehensive assessment than traditional metrics like Mean Squared Error (MSE), as it considers both global and local variations in image structure.



# Filter on color
[![](https://github.com/Mohamed-badawy-sayed/image_processing_Filters/blob/3675e6f05ed44220d019cced422d64c779c3d86b/repo/Image%20num%20(8).png)]()


# Filter on color
[![](https://github.com/Mohamed-badawy-sayed/image_processing_Filters/blob/3675e6f05ed44220d019cced422d64c779c3d86b/repo/Image%20num%20(5).png)]()

# PSNR Measurements

|          | DOG   | Hist     | MSR     |
|----------|-------|----------|---------|
| PSNR     | 5.97  | 16.6246  | 11.6054 |
[![](https://github.com/Mohamed-badawy-sayed/image_processing_Filters/blob/3675e6f05ed44220d019cced422d64c779c3d86b/repo/Image%20num%20(6).png)]()

# SSIM Measurements

|      | DOG     | Hist   | MSR   |
|------|---------|--------|-------|
| SSIM | -0.0811 | 0.7583 | 0.713 |
[![](https://github.com/Mohamed-badawy-sayed/image_processing_Filters/blob/3675e6f05ed44220d019cced422d64c779c3d86b/repo/Image%20num%20(7).png)]()

