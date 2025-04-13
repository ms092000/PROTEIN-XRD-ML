# PROTEIN-XRD-ML

This is the root directory for all the codes, results and explanations to be provided in further sub folders.

In this project I built an Deep Learning based model to automatically detect spots from XRD data from Protein Single Crystals.
Often owing to the very complex structure of Protein molecules, their XRD data lacks significant redundancy and lower symmetry points 
with good I/sigma ratio. In this case it takes long time for any software to detect and index all the spots on a single XRD image as 
often multiple high resolution spots have weak intensities and each image need multiple iterations of processing to complete. 

I developed a DL based procedure based on U-net architecture with spatial and channel wise attention to allow for high throughput image 
processing which can significantly reduce the time needed for processing data. Some of the major challenges applying DL in such a problem is the
the large class imbalance between pixels on image which classify as bragg relfections (0.5% of pixels) and pixels which constitute the Background
(99.5% pixels). Such a class imbalance makes the model strongly biased towards True Negative type errors. Another challenge is in training the model
itself and choosing a proper loss function which can caputure the local features of the image which vary strongly along the radially outward direction
in this case. The strong radial dependence of intensity often makes spots on towards the boundary be classified as background( which is also a reason 
why multiple refinement iterations of image is needed even under classical image processing techniques). However I devised a fast and efficient procedure 
to make a local distance map for each spot where distance of each pixel in the background gets a manhattan type distance of how far the point lies from 
the closest spot pixel on the image. Under a brute force type search this is is exteremly time and memory consuming as this procedure must be perforwed 
for all background pixels on the image O(N^2) and for each pixel we need to calculate its distance M spots. So, total time needed would be of the order
O(M*N^2) where N=3072 and M~N/5. I used combination of convolution and bitwise operations to efficently compute this distance map in O(2*N^2*k*(M/N^2)^0.5)
where k=3(kernel size), so more simple it takes time of the order O(M^0.5*N). Then I use an exponentially decreasing gaussian type weight centered at the 
boundary of each bragg refelction pixel which penalises the model strongly on getting the boundary between refelctions and background wrong across the entire
image.

Further I used some other L2 regularization and Batch Normalisation for efficient learning and could train an Neural Network which would effectively identify
over 99% of all bragg reflection on the image while preserving the morphology of the spot essential for understanding the quality of the crystal. 

I further want to integrate indexing algorithms with this to make a software package for efficent post processing of XRD data.
