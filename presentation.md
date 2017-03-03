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

- MRIQC
- fMRIprep
mn
<img src="assets/t12mni.svg" width="100%" />

???

mriqc - runs quality control measures on both structural and functional across either individual or group - outputs reports
fMRIprep - runs basic preprocessing with reports generated outlining how import steps affected the data
ALL RUN BECAUSE OF FACILITATION BY BIDS STRUCTURE
---
## Easiest way to convert to BIDS?

- Convert current files

or..
--
- Convert from dicoms!
<img src="assets/heudiconv.png" width="100%" />
--
  - [Heudiconv](https://github.com/nipy/heudiconv)
    - `Python 2`
    - `Nipype`
    - `dcmstack`
    - `dcm2niix`

  - Or if you have docker:
    - `docker pull nipy/heudiconv`
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
# Questions?
