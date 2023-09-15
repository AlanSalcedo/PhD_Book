# September 2023

---

### September 4<sup>th</sup> - September 8<sup>th</sup>

1. **ARA Birefringence:** Tested `Time_Diff_TwoRays()` on Justin's AraSim branch for SPICE pulser simulations. Now I am getting results consistent with Amy's birefringence paper. At first, I wasn't getting similar times so I tried adding Uzair's ray tracing and Amy's index of refraction profile, as well as some debugging. I learned a potential problem regarding coordinates systems.

---

### September 11<sup>th</sup> - September 15<sup>th</sup>

1. **ARA 5-Station Analysis:** Finished A1 simulations (finally!) by changing to Chiba's antenna gain model and reducing power threshold on noise-only simulations. Went ahead and did A4 simulations too. 
2. **ARA Birefringence:**  Went through `Report.cc::Connect_Interaction_Detector_V2()` and wrote notes on how to show time differences into waveforms. I just did this in a section for SPICE Pulser events which will serve me for testing results. Later I should implement it for all event types. 

3. **GENETIS:** Jason and I profiled AraSim. I am concerned about the conditions of the simulation for a proper profiling. For example, if all neutrinos trigger our detector we'll have more simulation time on FFTs in comparison to having no triggers (?).

4. **GENETIS:** Tried compiling PueoSim and sadly failed. 
