+++
date = "2017-03-01T19:27:27Z"
title = "Surface Distance Function"
description = "Performing segmentation evaluation with surface distances is a good way of measuring the overall accuracy of the segmentation. This function is written in Python to help with just that!"
topics = ["segmentation"]
tags = ["python", "segmentation","evaluation","surface distance"]
draft = true

+++

## Background
Recently, I have been doing a **lot** of segmentation evaluation. Traditionally, such verification is done by comparing the segmentation you have with the real version, called a "ground truth" (GT). There are a few different calculations that can be done (there'll be a longer post on just that) and 'surface distance' calculations are one of them.

## Method
For this calculation, we need to be able to find the outline of the segmentation and compare it to the outline of the GT. We can then take measurements of how far each segmentation pixel is from its corresponding pixel in the GT outline.

Now I've seen MATLAB code that can do this, though often its not entirely accurate. Plus I wanted to do this calculation on-the-fly as part of my program which was written in Python. So I came up with this:


{{< highlight python >}}
import numpy as np
from scipy.ndimage import morphology

def surfd(input1, input2, sampling=1, connectivity=1):
    
    input_1 = np.atleast_1d(input1.astype(np.bool))
    input_2 = np.atleast_1d(input2.astype(np.bool))
    

    conn = morphology.generate_binary_structure(input_1.ndim, connectivity)

    input1_border = input_1 - morphology.binary_erosion(input_1, conn)
    input2_border = input_2 - morphology.binary_erosion(input_2, conn)

    
    dta = morphology.distance_transform_edt(~input1_border,sampling)
    dtb = morphology.distance_transform_edt(~input2_border,sampling)
    
    sds = np.concatenate([np.ravel(dta[input2_border!=0]), np.ravel(dtb[input1_border!=0])])
       
    
    return sds
{{< /highlight >}}
