## Methrix

------------------------------------------------------------------------------------
Fast and efficient summarization of generic bedGraph files from Bisufite sequencing
------------------------------------------------------------------------------------

### Introduction

Bedgraph files generated by BS pipelines often come in various flavors. Critical downstream step requires aggregation of these
files into methylation/coverage matrices. 
You guessed it - `Methrix` does this for you, including many other cool stuffs!

<p align="center">
<img src="https://github.com/CompEpigen/methrix/vignettes/overview.png">
</p>

### Summary:

* Faster summarization of generic bedGraph files with `data.table` back-end
* Fills missing CpGs from reference genome
* Vectorized code (faster, memory expensive) and non-vectorized code (slower, minimal memory)
* Built upon `SummarizedExperiment` with custom methods for CpG extraction, sub-setting, and filtering
* Easy conversion to bsseq object for downstream analysis
* Extensive one click interactive html report generation
* Supports serialized arrays with `HDF5Array` and `saveHDF5SummarizedExperiment` [ ** In progress ** ]

### Updates:
see [here](https://github.com/CompEpigen/methrix/blob/master/NEWS.md)

### Installation
```r
if (!requireNamespace("BiocManager", quietly = TRUE))
    install.packages("BiocManager")
BiocManager::install("CompEpigen/methrix")
```

### Usage:
#### Step-1: Extract CpG loci from the reference genome

```r
> hs37d5_cpgs = methrix::extract_CPGs(ref_genome = "BSgenome.Hsapiens.1000genomes.hs37d5")
# Extracting CpGs..
# Here is a Chuck Norris joke while you wait..
# Chuck Norris doesn't chew gum. Chuck Norris chews tin foil.
# Done.
# Extracted 28787054 from 86 contigs.
```
#### Step-2: Read in bedgraphs and generate a methrix object

Test data is 3 stranded WGBS bedgraphs from MethylcTools

```r
> meth = methrix::read_bedgraphs(files = bdg_files[1:3], pipeline = "MethylcTools", collapse_starnds = TRUE, vect_batch_size = 3, ref_build = "Hs37d5", ref_cpgs = hs37d5_cpgs)
# Using MethylcTools as a preset
# Retained 28,162,972 CpGs after filtering for contigs
# Processing batch 1 of 1
# Missing 0 reference CpGs from: tumor00_JMMLC_D117.CG.bed.gz
# Missing 0 reference CpGs from: tumor00_JMMLC_D123.CG.bed.gz
# Missing 0 reference CpGs from: tumor00_JMMLC_D124.CG.bed.gz
# 00:02:14 elapsed (00:07:09 cpu)

> meth
An object of class  methrix 
                ID        Summary
1:       n_samples              3
2:          n_CpGs     28,162,972
3:     n_uncovered 2337368 [8.3%]
4:   n_chromosomes             25
5: Reference_Build         Hs37d5
6:           is_H5          FALSE
```

### Methrix operations

What can be done on `methrix` object?

```r
coverage_filter() #Filter matrices by coverage
get_matrix() #Extract methylation or coverage matrices
region_filter() #Filter matrices by genomic region of interst
remove_uncovered() #Remove loci that are uncovered across all samples
subset_methrix() #Subset methrix object based on given condition
write_bedgraphs() #Writes bedGraphs from methrix object (optionally convert to bigWig on *nix system)
order_by_sd() #rder methrix object by SD
methrix_report() #Create a detailed interative html summary report from methrix object
methrix2bsseq() #Convert methrix to bsseq object
```

#### Example

Filter matrices based on coverage statistics (e.g: at-least two samples should have a loci covered by 4 reads)

```r
> x5 = methrix::filter_methrix(m = x0, cov_thr = 5, min_samples = 2, n_threads = 4)
# Retained 8916847 of 28162972 sites

#New filtered methrix object
>  x5
# An object of class  methrix 
#                 ID Summary
# 1:       n_samples       3
# 2:          n_CpGs 8916847
# 3: Reference_Build  Hs37d5
# 4:           is_H5   FALSE 
```

### Quick (and dirty) benchmark

To be added

### To do

- [X] Write methods
- [ ] Add proper run-time benchmark
- [ ] Full implementation of data.table back-end
- [ ] Avoid matrices
- [ ] new class independent of summarized experiment
- [ ] benchmark memory consumption

### Note

***This repository is under active development***
