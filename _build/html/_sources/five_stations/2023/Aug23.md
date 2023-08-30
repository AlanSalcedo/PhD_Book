# August 2023

## August 28<sup>th</sup> - September 1<sup>st</sup>

### A1 Simulations

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
