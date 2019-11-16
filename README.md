# UNCALLED
A **U**tility for **N**anopore **C**urrent **Al**ignment to **L**arge **E**xpanses of **D**NA

Maps raw nanopore signals from fast5 files to large DNA references

## Installation

```
git clone --recursive https://github.com/skovaka/UNCALLED.git
cd UNCALLED
python setup.py install
```

Most dependecies included via submodules, so be sure to clone with `git --recursive`

UNCALLED must be installed into a python environment. To install without root privileges use the `--user` or `--prefix=<local-dir>` flag when installing, or use a tool such as [virtualenv](https://virtualenv.pypa.io) or [anaconda](https://www.anaconda.com).

Requires python-dev and GCC >= 4.8.1

[HDF5](https://www.hdfgroup.org/downloads/hdf5/) must be installed. Libraries and headers should be in system paths (ie `$LD_LIBRARY_PATH` and `$CPATH` respectively), or specified by running `python setup.py build_ext --library-dirs <hdf5-location>/lib --include-dirs <hdf5-location>/include` prior to installation.

## Usage

### Indexing

```
bwa index -p <bwa-prefix> <fasta-reference>
uncalled index -i <fasta-reference> -x <bwa-prefix>
```

UNCALLED requires a [BWA](https://github.com/lh3/bwa) index. You can use a previously built BWA index, or build a new one with the BWA instance provided in the `bwa/` submodule.

Before aligning, certain reference specific parameters must be computed using `uncalled index`. The `<fasta-reference>` should be the same FASTA file which was used to build the BWA index. This will create an additional file in the same directory as the BWA index named `<bwa-prefix>.uncl`.

### Mapping

```
uncalled map -x <bwa-prefix> -i <fast5-files>  >  <out.paf>
```

Arguments:
- `-x/--bwa-prefix` the prefix of the index to align to. Should be a BWA index that `uncalled index` was run on
- `-i/--fast5-files`  a text file containing the path to one fast5 file per line
- `-t/--threads` number of threads to use for mapping (default: 1)
- `-n/--read-count` maximum number of reads to map
- `--filter` text file containing subset of read IDs (one per line) to map from the fast5 files (will map all by default)

Outputs in a format similar to [PAF](https://github.com/lh3/miniasm/blob/master/PAF.md). Query coordinates, residue matches, and block lengths are estimated by average k-mer sampling rate.

See [example/](example/) for a simple read and reference example.

## Limitations

UNCALLED has not been tested on large (> ~100Mb) or highly repetitive references. `uncalled index` may take up large amounts of memory or crash on such references.

Currently only reads sequenced with r9.4 chemistry are supported.

This is very much a work in progress! Feel free to report any bugs.
