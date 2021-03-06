Why I don't *really* practice open science
##########################################

:author: C\. Titus Brown
:tags: python,science,bioinformatics,khmer
:date: 2012-04-07
:slug: blog-practicing-open-science
:category: science


I'm a pretty big advocate of anything open -- open source, open
access, and open science, in particular.  I always have been.  And now
that I'm a professor, I've been trying to figure out how to actually
*practice* open science effectively

What is open science?  Well, I think of it as talking regularly about
my unpublished research on the Internet, generally in my blog or on
some other persistent, explicitly public forum.  It should be done
regularly, and it should be done with a certain amount of depth or
self-reflection.  (See, for example, the wunnerful `Rosie Redfield
<http://www.nature.com/news/2011/110809/full/news.2011.469.html>`__
and `Nature's commentary
<http://www.nature.com/news/2011/110809/full/news.2011.469.html>`__
on her blogging of the arsenic debacle & tests thereof.)

Most of my cool, sexy bloggable work is in bioinformatics; I do have a
wet lab, and we're starting to get some neat stuff out of that
(incl. both some ascidian evo-devo and some chick transcriptomics) but
that's not as mature as the computational stuff I'm doing.  And, as
you know if you've seen any of my recent posts on this, I'm pretty
bullish about the computational work we've been doing: the de novo
assembly sequence foo is, frankly, super awesome and seems to solve
most of the scaling problems we face in short-read assembly.  And it
provides a path to solving the problems that it doesn't outright
*solve*.  (I'm talking about `partitioning
<http://ivory.idyll.org/blog/dec-11/kmer-percolation-posted.html>`__
and `digital normalization
<http://ivory.idyll.org/blog/mar-12/diginorm-paper-posted.html>`__.)

While I think we're doing awesome work, I've been uncharacteristically
(for me) shy about proselytizing it prior to having papers ready.  I
occasionally reference it on mailing lists, or in blog posts, or on
twitter, but the most I've talked about the details has been in talks
-- and I've rarely posted those talks online.  When I have, I don't
point out the nifty awesomeness in the talks, either, which of course
means it goes mostly unnoticed.  This seems to be at odds with my
oft-loudly stated position that open-everything is the way to go.
What's going on??  That's what this blog post is about.  I think it
sheds some interesting light on how science is actually practiced, and
why completely open science might waste a lot of people's time.

I'd like to dedicate this blog post to `Greg Wilson
<http://third-bit.com/>`__.  He and I chat irregularly about research,
and he's always seemed interested in what I'm doing but is stymied
because I don't talk about it much in public venues.  And he's been a
bit curious about why.  Which made me curious about why.  Which led to
this blog post, explaining why I think why.  (I've had it written for
a few months, but was waiting until I posted diginorm.)

----

For the past two years or so, I've been unusually focused on the
problem of putting together vast amounts of data -- the problem of de
novo assembly of short-read sequences.  This is because I work on
unusual critters -- soil microbes & non-model animals -- that nobody
has sequenced before, and so we can't make use of prior work.  We're
working in two fields primarily, metagenomics (sampling populations of
wild microbes) and mRNAseq (quantitative sequencing of transcriptomes,
mostly from non-model organisms).

The problems in this area are manifold, but basically boil down to two
linked issues: vast underlying diversity, and dealing with the even
vaster amounts of sequence necessary to thoroughly sample this
diversity.  There's lots of biology motivating this, but the
computational issues are, to first order, dominant: we can generate
more sequence than we can assemble.  This is the problem that
we've basically solved.

A rough timeline of our work is:

 - mid/late 2009: Likit, a graduate student in my lab, points out that
   we're getting way better gene models from assembly of chick mRNAseq
   than from reference-based approaches.  Motivates interest in assembly.

 - mid/late 2009: our lamprey collaborators deliver vast amounts of lamprey
   mRNAseq to us.  Reference genome sucks.  Motivates interest in assembly.

 - mid/late 2009: the JGI starts delivering ridiculous amount of soil
   sequencing data to us (specifically, Adina).  We do everything
   possible to avoid assembly.

 - early 2010: we realize that the least insane approach to analyzing
   soil sequencing data relies on assembly.

 - early 2010: Qingpeng, a graduate student, convinces me that
   existing software for counting k-mers (tallymer, specifically)
   doesn't scale to samples with 20 or 30 billion unique k-mers.  (He
   does this by repeatedly crashing our lab servers.)

 - mid-2010: a computational cabal within the lab (Jason, Adina, Rose)
   figures out how to count k-mers really efficiently, using a
   CountMin Sketch data structure (which we reinvent, BTW, but
   eventually figure out isn't novel.  o well).  We implement this in
   khmer.  (see `k-mer filtering <http://ivory.idyll.org/blog/jul-10/kmer-filtering>`__)

 - mid-2010: We use khmer to figure out just how much Illumina
   sequence sucks.  (see `Illumina read phenomenology <http://ivory.idyll.org/blog/jul-10/illumina-read-phenomenology>`__)

 - mid-2010: Arend joins our computational cabal, bringing detailed
   and random knowledge of graph theory with him.  We invent an
   *actually* novel use of Bloom filters for storing de Bruijn graphs.
   (`blog post
   <http://ivory.idyll.org/blog/dec-11/kmer-percolation-posted.html>`__)
   The idea of partitioning large metagenomic data sets into
   (disconnected) components is born.  (Not novel, as it turns out --
   see `MetaVelvet <http://metavelvet.dna.bio.keio.ac.jp/>`__ and `MetaIDBA <http://bioinformatics.oxfordjournals.org/content/27/13/i94.abstract>`__.)

 - late 2010: Adina and Rose figure out that Illumina suckage prevents
   us from actually getting this to work.

 - first half of 2011: Spent figuring out capacity of de Bruijn graph
   representation (Jason/Arend) and the right parameters to actually
   de-suckify large Illumina data sets (Adina).  We slowly progress
   towards actually being able to partition large metagenomic data
   sets reliably.  A friend browbeats me into applying the same
   technique to his ugly genomic data set, which magically seems to
   solve his assembly problems.

 - fall 2011: the idea of digital normalization is born: throwing away
   redundant data FTW. Early results are very promising (we throw away
   95% of data, get identical assembly) but it doesn't scale assembly
   as well as I'd hoped.

 - October 2011: JGI talk at the `metagenome informatics workshop - SLYT <http://www.youtube.com/watch?v=0Oon5viKMmA&list=PL29441D81BD645568&index=8&feature=plpp_video>`__, where
   we present our ideas of partitioning and digital normalization,
   together, for the first time.  We point out that this potentially
   solves all the scaling problems.

 - November 2011: We figure out the right parameters for digital
   normalization, turning up the awesomeness level dramatically.

 - through present: focus on actually writing this stuff up.  See:
   `de Bruijn graph preprint <http://ivory.idyll.org/blog/dec-11/kmer-percolation-posted.html>`__; `digital normalization preprint <http://ivory.idyll.org/blog/mar-12/diginorm-paper-posted.html>`__.

----

If you read this timeline (yeah, I know it's long, just skim) and look
at the dates of "public disclosure", there's a 12-14 month gap between
talking about k-mer counting (July 2010) and partitioning/etc (Oct
2011, metagenome informatics talk).  And then there's another
several-month gap before I really talk about digital normalization as
a good solution (basically, mid/late January 2012).

Why??

1. I was really freakin' busy actually getting the stuff to work, not
   to mention teaching, traveling, and every now and then actually
   being at home.

2. I was definitely worried about "theft" of ideas.  Looking back,
   this seems a mite ridiculous, but: I'm junior faculty in a
   fast-moving field.  Eeek!  I also have a duty to my grads and
   postdocs to get *them* published, which wouldn't be helped by being
   "scooped".

3. We kept on coming up with new solutions and approaches!  Digital
   normalization didn't exist until August 2011, for example;
   appropriate de-suckifying of Illumina data took until April or May
   of 2011; and proving that it all worked was, frankly, quite tough
   and took until October.  (More on this below.)

4. The code wasn't ready to use, and we hadn't worked out all the
   right parameters, and I wasn't ready to do the support necessary to
   address lots of people using the software.

All of these things meant I didn't talk about things openly on my blog.
Is this me falling short of "open science" ideals??

In my defense, on the "open science" side:

 - I gave plenty of invited talks in this period, including a few (one
   at JGI and one at UMD CBCB) attended by experts who certainly
   understood everything I was saying, probably better than me.

 - I posted some of these talks on `slideshare <http://www.slideshare.net/c.titus.brown/>`__.
   
 - all of our software development has been done on github, under
   github.com/ctb/khmer/.  It's all open source, available, etc.

...but these are sad excuses for open science.  None of these
activities really disseminated my research openly.  Why?

Well, invited talks by junior faculty like me are largely attended out
of curiosity and habit, rather than out of a burning desire to
understand what they're doing; odds are, the faculty in question
hasn't done anything particularly neat, because if they had, they'd be
well known/senior, right?  And who the heck goes
through other people's random presentations on slideshare?  So that's
not really dissemination, especially when the talks are given to an in
group already.

What about the source code?  The "but all my source code is available"
dodge is particularly pernicious.  Nobody, but nobody, looks at other
people's source code in science, unless it's (a) been released, (b)
been documented, and (c) claims to solve YOUR EXACT ACTUAL PROBLEM
RIGHT NOW RIGHT NOW.  The idea that someone is going to come along and
swoop your awesome solution out of your repository seems to me to be
ridiculous; **you'd be lucky to be that relevant, frankly.**

So I don't think any of that is a good way to disseminate what you've
done.  It's necessary for science, but it's not at all sufficient.

--

What do I think *is* sufficient for dissemination?  In my case, how do
you build solutions and write software that *actually has an impact*,
either on the way people think or (even better) on actual practice?
And is it compatible with open science?

1. Write effective solutions to common problems.  The code doesn't
   have to be pretty or even work all that well, but it needs to work
   well enough to run and solve a common problem.

2. Make your software available.  Duh.  It doesn't have to be open
   source, as far as I can tell; I think it should be, but plenty
   of people have restrictive licenses on their code and software,
   and it gets used.

3. Write about it in an open setting.  Blogs and mailing lists are ok;
   SeqAnswers is probably a good place for my field; but honestly,
   you've got to write it all down in a nice, coherent, well-thought
   out body of text.  And if you're doing that?  You might as well
   publish it.  Here is where Open Access really helps, because The
   Google will make it possible for people to find it, read it, and
   then go out and find your software.

The interesting thing about this list is that in addition to all the
less-than-salutary reasons (given above) for not blogging more
regularly about our stuff, I had one *very* good reason for not doing
so.

It's a combination of #1 and #3.

You see, **until near to the metagenome informatics meeting, I didn't
know if partitioning or digital normalization really worked**.  We had
really good indications that partitioning worked, but it was never
solid enough for me to push it strongly as an *actual* solution to big
data problems.  And digital normalization made so much sense that it
almost *had* to work, but, um, proving it was a different problem.
Only in October did we do a bunch of cross-validation that basically
convinced me that partitioning worked *really* well, and only in
November did we figure out how awesome digital normalization was.

So we thought we had solutions, but we weren't sure they were
effective, and we sure didn't have it neatly wrapped in a bow for
other people to use.  So #1 wasn't satisfied.

And, once we did have it working, we started to put a lot of energy
into demonstrating that it worked and writing it up for publication --
#3 -- which took a few months.

In fact, I would actually argue that before October 2011, we could
have wasted people's time by pushing our solutions out for general use
when we basically didn't know if they worked well.  Again, we
*thought* they did, but we didn't really know.

**This is a conundrum for open science: how do you know that someone
else's work is worth your attention?** Research is really hard, and it
may take months or years to nail down all the details; do you really
want to invest significant time or effort in someone else's research
before that's done?  And when they are done -- well, that's when they
submit it for publication, so you might as well just read that first!

--

This is basically the format for open science I'm evolving.  I'll blog
as I see fit, I'll post code and interact with people that I know who
need solutions, but I will wait until we have written a paper to
really open up about what we're doing.  A big part of that is trying
to only push solid science, such that I don't waste other people's
time, energy, and attention.

So: I'm planning to continue to post all my senior-author papers to
arXiv just before their first submission.  The papers will come with
open source and the full set of data necessary to recapitulate our
results.  And I'll blog about the papers, and the code, and the work,
and try to convince people that it's nifty and awesome and solves some
useful problems, or addresses cool science.  But I don't see any much
point in broadly discussing my stuff before a preprint is available.

Is this open science?  I don't really think so.  I'd really like to
talk more openly about our actual research, but for all the reasons
above, it doesn't seem like a good idea.  So I'll stick to trying to
give presentations on our stuff at conferences, and maybe posting the
presentations to slideshare when I think of it, and interacting with
people privately where I can understand what problems they're running
into.

What I'm doing is more about *open access* than open science: people
won't find out details of our work until I think it's ready for
publication, but they also won't have to wait for the review process
to finish.  While I'm not a huge fan of the way peer review is done, I
accept it as a necessary evil for getting my papers into a journal.
By the time I submit a paper, I'll be prepared to argue, confidently
and with actual evidence, that the approach is sound.  If the
reviewers disagree with me and find an actual mistake, I'll fix the
paper and apologize profusely & publicly; if reviewers just want more
experiments done to round out the story, I'll do 'em, but it's easy to
argue that additional experiments generally don't *detract* from the
paper unless they discover flaws (see above, re "apologize").  The
main thing reviewers seem to care about is softening grandiose claims,
anyway; this can be dealt with by (a) not making them and (b) sending
to impact-oblivious journals like PLoS One.  I see no problem with
posting the paper, in any of these circumstances.

Maybe I'm wrong; experience will tell if this is a good idea.  It'll
be interesting to see where I am once we get these papers out... which
may take a year or two, given all the stuff we are writing up.

I've also come to realize that most people don't have the time or
(mental) energy to spare to really come to grips with other people's
research.  We were doing some pretty weird stuff (sketch graph
representations? streaming sketch algorithms for throwing away data?),
and I don't have a prior body of work in this area; most people
probably wouldn't be able to guess at whether I was a quack without
really reading through my code and presentations, and understanding it
in depth.  That takes a *lot* of effort.  And most people
don't really understand the underlying issues anyway; those who do
probably care about them sufficiently to have their own research ideas
and are pursuing them instead, and don't have time to understand mine.
The rest just want a solution that runs and isn't obviously wrong.

In the medium term, the best I can hope for is that preprints and blog
posts will spur people to either use our software and approaches, or
that -- even better -- they will come up with nifty *new* approaches
that solve the problems in some new way that I'd never have thought
of.  And then I can read *their* work and build on *their* ideas.
**This is what we should strive for in science: the shortest
round trip between solid scientific inspiration in different labs.**
This does not necessarily mean open notebooks.

Overall, it's been an interesting personal journey from "blind
optimism" about openness to a more, ahem, "nuanced" set of thoughts
(i.e., I was wrong before :).  I'd be interested to hear what other
people have to say... drop me a note or make a comment.

--titus

p.s. I recognize that it's too early to really defend the claim that
our stuff provides a broad set of solutions.  That's not up to me to
say, for one thing.  For another, it'll take years to prove out.  So
I'm really talking about the hypothetical solution where it *is*
widely useful in practice, and how that intersects with open science
goals & practice.



----

**Legacy Comments**


Posted by Oliver Hofmann on 2012-04-08 at 00:36. 

::

   Terrific writeup, as always. I think the value in open science comes
   from the negative results. If digital normalization would have not
   worked out in the end it probably wouldn't have been written up as a
   blog post, much less as a paper and the next group might have tried
   their luck in a few months.    Whether that warrants all the extra
   work that goes into writing things up all the time and risk of being
   scooped is another question entirely, I guess.


Posted by Titus Brown on 2012-04-08 at 21:15. 

::

   Greg Wilson sent me this awesome video of a talk by Cameron Neylon: <a
   href="http://vimeo.com/35398123">http://vimeo.com/35398123</a> .  It
   is quite relevant.

