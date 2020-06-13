# Schrodinger's Cat - Dataset for Monocular Depth Estimation and Segmentation

This custom prepared dataset contains overlayed, mask and depth images of Home interiors and cats as background and foreground respectively. This dataset is useful in deep learning model training and prediction of mask and depth maps. 

Mask segmentation is pixel-wise mask of our foreground object (i.e cat in our dataset). Depth maps contain information about the distance between the surface of objects from a given viewpoint.

Link to the Dataset - [MonocularDepth_Mask_Dataset](https://drive.google.com/drive/folders/1ACsG-epUmRCJ0zaKIAGGc5DzeG3SSY65)

## Custom Dataset

* **Background Images (bg)** - Square shaped "Scene" images.
* **Foreground Images (fg)** - Images of objects of interest.
* **Overlayed Images (bg_fg)** - Images of foreground objects overlayed on background images at random locations. Foreground images are also flipped and overlayed on the background.
* **Mask Images (mask)** - Mask of the overlayed foreground-background images.
* **Depth Images (depth)** - Depth maps of foreground-background images.

## Dataset Overview

Below are some of the examples of images, number of images of each directory.

### Background (bg)

This directory contains images of Home interiors. These are the background images. 

* Total number of images = 100
* Image size = 192 x 192 resolution


![](https://github.com/Shashank-Holla/TSAI-EVA4/blob/master/Session14_RCNN%26DenseDepth/results/bg.jpg)


### Foreground (fg)

This directory contains images of cats. These are the foreground images. 

* Total number of images= 100
* Image size = X x 80 (Foreground images have fixed height of 80 while the width is depends on the aspect ratio of the foreground object).
* Includes alpha channel to maintain transparency details

![](https://github.com/Shashank-Holla/TSAI-EVA4/blob/master/Session14_RCNN%26DenseDepth/results/fg.png)


### Overlayed images (bg_fg)

This directory contains the resultant image of the foreground overlayed on the background. 

* Total number of images = 400K
* Image size = 192 x 192 resolution

![](https://github.com/Shashank-Holla/TSAI-EVA4/blob/master/Session14_RCNN%26DenseDepth/results/ov.jpg)


### Mask (mask)

This directory contains the mask of the object of the corresponding overlayed image. 

* Total number of images = 400K
* Image size = 192 x 192 resolution

![](https://github.com/Shashank-Holla/TSAI-EVA4/blob/master/Session14_RCNN%26DenseDepth/results/ma.jpg)


### Depth (depth)

This directory contains the depth estimate maps of the corresponding overlayed image. 

* Total number of images = 400K
* Image size = 192 x 192 resolution

![](https://github.com/Shashank-Holla/TSAI-EVA4/blob/master/Session14_RCNN%26DenseDepth/results/de.jpg)


## Dataset Statistics

Below are the stats gathered for the dataset. Foreground images are scaled to a height of 80 while maintaining the aspect ratio.

| Image Type | No of Images | Number of channels | Image Dimension | Total Imageset Size | Mean  | Std. Dev |
|------------|--------------|--------------------|-----------------|---------------------|-------|----------|
| fg         | 100          | 4 (includes alpha) |  192 * 192      |                     |       |          |
| bg         | 100          |       3            |  x * 80         |                     |(0.6897, 0.6509, 0.6079)      | (0.1890, 0.2089, 0.2345)         |
| fg_bg      | 400000       |       3            |  192 * 192      | 6 GB                |(0.6808, 0.6413, 0.5983) | (0.1943, 0.2126, 0.2364)  |
| mask       | 400000       |       1            |  192 * 192      | 866 MB              |0.0614 |0.2373    |
| depth      | 400000       |       1            |  192 * 192      | 1 GB                |0.4998       |0.2730          |





## Dataset creation

### Background and Foreground images (bg and fg)

* 100 images of home interiors are collected from the web. The raw background images are cropped while maintaining 1:1 aspect ratio.     Further the background images are rescaled to 192 X 192 dimension.

![](https://github.com/Shashank-Holla/TSAI-EVA4/blob/master/Session14_RCNN%26DenseDepth/results/background_images.jpg)

* 100 images of cats are collected from the web. For the image overlay, the foreground images are scaled to a fixed height of 80 while maintaining the image's aspect ratio.

![](https://github.com/Shashank-Holla/TSAI-EVA4/blob/master/Session14_RCNN%26DenseDepth/results/foreground_images.jpg)



### Creation of foreground images with transparency

For a clean overlay of the foreground image on background, the foreground images are required to have a transparent background. GIMP (GNU Image Manipulation Program) is used for this purpose. Steps are-

* For the image in GIMP, add Alpha channel to the image ( Layer -> Transparency -> Add Alpha channel ). Alpha channel value of 0 represents full transparency while the maximum value indicates opaqueness.

* Select the object in the image using Free Hand tool. Later, invert the selection and delete the background.

* Save the modified image as .png file to retain the alpha channel.


### Overlay of the Foreground image on the background image and creation of variants

Variants of the Foreground-Background images are created by overlaying the foreground image on the background image at random locations. Each of the foreground images are also horizontally flipped and overlayed on the background image to create more variants.

* Random location `(x_offset, y_offset)` is identified on the background image to overlay the foreground image. The offset locations `x_offset` and `y_offset` are ensured to be within the range of `(background_width - foreground_width)` and `(background_height - foreground_height)` respectively.

* Based on the alpha channel of the foreground image, pixel values of the foreground image is added to background images at the identified offset location.

### Mask creation for the overlayed image

* Black canvas of single channel of the size of the background image is prepared.

* The foreground image is converted to greyscale (single channel) and all the pixel values of the foreground is set to 255 (white).

* Single channel foreground image is overlayed on the background's black canvas using the alpha channel of the foreground image.

The preparation time for the overlayed and its mask images is about 32 min (11 min image creation + 20 min for Folder zip and copy).

### Creation of Depth images

* Depth estimate maps are created using [DenseDepth](https://github.com/ialhashim/DenseDepth) repository. Pre-trained NYU model is used to create the depth images.

* DenseDepth model's output is rescaled using min and max value of the output to obtain greater clarity between the depths. The depth images are saved as greyscale images.

The preparation time for dense depth images is about 5 hours (21 seconds per batch of 384 images- Read (Read from file, Image resize, stack of images)=9 sec, Process (Depth estimate model) = 10 sec, Write (write to disk) = 0.33 sec) 

