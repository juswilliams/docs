|                         |                                            |
|-------------------------|--------------------------------------------|
| **Authors**             | Scott Newman, Michael Macias               |
| **Publication**         | Mutational Signatures employs MutationalPatterns: "[MutationalPatterns: comprehensive genome-wide analysis of mutational processes.][10.1186/s13073-018-0539-0]" |
| **Technical Support**   | [Contact Us](https://stjude.cloud/contact) |

## Overview

**Mutational Signatures** finds and quantifies COSMIC mutational signatures
across samples. This is done by finding the optimal non-negative linear
combination of mutation signatures to reconstruct a mutation matrix. It
builds the initial mutation matrix from multiple single-sample VCFs and, by
default, fits it to [mutational signatures from COSMIC]. Mutational
Signatures employs [MutationalPatterns] ([Blokzijl, et al. (2018)]) to
achieve this.

Mutational Signatures supports both hg19 (GRCh37) and hg38 (GRCh38).

[mutational signatures from COSMIC]: https://cancer.sanger.ac.uk/cosmic/signatures
[MutationalPatterns]: https://bioconductor.org/packages/release/bioc/html/MutationalPatterns.html
[Blokzijl, et al. (2018)]: #references


## Inputs

| Name                           | Type           | Description                                                                                  | Example               |
|--------------------------------|----------------|----------------------------------------------------------------------------------------------|-----------------------|
| [VCF(s)]                       | Array of files | List of VCF inputs. Can be single-sample or multi-sample and uncompressed or gzipped.        | [`*.vcf`, `*.vcf.gz`] |
| [Sample sheet]                 | File           | Tab-delimited file (no headers) with sample ID and tag pairs [optional]                      | `*.txt`               |
| Genome build                   | String         | Genome build used as reference. Can be either "GRCh37" or "GRCh38". [default: "GRCh38"]      | GRCh38                |
| Minimum mutation burden        | Integer        | Minimum number of somatic SNVs a sample must have to be considered for analysis [default: 9] | 15                    |
| Minimum signature contribution | Integer        | Minimum number of mutations attributable to a single signature [default: 9]                  | 100                   |
| [Output prefix]                | String         | Prefix to append to output filenames [optional]                                              | mtsg                  |
| [Disabled VCF column]          | Integer        | VCF column (starting from sample names, zero-based) to ignore when reading VCFS [optional]   | 1                     |

[VCF(s)]: #vcfs
[Sample sheet]: #sample-sheet
[Output prefix]: #output-prefix
[Disabled VCF column]: #disabled-vcf-column

### Input configuration

Mutational Signatures only requires VCFs as inputs. This can be a single multi-sample VCF, multiple single-sample VCFs, or a combination of both. All other inputs are optional.


<h4 id="VCF(s)">VCF(s)</h4>

_VCF(s)_ is a list of VCF inputs. The inputs can be single-sample or
multi-sample and uncompressed or gzipped. Sample names are taken from the VCF
header.

When using multi-sample VCFs, empty cells/absent variant calls must be
denoted with `.:.`.

gVCFs are not supported.

<h4 id="Sample Sheet">Sample Sheet</h4>

_Sample sheet_ is a tab-delimited file (no headers) with two columns: the
sample ID and a tag. The tag is an arbitrary identifier used to group the
samples, typically a disease abbreviation or tissue of origin.

If not given, a sample sheet will be generated automatically.

<h5>Example</h5>


|             |      |
|-------------|------|
| SJACT001_D  | ACT  |
| SJACT002_D  | ACT  |
| SJBALL063_D | BALL |
| SJHGG017_D  | HGG  |

<h4 id="output-prefix">Output prefix</h4>

_Output prefix_ is the prefix to append to the output filenames. By default,
if a single input VCF is given, its basename is used as the output prefix. If
multiple input VCFs are given, a default "mtsg" prefix is used. This behavior
can be overridden by a user-defined prefix.

<h5>Example</h5>

| VCF(s)                              | Prefix                  | Output filename for raw signatures       |
|-------------------------------------|-------------------------|------------------------------------------|
| [`pcgp.b38.refseq.goodbad.vcf`]     | pcgp.b38.refseq.goodbad | `pcgp.b38.refseq.goodbad.signatures.txt` |
| [`SJOS013_D.vcf`, `SJRHB007_D.vcf`] | mtsg                    | `mtsg.signatures.txt`                    |


<h4 id="disabled-vcf-column">Disabled VCF column</h4>

_Disabled VCF column_ is the column index to ignore when reading VCFs. This
is useful when the inputs are tumor-normal VCFs, and one column should be
ignored. Otherwise, the results would likely be duplicated.

The argument is a zero-based index relative to the sample names in the header
of the VCF. For example, in a VCF with samples `SJEPD003_D` and `SJEPD003_G`,
the germline sample (`SJEPD003_G`) can be discarded by setting the _disabled
VCF column_ to `1`.

<h5>Example</h5>

|        |     |    |     |     |      |        |      |        | 0          | 1          |
|--------|-----|----|-----|-----|------|--------|------|--------|------------|------------|
| #CHROM | POS | ID | REF | ALT | QUAL | FILTER | INFO | FORMAT | SJEPD003_D | SJEPD003_G |

## Outputs

| Name                       | Type | Description                                                                        |
|----------------------------|------|------------------------------------------------------------------------------------|
| [Raw signatures]           | File | Tab-delimited file of the raw results with sample contributions for each signature |
| [Signatures visualization] | File | HTML file for interactive plotting                                                 |
| [Sample sheet][sso]        | File | Tab-delimited file (no headers) with sample ID and tag pairs                       |

[Raw signatures]: #raw-signatures
[Signatures visualization]: #signatures-visualization
[sso]: #sample-sheet-out

## Workflow Steps

Mutational Signatures runs four steps using subcommands of [mtsg].

  1. Split VCFs (single or multi-sample) to multiple single-sample VCFs.
  2. If not given, generate a sample sheet from the directory of single-sample
     VCFs.
  3. Build a mutation matrix and reconstruct/fit it using COSMIC mutation
     signatures.
  4. Create a visualization file using the fitted signatures.


## Creating a workspace
Before you can run one of our workflows, you must first create a workspace in DNAnexus for the run. Refer to [the general workflow guide](running-sj-workflows.md#getting-started) to learn how to create a DNAnexus workspace for each workflow run.

You can navigate to the Mutational Signatures workflow page [here](https://platform.stjude.cloud/workflows/mutational_signatures).

## Uploading Input Files

Mutational Signatures requires at least one VCF and an optional sample sheet to be uploaded. 

Refer to [the general workflow guide](running-sj-workflows.md#uploading-files) to learn how to upload input files to the workspace you just created.

## Running the Workflow

Refer to [the general workflow guide](running-sj-workflows.md#running-the-workflow) to learn how to launch the workflow, hook up input files, adjust parameters, start a run, and monitor run progress.


## Analysis of Results
Each tool in St. Jude Cloud produces a visualization that makes understanding results more accessible than working with excel spreadsheet or tab delimited files. This is the primary way we recommend you work with your results. 

Refer to [the general workflow guide](running-sj-workflows.md#custom-visualizations) to learn how to access these visualizations.

We also include the raw output files for you to dig into if the visualization is not sufficient to answer your research question. 

Refer to [the general workflow guide](running-sj-workflows.md#raw-results-files) to learn how to access raw results files.

## Interpreting results

Upon a successful run of Mutational Signatures, three files are saved to the results directory: raw signature contributions, a visualization file, and a
sample sheet.

### Raw signatures

_Raw signatures_ is a tab-delimited file of the raw results with sample
contributions for each signature. Column 1 is the sample name, columns
2-(N-1) are the COSMIC signatures contribution counts, and column N is the
group tag, where N is the total number of columns. The number of columns is
variable since if the signature has no contributions for all samples, it is
completely omitted.

Note that the last column `tissue` is a misnomer. It aligns to the arbitrary
tag given in the [sample sheet](#sample-sheet).

<h5>Example</h5>

|              | Signature.1 | Signature.2 | … | Signature.30 | tissue |
|--------------|------------:|------------:|:-:|-------------:|--------|
| SJACT001_D   |  1.71758029 |  131.033723 | … |   18.6910151 | ACT    |
| SJAMLM7005_D |  51.9627312 |  7.10850351 | … |            0 | AMLM7  |

### Signatures visualization

_Signatures visualization_ is an HTML file that can be used for interactive
plotting.

When opened in a web browser, a set of controls allows plotting various
stacked bar charts: total contributions by signature, total contributions by
tag, and total contributions by sample per tag. The total contributions can be
stacked as absolute values or as a percentage of the total.

### Sample Sheet

When no sample sheet is given as an input, one is generated automatically,
but it is not guaranteed the derived tags will be of any use. This generated
sample sheet is given as an output in the case the tags need to be manually
edited, and the job is resubmitted with it as an input.

When a sample sheet is given as an input, the sample sheet output is a copy
of the input.

See also the description for the input [sample sheet](#sample-sheet).

## Troubleshooting

To troubleshoot a failed run of Mutational Signatures, check the job log for
details.

<h3 id="wrong-genome-build">Wrong genome build</h3>

If the "Building mutation matrix" step during `run` fails, it is likely that
the selected genome build does not match the input VCF(s). Rerun the job with
a matching genome build.

<h4>Example</h4>

```
R: Building mutation matrix from 6 VCFs
R: Error in mut_matrix(vcf_list = filtered_vcfs, ref_genome = ref_genome) :
R:   Error in .Call2("solve_user_SEW", refwidths, start, end, width, translate.negative.coord,  :
R:   solving row 526: 'allow.nonnarrowing' is FALSE and the supplied start (79440206) is > refwidth + 1
```

## Frequently asked questions

None yet! If you have any questions not covered here, feel free to reach
out on [our contact form](https://hospital.stjude.org/apps/forms/fb/st-jude-cloud-contact/).


## References

  * Blokzijl F, Janssen R, van Boxtel R, Cuppen E (2018). "MutationalPatterns:
    comprehensive genome-wide analysis of mutational processes." _Genome
    Medicine_. doi: [10.1186/s13073-018-0539-0]. PMID: [29695279].


## Similar Topics

[Running our Workflows](../analyzing-data/running-sj-workflows.md)  
[Working with our Data Overview](../managing-data/working-with-our-data.md)   
[Downloading/Uploading Data](../managing-data/data-transfer-app.md)   


[10.1186/s13073-018-0539-0]: https://doi.org/10.1186/s13073-018-0539-0
[29695279]: https://www.ncbi.nlm.nih.gov/pubmed/29695279

[mtsg]: https://github.com/stjude/mtsg

