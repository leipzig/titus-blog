Terabase metagenomics -- the computational side
###############################################

:author: C\. Titus Brown
:tags: bioinformatics,metagenomics
:date: 2010-07-25
:slug: computation-for-terabase-metagenomics
:category: science


The `Terabase Metagenomics meeting
<http://ivory.idyll.org/blog/jul-10/terabase-metagenomics>`__ was good
fun, but I most valued the computational component (because that's
what I do).  Rachel Mackelprang and Rob Knight and I wrote down a list
of the computational issues involved in a petabase metagenomics
project, and that list will help direct my future research.  I'll try
to write up the list sooner rather than later, but in the meantime, I
wanted to write down some general thoughts on the subject of
processing large amounts of data for metagenomics.

In any petabase metagenomics sequencing project focused on probing the
unknown, we are going to have to face two basic computational issues:
assembly and sequence comparison.  Both of these issues require an
all-by-all analysis, or at least the approximation of one.  That is,
as our sampling grows, we're going to want to assemble contiguous DNA
sequences from larger and larger pools of short sequences; this
requires some form of comparison that will scale as the amount of
genomic novelty increases.  We're also going to want to take the
products of sequencing and assembly (raw reads and contigs) and
compare them or derivative products to all known genes.  By and large,
this problem boils down to systematic comparisons of points in a
sparsely but maybe thoroughly occupied n-dimensional space -- an
N-squared problem that has no exact parallelizable solution.

Currently, neither assembly nor gene comparison scales well.
Metagenomic assembly is currently the most disastrous: existing
short-read assemblers use `De Bruijn graphs
<http://www.ncbi.nlm.nih.gov/pubmed/20211242>`__ and require ginormous
amounts of memory (half-terabyte amounts or more) to run.  Gene
comparison is largely done using custom tools (for e.g. rRNA
comparison) or the more general BLAST and HMMER/profile tools; these
tools scale reasonably well for comparisons, but for post-processing
into high-quality phylogenies and gene families, there is no scalable
solution that I know of.

I firmly believe that the intractable-looking problem of assembly is
going to be among the first to fall.  This is because, at least in
theory, metagenomic assembly should be massively parallelizable:
metagenomes contain many *distinct* genomes that don't connect to each
other, so if you can filter or bin the raw sequences into assemblable
subsets, then you will be able to run the individual assemblies on as
many computers as you have original contigs.  This is a logical and
simple approach that simply awaits appropriate heuristic techniques
for division of the reads -- and we'll figure that out sooner rather
than later.  Plus, people much smarter than me are actively working on
better ways of parallelizing the basic De Bruijn graph approach, and
that will inevitably bear fruit.

The marker-gene comparison problem may already be solved.  For any
given gene family (like 16s) you can already do linear-time
comparisons with profile methods like HMMER. Using existing breakdowns
of phase space as a scaffold for new marker data as it arrives should
be possible.  At least, I can think of several ways that it will work,
and Rob Knight is quite optimistic.  (This is his area of expertise,
and since he's a Python programmer, I'm inclined to give him the
benefit of the doubt. ;)

The big problem facing in the future, I think, is the clustering and
discovery of gene families.  As we sequence deeper into the unknown,
we're going to have to do ever larger, more systematic, and more
sensitive comparisons of new data against all known genes, in all
types of ways -- DNA and protein sequence, RNA secondary structure,
and protein structure.  The goal will be to elucidate gene families
(which is an ill-defined concept), their phylogenetic structure, and
as much as possible about their function, across all of the domains of
life.  This is hard because we are explicitly looking for trace
connections -- distant homologies and similarities -- that we haven't
seen yet.  Unlike the marker-gene approach, we don't have good
heuristics for reducing the problem to one of interpolation, and this
means that the problem remains (at first glance) an O(N^2) problem.
Even if you use a prefiltering approach (like BLAST or HMMER 3) you
are still supra-linear, which is deadly when you're talking about
billions or trillions of sequences.

Memory usage is particularly problematic for gene comparison, with
most clustering techniques requiring scads of memory (N^2, in the
extreme) for comparison problems.  This may be something we can
reduce with heuristic approaches, but biology has a way of messing
with us so we can't rely on heuristics up front - *especially* in
large discovery-based projects.

There's an additional complication, though: we really want to reduce
the problem to one of online processing.  As we gather petascale data,
we don't want to do all-by-all comparisons of the new data against the
old data; rather, we want to fit as much of the new data into an
existing structure of classifications, and then sideline the remaining
data for more complex exploration.  This results in an O(N)
classification for the "easy" or known stuff, while dealing with the
hard stuff remains Bad (supra-linear, and perhaps exponential).  For
example, with respect to assembly, we expect to have assemblies of
most of the more abundant soil genomes within the next 2-3 years; we
should be able to pre-screen new data by seeing if it matches to an
existing assembly, and if it does, simply count it (or discard it).
Only the data that matches to no prior assembly would be kept.  A
similar approach can be used for 16s or marker-gene data.  I can think
of ways to approach it for protein-coding genes and non-coding RNA as
well, but I'm very afraid of missing some or many of the unknown
unknowns in our data sets by being too liberal with classification.
On the flip side, being too conservative will inevitably lead to
scaling problems, so there's no way to win.

As with all Big Data problems, the sheer scale of these issues causes
all sorts of annoying data processing and storage problems.  It's
become a ruling principle of my tool development that algorithms must
be constant memory: hence things like our `k-mer filtering approach
<http://ivory.idyll.org/blog/jul-10/kmer-filtering>`__, which trades
accuracy guarantees for pragmatic issues like the ability to run the
software in the first place.

It's worth noting that Moore's Law will not save us.  Neither memory
nor CPU power is growing as fast as sequencing capacity -- some
estimate the exponent on sequencing capacity as being > 5.  (Thank
goodness, disk space will probably keep up!)  We're going to get a
huge increase in the number of cores, but that doesn't really help
with Big Data unless you have robust pleasantly parallel algorithms
that don't require much memory -- which we don't, and in general
probably won't, have.

So, my (our) outlook for the next few years will be: build tools that
scale way better than our existing tools, in all directions (time,
memory, parallelizability); yield guaranteed behavior with respect to
memory consumption in particular; and are optimally sensitive and
specific for the large class of unknowns (new proteins, ncRNA, and
structures) that await us in the world of sequence.

Sounds like fun!

By the way, for those of you who like to think about problem equivalence,
the protein comparison problem ultimately boils down to comparing protein
structures.  We, umm, can't solve that one, either...

--titus
