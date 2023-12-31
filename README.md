
 <h1 style="text-align: center;">Image Processing Filters</h1>


## Histogram Equalization

Histogram equalization is a method in image proccessing  baesd on histogram of an image. it is used especially when the image is represented by a narrow range of intensity values.
its main functionality is to make the image's pixels have better  distributed on the histogram.




## difference of gaussian (DOG) 

In order to explain the DOG filter, we should first explain gaussian filter.

Gassian filter is based on the mathematical concept of the Gaussian function, which is a bell-shaped curve.



●π is the mathematical constant Pi (approximately 3.14159).

●σ is the standard deviation of the Gaussian distribution, controlling the spread of the curve.

●e is the mathematical constant Euler's number (approximately 2.71828).

●G(x,y) is the value of the 2D Gaussian function at position (x,y)

The primary purpose of applying a Gaussian filter to an image is to reduce noise by smoothing the image 

Now let us explain GOG filter:

The Difference of Gaussians (DoG) filter is used in image processing to enhance edges and identify features at different scales.
It is obtained by subtracting one blurred version of an image from another, where each version is created using a Gaussian filter with a different standard deviation.

##  MSR Filter 
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



## PSNR
The PSNR block computes the peak signal-to-noise ratio, in decibels, between two images. This ratio is used as a quality measurement between the original and a compressed image. The higher the PSNR, the better the quality of the compressed, or reconstructed image.

PSNR = 10log10((L-1)^2/MSE

Here, L is the number of maximum possible intensity levels (minimum intensity level suppose to be 0) in an image.

MSE = 1/m*n ΣΣ(O(i,j)-D(i,j))^2
Where, O represents the matrix data of original image. D represents the matrix data of degraded image. m represents the numbers of rows of pixels and i represents the index of that row of the image. n represents the number of columns of pixels and j represents the index of that column of the image.



## SSIM 
Structural Similarity Index is a metric used to measure the similarity between two images. SSIM takes into account luminance, contrast, and structure, which are important aspects of human perception. The index produces a value between -1 and 1, where 1 indicates perfect similarity, -1 indicates perfect dissimilarity, and 0 means no similarity.
SSIM is widely used in image processing and computer vision to evaluate the quality of compressed images, assess the impact of image processing algorithms, or compare the similarity between an original image and a processed one. It provides a more comprehensive assessment than traditional metrics like Mean Squared Error (MSE), as it considers both global and local variations in image structure.

## Code to use 
```matlab
input=datastore("dataset");
gaussian1 = fspecial('Gaussian', 10, 1.07);
gaussian2 = fspecial('Gaussian', 10, 1);
dog = gaussian1 - gaussian2;
sigma = 5;
scales = [15, 80, 250];
filt_size = 2 * ceil(3 * sigma) + 1;
Psnr_histo=0;
Psnr_MSR=0;
Psnr_DoG=0;
SSIM_histo=0;
SSIM_MSR=0;
SSIM_DoG=0;

for i=1:length(input.Files)
    img=input.read();
    img=rgb2ycbcr(img);
    img2=img;
    GrayLevel=img(:,:,1);
    disp('%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%');
    
    
    %%%%%%%%%%%%% apply histogram%%%%%%%%%%%%%%%%%
    HistImage=histeq(GrayLevel);
    img(:,:,1)=HistImage;
    imwrite(ycbcr2rgb(img),['Histogram/img' int2str(i) '.jpg']);
    Psnr_histo=Psnr_histo+psnr(img,img2);
    SSIM_histo=SSIM_histo+ssim(img,img2);
    disp (['psnr for image ' int2str(i) ' after apply Histogram equalization equals to '  num2str(psnr(img,img2))]);
    disp (['ssim for image ' int2str(i) ' after apply Histogram equalization equals to '  num2str(ssim(img,img2))]);
    


    %%%%%%%%%%%%% apply DoG %%%%%%%%%%%%%%%%%%%%%%
    DoGImage=conv2(double(GrayLevel), dog, 'same');
    DoGImage=uint8(255*DoGImage);
    img(:,:,1)=DoGImage;
    imwrite(ycbcr2rgb(img),['DOG/img' int2str(i) '.jpg']);
    Psnr_DoG=Psnr_DoG+psnr(img,img2);
    SSIM_DoG=SSIM_DoG+ssim(img,img2);
    disp (['psnr for image ' int2str(i) ' after apply DoG equals to '  num2str(psnr(img,img2))]);
    disp (['ssim for image ' int2str(i) ' after apply DoG equals to '  num2str(ssim(img,img2))]);
    


    %%%%%%%%%%%%% apply MSR %%%%%%%%%%%%%%%%%%%%%%
    Img = double(GrayLevel);
    enhanced = zeros(size(Img));
    for j = 1:length(scales)
        gaussian_filter = fspecial('gaussian', [filt_size, filt_size], sigma * scales(j));
        blurred_image = imfilter(Img, gaussian_filter, 'conv');
     
        retinex_component = log(Img + 1) - log(blurred_image + 1);
     
        enhanced = enhanced + retinex_component;
    end
    enhanced = uint8( (enhanced - min(enhanced(:))) / (max(enhanced(:)) - min(enhanced(:))) * 255 );
    img(:,:,1)=enhanced;
    imwrite(ycbcr2rgb(img),['MSR/img' int2str(i) '.jpg']);
    Psnr_MSR=Psnr_MSR+psnr(img,img2);
    SSIM_MSR=SSIM_MSR+ssim(img,img2);
    disp (['psnr for image ' int2str(i) ' after apply MSR equals to '  num2str(psnr(img,img2))]);
    disp (['ssim for image ' int2str(i) ' after apply MSR equals to '  num2str(ssim(img,img2))]);
    
    
    disp('%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%');
    
    
end
Psnr_MSR=Psnr_MSR/length(input.Files);
Psnr_DoG=Psnr_DoG/length(input.Files);
Psnr_histo=Psnr_histo/length(input.Files);
SSIM_MSR=SSIM_MSR/length(input.Files);
SSIM_DoG=SSIM_DoG/length(input.Files);
SSIM_histo=SSIM_histo/length(input.Files);
disp(['average Psnr for histogram equalization is ' num2str(Psnr_histo)]);
disp(['average Psnrfor MSR is ' num2str(Psnr_MSR)]);
disp(['average Psnr for DoG is ' num2str(Psnr_DoG)]);
disp(['average ssim for histogram equalization is ' num2str(SSIM_histo)]);
disp(['average ssim for MSR is ' num2str(SSIM_MSR)]);
disp(['average ssim for DoG is ' num2str(SSIM_DoG)]);

```

## Filter on color
[![](https://github.com/Mohamed-badawy-sayed/image_processing_Filters/blob/3675e6f05ed44220d019cced422d64c779c3d86b/repo/Image%20num%20(8).png)]()


## Filter on grayscale
[![](https://github.com/Mohamed-badawy-sayed/image_processing_Filters/blob/3675e6f05ed44220d019cced422d64c779c3d86b/repo/Image%20num%20(5).png)]()

## PSNR Measurements

| [![](https://github.com/Mohamed-badawy-sayed/image_processing_Filters/blob/3675e6f05ed44220d019cced422d64c779c3d86b/repo/Image%20num%20(6).png)]() |
|----------------------------------------------------------------------------------------------------------------------------------------------------|

| MSR     | Hist    | DOG  |
|---------|---------|------|
| 11.6054 | 16.6246 | 5.97 |

## SSIM Measurements

| [![](https://github.com/Mohamed-badawy-sayed/image_processing_Filters/blob/3675e6f05ed44220d019cced422d64c779c3d86b/repo/Image%20num%20(7).png)]() |
|----------------------------------------------------------------------------------------------------------------------------------------------------|

| MSR   | Hist   | DOG     |
|-------|--------|---------|
| 0.713 | 0.7583 | -0.0811 |



## contributors

<table>
    <tr>
        <td><a
                href="https://github.com/Mohamed-badawy-sayed"><img
                    src="https://github.com/Mohamed-badawy-sayed/info/blob/baf974551d6a6ccc8b8429f99c7f656b9529ec78/repo/images/file/profile%20(1).png"
                    alt="Image 1"></a></td>
        <td><a
                href="https://github.com/Mostafa-El-gelany"><img
                    src="https://github.com/Mohamed-badawy-sayed/info/blob/baf974551d6a6ccc8b8429f99c7f656b9529ec78/repo/images/file/profile%20(2).png"
                    alt="Image 1"></a></td>
        <td><a
                href="https://github.com/Ahmed-Nageh-Abbas"><img
                    src="https://github.com/Mohamed-badawy-sayed/info/blob/baf974551d6a6ccc8b8429f99c7f656b9529ec78/repo/images/file/profile%20(3).png"
                    alt="Image 1"></a></td>
        <td><a
                href="https://github.com/Mahmoud-Khalell"><img
                    src="https://github.com/Mohamed-badawy-sayed/info/blob/baf974551d6a6ccc8b8429f99c7f656b9529ec78/repo/images/file/profile%20(4).png"
                    alt="Image 1"></a></td>
        <td><a
                href="https://github.com/nadamaaman55"><img
                    src="https://github.com/Mohamed-badawy-sayed/info/blob/baf974551d6a6ccc8b8429f99c7f656b9529ec78/repo/images/file/profile%20(6).png"
                    alt="Image 1"></a></td>
        <td><a
                href="https://github.com/Basant-Benyamen"><img
                    src="https://github.com/Mohamed-badawy-sayed/info/blob/baf974551d6a6ccc8b8429f99c7f656b9529ec78/repo/images/file/profile%20(5).png"
                    alt="Image 1"></a></td>
    </tr>
    <tr>
        <td><a href="https://github.com/Mohamed-badawy-sayed"> Mohamed
                badawy</a></td>
        <td><a href="https://github.com/Mostafa-El-gelany">Mostafa
                El-gelany</a></td>
        <td><a href="https://github.com/Ahmed-Nageh-Abbas">Ahmed Nageh</a></td>
        <td><a href="https://github.com/Mahmoud-Khalell">Mahmoud
                Khalell</a></td>
        <td><a href="https://github.com/nadamaaman55">Nada Maaman</a></td>
        <td><a href="https://github.com/Basant-Benyamen">Basant Benyamen</a></td>
    </tr>
</table>


