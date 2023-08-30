# August 23

## August 21<sup>st</sup> - August 25<sup>th</sup>

### How to Calculate Time Differences 

Amy's [birefringence paper](https://arxiv.org/abs/2110.09015) shows time differences between the two ways emitted from the SPICE transmitter pulsers from different heights arriving at each station in Fig. 9.

Fig. 9 -and all other figures- is produced by `cpol.cc` in ~L4484 and called `h9`. Then, this is filled with information from TGraph `g_sumphase`. **Time differences** are contained in `vtimediff` inside `g_sumphase` as seen in L3540.

The vector `vtimediff` has dimension of number of stations and it is filled in L2357 with:

> `vtimediff[i].push_back(sumphase/(PI)*1./freq*1.E9)`

with time differences in ns. Then double `sumphase` is calculated in L2214 as:

> `sumphase=deltantimeslength_alongpath*PI/TMath::C()*freq`

where `freq` is defined in L185 as 160 MHz (**also read in `if-statement` in L201?**), `TMath::C()` is the speed of light in vacuum (**Why not use `CLIGHT` in L39?**). We keep going with `deltantimeslength_alongpath` being calculated in L2159 as:

> `deltantimeslength_alongpath+=deltan_alongpath*length*notflipped`

where length is given in L1560 by:

> `double length=rhat_thisstep.Mag()`

TVector3 `rhat_thisstep` is filled in L1489-1491 as follows:

> `rhat_thisstep[0]=-1.*(res[UZAIRSTEP]-res[0])*yhat[0]`
        `rhat_thisstep[1]=-1.*(res[UZAIRSTEP]-res[0])*yhat[1]`
        `rhat_thisstep[2]=-1.*(zs[UZAIRSTEP]-zs[0])`

Here vector `res` and `zs` are y- and z- coordinates in a ray and are filled in L1394-1420 by `GetFullDirectRayPath(z0, x1, z1, paramsd[3], res, zs)`. The values `z0`, `x1`, `z1` are the positions of the SPICE pulser and ARA station, and `paramsd[3]` is filled by `GetDirectRayPar(z0,x1,z1)`. Variable `paramsd[3]` contains something called `lvalueD` regarding some L parameter (**What is this?**). Now TVector3 `yhat` is the horizontal direction from the pulser to the station according to L1470 later turned to a unit vector. **Note: the magnitude of `rhat_thisstep` is set to 1 after `length` is calculated**.

Now let's go back to:

> `deltantimeslength_alongpath+=deltan_alongpath*length*notflipped`

the value `notflipped` is given by the function `Flipped(theta_e1, theta_e1_start)` in L2156(**What is this telling us?**) where `theta_e1` is given by `getManyAnglesontheClock()` in L2130 (**This deals with rotations so we could ignore flipping for work on time differences, right?**). Finally, we'll be looking at `deltan_alongpath` which is calculated in L1576 as:

> `deltan_alongpath=getDeltaN(BIAXIAL,nvec_thisstep,rhat_thisstep,angle_iceflow,n_e1,n_e2,p_e1,p_e2)`

where BIAXIAL is a configuration value specified by the user (**I should add this to `Settings.cc` in AraSim**), `nvec_thisstep` contains the values defining the indicatrix, `angle_iceflow` is defined in L273 by `const double angle_iceflow=(36.+ (46./60.) + (23./3600.) + 90.)/DEGRAD`, and variables `n_e1`, `n_e2`, `p_e1`, `p_e2` are the indices of refraction felt by the two rays and their polarizations. We need to go back and look at `nvec_thisstep` separately.

Vector `nvec_thisstep` has size 3 and is filled by `gn1`, `gn2`, and `gn3` in L1483-1485. Now, for instance, `gn1` is a TGraph of size `n1vec.size()` filled with `vdepths_n1[0]` and `n1vec[0]`. Finally, vector `n1vec` is filled between lines L649-783 by loading data from `n1.txt` after some data-conditioning and smoothing.

**This is it for tracing how time differences are calculated, right?**

Next, we have to look at some variables used for loops that I have ignored so far, namely `UZAIRSTEP` and `istep`.

Recall that the time differences are in vector `vtimediff`. This is in L2357 so I finished reading the code up to this point.
