### Homework 2 | Johnny Chiu

This homework was a lot more difficult than just applying the RANSAC method to stitch images together. There were a lot of moving components that required debugging and trying out the math and optimization methods that were never introduced unless I'm experienced in utilizing Python.

#### Detect Corners

This step was simple after importing the images from the files. It's a matter of using the library function in OpenCV to detect most of the corners and many of these are bad corners or not corners at all.

![image-20210409234204876](C:\Users\johnn\AppData\Roaming\Typora\typora-user-images\image-20210409234204876.png)

#### AMNS

The second step of the project is the AMNS process. Corner Harris tells us which points in the image are likely to be corners and AMNS helps us pick the stronger corner points as well as pick corner points as feature points that are far away from each other. We first find the local maximum to establish corner points in each 3x3 region, then we look at which corner points are close to each other. A stronger corner point suppresses any nearby corner points and we choose strong points away from each other. The major problem with this section is being able to optimize the algorithm and it takes a long time to debug since the algorithm itself runs a long time.  One needs to be knowledgeable in Python to optimize the algorithm and even with optimization and shrinking the code to one loop with a few lines of code, the computer still takes a long time go through all the corner points.

![image-20210409234010151](C:\Users\johnn\AppData\Roaming\Typora\typora-user-images\image-20210409234010151.png)

#### Feature Descriptors

This step is how we'll able to compare feature points. We create feature descriptors for each feature point. For corner points, creating a feature point takes a 40x40 neighborhood and observes the intensities from the neighborhoods rather than the traditional sense of SIFT descriptors. There is also a Gaussian blur applied to it, and it's normalized as well. It's important for the next step in feature matching.

![image-20210409232721221](C:\Users\johnn\AppData\Roaming\Typora\typora-user-images\image-20210409232721221.png)

#### Feature Matching

We take the feature descriptors from last section and between two images, do a Euclidean distance matching. Whichever two descriptors whose Euclidean distance is the shortest from each other is considered as a possible pair from the feature points in the two images. We reject any matches where the second shortest Euclidean distance is within 80% of the shortest distance. This part is straightforward as it's purely math based and we just need to be careful with the indexing. I also return a Boolean value to check the minimum distance. If the minimum distance is not less than 4, then there are no correct matches between the two images.![set1_12matches](D:\CMSC426\HW2\hw2\images\input\set1_12matches.jpg)

#### RANSAC

Here we compute the best homography matrix through RANSAC. I randomly choose 4 pairs of points 30 times and find the homography matrix of those 4 points and run a for loop against all the pairs and compare the distance between Hp_1 and p_2. Those pairs whose distance is under 4 is considered an inliner and will be added to the set of inliners for that homography matrix which is stored. At the end of the loop, I'll look at which inliner set is the largest and return the homography matrix that made that inliner set. This part is complicated as I needed to adjust the number of times I choose the 4 pairs and there's an issue with utilizing the given functions and there's a lot of math involved in this section in general.

![image-20210409235823621](C:\Users\johnn\AppData\Roaming\Typora\typora-user-images\image-20210409235823621.png)

#### Blending

This part is the most exhausting part of the project. We have to find the border of a large canvas that would fit all of my images that would be stitched together and there's a translation matrix that we need to find as well without any background on how to find this matrix. Furthermore, there's extreme confusion with matrix indices and coordinate points because numpy does not like using the coordinate plan system and OpenCV does not like using matrix indices, so it's chaotic to switch between the two. However, I was able to establish a base image and have all the homography matrices transform the other images into the base image system without too much trouble. I took the height value when I stitched the images together.![set1_panorama](D:\CMSC426\HW2\hw2\images\input\set1_panorama.png)