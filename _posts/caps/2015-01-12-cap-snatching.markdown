---
layout: post
title: "Cap Snatching"
modified:
categories: caps/
excerpt:
tags: [influenza, R]
image:
  feature:
date: 2015-01-12T21:39:10+01:00
---

# Integrating protein synthesis change and cap snatching

Here we compare the proteome synthesis change data and mRNA seq data after infecto to the cap snatching data from [Sikora et al. 2014](http://www.nature.com/srep/2014/140826/srep06181/full/srep06181.html). The cap-snatching data were collected 4h after infection.

In this first quick analysis we focus on the difference between quantitaive measurements of the snatched and non-snatched genes.




## Comparisons

We begin by loading the dataset of host mRNAs that have been mapped to the viral
segments of IAV H2N3 by [Sikora et al. 2014](http://www.nature.com/srep/2014/140826/srep06181/full/srep06181.html). The following plot shows a histogram of mappings of genes to the segments.

<figure>
<img src="/images/caps_files/figure-html/load_caps-1.png" title="" alt="" style="display: block; margin: auto;" />
<figcaption>
The counts of sequences attached to viral mRNA for each segment.
</figcaption>
</figure>

This shows that a cutoff of $0.01$ for the normalized values probably
is a good first take to define a specific snatching of an this mRNA by a viral mRNA.



## Cap snatching compared to synthesis changes

We can now classify the measured proteins into *snatched* and *non-snatched* proteins and
look at the difference in expression in those classes.



First we have to count the number of genes that are actually labeled as snatched with the current cut-off.
We do an automatic labeling in our data table and get the following results:

```
is.snatched    count
------------  ------
FALSE           7167
TRUE           2356
```

These numbers seem very high, but we do some double counting here.
All genes that are snatched by *n* of the viral segments are counted in the group
*snatched* *n* times. And only those that are never snatched count in
the group *not snatched*.



Now we can compare the expression value of the two groups at the different time points.

<figure>
<img src="/images/caps_files/figure-html/mdccapsboxplot-1.png" title="" alt="" style="display: block; margin: auto;" />
</figure>

```
ratio                       time       wilcox.p.value
--------------------------  --------  ---------------
log2.Mallard.Panama         0h.4h           0.0000060
log2.Mallard.Panama         4h.8h           0.0000000
log2.Mallard.Panama         8h.12h          0.0017346
log2.Mallard.Panama         12h.16h         0.2413016
log2.Mallard.no.infection   0h.4h           0.1156335
log2.Mallard.no.infection   4h.8h           0.0000000
log2.Mallard.no.infection   8h.12h          0.0000000
log2.Mallard.no.infection   12h.16h         0.0000000
log2.Panama.no.infection    0h.4h           0.0000022
log2.Panama.no.infection    4h.8h           0.0000000
log2.Panama.no.infection    8h.12h          0.0000000
log2.Panama.no.infection    12h.16h         0.0214226
```

The boxplot shows differences in the median and 25 to 75 quantiles for most time-points.
Most of the differences are significant (two-sided Wilcoxon Rank Sum test).

### Look at the M segment

The viral M1 gene showed especially strong differences in expression in the rest of the data.
Therefore we now look specifically at the M segment disregarding all other snatched genes.

<img src="/images/caps_files/figure-html/mdccapsboxplotM-1.png" title="" alt="" style="display: block; margin: auto;" />

```
ratio                 time       wilcox.p.value
--------------------  --------  ---------------
log2.Mallard.Panama   0h.4h           0.5194123
log2.Mallard.Panama   4h.8h           0.0015529
log2.Mallard.Panama   8h.12h          0.0000291
log2.Mallard.Panama   12h.16h         0.3513023
```

The results are very similar to the above, although the p-values are larger due to the smaller number of samples.
Still, the time points in the middle are significantly different between snatched and non snatched groups.

### Differences between segments


```
## Source: local data frame [8 x 2]
##
##   viral.segment   n()
## 1            HA  5312
## 2             M  5944
## 3           NA.  3704
## 4            NP 11736
## 5            NS 12984
## 6            PA  1776
## 7           PB1  4912
## 8           PB2  3136
```


viral.segment    wilcox.p.value
--------------  ---------------
HA                    0.7345086
M                     0.0012494
NA.                   0.0094414
NP                    0.0000175
NS                    0.0657460
PA                    0.4574130
PB1                   0.0000000
PB2                   0.2478941

There seem to be strong differences between the single segments that should be compared to the
abundance of viral mRNA.

# Compare to RNAseq data from MDC



The mrna data contains 13034 genes with an RPKM values larger than 1 in any of the conditions, which I consider
present here. We calculate $log2$ fold-changes and do a very similar analysis as above.




is.snatched    count
------------  ------
FALSE          12955
TRUE           3402

<img src="/images/caps_files/figure-html/mdccapsboxplot_mrna-1.png" title="" alt="" style="display: block; margin: auto;" />


condition      wilcox.p.value
------------  ---------------
mal_vs_pan          0.2525941
mal_vs_mock         0.0000000
pan_vs_mock         0.0000000

The snatched genes show a significantly stronger downregulation after infection then the rest. But there is no
measurable difference between the two groups for Mallard versus Panama infection.

## Absolute abundances

Do we see differences in the RPKM values of snatched versus non-snatched genes?



is.snatched    count
------------  ------
FALSE          12955
TRUE           3402

<img src="/images/caps_files/figure-html/mdccapsboxplot_mrna_rpkm-1.png" title="" alt="" style="display: block; margin: auto;" />

As expected, the genes that are identified as snatched are also more abundantly
expressed at the mRNA level.

# Conclusion

The group of proteins that was cap snatched in the existing data
was downregulated more strongly than the rest of the transcriptome and also showed a stronger reduction
in protein synthesis.

The difference in RPKM values is especially pronounced, but the snatching measurement is also biased towards more
abundant transcripts.

Interestingly we also see strong differences in synthesis change in the early time points of infection for the genes snatched by the M segment,
which is differently expressed between the two virus strains.
