name: inverse
layout: true
class: center, middle, inverse
---
# From DICOM to a BIDS dataset
### mathiasg@mit.edu

---
name: content
layout: false

## Roadmap

.middle[
.center[
### - [Data and BIDS](#datamanage)
### - [Heudiconv](#heudiconv)
### - [Interactive Conversion](#conversion)
### - [Extra pieces](#extrasteps)
### - [BIDS-Apps](#bidsapps)
]
]

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
.right[*Gorgolewski, K. J. et al. 2016*]


???

result of collaboration from over 5000 researchers.

---
name: heudiconv

## Easiest way to convert to BIDS?

### [Heudiconv](https://github.com/nipy/heudiconv)

<img src="assets/heudiconv.png" width="100%" />

--

- With docker, it's as easy as `docker pull nipy/heudiconv`

--

- Without docker, you'll need these requirements
    - `python 2.7`
    - `nipype`
    - `dcmstack`
    - `dcm2niix`

---
name: conversion
### Sample conversion

  - Start out running heudiconv without any converter, just passing in dicoms.

```
docker run --rm -it -v $PWD:/data nipy/heudiconv -d /data/%s/YAROSLAV_DBIC-TEST1/HEAD_ADVANCED_APPLICATIONS_LIBRARIES_20160824_104430_780000/*/*IMA -s PHANTOM1_3 -f /data/convertall.py -c none -o /data/output
```
--

    - Note: we are specifying `none` as our converter, and we'll be using the `convertall.py` boilerplate heuristic.
---
### Sample conversion

  - Once run, you should now have a directory with your subject, and a sub-directory `info`.

  - You can see a `dicominfo.txt` - we'll be using the information here to convert to a file structure (BIDS)

--

  - The full specifications for BIDS can be found [here](http://bids.neuroimaging.io/bids_spec1.0.1.pdf)

---
### The heuristic file

```python
import os


def create_key(template, outtype=('nii.gz',), annotation_classes=None):
    if template is None or not template:
        raise ValueError('Template must be a valid format string')
    return template, outtype, annotation_classes


def infotodict(seqinfo):
    """Heuristic evaluator for determining which runs belong where

    allowed template fields - follow python string module:

    item: index within category
    subject: participant id
    seqitem: run number during scanning
    subindex: sub index within group
    """

    data = create_key('run{item:03d}', outtype=('nii.gz',))
    info = {data: []}
    last_run = len(seqinfo)
    for s in seqinfo:
        # TODO: clean it up -- unused stuff laying around
        x, y, sl, nt = (s[6], s[7], s[8], s[9])
        info[data].append(s[2])
    return info
```
---
### Creating keys

- Keys define type of scan

- Let's extract T1, diffusion, and rest

--

```python
def infotodict(seqinfo):
    """Heuristic evaluator for determining which runs belong where

    allowed template fields - follow python string module:

    item: index within category
    subject: participant id
    seqitem: run number during scanning
    subindex: sub index within group
    """
    # paths done in BIDS format
    t1w = create_key('anat/sub-{subject}_T1w')
    dwi = create_key('dwi/sub-{subject}_run-{item:01d}_dwi')
    rest = create_key('func/sub-{subject}_task-rest_run-{item:01d}_bold')

    info = {t1w: [], dwi: [], rest: []}
```
---
### Sequence Info

  - And now for each key, we will look at the `dicominfo.txt` and set a unique criteria that only that scan will meet.

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
