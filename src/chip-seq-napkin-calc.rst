Calculating necessary coverage for ChIP-seq
###########################################

:author: C\. Titus Brown
:tags: science,bioinformatics
:date: 2009-08-19
:slug: chip-seq-napkin-calc
:category: science


OK, so you have a genome -- let's say it's about 1gb in size -- and
you want to do ChIP-seq on a transcription factor that you think
binds ~1000 places in the genome.  You've measured the specificity
of the transcription factor and it seems to enrich about 10-fold
over background (an OK but not fantastic number).  How much sequencing
do you need to do to see a statistically significant signal?

We need two other numbers.  First, what fragment size are you going to
use?  And second, what level of signal over background do you want to
see?

Let's choose a fragment size of 300 bp and look for a 10:1 signal over
background, just for grins.

The math then works out like this: you need roughly 1 sample from each
fragment in the ChIP mixture (background + specific fragments) to get
an average 10x signal with a 10-fold enrichment.  So, if the
background is 1 gb / 300 bp, or 3 x 10^6 fragments, and the signal is
10x enrichment x 1000 locations, or 10^4, then you need N=(3x10^6 +
10^4) samples to hit each background fragment once & each "real"
location 10 times.  Note that (3x10^6 + 10^4) is approximately 3x10^6:
that is, the background dominates the necessary sequencing for
transcription factors that bind in so few places in the genome.

If you want at least one sample (on average) from each of 3x10^6
fragments, you want 3x10^6 samples.  A single Solexa lane yields
approx 2.5x10^6 mappable reads (as of the last data sets I have -- so
it should have improved by now), suggesting that a single Solexa lane
should yield nearly enough samples to see a <deep breath> 10x signal
with a 10:1 enrichment over background by ChIP in a 1 gb genome with a
300 bp fragment size.

Now, are these realistic numbers?  In some cases, yes; in others, I
don't know, but I think so.  Some factoids and guesstimates:

 - the chick genome is 1.2 gb in size, or approximately 1 gb.

 - 300 bp is a "typical" choice for fragment size, although of course
   you'd prefer smaller (for better resolution).

 - a 10x signal should stand out over background, using an
   off-the-cuff estimate of variance (sqrt(10) ~ 3, so 95% of your
   true peaks should have > (10 - 2var == 4) reads associated with
   them).

   Of course, you're going to have a *lot* of background, so there
   will be many peaks that are just coincidental.  I'm not sure how to
   do that napkin-sized calculation -- should I just look three
   standard deviations out (1 +/- 3 = 4) and see how many peaks I'd
   expect in that interval?  It should be less than 1 percent, but
   that's still an awful lot of peaks when you're considering a
   background of 3x10^6 fragments.

 - People (TM) tell me that 10:1 enrichment is not atypical of an OK
   antibody.

So, assuming these numbers are about right, where can you optimize
most easily?  There aren't any non-linear influences, so you can't
look to tweak there; it comes down to what's easy and cheap.

 - Mo' sequencing = mo' bettah, obviously, but each doubling of signal also
   doubles your cost.

 - Increasing your fragment size is cheap and divides out linearly --
   a doubling from a 300 to a 600 bp fragment size will give you twice
   as much signal.  Unfortunately it will also decrease your resolution,
   which will in turn have a significant impact on any bioinformatics
   you might do for finding binding sites.

   But you could always go back and confirm your predictions with
   ChIP-QPCR, which most people do anyway.

 - You could start with a better antibody, too ;).  Of course, usually
   that's not so easy to obtain.

   I seem to recall that the Johnson et al. paper (`pubmed 17540862
   <http://www.ncbi.nlm.nih.gov/pubmed/17540862>`__) used an NRSF
   antibody that was estimated to yield 100x enrichment.  Obviously
   that would significantly help with your signal-to-noise ratio!

I guess I'd look to changing the fragment size first, hmm.  Wonder
what kind of effect that has on the bioinformatics?  I'll have to think
about that more.

I'd appreciate any comments or thoughts people might have... I'm not
even sure this is the right approach to calculating the numbers, but
it makes sense to me!  Please comment, or drop me a note at
ctb@msu.edu.

--titus

p.s. Yes, background is not uniform.  But I have yet to see a good method for
calculating it; most people simply do a negative control and sequence that,
too.
