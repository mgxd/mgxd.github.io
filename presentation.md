name: inverse
layout: true
class: center, middle, inverse
---
# From DICOM to a BIDS dataset
### mathiasg@mit.edu
---
name: content
layout: false
class: center, middle
## Roadmap
#### - [Data and BIDS](#datamanage)
#### - [Heudiconv](#heudiconv)
#### - [Extra pieces](#extrasteps)
#### - [BIDS-Apps](#bidsapps)

---
name: datamanage

### Data management

- Large amounts of variety of scans acquired during acquisition
 - Anatomical
 - Functional
 - Diffusion
 - Field maps

--
- Lack of data storage/organization standard in neuroimaging community

--

- This can lead to problems:
  - harder to share data (even in same lab)
  - separate tracking of specific metadata when processing dataset
  - is my dataset missing anything?
---
## [Brain Imaging Data Structure](http://bids.neuroimaging.io) (or BIDS)

<img src="assets/data2bids.jpg" width="100%" />
<p align="right">*Gorgolewski, K. J. et al. 2016*</p>

???

result of collaboration from over 5000 researchers.

---
name: heudiconv

## Easiest way to convert to BIDS?
## [Heudiconv](https://github.com/nipy/heudiconv)
<img src="assets/heudiconv.png" width="100%" />

--

- With docker, it's as easy as `docker pull nipy/heudiconv`

--
- Without docker, you'll need these requirements
    - `python 2`
    - `nipype`
    - `dcmstack`
    - `dcm2niix`

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
name: extrasteps

### Is it BIDS yet?

- Let's check:
  - [In-browser validator](http://incf.github.io/bids-validator)
  - Also can validate through command line

--



  - A change to `heudiconv` will be coming soon that fixes most, if not all, of these problems.
---
name: inverse
layout: true
class: center, inverse
---
### Now what?

<img src="assets/bids-apps.png" width="100%" />

???

over 20 apps!
---
name: bidsapps

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
