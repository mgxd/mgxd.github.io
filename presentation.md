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

- Lack of data storage/organization standard in neuroimaging community

--

- This can lead to problems:
  - harder to share data (even in same lab)
  - separate tracking of specific metadata when processing dataset
  - no way to automatically validate completeness of dataset
---
## [Brain Imaging Data Structure](http://bids.neuroimaging.io) (or BIDS)
<img src="assets/data2bids.jpg" width="100%" />
---
### So what do I need?

- Full specification can be found [online](http://bids.neuroimaging.io/bids_spec1.0.0.pdf)

--

- [Online validator](http://incf.github.io/bids-validator) can easily check for you!
  - For HPC - can also validate through command line

### Conversion from DICOM to BIDS

  - [Heudiconv](https://github.com/nipy/heudiconv)
---
# Questions?
