TEToolkit
=========

Version: 1.5.1

*NOTE* TEtranscripts rely on specially curated GTF files, which are not
packaged with this software due to their size. Please go to 
`our website <http://hammelllab.labsites.cshl.edu/software#TEToolkit>`_
for instructions to download the curated GTF files.

TEtranscripts takes RNA-seq (and similar data) and annotates reads to both
genes & transposable elements. It then performs differential analysis using
DESeq.


`Github Page <https://github.com/mhammell-laboratory/tetoolkit>`_

`Pypi Page <https://pypi.python.org/pypi/TEToolkit>`_

`MHammell Lab <http://hammelllab.labsites.cshl.edu/software>`_

Created by Ying Jin, Eric Paniagua, Oliver Tam & Molly Hammell, February 2014

Copyright (C) 2014-2015 Ying Jin, Eric Paniagua, Oliver Tam & Molly Hammell

Contact: Ying Jin (yjin@cshl.edu)

Requirements
------------

Python:     2.6.x or 2.7.x (not tested in Python 3.x)

pysam:      0.8.2.1 or greater

R:          2.15.x or greater

DESeq:      1.5.x or greater


Installation
------------

1. Download compressed tarball.
2. Unpack tarball.
3. Navigate into unpacked directory.
4. Run the following::

    $ python setup.py install

If you want to install locally (e.g. /local/home/usr),
run this command instead::

    $ python setup.py install --prefix /local/home/usr

*NOTE* In the above example, you must add

    /local/home/usr/bin

to the PATH variable, and

     /local/home/usr/lib/python2.X/site-packages 

to the PYTHONPATH variable, where python2.X refers to the 
python version (e.g. python2.7 if using python version 2.7.x).


TEtranscripts
=============

Usage
-----

::

    usage: TEtranscripts -t treatment sample [treatment sample ...] 
                         -c control sample [control sample ...]
                         --GTF genic-GTF-file
                         --TE TE-GTF-file 
                         [optional arguments]

    Required arguments:
      -t | --treatment [treatment sample 1 treatment sample 2...]
         Sample files in group 1 (e.g. treatment/mutant), separated by space
      -c | --control [control sample 1 control sample 2 ...]
         Sample files in group 2 (e.g. control/wildtype), separated by space
      --GTF genic-GTF-file  GTF file for gene annotations
      --TE TE-GTF-file      GTF file for transposable element annotations

    Optional arguments:

      *Input/Output options*
      --format [input file format]
         Input file format: BAM or SAM. DEFAULT: BAM
      --stranded [option]   Is this a stranded library? (yes, no, or reverse).
                            DEFAULT: yes.
      --sortByPos           Input file is sorted by chromosome position.
      --project [name]      Prefix used for output files (e.g. project name)
                            DEFAULT: TEtranscript_out

      *Analysis options*
      --mode [TE counting mode]
         How to count TE:
            uniq        (unique mappers only)
            multi       (distribute among all alignments).
         DEFAULT: multi
      --minread [min_read] read count cutoff. DEFAULT: 1
      -L | --fragmentLength [fragLength]
         Average length of fragment used for single-end sequencing
         DEFAULT: For paired-end, estimated from the input alignment file. For single-end, ignored by default.
      -n | --norm [normalization]
         Normalization method : DESeq_default (default normalization method of DESeq), TC (total annotated read counts), quant (quantile normalization). 
         DEFAULT: DESeq_default
      -i | --iteration 
         maximum number of iterations used to optimize multi-reads assignment. DEFAULT: 10
      -p | --padj [pvalue]
         FDR cutoff for significance. DEFAULT: 0.05
      -f | --foldchange [foldchange]
         Fold-change ratio (absolute) cutoff for differential expression. 
         DEFAULT: 1

      *Other options*
      -h | --help
         Show help message
      --verbose [number]
         Set verbose level.
           0: only show critical messages
           1: show additional warning messages
           2: show process information
           3: show debug messages
         DEFAULT: 2
      --version
         Show program's version and exit

*NOTE* BAM files must be either unsorted or sorted by queryname. If the BAM files are sorted by position, please use the '--sortByPos' option


Example Command Lines
---------------------

If BAM files are unsorted, or sorted by queryname:: 

    TEtranscripts --format BAM --mode multi -t RNAseq1.bam RNAseq2.bam -c CtlRNAseq1.bam CtlRNAseq.bam --project sample_nosort_test

If BAM files are sorted by coordinates/position::

    TEtranscripts --sortByPos --format BAM --mode multi -t RNAseq1.bam RNAseq2.bam -c CtlRNAseq1.bam CtlRNAseq.bam --project sample_sorted_test


Recommendations for TEToolkit input files
=========================================

TEToolkit can perform transposable element quantification from alignment results (e.g. BAM files) generated from a variety of programs. 
Given the variety of experimental systems, we could not provide an optimal alignment strategy for every approach. Therefore,
we recommend that users identify the optimal parameters for their particular genome and alignment program in order to get the best
results.

When optimizing the alignment parameters, we recommend taking these points into consideration:

*Allowing sufficient number of multi-mappers during alignment*

Most alignment programs provide only 1 alignment per read by default. We recommend reporting multiple alignments per read. We have found 
that reporting a maximum of 100 alignments per read provides an optimal compromise between the size of the alignment file and recovery 
of multi-mappers in many genome builds. However, we highly suggest that users optimize this parameter for their particular experiment, 
as this could significantly improve the quality of transposable element quantification.

*Optimizing alignment parameters for non-reference strains*

It is common that the specific laboratory strains used in an experiment contains genomic variations not present in the reference strain.
While this can be mitigated through allowing mismatches during alignments, certain lab strains (e.g. Drosophila melanogaster) have
diverged significantly from the reference genomes. We highly recommend that users should refine their alignment procedures to better
account for the expected variations between their lab strains and the reference genome, which will accordingly improve their analysis
with TEToolkit. Users can also align to a custom genome build specific to their organism, though they would need GTF annotations for 
genes and transposable elements that are compatible with their custom genome in order to utilize TEToolkit. Please contact us if you
require advice in generating these annotation files.

*Specific recommendations when using STAR*

`STAR <https://github.com/alexdobin/STAR>`_ utilizes two parameters for optimal identification of multi-mappers `--outFilterMultimapNmax` and `--outAnchorMultimapNmax`. 
The author of STAR recommends that `--outAnchorMultimapNmax` should be set at twice the value used in `--outFilterMultimapNmax`, 
but no less than 50. In our study, we used the same number for both parameters (100), and found negligible differences in identifying 
multi-mappers. Upon further discussion with the author of STAR, we recommend that setting the same value for `--outAnchorMultimapNmax`
and `--outFilterMultimapNmax`, though we highly suggest users test multiple values of `--outAnchorMultimapNmax` to identify the 
optimal value for their experiment.


Copying & distribution
======================

TEtranscripts is part of TEToolKit.

TEToolKit is free software: you can redistribute it and/or modify
it under the terms of the GNU General Public License as published by
the Free Software Foundation, either version 3 of the License, or
(at your option) any later version.

This program is distributed in the hope that it will be useful,
but *WITHOUT ANY WARRANTY*; without even the implied warranty of
*MERCHANTABILITY or FITNESS FOR A PARTICULAR PURPOSE*.  See the
GNU General Public License for more details.

You should have received a copy of the GNU General Public License
along with TEToolKit.  If not, see `this website <http://www.gnu.org/licenses/>`_.


