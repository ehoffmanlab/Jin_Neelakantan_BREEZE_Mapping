# Jin_Neelakantan_BREEZE_Mapping

All relevant files to the BREEZE-mapping pipeline may be found at the following Zenodo page:

[![DOI](https://zenodo.org/badge/DOI/10.5281/zenodo.7996403.svg)](https://doi.org/10.5281/zenodo.7996403)

Contains relevant DOI information and README instructions for use of the BREEZE-mapping pipeline.

This code was used in **Weinschutz Mendes et al. (2023)** to analyze brain activity and volume phenotypes in zebrafish mutants of autism risk genes and is described in detail in **Jin, Neelakantan et al. (2023)**. In addition to brain activity and volume, this method describes how to quantify whole-brain volume, activity, and other cellular markers in larval zebrafish and includes four main sections: I) Pre-Processing II) Registration III) Quantification IV) Visualization. Please see **Jin, Neelakantan et al. (2023)** for a detailed description of each step in the protocol.

Note: The following set of code has been designed for use with the Slurm Workload Manager (Simple Linux Utility for Research Management). It should be run on a Slurm-equipped high-performance computing cluster. 

**Information:**

- This protocol was developed specifically for confocal images of larval zebrafish brains at 6 days post fertilization acquired in the dorsal to ventral direction. For other stages of development, a different atlas and standard reference must be used. 
- The protocol’s four main steps use code found in the **bin** folder. See **Figure 1** in **Jin, Neelakantan, et al.** **(2023)** for details.

**I) Pre-Processing**

**Required User Input Files:**

1. Microscope files (*.lif*)

**To run the Pre-Processing steps:**

1. To convert images to max projection images (*.tif*) and nrrd files (*.nrrd*), run ***max\_nrrd\_2.ijm*** (two channels) or ***max\_nrrd\_3.ijm*** (three channels) in ImageJ.
1. Run ***summary\_2.js*** (two channels**)** or ***summary\_3.js*** (three channels) in Adobe Illustrator to generate a summary of all images and to check the quality of the images.
1. Convert *.nrrd* files to *\*optfixed.nii.gz* files for registration using BioImage Suite Web App ([https://bioimagesuiteweb.github.io/webapp/viewer.html#](https://bioimagesuiteweb.github.io/webapp/viewer.html)).

**Pre-processing Output Files:**

1. Nrrd files (*.nrrd*)
1. Max projection images (*.tif*)
1. Image summary file (*.ai*)
1. “Fixed” zebrafish images (*\*.optfixed.nii.gz*)

**II) Registration**

**Required User Input Files**

1. “Fixed” zebrafish images (*\*.optfixed.nii.gz*)
1. Parameter file (*nonlinearRegistration.param*)
1. Standard Reference file (*standard\_ref.nii.gz*)
1. Atlas mask (*dis\_atlas\_mask.nii.gz*)
1. Resampled Standard Reference file (*standard\_ref\_rsp.nii.gz*)





**To run the Registration steps:**

1. In terminal, run the script ***reg.sh.***
1. Make sure the following software is available on the HPCC to run these steps: 

*(i)* MATLAB version 2020b or above

*(ii)* BioImage Suite WebApp (and companion command line tools biswebnode)

*(iii)* FSL

*(iv)* NodeJS

*(v)* Analysis of Functional Neuroimaging (AFNI) (NIH)

1. The script ***reg.sh*** will perform registration, create divided files, and calculate Jacobian values for each image.

**Registration Output Files:**

1. Registered files (*r\_\*optfixed.nii.gz* for C0, C1, C2 markers)
1. Geometric transformation C1 file (*standard\*.bisxform*)
1. Divided files (*r\_\*C0divC1.nii.gz*, *r\_\*C2divC1.nii.gz* )
1. Jacobian files (*mask\_ja\_\*.nii.gz* and *ja\_\*.nii.gz*)

**III) Quantification**

**Required User Input Files**

1. Divided files (*r\_\*C0divC1.nii.gz, r\_\*C2divC1.nii.gz* )
1. Jacobian files (*mask\_ja\_\*.nii.gz* and *ja\_\*.nii.gz*)
1. 8 region atlas (*Zbrain\_atlas\_8region.nii.gz*)
1. Resampled 8 region atlas (*Zbrain\_atlas\_8region\_rsp.nii.gz*)
1. 4 region atlas (Zbrain\_atlas\_4region.nii.gz)
1. Resampled 4 region atlas (*Zbrain\_atlas\_4region\_rsp.nii.gz*)
1. 149 region atlas (*Zbrain\_atlas\_149region.nii.gz*)

**To run the Quantification steps:**

1. In terminal, run ***roi.sh*** followed by the atlas region number (4,8, or 149) to quantify cell-specific marker fluorescence intensity calculations.
1. In terminal, run ***vol.sh*** followed by the atlas region number (4 or 8) to quantify brain volume (Jacobian) calculations.

Note: Resampled atlases are used for the Jacobian files due to the difference in dimensions (non-resampled- X:621 Y:840 Z:138 vs. resampled- X:248 Y:335 Z:138).

**Quantification Output Files:**

1. Brain activity or cell-specific marker spreadsheet (\*\_*C0divC1\_combined.csv*, \*\_*C2divC1\_combined.csv*)
1. Brain volume spreadsheet (*mask\_ja\_\*\_combined.csv*)


**IV) Visualization**

**Required User Input Files**

1. Divided files *(r\_\*C0divC1.nii.gz, r\_\*C2divC1.nii.gz)* 
1. Jacobian files *(mask\_ja\_\*.nii.gz*)

**To run the Visualization steps:**

1. In terminal, run ***zscore.sh*** followed by the genotype names (wt, het, hom) as labelled in the file name to calculate a z-score with respect to wild-type. 
1. In terminal, run ***afni\_roi.sh*** followed by the genotype names as labelled in the file name (e.g., to calculate statistically significant voxels for a genotype named “hom”, the input would be afni\_roi.sh hom).
1. In terminal, run ***afni\_vol.sh*** followed by the genotype names as labelled in the file name (e.g., to calculate statistically significant voxels for a genotype named “hom”, the input would be afni\_vol.sh hom).
1. In terminal, run ***clusterfilter.sh*** followed by the p-value threshold (pthr) in the leftmost column of the 3dClustSim files (*\*.1D*).
1. The BioImage Suite Viewer Tool ([https://bioimagesuiteweb.github.io/webapp/viewer.html#](https://bioimagesuiteweb.github.io/webapp/viewer.html)) is used to extract Frame 1 from the ttest file.
1. Frame 1 files (both cluster filtered, and non-cluster filtered) are visualized in the BioImage Suite Overlay Viewer (https://bioimagesuiteweb.github.io/webapp/overlayviewer.html?load=https://bioimagesuiteweb.github.io/webapp/images/viewer.biswebstate) and the BioImage Suite Dual Viewer (https://bioimagesuiteweb.github.io/webapp/dualviewer.html#).


**Visualization Output Files:**

1. Z-score files *(r\_\* C0divC1\_zscore.nii.gz, r\_\* C2divC1\_zscore.nii.gz, and mask\_ja\_\*\_zscore.nii.gz*)
1. t-test file (*\*\_vs\_wt\_roi\_ttest\_C0divC1.nii.gz, \*\_vs\_wt\_roi\_ttest\_C2divC1.nii.gz,  \*\_vs\_wt\_volume\_ttest.nii.gz)*
1. Z-brain binary files (*Zbrain\_binary\_mask\_rsp\_roi.nii.gz, Zbrain\_binary\_mask\_rsp.nii.gz* )
1. Residual files (*\*\_vs\_wt\_\*\_roi\_resid\_C0divC1.nii.gz, \*\_vs\_wt\_\*\_roi\_resid\_C2divC1.nii.gz, \*\_vs\_wt\_\*\_volume\_resid.nii.gz* )
1. **3DFWHMx** Regression Plot file (*3dFWHMx.1D.png*)
1. **3DFWHMx** Regression Plot autocorrelation data *(\*\_3dFWHMx.1D*)
1. Frame 1 files (*\*roi\_ttest\_Frame\_001\_C0divC1.nii.gz, \*roi\_ttest\_Frame\_001\_C2divC1.nii.gz, and \*volume\_Frame\_001.nii.gz)*
1. Cluster filtered Frame 1 file (*ttest\_expfolder\_Frame001\_roi\_genotype\_p-value \_ clustersize\_cellspecificmarker.nii.gz)*
1. **3dClustSim** files (*\*.1D*)


