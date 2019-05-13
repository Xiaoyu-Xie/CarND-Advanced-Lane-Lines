## Advanced Lane Finding
[![Udacity - Self-Driving Car NanoDegree](https://s3.amazonaws.com/udacity-sdc/github/shield-carnd.svg)](http://www.udacity.com/drive)


The goal of this project is to write a software pipeline to identify the lane boundaries in a video collection from on road vehicle. Lightness and curvature issues found in previous “Lane Finding” project shall be resolved through different color spaces and perspective transform.

Objective of the Project
---

The goals / steps of this project are the following:

* Compute the camera calibration matrix and distortion coefficients given a set of chessboard images.
* Apply a distortion correction to raw images.
* Use color transforms, gradients, etc., to create a thresholded binary image.
* Apply a perspective transform to rectify binary image ("birds-eye view").
* Detect lane pixels and fit to find the lane boundary.
* Determine the curvature of the lane and vehicle position with respect to center.
* Warp the detected lane boundaries back onto the original image.
* Output visual display of the lane boundaries and numerical estimation of lane curvature and vehicle position.

Code was written in the online workspace and saved in the /My_Code/Advanced_Lane_Lines.ipynb. It also depicts the functionality of each function ans shows intermediate steps for final output.

The output for test images are saved in /output_images folder and videos are saved in /output_videos folder.

[//]: # (Image References)


[image1]: ./Write_Up_Images/Original_Image.jpg "Original"
[image2]: ./Write_Up_Images/Undistored_Image.jpg "Undistorted"
[image3]: ./Write_Up_Images/Original_Image_Lane.jpg "Original"
[image4]: ./Write_Up_Images/Warped_Image.jpg "Warped"
[image5]: ./Write_Up_Images/Sliding_Window.jpg "Sliding Window"
[image6]: ./Write_Up_Images/Sliding_Window_Marked_Region.jpg "Sliding Window with Marked Region"
[image7]: ./Write_Up_Images/Marked_Region_on_Actual_Figure.jpg "Sliding Window on Actual Figure"
[image8]: ./Write_Up_Images/Filtered_Image_Lane.jpg "Filtered Image Lane"

## Pipeline (Rubric Points)
In this section, each rubric point will be individually considered and elabrated.

*Camera Calibration*
---
The funciton `calibrate_camera` reads in all chessbord imaged taken from different angles and returns calibration and distortion coeffictients. Then, based on those coefficion, the code is able to undistort the image. Below shows a example of a image before and after undistortion process.

Original Image:
![alt-text-1][image1] 
Undistorted Image:
![alt-text-2][image2] 

*Apply Different Thresholds and Create Useable Binary Image*
---
This is the most time consuming section, because it needs carefully calibrating different threholds in different color spacen and gradients. Also the quality of calibration directly affects the lane dectection in the following sections.

`abs_sobel_thresh` is created to filter information from x or y direction in the gray image.

`mag_thresh` is created to filter information based on absolute magnittue of gradient in the gray image.

`dir_threshold` is created to filter information based on gradient direction in the gray image.

`filtered_img` is created to filter information with all three above filtering methods combined and different thresholds applied in different color space. Besides, a mask of intereted region is applied to filter out irrelevant information.

Below is a example of orignal image and thresholded image.

Original Image:
![alt-text-1][image3] 
Thresholded Image:
![alt-text-2][image8] 

*Perspective Transform*
---
`perspective_transform` is created to warp the view of image. It requires 4 points manually selected in unwarped image and then find their corresponding position in the warped image. The transformation matrix is given by `cv2.getPerspectiveTransform` and inverse matrix is given by Minv = cv2.getPerspectiveTransform.

Below is a example of orignal image and warped image.

Original Image:
![alt-text-1][image3] 
Warped Image:
![alt-text-2][image4] 

*Identify Lane-Line Pixels and Fit to Polynomial*
---
Two approaches are created in the code to identify lane-line pixels. The first approach, `find_lane_sliding_window`,is to use a sliding window to find out where most pixels point clusters in each window. The second approach, `find_lane_from_prior`, takes advantage of first approach and searchs pixels around fitted curves in the previous frame.

So the first approach is used when curve fitting info is unknown in the previous frame and second approach is used when lane is correctly identified in the previous frame.

As to polynomial fitting, once lane-line pixels are identified, it is easy to use `fit_poly` to fit pixels to a quadratic equation.

Image with fitted lane curve and identified lane-line pixels:
![alt-text-1][image5]
![alt-text-2][image6]

*Radius of Curvature and Position of the Vehicle*
---
`curvature_and_position` is created to calculate the lane curvature and car position based on the fitted quadratic equation.

Curvature of lane in image shown above is：2688.8 m.

Distance of vehicle in image shown above is: 0.0264 m (right).

*Final Image*
---
Final image output is similar to figure shown below:
![alt-text-2][image7] 

*Video*
---
project_video_output and harder_challenge_video_output can be found in .\output_videos folder.

Links to project_video_output: https://youtu.be/VEujYoKxFM0

Links to harder_challenge_video_output: https://youtu.be/VEujYoKxFM0

## Discussion

*Threshold tuning*
---
As talked in the pipline section, the hardest part is to fine tune thresholds for different filtering approaches. Even if I tried with different thresholds combinations, it is still difficult to make the code work perfectly for challenge and harder challenge videos because the lane in the videos are either too bright or too dim.

*Curvy lanes*
---
The algorithm still works for the challenge_video but fails in harder_challenge_video. The harder video has sharper turns in some sequential frames. So I think instead of taking a perspective transform of enire image, we can select a smaller section to do the transform since this video has sharper turns and the length of a lane is shorter than the other vidoes.
