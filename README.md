# tsrexplorer

## Installing TSRexplorer

**Create conda environment**
```
conda create -n tsrexplorer -y -c conda-forge -c bioconda \
r-tidyverse \
r-devtools \
r-ggseqlogo \
r-ggally \
bioconductor-genomicranges \
bioconductor-genomicfeatures \
bioconductor-biostrings \
bioconductor-rsamtools \
bioconductor-chipseeker \
bioconductor-edger
```

**Install latest version of tsrexplorer**
```
devtools::install_github("rpolicastro/tsrexplorer")
```

## Using TSRexplorer

### Preparing TSRexplorer

**Load tsrexplorer**

```
library("tsrexplorer")
```

**Load example data**

```
TSSs <- system.file("extdata", "yeast_TSSs.RDS", package = "tsrexplorer")
TSSs <- readRDS(TSSs)

TSRs <- system.file("extdata", "yeast_TSRs.RDS", package = "tsrexplorer")
TSRs <- readRDS(TSRs)

annotation <- system.file("extdata", "yeast_annotation.gtf", package="tsrexplorer")
assembly <- system.file("extdata", "yeast_assembly.fasta", package="tsrexplorer")
```

**create tsr object**

```
exp <- tsr_explorer(TSSs, TSRs)
```

## TSS Analysis

### Count Normalization and Correlation

**tmm normalize counts**

```
exp <- count_normalization(exp, data_type = "tss")
```

**tss correlation heatmaps and scatter plots**

```
p <- plot_correlation(exp, data_type = "tss") +
	theme_bw() +
	theme(text = element_text(size = 6))

ggsave("tss_correlation.png", plot = p, device = "png", type = "cairo", height = 3, width = 3)
```
![tss_corr_plot](./inst/images/tss_correlation.png)

### TSS Annotation and Genomic Distribution

**Annotate TSSs**

```
exp <- annotate_features(exp, annotation_file = annotation, data_type = "tss", feature_type = "transcript")
```

**TSS Genomic Distribution**

```
tss_distribution <- genomic_distribution(exp, data_type = "tss", threshold = 3)

p <- plot_genomic_distribution(tss_distribution) +
	theme(text = element_text(size = 6))

ggsave("tss_genomic_distribution.png", plot = p, device = "png", type = "cairo", height = 1.5, width = 4)
```

![tss_genomic_distribution](./inst/images/tss_genomic_distribution.png)

It is also possible to plot genomic distribution based on the quantile of the TSS score.

```
tss_distribution <- genomic_distribution(exp, data_type = "tss", threshold = 3, quantiles = 5)

p <- plot_genomic_distribution(genomic_dist) +
	theme(text = element_text(size = 6))

ggsave("tss_genomic_distribution_quantiles.png", plot = p, device = "png", type = "cairo", height = 4, width = 4)
```

![tss_genomic_distribution_quantiles](./inst/images/tss_genomic_distribution_quantiles.png)

### TSS Average Plot and Heatmap

**TSS average plot**

```
p <- plot_average(exp, data_type = "tss", threshold = 3, ncol = 3) +
	theme(text = element_text(size = 6))

ggsave("tss_average_plot.png", plot = p, device = "png", type = "cairo", height = 0.5, width = 4)
```

![tss_average_plot](./inst/images/tss_average_plot.png)

You can also plot TSS average plots broken down by TSS quantile

```
p <- plot_average(exp, data_type = "tss", threshold = 3, quantiles = 5) +
	theme(text = element_text(size = 5))

ggsave("tss_average_plot_quantiles.png", plot = p, device = "png", type = "cairo", height = 2.5, width = 3.5)
```

![tss_average_plot_quantiles](./inst/images/tss_average_plot_quantiles.png)

**TSS heatmap**

```
count_matrix <- tss_heatmap_matrix(exp, sample = "S288C_WT_100ng_1", threshold = 3, anno_type = "geneId", upstream = 250, downstream = 250)

p <- plot_tss_heatmap(count_matrix) +
	theme(text = element_text(size = 6))

ggsave("tss_heatmap.png", plot = p, device = "png", type = "cairo", height = 2, width = 2.5)
```

![tss_heatmap](./inst/images/tss_heatmap.png)

### TSS Motif and Base Composition

**TSS sequence logo**

```
seqs <- tss_sequences(exp, sample = "S288C_WT_100ng_1", genome_assembly = assembly, threshold = 3)

p <- plot_sequence_logo(seqs) +
	theme(text = element_text(size = 6))

ggsave("tss_seq_logo.png", plot = p, device = "png", type = "cairo", height = 1, width = 3)

```

![tss_sequence_logo](./inst/images/tss_seq_logo.png)

**TSS base color map**

```
p <- plot_sequence_colormap(seqs) +
	theme(text = element_text(size = 6))

ggsave("tss_seq_colormap.png", plot = p, device = "png", type = "cairo", height = 2.5, width = 2.5)
```
![tss_sequence_colormap](./inst/images/tss_seq_colormap.png)

**TSS dinucleotide frequencies**

```
frequencies <- dinucleotide_frequencies(exp, genome_assembly = assembly, threshold = 3)

p <- plot_dinucleotide_frequencies(frequencies, ncol = 3) +
	theme(text = element_text(size = 6))

ggsave("tss_dinucleotide_frequencies.png", plot = p, device = "png", type = "cairo", height = 2, width = 5)
```

![tss_dinucelotide_frequencies](./inst/images/tss_dinucleotide_frequencies.png)

You can also plot dinucleotide frequencies by quantile

```
freqs <- dinucleotide_frequencies(exp, assembly, threshold = 3, quantiles = 5)

p <- plot_dinucleotide_frequencies(freqs) +
	theme(text = element_text(size = 5))

ggsave("tss_dinucleotide_frequencies_quantiles.png", plot = p, device = "png", type = "cairo", height = 5, width = 4)
```

![tss_dinucleotide_frequencies_quantiles](.inst/images/tss_dinucleotide_frequencies_quantiles.png)

### Misc TSS Plots

**Average distance of dominant TSS**

```
dominant <- dominant_tss(exp, sample = "S288C_WT_100ng_1", threshold = 3, feature_type = "geneId")

p <- plot_dominant_tss(dominant, upstream = 500, downstream = 500)

ggsave("dominant_tss.png", plot = p, device = "png", type = "cairo", height = 4, width = 4)
```

![dominant_tss](./inst/images/dominant_tss.png)

**Max UTR Length**
``` 
max <- max_utr(exp, sample = "S288C_WT_100ng_1", threshold = 3, feature_type = "geneId")

p <- plot_max_utr(max)

ggsave("max_utr.png", plot = p, device = "png", type = "cairo", height = 4, width = 4)
```

![max_utr](./inst/images/max_utr.png)

## TSR Analysis

### Count Normalization and Correlation

**TMM normalize counts**

```
exp <- count_normalization(exp, data_type = "tsr")
```

**TSR correlation heatmap and scatter plot**

```
p <- plot_correlation(exp, data_type = "tsr") +
	theme_bw() +
	theme(text = element_text(size = 6))

ggsave("tsr_correlation.png", plot = p, device = "png", type = "cairo", height = 3, width = 3)
```

![tsr_correlation](./inst/images/tsr_correlation.png)

### TSR Metric Plots

**Plot Selected TSR Metric**

The GRanges object with TSRs originally added to the TSR object is allowed to have additional columns.
Information about the TSRs can be contained within these columns, such as TSR width, number of unique TSSs, etc.
You can make a density plot of any of these additional columns by specifying the name of the column containing that metric.

```
p <- plot_tsr_metric(exp, tsr_metrics = c("nTAGs", "nTSSs"), log2_transform = TRUE, ncol = 2) +
	theme(text = element_text(size = 6))

ggsave("tsr_metrics.png", plot = p, device = "png", type = "cairo", width = 4, height = 2)
```
![tsr_metrics](./inst/images/tsr_metrics.png)

### TSR Annotation and Genomic Distribution

**TSR Annotation**

```
exp <- annotate_features(exp, annotation_file = annotation, data_type = "tsr", feature_type = "transcript")
```

**TSR Genomic Distribution**

```
tsr_distribution <- genomic_distribution(exp, data_type = "tsr")

p <- plot_genomic_distribution(tsr_distribution) +
	theme(text = element_text(size = 6))

ggsave("tsr_genomic_distribution.png", plot = p, device = "png", type = "cairo", height = 1.5, width = 4)
```

![tsr_genomic_distribution](./inst/images/tsr_genomic_distribution.png)

### TSR Average Plot and Heatmap

**TSR Average Plot**

```
p <- plot_average(exp, data_type = "tsr", ncol = 3) +
	theme(text = element_text(size = 6))

ggsave("tsr_average_plot.png", plot = p, device = "png", type = "cairo", height = 1.5, width = 4)
```

![tsr_average_plot](./inst/images/tsr_average_plot.png)

**TSR Heatmap**

```
counts <- tsr_heatmap_matrix(exp, sample = "S288C_WT_100ng_1", feature_type = "transcriptId")

p <- plot_tsr_heatmap(counts) +
	theme(text = element_text(size = 6))

ggsave("tsr_heatmap.png", plot = p, device = "png", type = "cairo", height = 2, width = 2.5)
```

![tsr_heatmap](./inst/images/tsr_heatmap.png)

### Differential TSRs

(Work in Progress)

**Find Differential TSRs**

```
edger_model <- fit_edger_model(
	exp,
	data_type = "tsr", 
	samples = c(
		"S288C_WT_100ng_1",
		"S288C_WT_100ng_2",
		"S288C_WT_100ng_3",
		"S288C_Diamide_100ng_1",
		"S288C_Diamide_100ng_2",
		"S288C_Diamide_100ng_3"
	),
	groups = c(1, 1, 1, 2, 2, 2)
)

diff_tsrs <- differential_expression(edger_model, data_type = "tsr", comparisons = c(1, 2))
```

**Annotate Differential TSRs**

```
annotated_diff_tsrs <- annotate_differential_tsrs(diff_tsrs, annotation_file = annotation, feature_type = "transcript")
```

**Differential TSRs Volcano Plot**

```
p <- plot_volcano(diff_tsrs)

ggsave("diff_tsrs_volcano_plot.png", plot = p, device = "png", type = "cairo", height = 2, width = 4)
```

![diff_tsrs_volcano_plot](./inst/images/diff_tsrs_volcano_plot.png)

## TSS Mapping vs RNA-seq

**Add RNA-seq Data**

```
RNAseq <- system.file("extdata", "yeast_RNAseq.RDS",  package = "tsrexplorer")
RNAseq <- readRDS(RNAseq)

TSSs_total <- system.file("extdata", "yeast_TSSs_total.RDS",  package = "tsrexplorer")
TSSs_total <- readRDS(TSSs_total)

exp <- add_rnaseq_feature_counts(exp, RNAseq)
exp <- count_normalization(exp, data_type = "rnaseq_features")

exp <- add_tss_feature_counts(exp, TSSs_total)
exp <- count_normalization(exp, data_type = "tss_features")
```

**RNA-seq Versus TSS Correlation Heatmap and Scatter Plot**

```
p <- plot_correlation(exp, data_type = "rnaseq_v_tss", correlation_metric = "spearman") +
	theme_bw() +
	theme(text = element_text(size = 6))

ggsave("rnaseq_correlation.png", plot = p, device = "png", type = "cairo", height = 4.5, width = 4.5)
```

![rnaseq_tss_corr](./inst/images/rnaseq_correlation.png)
