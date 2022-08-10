# TAGET user manual

TAGET is a computational toolkit that provides a wide spectrum of tools for analyzing full-length transcriptome data. Based on its highly precise transcript alignment and junction prediction, TAGET enables accurate novel isoform, gene fusion detection, and expression quantification analyses

## software install

	git clone https://github....

## Environmental dependence

 * HISAT2/MINIMAP2/GMAP at least one
 * samtools
 * python3
 * R>=3.3

## FAST RUN

	python main.py -f [fasta] -g [genome fasta] -o [output directory] -a[annot gtf] -p [process] --use_minimap2 [1] --use_hisat2 [hisat2 index]
	
	or you can use
	
	python main.py -c TransAnnot.Config

## software running
1.the config file contain environmental path of each software and the index file of the reference genome 


2. you can set the following parameters at the first time
	* the path of HISAT2/Minimap2
	* the index file of reference genome
	* reference genome(FASTA)、anotiation of transcript file default Ensemble（GTF）、process number

3. After setting the base parameters,you can set the fasta file of the full length transcript and the output dictionary or you can use `-c config` and `-f [fatsa] -o [output]`
4. The reads\transcript\gene expression can be caculated by the parameter of --tpm



## running result

The output files contain the following files:


* [{sample_id}.annot.bed]() the bed format with annotated genes

* [{sample_id}.annot.stat]()  the annotations of each transcripts

* [{sample_id}.annot.db.pickle]()  the input file of visualization

* [{sample_id}.annot.cluster.gene]()  the cluster of genes

* [{sample_id}.annot.cluster.transcript]()  the cluster of transcript

* [{sample_id}.annot.cluster.reads]()  the cluster of reads

* [{sample_id}.annot.junction]()  the information of splice junction file

* [{sample_id}.annot.multiAnno]()  muliti-annotation transcript

### {sample_id}.annot.stat each coloumn：

* `ID`： reads ID
* `Classification`： classificatioin of reads
* `Subtype`: subtype of reads
* `Gene`: gene annotation or region in genome[chr1:100000-100500]
* `Transcript`: transcript annotation
* `Chrom`: chromosome
* `Strand`: strand
* `Seq_length`: reads length
* `Seq_exon`: exon number of reads
* `Ref_length`: length of transcript annotation
* `Ref_exon_num`: exon number of transcript annotatioin
* `diff_to_gene_start`: 5` site difference of reads and annotation gene in reference genome
* `diff_to_gene_end`: 3` site difference of reads and annotation gene in reference genome
* `diff_to_transcript_start`: 5` site difference of reads and annotation transcript in reference genome
* `diff_to_transcript_end`: 3` site difference of reads and annotation transcript in reference genome
* `exon_miss_to_transcript_start`: number of exon missed in 5` site between reads and transcript annotation
* `exon_miss_to_transcript_end`: number of exon missed in 3` site between reads and transcript annotation

## TransAnnot.Config


* `FASTA`: `[path]`，input file,fasta format of full length transcript
* `OUTPUT_DIR`: `[path]`，the output dictionary
* `GENOME_FA`: `[path]`，the fasta file of reference genome (eg,hg38.fa)
* `GTF_ANNOTATION`:`[path]`，the annotion file of gene default gtf format
* `PROCESS`: `[int]`,the number of process
* `SAMPLE_UNIQUE_NAME`:`[string]`,the output prefix of each files
* `SAMTOOLS`:`[path]`，the pathway of samtools
* `USE_HISAT2`: `[int]`，wether or not use HISAT2, 1 means use,0 means not use
* `HISAT2`: `[path]`,the pathway of Hisat2
* `HISAT2_INDEX`: `[path]`，the pathway of index of Hisat2 ，generated by `hisat2-build`
* `USE_MINIMAP2`: `[int]`，wether or not use minimap2, 1 means use,0 means not use
* `MINIMAP2`: `[path]`，the pathway of Minimap2
* `USE_GMAP`: `[int]`，wether or not use GMAP, 1 means use,0 means not use
* `GMAP`: `[path]`，the pathway of GMAP
* `GMAP_INDEX`: `[path]`,the pathway of GMAP index,generated by `gmap_build`
* `TPM_LIST`: `[path]`，the expression of Isoform
* `READ_LENGTH`: `[int]`，the read length used by HISAT2，default 100
* `READ_OVERLAP`: `[int]` the read overlap used by HISAT2， default 80
* `MIN_READ_LENGTH`: `[int]`，the minimum length of read，default 30



## TransAnnotMerge


We can use TransAnnotMerge to generate expression matrix of multi-samples


### TransAnnotMerge运行命令

`python TranAnnotMerge -c MergeConfig -o outputdir -m [TPM/FLC/None]`



* `-c`： Merge Config，consist of four coloumn,sample ID() ，[{sample_id}.annot.stat]()，[{sample_id}.annot.bed]()，[{sample_id}.annot.db.pickle]()。形如

| #sample | stat | bed | db |
| ------- | ---- | --- | -- |
| ------- | ---- | --- | -- |

* `-o`: the output dictionary
* `-m`：the gene and transcript expression displayed by different methods,FLC: full length count，if none is not expression matrix

### TransAnnotMerge running

1. extract isoform expression from fasta file：`python fa2exp.py -f [fa] -o [exp]`
2. running TranAnnotMerge: `python TranAnnotMerge -c MergeConfig -o outputdir -m TPM`

### TransAnnotMerge result file

* `{sample_id}.reads.exp`： the read expression of each file
* `{sample_id}.transcript.exp`： the transcript expression of each file
* `{sample_id}.gene.exp`： the gene expression of each file
* `gene.exp`： the gene expression matrix of each sample
* `transcript.exp`： the transcirpt expression matrix of each sample
* `merge.db.pickle`： view transcirpt
### DIU analysis
`python expression_V1.py  -t {sample_id}.transcript.exp -g {sample_id}.gene.exp -o {prefix}`
* `-t transcript expression of tumor and normal`
* `-g gene expression of tumor and normal`
* `-o prefix of outfile`
* `-r default 0.05 filter the low express transcript`
* `-p default 50 filter the low express gene`
## the classfication of isoform annotated by TAGET


### the classfication of transcript

* `FSM`: full splice site match
* `ISM`: incomplete splice site match
* `NIC`: novel in catalog
* `NNC`: novel not in catalog
* `GENIC`: genic
* `INTERGENIC`: intergenic
* `FUSION`: fusion
* `UNKNOWN`： unknown

### the classfication of exon

* `KE`: known exon
* `LEKE`: left end known exon
* `REKE`: right end known exon
* `NEKSLE`: novel exon with known splice site in left end exon and has the unique region overlap with at least two known exons
* `NEKSRE`: novel exon known splice site in right end exon and has the unique region overlap with at least two known exons
* `IE`: intron retention: two known splice sites from the same transcript's sequential exon
* `NEDT`: novel exon with two known splice sites from different transcript
* `NELS`: novel exon with novel left splice site
* `NERS`: novel exon with novel right splice site
* `LEE`: left exon_extension： the novel splice site in the left end of the exon which is longer than any exons overlap with it
* `REE`: right exon_extension: the novel splice site in the right end of the exon which is longer than any exons overlap with it
* `NEDS`: novel exon:double novel splice sites overlap with at least one known exon
* `NEIG`: novel exon inner-gene：novel exon inside the gene and without any overlap with known exon
* `NEOG`: novel exon inter-gene：novel exon outside the gene
* `NELE`: novel exon with novel splice site in the far left exon
* `NERE`: novel exon with novel splice site in the far right exon
* `MDNS`: monoexon with double novel splice sites
# TAGET gene fusion detect and filter
`Rscript TAGET_fusion_2-3_ajust.r -j Jin_fusion_select.py -e STAT_select.py -l ${i}.fa.long.bed -s ${i}.fa.short.bed -a ${i}.fa.anno.final.stat -t hg38.gtf -f ${i}.fa -n name_${i} -o ./output`

* `${i}.fa.long.bed: the bed file mapped by minimap2 generated by TAGET`
* `${i}.fa.short.bed:the bed file mapped by HISAT2 generated by TAGET`
* `${i}.fa: the CCS read from Pacbio platform`
* `name_${i}: the prefix of generated file name`
