# August 2023

---

## Aug. 21st - Aug. 25th

### Summary

1.  Worked through `cpol.cc` to find how time differences in rays are calculated. I think that I have studied this completely. Notes about this are written on the Birefringence section below.  

2.  Studied Maya's birefringence branch that seems to take parts of `cpol.cc` and convert them into functions defined in `birefringence.cc`.

3.  Added `Birefringence.cc` and `Birefringence.hh` into AraSim. I've only put two functions in there so far: `Read_Indicatrix_par()` and `Smooth_Indicatrix_par()` which are tweaked functions modularized from `cpol.cc` by Maya. Also, added `/data/birefringence` folder with the n1, n2, n3 data. See changes on this [GitHub PR](https://github.com/AlanSalcedo/AraSim/tree/Birefringence_Alan_New). 

--- 


### Birefringence: How time differences are calculated 


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

---

## Aug. 28th - Sep. 1st

## Summary

1. **ARA 5-Station Analysis:** Updated A1 simulations to use Chiba's antenna model and put files on [GitHub](https://github.com/AlanSalcedo/A1_simulations) to facilitate their use by other people.

2. **GENETIS Building:** Made straightened bicone model 2.5 mm thick and made of ABS plastic using Fusion 360 by recommendation of people from CART.

---

### ARA 5-Station Analysis: A1 Simulations

For our 5-station analysis, we need simulations for detecting events at each stationusing the most updated signal-chain gain responses, noise models, channel masking, antenna positions and corresponding cable delays. I volunteered to help Mohammad Seikh (from U. Kansas) running simulations for A1.

Before being able to run simulations on Condor, I needed to modify AraSim to corresctly simulate A1 having an ATRI board in its DAQ box to ensure data digitization in simulations is similar to current real data. All changes are documented [here](https://aradocs.wipac.wisc.edu/cgi-bin/DocDB/ShowDocument?docid=2869) and in this [GitHub PR](https://github.com/ara-software/AraSim/pull/84).

Now running simulations is fairly straightforward using the following [directory](https://github.com/AlanSalcedo/A1_simulations). Just `git clone` it into your Cobalt account, ssh into `username@submitter.icecube.wisc.edu`, copy any of the `batch_run_A1_%key` directories into `/scratch/username` (`%key = signal` performs signal + noise simulations, `%key = noise` performs noise only simulations, and `%key = sim_test` is made to test smaller runs before sending larger ones), cd into `scratch/username/batch_run_A1_%key` and run dagmans with `$ condor_submit_dag A1.dag`.

Notes:

1. Of course, `username` is your username on Cobalt.
2. You can learn the basics about condor [here](https://wiki.icecube.wisc.edu/index.php/Condor/Basics) and dagmans [here](https://wiki.icecube.wisc.edu/index.php/Condor/DAGMan).
3. No one talks about how to cancel jobs on Condor which annoyed me! So here it is: `$ condor_rm job_id` where `job_id` can be found with `condor_q -nobatch`. To cancel all the jobs sent through dagman, just kill the `.dag` job.
4. Read through `A1.dag`, `ARA_job.sub`, and `ARA_job.sh` in that order to understand how they interact, then modify them to your needs since many places work for how I set up my directories.
5. If you need to create new dagmans, I added a python code `dagman_create.py` that you can modify and run with `$ python dagman_create.py` 
6. Finally, be sure to create the `output` and `data_analysis` directories called in `ARA_job.sh` before running because Condor is a bit dumb and may not store the data otherwise.

### GENETIS Building Team: Adding Thickness and ABS Plastic to Straightened Bicone Model

The GENETIS team produced an `.stl` file for a bicone antenna with curved sides that later got straightened. This file only produces the shape of the antenna without thickness. My task was to add thickness to it and define its material as ABS plastic so that we can 3D print it at CDME later.

Our group had done antenna modeling using XFdtd in the past. Typically, one can create some shapes and add properties like thickness in there but the main functionality of Xfdtd is to simulate this antenna's response to electromagnetic fields. I tried using XFdtd to give thickness to the model but the capability wasn't active as it is for shapes that you can create internally in XF. I gave up using XF immediately.

People from OSU-CART use Fusion 360 for 3D models which can then be printed, so they recommended me to use it as well. 

In Fusion 360, you can `upload` the `.stl` file, click under the "mesh" tab, click on "prepare" and "Generate Face Groups", select all the parts of our model, and generate face groups with the default values provided. After this, you can click under "modify" then "convert mesh" and under "method" make it prismatic. The main parts of the antenna's body now combine to produce smoother and larger pieces. Then right click on each of these for the "thicken" option to appear. Once you have added the thickness, you can right click on each "MeshBody" under "Bodies" to access "Physical Material", there you can made each piece of the desired material. I just madd all of them ABS plastic.
