# August 2023

## August 28<sup>th</sup> - September 1<sup>st</sup>

### GENETIS Building Team: Adding Thickness and ABS Plastic to Straightened Bicone Model

The GENETIS team produced an `.stl` file for a bicone antenna with curved sides that later got straightened. This file only produces the shape of the antenna without thickness. My task was to add thickness to it and define its material as ABS plastic so that we can 3D print it at CDME later.

Our group had done antenna modeling using XFdtd in the past. Typically, one can create some shapes and add properties like thickness in there but the main functionality of Xfdtd is to simulate this antenna's response to electromagnetic fields. I tried using XFdtd to give thickness to the model but the capability wasn't active as it is for shapes that you can create internally in XF. I gave up using XF immediately.

People from OSU-CART use Fusion 360 for 3D models which can then be printed, so they recommended me to use it as well.

In Fusion 360, you can upload the `.stl` file, click under the "mesh" tab, click on "prepare" and "Generate Face Groups", select all the parts of our model, and generate face groups with the default values provided. After this, you can click under "modify" then "convert mesh" and under "method" make it prismatic. The main parts of the antenna's body now combine to produce smoother and larger pieces. Then right click on each of these for the "thicken" option to appear. Once you have added the thickness, you can right click on each "MeshBody" under "Bodies" to access "Physical Material", there you can made each piece of the desired material. I just made all of them ABS plastic.

Here's how it looks:

```{image} ../images/straightened_bicone.png
:alt: straightened_bicone
:class: bg-primary mb-1
:width: 500px
:align: center
```
