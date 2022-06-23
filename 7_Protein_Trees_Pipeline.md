# Protein trees

Gene trees aim to represent the evolutionary history of gene families, which evolved from a common ancestor.
Reconciliation of the gene tree against the species trees allow us to distinguish duplication and speciation events,
thus inferring [orthologues and paralogues](https://www.ensembl.org/info/genome/compara/homology_types.html).

There is a clear concordance with reciprocal best approaches in the simple case of unique orthologous genes. However,
the gene tree pipeline is able to find more complex one-to-many and many-to-many relations. This, for instance,
significantly raises the number of Teleost (bony fish) to Mammal orthologues and has even more dramatic effects on
Fly/Mammal or Worm/Mammal orthologous gene predictions. Using this approach, we are also able to "time" duplication
events which produce paralogues by identifying the most recent common ancestor (i.e. taxonomy level) for a given
internal node of the tree.

### Details on tree building

The gene orthology and paralogy prediction pipeline has eight basic steps:

1. Load a [representative translation](http://www.ensembl.org/Help/Glossary?id=346) of each gene from all species used in
   Ensembl. We currently choose the longest
   translation annotated by the CCDS project, if any, or the longest protein-coding translation otherwise.
2. Run an HMM search on [the TreeFam HMM library](https://www.ensembl.org/info/genome/compara/hmm_lib.html) to classify the
   sequences into their families.
3. Cluster the genes that did not have any match into additional families:
   - run [NCBI Blast+1](http://europepmc.org/abstract/MED/20003500) (refined with SmithWaterman) on every orphaned gene
against every other (both self and non-self
species). We use the version 2.2.28, with the parameters: -seg no -max_hsps_per_subject 1 -use_sw_tback -num_threads 1.
   - Build a sparse graph of gene relations based on Blast e-values and generate clusters
using [hcluster_sg2](https://sourceforge.net/p/treesoft/code/HEAD/tree/). Hcluster_sg has
a little insight about the phylogeny of the species (namely: a list of outgroup species), which helps defining pertinent
clusters for the ingroup species. We use yeast (Saccharomyces cerevisiae) as an outgroup (via the -C option) and the
following command line arguments: -m 750 -w 0 -s 0.34 -O. See below for more details.
4.  Large families that would be too complex to analyse are broken down with QuickTree7 to limit them to 1,500 genes.
5.  For each cluster (family), build a multiple alignment based on the protein sequences using either a combination of
multiple aligners, consensified by M-Coffee3 or Mafft4 when the cluster is too large, or MCoffee is too long. We use the
version 9.03.r1318 of M-Coffee, with the aligner set: mafftgins_msa, muscle_msa, kalign_msa, t_coffee_msa, and the Mafft
version 7.113 with the command line options: --auto.
6.  For each aligned cluster, build a phylogenetic tree using TreeBeST5 using the CDS back-translation of the protein
multiple alignment from the original DNA sequences. A rooted tree with internal duplication tags is obtained at this
stage, reconciling it against a species tree inferred from the NCBI taxonomy. See below for more details.
7.  From each gene tree, infer gene pairwise relations of orthology and paralogy types.
8.  A stable ID is assigned to each GeneTree.

### Clustering

hcluster_sg2 performs hierarchical clustering under mean distance. It reads an input file that describes the similarity
between two sequences, and groups two nearest nodes at each step. When two nodes are joined, the distance between the
joined node and all the other nodes are updated by mean distance. This procedure is iterated until one of the three
rules is met:

1. Do not merge cluster A and B if the total number of edges between A and B is smaller than |A|*|B|/3, where |A| and |B|
are the sizes of A and B, respectively. This rule guarantees each cluster is compact.
2. Do not join A to any other cluster if |A| < 500. This rule avoids huge clusters which may cause computational burden for
multiple alignment and tree building as well.
3. Do not join A and B if both A and B contain plant genes or both A and B contain Fungi genes. This rule tries to find
animal gene families. TreeFam clustering is done with outgroups.
Hcluster_sg also introduces an additional edge breaking rule: removes an edge between cluster A and B if the number of
edges between A and B is smaller than |A|*|B|/10. This heuristic rule removes weak relations which are quite unlikely to
be joined at a later step.

As the pipeline has to complete in time for the Ensembl releases, we limit the size of the clusters to 1,500 genes. For
larger clusters, we run Mafft4 and QuickTree7. QuickTree is a very fast and efficient to build an unrooted phylogenetic
tree. We then find the branch that best splits the cluster into two halves. We recursively follow this approach until
each sub-cluster is smaller than 1,500 genes.

Tree building

The CDS back-translated protein alignment (i.e., codon alignment) is used to build five different trees (within
TreeBeST5):

1. a maximum likelihood (ML) tree built, based on the protein alignment with the WAG model, which takes into the account
the species tree
2. a ML tree built using phyml, based on the codon alignment with the Hasegawa-Kishino-Yano (HKY) model, also taking into
account the species tree
3. a neighbour-joining (NJ) tree using p-distance, based on the codon alignment
4. a NJ tree using dN distance, based on the codon alignment
5. a NJ tree using dS distance, based on the codon alignment
For (1) and (2), TreeBeST uses a modified version of phyml release 2.4.5 which takes an input species tree, and tries to
build a gene tree that is consistent with the topology of the species tree. This "species-guided" phyml uses the
original phyml tree-search code. However, the objective function maximised during the tree-search is multiplied by an
extra likelihood factor not found in the original phyml. This extra likelihood factor reflects the number of
duplications and losses inferred in a gene tree, given the topology of the species tree. The species-guided phyml allows
the gene tree to have a topology that is inconsistent with the species tree if the alignment strongly supports this. The
species tree is based on the NCBI taxonomy tree (subject to some modifications depending on new datasets).

The final tree is made by merging the five trees into one consensus tree using the "tree merging" algorithm. This allows
TreeBeST to take advantage of the fact that DNA-based trees often are more accurate for closely related parts of trees
and protein-based trees for distant relationships, and that a group of algorithms may outperform others under certain
scenarios. The algorithm simultaneously merges the five input trees into a consensus tree. The consensus topology
contains clades found in any of the input trees, where the clades chosen are those that minimise the number of
duplications and losses inferred, and have the highest bootstrap support. Branch lengths are estimated for the final
consensus tree based on the DNA alignment, using phyml with the HKY model.