# Finding Lane Lines on the Road

The goal of this project is making a pipeline to detect the lane lines on the road. For this purpose, computer vision techniques are used with a series of transformations to predict left and right lane lines on the road.

## 1. The Pipeline

In this project, the pipeline consists of 5 steps: 3 transformations, masking the region of interest and finally drawing the predicted lines.

Several test images are used with dimensions: (540, 960, 3).

An example of a test image.
![An example of a test image](/test_images/solidWhiteCurve.jpg)

To detect the edges on the given test images, grayscale images were needed. Thus, as a first step, images with three color channels (RGB) are transformed to grayscale images using the method `cvtColor` of OpenCV (Open Source Computer Vision Library). 

An example of an image transformed to grayscale.
![An example of a grayscale image](/transformations/test_images_grayscale/solidWhiteCurve.jpg)

But before detecting the edges with these grayscaled images, it is more convenient to apply a Gaussian smoothing to reduce the noise. `GaussianBlur` method of OpenCV is used for Gaussian smoothing. 

An example of an image after Gaussian smoothing.
![An example of a Gaussian smooting image](/transformations/test_images_blur/solidWhiteCurve.jpg)

Then, to detect the edges on the image, Canny edge detection is applied (John F. Canny, 1986). The edges are detected using the `Canny` method of OpenCv.

After these series of transformations edges were finally detected. 

An example for the edges on a test image.
![An example of a Canny edges](/transformations/test_images_edges/solidWhiteCurve.jpg)

Since the purpose is only to find the lane lines, the region of interest is masked to get only the edges on the road. The `region_of_interest` function is used for a polygonal masking.

An example for edges in the region of interest.
![An example of the region of interest](/transformations/test_images_masked/solidWhiteCurve.jpg)

Then to find the lane lines from Canny edges, a Hough transform (Paul Hough, 1962) is applied on the final image. `HoughLinesP` method of openCV is used for this transformation. After the Hough transformation, The raw data of lines indicating where the lane lines exist on the road is obtained. Then, `draw_lines` function is used to mark these lines in red color on the original images. A low opacity value on the original image is also used to highlight the markings.

An example for the final image with lane lines marked.
![An example of a Hugh transform](/test_images_output/solidWhiteCurve.jpg)


## 2. Extention to Detecting and Marking Lane Lines

In the pipeline, `draw_lines` function is extended by implementing a polynomial regression for the raw data samples of detected lines with the Hugh transform. A regression with a linear polynomial (m * x + b) was enough to get a prediction for the left and right lane lines separately to draw two solid red lines on them.

After this extension, the pipeline was completed. This whole pipeline is then used to detect and mark the lane lines on several video streams.

## 3. Potential Shortcomings

One important shortcoming is that horizontal lines were also detected as can be seen on the last image above. Since the horizontal lines seem to appear only on the horizon, restricting the region of interest by lowering the height of the mask might be a solution. But, when it comes to more challenging streams, that is not the case. For example, when there is shadow on the road, horizontal lines not only appear at the horizon but also on the regions that shadows appear. Application on the `challenge.mp4` video stream is a good example for this.

Horizontal lines appear when there is shadow on the road.
[![Video challenge raw uncleaned](test_videos_output/challenge_raw.gif?raw=true)](https://youtu.be/f5VzWaOixvQ)


To get rid of these unwanted horizontal lines, at the Hough transformation step, a clean-out method is implemented. This clean-out method discards the lines with a negative/positive slope of a value that is approximately zero.

After the clean-out implementation, all the horizontal lines are cleaned out from the challenge video stream.
[![Video challenge raw cleaned](test_videos_output/challenge_cleaned_raw.gif?raw=true)](https://youtu.be/VY4R8rLKlBM)

Another shortcoming is that in this work the curved lane lines are not being predicted. The pipeline cannot detect road bends. Thus, a path planning for a vehicle on a curved road would fail.

Another shortcoming is that when there is reflection of the sun on the road, it gets hard to detect the lines. Thus, whenever there is a strong reflection on the road, lane line predictions are lost.

## 4. Possible Improvements

Since the system cannot detect curved lines, a possible improvement would be to use a higher order polynomial regression when detecting the lines. Thus the system would also be able to detect the road bends.

Another improvement would be to use a color selection. Selecting only the white and yellow colors and then going through the steps of the pipeline would improve the success of detecting lines, especially when there is reflection on the road. For example, this approach would provide a more robust prediction on the challenge video and thus, the lane markings would become more stable.

## 5. The Results

Following the steps of the pipeline mentioned above, results are examined on several different video streams of roads with different lane line types. These types consists of lines with different colors: yellow and white. And, in addition, a challange video stream with reflections and shadows on the road is also examined. As a result, with the improvement of a clean-out method, lane line predictions were stable. But, possible improvements are still needed for the challenge video stream.

Solid white lane line video stream.
[![Solid white line](test_videos_output/solidwhite.gif?raw=true)](https://youtu.be/1eIg3_gjjr8)

Solid yellow lane line video stream.
[![Solid yellow line](test_videos_output/solidyellow.gif?raw=true)](https://youtu.be/TybEEiCNZ8E)

The challenge video stream.
[![Challenge](test_videos_output/challenge.gif?raw=true)](https://youtu.be/lpxl0whQdKI)