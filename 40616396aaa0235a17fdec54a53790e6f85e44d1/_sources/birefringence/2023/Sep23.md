# September 2023

## September 11<sup>th</sup> - September 15<sup>th</sup>

### Coodinate Systems for Birefringence 

Remember these lines from L1489-1491 in `cpol.cc`?

> `rhat_thisstep[0]=-1.*(res[UZAIRSTEP]-res[0])*yhat[0]`
        `rhat_thisstep[1]=-1.*(res[UZAIRSTEP]-res[0])*yhat[1]`
        `rhat_thisstep[2]=-1.*(zs[UZAIRSTEP]-zs[0])`

At the time I didn't understand the details and they came back to haunt me, so I am going to explain them here. 

The TVector3 `rhat_thisstep` is a vector between two adjacent points (out of many) on a ray path solution from Tx to Rx. The question is: on what coordinate system are its components defined? 

Recall that `yhat` is a horizontal unit vector pointing from the SPICE pulser to the ARA station. Then `yhat[0]` and `yhat[1]` are the x and y components of this vector (for a unit vector these are the cosine and sine of the angle it makes with the x-axis). 

Looking at `cpol.cc` (L235), we see that the station and SPICE pulser coodinates are defined in the **surveyor's** coordinate system (Grid North, Grid East or Northing, Easting, **right?**). Look at the figure below for an illustration.


```{image} ../images/birefringence_coordinates.png
:alt: birefringence_coordinates
:class: bg-primary mb-1
:width: 750px
:align: center
```
<br>

Now the most important part. Our principal axes n<sub>$\alpha$</sub>, n<sub>$\beta$</sub>, and n<sub>$\gamma$</sub> are defined so that n<sub>$\alpha$</sub> is aligned with the direction of the ice flow (N36° 46'23"W in northing-easting coordinates) and n<sub>$\alpha$</sub> is aligned with the vertical direction. 

When figuring out how the ray intersects the indicatrix at a given point, we need the angles that `rhat_thisstep` makes with respect to a coordinate system having the x-axis parallel to the iceflow. This (and getting the polarization angles of the two rays in this coordinate system) is done by Amy in `getDeltaN()` using the variable `angle_iceflow` defined in northing-easting coordinates.

**This will need to be adapted in my implementation.** I will be doing single-station simulations where the coordinate system is station-centric and rotated so that the x-axis is already aligned with the ice flow. **I could then let `angle_iceflow = 0` in this case, since `yhat` (now a vector from the interaction vertex to the station) is already defined in the same coordinates as the principal axes of the indicatrix.**
