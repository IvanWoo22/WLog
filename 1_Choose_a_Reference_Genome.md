### Reference genome names are composed of an acronym of the organization that prepared the reference genome e.g. GRCh stands for Genome Reference Consortium human, and a number that represents the version of the reference genome.

For some organisms we provide several genomes, e.g. there are 3 reference genomes for H. sapiens. What are the
differences between these reference genomes“ And how do you chose the correct one“ The answer is not so straightforward
and depends on several factors - let's discuss each of them:

**1) Versions of the reference genome**  For instance:  Homo sapiens GRCh37.75 (unmasked) vs GRCh38.80 (unmasked) The
numbers correspond to versions (or "builds") of the reference genome - the higher the number, the more recent the
version. We generally recommend you use the latest version possible. One thing to remember is that for the newest genome
builds, it's likely that resources such as genome annotations and functional information will be limited, as it takes
time for Ensembl/ UCSC to integrate additional genomic data with the new build.

**2) One organism - many strains**  K12 and O103:H2 are two different strains of E.coli. K12 is an innocuous strain
commonly used in various labs around the world. O103:H2 is a pathogenic strains, commonly isolated from human cases in
Europe. Depending on your experiment, you should choose a matching reference genome.

**3) Masked, soft-masked and unmasked genomes**  There are three types of Ensembl reference genomes: unmasked,
soft-masked and masked. Generally speaking, it's recommended to use unmasked reference genomes builds for alignment.
Masking is used to detect and conceal interspersed repeats and low complexity DNA regions so that they could be
processed properly by alignment tools.

---

### When a genome is "masked", all repeats and low complexity regions are hidden away and replaced with "N"s, so that they will not be aligned to.

Masked reference genomes are also known as hard-masked DNA sequences. Repetitive and low complexity DNA regions are
detected and replaced with 'N's. The use of masked genome may adversely affect the analysis results, leading to wrong
read mapping and incorrect variant calls.

**When should you use a masked genome**  We generally don't recommend using masked genome, as it relates to the loss of
information (after mapping, some "unique" sequences may not be truly unique) and does not guarantee 100% accuracy and
sensitivity (e.g. masking cannot be absolutely perfect). Moreover, it can lead to the increase in number of falsely
mapped reads.

---

### Soft-masked — repeated and low complexity regions are still presented in the genome, but they have been replaced with lowercased versions of their nucleic base.

In soft-masked reference genomes, repeats and low complexity regions are also detected but in this case they are masked
by converting to a lowercase variants of the base (e.g. acgt).

**When should you use a soft-masked genome**  The soft-masked sequence does contain repeats indicated by lowercase
letters, so the use of soft-masked reference could improve the quality of the mapping without detriment to sensitivity.
But it should be noted that most of the alignment tools do not take into account soft-masked regions, for example BWA,
tophat, bowtie2 tools always use all bases in alignment weather they are in lowercase nucleotide or not. That is why,
there is no actual benefit from the use of soft masked genome in comparison with unmasked one. Sometimes you'll also see
repeat-masked genomes. What are they Masking can be performed by special tools, like RepeatMasker. The tool goes through
DNA sequence looking for repeats and low-complexity regions. By default, the tool replaces the found bases with "N"s.

---

### Unmasked — an "unmasked" genome contains all repeats and low complexity regions without any changes.

**When should you use an unmasked genome**  We recommend you use unmasked genome when you don't want to lose any
information. If you want to perform filtering, it's better to do it after the mapping step.

**Example:**  To perform WES analysis, we recommend you use an unmasked reference genome of the latest releases and
assemblies (e.g. Homo sapiens / GRCh37.75 (unmasked) for human samples).