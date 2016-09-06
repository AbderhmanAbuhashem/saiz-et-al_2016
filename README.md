# Saiz *et al.* (2016) *Nature Communications*

This is the README file for the saiz-et-al_2016 repository associated with the article by Saiz *et al.*, *Asynchronous fate decisions by single cells collectively ensure consistent lineage composition in the mouse blastocyst* **Nature Communications**, (2016) LINK, DOI.

This repository contains the data extracted from all embryo images in the study, the R scripts used to clean, transform and analyze the data and the code to generate all plots in the article.
All original microscopy images and raw image segmentation files can be found in [Figshare](https://dx.doi.org/10.6084/m9.figshare.c.3447537. ‘Figshare dataset’)

## Files included

Data tables:
- **FGF_all_pooled_raw.csv**: segmentation data for all embryos shown in Figs 1-3 and 5, and their associated Supplementary Figures 1-5 and 7. This table was generated by combining the original data tables for each embryo. These data have been corrected for over- and under-segmentation, and contain some metadata, although they are untransformed.
- **FGF_all_pooled_trans.csv**: the table resulting from transforming *FGF_all_pooled_raw.csv* using *Transformations.R* (see below).
- **scaling_pooled.csv**: segmentation data for all embryos in scaling experiments shown in Fig. 4 and Supplementary Fig. 6. This table was generated by combining the original data tables for each embryo. These have been corrected for over- and under-segmentation, although they are untransformed.
- **FGFonCD1_exp_ref.csv**: metadata for *FGF_all_pooled_raw.csv*.
- **scaling_exp_ref.csv**: metadata for *scaling_pooled.csv*.
- **Regimes.csv**: summary of the treatment regimes used and the corresponding stage at collection, fixation and treatment length (corresponds to Supplementary Table 1 in the article).

Scripts:
- **Transformations.R**: R script to load, tidy and transform the corrected data tables (both *FGF_all_pooled_raw.csv* and *scaling_pooled.csv*).
- **tables.R**: R script to generate summary tables containing information shown in the article figures as well as summary tables used to create plots.
- **stage.R**: R script to automatically assign stage to all embryos based on their cell count.
- **empirical_bayes.R**: R script to correct fluorescence level decay along the Z-axis using an Empirical Bayes approach, as described in the article’s Methods.
- **correct.fgf.R**: R script to correct fluorescence level decay along the Z-axis. These values are only used for embryos stained for OCT4, NANOG and GATA4 (shown in Fig. 3 and Supplementary Fig. 5b).
- **identities.R**: R script to automatically assign identities to all cells using a k-means clustering approach.
- **identify.R**: R script to automatically assign identities to all cells using a thresholding approach. The threshold in this function only applies to embryos where Treatment == ‘Littermate’ (data shown in Fig. 1 and Supplementary Fig. 1). The k-means clustering approach supersedes this method, and therefore thresholding-based identities were ultimately not used for the analysis of the data - they are provided only as reference.
- **figures_*n*-S*m*.R**: R scripts to generate all plots present in the corresponding figures in the published article.

## Data structure

All segmentation data is amalgamated two data frames in .csv format (*FGF_all_pooled_raw.csv* and *scaling_pooled.csv*). 
**FGF_all_pooled_raw.csv** and **FGFonCD1_exp_ref.csv** are made up of the following variables:
- **Experiment**: a unique identifier for each experiment (i.e., embryo litter), with the following structure: date (in MMDDYY format) + Regime (in R*n* format, see *Regimes.csv*) + identifier (if necessary - such as for two experiments with the same date and regime). Each **experiment** corresponds to a single litter of embryos.
- **Embryo_ID**: a unique identifier for each embryo, with the following structure: Experiment_ID (as above) + Embryo identifier (Experimental group initial + embryo number). Initials for experimental groups are as follow:
	* C: Control
	* F: FGF4
	* P: PD03 (MEKi)
	* A: AZD4547 (FGFRi)
	* S: SU5402 (FGFRi)
	* LM: reference littermate
- **Experimenter**: initials of individual collecting and processing the sample. NS: Nestor Saiz; KW: Kiah M Williams.
- **Regime**: culture regime, as defined in *Regimes.csv*.
- **Treatment**: treatment condition. Abbreviations defined as follows:
	* Control: culture media (KSOM) with or without 1ug/ml of Heparin
	* FGF4_1000: KSOM + 1ug/ml (1000ng/ml)  of rhFGF4 + 1ug/ml of Heparin
	* PD03_1: KSOM + 1uM PD0325901
	* AZD_1: KSOM + 1uM AZD4547
	* SU_10: KSOM + 10uM SU5402 (excluded from analysis)
	* SU_20: KSOM + 20uM SU5402
- **Tt_length**: length of treatment in the indicated conditions.
- **Cellcount**: total cell number of the embryo.
- **Cell_ID**: identifier for each nucleus (proxy for the cell) in an embryo. Cell_IDs correspond to the number assigned by MINS to each segmented nucleus, which can be found in the _*overlaid.tiff_ file for the corresponding embryo, at the corresponding XYZ coordinates.  
**NOTE:** Cell_IDs that are non-integers were generated during correction for undersegmentation. Fluorescence intensities for these nuclei have been measured manually and are reliable. However, XYZ coordinates may correspond to the nearest neighbor (corresponding integer Cell_ID).
- **Identity**: lineage identity, manually assigned by the experimenter. It has the following levels:
	* TE: trophectoderm - outer cell, CDX2+ (if applicable)
	* EPI: epiblast (NANOG+)
	* PRE: primitive endoderm (GATA6+)
	* DP: double positive (GATA6+, NANOG+)
	* DN: double negative (GATA6-, NANOG-)
- **Size**: volume of the segmented nucleus.
- **X**: X coordinate for that nucleus.
- **Y**: Y coordinate for that nucleus.
- **Z**: Z coordinate for that nucleus.
- **CH*n*.Avg**: average fluorescence intensity per pixel for the segmented nucleus in channel *n* (1-5). 
	* Channel 1: Hoechst (DNA stain)
	* Channel 2: CDX2 or OCT4 (see Markers)
	* Channel 3: bright field (not used)
	* Channel 4: GATA6 or GATA4 (see Markers)
	* Channel 5: NANOG
- **CH*n*.Sum**: sum of total fluorescence intensity for the segmented nucleus in channel *n*.
- **TE_ICM**: TE or ICM identity for each cell, assigned based on **Identity** (ICM = non-TE cells).
- **Stage**: embryonic stage based on total cell count (Cellcount), assigned using *stage.R*. Bins are <32, 32-64, 64-90, 90-120, 120-150, >150 cells. Intervals include the lower number but not the upper number of the interval.
- **Markers**: indicate the antibody combination used for each embryo:
	* 'C2G6NG': CDX2, GATA6, NANOG (rabbit)
	* 'O4G4NG': OCT4, GATA4, NANOG (rat)
- **CH*n*.logCor**: fluorescence intensity for channel *n* after correcting the log value of CH*n*.Avg for Z-associated fluorescence decay using *correct.fgf.R*.
- **CH*n*.ebLogCor**: fluorescence intensity for channel *n* after correcting the log value of CH*n*.Avg, per embryo, using *empirical_bayes.R*.
- **Identity.km**: identity assigned automatically after defining clusters for EPI, DP, PRE and DN populations using the k-means clustering approach (*identities.R*).
- **Exp_date**: date the experiment was started (embryo collection).
- **Img_date**: date the embryos were imaged.
- **Identity.lin**: identity assigned automatically using a thresholding method for the data on linear scale (after inverting the logarithm) - *identify.R*.
- **Xpoint**: experimental point
	* sp: start point - embryo fixed at collection.
	* xp: exchange point - embryo fixed at time of media exchange.
	* ep: end point - embryo fixed after *in vitro* culture.

**scaling_pooled.csv** and **scaling_exp_ref.csv** are made up of the same as above variables, with the following differences:

- **Experiment**: a unique identifier for each experiment (i.e., embryo litter), with the following structure: date (in MMDDYY format) + Experiment type (*halfCD1* for ‘half embryos’ or *2xCD1* for ‘double embryos’) + Litter_ID (A-F). Each experiment corresponds to a single litter of embryos.
- **Embryo_ID**: a unique identifier for each embryo, with the following structure: Experiment_ID (as above) + Embryo identifier (Experimental group initial + embryo number). Initials for experimental groups are as follow:
	* C: Single embryo (control)
	* H: Half embryo (1/2 8-cell stage)
	* D: Double embryo (2x 8-cel stage)
- **Treatment**: experimental manipulation, as follows:
	* Single: re-aggregated 8-cell embryo (for ‘half embryo’ experiments) or unmanipulated 8-cell stage embryo (for ‘double embryo’ experiments). See article’s Methods.
	* Half: re-aggregated 4-cell embryo (after 8-cell stage dissociation).
	* Double: aggregated 2x 8-cell stage embryos.


## Usage

To re-create the plots from the article:

1. Download all files to a local directory - **_respect the file names_**.
2. Using RStudio, create a new project in that directory and open the Rproject file. Alternatively, download *FGFonCD1_analysis.Rproj*. This file will recognize the working directory and has *Transformations.R* loaded already.
3. Open *Transformations.R* and *tables.R* and source them or load them from the console by typing 
```R
source(‘Transformations.R’)
source(‘tables.R’)
```

4. Open the figure files and source them or load them from the console by typing 
```R
source(‘filename.R’) #Where filename.R is the corresponding figure’s script
```

To explore the data and re-analyze, use your preferred software and the data structure above as reference.
