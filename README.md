# InSilicoSeq
## A sequencing simulator

[![Build Status](https://travis-ci.org/HadrienG/InSilicoSeq.svg?branch=master)](https://travis-ci.org/HadrienG/InSilicoSeq)
[![Documentation Status](https://readthedocs.org/projects/insilicoseq/badge/?version=latest)](http://insilicoseq.readthedocs.io/en/latest/?badge=latest)
[![PyPI version](https://badge.fury.io/py/InSilicoSeq.svg)](https://badge.fury.io/py/InSilicoSeq)
[![codecov](https://codecov.io/gh/HadrienG/InSilicoSeq/branch/master/graph/badge.svg)](https://codecov.io/gh/HadrienG/InSilicoSeq)
[![LICENSE](https://img.shields.io/badge/license-MIT-lightgrey.svg)](LICENSE)

InSilicoSeq is a sequencing simulator producing realistic Illumina reads.
Primarily intended for simulating metagenomic samples, it can also be used to produce sequencing data from a single genome.

InSilicoSeq is written in python, and use kernel density estimators to model the read quality of real sequencing data.

InSilicoSeq support substitution, insertion and deletion errors. If you don't have the use for insertion and deletion error a basic error model is provided.

## Installation

To install InSilicoSeq, type the following in your terminal:

```shell
pip3 install InSilicoSeq
```

or if you want to use python2, or are using python3 and pip3 aliased to python and pip:

```bash
pip install InSilicoSeq
```

Alternatively, with docker:

```shell
docker pull hadrieng/insilicoseq:1.2.0
```

For more installation options, please refer to the full [documentation](http://insilicoseq.readthedocs.io)

## Usage

InSilicoSeq has two subcommands: `iss generate` to generate Illumina reads and `iss model` to create an error model from which the reads will take their characteristics.

InSilicoSeq comes with pre-computed error models that should be sufficient for most use cases.

### Generate reads with a pre-computed error model

for generating 1 million reads modelling a MiSeq instrument:

```shell
curl -O -J -L https://osf.io/thser/download  # download the example data
iss generate --genomes SRS121011.fasta --model miseq --output miseq_reads
```

where `genomes.fasta` should be replaced by a (multi-)fasta file containing the reference genome from which the simulated reads will be generated.

InSilicoSeq comes with 3 error models: `MiSeq`, `HiSeq` and `NovaSeq`.

If you have built your own model, pass the `.npz` file to the `--model` argument to simulate reads from your own error model.

For 10 million reads and a custom error model:

```shell
curl -O -J -L https://osf.io/thser/download  # download the example data
iss generate -g SRS121011.fasta -n 10m --model my_model.npz --output my_reads
```

granted you have built `my_model.npz` with `iss model` (see [below](#create-your-own-error-model))

For more examples and a full list of options, please refer to the full
[documentation](http://insilicoseq.readthedocs.io)

### Generate reads without input genomes

We can download some for you! InSilicoSeq can download random genomes from the ncbi using the infamous [eutils](https://www.ncbi.nlm.nih.gov/books/NBK25501/)

The command

```shell
iss generate --ncbi bacteria -u 10 --model MiSeq --output ncbi_reads
```

will generate 1 million reads from 10 random bacterial genomes.

For more examples and a full list of options, please refer to the full [documentation](http://insilicoseq.readthedocs.io)

### Create your own error model

If you do not wish to use the pre-computed error models provided with InSilicoSeq, it is possible to create your own.

Say you have a reference metagenome called `genomes.fasta`, and read pairs `reads_R1.fastq.gz` and `reads_R2.fastq.gz`

Align you reads against the reference:

```shell
bowtie2-build genomes.fasta genomes
bowtie2 -x genomes -1 reads_R1.fastq.gz -2 reads_R2.fastq.gz | \
samtools view -bS | samtools sort -o genomes.bam
samtools index genomes.bam
```


then build the model:

```shell
iss model -b genomes.bam -o genomes
```

which will create a `genome.npz` file containing your newly built model

## License

Code is under the [MIT](LICENSE) license.

## Issues

Found a bug or have a question? Please open an [issue](https://github.com/HadrienG/InSilicoSeq/issues)

## Contributing

We welcome contributions from the community! See our [Contributing](CONTRIBUTING.md) guidelines

## Citation

A paper will be on its way. In the meantime if you use InSilicoSeq in your research, please cite the poster

> Gourlé, Hadrien (2017): Simulating Illumina data with InSilicoSeq. figshare. https://doi.org/10.6084/m9.figshare.5053327.v1
