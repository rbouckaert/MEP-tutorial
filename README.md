---
author: Alexei Drummond, Remco Bouckaert, Walter Xie
level: Beginner
title: Time-stamped data
subtitle: Time-stamped data
beastversion: 2.5.2
---

# Time-stamped data

This tutorial estimates the rate of evolution from a set of virus
sequences which have been isolated at different points in time
(heterochronous or time-stamped data). The data are 129 sequences from
the G (attachment protein) gene of human respiratory syncytial virus
subgroup A (RSVA) from various parts of the world with isolation dates
ranging from 1956-2002 
({% cite Zlateva:2004uq --file MEP-tutorial/master-refs %}, {% cite Zlateva:2005qy --file MEP-tutorial/master-refs %}). 
RSVA causes
infections of the lower respiratory tract causing symptoms that are
often indistinguishable from the common cold. By age 3, nearly all
children will be infected and a small percentage (<3%) will develop
more serious inflammation of the bronchioles requiring hospitalisation.

The aim of this tutorial is to obtain estimates for :

-   the rate of molecular evolution

-   the date of the most recent common ancestor

-   the phylogenetic relationships with measures of statistical support.

The following software will be used in this tutorial:

-   **BEAST** - this package contains the BEAST program, BEAUti,
    DensiTree, TreeAnnotator and other utility programs. This tutorial
    is written for BEAST v`2.5.x`, which has support for multiple
    partitions. It is available for download from\
    [http://www.beast2.org](http://www.beast2.org).

-   **Tracer** - this program is used to explore the output of BEAST
    (and other Bayesian MCMC programs). It graphically and quantitively
    summarises the distributions of continuous parameters and provides
    diagnostic information. At the time of writing, the current version
    is v`1.7`. It is available for download from
    [http://beast.community/tracer](http://beast.community/tracer).

-   **FigTree** - this is an application for displaying and printing
    molecular phylogenies, in particular those obtained using BEAST. At
    the time of writing, the current version is v`1.4.3`. It is
    available for download from [http://beast.community/figtree](http://beast.community/figtree).

## The NEXUS alignment

The data is in a file called
[RSV2.nex](https://github.com/CompEvol/beast2/blob/master/examples/nexus/RSV2.nex?raw=true).
You can find it in the `examples/nexus` directory in the directory
where BEAST was installed. Or click the link to download the data. After
the data is opened in your web browser, right click mouse and save it as
`RSV2.nex`.

This file contains an alignment of 129 sequences from the G gene of RSVA
virus, 629 nucleotides in length. Import this alignment into BEAUti.
Because this is a protein-coding gene we are going to split the
alignment into three partitions representing each of the three codon
positions. To do this we will click the `Split` button at the
bottom of the `Partitions` panel and then select the `1 + 2 + 3
frame 3` from the drop-down menu (Figure \[fig:BEAUti\_split\]).

<figure>
	<a name="fig:BEAUti\_split"></a>
	<img style="width:80.0%;" src="figures//BEAUti_split.png" alt="">
	<figcaption>Figure 1: Split the alignment</figcaption>
</figure>
<br>

This signifies that the first full codon starts at the third nucleotide
in the alignment. This will create three rows in the partitions panel.
You will have to re-link the tree and clock models across the three
partitions (and name them `tree` and `clock` respectively) before
continuing to the next step. The partition panel should now look
something like this:

<figure>
	<a name="fig:BEAUti\_partition"></a>
	<img style="width:80.0%;" src="figures/BEAUti_partition.png" alt="">
	<figcaption>Figure 2: A screenshot of the Partitions tab in BEAUti</figcaption>
</figure>
<br>

## Tip dates 

By default all the taxa are assumed to have a date of zero (i.e. the
sequences are assumed to be sampled at the same time). In this case, the
RSVA sequences have been sampled at various dates going back to the
1950s. The actual year of sampling is given in the name of each taxon
and we could simply edit the value in the Date column of the table to
reflect these. However, if the taxa names contain the calibration
information, then a convenient way to specify the dates of the sequences
in BEAUti is to click the checkbox `Use tip dates` and then use
the `Configure` button at the top of the `Tip Dates` panel.
Clicking this will make a dialog box appear.

<figure>
	<a name="fig:BEAUti\_GuessDates"></a>
	<img style="width:80.0%;" src="figures/BEAUti_GuessDates.png" alt="">
	<figcaption>Figure 3: Guess dates dialog</figcaption>
</figure>
<br>

Select the option to `use everything`, choose `after last` from from
drop-down box and type `s` into the corresponding text box. This will
extract the trailing numbers from the taxon names after the last little
`s`, which are interpreted as the year (in this case since 1900) that
the sample was isolated.

The dates panel should now look something like this:

<figure>
	<a name="fig:BEAUti\_dates"></a>
	<img style="width:80.0%;" src="figures/BEAUti_dates.png" alt="">
	<figcaption>Figure 4: Dates panel</figcaption>
</figure>
<br>

## Setting the substitution model 

We will use the HKY model with empirical base frequencies for all three
partitions. To do this first link the site partitions and then choose
HKY and Empirical from the Subst Model and Frequencies drop-boxes. Also
check the estimate box for the Mutation Rate,which will finally trigger
to check the `Fix mean mutation rate` box.

<figure>
	<a name="fig:BEAUti\_Site\_Model"></a>
	<img style="width:80.0%;" src="figures/BEAUti_Site_Model.png" alt="">
	<figcaption>Figure 5: Site model</figcaption>
</figure>
<br>

Then, hold `shift` key to select all site models on the left side, and
click `OK` to clone the setting from defined site model (Figure
\[fig:cloneFrom\]). Go through each site model, as you can see, their
configurations are same now.

<figure>
	<a name="fig:cloneFrom"></a>
	<img style="width:80.0%;" src="figures/cloneFrom.png" alt="">
	<figcaption>Figure 6: Clone configuration from one site model to others.</figcaption>
</figure>
<br>

## Clock model

We are going to use the strict clock model, which is the default, so 
no changes are necessary in the clock model panel.

## Priors 

To set up the priors, select the `Priors` tab. Choose `Coalescent Constant Population` 
for the tree prior. Set the prior on the clockRate
parameter to a log-normal with `M=-5` and `S=1.25`.

<figure>
	<a name="fig:BEAUti\_priors"></a>
	<img style="width:80.0%;" src="figures/BEAUti_priors.png" alt="">
	<figcaption>Figure 7: Priors</figcaption>
</figure>
<br>

## Setting the MCMC options

For this dataset let’s initially set the chain length to `1000000` as this will
run reasonably quickly on most modern computers. Set the sampling
frequencies for the screen to `10000`, the trace log file to `400` and the trees
file to `400`.

<figure>
	<a name="fig:BEAUti\_mcmc"></a>
	<img style="width:80.0%;" src="figures/BEAUti_mcmc.png" alt="">
	<figcaption>Figure 8: MCMC options</figcaption>
</figure>
<br>

## Running BEAST 

Save the BEAST file (e.g. `RSV2.xml`) and run it in BEAST.

<figure>
	<a name="fig:BEAST"></a>
	<img style="width:80.0%;" src="figures/BEAST.png" alt="">
	<figcaption>Figure 9: A screenshot of BEAST.</figcaption>
</figure>
<br>

Now run BEAST and when it asks for an input file, provide your newly
created XML file as input. BEAST will then run until it has finished
reporting information to the screen. The actual results files are save
to the disk in the same location as your input file. The output to the
screen will look something like this:

```
                        BEAST v2.5.2, 2002-2019
             Bayesian Evolutionary Analysis Sampling Trees
                       Designed and developed by
 Remco Bouckaert, Alexei J. Drummond, Andrew Rambaut & Marc A. Suchard
                                    
                     Department of Computer Science
                         University of Auckland
                        remco@cs.auckland.ac.nz
                        alexei@cs.auckland.ac.nz
                                    
                   Institute of Evolutionary Biology
                        University of Edinburgh
                           a.rambaut@ed.ac.uk
                                    
                    David Geffen School of Medicine
                 University of California, Los Angeles
                           msuchard@ucla.edu
                                    
                      Downloads, Help & Resources:
                           http://beast2.org/
                                    
  Source code distributed under the GNU Lesser General Public License:
                   http://github.com/CompEvol/beast2
                                    
                           BEAST developers:
   Alex Alekseyenko, Trevor Bedford, Erik Bloomquist, Joseph Heled, 
 Sebastian Hoehna, Denise Kuehnert, Philippe Lemey, Wai Lok Sibon Li, 
Gerton Lunter, Sidney Markowitz, Vladimir Minin, Michael Defoin Platel, 
          Oliver Pybus, Tim Vaughan, Chieh-Hsi Wu, Walter Xie
                                    
                               Thanks to:
          Roald Forsberg, Beth Shapiro and Korbinian Strimmer
    ...
    
    ...
         990000     -6108.0939     -5503.4454      -604.6484 1m45s/Msamples
        1000000     -6102.6691     -5505.1198      -597.5493 1m44s/Msamples

Operator                                                   Tuning    #accept    #reject      Pr(m)  Pr(acc|m)
ScaleOperator(StrictClockRateScaler.c:clock)              0.78157       8218      27756    0.03601    0.22844 
UpDownOperator(strictClockUpDownOperator.c:clock)         0.84475        551      35258    0.03601    0.01539 Try setting scaleFactor to about 0.919
ScaleOperator(KappaScaler.s:RSV2_1)                       0.38916        323        934    0.00120    0.25696 
DeltaExchangeOperator(FixMeanMutationRatesOperator)       0.33840       4766      19363    0.02401    0.19752 
ScaleOperator(KappaScaler.s:RSV2_2)                       0.39201        288        901    0.00120    0.24222 
ScaleOperator(KappaScaler.s:RSV2_3)                       0.41649        271        964    0.00120    0.21943 
ScaleOperator(CoalescentConstantTreeScaler.t:tree)        0.71887        273      35495    0.03601    0.00763 Try setting scaleFactor to about 0.848
ScaleOperator(CoalescentConstantTreeRootScaler.t:tree)    0.64576       3140      33308    0.03601    0.08615 Try setting scaleFactor to about 0.804
Uniform(CoalescentConstantUniformOperator.t:tree)               -     193184     166770    0.36014    0.53669 
SubtreeSlide(CoalescentConstantSubtreeSlide.t:tree)       4.11043      28087     152489    0.18007    0.15554 
Exchange(CoalescentConstantNarrow.t:tree)                       -      44602     135445    0.18007    0.24772 
Exchange(CoalescentConstantWide.t:tree)                         -         84      35705    0.03601    0.00235 
WilsonBalding(CoalescentConstantWilsonBalding.t:tree)           -        205      35530    0.03601    0.00574 
ScaleOperator(PopSizeScaler.t:tree)                       0.60390      10084      26007    0.03601    0.27940 

     Tuning: The value of the operator's tuning parameter, or '-' if the operator can't be optimized.
    #accept: The total number of times a proposal by this operator has been accepted.
    #reject: The total number of times a proposal by this operator has been rejected.
      Pr(m): The probability this operator is chosen in a step of the MCMC (i.e. the normalized weight).
  Pr(acc|m): The acceptance probability (#accept as a fraction of the total proposals for this operator).


Total calculation time: 106.096 seconds
End likelihood: -6102.669168760964
```

## Analysing the BEAST output 

Note that the effective sample sizes (ESSs) for many of the logged
quantities are small (ESSs less than 100 will be highlighted in red by
Tracer). This is not good. A low ESS means that the trace contains a lot
of correlated samples and thus may not represent the posterior
distribution well. In the bottom right of the window is a frequency plot
of the samples which is expected given the low ESSs is extremely rough.

If we select the tab on the right-hand-side labelled `Trace` we can view
the raw trace, that is, the sampled values against the step in the MCMC
chain.

<figure>
	<a name="fig:Tracer1"></a>
	<img style="width:80.0%;" src="figures/Tracer1.png" alt="">
	<figcaption>Figure 10: A screenshot of Tracer.</figcaption>
</figure>
<br>

Here you can see how the samples are correlated. There are 2500 samples
in the trace (we ran the MCMC for steps sampling every 400) but adjacent
samples often tend to have similar values. The ESS for the absolute rate
of evolution (clockRate) is about `65` so we are only getting 1
independent sample to every `65 ~ 2500/38` actual samples). With a short
run such as this one, it may also be the case that the default burn-in
of 10% of the chain length is inadequate. Not excluding enough of the
start of the chain as burn-in will render estimates of ESS unreliable.

The simple response to this situation is that we need to run the chain
for longer. Given the lowest ESS (for the constant coalescent) is
`50`, it would suggest that we have to run the chain for at least
4 times the length to get reasonable ESSs that are `>200`. So let’s go
for a chain length of 6000000 and log every 5000. Go back to the `MCMC`
options section in BEAUti, and create a new BEAST XML file with a longer
chain length. Now run BEAST and load the new log file into Tracer (you
can leave the old one loaded for comparison).

Click on the Trace tab and look at the raw trace plot.

<figure>
	<a name="fig:Tracer\_density"></a>
	<img style="width:80.0%;" src="figures/Tracer2.png" alt="">
	<figcaption>Figure 11: tracer</figcaption>
</figure>
<br>

We have chosen options that produce 12000 samples and with an ESS
of about `239` there is still auto-correlation between the samples but
`>239` effectively independent samples will now provide a very good
estimate of the posterior distribution. There are no obvious trends in
the plot which would suggest that the MCMC has not yet converged, and
there are no significant long range fluctuations in the trace which
would suggest poor mixing.

As we are satisfied with the mixing we can now move on to one of the
parameters of interest: substitution rate. Select `clockRate` in the
left-hand table. This is the average substitution rate across all sites
in the alignment. Now choose the density plot by selecting the tab
labeled `Marginal Density`. This shows a plot of the marginal
posterior probability density of this parameter. You should see a plot
similar to this:

<figure>
	<a name="fig:Tracer\_density"></a>
	<img style="width:80.0%;" src="figures/Tracer_density.png" alt="">
	<figcaption>Figure 12: marginal density in tracer</figcaption>
</figure>
<br>

As you can see the posterior probability density is roughly bell-shaped.
There is some sampling noise which would be reduced if we ran the chain
for longer or sampled more often but we already have a good estimate of
the mean and HPD interval. You can overlay the density plots of multiple
traces in order to compare them (it is up to the user to determine
whether they are comparable on the the same axis or not). Select the
relative substitution rates for all three codon positions in the table
to the left (labelled `mutationRate.1`, `mutationRate.2` and
`mutationRate.3`). You will now see the posterior probability densities
for the relative substitution rate at all three codon positions
overlaid:

<figure>
	<a name="fig:Tracer\_relativeRates"></a>
	<img style="width:80.0%;" src="figures/Tracer_relativeRates.png" alt="">
	<figcaption>Figure 13: The posterior probability densities for the relative substitution rates</figcaption>
</figure>
<br>

## Summarising the trees 

Use the program TreeAnnotator to summarise the tree. TreeAnnotator is an application that
comes with BEAST.

<figure>
	<a name="fig:treeannotator"></a>
	<img style="width:80.0%;" src="figures/treeannotator.png" alt="">
	<figcaption>Figure 14: TreeAnnotator for creating a summary tree from a posterior tree set.</figcaption>
</figure>
<br>


Summary trees can be viewed using FigTree (a program separate from BEAST) and 
DensiTree (distributed with BEAST).

<figure>
	<a name="fig:RSV2tree"></a>
	<img style="width:80.0%;" src="figures/RSV2_mcc_tree.png" alt="">
	<figcaption>Figure 15: The Maximum clade credibility tree for the G gene of 129 RSVA-2 viral samples.</figcaption>
</figure>
<br>

Below a DensiTree with clade height bars for clades with over 50% support. Root
canal tree represents maximum clade credibility tree.

<figure>
	<a name="fig:RSV2tree"></a>
	<img style="width:80.0%;" src="figures/DensiTree.png" alt="">
	<figcaption>Figure 16: The posterior tree set visualised in DensiTree.</figcaption>
</figure>
<br>


## Questions 

> In what year did the common ancestor of all RSVA viruses sampled live?
> What is the 95% HPD?

## Bonus section: Bayesian Skyline plot 

We can reconstruct the population history using the Bayesian Skyline
plot. In order to do so, load the XML file into BEAUti, select the
priors-tab and change the tree prior from coalescent with constant
population size to coalescent with Bayesian skyline. Note that an extra
item is added to the priors called `Markov chained population sizes`
which is a prior that ensures dependence between population sizes.

<figure>
	<a name="fig:BEAUti\_priors2"></a>
	<img style="width:80.0%;" src="figures/BEAUti_priors2.png" alt="">
	<figcaption>Figure 17: Priors</figcaption>
</figure>
<br>

By default the number of groups used in the skyline analysis is set to
5, To change this, select menu View/Show Initialization panel and a list
of parameters is shown. Select `bPopSizes.t:tree` and change the
dimension to 3. Likewise, selection `bGroupSizes.t:tree` and change
its dimension to 3. The dimensions of the two parameters should be the
same. More groups mean more population changes can be detected, but it
also means more parameters need to be estimated and the chain runs
longer. The extended Bayesian skyline plot automatically detects the
number of changes, so it could be used as an alternative tree prior.

<figure>
	<a name="fig:BEAUti\_init"></a>
	<img style="width:80.0%;" src="figures/BEAUti_init.png" alt="">
	<figcaption>Figure 18: Initialization panel</figcaption>
</figure>
<br>

This analysis requires a bit longer to converge, so change the MCMC
chain length to 10 million, and the log intervals for the trace-log and
tree-log to 10 thousand. Then, save the file and run BEAST. You can also 
download the log (`RSV2-bsp.log`) and tree (`tree-bsp.trees`) files from 
the `precooked-runs` directory.

To plot the population history, load the log file in tracer and select
the menu Analysis/Bayesian Skyline Reconstruction.

<figure>
	<a name="fig:tracerBSP1"></a>
	<img style="width:80.0%;" src="figures/tracerBSP1.png" alt="">
	<figcaption>Figure 19: Bayesian Skyline Reconstruction in Tracer</figcaption>
</figure>
<br>

A dialog is shown where you can specify the tree file associated with
the log file. Also, since the youngest sample is from 2002, change the
entry for age of youngest tip to 2002.

<figure>
	<a name="fig:tracerBSP2"></a>
	<img style="width:80.0%;" src="figures/tracerBSP2.png" alt="">
	<figcaption>Figure 20: Bayesian Skyline Reconstruction dialog in Tracer</figcaption>
</figure>
<br>

After some calculation, a graph appears showing population history where
the median and 95% HPD intervals are plotted. After selecting the 
`solid interval` checkbox, the graph should look something like this.

<figure>
	<a name="fig:tracerBSP3"></a>
	<img style="width:80.0%;" src="figures/tracerBSP3.png" alt="">
	<figcaption>Figure 21: Bayesian Skyline Reconstruction</figcaption>
</figure>
<br>

## Questions 

>
>1.  By what amount did the effective population size of RSVA grow from
>    1970 to 2002 according to the BSP?
>
>2.  What are the underlying assumptions of the BSP? Are the violated by
>    this data set?
>

## Exercise

Change the Bayesian skyline prior to extended Bayesian skyline plot
(EBSP) prior and run till convergence. EBSP produces an extra log file,
called `EBSP.$(seed).log` where `$(seed)` is replaced by the seed you used
to run BEAST. A plot can be created by running the EBSPAnalyser utility,
and loading the output file in a spreadsheet.

> How many groups are indicated by the EBSP analysis? This is much lower
> than for BSP. How does this affect the population history plots?

# Useful Links

- [Bayesian Evolutionary Analysis with BEAST 2](http://www.beast2.org/book.html) {% cite BEAST2book2014 --file MEP-tutorial/master-refs %}
- BEAST 2 website and documentation: [http://www.beast2.org/](http://www.beast2.org/)
- Join the BEAST user discussion: [http://groups.google.com/group/beast-users](http://groups.google.com/group/beast-users) 

# Relevant References

{% bibliography --cited --file MEP-tutorial/master-refs.bib %}