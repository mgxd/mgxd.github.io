name: inverse
layout: true
class: center, middle, inverse
---
# Data Standards (BIDS) and conversion
### mathiasg@mit.edu
---
layout: false
### Neuroimaging data is BIG

- MRI as a research tool has been used for over two decades.

--

- Different types of MRI scans within studies
  - Anatomical
  - Diffusion
  - Functional
---
### Managing this data

- Lack of data storage/organization standard in neuroimaging community.

--

- This can lead to problems:
  - harder to share data (even in same lab)
  - separate tracking of specific metadata when processing dataset
  - no way to automatically validate completeness of dataset
---
## [Brain Imaging Data Structure](http://bids.neuroimaging.io) (or BIDS)

<img src="assets/data2bids.jpg" width="100%" />
*Gorgolewski, K. J. et al. 2016*
???

who came up with this? over 5000 researchers.
---
### So what do I need?

- Full specification can be found [online](http://bids.neuroimaging.io/bids_spec1.0.0.pdf)

--

- [In-browser validator](http://incf.github.io/bids-validator) can easily check for you!
  - For HPC - can also validate through command line

???

run validator showing warnings/errors
---
### Now what?

<img src="assets/bids-apps.png" width="100%" />

???

over 20 apps!
---
### BIDS-Apps

- Each app self-contained inside own docker image
  - no hassle of installing/updating new versions
--

- [MRIQC](https://github.com/poldracklab/mriqc)

- [fMRIprep](https://github.com/poldracklab/fmriprep)
--

<object type="image/svg+xml" data="assets/t12mni.svg" width="100%">
</object>

???

mriqc - runs quality control measures on both structural and functional across either individual or group - outputs reports
fMRIprep - runs basic preprocessing with reports generated outlining how import steps affected the data
ALL RUN BECAUSE OF FACILITATION BY BIDS STRUCTURE
---
## Easiest way to convert to BIDS?

- Convert current files or..
--
<img src="assets/heudiconv.png" width="100%" />

<p>[Heudiconv](https://github.com/nipy/heudiconv)</p>
--

    - `Python 2`
    - `Nipype`
    - `dcmstack`
    - `dcm2niix`

Or if you have docker: `docker pull nipy/heudiconv`
---
### Sample conversion

  - Start out running heudiconv without any converter, just passing in dicoms.
  ```
  heudiconv -d $DICOMPATH/%s/*.dcm -f convertall.py -c none -s $YOUR_SUBJECT
  ```
--

    - Note: we are not designating a converter yet, but we'll be using the `convertall.py` found in the sample heuristics.
---
### Check the generated info!

  - Once run, you should now have a directory with your subject, and a sub-directory `info`.

    - In there, you can see a `dicominfo.txt` - we'll be using the information here to convert to a file structure (BIDS)
--

  <img src="assets/dicominfo.png" width="100%" />
---
### Put the guts in your heuristic!
  - For this example, we want to extract T1, diffusion, and the face matching task

--

  - First, define the keys
  ```python
  t1 = create_key('anat/sub-{subject}_T1w')
  dwi = create_key('dwi/sub-{subject}_acq-{acq}_dwi')
  facematch=create_key('func/sub-{subject}_task-facematch_run-{item:02d}_bold')
  info = {t1:[], dwi:[], facematch:[]}
  ```
--

  - And now for each key, look at the `dicominfo.txt` and set a unique criteria that only that series will meet. For example:
--

    ```python
    for idx, s in enumerate(seqinfo):
        x,y,sl,nt = (s[6], s[7], s[8], s[9])
        if (sl == 176) and (nt ==1) and ('T1_MPRAGE' in s[12]):
            info[t1].append(s[2])
    ```
---
### Test it out!
  - After setting rules for each key, re-run `heudiconv` with some added parameters:

--

    - `-c dcm2niix` - this sets the converter that will be used (dcm2niix is recommended)
    - `-f my_heuristic.py` - this tells heudiconv to look for the specific keys you defined when converting
    - `-b` - this flag tells `dcm2niix` to output BIDS metadata `json` files.
--

  - Something missing? Double check your `heuristic.py` and `dicominfo.txt`!
---
### Is it BIDS yet?
  - 90% there, but (currently) you will have to fix any errors from the validator.

<h2>BUT</h2>

  - A change to `heudiconv` will be coming soon that fixes most, if not all, of these problems.
---
name: inverse
layout: true
class: center, inverse
---
# <u>Thanks</u>


<h3> Satra Ghosh </h3>
<br>
<h3> Chris Gorgolewski </h3>
<br>
<h3> Heudiconv contributors </h3>
---
name: inverse
layout: true
class: center, middle, inverse
---
# Questions?
