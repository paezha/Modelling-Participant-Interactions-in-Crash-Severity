
<!-- README.md is generated from README.Rmd. Please edit that file -->

<!--Equation for README.md generated using this app https://alexanderrodin.com/github-latex-markdown/ -->

# A systematic assessment of the use of opponent variables, data subsetting and hierarchical specification in two-party crash severity analysis

<!-- badges: start -->

<!-- badges: end -->

Antonio Paez (McMaster University)  
Hany Hassan (Louisiana State University)  
Mark Ferguson (McMaster University)  
Saiedeh Razavi (McMaster University)

## Abstract

Road crashes impose an important burden on health and the economy.
Numerous efforts have been undertaken to understand the factors that
affect road collisions in general, and the severity of crashes in
particular. In this literature several strategies have been proposed to
model interactions between parties in a crash, including the use of
variables regarding the other party (or parties) in the collision, data
subsetting, and estimating models with hierarchical components. Since no
systematic assessment has been conducted of the performance of these
strategies, they appear to be used in an ad-hoc fashion in the
literature. The objective of this paper is to empirically evaluate ways
to model party interactions in the context of crashes involving two
parties. To this end, a series of models are estimated using data from
Canada’s National Collision Database. Three levels of crash severity (no
injury/injury/fatality) are analyzed using ordered probit models and
covariates for the parties in the crash and the conditions of the crash.
The models are assessed using predicted shares and classes of outcomes,
and the results highlight the importance of considering opponent effects
in crash severity analysis. The study also suggests that hierarchical
(i.e., multi-level) specifications and subsetting do not necessarily
perform better than a relatively simple single-level model with
opponent-related factors. The results of this study provide insights
regarding the performance of different modelling strategies, and should
be informative to researchers in the field of crash severity.

## Keywords

Crash severity  
Modelling strategies  
Ordinal probit  
Opponent effects  
Canada

<!--- 
_Text based on elsarticle sample manuscript, see [http://www.elsevier.com/author-schemas/latex-instructions#elsarticle](http://www.elsevier.com/author-schemas/latex-instructions#elsarticle)_

This paper is an R markdown document that includes self-contained, reproducible analysis. To reproduce the paper the following files are needed:

1. Interactions-Crash-Severity-v1.Rmd (this R markdown document)

2. Two data files: NCDB_2016.csv and NCDB_2017.csv. These datasets correspond to the 2016 and 2017 releases of Canada's National Crash Database. These data are released by Transport Canada under the Open Government License - Canada version 2.0 [https://open.canada.ca/en/open-government-licence-canada]. The datasets can be obtained here:

https://open.canada.ca/data/en/dataset/1eb9eba7-71d1-4b30-9fb1-30cbdab7e63a

Or from the following repository:

https://github.com/paezha/Modelling-Participant-Interactions-in-Crash-Severity

3. For compiling into a pdf in the style of an Elsevier document: elsarticle.cls, elsevier-harvard.csl, elsevier-harvard-without-titles.csl, elsevier-vancouver.csl, elsevier-without-titles.cls, elsevier-with-titles.csl, elsevier-with-titles-alphabetical.csl, mybibfile.bib
--->

# Introduction

Modelling the severity of injuries to victims of road crashes has been a
preoccupation of transportation researchers, planners, auto insurance
companies, governments, and the general public for decades. One of the
earliest studies to investigate the severity of injuries conditional on
a collision having occurred was by White and Clayton (1972). Kim et
al. (1995) later stated that the “linkages between severity of injury
and driver characteristics and behaviors have not been thoroughly
investigated” (p. 470). Nowadays, there is a burgeoning literature on
this subject, which often relies on multivariate analysis of crash
severity to clarify the way various factors can affect the outcome of an
incident, and to discriminate between various levels of injury.

Crash severity is an active area of research, and one where
methodological developments have aimed at improving the reliability,
accuracy, and precision of models (e.g., Savolainen et al. 2011; Yasmin
and Eluru 2013; Bogue, Paleti, and Balan 2017). Of interest in this
literature is how different parties in a crash interact to influence the
severity of individual outcomes. The importance of these interactions
has been recognized by numerous authors (e.g., Chiou et al. 2013; Lee
and Li 2014; Torrao, Coelho, and Rouphail 2014; Li et al. 2017). Lee and
Li (2014) for instance, note that “\[for\] two-vehicle crashes, most
studies only considered the effects of one vehicle on driver’s injury
severity or the highest injury severity of two drivers. However, it is
expected that driver’s injury severity is not only affected by
characteristics of his/her own vehicle, but also characteristics of a
partner vehicle.” More generally, the severity of the outcome depends,
at least in part, on the characteristics of the parties, and a crash
between two heavy vehicles is likely to have very different outcomes
compared to crash where a heavy vehicle hits a pedestrian.

For the purpose of this paper, we define a party as one or more
individuals travelling in a traffic unit that becomes involved in a
crash. Sometimes the traffic unit is a vehicle, and the party is a
single individual (i.e. a single occupant vehicle); but in other cases,
a party could consist of several individuals (i.e., a driver and one or
more passengers). Other times, the individual *is* the traffic unit, for
instance a pedestrian or a bicyclist. An opponent is a different party
that is involved in the same collision. In the literature, interactions
between parties in a collision are treated by means of different
strategies, including the use of *data subsetting*, *opponent
variables*, and estimating models with *hierarchical components*. These
strategies are not new, however, a systematic comparison between them is
missing from the literature. For this reason, the objective of this
paper is to empirically evaluate different strategies to model party
interactions in crash severity in the context of incidents involving two
parties. More concretely, this research aims to:

1.  Systematize the analysis of party interactions in crash severity
    models. Although every strategy considered here has been used in
    past research, in this paper they are organized in a way that
    clarifies their operation.

2.  Present a data management workflow to prepare a dataset to implement
    analysis of opponent effects.

3.  Provide evidence of the performance of different modelling
    strategies. In particular, the importance of considering
    opponent-level effects and the suitability of single-level models.

4.  Present an example of reproducible research in crash severity
    analysis: all data and code are publicly available from the
    beginning of the peer-review process.

For the assessment we use data from Canada’s National Collision
Database, a database that collects all police-reported collisions in the
country. Using the most recent version of the data set (2017). Three
levels of crash severity (no injury/injury/fatality) are analyzed using
ordered logit models, and covariates for the parties in the crash and
the conditions of the crash. For model assessment, we conduct an
in-sample prediction exercise using the estimation sample (i.e.,
*nowcasting*), and also an out-of-sample prediction exercise using the
data set corresponding to 2016 (i.e., *backcasting*). The models are
assessed using predicted shares and predicted classes of outcomes at the
individual level, using an extensive array of verification statistics.

The rest of this paper is structured as follows. In Section  we discuss
some background matters, and follow this with a concise review of the
modelling strategies used to analyze crash severity . Section  describes
the data requirements, data preprocessing, and the modelling strategies,
along with the results of model estimation. The results of assessing the
models and the discussion of these results is found in Section . We then
present some additional thoughts about the applicability of this
approach in Section  before offering some concluding remarks in Section
.

# Background

Crash severity is often modeled using models for discrete outcomes.
Analysts interested in crash severity have at their disposal an ample
repertoire of models to choose from, including classification techniques
from machine learning (e.g., Iranitalab and Khattak 2017; Khan, Bill,
and Noyce 2015; Chang and Wang 2006; Effati, Thill, and Shabani 2015),
Poisson models for counts (e.g., Ma, Kockelman, and Damien 2008),
unordered logit/probit models (e.g., Tay et al. 2011), as well as
ordered logit/probit models (e.g., Rifaat and Chin 2007), with numerous
variants, such as random parameters/mixed logit (e.g., Aziz, Ukkusuri,
and Hasan 2013; Haleem and Gan 2013), partial proportional odds models
(e.g., Mooradian et al. 2013; Sasidharan and Menendez 2014), and the use
of copulas (e.g., Wang et al. 2015). Recent reviews of methods include
Savolainen et al. (2011), Yasmin and Eluru (2013), and Mannering et
al. (2016).

Irrespective of the modelling framework employed, models of crash
severity often include variables in several categories, as shown with
examples in Table  (also see Montella et al. 2013). Many crash databases
and analyses also account for the multi-event nature of many crashes.
Individuals in the crash may have had different roles depending on their
situation, with some acting as operators of a vehicle (i.e., drivers,
bicyclists), while others were passengers. They also may differ
depending on what type of traffic unit they were, for example
pedestrians, or operators/passengers of a vehicle. The multiplicity of
roles makes for complicated modelling decisions when trying to
understand the severity of injuries; for example, what is the unit of
analysis, the person, the traffic unit, or the collision? Not
surprisingly, it is possible to find examples of studies that adopt
different perspectives. A common simplifying strategy in model
specification is to consider only *drivers* and/or only *single-vehicle*
crashes (e.g., Kim et al. 2013; Lee and Li 2014; Gong and Fan 2017;
Osman, Mishra, and Paleti 2018). This strategy reduces the dimensions of
the event, and it becomes possible, for example, to equate the traffic
unit to the person for modelling purposes.

The situation becomes more complex when dealing with events that involve
two traffic units (e.g., Torrao, Coelho, and Rouphail 2014; Wang et al.
2015) and multi-traffic unit crashes (e.g., Wu et al. 2014; Bogue,
Paleti, and Balan 2017). Different strategies have been developed to
study these, more complex events. A number of studies advocate the
estimation of separate models for different parties, individuals, and/or
situations. In this way, Wang and Kockelman (2005) estimated models for
single-vehicle and two-vehicle crashes, while Savolainen and Mannering
(2007) estimated models for single-vehicle and multi-vehicle crashes.
More recently, Duddu et al. (2018) and Penmetsa et al. (2017) presented
research that estimated separate models for at-fault and not-at-fault
drivers. The strategy of estimating separate models relies on
*subsetting* the data set, although it is possible to link the relevant
models more tightly by means of a common covariance structure, as is the
case of bivariate models (e.g., Chiou et al. 2013; Chen, Song, and Ma
2019) or models with copulas (e.g., Rana, Sikder, and Pinjari 2010;
Shamsunnahar et al. 2014; Wang et al. 2015).

A related strategy to specify crash severity models is to organize the
data in such a way that it is possible to introduce *opponent effects*.
There are numerous examples of studies that consider at least some
characteristics of the opposite party (or parties) in two- or
multi-vehicle crashes. For example, Wang and Kockelman (2005) considered
the type of the opposite vehicle in their model for two-vehicle
collisions. Similarly, Torrao et al. (2014) included in their analysis
the age, wheelbase, weight, and engine size of the opposite vehicle,
while Bogue et al. (2017) used the body type of the opposite vehicle.
Penmetsa et al. (2017) and Duddu et al. (2018) are two of the most
comprehensive examples of using opponent’s information, as they included
attributes of individuals in the opposite party (their physical
condition, sex, and age), as well as characteristics of the other
party’s traffic unit (the vehicle type of the opponent). The twin
strategies of subsetting the sample and using the attributes of the
opponent are not mutually exclusive, but neither are they used
consistently together, as a scan of the literature reveals.

Another strategy is to introduce *hierarchical components* in the model,
a technique widely used in the hierarchical or multi-level modelling
literature. This involves considering observations as being nested at
different levels: individuals nested in traffic units, which in turn are
nested in accidents, as an example.

In this paper we consider three general modelling strategies, as
follows:

  - Strategy 1. Introducing opponent-related factors

  - Strategy 2. Single-level model and multi-level (hierarchical) model
    specifications

  - Strategy 3. Full sample and sample subsetting

These are discussed in more detail in the following section.

<table>

<caption>

Categories of variables used in the analysis of crash severity with
examples

</caption>

<thead>

<tr>

<th style="text-align:left;">

Category

</th>

<th style="text-align:left;">

Examples

</th>

</tr>

</thead>

<tbody>

<tr>

<td style="text-align:left;">

Human-factors

</td>

<td style="text-align:left;">

Attributes of individuals in the crash, e.g., injury status, age,
gender, licensing status, professional driver status

</td>

</tr>

<tr>

<td style="text-align:left;">

Traffic unit-factors

</td>

<td style="text-align:left;">

Attributes of the traffic unit, e.g., type of traffic unit (pedestrian,
car, motorcycle, etc.), maneuver, etc.

</td>

</tr>

<tr>

<td style="text-align:left;">

Environmental-factors

</td>

<td style="text-align:left;">

Attributes of the crash, e.g., location, weather conditions, light
conditions, number of parties, etc.

</td>

</tr>

<tr>

<td style="text-align:left;">

Road-factors

</td>

<td style="text-align:left;">

Attributes of the road, e.g., surface condition, grade, geometry, etc.

</td>

</tr>

<tr>

<td style="text-align:left;">

Opponent-related factors

</td>

<td style="text-align:left;">

Attributes of the opponent, e.g., age of opponent, gender of opponent,
opponent vehicle type, etc.

</td>

</tr>

</tbody>

</table>

# Methods

## Choice of model

Before describing the modelling strategies, it is important to explain
our choice of model. There have already been comparisons between
different models. Yasmin and Eluru (2013), for instance, conducted an
extensive comparison of models for discrete outcomes in crash severity
analysis, and found only small differences in the performance of
unordered models and ordered models; however, ordered models are usually
more parsimonious since only one latent functions needs to be estimated
for all outcomes, as opposed to one for each outcome in unordered
models. Bogue et al. (2017) also compared unordered and ordered models
in the form of the mixed multinomial logit and a modified rank ordered
logit, respectively, and found that the ordered model performed best. To
keep the empirical assessment manageable, in this paper we will consider
only the ordinal logit model, and will comment on potential extensions
in Section .

The ordinal model is a latent-variable approach, whereby the severity of
the crash (observed) is linked to an underlying latent variable that is
a function of the variables of interest, as follows:

<!--
\begin{equation}
\label{eq:latent-function}
y_{itk}^*=\sum_{l=1}^L\alpha_lp_{itkl} + \sum_{m=1}^M\beta_mu_{tkm} + \sum_{q=1}^Q\kappa_qc_{kq} + \epsilon_{itk}
\end{equation}

The left-hand side of the expression above ($y_{itk}^*$) is a latent (unobservable) variable that is associated with the severity of crash $k$ ($k=1,\cdots,K$) for individual $i$ in traffic unit $t$. The right-hand side of the expression is split in four parts. The first part gathers $l=1,\cdots,L$ human-factors $p$ for individual $i$ in traffic unit $t$ and crash $k$; these could relate to the person (e.g., age, gender, and road user class). The second part gathers $m=1,\cdots,M$ attributes $u$ related to traffic unit $t$ in crash $k$; these could be items such as maneuver or vehicle type. The third part gathers $q=1,\cdots,Q$ attributes $c$ related to the crash $k$, including environmental-factors and road-factors, such as weather conditions, road alignment, and type of surface. Lastly, the fourth element is a random term specific to individual $i$ in traffic unit $t$ and crash $k$. The function consists of a total of $Z=L+M+Q$ covariates and associated parameters.
-->

![](https://render.githubusercontent.com/render/math?math=%5Cbegin%7Bequation%7D%20%5Clabel%7Beq%3Alatent-function%7D%20y_%7Bitk%7D%5E*%3D%5Csum_%7Bl%3D1%7D%5EL%5Calpha_lp_%7Bitkl%7D%20%2B%20%5Csum_%7Bm%3D1%7D%5EM%5Cbeta_mu_%7Btkm%7D%20%2B%20%5Csum_%7Bq%3D1%7D%5EQ%5Ckappa_qc_%7Bkq%7D%20%2B%20%5Cepsilon_%7Bitk%7D%20%5Cend%7Bequation%7D)

The left-hand side of the expression above
(![y\_{itk}^\*](https://render.githubusercontent.com/render/math?math=y_%7Bitk%7D%5E*))
is a latent (unobservable) variable that is associated with the severity
of crash *k*
(![k=1,,K](https://render.githubusercontent.com/render/math?math=k%3D1%2C%5Ccdots%2CK))
for individual *i* in traffic unit *t*. The right-hand side of the
expression is split in four parts. The first part gathers
\(l=1,\cdots,L\)![l=1,,L](https://render.githubusercontent.com/render/math?math=l%3D1%2C%5Ccdots%2CL)
human-factors *p* for individual *i* in traffic unit *t* and crash *k*;
these could relate to the person (e.g., age, gender, and road user
class). The second part gathers
![m=1,,M](https://render.githubusercontent.com/render/math?math=m%3D1%2C%5Ccdots%2CM)
attributes *u* related to traffic unit *t* in crash *k*; these could be
items such as maneuver or vehicle type. The third part gathers
![q=1,,Q](https://render.githubusercontent.com/render/math?math=q%3D1%2C%5Ccdots%2CQ)
attributes *c* related to the crash *k*, including environmental-factors
and road-factors, such as weather conditions, road alignment, and type
of surface. Lastly, the fourth element is a random term specific to
individual *i* in traffic unit *t* and crash *k*. The function consists
of a total of
![Z=L+M+Q](https://render.githubusercontent.com/render/math?math=Z%3DL%2BM%2BQ)
covariates and associated parameters.

For conciseness, in what follows we will abbreviate the function as
follows:

<!--
\begin{equation}
\label{eq:latent-function-compact}
y_{itk}^*=\sum_{z=1}^Z\theta_zx_{itkz} + \epsilon_{itk}
\end{equation}
-->

![](https://render.githubusercontent.com/render/math?math=%5Cbegin%7Bequation%7D%20%5Clabel%7Beq%3Alatent-function-compact%7D%20y_%7Bitk%7D%5E*%3D%5Csum_%7Bz%3D1%7D%5EZ%5Ctheta_zx_%7Bitkz%7D%20%2B%20%5Cepsilon_%7Bitk%7D%20%5Cend%7Bequation%7D)

The latent variable is not observed directly, but it is possible to
posit a probabilistic relationship with the outcome
![y\_{itk}](https://render.githubusercontent.com/render/math?math=y_%7Bitk%7D)
(the severity of crash *k* for individual *i* in traffic unit *t*).
Depending on the characteristics of the data and the assumptions made
about the random component of the latent function different models can
be obtained. For example, if crash severity is coded as a variable with
three outcomes (e.g., property damage only/injury/fatal), we can relate
the latent variable to the outcome as follows:

<!--
\begin{equation}
\label{eq:latent-function-ordered-outcomes}
y_{itk} = 
\begin{cases}
\text{fatality} & \text{if } y_{itk}^*> \mu_2\\
\text{injury} & \text{if } \mu_1< y_{itk}^*< \mu_2\\
\text{PDO} & \text{if } y_{itk}^*< \mu_1
\end{cases}
\end{equation}
-->

![](https://render.githubusercontent.com/render/math?math=%5Cbegin%7Bequation%7D%20%5Clabel%7Beq%3Alatent-function-ordered-outcomes%7D%20y_%7Bitk%7D%20%3D%20%20%5Cbegin%7Bcases%7D%20%5Ctext%7Bfatality%7D%20%26%20%5Ctext%7Bif%20%7D%20y_%7Bitk%7D%5E*%3E%20%5Cmu_2%5C%5C%20%5Ctext%7Binjury%7D%20%26%20%5Ctext%7Bif%20%7D%20%5Cmu_1%3C%20y_%7Bitk%7D%5E*%3C%20%5Cmu_2%5C%5C%20%5Ctext%7BPDO%7D%20%26%20%5Ctext%7Bif%20%7D%20y_%7Bitk%7D%5E*%3C%20%5Cmu_1%20%5Cend%7Bcases%7D%20%5Cend%7Bequation%7D)

where
![\_1](https://render.githubusercontent.com/render/math?math=%5Cmu_1)
and
![\_2](https://render.githubusercontent.com/render/math?math=%5Cmu_2)
are estimable thresholds. Due to the stochastic nature of the latent
function, the outcome of the crash is not fully determined. However, we
can make the following probability statements:

<!--
\begin{equation}
\label{eq:probability-ordered-outcomes}
\begin{array}{rcl}\
P(y_{itk} = \text{PDO}) &=& 1 - P(y_{itk} = \text{injury}) - P(y_{itk} = \text{fatality})\\ 
P(y_{itk} = \text{injury}) &=& P(\mu_1 - \sum_{z=1}\theta_zp_{itkz} < \epsilon_{itk} < \mu_2 - \sum_{z=1}\theta_zp_{itkz})\\
P(y_{itk} = \text{fatality}) &=& P(\epsilon_{itk} < \mu_1 - \sum_{z=1}\theta_zp_{itkz})
\end{array}
\end{equation}
-->

![](https://render.githubusercontent.com/render/math?math=%5Cbegin%7Bequation%7D%20%5Clabel%7Beq%3Aprobability-ordered-outcomes%7D%20%5Cbegin%7Barray%7D%7Brcl%7D%5C%20P\(y_%7Bitk%7D%20%3D%20%5Ctext%7BPDO%7D\)%20%26%3D%26%201%20-%20P\(y_%7Bitk%7D%20%3D%20%5Ctext%7Binjury%7D\)%20-%20P\(y_%7Bitk%7D%20%3D%20%5Ctext%7Bfatality%7D\)%5C%5C%20%20P\(y_%7Bitk%7D%20%3D%20%5Ctext%7Binjury%7D\)%20%26%3D%26%20P\(%5Cmu_1%20-%20%5Csum_%7Bz%3D1%7D%5Ctheta_zp_%7Bitkz%7D%20%3C%20%5Cepsilon_%7Bitk%7D%20%3C%20%5Cmu_2%20-%20%5Csum_%7Bz%3D1%7D%5Ctheta_zp_%7Bitkz%7D\)%5C%5C%20P\(y_%7Bitk%7D%20%3D%20%5Ctext%7Bfatality%7D\)%20%26%3D%26%20P\(%5Cepsilon_%7Bitk%7D%20%3C%20%5Cmu_1%20-%20%5Csum_%7Bz%3D1%7D%5Ctheta_zp_%7Bitkz%7D\)%20%5Cend%7Barray%7D%20%5Cend%7Bequation%7D)

If the random terms are assumed to follow the logistic distribution, the
ordered logit model is obtained; if the normal distribution, then the
ordered probit model. Estimation methods for these models are very
well-established (e.g., Maddala 1986; Train 2009). There are numerous
variations of the basic modelling framework above, including
hierarchical models, bivariate models, multinomial models, and Bayesian
models, among others (see Savolainen et al. 2011 for a review of
methods).

## Strategy 1: opponent-related factors

When opponent-related variables are included, the function is augmented
as follows:

<!--
\begin{equation}
\label{eq:latent-function-with-opponent-variables}
y_{itk}^*=\sum_{l=1}^L\alpha_lp_{itkl} + \sum_{m=1}^M\beta_mu_{tkm} + \sum_{q=1}^Q\kappa_qc_{kq} + \sum_{r=1}^R\delta_ro_{jvkr} + \epsilon_{itk}
\end{equation}
-->

![](https://render.githubusercontent.com/render/math?math=%5Cbegin%7Bequation%7D%20%5Clabel%7Beq%3Alatent-function-with-opponent-variables%7D%20y_%7Bitk%7D%5E*%3D%5Csum_%7Bl%3D1%7D%5EL%5Calpha_lp_%7Bitkl%7D%20%2B%20%5Csum_%7Bm%3D1%7D%5EM%5Cbeta_mu_%7Btkm%7D%20%2B%20%5Csum_%7Bq%3D1%7D%5EQ%5Ckappa_qc_%7Bkq%7D%20%2B%20%5Csum_%7Br%3D1%7D%5ER%5Cdelta_ro_%7Bjvkr%7D%20%2B%20%5Cepsilon_%7Bitk%7D%20%5Cend%7Bequation%7D)

This function includes one additional summation compared to Equation .
This summation gathers
![r=1,,R](https://render.githubusercontent.com/render/math?math=r%3D1%2C%5Ccdots%2CR)
attributes *o* related to individual *j* in traffic unit *v* that was an
opposite party to individual *i* in traffic unit *t* in crash *k*. These
attributes could be individual characteristics of the operator of the
opposite traffic unit (such as age and gender) and/or characteristics of
the opposite traffic unit (such as vehicle type or weight). To qualify
as an opposite party, individual *j* must have been an individual in
crash *k* but operating traffic unit
![vt](https://render.githubusercontent.com/render/math?math=v%5Cne%20t).
Sometimes the person *is* the traffic unit, as is the case of a
pedestrian. And we exclude passengers of vehicles as opponents, since
they do not operate the traffic unit. In case the opponent attributes
include only characteristics of the traffic unit, the condition for the
traffic unit to be an opponent is that it was part of crash *k* and was
different from *t*. After introducing this new set of terms, the latent
function now consists of a total of
![Z=L+M+Q+R](https://render.githubusercontent.com/render/math?math=Z%3DL%2BM%2BQ%2BR)
covariates and associated parameters.

## Strategy 2: hierarchical model specification

We can choose to conceptualize the event leading to the outcome as a
hierarchical process. There are a few different ways of doing this. For
example, the hierarchy could be based on individuals in traffic units.
In this case, we can rewrite the latent function as follows:

<!--
\begin{equation}
\label{eq:latent-function-with-hierarchical-traffic-unit}
y_{itk}^*=\sum_{m=1}^M\beta_mu_{tkm} + \sum_{q=1}^Q\kappa_qc_{kq} + \sum_{r=1}^R\delta_ro_{jvkr} + \epsilon_{itk}
\end{equation}
-->

![](https://render.githubusercontent.com/render/math?math=%5Cbegin%7Bequation%7D%20%5Clabel%7Beq%3Alatent-function-with-hierarchical-traffic-unit%7D%20y_%7Bitk%7D%5E*%3D%5Csum_%7Bm%3D1%7D%5EM%5Cbeta_mu_%7Btkm%7D%20%2B%20%5Csum_%7Bq%3D1%7D%5EQ%5Ckappa_qc_%7Bkq%7D%20%2B%20%5Csum_%7Br%3D1%7D%5ER%5Cdelta_ro_%7Bjvkr%7D%20%2B%20%5Cepsilon_%7Bitk%7D%20%5Cend%7Bequation%7D)

The coefficients of the traffic unit nest the individual attributes as
follows. For any given coefficient *m*:

<!--
\begin{equation}
\label{eq:hierarchical-traffic-unit-coefficients}
\beta_{m}=\sum_{l=1}^L\beta_{ml}p_{itkl} 
\end{equation}
-->

![](https://render.githubusercontent.com/render/math?math=%5Cbegin%7Bequation%7D%20%5Clabel%7Beq%3Ahierarchical-traffic-unit-coefficients%7D%20%5Cbeta_%7Bm%7D%3D%5Csum_%7Bl%3D1%7D%5EL%5Cbeta_%7Bml%7Dp_%7Bitkl%7D%20%20%5Cend%7Bequation%7D)

Therefore, the corresponding term in the latent function becomes
(assuming that ![p\_{itk1} =
1](https://render.githubusercontent.com/render/math?math=p_%7Bitk1%7D%20%3D%201),
i.e., it is a constant term):

<!--
\begin{equation}
\label{eq:hierarchical-traffic-unit-coefficients}
\begin{array}{rcl}\
\beta_{m}u_{tkm} &=& \big( \beta_{m1} + \beta_{m2}p_{itk2} + \cdots + \beta_{mL}p_{itkL}\big)u_{tkm}\\ 
&=& \beta_{m1}u_{tkm} + \beta_{m2}p_{itk2}u_{tkm} + \cdots + \beta_{mL}p_{itkL}u_{tkm}
\end{array}
\end{equation}
-->

![](https://render.githubusercontent.com/render/math?math=%5Cbegin%7Bequation%7D%20%5Clabel%7Beq%3Ahierarchical-traffic-unit-coefficients%7D%20%5Cbegin%7Barray%7D%7Brcl%7D%5C%20%5Cbeta_%7Bm%7Du_%7Btkm%7D%20%26%3D%26%20%5Cbig\(%20%5Cbeta_%7Bm1%7D%20%2B%20%5Cbeta_%7Bm2%7Dp_%7Bitk2%7D%20%2B%20%5Ccdots%20%2B%20%5Cbeta_%7BmL%7Dp_%7BitkL%7D%5Cbig\)u_%7Btkm%7D%5C%5C%20%20%26%3D%26%20%5Cbeta_%7Bm1%7Du_%7Btkm%7D%20%2B%20%5Cbeta_%7Bm2%7Dp_%7Bitk2%7Du_%7Btkm%7D%20%2B%20%5Ccdots%20%2B%20%5Cbeta_%7BmL%7Dp_%7BitkL%7Du_%7Btkm%7D%20%5Cend%7Barray%7D%20%5Cend%7Bequation%7D)

As an alternative, the nesting unit could be the interaction
person-opponent, in which case the opponent-level attributes are nested
in the following fashion:

<!--
\begin{equation}
\label{eq:latent-function-with-opponent-variables}
y_{itk}^*=\sum_{l=1}^L\alpha_lp_{itkl} + \sum_{m=1}^M\beta_mu_{tkm} + \sum_{q=1}^Q\kappa_qc_{kq} + \epsilon_{itk}
\end{equation}
-->

![](https://render.githubusercontent.com/render/math?math=%5Cbegin%7Bequation%7D%20%5Clabel%7Beq%3Alatent-function-with-opponent-variables%7D%20y_%7Bitk%7D%5E*%3D%5Csum_%7Bl%3D1%7D%5EL%5Calpha_lp_%7Bitkl%7D%20%2B%20%5Csum_%7Bm%3D1%7D%5EM%5Cbeta_mu_%7Btkm%7D%20%2B%20%5Csum_%7Bq%3D1%7D%5EQ%5Ckappa_qc_%7Bkq%7D%20%2B%20%5Cepsilon_%7Bitk%7D%20%5Cend%7Bequation%7D)

with any person-level coefficient *l* that we wish to expand defined as
follows:

<!--
\begin{equation}
\label{eq:hierarchical-traffic-unit-coefficients}
\alpha_{l}=\sum_{r=1}^R\alpha_{lr}o_{jvkr}
\end{equation}
-->

![](https://render.githubusercontent.com/render/math?math=%5Cbegin%7Bequation%7D%20%5Clabel%7Beq%3Ahierarchical-traffic-unit-coefficients%7D%20%5Calpha_%7Bl%7D%3D%5Csum_%7Br%3D1%7D%5ER%5Calpha_%7Blr%7Do_%7Bjvkr%7D%20%5Cend%7Bequation%7D)

with the same conditions as before, that
![ji](https://render.githubusercontent.com/render/math?math=j%5Cne%20i)
is the operator of traffic unit
![vt](https://render.githubusercontent.com/render/math?math=v%5Cne%20t).
The corresponding term in the latent function is now (assuming that
![o\_{jvk1}=1](https://render.githubusercontent.com/render/math?math=o_%7Bjvk1%7D%3D1),
i.e., it is a constant term):

<!--
\begin{equation}
\label{eq:hierarchical-traffic-unit-coefficients}
\begin{array}{rcl}\
\alpha_{l}p_{itkl} &=& \big(\alpha_{l1} + \alpha_{l2}o_{jvk2} + \cdots + \alpha_{lR}o_{jvkR} \big)p_{itkl}\\
&=& \alpha_{l1}p_{itkl} + \alpha_{l2}o_{jvk2}p_{itkl} + \cdots + \alpha_{lR}o_{jvkR}p_{itkl}
\end{array} 
\end{equation}
-->

![](https://render.githubusercontent.com/render/math?math=%5Cbegin%7Bequation%7D%20%5Clabel%7Beq%3Ahierarchical-traffic-unit-coefficients%7D%20%5Cbegin%7Barray%7D%7Brcl%7D%5C%20%5Calpha_%7Bl%7Dp_%7Bitkl%7D%20%26%3D%26%20%5Cbig\(%5Calpha_%7Bl1%7D%20%2B%20%5Calpha_%7Bl2%7Do_%7Bjvk2%7D%20%2B%20%5Ccdots%20%2B%20%5Calpha_%7BlR%7Do_%7BjvkR%7D%20%5Cbig\)p_%7Bitkl%7D%5C%5C%20%26%3D%26%20%5Calpha_%7Bl1%7Dp_%7Bitkl%7D%20%2B%20%5Calpha_%7Bl2%7Do_%7Bjvk2%7Dp_%7Bitkl%7D%20%2B%20%5Ccdots%20%2B%20%5Calpha_%7BlR%7Do_%7BjvkR%7Dp_%7Bitkl%7D%20%5Cend%7Barray%7D%20%20%5Cend%7Bequation%7D)

Discerning readers will identify this model specification strategy as
Casetti’s expansion method (Casetti 1972; Roorda et al. 2010). This is a
deterministic strategy for modelling contextual effects which, when
augmented with random components, becomes the well-known multi-level
modelling method (Hedeker and Gibbons 1994, more on this in Section ).
It is worthwhile to note that higher-order hierarchical effects are
possible; for instance, individual attributes nested within traffic
units, which in turn are nested within collisions. We do not explore
higher-level hierarchies further in the current paper.

## Strategy 3: sample subsetting

The third model specification strategy that we will consider is
subsetting the sample. This is applicable in conjunction with any of the
other strategies discussed above. In essence, we define the latent
function with restrictions as follows. Consider a continuous variable,
e.g., age of person, and imagine that we wish to concentrate the
analysis on older adults (e.g., Dissanayake and Lu 2002). The latent
function is defined as desired (see above), however, the following
restriction is applied to the sample:

<!--
\begin{equation}
\label{eq:sampling-age}
\text{Age of individual } i \text{ in traffic unit } t \text{ in crash } k = 
\begin{cases}
\ge 65 & \text{use record } itk\\
< 65 & \text{do not use record } itk
\end{cases}
\end{equation}
-->

![](https://render.githubusercontent.com/render/math?math=%5Cbegin%7Bequation%7D%20%5Clabel%7Beq%3Asampling-age%7D%20%5Ctext%7BAge%20of%20individual%20%7D%20i%20%5Ctext%7B%20in%20traffic%20unit%20%7D%20t%20%5Ctext%7B%20in%20crash%20%7D%20k%20%3D%20%20%5Cbegin%7Bcases%7D%20%5Cge%2065%20%26%20%5Ctext%7Buse%20record%20%7D%20itk%5C%5C%20%3C%2065%20%26%20%5Ctext%7Bdo%20not%20use%20record%20%7D%20itk%20%5Cend%7Bcases%7D%20%5Cend%7Bequation%7D)

Suppose instead that we are interested in crashes by or against a
specific type of traffic unit (e.g., pedestrians, Amoh-Gyimah et al.
2017):

<!--
\begin{equation}
\label{eq:sampling-pedestrian}
\text{Road user class of individual } i \text{ in traffic unit } t \text{ in crash } k = 
\begin{cases}
\text{Pedestrian} & \text{use record } itk\\
\text{Not pedestrian} & \text{do not use record } itk
\end{cases}
\end{equation}
-->

![](https://render.githubusercontent.com/render/math?math=%5Cbegin%7Bequation%7D%20%5Clabel%7Beq%3Asampling-pedestrian%7D%20%5Ctext%7BRoad%20user%20class%20of%20individual%20%7D%20i%20%5Ctext%7B%20in%20traffic%20unit%20%7D%20t%20%5Ctext%7B%20in%20crash%20%7D%20k%20%3D%20%20%5Cbegin%7Bcases%7D%20%5Ctext%7BPedestrian%7D%20%26%20%5Ctext%7Buse%20record%20%7D%20itk%5C%5C%20%5Ctext%7BNot%20pedestrian%7D%20%26%20%5Ctext%7Bdo%20not%20use%20record%20%7D%20itk%20%5Cend%7Bcases%7D%20%5Cend%7Bequation%7D)

or:

<!--
\begin{equation}
\label{eq:sampling-pedestrian-opponent}
\text{Road user class of individual } j \text{ in traffic unit } v \text{ in crash } k = 
\begin{cases}
\text{Pedestrian} & \text{use record } jvk\\
\text{Not pedestrian} & \text{do not use record } jvk
\end{cases}
\end{equation}
-->

![](https://render.githubusercontent.com/render/math?math=%5Cbegin%7Bequation%7D%20%5Clabel%7Beq%3Asampling-pedestrian-opponent%7D%20%5Ctext%7BRoad%20user%20class%20of%20individual%20%7D%20j%20%5Ctext%7B%20in%20traffic%20unit%20%7D%20v%20%5Ctext%7B%20in%20crash%20%7D%20k%20%3D%20%20%5Cbegin%7Bcases%7D%20%5Ctext%7BPedestrian%7D%20%26%20%5Ctext%7Buse%20record%20%7D%20jvk%5C%5C%20%5Ctext%7BNot%20pedestrian%7D%20%26%20%5Ctext%7Bdo%20not%20use%20record%20%7D%20jvk%20%5Cend%7Bcases%7D%20%5Cend%7Bequation%7D)

More generally, for any variable *x* of interest:

<!--
\begin{equation}
\label{eq:sampling-general}
x_{itk} = 
\begin{cases}
\text{Condition: TRUE} & \text{use record } itk\\
\text{Condition: FALSE} & \text{do not use record } itk
\end{cases}
\end{equation}
-->

![](https://render.githubusercontent.com/render/math?math=%5Cbegin%7Bequation%7D%20%5Clabel%7Beq%3Asampling-general%7D%20x_%7Bitk%7D%20%3D%20%20%5Cbegin%7Bcases%7D%20%5Ctext%7BCondition%3A%20TRUE%7D%20%26%20%5Ctext%7Buse%20record%20%7D%20itk%5C%5C%20%5Ctext%7BCondition%3A%20FALSE%7D%20%26%20%5Ctext%7Bdo%20not%20use%20record%20%7D%20itk%20%5Cend%7Bcases%7D%20%5Cend%7Bequation%7D)

Several conditions can be imposed to subset the sample in any way that
the analyst deems appropriate or suitable.

# Setting for empirical assessment

In this section we present the setting for the empirical assessment of
the modelling strategies discussed in Section , namely matters related
to data and model estimation.

## Data for empirical assessment

To assess the performance of the various modelling strategies we use
data from Canada’s National Collision Database (NCDB). This database
contains all police-reported motor vehicle collisions on public roads in
Canada. Data are originally collected by provinces and territories, and
shared with the federal government, that proceeds to combine, track, and
analyze them for reporting deaths, injuries, and collisions in Canada at
the national level. The NCDB is provided by Transport Canada, the agency
of the federal government of Canada in charge of transportation policies
and programs, under the Open Government License - Canada version 2.0
\[<https://open.canada.ca/en/open-government-licence-canada>\].

The NCDB is available from 1999. For the purpose of this paper, we use
the data corresponding to 2017, which is the most recent year available
as of this writing. Furthermore, for assessment we also use the data
corresponding to 2016. Similar to databases in other jurisdictions (see
Montella et al. 2013), the NCDB contains information pertaining to the
collision, the traffic unit(s), and the person(s) involved in a crash.
The definitions of variables in this database are shown in the Appendix
at the end of this document, in Tables , , and . Notice that, compared
to Table , environmental-factors variables and road-factors variables
are gathered under a single variable class, namely collision-related,
since they are unique for each crash.

Data are organized by person; in other words, there is one record per
individual in a collision, be they drivers, passengers, pedestrians,
etc. The only variable directly available with respect to opponents in a
collision is the number of vehicles involved (see models in Bogue,
Paleti, and Balan 2017). Therefore, the data needs to be processed to
obtain attributes of the opposing party for each individual in a
collision. The protocol to do this is described next.

## Data preprocessing

To prepare the data for analysis, in particular for Strategy 1
(opponent-related factors), we apply an initial filter, whereby we scan
the database to remove all records that are not a person (including
parked cars and other objects) or that are missing information.

After the initial filter, the database is summarized to find the number
of individual-level records that correspond to each collision (C\_CASE).
At this point, there are 32,298 collisions, involving only one (known)
individual, there are 46,483 collisions involving two parties, 19,433
collisions with three parties, 8,250 collisions involving four parties,
3,783 collisions with five parties, 1,789 collisions with six parties,
and 1,491 collisions involving seven or more parties These parties were
possibly occupants in different vehicles or were otherwise their own
traffic units (e.g., pedestrians). Accordingly, the sample includes
174,741 drivers, 61,403 passengers, 10,798 pedestrians, 5,286
bicyclists, and 6,564 motorcyclists.

The next step is to remove all collisions that involve only one party.
This still leaves numerous cases where multiple parties could have been
in a single vehicle, for instance in a collision against a stationary
object. Therefore, we proceed to use the vehicle sequence number to find
the number of vehicles involved in each collision. This reveals that
there are 20,732 collisions that involve only one vehicle but possibly
multiple individuals (i.e., driver and one or more passengers). In
addition, there are 165,520 collisions involving two vehicles (and
possibly multiple individuals). Finally, there are 40,242 collisions
with three or more vehicles.

Once we have identified the number of vehicles in each collision, we
select all cases that involve only two vehicles. The most common cases
in two-vehicle collisions are those that include drivers (40,297
collisions; this is reflective of the prevalence of single-occupant
vehicles). This is followed by cases with passengers (14,120
collisions), pedestrians (5,204 collisions), bicyclists (2,238
collisions), and motorcyclists (1,016 collisions). The distribution of
individuals per traffic unit is as follows: 80,382 individuals are coded
as being in V\_ID = 1, 76,523 individuals are coded as being in V\_ID =
2, and 7,932 individuals are coded as pedestrians. In addition, 683
individuals are coded as being in vehicles 3 through 9, despite our
earlier filter to retain only collisions with two vehicles. At this
point we select only individuals assigned to vehicles 1 or 2, as well as
pedestrians. As a result of this filter a number of cases with only one
known individual need to be removed.

<table>

<caption>

Summary of opponent interactions and outcomes by road user class

</caption>

<thead>

<tr>

<th style="border-bottom:hidden" colspan="1">

</th>

<th style="border-bottom:hidden; padding-bottom:0; padding-left:3px;padding-right:3px;text-align: center; " colspan="4">

<div style="border-bottom: 1px solid #ddd; padding-bottom: 5px; ">

Road User Class of Opponent

</div>

</th>

<th style="border-bottom:hidden; padding-bottom:0; padding-left:3px;padding-right:3px;text-align: center; " colspan="3">

<div style="border-bottom: 1px solid #ddd; padding-bottom: 5px; ">

Outcome

</div>

</th>

<th style="border-bottom:hidden; padding-bottom:0; padding-left:3px;padding-right:3px;text-align: center; " colspan="3">

<div style="border-bottom: 1px solid #ddd; padding-bottom: 5px; ">

Proportion by Road User Class

</div>

</th>

</tr>

<tr>

<th style="text-align:left;">

Road User Class

</th>

<th style="text-align:right;">

Driver

</th>

<th style="text-align:right;">

Pedestrian

</th>

<th style="text-align:right;">

Bicyclist

</th>

<th style="text-align:right;">

Motorcyclist

</th>

<th style="text-align:right;">

No Injury

</th>

<th style="text-align:right;">

Injury

</th>

<th style="text-align:right;">

Fatality

</th>

<th style="text-align:left;">

No Injury

</th>

<th style="text-align:left;">

Injury

</th>

<th style="text-align:left;">

Fatality

</th>

</tr>

</thead>

<tbody>

<tr grouplength="5">

<td colspan="11" style="border-bottom: 1px solid;">

<strong>All opponents</strong>

</td>

</tr>

<tr>

<td style="text-align:left; padding-left: 2em;" indentlevel="1">

Driver

</td>

<td style="text-align:right;">

97582

</td>

<td style="text-align:right;">

7880

</td>

<td style="text-align:right;">

3799

</td>

<td style="text-align:right;">

2498

</td>

<td style="text-align:right;">

59180

</td>

<td style="text-align:right;">

52143

</td>

<td style="text-align:right;">

436

</td>

<td style="text-align:left;">

0.52953

</td>

<td style="text-align:left;">

0.46657

</td>

<td style="text-align:left;">

0.003901

</td>

</tr>

<tr>

<td style="text-align:left; padding-left: 2em;" indentlevel="1">

Passenger

</td>

<td style="text-align:right;">

35359

</td>

<td style="text-align:right;">

1282

</td>

<td style="text-align:right;">

667

</td>

<td style="text-align:right;">

818

</td>

<td style="text-align:right;">

19308

</td>

<td style="text-align:right;">

18667

</td>

<td style="text-align:right;">

151

</td>

<td style="text-align:left;">

0.50643

</td>

<td style="text-align:left;">

0.48961

</td>

<td style="text-align:left;">

0.003961

</td>

</tr>

<tr>

<td style="text-align:left; padding-left: 2em;" indentlevel="1">

Pedestrian

</td>

<td style="text-align:right;">

7880

</td>

<td style="text-align:right;">

0

</td>

<td style="text-align:right;">

0

</td>

<td style="text-align:right;">

0

</td>

<td style="text-align:right;">

145

</td>

<td style="text-align:right;">

7507

</td>

<td style="text-align:right;">

228

</td>

<td style="text-align:left;">

0.01840

</td>

<td style="text-align:left;">

0.95266

</td>

<td style="text-align:left;">

0.028934

</td>

</tr>

<tr>

<td style="text-align:left; padding-left: 2em;" indentlevel="1">

Bicyclist

</td>

<td style="text-align:right;">

3799

</td>

<td style="text-align:right;">

1

</td>

<td style="text-align:right;">

0

</td>

<td style="text-align:right;">

40

</td>

<td style="text-align:right;">

49

</td>

<td style="text-align:right;">

3760

</td>

<td style="text-align:right;">

31

</td>

<td style="text-align:left;">

0.01276

</td>

<td style="text-align:left;">

0.97917

</td>

<td style="text-align:left;">

0.008073

</td>

</tr>

<tr>

<td style="text-align:left; padding-left: 2em;" indentlevel="1">

Motorcyclist

</td>

<td style="text-align:right;">

2498

</td>

<td style="text-align:right;">

30

</td>

<td style="text-align:right;">

40

</td>

<td style="text-align:right;">

338

</td>

<td style="text-align:right;">

204

</td>

<td style="text-align:right;">

2598

</td>

<td style="text-align:right;">

104

</td>

<td style="text-align:left;">

0.07020

</td>

<td style="text-align:left;">

0.89401

</td>

<td style="text-align:left;">

0.035788

</td>

</tr>

<tr grouplength="5">

<td colspan="11" style="border-bottom: 1px solid;">

<strong>Opponent: Driver</strong>

</td>

</tr>

<tr>

<td style="text-align:left; padding-left: 2em;" indentlevel="1">

Driver

</td>

<td style="text-align:right;">

97582

</td>

<td style="text-align:right;">

0

</td>

<td style="text-align:right;">

0

</td>

<td style="text-align:right;">

0

</td>

<td style="text-align:right;">

45493

</td>

<td style="text-align:right;">

51657

</td>

<td style="text-align:right;">

432

</td>

<td style="text-align:left;">

0.46620

</td>

<td style="text-align:left;">

0.52937

</td>

<td style="text-align:left;">

0.004427

</td>

</tr>

<tr>

<td style="text-align:left; padding-left: 2em;" indentlevel="1">

Passenger

</td>

<td style="text-align:right;">

35359

</td>

<td style="text-align:right;">

0

</td>

<td style="text-align:right;">

0

</td>

<td style="text-align:right;">

0

</td>

<td style="text-align:right;">

16672

</td>

<td style="text-align:right;">

18536

</td>

<td style="text-align:right;">

151

</td>

<td style="text-align:left;">

0.47151

</td>

<td style="text-align:left;">

0.52422

</td>

<td style="text-align:left;">

0.004270

</td>

</tr>

<tr>

<td style="text-align:left; padding-left: 2em;" indentlevel="1">

Pedestrian

</td>

<td style="text-align:right;">

7880

</td>

<td style="text-align:right;">

0

</td>

<td style="text-align:right;">

0

</td>

<td style="text-align:right;">

0

</td>

<td style="text-align:right;">

145

</td>

<td style="text-align:right;">

7507

</td>

<td style="text-align:right;">

228

</td>

<td style="text-align:left;">

0.01840

</td>

<td style="text-align:left;">

0.95266

</td>

<td style="text-align:left;">

0.028934

</td>

</tr>

<tr>

<td style="text-align:left; padding-left: 2em;" indentlevel="1">

Bicyclist

</td>

<td style="text-align:right;">

3799

</td>

<td style="text-align:right;">

0

</td>

<td style="text-align:right;">

0

</td>

<td style="text-align:right;">

0

</td>

<td style="text-align:right;">

43

</td>

<td style="text-align:right;">

3725

</td>

<td style="text-align:right;">

31

</td>

<td style="text-align:left;">

0.01132

</td>

<td style="text-align:left;">

0.98052

</td>

<td style="text-align:left;">

0.008160

</td>

</tr>

<tr>

<td style="text-align:left; padding-left: 2em;" indentlevel="1">

Motorcyclist

</td>

<td style="text-align:right;">

2498

</td>

<td style="text-align:right;">

0

</td>

<td style="text-align:right;">

0

</td>

<td style="text-align:right;">

0

</td>

<td style="text-align:right;">

98

</td>

<td style="text-align:right;">

2299

</td>

<td style="text-align:right;">

101

</td>

<td style="text-align:left;">

0.03923

</td>

<td style="text-align:left;">

0.92034

</td>

<td style="text-align:left;">

0.040432

</td>

</tr>

<tr grouplength="5">

<td colspan="11" style="border-bottom: 1px solid;">

<strong>Opponent: Pedestrian</strong>

</td>

</tr>

<tr>

<td style="text-align:left; padding-left: 2em;" indentlevel="1">

Driver

</td>

<td style="text-align:right;">

0

</td>

<td style="text-align:right;">

7880

</td>

<td style="text-align:right;">

0

</td>

<td style="text-align:right;">

0

</td>

<td style="text-align:right;">

7693

</td>

<td style="text-align:right;">

187

</td>

<td style="text-align:right;">

0

</td>

<td style="text-align:left;">

0.97627

</td>

<td style="text-align:left;">

0.02373

</td>

<td style="text-align:left;">

0.000000

</td>

</tr>

<tr>

<td style="text-align:left; padding-left: 2em;" indentlevel="1">

Passenger

</td>

<td style="text-align:right;">

0

</td>

<td style="text-align:right;">

1282

</td>

<td style="text-align:right;">

0

</td>

<td style="text-align:right;">

0

</td>

<td style="text-align:right;">

1246

</td>

<td style="text-align:right;">

36

</td>

<td style="text-align:right;">

0

</td>

<td style="text-align:left;">

0.97192

</td>

<td style="text-align:left;">

0.02808

</td>

<td style="text-align:left;">

0.000000

</td>

</tr>

<tr>

<td style="text-align:left; padding-left: 2em;" indentlevel="1">

Pedestrian

</td>

<td style="text-align:right;">

0

</td>

<td style="text-align:right;">

0

</td>

<td style="text-align:right;">

0

</td>

<td style="text-align:right;">

0

</td>

<td style="text-align:right;">

0

</td>

<td style="text-align:right;">

0

</td>

<td style="text-align:right;">

0

</td>

<td style="text-align:left;">

  - 
    
    </td>
    
    <td style="text-align:left;">
    
      - 
        
        </td>
        
        <td style="text-align:left;">
        
          - 
            
            </td>
            
            </tr>
            
            <tr>
            
            <td style="text-align:left; padding-left: 2em;" indentlevel="1">
            
            Bicyclist
            
            </td>
            
            <td style="text-align:right;">
            
            0
            
            </td>
            
            <td style="text-align:right;">
            
            1
            
            </td>
            
            <td style="text-align:right;">
            
            0
            
            </td>
            
            <td style="text-align:right;">
            
            0
            
            </td>
            
            <td style="text-align:right;">
            
            0
            
            </td>
            
            <td style="text-align:right;">
            
            1
            
            </td>
            
            <td style="text-align:right;">
            
            0
            
            </td>
            
            <td style="text-align:left;">
            
            0.00000
            
            </td>
            
            <td style="text-align:left;">
            
            1.00000
            
            </td>
            
            <td style="text-align:left;">
            
            0.000000
            
            </td>
            
            </tr>
            
            <tr>
            
            <td style="text-align:left; padding-left: 2em;" indentlevel="1">
            
            Motorcyclist
            
            </td>
            
            <td style="text-align:right;">
            
            0
            
            </td>
            
            <td style="text-align:right;">
            
            30
            
            </td>
            
            <td style="text-align:right;">
            
            0
            
            </td>
            
            <td style="text-align:right;">
            
            0
            
            </td>
            
            <td style="text-align:right;">
            
            11
            
            </td>
            
            <td style="text-align:right;">
            
            19
            
            </td>
            
            <td style="text-align:right;">
            
            0
            
            </td>
            
            <td style="text-align:left;">
            
            0.36667
            
            </td>
            
            <td style="text-align:left;">
            
            0.63333
            
            </td>
            
            <td style="text-align:left;">
            
            0.000000
            
            </td>
            
            </tr>
            
            <tr grouplength="5">
            
            <td colspan="11" style="border-bottom: 1px solid;">
            
            <strong>Opponent: Bicyclist</strong>
            
            </td>
            
            </tr>
            
            <tr>
            
            <td style="text-align:left; padding-left: 2em;" indentlevel="1">
            
            Driver
            
            </td>
            
            <td style="text-align:right;">
            
            0
            
            </td>
            
            <td style="text-align:right;">
            
            0
            
            </td>
            
            <td style="text-align:right;">
            
            3799
            
            </td>
            
            <td style="text-align:right;">
            
            0
            
            </td>
            
            <td style="text-align:right;">
            
            3706
            
            </td>
            
            <td style="text-align:right;">
            
            93
            
            </td>
            
            <td style="text-align:right;">
            
            0
            
            </td>
            
            <td style="text-align:left;">
            
            0.97552
            
            </td>
            
            <td style="text-align:left;">
            
            0.02448
            
            </td>
            
            <td style="text-align:left;">
            
            0.000000
            
            </td>
            
            </tr>
            
            <tr>
            
            <td style="text-align:left; padding-left: 2em;" indentlevel="1">
            
            Passenger
            
            </td>
            
            <td style="text-align:right;">
            
            0
            
            </td>
            
            <td style="text-align:right;">
            
            0
            
            </td>
            
            <td style="text-align:right;">
            
            667
            
            </td>
            
            <td style="text-align:right;">
            
            0
            
            </td>
            
            <td style="text-align:right;">
            
            649
            
            </td>
            
            <td style="text-align:right;">
            
            18
            
            </td>
            
            <td style="text-align:right;">
            
            0
            
            </td>
            
            <td style="text-align:left;">
            
            0.97301
            
            </td>
            
            <td style="text-align:left;">
            
            0.02699
            
            </td>
            
            <td style="text-align:left;">
            
            0.000000
            
            </td>
            
            </tr>
            
            <tr>
            
            <td style="text-align:left; padding-left: 2em;" indentlevel="1">
            
            Pedestrian
            
            </td>
            
            <td style="text-align:right;">
            
            0
            
            </td>
            
            <td style="text-align:right;">
            
            0
            
            </td>
            
            <td style="text-align:right;">
            
            0
            
            </td>
            
            <td style="text-align:right;">
            
            0
            
            </td>
            
            <td style="text-align:right;">
            
            0
            
            </td>
            
            <td style="text-align:right;">
            
            0
            
            </td>
            
            <td style="text-align:right;">
            
            0
            
            </td>
            
            <td style="text-align:left;">
            
              - 
                
                </td>
                
                <td style="text-align:left;">
                
                  - 
                    
                    </td>
                    
                    <td style="text-align:left;">
                    
                      - 
                        
                        </td>
                        
                        </tr>
                        
                        <tr>
                        
                        <td style="text-align:left; padding-left: 2em;" indentlevel="1">
                        
                        Bicyclist
                        
                        </td>
                        
                        <td style="text-align:right;">
                        
                        0
                        
                        </td>
                        
                        <td style="text-align:right;">
                        
                        0
                        
                        </td>
                        
                        <td style="text-align:right;">
                        
                        0
                        
                        </td>
                        
                        <td style="text-align:right;">
                        
                        0
                        
                        </td>
                        
                        <td style="text-align:right;">
                        
                        0
                        
                        </td>
                        
                        <td style="text-align:right;">
                        
                        0
                        
                        </td>
                        
                        <td style="text-align:right;">
                        
                        0
                        
                        </td>
                        
                        <td style="text-align:left;">
                        
                          - 
                            
                            </td>
                            
                            <td style="text-align:left;">
                            
                              - 
                                
                                </td>
                                
                                <td style="text-align:left;">
                                
                                  - 
                                    
                                    </td>
                                    
                                    </tr>
                                    
                                    <tr>
                                    
                                    <td style="text-align:left; padding-left: 2em;" indentlevel="1">
                                    
                                    Motorcyclist
                                    
                                    </td>
                                    
                                    <td style="text-align:right;">
                                    
                                    0
                                    
                                    </td>
                                    
                                    <td style="text-align:right;">
                                    
                                    0
                                    
                                    </td>
                                    
                                    <td style="text-align:right;">
                                    
                                    40
                                    
                                    </td>
                                    
                                    <td style="text-align:right;">
                                    
                                    0
                                    
                                    </td>
                                    
                                    <td style="text-align:right;">
                                    
                                    16
                                    
                                    </td>
                                    
                                    <td style="text-align:right;">
                                    
                                    24
                                    
                                    </td>
                                    
                                    <td style="text-align:right;">
                                    
                                    0
                                    
                                    </td>
                                    
                                    <td style="text-align:left;">
                                    
                                    0.40000
                                    
                                    </td>
                                    
                                    <td style="text-align:left;">
                                    
                                    0.60000
                                    
                                    </td>
                                    
                                    <td style="text-align:left;">
                                    
                                    0.000000
                                    
                                    </td>
                                    
                                    </tr>
                                    
                                    <tr grouplength="5">
                                    
                                    <td colspan="11" style="border-bottom: 1px solid;">
                                    
                                    <strong>Opponent:
                                    Motorcyclist</strong>
                                    
                                    </td>
                                    
                                    </tr>
                                    
                                    <tr>
                                    
                                    <td style="text-align:left; padding-left: 2em;" indentlevel="1">
                                    
                                    Driver
                                    
                                    </td>
                                    
                                    <td style="text-align:right;">
                                    
                                    0
                                    
                                    </td>
                                    
                                    <td style="text-align:right;">
                                    
                                    0
                                    
                                    </td>
                                    
                                    <td style="text-align:right;">
                                    
                                    0
                                    
                                    </td>
                                    
                                    <td style="text-align:right;">
                                    
                                    2498
                                    
                                    </td>
                                    
                                    <td style="text-align:right;">
                                    
                                    2288
                                    
                                    </td>
                                    
                                    <td style="text-align:right;">
                                    
                                    206
                                    
                                    </td>
                                    
                                    <td style="text-align:right;">
                                    
                                    4
                                    
                                    </td>
                                    
                                    <td style="text-align:left;">
                                    
                                    0.91593
                                    
                                    </td>
                                    
                                    <td style="text-align:left;">
                                    
                                    0.08247
                                    
                                    </td>
                                    
                                    <td style="text-align:left;">
                                    
                                    0.001601
                                    
                                    </td>
                                    
                                    </tr>
                                    
                                    <tr>
                                    
                                    <td style="text-align:left; padding-left: 2em;" indentlevel="1">
                                    
                                    Passenger
                                    
                                    </td>
                                    
                                    <td style="text-align:right;">
                                    
                                    0
                                    
                                    </td>
                                    
                                    <td style="text-align:right;">
                                    
                                    0
                                    
                                    </td>
                                    
                                    <td style="text-align:right;">
                                    
                                    0
                                    
                                    </td>
                                    
                                    <td style="text-align:right;">
                                    
                                    818
                                    
                                    </td>
                                    
                                    <td style="text-align:right;">
                                    
                                    741
                                    
                                    </td>
                                    
                                    <td style="text-align:right;">
                                    
                                    77
                                    
                                    </td>
                                    
                                    <td style="text-align:right;">
                                    
                                    0
                                    
                                    </td>
                                    
                                    <td style="text-align:left;">
                                    
                                    0.90587
                                    
                                    </td>
                                    
                                    <td style="text-align:left;">
                                    
                                    0.09413
                                    
                                    </td>
                                    
                                    <td style="text-align:left;">
                                    
                                    0.000000
                                    
                                    </td>
                                    
                                    </tr>
                                    
                                    <tr>
                                    
                                    <td style="text-align:left; padding-left: 2em;" indentlevel="1">
                                    
                                    Pedestrian
                                    
                                    </td>
                                    
                                    <td style="text-align:right;">
                                    
                                    0
                                    
                                    </td>
                                    
                                    <td style="text-align:right;">
                                    
                                    0
                                    
                                    </td>
                                    
                                    <td style="text-align:right;">
                                    
                                    0
                                    
                                    </td>
                                    
                                    <td style="text-align:right;">
                                    
                                    0
                                    
                                    </td>
                                    
                                    <td style="text-align:right;">
                                    
                                    0
                                    
                                    </td>
                                    
                                    <td style="text-align:right;">
                                    
                                    0
                                    
                                    </td>
                                    
                                    <td style="text-align:right;">
                                    
                                    0
                                    
                                    </td>
                                    
                                    <td style="text-align:left;">
                                    
                                      - 
                                        
                                        </td>
                                        
                                        <td style="text-align:left;">
                                        
                                          - 
                                            
                                            </td>
                                            
                                            <td style="text-align:left;">
                                            
                                              - 
                                                
                                                </td>
                                                
                                                </tr>
                                                
                                                <tr>
                                                
                                                <td style="text-align:left; padding-left: 2em;" indentlevel="1">
                                                
                                                Bicyclist
                                                
                                                </td>
                                                
                                                <td style="text-align:right;">
                                                
                                                0
                                                
                                                </td>
                                                
                                                <td style="text-align:right;">
                                                
                                                0
                                                
                                                </td>
                                                
                                                <td style="text-align:right;">
                                                
                                                0
                                                
                                                </td>
                                                
                                                <td style="text-align:right;">
                                                
                                                40
                                                
                                                </td>
                                                
                                                <td style="text-align:right;">
                                                
                                                6
                                                
                                                </td>
                                                
                                                <td style="text-align:right;">
                                                
                                                34
                                                
                                                </td>
                                                
                                                <td style="text-align:right;">
                                                
                                                0
                                                
                                                </td>
                                                
                                                <td style="text-align:left;">
                                                
                                                0.15000
                                                
                                                </td>
                                                
                                                <td style="text-align:left;">
                                                
                                                0.85000
                                                
                                                </td>
                                                
                                                <td style="text-align:left;">
                                                
                                                0.000000
                                                
                                                </td>
                                                
                                                </tr>
                                                
                                                <tr>
                                                
                                                <td style="text-align:left; padding-left: 2em;" indentlevel="1">
                                                
                                                Motorcyclist
                                                
                                                </td>
                                                
                                                <td style="text-align:right;">
                                                
                                                0
                                                
                                                </td>
                                                
                                                <td style="text-align:right;">
                                                
                                                0
                                                
                                                </td>
                                                
                                                <td style="text-align:right;">
                                                
                                                0
                                                
                                                </td>
                                                
                                                <td style="text-align:right;">
                                                
                                                338
                                                
                                                </td>
                                                
                                                <td style="text-align:right;">
                                                
                                                79
                                                
                                                </td>
                                                
                                                <td style="text-align:right;">
                                                
                                                256
                                                
                                                </td>
                                                
                                                <td style="text-align:right;">
                                                
                                                3
                                                
                                                </td>
                                                
                                                <td style="text-align:left;">
                                                
                                                0.23373
                                                
                                                </td>
                                                
                                                <td style="text-align:left;">
                                                
                                                0.75740
                                                
                                                </td>
                                                
                                                <td style="text-align:left;">
                                                
                                                0.008876
                                                
                                                </td>
                                                
                                                </tr>
                                                
                                                </tbody>
                                                
                                                </table>

At this point we have a complete, workable sample of individual records
of parties in two-vehicle collisions. There are two possible cases for
the collisions, depending on the traffic units involved: 1) vehicle vs
vehicle collisions (“vehicle” is all motorized vehicles, including
motorcycles/mopeds, as well as bicycles); and 2) vehicle vs pedestrian
collisions. To identify the opposite parties in each collision it is
convenient to classify collisions by pedestrian involvement. In this
way, we find that the database includes 16,636 collisions that are
vehicle vs pedestrian (possibly multiple pedestrians), and 147,594
collisions that involve two vehicles. After splitting the database
according to pedestrian involvement, we can now extract relevant
information about the different parties in the collision. This involves
renaming the person-level variables so that we can distinguish each
individual by their party in a given record. Notice that when working
with individuals in vehicles, only drivers are considered opposites in a
collision.

Once the personal attributes of opposite operators in a given collision
are extracted, their information is joined to the individual records by
means of the collision unique identifier. As a result of this process, a
new set of variables are now available for analysis: the age, sex, and
road user class of the opposite driver, as well as the type of the
opposite vehicle. A summary of opponent interactions and outcomes can be
found in Table . The information there shows that the most common type
of opponent for drivers are other drivers, followed by pedestrians. The
only opponents of pedestrians, on the other hand, are drivers.
Bicyclists and motorcyclists are mostly opposed by drivers, but
occasionally by other road users as well. In terms of outcomes, we
observe that virtually all fatalities occur when the opponent is a
driver, and only very rarely when the opponent is a motorcyclist.
Injuries are also more common when the opponent is a driver, whereas “no
injury” is a relatively more frequent outcome when the opponent is a
pedestrian or a bicyclist.

## Model estimation

Before model estimation, the variables are prepared as follows. First,
age is scaled from years to decades. Secondly, new variables are defined
to describe the vehicle type. Three classes of vehicle types are
considered: 1) light duty vehicles (which in Canada include passenger
cars, passenger vans, light utility vehicles, and light duty pick up
trucks); 2) light trucks (all other vehicles
![](https://render.githubusercontent.com/render/math?math=%5Cle) 4536 kg
in gross vehicle weight rating); and heavy vehicles (all other vehicles
![](https://render.githubusercontent.com/render/math?math=%5Cge) 4536 kg
in gross vehicle weight rating). Furthermore, this typology of vehicle
is combined with the road user class of the individual to distinguish
between drivers and passengers of light duty vehicles, light trucks, and
heavy vehicles, in addition to pedestrians, bicyclists, and
motorcyclists. This is done for both the individual and the opponent.
Variable interactions are calculated to produce hierarchical variables.
For example, for a hierarchical definition of traffic unit-level
variables, age (and the square of age to account for possible
non-monotonic effects) are interacted with gender, road user class, and
vehicle type. For hierarchical opponent variables, age (and the square
of age) are interacted with the age of opponent (and the corresponding
square). The variables thus obtained are shown in Table . As seen in the
table, Models 1 and 2 are single-level models, and the difference
between them is that Model 2 includes opponent variables. Models 3 and
4, in contrast, are hierarchical models. Model 3 considers the hierarchy
on the basis of the traffic unit, while Model 4 considers the hierarchy
on the basis of the opponent.

Models 1 through 4 are estimated using the full sample. As discussed
above, a related modelling strategy is to subset the sample (e.g.,
Islam, Jones, and Dye 2014; Lee and Li 2014; Torrao, Coelho, and
Rouphail 2014; Wu et al. 2014). In this case we subset by a combination
of traffic unit type of the individual (i.e., light duty vehicle, light
truck, heavy vehicle, pedestrian, bicyclist, and motorcyclist) and
vehicle type of the opponent (i.e., light duty vehicle, light truck,
heavy vehicle). This leads to an ensemble of eighteen models to be
estimated using subsets of data (see Table ). By subsetting the sample,
at least *some* opponent effects are incorporated implicitly. Models 1
and 2 are re-estimated using this strategy, dropping variables as
necessary whenever they become irrelevant (for instance, after filtering
for pedestrians, no other traffic unit types are present in the subset
of data). In addition to variables that are no longer relevant in some
data subsets, it is important to note that when using some data subsets
a few variables had to be occasionally dropped to avoid convergence
issues. This tended to happen particularly with smaller subsets where
some particular combination of attributes was rare as a result of
subsampling (e.g., in 2017 there were few or no collisions that involved
a motorcyclist and a heavy vehicle in a bridge, or overpass, or
viaduct). The process of estimation carefully paid attention to
convergence issues to ensure the validity of the models reported here.

<table>

<caption>

Summary of variables and model specification

</caption>

<thead>

<tr>

<th style="text-align:left;">

Variable

</th>

<th style="text-align:left;">

Notes

</th>

<th style="text-align:left;">

Model 1 Single-level /No opponent

</th>

<th style="text-align:left;">

Model 2 Single-level /Opponent attributes

</th>

<th style="text-align:left;">

Model 3 Hierarchical: Traffic unit

</th>

<th style="text-align:left;">

Model 4 Hierarchical: Opponent attributes

</th>

</tr>

</thead>

<tbody>

<tr grouplength="4">

<td colspan="6" style="border-bottom: 1px solid;">

<strong>Individual-level variables</strong>

</td>

</tr>

<tr>

<td style="text-align:left; padding-left: 2em;" indentlevel="1">

Age

</td>

<td style="text-align:left;">

In decades

</td>

<td style="text-align:left;">

\(\checkmark\)

</td>

<td style="text-align:left;">

\(\checkmark\)

</td>

<td style="text-align:left;">

\(\checkmark\)

</td>

<td style="text-align:left;">

\(\checkmark\)

</td>

</tr>

<tr>

<td style="text-align:left; padding-left: 2em;" indentlevel="1">

Age Squared

</td>

<td style="text-align:left;">

</td>

<td style="text-align:left;">

\(\checkmark\)

</td>

<td style="text-align:left;">

\(\checkmark\)

</td>

<td style="text-align:left;">

\(\checkmark\)

</td>

<td style="text-align:left;">

\(\checkmark\)

</td>

</tr>

<tr>

<td style="text-align:left; padding-left: 2em;" indentlevel="1">

Sex

</td>

<td style="text-align:left;">

Reference: Female

</td>

<td style="text-align:left;">

\(\checkmark\)

</td>

<td style="text-align:left;">

\(\checkmark\)

</td>

<td style="text-align:left;">

\(\checkmark\)

</td>

<td style="text-align:left;">

\(\checkmark\)

</td>

</tr>

<tr>

<td style="text-align:left; padding-left: 2em;" indentlevel="1">

Use of Safety Devices

</td>

<td style="text-align:left;">

7 levels; Reference: No Safety Device

</td>

<td style="text-align:left;">

\(\checkmark\)

</td>

<td style="text-align:left;">

\(\checkmark\)

</td>

<td style="text-align:left;">

\(\checkmark\)

</td>

<td style="text-align:left;">

\(\checkmark\)

</td>

</tr>

<tr grouplength="6">

<td colspan="6" style="border-bottom: 1px solid;">

<strong>Traffic unit-level variables</strong>

</td>

</tr>

<tr>

<td style="text-align:left; padding-left: 2em;" indentlevel="1">

Passenger

</td>

<td style="text-align:left;">

Reference: Driver

</td>

<td style="text-align:left;">

\(\checkmark\)

</td>

<td style="text-align:left;">

\(\checkmark\)

</td>

<td style="text-align:left;">

</td>

<td style="text-align:left;">

\(\checkmark\)

</td>

</tr>

<tr>

<td style="text-align:left; padding-left: 2em;" indentlevel="1">

Pedestrian

</td>

<td style="text-align:left;">

Reference: Driver

</td>

<td style="text-align:left;">

\(\checkmark\)

</td>

<td style="text-align:left;">

\(\checkmark\)

</td>

<td style="text-align:left;">

</td>

<td style="text-align:left;">

\(\checkmark\)

</td>

</tr>

<tr>

<td style="text-align:left; padding-left: 2em;" indentlevel="1">

Bicyclist

</td>

<td style="text-align:left;">

Reference: Driver

</td>

<td style="text-align:left;">

\(\checkmark\)

</td>

<td style="text-align:left;">

\(\checkmark\)

</td>

<td style="text-align:left;">

</td>

<td style="text-align:left;">

\(\checkmark\)

</td>

</tr>

<tr>

<td style="text-align:left; padding-left: 2em;" indentlevel="1">

Motorcyclist

</td>

<td style="text-align:left;">

Reference: Driver

</td>

<td style="text-align:left;">

\(\checkmark\)

</td>

<td style="text-align:left;">

\(\checkmark\)

</td>

<td style="text-align:left;">

</td>

<td style="text-align:left;">

\(\checkmark\)

</td>

</tr>

<tr>

<td style="text-align:left; padding-left: 2em;" indentlevel="1">

Light Truck

</td>

<td style="text-align:left;">

Reference: Light Duty Vehicle

</td>

<td style="text-align:left;">

\(\checkmark\)

</td>

<td style="text-align:left;">

\(\checkmark\)

</td>

<td style="text-align:left;">

</td>

<td style="text-align:left;">

\(\checkmark\)

</td>

</tr>

<tr>

<td style="text-align:left; padding-left: 2em;" indentlevel="1">

Heavy Vehicle

</td>

<td style="text-align:left;">

Reference: Light Duty Vehicle

</td>

<td style="text-align:left;">

\(\checkmark\)

</td>

<td style="text-align:left;">

\(\checkmark\)

</td>

<td style="text-align:left;">

</td>

<td style="text-align:left;">

\(\checkmark\)

</td>

</tr>

<tr grouplength="6">

<td colspan="6" style="border-bottom: 1px solid;">

<strong>Opponent variables</strong>

</td>

</tr>

<tr>

<td style="text-align:left; padding-left: 2em;" indentlevel="1">

Age of Opponent

</td>

<td style="text-align:left;">

In decades

</td>

<td style="text-align:left;">

</td>

<td style="text-align:left;">

\(\checkmark\)

</td>

<td style="text-align:left;">

\(\checkmark\)

</td>

<td style="text-align:left;">

</td>

</tr>

<tr>

<td style="text-align:left; padding-left: 2em;" indentlevel="1">

Age of Opponent Squared

</td>

<td style="text-align:left;">

</td>

<td style="text-align:left;">

</td>

<td style="text-align:left;">

\(\checkmark\)

</td>

<td style="text-align:left;">

\(\checkmark\)

</td>

<td style="text-align:left;">

</td>

</tr>

<tr>

<td style="text-align:left; padding-left: 2em;" indentlevel="1">

Sex of Opponent

</td>

<td style="text-align:left;">

Reference: Female

</td>

<td style="text-align:left;">

</td>

<td style="text-align:left;">

\(\checkmark\)

</td>

<td style="text-align:left;">

\(\checkmark\)

</td>

<td style="text-align:left;">

</td>

</tr>

<tr>

<td style="text-align:left; padding-left: 2em;" indentlevel="1">

Opponent: Light Duty Vehicle

</td>

<td style="text-align:left;">

Reference: Pedestrian/Bicyclist/Motorcyclist

</td>

<td style="text-align:left;">

</td>

<td style="text-align:left;">

\(\checkmark\)

</td>

<td style="text-align:left;">

\(\checkmark\)

</td>

<td style="text-align:left;">

\(\checkmark\)

</td>

</tr>

<tr>

<td style="text-align:left; padding-left: 2em;" indentlevel="1">

Opponent: Light Truck

</td>

<td style="text-align:left;">

Reference: Pedestrian/Bicyclist/Motorcyclist

</td>

<td style="text-align:left;">

</td>

<td style="text-align:left;">

\(\checkmark\)

</td>

<td style="text-align:left;">

\(\checkmark\)

</td>

<td style="text-align:left;">

\(\checkmark\)

</td>

</tr>

<tr>

<td style="text-align:left; padding-left: 2em;" indentlevel="1">

Opponent: Heavy Vehicle

</td>

<td style="text-align:left;">

Reference: Pedestrian/Bicyclist/Motorcyclist

</td>

<td style="text-align:left;">

</td>

<td style="text-align:left;">

\(\checkmark\)

</td>

<td style="text-align:left;">

\(\checkmark\)

</td>

<td style="text-align:left;">

\(\checkmark\)

</td>

</tr>

<tr grouplength="14">

<td colspan="6" style="border-bottom: 1px solid;">

<strong>Hierarchical traffic unit variables</strong>

</td>

</tr>

<tr>

<td style="text-align:left; padding-left: 2em;" indentlevel="1">

Light Truck Driver:Age

</td>

<td style="text-align:left;">

</td>

<td style="text-align:left;">

</td>

<td style="text-align:left;">

</td>

<td style="text-align:left;">

\(\checkmark\)

</td>

<td style="text-align:left;">

</td>

</tr>

<tr>

<td style="text-align:left; padding-left: 2em;" indentlevel="1">

Light Truck Driver:Age Squared

</td>

<td style="text-align:left;">

</td>

<td style="text-align:left;">

</td>

<td style="text-align:left;">

</td>

<td style="text-align:left;">

\(\checkmark\)

</td>

<td style="text-align:left;">

</td>

</tr>

<tr>

<td style="text-align:left; padding-left: 2em;" indentlevel="1">

Heavy Vehicle Driver:Age

</td>

<td style="text-align:left;">

</td>

<td style="text-align:left;">

</td>

<td style="text-align:left;">

</td>

<td style="text-align:left;">

\(\checkmark\)

</td>

<td style="text-align:left;">

</td>

</tr>

<tr>

<td style="text-align:left; padding-left: 2em;" indentlevel="1">

Heavy Vehicle Driver:Age Squared

</td>

<td style="text-align:left;">

</td>

<td style="text-align:left;">

</td>

<td style="text-align:left;">

</td>

<td style="text-align:left;">

\(\checkmark\)

</td>

<td style="text-align:left;">

</td>

</tr>

<tr>

<td style="text-align:left; padding-left: 2em;" indentlevel="1">

Light Truck Passenger:Age

</td>

<td style="text-align:left;">

</td>

<td style="text-align:left;">

</td>

<td style="text-align:left;">

</td>

<td style="text-align:left;">

\(\checkmark\)

</td>

<td style="text-align:left;">

</td>

</tr>

<tr>

<td style="text-align:left; padding-left: 2em;" indentlevel="1">

Light Truck Passenger:Age Squared:

</td>

<td style="text-align:left;">

</td>

<td style="text-align:left;">

</td>

<td style="text-align:left;">

</td>

<td style="text-align:left;">

\(\checkmark\)

</td>

<td style="text-align:left;">

</td>

</tr>

<tr>

<td style="text-align:left; padding-left: 2em;" indentlevel="1">

Heavy Vehicle Passenger:Age

</td>

<td style="text-align:left;">

</td>

<td style="text-align:left;">

</td>

<td style="text-align:left;">

</td>

<td style="text-align:left;">

\(\checkmark\)

</td>

<td style="text-align:left;">

</td>

</tr>

<tr>

<td style="text-align:left; padding-left: 2em;" indentlevel="1">

Heavy Vehicle Passenger:Age Squared

</td>

<td style="text-align:left;">

</td>

<td style="text-align:left;">

</td>

<td style="text-align:left;">

</td>

<td style="text-align:left;">

\(\checkmark\)

</td>

<td style="text-align:left;">

</td>

</tr>

<tr>

<td style="text-align:left; padding-left: 2em;" indentlevel="1">

Pedestrian:Age

</td>

<td style="text-align:left;">

</td>

<td style="text-align:left;">

</td>

<td style="text-align:left;">

</td>

<td style="text-align:left;">

\(\checkmark\)

</td>

<td style="text-align:left;">

</td>

</tr>

<tr>

<td style="text-align:left; padding-left: 2em;" indentlevel="1">

Pedestrian:Age Squared

</td>

<td style="text-align:left;">

</td>

<td style="text-align:left;">

</td>

<td style="text-align:left;">

</td>

<td style="text-align:left;">

\(\checkmark\)

</td>

<td style="text-align:left;">

</td>

</tr>

<tr>

<td style="text-align:left; padding-left: 2em;" indentlevel="1">

Bicyclist:Age

</td>

<td style="text-align:left;">

</td>

<td style="text-align:left;">

</td>

<td style="text-align:left;">

</td>

<td style="text-align:left;">

\(\checkmark\)

</td>

<td style="text-align:left;">

</td>

</tr>

<tr>

<td style="text-align:left; padding-left: 2em;" indentlevel="1">

Bicyclist:Age Squared

</td>

<td style="text-align:left;">

</td>

<td style="text-align:left;">

</td>

<td style="text-align:left;">

</td>

<td style="text-align:left;">

\(\checkmark\)

</td>

<td style="text-align:left;">

</td>

</tr>

<tr>

<td style="text-align:left; padding-left: 2em;" indentlevel="1">

Motorcyclist:Age

</td>

<td style="text-align:left;">

</td>

<td style="text-align:left;">

</td>

<td style="text-align:left;">

</td>

<td style="text-align:left;">

\(\checkmark\)

</td>

<td style="text-align:left;">

</td>

</tr>

<tr>

<td style="text-align:left; padding-left: 2em;" indentlevel="1">

Motorcyclist:Age Squared

</td>

<td style="text-align:left;">

</td>

<td style="text-align:left;">

</td>

<td style="text-align:left;">

</td>

<td style="text-align:left;">

\(\checkmark\)

</td>

<td style="text-align:left;">

</td>

</tr>

<tr grouplength="6">

<td colspan="6" style="border-bottom: 1px solid;">

<strong>Hierarchical opponent variables</strong>

</td>

</tr>

<tr>

<td style="text-align:left; padding-left: 2em;" indentlevel="1">

Age:Age of Opponent

</td>

<td style="text-align:left;">

</td>

<td style="text-align:left;">

</td>

<td style="text-align:left;">

</td>

<td style="text-align:left;">

</td>

<td style="text-align:left;">

\(\checkmark\)

</td>

</tr>

<tr>

<td style="text-align:left; padding-left: 2em;" indentlevel="1">

Age:Age of Female Opponent

</td>

<td style="text-align:left;">

</td>

<td style="text-align:left;">

</td>

<td style="text-align:left;">

</td>

<td style="text-align:left;">

</td>

<td style="text-align:left;">

\(\checkmark\)

</td>

</tr>

<tr>

<td style="text-align:left; padding-left: 2em;" indentlevel="1">

Age:Age of Male Opponent Squared

</td>

<td style="text-align:left;">

</td>

<td style="text-align:left;">

</td>

<td style="text-align:left;">

</td>

<td style="text-align:left;">

</td>

<td style="text-align:left;">

\(\checkmark\)

</td>

</tr>

<tr>

<td style="text-align:left; padding-left: 2em;" indentlevel="1">

Age:Age of Female Opponent Squared

</td>

<td style="text-align:left;">

</td>

<td style="text-align:left;">

</td>

<td style="text-align:left;">

</td>

<td style="text-align:left;">

</td>

<td style="text-align:left;">

\(\checkmark\)

</td>

</tr>

<tr>

<td style="text-align:left; padding-left: 2em;" indentlevel="1">

Age Squared:Age of Male Opponent

</td>

<td style="text-align:left;">

</td>

<td style="text-align:left;">

</td>

<td style="text-align:left;">

</td>

<td style="text-align:left;">

</td>

<td style="text-align:left;">

\(\checkmark\)

</td>

</tr>

<tr>

<td style="text-align:left; padding-left: 2em;" indentlevel="1">

Age Squared:Age of Female Opponent

</td>

<td style="text-align:left;">

</td>

<td style="text-align:left;">

</td>

<td style="text-align:left;">

</td>

<td style="text-align:left;">

</td>

<td style="text-align:left;">

\(\checkmark\)

</td>

</tr>

<tr grouplength="7">

<td colspan="6" style="border-bottom: 1px solid;">

<strong>Collision-level variables</strong>

</td>

</tr>

<tr>

<td style="text-align:left; padding-left: 2em;" indentlevel="1">

Crash Configuration

</td>

<td style="text-align:left;">

19 levels; Reference: Hit a moving object

</td>

<td style="text-align:left;">

\(\checkmark\)

</td>

<td style="text-align:left;">

\(\checkmark\)

</td>

<td style="text-align:left;">

\(\checkmark\)

</td>

<td style="text-align:left;">

\(\checkmark\)

</td>

</tr>

<tr>

<td style="text-align:left; padding-left: 2em;" indentlevel="1">

Road Configuration

</td>

<td style="text-align:left;">

12 levels; Reference: Non-intersection

</td>

<td style="text-align:left;">

\(\checkmark\)

</td>

<td style="text-align:left;">

\(\checkmark\)

</td>

<td style="text-align:left;">

\(\checkmark\)

</td>

<td style="text-align:left;">

\(\checkmark\)

</td>

</tr>

<tr>

<td style="text-align:left; padding-left: 2em;" indentlevel="1">

Weather

</td>

<td style="text-align:left;">

9 levels; Reference: Clear and sunny

</td>

<td style="text-align:left;">

\(\checkmark\)

</td>

<td style="text-align:left;">

\(\checkmark\)

</td>

<td style="text-align:left;">

\(\checkmark\)

</td>

<td style="text-align:left;">

\(\checkmark\)

</td>

</tr>

<tr>

<td style="text-align:left; padding-left: 2em;" indentlevel="1">

Surface

</td>

<td style="text-align:left;">

11 levels; Reference: Dry

</td>

<td style="text-align:left;">

\(\checkmark\)

</td>

<td style="text-align:left;">

\(\checkmark\)

</td>

<td style="text-align:left;">

\(\checkmark\)

</td>

<td style="text-align:left;">

\(\checkmark\)

</td>

</tr>

<tr>

<td style="text-align:left; padding-left: 2em;" indentlevel="1">

Road Alignment

</td>

<td style="text-align:left;">

8 levels; Reference: Straight and level

</td>

<td style="text-align:left;">

\(\checkmark\)

</td>

<td style="text-align:left;">

\(\checkmark\)

</td>

<td style="text-align:left;">

\(\checkmark\)

</td>

<td style="text-align:left;">

\(\checkmark\)

</td>

</tr>

<tr>

<td style="text-align:left; padding-left: 2em;" indentlevel="1">

Traffic Controls

</td>

<td style="text-align:left;">

19 levels; Reference: Operational traffic signals

</td>

<td style="text-align:left;">

\(\checkmark\)

</td>

<td style="text-align:left;">

\(\checkmark\)

</td>

<td style="text-align:left;">

\(\checkmark\)

</td>

<td style="text-align:left;">

\(\checkmark\)

</td>

</tr>

<tr>

<td style="text-align:left; padding-left: 2em;" indentlevel="1">

Month

</td>

<td style="text-align:left;">

12 levels; Reference: January

</td>

<td style="text-align:left;">

\(\checkmark\)

</td>

<td style="text-align:left;">

\(\checkmark\)

</td>

<td style="text-align:left;">

\(\checkmark\)

</td>

<td style="text-align:left;">

\(\checkmark\)

</td>

</tr>

</tbody>

</table>

# Model assessment

In this section we report an in-depth examination of the performance of
the models. We begin by inspecting the statistical goodness of fit of
the models by means of Akaike’s Information Criterion (\(AIC\)). Next,
we use the models to conduct in-sample predictions (i.e., *nowcasting*),
using the same sample that was used to estimate the models, and
out-of-sample predictions, using the data set corresponding to the year
2016 (i.e., *backcasting*). Predictions are commonly evaluated in two
different ways in the literature. Some researchers analyze the outcome
shares based on the predicted probabilities (e.g., Bogue, Paleti, and
Balan 2017; Yasmin and Eluru 2013). This is a form of aggregate
forecasting. Other researchers, in contrast, evaluate the classes of
outcomes based on the individual-level predictions (e.g., Tang et al.
2019; Torrao, Coelho, and Rouphail 2014). This is a form of disaggregate
forecasting.

## Goodness of fit of models

We begin our empirical assessment by examining the results of estimating
the models described above. Tables  and  present some key summary
statistics of the estimated models. Of interest is the goodness of fit
of the models, which in the case is measured with Akaike’s Information
Criterion (*AIC*). This criterion is calculated as follows:

<!--
\begin{equation}
\label{eq:aic}
AIC = 2Z - 2\ln{\hat{L}}
\end{equation}
-->

![](https://render.githubusercontent.com/render/math?math=%5Cbegin%7Bequation%7D%20%5Clabel%7Beq%3Aaic%7D%20AIC%20%3D%202Z%20-%202%5Cln%7B%5Chat%7BL%7D%7D%20%5Cend%7Bequation%7D)

where *Z* is the number of coefficients estimated by the model, and
![](https://render.githubusercontent.com/render/math?math=%5Chat%7BL%7D)
the maximized likelihood of the model. Since *AIC* penalizes the model
fit by means of the number of coefficients, this criterion gives
preference to more parsimonious models. The objective is to minimize the
*AIC*, and therefore smaller values of this criterion represent better
model fits. Model comparison can be conducted using the relative
likelihood. Suppose that we have two models, say Model *a* and Model
*b*, with ![AIC\_{a}
AIC\_{b}](https://render.githubusercontent.com/render/math?math=AIC_%7Ba%7D%20%5Cle%20AIC_%7Bb%7D).
The relative likelihood is calculated as:

<!--
\begin{equation}
\label{eq:relative-likelihood}
e^{(AIC_a - AIC_b)/2}
\end{equation}
-->

![](https://render.githubusercontent.com/render/math?math=%5Cbegin%7Bequation%7D%20%5Clabel%7Beq%3Arelative-likelihood%7D%20e%5E%7B\(AIC_a%20-%20AIC_b\)%2F2%7D%20%5Cend%7Bequation%7D)

The relative likelihood is interpreted as the probability that Model *b*
minimizes the information loss as well as Model *a*.

Turning our attention to the models estimated using the full sample
(Table ), it is possible to see that, compared to the base
(single-level) model without opponent variables (Model 1), there are
large and significant improvements in goodness of fit to be gained by
introducing opponent effects. However, the gains are not as large when
hierarchical specifications are used, even when the number of additional
coefficients that need to be estimated is not substantially larger
(recall that the penalty per coefficient in *AIC* is 2). The best model
according to this measure of goodness of fit is Model 2 (single-level
with opponent effects), followed by Model 4 (hierarchical opponent
variables), Model 3 (hierarchical traffic unit variables with opponent
effects), and finally Model 1 (single-level without opponent effects).

It is important to note that the likelihood function of a model, and
therefore the value of its \(AIC\), both depend on the size of the
sample, which is why *AIC* is not comparable across models estimated
with different sample sizes. For this reason, the full sample models
cannot be compared directly to the models estimated with subsets of
data. The models in the ensembles, however, can be compared to each
other (Table ). As seen in the table, introducing opponent variables
leads to a better fit in the case of most, but not all models. The
simplest model (single-level without opponent effects) is clearly the
best fitting candidate in the case of bicycle vs light truck collisions,
bicycle vs heavy vehicle collisions, motorcyclist vs light duty vehicle
collisions, and motorcyclist vs heavy vehicle collisions. Model 1 is a
statistical toss for best performance with two competing models in the
case of pedestrian vs heavy vehicle collisions. The relative likelihood
of Model 1 compared to Models 2 and 3 in this case is 0.56, which means
that these two models are 0.56 times as probable as Model 1 to minimize
the information loss.

Model 2 is the best fit in the case of light truck vs heavy vehicle
collisions. This model is also tied for best fit with Model 2 in the
case of pedestrian vs light duty vehicle and pedestrian vs light truck
collisions, and is a statistical toss with Model 4 in the case of heavy
vehicle vs heavy vehicle collisions (relative likelihood is 0.592).
Model 3 is the best fit in the case of light duty vehicle vs light duty
vehicle collisions and heavy vehicle vs light duty vehicle. Model 4 is
the best fit in the case of light duty vehicle vs light truck
collisions, light duty vehicle vs heavy vehicle collisions, light truck
vs light duty vehicle collisions, heavy vehicle vs light truck
collisions, and motorcycle vs light truck collisions. This model is a
statistical toss with Model 2 in the case of light truck vs light truck
collisions, with a relative likelihood of 0.791.

These results give some preliminary ideas about the relative performance
of the different modelling strategies. In the next subsection we delve
more deeply into this question by examining the predictive performance
of the various modelling strategies. The results up to this point
indicate that different model specification strategies might work best
when combined with subsampling strategies. For space reasons, from this
point onwards, we will consider the ensembles of models for predictions
and will not compare individual models within the ensembles; this we
suggest is a matter for future research.

<table>

<caption>

Summary of model estimation results: Full sample models

</caption>

<thead>

<tr>

<th style="text-align:left;">

Model

</th>

<th style="text-align:center;">

</th>

<th style="text-align:center;">

</th>

<th style="text-align:center;">

AIC

</th>

</tr>

</thead>

<tbody>

<tr>

<td style="text-align:left;">

Model 1. Single-level/No opponent

</td>

<td style="text-align:center;">

164,511

</td>

<td style="text-align:center;">

102

</td>

<td style="text-align:center;">

195,215

</td>

</tr>

<tr>

<td style="text-align:left;">

Model 2. Single-level/Opponent attributes

</td>

<td style="text-align:center;">

164,511

</td>

<td style="text-align:center;">

108

</td>

<td style="text-align:center;">

178,943

</td>

</tr>

<tr>

<td style="text-align:left;">

Model 3. Hierarchical: Traffic unit

</td>

<td style="text-align:center;">

164,511

</td>

<td style="text-align:center;">

118

</td>

<td style="text-align:center;">

181,333

</td>

</tr>

<tr>

<td style="text-align:left;">

Model 4. Hierarchical: Opponent)

</td>

<td style="text-align:center;">

164,511

</td>

<td style="text-align:center;">

111

</td>

<td style="text-align:center;">

179,018

</td>

</tr>

</tbody>

</table>

<table>

<caption>

Summary of model estimation results: Data Subset Models

</caption>

<thead>

<tr>

<th style="border-bottom:hidden" colspan="1">

</th>

<th style="border-bottom:hidden" colspan="1">

</th>

<th style="border-bottom:hidden; padding-bottom:0; padding-left:3px;padding-right:3px;text-align: center; " colspan="2">

<div style="border-bottom: 1px solid #ddd; padding-bottom: 5px; ">

Model 1<br>Single-level/No opponent

</div>

</th>

<th style="border-bottom:hidden; padding-bottom:0; padding-left:3px;padding-right:3px;text-align: center; " colspan="2">

<div style="border-bottom: 1px solid #ddd; padding-bottom: 5px; ">

Model 2<br>Single-level/Opponent attributes

</div>

</th>

<th style="border-bottom:hidden; padding-bottom:0; padding-left:3px;padding-right:3px;text-align: center; " colspan="2">

<div style="border-bottom: 1px solid #ddd; padding-bottom: 5px; ">

Model 3<br>Hierarchical: Traffic unit

</div>

</th>

<th style="border-bottom:hidden; padding-bottom:0; padding-left:3px;padding-right:3px;text-align: center; " colspan="2">

<div style="border-bottom: 1px solid #ddd; padding-bottom: 5px; ">

Model 4<br>Hierarchical: Opponent

</div>

</th>

</tr>

<tr>

<th style="text-align:left;">

Model

</th>

<th style="text-align:center;">

</th>

<th style="text-align:center;">

</th>

<th style="text-align:center;">

AIC

</th>

<th style="text-align:center;">

</th>

<th style="text-align:center;">

AIC

</th>

<th style="text-align:center;">

</th>

<th style="text-align:center;">

AIC

</th>

<th style="text-align:center;">

</th>

<th style="text-align:center;">

AIC

</th>

</tr>

</thead>

<tbody>

<tr>

<td style="text-align:left;">

Light duty vehicle vs light duty vehicle

</td>

<td style="text-align:center;">

114,841

</td>

<td style="text-align:center;">

94

</td>

<td style="text-align:center;">

145,390

</td>

<td style="text-align:center;">

97

</td>

<td style="text-align:center;">

143,903

</td>

<td style="text-align:center;">

100

</td>

<td style="text-align:center;">

143,896

</td>

<td style="text-align:center;">

100

</td>

<td style="text-align:center;">

144,004

</td>

</tr>

<tr>

<td style="text-align:left;">

Light duty vehicle vs light truck

</td>

<td style="text-align:center;">

3,237

</td>

<td style="text-align:center;">

79

</td>

<td style="text-align:center;">

3,943

</td>

<td style="text-align:center;">

82

</td>

<td style="text-align:center;">

3,927

</td>

<td style="text-align:center;">

85

</td>

<td style="text-align:center;">

3,937

</td>

<td style="text-align:center;">

85

</td>

<td style="text-align:center;">

3,922

</td>

</tr>

<tr>

<td style="text-align:left;">

Light duty vehicle vs heavy vehicle

</td>

<td style="text-align:center;">

5,013

</td>

<td style="text-align:center;">

88

</td>

<td style="text-align:center;">

5,895

</td>

<td style="text-align:center;">

91

</td>

<td style="text-align:center;">

5,878

</td>

<td style="text-align:center;">

94

</td>

<td style="text-align:center;">

5,888

</td>

<td style="text-align:center;">

94

</td>

<td style="text-align:center;">

5,864

</td>

</tr>

<tr>

<td style="text-align:left;">

Light truck vs light duty vehicle

</td>

<td style="text-align:center;">

3,121

</td>

<td style="text-align:center;">

79

</td>

<td style="text-align:center;">

3,885

</td>

<td style="text-align:center;">

82

</td>

<td style="text-align:center;">

3,877

</td>

<td style="text-align:center;">

85

</td>

<td style="text-align:center;">

3,881

</td>

<td style="text-align:center;">

85

</td>

<td style="text-align:center;">

3,875

</td>

</tr>

<tr>

<td style="text-align:left;">

Light truck vs light truck

</td>

<td style="text-align:center;">

809

</td>

<td style="text-align:center;">

67

</td>

<td style="text-align:center;">

1,170

</td>

<td style="text-align:center;">

70

</td>

<td style="text-align:center;">

1,156

</td>

<td style="text-align:center;">

73

</td>

<td style="text-align:center;">

1,162

</td>

<td style="text-align:center;">

73

</td>

<td style="text-align:center;">

1,155

</td>

</tr>

<tr>

<td style="text-align:left;">

Light truck vs heavy vehicle

</td>

<td style="text-align:center;">

198

</td>

<td style="text-align:center;">

64

</td>

<td style="text-align:center;">

288

</td>

<td style="text-align:center;">

67

</td>

<td style="text-align:center;">

281

</td>

<td style="text-align:center;">

70

</td>

<td style="text-align:center;">

287

</td>

<td style="text-align:center;">

70

</td>

<td style="text-align:center;">

286

</td>

</tr>

<tr>

<td style="text-align:left;">

Heavy vehicle vs light duty vehicle

</td>

<td style="text-align:center;">

4,726

</td>

<td style="text-align:center;">

79

</td>

<td style="text-align:center;">

4,326

</td>

<td style="text-align:center;">

84

</td>

<td style="text-align:center;">

4,283

</td>

<td style="text-align:center;">

86

</td>

<td style="text-align:center;">

4,268

</td>

<td style="text-align:center;">

87

</td>

<td style="text-align:center;">

4,287

</td>

</tr>

<tr>

<td style="text-align:left;">

Heavy vehicle vs light truck

</td>

<td style="text-align:center;">

180

</td>

<td style="text-align:center;">

64

</td>

<td style="text-align:center;">

225

</td>

<td style="text-align:center;">

65

</td>

<td style="text-align:center;">

205

</td>

<td style="text-align:center;">

67

</td>

<td style="text-align:center;">

207

</td>

<td style="text-align:center;">

66

</td>

<td style="text-align:center;">

187

</td>

</tr>

<tr>

<td style="text-align:left;">

Heavy vehicle vs heavy vehicle

</td>

<td style="text-align:center;">

779

</td>

<td style="text-align:center;">

74

</td>

<td style="text-align:center;">

1,147

</td>

<td style="text-align:center;">

77

</td>

<td style="text-align:center;">

1,136

</td>

<td style="text-align:center;">

80

</td>

<td style="text-align:center;">

1,141

</td>

<td style="text-align:center;">

80

</td>

<td style="text-align:center;">

1,137

</td>

</tr>

<tr>

<td style="text-align:left;">

Pedestrian vs light duty vehicle

</td>

<td style="text-align:center;">

7,176

</td>

<td style="text-align:center;">

88

</td>

<td style="text-align:center;">

2,826

</td>

<td style="text-align:center;">

91

</td>

<td style="text-align:center;">

2,821

</td>

<td style="text-align:center;">

91

</td>

<td style="text-align:center;">

2,821

</td>

<td style="text-align:center;">

93

</td>

<td style="text-align:center;">

2,827

</td>

</tr>

<tr>

<td style="text-align:left;">

Pedestrian vs light truck

</td>

<td style="text-align:center;">

328

</td>

<td style="text-align:center;">

62

</td>

<td style="text-align:center;">

202

</td>

<td style="text-align:center;">

65

</td>

<td style="text-align:center;">

200

</td>

<td style="text-align:center;">

65

</td>

<td style="text-align:center;">

200

</td>

<td style="text-align:center;">

68

</td>

<td style="text-align:center;">

206

</td>

</tr>

<tr>

<td style="text-align:left;">

Pedestrian vs heavy vehicle

</td>

<td style="text-align:center;">

376

</td>

<td style="text-align:center;">

64

</td>

<td style="text-align:center;">

409

</td>

<td style="text-align:center;">

67

</td>

<td style="text-align:center;">

410

</td>

<td style="text-align:center;">

67

</td>

<td style="text-align:center;">

410

</td>

<td style="text-align:center;">

70

</td>

<td style="text-align:center;">

417

</td>

</tr>

<tr>

<td style="text-align:left;">

Bicyclist vs light duty vehicle

</td>

<td style="text-align:center;">

3,521

</td>

<td style="text-align:center;">

80

</td>

<td style="text-align:center;">

654

</td>

<td style="text-align:center;">

83

</td>

<td style="text-align:center;">

659

</td>

<td style="text-align:center;">

83

</td>

<td style="text-align:center;">

659

</td>

<td style="text-align:center;">

85

</td>

<td style="text-align:center;">

657

</td>

</tr>

<tr>

<td style="text-align:left;">

Bicyclist vs light truck

</td>

<td style="text-align:center;">

116

</td>

<td style="text-align:center;">

42

</td>

<td style="text-align:center;">

84

</td>

<td style="text-align:center;">

57

</td>

<td style="text-align:center;">

114

</td>

<td style="text-align:center;">

57

</td>

<td style="text-align:center;">

114

</td>

<td style="text-align:center;">

54

</td>

<td style="text-align:center;">

108

</td>

</tr>

<tr>

<td style="text-align:left;">

Bicyclist vs heavy vehicle

</td>

<td style="text-align:center;">

  - 
    
    </td>
    
    <td style="text-align:center;">
    
      - 
        
        </td>
        
        <td style="text-align:center;">
        
          - 
            
            </td>
            
            <td style="text-align:center;">
            
              - 
                
                </td>
                
                <td style="text-align:center;">
                
                  - 
                    
                    </td>
                    
                    <td style="text-align:center;">
                    
                      - 
                        
                        </td>
                        
                        <td style="text-align:center;">
                        
                          - 
                            
                            </td>
                            
                            <td style="text-align:center;">
                            
                              - 
                                
                                </td>
                                
                                <td style="text-align:center;">
                                
                                  - 
                                    
                                    </td>
                                    
                                    </tr>
                                    
                                    <tr>
                                    
                                    <td style="text-align:left;">
                                    
                                    Motorcyclist vs light duty vehicle
                                    
                                    </td>
                                    
                                    <td style="text-align:center;">
                                    
                                    2,298
                                    
                                    </td>
                                    
                                    <td style="text-align:center;">
                                    
                                    78
                                    
                                    </td>
                                    
                                    <td style="text-align:center;">
                                    
                                    1,367
                                    
                                    </td>
                                    
                                    <td style="text-align:center;">
                                    
                                    81
                                    
                                    </td>
                                    
                                    <td style="text-align:center;">
                                    
                                    1,373
                                    
                                    </td>
                                    
                                    <td style="text-align:center;">
                                    
                                    81
                                    
                                    </td>
                                    
                                    <td style="text-align:center;">
                                    
                                    1,373
                                    
                                    </td>
                                    
                                    <td style="text-align:center;">
                                    
                                    84
                                    
                                    </td>
                                    
                                    <td style="text-align:center;">
                                    
                                    1,373
                                    
                                    </td>
                                    
                                    </tr>
                                    
                                    <tr>
                                    
                                    <td style="text-align:left;">
                                    
                                    Motorcyclist vs light truck
                                    
                                    </td>
                                    
                                    <td style="text-align:center;">
                                    
                                    127
                                    
                                    </td>
                                    
                                    <td style="text-align:center;">
                                    
                                    56
                                    
                                    </td>
                                    
                                    <td style="text-align:center;">
                                    
                                    153
                                    
                                    </td>
                                    
                                    <td style="text-align:center;">
                                    
                                    59
                                    
                                    </td>
                                    
                                    <td style="text-align:center;">
                                    
                                    153
                                    
                                    </td>
                                    
                                    <td style="text-align:center;">
                                    
                                    59
                                    
                                    </td>
                                    
                                    <td style="text-align:center;">
                                    
                                    153
                                    
                                    </td>
                                    
                                    <td style="text-align:center;">
                                    
                                    47
                                    
                                    </td>
                                    
                                    <td style="text-align:center;">
                                    
                                    94
                                    
                                    </td>
                                    
                                    </tr>
                                    
                                    <tr>
                                    
                                    <td style="text-align:left;">
                                    
                                    Motorcyclist vs heavy vehicle
                                    
                                    </td>
                                    
                                    <td style="text-align:center;">
                                    
                                    62
                                    
                                    </td>
                                    
                                    <td style="text-align:center;">
                                    
                                    43
                                    
                                    </td>
                                    
                                    <td style="text-align:center;">
                                    
                                    88
                                    
                                    </td>
                                    
                                    <td style="text-align:center;">
                                    
                                    45
                                    
                                    </td>
                                    
                                    <td style="text-align:center;">
                                    
                                    90
                                    
                                    </td>
                                    
                                    <td style="text-align:center;">
                                    
                                    46
                                    
                                    </td>
                                    
                                    <td style="text-align:center;">
                                    
                                    92
                                    
                                    </td>
                                    
                                    <td style="text-align:center;">
                                    
                                    51
                                    
                                    </td>
                                    
                                    <td style="text-align:center;">
                                    
                                    102
                                    
                                    </td>
                                    
                                    </tr>
                                    
                                    </tbody>
                                    
                                    <tfoot>
                                    
                                    <tr>
                                    
                                    <td style="padding: 0; border: 0;" colspan="100%">
                                    
                                    <span style="font-style: italic;">Note:
                                    </span>
                                    
                                    </td>
                                    
                                    </tr>
                                    
                                    <tr>
                                    
                                    <td style="padding: 0; border: 0;" colspan="100%">
                                    
                                    <sup></sup> There are zero cases of
                                    Bicyclist vs heavy vehicle in the
                                    sample
                                    
                                    </td>
                                    
                                    </tr>
                                    
                                    </tfoot>
                                    
                                    </table>

## Outcome shares based on predicted probabilities

In this, and the following section, *backcasting* refers to the
prediction of probabilities and classes of outcomes using the 2016 data
set. When conducting backcasting, the data set is preprocessed in
identical manner as the 2017 data set. In addition, the variables used
in backcasting match exactly those in the models. This means that some
variables were dropped when they were present in the 2016 data set but
not in the models. This tended to happen in the case of relatively rare
outcomes (e.g., in 2016, there was at least one collision between a
heavy vehicle and a light duty vehicle in a school crossing zone; no
such event was observed in 2017).

The shares of each outcome are calculated as the sum of the estimated
probabilities for each observation:

<!--
\begin{equation}
\label{eq:predicted-shares}
\begin{array}{rcl}
\hat{S}_{\text{PDO}} &=& \sum_{itk}\hat{P}(y_{itk}=\text{PDO})\\
\hat{S}_{\text{injury}} &=& \sum_{itk}\hat{P}(y_{itk}=\text{injury})\\
\hat{S}_{\text{fatality}} &=& \sum_{itk}\hat{P}(y_{itk}=\text{fatality})
\end{array}
\end{equation}
-->

![](https://render.githubusercontent.com/render/math?math=%5Cbegin%7Bequation%7D%20%5Clabel%7Beq%3Apredicted-shares%7D%20%5Cbegin%7Barray%7D%7Brcl%7D%20%5Chat%7BS%7D_%7B%5Ctext%7BPDO%7D%7D%20%26%3D%26%20%5Csum_%7Bitk%7D%5Chat%7BP%7D\(y_%7Bitk%7D%3D%5Ctext%7BPDO%7D\)%5C%5C%20%5Chat%7BS%7D_%7B%5Ctext%7Binjury%7D%7D%20%26%3D%26%20%5Csum_%7Bitk%7D%5Chat%7BP%7D\(y_%7Bitk%7D%3D%5Ctext%7Binjury%7D\)%5C%5C%20%5Chat%7BS%7D_%7B%5Ctext%7Bfatality%7D%7D%20%26%3D%26%20%5Csum_%7Bitk%7D%5Chat%7BP%7D\(y_%7Bitk%7D%3D%5Ctext%7Bfatality%7D\)%20%5Cend%7Barray%7D%20%5Cend%7Bequation%7D)

where
![(y\_{itk}=h\_w)](https://render.githubusercontent.com/render/math?math=%5Chat%7BP%7D\(y_%7Bitk%7D%3Dh_w\))
is the estimated probability of outcome
![h\_w](https://render.githubusercontent.com/render/math?math=h_w) for
individual *i* in traffic unit *t* and crash *k*. The estimated share of
outcome *h* is
![\_{h\_w}](https://render.githubusercontent.com/render/math?math=%5Chat%7BS%7D_%7Bh_w%7D).

The estimated shares can be used to assess the ability of the model to
forecast for the population the total number of cases of each outcome. A
summary statistic useful to evaluate the performance is the Average
Percentage Error, or *APE* (see Bogue, Paleti, and Balan 2017, 31),
which is calculated for each outcome as follows:

<!--
\begin{equation}
\label{eq:APE}
APE_{h_w} = \Bigg|\frac{\hat{S}_{h_w} - S_{h_w}}{S_{h_w}}\Bigg|\times 100
\end{equation}
-->

![](https://render.githubusercontent.com/render/math?math=%5Cbegin%7Bequation%7D%20%5Clabel%7Beq%3AAPE%7D%20APE_%7Bh_w%7D%20%3D%20%5CBigg%7C%5Cfrac%7B%5Chat%7BS%7D_%7Bh_w%7D%20-%20S_%7Bh_w%7D%7D%7BS_%7Bh_w%7D%7D%5CBigg%7C%5Ctimes%20100%20%5Cend%7Bequation%7D)

The Weighted Average Percentage Error (*WAPE*) aggregates the *APE* as
follows:

<!--
\begin{equation}
\label{eq:WAPE}
WAPE = \frac{\sum_w^WAPE_{h_w}\times S_{h_w}}{\sum_w^WS_{h_w}}
\end{equation}
-->

![](https://render.githubusercontent.com/render/math?math=%5Cbegin%7Bequation%7D%20%5Clabel%7Beq%3AWAPE%7D%20WAPE%20%3D%20%5Cfrac%7B%5Csum_w%5EWAPE_%7Bh_w%7D%5Ctimes%20S_%7Bh_w%7D%7D%7B%5Csum_w%5EWS_%7Bh_w%7D%7D%20%5Cend%7Bequation%7D)

The results of this exercise are reported in Table . Of the four
full-sample models (Models 1-4), the *APE* of Model 2 is lowest in the
nowcasting exercise for every outcome, with the exception of Fatality,
where Model 4 produces a considerably lower *APE*. When the results are
aggregated by means of the *WAPE*, Model 2 gives marginally better
results than Model 4. It is interesting to see that the four ensemble
models have lower *APE* values across the board in the nowcasting
exercise, and much better *WAPE* than the full sample models. However,
once we turn to the results of the backcasting exercise, these results
do not hold, and it is possible to see that the Average Percentage
Errors of the ensemble worsen considerably, particularly in the case of
Fatality. The Weighted Average Prediction Error of the ensemble models
in the backcasting exercise is also worse than for any of the full
sample models. Excellent in-sample predictions but not as good
out-of-sample predictions are often evidence of overfitting, as in the
case of the ensemble models here.

In terms of backcasting, full sample Model 1 is marginally better than
full sample Models 2 and 3, and better than full sample Model 4. The
reason for this is the lower *APE* of Model 1 when predicting Injury,
the most frequent outcome. However, the performance of Model 1 with
respect to Fatality (the least frequent outcome) is the worst of all
models. Whereas Model 4 has the best performance predicting Fatality,
its performance with respect to other classes of outcomes is less
impressive. Model 3 does better than Model 2 with respect to Injury, but
performs relatively poorly when backcasting Fatality. Overall, Model 2
appears to be the most balanced, with good in-sample performance and
competitive out-of-sample performance that is also balanced with respect
to the various classes of outcomes.

<table>

<caption>

Predicted shares and average prediction errors (APE) by model
(percentages)

</caption>

<thead>

<tr>

<th style="border-bottom:hidden" colspan="1">

</th>

<th style="border-bottom:hidden; padding-bottom:0; padding-left:3px;padding-right:3px;text-align: center; " colspan="3">

<div style="border-bottom: 1px solid #ddd; padding-bottom: 5px; ">

No Injury

</div>

</th>

<th style="border-bottom:hidden; padding-bottom:0; padding-left:3px;padding-right:3px;text-align: center; " colspan="3">

<div style="border-bottom: 1px solid #ddd; padding-bottom: 5px; ">

Injury

</div>

</th>

<th style="border-bottom:hidden; padding-bottom:0; padding-left:3px;padding-right:3px;text-align: center; " colspan="3">

<div style="border-bottom: 1px solid #ddd; padding-bottom: 5px; ">

Fatality

</div>

</th>

<th style="border-bottom:hidden" colspan="1">

</th>

</tr>

<tr>

<th style="text-align:left;">

Model

</th>

<th style="text-align:right;">

Observed

</th>

<th style="text-align:right;">

Predicted

</th>

<th style="text-align:right;">

APE

</th>

<th style="text-align:right;">

Observed

</th>

<th style="text-align:right;">

Predicted

</th>

<th style="text-align:right;">

APE

</th>

<th style="text-align:right;">

Observed

</th>

<th style="text-align:right;">

Predicted

</th>

<th style="text-align:right;">

APE

</th>

<th style="text-align:right;">

WAPE

</th>

</tr>

</thead>

<tbody>

<tr grouplength="8">

<td colspan="11" style="border-bottom: 1px solid;">

<strong>In-sample (nowcasting using 2017 data set, i.e., estimation data
set)</strong>

</td>

</tr>

<tr>

<td style="text-align:left; padding-left: 2em;" indentlevel="1">

Model 1

</td>

<td style="text-align:right;">

78886

</td>

<td style="text-align:right;">

79029.00

</td>

<td style="text-align:right;">

0.18

</td>

<td style="text-align:right;">

84675

</td>

<td style="text-align:right;">

84533.74

</td>

<td style="text-align:right;">

0.17

</td>

<td style="text-align:right;">

950

</td>

<td style="text-align:right;">

948.26

</td>

<td style="text-align:right;">

0.18

</td>

<td style="text-align:right;">

0.17

</td>

</tr>

<tr>

<td style="text-align:left; padding-left: 2em;" indentlevel="1">

Model 2

</td>

<td style="text-align:right;">

78886

</td>

<td style="text-align:right;">

78928.98

</td>

<td style="text-align:right;">

0.05

</td>

<td style="text-align:right;">

84675

</td>

<td style="text-align:right;">

84641.94

</td>

<td style="text-align:right;">

0.04

</td>

<td style="text-align:right;">

950

</td>

<td style="text-align:right;">

940.08

</td>

<td style="text-align:right;">

1.04

</td>

<td style="text-align:right;">

0.05

</td>

</tr>

<tr>

<td style="text-align:left; padding-left: 2em;" indentlevel="1">

Model 3

</td>

<td style="text-align:right;">

78886

</td>

<td style="text-align:right;">

79027.29

</td>

<td style="text-align:right;">

0.18

</td>

<td style="text-align:right;">

84675

</td>

<td style="text-align:right;">

84512.50

</td>

<td style="text-align:right;">

0.19

</td>

<td style="text-align:right;">

950

</td>

<td style="text-align:right;">

971.21

</td>

<td style="text-align:right;">

2.23

</td>

<td style="text-align:right;">

0.20

</td>

</tr>

<tr>

<td style="text-align:left; padding-left: 2em;" indentlevel="1">

Model 4

</td>

<td style="text-align:right;">

78886

</td>

<td style="text-align:right;">

78939.18

</td>

<td style="text-align:right;">

0.07

</td>

<td style="text-align:right;">

84675

</td>

<td style="text-align:right;">

84622.54

</td>

<td style="text-align:right;">

0.06

</td>

<td style="text-align:right;">

950

</td>

<td style="text-align:right;">

949.28

</td>

<td style="text-align:right;">

0.08

</td>

<td style="text-align:right;">

0.06

</td>

</tr>

<tr>

<td style="text-align:left; padding-left: 2em;" indentlevel="1">

Model 1 Ensemble

</td>

<td style="text-align:right;">

62413

</td>

<td style="text-align:right;">

62402.78

</td>

<td style="text-align:right;">

0.02

</td>

<td style="text-align:right;">

83564

</td>

<td style="text-align:right;">

83573.58

</td>

<td style="text-align:right;">

0.01

</td>

<td style="text-align:right;">

931

</td>

<td style="text-align:right;">

931.64

</td>

<td style="text-align:right;">

0.07

</td>

<td style="text-align:right;">

0.01

</td>

</tr>

<tr>

<td style="text-align:left; padding-left: 2em;" indentlevel="1">

Model 2 Ensemble

</td>

<td style="text-align:right;">

62417

</td>

<td style="text-align:right;">

62407.00

</td>

<td style="text-align:right;">

0.02

</td>

<td style="text-align:right;">

83595

</td>

<td style="text-align:right;">

83604.14

</td>

<td style="text-align:right;">

0.01

</td>

<td style="text-align:right;">

931

</td>

<td style="text-align:right;">

931.86

</td>

<td style="text-align:right;">

0.09

</td>

<td style="text-align:right;">

0.01

</td>

</tr>

<tr>

<td style="text-align:left; padding-left: 2em;" indentlevel="1">

Model 3 Ensemble

</td>

<td style="text-align:right;">

62411

</td>

<td style="text-align:right;">

62401.23

</td>

<td style="text-align:right;">

0.02

</td>

<td style="text-align:right;">

83596

</td>

<td style="text-align:right;">

83604.71

</td>

<td style="text-align:right;">

0.01

</td>

<td style="text-align:right;">

933

</td>

<td style="text-align:right;">

934.06

</td>

<td style="text-align:right;">

0.11

</td>

<td style="text-align:right;">

0.01

</td>

</tr>

<tr>

<td style="text-align:left; padding-left: 2em;" indentlevel="1">

Model 4 Ensemble

</td>

<td style="text-align:right;">

62405

</td>

<td style="text-align:right;">

62395.28

</td>

<td style="text-align:right;">

0.02

</td>

<td style="text-align:right;">

83578

</td>

<td style="text-align:right;">

83586.75

</td>

<td style="text-align:right;">

0.01

</td>

<td style="text-align:right;">

932

</td>

<td style="text-align:right;">

932.97

</td>

<td style="text-align:right;">

0.10

</td>

<td style="text-align:right;">

0.01

</td>

</tr>

<tr grouplength="7">

<td colspan="11" style="border-bottom: 1px solid;">

<strong>Out-of-sample (backcasting using 2016 data set)</strong>

</td>

</tr>

<tr>

<td style="text-align:left; padding-left: 2em;" indentlevel="1">

Model 1

</td>

<td style="text-align:right;">

96860

</td>

<td style="text-align:right;">

96364.67

</td>

<td style="text-align:right;">

0.51

</td>

<td style="text-align:right;">

101605

</td>

<td style="text-align:right;">

102002.59

</td>

<td style="text-align:right;">

0.39

</td>

<td style="text-align:right;">

1109

</td>

<td style="text-align:right;">

1206.74

</td>

<td style="text-align:right;">

8.81

</td>

<td style="text-align:right;">

0.50

</td>

</tr>

<tr>

<td style="text-align:left; padding-left: 2em;" indentlevel="1">

Model 2

</td>

<td style="text-align:right;">

96860

</td>

<td style="text-align:right;">

96361.41

</td>

<td style="text-align:right;">

0.51

</td>

<td style="text-align:right;">

101605

</td>

<td style="text-align:right;">

102112.08

</td>

<td style="text-align:right;">

0.50

</td>

<td style="text-align:right;">

1109

</td>

<td style="text-align:right;">

1100.51

</td>

<td style="text-align:right;">

0.77

</td>

<td style="text-align:right;">

0.51

</td>

</tr>

<tr>

<td style="text-align:left; padding-left: 2em;" indentlevel="1">

Model 3

</td>

<td style="text-align:right;">

96860

</td>

<td style="text-align:right;">

96354.01

</td>

<td style="text-align:right;">

0.52

</td>

<td style="text-align:right;">

101605

</td>

<td style="text-align:right;">

102086.18

</td>

<td style="text-align:right;">

0.47

</td>

<td style="text-align:right;">

1109

</td>

<td style="text-align:right;">

1133.82

</td>

<td style="text-align:right;">

2.24

</td>

<td style="text-align:right;">

0.51

</td>

</tr>

<tr>

<td style="text-align:left; padding-left: 2em;" indentlevel="1">

Model 4

</td>

<td style="text-align:right;">

96860

</td>

<td style="text-align:right;">

96325.85

</td>

<td style="text-align:right;">

0.55

</td>

<td style="text-align:right;">

101605

</td>

<td style="text-align:right;">

102136.72

</td>

<td style="text-align:right;">

0.52

</td>

<td style="text-align:right;">

1109

</td>

<td style="text-align:right;">

1111.43

</td>

<td style="text-align:right;">

0.22

</td>

<td style="text-align:right;">

0.54

</td>

</tr>

<tr>

<td style="text-align:left; padding-left: 2em;" indentlevel="1">

Model 1 Ensemble

</td>

<td style="text-align:right;">

77457

</td>

<td style="text-align:right;">

76822.49

</td>

<td style="text-align:right;">

0.82

</td>

<td style="text-align:right;">

100013

</td>

<td style="text-align:right;">

100580.60

</td>

<td style="text-align:right;">

0.57

</td>

<td style="text-align:right;">

1072

</td>

<td style="text-align:right;">

1138.91

</td>

<td style="text-align:right;">

6.24

</td>

<td style="text-align:right;">

0.71

</td>

</tr>

<tr>

<td style="text-align:left; padding-left: 2em;" indentlevel="1">

Model 2 Ensemble

</td>

<td style="text-align:right;">

77459

</td>

<td style="text-align:right;">

76799.11

</td>

<td style="text-align:right;">

0.85

</td>

<td style="text-align:right;">

100049

</td>

<td style="text-align:right;">

100630.48

</td>

<td style="text-align:right;">

0.58

</td>

<td style="text-align:right;">

1071

</td>

<td style="text-align:right;">

1149.41

</td>

<td style="text-align:right;">

7.32

</td>

<td style="text-align:right;">

0.74

</td>

</tr>

<tr>

<td style="text-align:left; padding-left: 2em;" indentlevel="1">

Model 3 Ensemble

</td>

<td style="text-align:right;">

77459

</td>

<td style="text-align:right;">

76786.76

</td>

<td style="text-align:right;">

0.87

</td>

<td style="text-align:right;">

100050

</td>

<td style="text-align:right;">

100644.29

</td>

<td style="text-align:right;">

0.59

</td>

<td style="text-align:right;">

1072

</td>

<td style="text-align:right;">

1149.95

</td>

<td style="text-align:right;">

7.27

</td>

<td style="text-align:right;">

0.75

</td>

</tr>

<tr>

<td style="text-align:left;">

Model 4 Ensemble

</td>

<td style="text-align:right;">

77461

</td>

<td style="text-align:right;">

76766.08

</td>

<td style="text-align:right;">

0.90

</td>

<td style="text-align:right;">

100029

</td>

<td style="text-align:right;">

100630.21

</td>

<td style="text-align:right;">

0.60

</td>

<td style="text-align:right;">

1070

</td>

<td style="text-align:right;">

1163.71

</td>

<td style="text-align:right;">

8.76

</td>

<td style="text-align:right;">

0.78

</td>

</tr>

</tbody>

<tfoot>

<tr>

<td style="padding: 0; border: 0;" colspan="100%">

<span style="font-style: italic;">Note: </span>

</td>

</tr>

<tr>

<td style="padding: 0; border: 0;" colspan="100%">

<sup></sup> Model 1. Single-level/No opponent

</td>

</tr>

<tr>

<td style="padding: 0; border: 0;" colspan="100%">

<sup></sup> Model 2. Single-level/Opponent attributes

</td>

</tr>

<tr>

<td style="padding: 0; border: 0;" colspan="100%">

<sup></sup> Model 3. Hierarchical: Traffic unit

</td>

</tr>

<tr>

<td style="padding: 0; border: 0;" colspan="100%">

<sup></sup> Model 4. Hierarchical: Opponent

</td>

</tr>

</tfoot>

</table>

## Outcome frequency based on predicted classes

*APE* and *WAPE* are summary measures of the performance of models at
the aggregated level. Aggregate-level predictions (i.e., shares of
outcomes) are of interest from a population health perspective. In other
cases, an analyst might be interested in the predicted outcomes at the
individual level. In this section we examine the frequency of outcomes
based on predicted classes, using the same two settings as above:
nowcasting and backcasting.

The individual-level outcomes are examined using an array of
verification statistics. Verification statistics are widely used in the
evaluation of predictive approaches were the outcomes are categorical,
and are often based on the analysis of *confusion matrices* (e.g.,
Provost and Kohavi 1998; Beguería 2006). Confusion matrices are
cross-tabulations of *observed* and *predicted* classes. In a two-by-two
confusion matrix there are four possible combinations of observed to
predicted classes: hits, misses, false alarms, and correct non-events,
as shown in Table . When the outcome has more than two classes, the
confusion matrix is converted to a two-by-two table to calculate
verification statistics.

<table>

<caption>

Example of a two-by-two confusion matrix

</caption>

<thead>

<tr>

<th style="border-bottom:hidden" colspan="1">

</th>

<th style="border-bottom:hidden; padding-bottom:0; padding-left:3px;padding-right:3px;text-align: center; " colspan="2">

<div style="border-bottom: 1px solid #ddd; padding-bottom: 5px; ">

Observed

</div>

</th>

<th style="border-bottom:hidden" colspan="1">

</th>

</tr>

<tr>

<th style="text-align:left;">

Predicted

</th>

<th style="text-align:center;">

Yes

</th>

<th style="text-align:center;">

No

</th>

<th style="text-align:center;">

Marginal Total

</th>

</tr>

</thead>

<tbody>

<tr>

<td style="text-align:left;">

Yes

</td>

<td style="text-align:center;">

Hit

</td>

<td style="text-align:center;">

False Alarm

</td>

<td style="text-align:center;">

Predicted Yes

</td>

</tr>

<tr>

<td style="text-align:left;">

No

</td>

<td style="text-align:center;">

Miss

</td>

<td style="text-align:center;">

Correct Non-event

</td>

<td style="text-align:center;">

Predicted No

</td>

</tr>

<tr>

<td style="text-align:left;">

Marginal Total

</td>

<td style="text-align:center;">

Observed Yes

</td>

<td style="text-align:center;">

Observed No

</td>

<td style="text-align:center;">

</td>

</tr>

</tbody>

</table>

The statistics used in our assessment are summarized in Table ,
including brief descriptions of their interpretation. The statistics
evaluate different aspects of the performance of a model. Some are
concerned with the ability of the model to be right, others are
concerned with the ability of the model to match the observed outcomes,
and yet others measure the ability of the model to not be wrong. These
verification statistics are discussed briefly next.

Percent correct and percent correct by class *PC* and
![PC\_c](https://render.githubusercontent.com/render/math?math=PC_c) are
relatively simple statistics, and are calculated as the proportion of
correct predictions (i.e., hits and correct non-events) relative to the
number of events, for the whole table *PC* or for one class only
![PC\_c](https://render.githubusercontent.com/render/math?math=PC_c).

Bias (*B*) measures for each outcome class the proportion of total
predictions by class (e.g., hits as well as false alarms) relative to
the total number of cases observed for that class. For this reason, it
is possible for predictions to have low bias (values closer to 1) but
still do poorly in terms of hits.

Critical Success Index (*CSI*) evaluates forecasting skill while
assuming that the number of correct non-events is inconsequential for
demonstrating skill. Accordingly, the statistic is calculated as the
proportion of hits relative to the sum of hits plus false alarms plus
misses.

Probability of False Detection (*F*) is the proportion of false alarms
relative to the total number of times that the event is not observed.
This statistic measures the frequency with which the model incorrectly
predicts an event, but not when it incorrectly misses it. The
Probability of Detection (*POD*), in contrast, measures the frequency
with which the model correctly predicts a class, relative to the number
of cases of that class.

The False Alarm Ratio (*FAR*) is the fraction of predictions by class
that were false alarms evaluate a different way in which a model can
make equivocal predictions. In this case, lower scores are better.

The last three verification statistics that we consider are skill scores
that simultaneously consider different aspects of prediction, and are
therefore overall indicators of prediction skill. Heidke’s Skill Score
(*HSS*) is the fraction of correct predictions above those that could be
attributed to chance. Peirce’s Skill Score (*PSS*) combines the
Probability of Detection (*POD*) of a model and its Probability of False
Detection (*F*) to measure the skill of a model to discriminate the
classes of outcomes. Lastly, Gerrity Score (*GS*) is a measure of the
model’s skill predicting the correct classes that tends to reward
correct forecasts of the least frequent class.

We discuss the results of calculating this battery of verification
statistics, first for the nowcasting case (Table ) and subsequently for
the backcasting case (Table ).

<table>

<caption>

Verification statistics

</caption>

<thead>

<tr>

<th style="text-align:left;">

Statistic

</th>

<th style="text-align:left;">

Description

</th>

<th style="text-align:left;">

Notes

</th>

</tr>

</thead>

<tbody>

<tr>

<td style="text-align:left;">

Percent Correct (\(PC\))

</td>

<td style="text-align:left;width: 30em; ">

Total hits and correct non-events divided by number of cases

</td>

<td style="text-align:left;width: 30em; ">

Strongly influenced by most common category

</td>

</tr>

<tr>

<td style="text-align:left;">

Percent Correct by Class (\(PC_c\))

</td>

<td style="text-align:left;width: 30em; ">

Same as Percent Correct but by category

</td>

<td style="text-align:left;width: 30em; ">

Strongly influenced by most common category

</td>

</tr>

<tr>

<td style="text-align:left;">

Bias (\(B\))

</td>

<td style="text-align:left;width: 30em; ">

Total predicted by category, divided by total observed by category

</td>

<td style="text-align:left;width: 30em; ">

\(B&gt;1\): class is overpredicted; \(B&lt;1\): class is underpredicted

</td>

</tr>

<tr>

<td style="text-align:left;">

Critical Success Index (\(CSI\))

</td>

<td style="text-align:left;width: 30em; ">

Total hits divided by total hits + false alarms + misses

</td>

<td style="text-align:left;width: 30em; ">

\(CSI = 1\): perfect score; \(CSI = 0\): no skill

</td>

</tr>

<tr>

<td style="text-align:left;">

Probability of False Detection (\(F\))

</td>

<td style="text-align:left;width: 30em; ">

Proportion of no events forecast as yes; sensitive to false alarms but
ignores misses

</td>

<td style="text-align:left;width: 30em; ">

\(F = 0\): perfect score

</td>

</tr>

<tr>

<td style="text-align:left;">

Probability of Detection (\(POD\))

</td>

<td style="text-align:left;width: 30em; ">

Total hits divided by total observed by class

</td>

<td style="text-align:left;width: 30em; ">

\(POD = 1\): perfect score

</td>

</tr>

<tr>

<td style="text-align:left;">

False Alarm Ratio (\(FAR\))

</td>

<td style="text-align:left;width: 30em; ">

Total false alarms divided by total forecast yes by class; measures
fraction of predicted yes that did not occur

</td>

<td style="text-align:left;width: 30em; ">

\(FAR = 0\): perfect score

</td>

</tr>

<tr>

<td style="text-align:left;">

Heidke Skill Score (\(HSS\))

</td>

<td style="text-align:left;width: 30em; ">

Fraction of correct predictions after removing predictions attributable
to chance; measures fractional improvement over random; tends to reward
conservative forecasts

</td>

<td style="text-align:left;width: 30em; ">

\(HSS = 1\): perfect score; \(HSS = 0\): no skill; \(HSS &lt; 0\):
random is better

</td>

</tr>

<tr>

<td style="text-align:left;">

Peirce Skill Score (\(PSS\))

</td>

<td style="text-align:left;width: 30em; ">

Combines \(POD\) and \(F\); measures ability to separate yes events from
no events; tends to reward conservative forecasts

</td>

<td style="text-align:left;width: 30em; ">

\(PSS = 1\): perfect score; \(PSS = 0\): no skill

</td>

</tr>

<tr>

<td style="text-align:left;">

Gerrity Score (\(GS\))

</td>

<td style="text-align:left;width: 30em; ">

Measures accuracy of predicting the correct category, relative to
random; tends to reward correct forecasts of less likely category

</td>

<td style="text-align:left;width: 30em; ">

\(GS = 1\): perfect score; \(GS = 0\): no skill

</td>

</tr>

</tbody>

</table>

### Nowcasting: verification statistics

At first glance, the results of the verification statistics (Table )
make it clear that no model under comparison is consistently a top
performer from every aspect of prediction. Recalling Box’s aphorism, all
models are wrong but some are useful - in this case it just so happens
that some models are more wrong than others in subtly different ways.
That said, it is noticeable that the worst scores across the board tend
to accrue to Model 1 in its full sample and ensemble versions. On the
other hand, Model 2 (full sample) concentrates most of the best scores
and second best scores of all the models, but also some of the worst
scores for Fatality. Model 4, in contrast, has most of the second best
scores and a few top scores, but not a single worst score.

Of all the models, Model 2 (full sample) performs best in terms of
Percent Correct, followed by Model 4 (full sample). The worst performer
from this perspective is Model 1 (full sample), with a *PC* score
several percentage points below the top models. The second score is
Percent Correct by Class
(![PC\_c](https://render.githubusercontent.com/render/math?math=PC_c)).
This score is calculated individually for each outcome class. Model 2
(full sample), has the best performance for outcomes No Injury and
Injury, and the second best score for Fatality. Model 4 (full sample)
has the best score for Fatality, and is second best for No Injury and
Injury. Model 1 (full sample) has worst scores for No Injury and Injury
whereas its ensemble version has the worst score for Fatality. It is
important to note that *PC* and
\(![PC_c](https://render.githubusercontent.com/render/math?math=PC_c)\)
are heavily influenced by the most common category, something that can
be particularly appreciated in the scores for Fatality. The scores for
this class of outcome are generally high, despite the fact that the
number of hits are relatively low; the high values of
![PC\_c](https://render.githubusercontent.com/render/math?math=PC_c) in
this case are due to the high occurrence of correct non-events elsewhere
in the table.

The models with the best performance in terms of *B* are Model 1 (full
sample) for No Injury and Injury, and Model 3 (ensemble) for Fatality.
Model 4 (full sample) is the second best performer for No Injury and
Injury, and Model 4 (ensemble) is second best performer for Fatality.
Model 1 (ensemble) has the worst bias for No Injury and Injury, whereas
Model 2 (full sample) has the worst bias for Fatality.

No model performs uniformly best from the perspective of Critical
Success Index (*CSI*). Model 2 (full sample) has the best *CSI* for No
Injury, Model 2 (ensemble) has the best score for Injury, and Model 4
(ensemble) the best score for Fatality. On the other hand, Model 1
(ensemble) has the worst score for No Injury, Model 1 (full sample) the
worst score for Injury, and Model 2 the worst score for Fatality. These
scores indicates that the models are not particularly skilled at
predicting the corresponding classes correctly, given the frequency with
which they give false alarms or miss the class.

The lowest probability of false detection *F* in the case of No Injury
is 19.17% for Model 2 (ensemble), with every other model having values
lower than 21%, with the exception of Model 1 (full sample) that has a
score of 26.46%. With respect to Injury, the lowest probabilities range
35.8% and 35.29% and 35.35% for Models 4 (full sample) and 2 (full
sample). In contrast, the highest probability of false detection for
Injury is 45.12% for Model 1 (ensemble). The scores for *F* for Fatality
are all extremely low as a consequence of the very low frequency of this
class of outcome in the sample.

As with some other verification statistics, no model is consistently a
best performer in terms of *POD*. Model 4 (full sample) has the highest
probability of detection for No Injury (65.38%), followed by Model 2
(65.32%), whereas the worst probability of detection is by Model 1
(ensemble) with a score of 55.54%. In terms of Injury, all models have
*POD* higher than 79%, and the highest score is 80.67% for Model 2
(ensemble). The exception is Model 1 (full sample), which has a
considerably lower *POD* of Injury with a score of 73.36%. Lastly, in
terms of Fatalities, all models have very low probabilities of
detection, ranging from a high of 4.94% in the case of Model 4
(ensemble) to a worst score of 0.11% in the case of Model 2 (full
sample).

Model 2 (full sample) has the best *FAR* statistic for No Injury, as
only 25.05% of predictions for this class are false alarms. The next
best score is by Model 4 (full sample), with only 25.23% of No Injury
predictions being false alarms. The worst performance in this class is
by Model 1 (ensemble), which produces almost a third of false alarms in
its predictions of No Injury. In the case of Injury, the False Alarm
Ratio ranges from a low of 29.15% by Model (ensemble), with every other
model having scores lower than 30% except Model 1 (full sample), that
gives almost 32% of false alarms. In terms of Fatality, the lowest *FAR*
is also for Model 4 (ensemble) with only 17.86% of false alarms, whereas
the worst performance is by Model 2 (full sample), which produces over
95% of false alarms.

The skill scores help to remove some of the ambiguity regarding the
overall performance of a model. In this way, we know that Model 2 (full
sample) does not do particularly well with the class Fatality - however,
of all models, it tends to have the best overall performance. Its *HSS*,
for example, suggests that it achieves 44.74% of correct predictions
after removing correct predictions attributable to chance. In contrast,
the lowest score is for Model 1 (ensemble), which only achieves 36.26%
correct predictions after removing those attributable to chance. Model 2
(full sample) also has the highest *PSS* and the second highest *GS*.
Model 4 (full sample) has the highest *GS* and the second highest *HSS*
and *PSS*.

<table>

<caption>

Assessment of in-sample outcomes (nowcasting using 2017 data set, i.e.,
estimation data set)

</caption>

<thead>

<tr>

<th style="border-bottom:hidden; padding-bottom:0; padding-left:3px;padding-right:3px;text-align: center; " colspan="1">

<div style="border-bottom: 1px solid #ddd; padding-bottom: 5px; ">

Predicted

</div>

</th>

<th style="border-bottom:hidden; padding-bottom:0; padding-left:3px;padding-right:3px;text-align: center; " colspan="3">

<div style="border-bottom: 1px solid #ddd; padding-bottom: 5px; ">

Observed Outcome

</div>

</th>

<th style="border-bottom:hidden; padding-bottom:0; padding-left:3px;padding-right:3px;text-align: center; " colspan="10">

<div style="border-bottom: 1px solid #ddd; padding-bottom: 5px; ">

Verification Statistics

</div>

</th>

</tr>

<tr>

<th style="text-align:left;">

Outcome

</th>

<th style="text-align:right;">

No Injury

</th>

<th style="text-align:right;">

Injury

</th>

<th style="text-align:right;">

Fatality

</th>

<th style="text-align:left;">

</th>

<th style="text-align:left;">

</th>

<th style="text-align:left;">

Bias\(^1\)

</th>

<th style="text-align:left;">

</th>

<th style="text-align:left;">

</th>

<th style="text-align:left;">

</th>

<th style="text-align:left;">

</th>

<th style="text-align:left;">

</th>

<th style="text-align:left;">

</th>

<th style="text-align:left;">

</th>

</tr>

</thead>

<tbody>

<tr grouplength="3">

<td colspan="14" style="border-bottom: 1px solid;">

<strong>Model 1. Single-level/No opponent</strong>

</td>

</tr>

<tr>

<td style="text-align:left; padding-left: 2em;" indentlevel="1">

No Injury

</td>

<td style="text-align:right;">

50652

</td>

<td style="text-align:right;">

22503

</td>

<td style="text-align:right;">

150

</td>

<td style="text-align:left;vertical-align: middle !important;" rowspan="3">

</td>

<td style="text-align:left;">

</td>

<td style="text-align:left;">

</td>

<td style="text-align:left;">

</td>

<td style="text-align:left;">

</td>

<td style="text-align:left;">

</td>

<td style="text-align:left;">

</td>

<td style="text-align:left;vertical-align: middle !important;" rowspan="3">

</td>

<td style="text-align:left;vertical-align: middle !important;" rowspan="3">

</td>

<td style="text-align:left;vertical-align: middle !important;" rowspan="3">

</td>

</tr>

<tr>

<td style="text-align:left; padding-left: 2em;" indentlevel="1">

Injury

</td>

<td style="text-align:right;">

28232

</td>

<td style="text-align:right;">

62121

</td>

<td style="text-align:right;">

797

</td>

<td style="text-align:left;">

</td>

<td style="text-align:left;">

</td>

<td style="text-align:left;">

</td>

<td style="text-align:left;">

</td>

<td style="text-align:left;">

</td>

<td style="text-align:left;">

</td>

</tr>

<tr>

<td style="text-align:left; padding-left: 2em;" indentlevel="1">

Fatality

</td>

<td style="text-align:right;">

2

</td>

<td style="text-align:right;">

51

</td>

<td style="text-align:right;">

3

</td>

<td style="text-align:left;">

</td>

<td style="text-align:left;">

</td>

<td style="text-align:left;">

</td>

<td style="text-align:left;">

</td>

<td style="text-align:left;">

</td>

<td style="text-align:left;">

</td>

</tr>

<tr grouplength="3">

<td colspan="14" style="border-bottom: 1px solid;">

<strong>Model 2. Single-level/Opponent attributes</strong>

</td>

</tr>

<tr>

<td style="text-align:left; padding-left: 2em;" indentlevel="1">

No Injury

</td>

<td style="text-align:right;">

51530

</td>

<td style="text-align:right;">

17136

</td>

<td style="text-align:right;">

85

</td>

<td style="text-align:left;vertical-align: middle !important;" rowspan="3">

</td>

<td style="text-align:left;">

</td>

<td style="text-align:left;">

</td>

<td style="text-align:left;">

</td>

<td style="text-align:left;">

</td>

<td style="text-align:left;">

</td>

<td style="text-align:left;">

</td>

<td style="text-align:left;vertical-align: middle !important;" rowspan="3">

</td>

<td style="text-align:left;vertical-align: middle !important;" rowspan="3">

</td>

<td style="text-align:left;vertical-align: middle !important;" rowspan="3">

</td>

</tr>

<tr>

<td style="text-align:left; padding-left: 2em;" indentlevel="1">

Injury

</td>

<td style="text-align:right;">

27356

</td>

<td style="text-align:right;">

67515

</td>

<td style="text-align:right;">

864

</td>

<td style="text-align:left;">

</td>

<td style="text-align:left;">

</td>

<td style="text-align:left;">

</td>

<td style="text-align:left;">

</td>

<td style="text-align:left;">

</td>

<td style="text-align:left;">

</td>

</tr>

<tr>

<td style="text-align:left; padding-left: 2em;" indentlevel="1">

Fatality

</td>

<td style="text-align:right;">

0

</td>

<td style="text-align:right;">

24

</td>

<td style="text-align:right;">

1

</td>

<td style="text-align:left;">

</td>

<td style="text-align:left;">

</td>

<td style="text-align:left;">

</td>

<td style="text-align:left;">

</td>

<td style="text-align:left;">

</td>

<td style="text-align:left;">

</td>

</tr>

<tr grouplength="3">

<td colspan="14" style="border-bottom: 1px solid;">

<strong>Model 3. Hierarchical: Traffic unit</strong>

</td>

</tr>

<tr>

<td style="text-align:left; padding-left: 2em;" indentlevel="1">

No Injury

</td>

<td style="text-align:right;">

51102

</td>

<td style="text-align:right;">

17297

</td>

<td style="text-align:right;">

79

</td>

<td style="text-align:left;vertical-align: middle !important;" rowspan="3">

</td>

<td style="text-align:left;">

</td>

<td style="text-align:left;">

</td>

<td style="text-align:left;">

</td>

<td style="text-align:left;">

</td>

<td style="text-align:left;">

</td>

<td style="text-align:left;">

</td>

<td style="text-align:left;vertical-align: middle !important;" rowspan="3">

</td>

<td style="text-align:left;vertical-align: middle !important;" rowspan="3">

</td>

<td style="text-align:left;vertical-align: middle !important;" rowspan="3">

</td>

</tr>

<tr>

<td style="text-align:left; padding-left: 2em;" indentlevel="1">

Injury

</td>

<td style="text-align:right;">

27784

</td>

<td style="text-align:right;">

67337

</td>

<td style="text-align:right;">

868

</td>

<td style="text-align:left;">

</td>

<td style="text-align:left;">

</td>

<td style="text-align:left;">

</td>

<td style="text-align:left;">

</td>

<td style="text-align:left;">

</td>

<td style="text-align:left;">

</td>

</tr>

<tr>

<td style="text-align:left; padding-left: 2em;" indentlevel="1">

Fatality

</td>

<td style="text-align:right;">

0

</td>

<td style="text-align:right;">

41

</td>

<td style="text-align:right;">

3

</td>

<td style="text-align:left;">

</td>

<td style="text-align:left;">

</td>

<td style="text-align:left;">

</td>

<td style="text-align:left;">

</td>

<td style="text-align:left;">

</td>

<td style="text-align:left;">

</td>

</tr>

<tr grouplength="3">

<td colspan="14" style="border-bottom: 1px solid;">

<strong>Model 4. Hierarchical: Opponent</strong>

</td>

</tr>

<tr>

<td style="text-align:left; padding-left: 2em;" indentlevel="1">

No Injury

</td>

<td style="text-align:right;">

51575

</td>

<td style="text-align:right;">

17317

</td>

<td style="text-align:right;">

84

</td>

<td style="text-align:left;vertical-align: middle !important;" rowspan="3">

</td>

<td style="text-align:left;">

</td>

<td style="text-align:left;">

</td>

<td style="text-align:left;">

</td>

<td style="text-align:left;">

</td>

<td style="text-align:left;">

</td>

<td style="text-align:left;">

</td>

<td style="text-align:left;vertical-align: middle !important;" rowspan="3">

</td>

<td style="text-align:left;vertical-align: middle !important;" rowspan="3">

</td>

<td style="text-align:left;vertical-align: middle !important;" rowspan="3">

</td>

</tr>

<tr>

<td style="text-align:left; padding-left: 2em;" indentlevel="1">

Injury

</td>

<td style="text-align:right;">

27311

</td>

<td style="text-align:right;">

67335

</td>

<td style="text-align:right;">

863

</td>

<td style="text-align:left;">

</td>

<td style="text-align:left;">

</td>

<td style="text-align:left;">

</td>

<td style="text-align:left;">

</td>

<td style="text-align:left;">

</td>

<td style="text-align:left;">

</td>

</tr>

<tr>

<td style="text-align:left; padding-left: 2em;" indentlevel="1">

Fatality

</td>

<td style="text-align:right;">

0

</td>

<td style="text-align:right;">

23

</td>

<td style="text-align:right;">

3

</td>

<td style="text-align:left;">

</td>

<td style="text-align:left;">

</td>

<td style="text-align:left;">

</td>

<td style="text-align:left;">

</td>

<td style="text-align:left;">

</td>

<td style="text-align:left;">

</td>

</tr>

<tr grouplength="3">

<td colspan="14" style="border-bottom: 1px solid;">

<strong>Model 1 Ensemble. Single-level/No opponent</strong>

</td>

</tr>

<tr>

<td style="text-align:left; padding-left: 2em;" indentlevel="1">

No Injury

</td>

<td style="text-align:right;">

34664

</td>

<td style="text-align:right;">

16433

</td>

<td style="text-align:right;">

63

</td>

<td style="text-align:left;vertical-align: middle !important;" rowspan="3">

</td>

<td style="text-align:left;">

</td>

<td style="text-align:left;">

</td>

<td style="text-align:left;">

</td>

<td style="text-align:left;">

</td>

<td style="text-align:left;">

</td>

<td style="text-align:left;">

</td>

<td style="text-align:left;vertical-align: middle !important;" rowspan="3">

</td>

<td style="text-align:left;vertical-align: middle !important;" rowspan="3">

</td>

<td style="text-align:left;vertical-align: middle !important;" rowspan="3">

</td>

</tr>

<tr>

<td style="text-align:left; padding-left: 2em;" indentlevel="1">

Injury

</td>

<td style="text-align:right;">

27749

</td>

<td style="text-align:right;">

67121

</td>

<td style="text-align:right;">

829

</td>

<td style="text-align:left;">

</td>

<td style="text-align:left;">

</td>

<td style="text-align:left;">

</td>

<td style="text-align:left;">

</td>

<td style="text-align:left;">

</td>

<td style="text-align:left;">

</td>

</tr>

<tr>

<td style="text-align:left; padding-left: 2em;" indentlevel="1">

Fatality

</td>

<td style="text-align:right;">

0

</td>

<td style="text-align:right;">

10

</td>

<td style="text-align:right;">

39

</td>

<td style="text-align:left;">

</td>

<td style="text-align:left;">

</td>

<td style="text-align:left;">

</td>

<td style="text-align:left;">

</td>

<td style="text-align:left;">

</td>

<td style="text-align:left;">

</td>

</tr>

<tr grouplength="3">

<td colspan="14" style="border-bottom: 1px solid;">

<strong>Model 2 Ensemble. Single-level/Opponent attributes</strong>

</td>

</tr>

<tr>

<td style="text-align:left; padding-left: 2em;" indentlevel="1">

No Injury

</td>

<td style="text-align:right;">

35443

</td>

<td style="text-align:right;">

16146

</td>

<td style="text-align:right;">

60

</td>

<td style="text-align:left;vertical-align: middle !important;" rowspan="3">

</td>

<td style="text-align:left;">

</td>

<td style="text-align:left;">

</td>

<td style="text-align:left;">

</td>

<td style="text-align:left;">

</td>

<td style="text-align:left;">

</td>

<td style="text-align:left;">

</td>

<td style="text-align:left;vertical-align: middle !important;" rowspan="3">

</td>

<td style="text-align:left;vertical-align: middle !important;" rowspan="3">

</td>

<td style="text-align:left;vertical-align: middle !important;" rowspan="3">

</td>

</tr>

<tr>

<td style="text-align:left; padding-left: 2em;" indentlevel="1">

Injury

</td>

<td style="text-align:right;">

26974

</td>

<td style="text-align:right;">

67436

</td>

<td style="text-align:right;">

829

</td>

<td style="text-align:left;">

</td>

<td style="text-align:left;">

</td>

<td style="text-align:left;">

</td>

<td style="text-align:left;">

</td>

<td style="text-align:left;">

</td>

<td style="text-align:left;">

</td>

</tr>

<tr>

<td style="text-align:left; padding-left: 2em;" indentlevel="1">

Fatality

</td>

<td style="text-align:right;">

0

</td>

<td style="text-align:right;">

13

</td>

<td style="text-align:right;">

42

</td>

<td style="text-align:left;">

</td>

<td style="text-align:left;">

</td>

<td style="text-align:left;">

</td>

<td style="text-align:left;">

</td>

<td style="text-align:left;">

</td>

<td style="text-align:left;">

</td>

</tr>

<tr grouplength="3">

<td colspan="14" style="border-bottom: 1px solid;">

<strong>Model 3 Ensemble. Hierarchical: Traffic unit</strong>

</td>

</tr>

<tr>

<td style="text-align:left; padding-left: 2em;" indentlevel="1">

No Injury

</td>

<td style="text-align:right;">

35498

</td>

<td style="text-align:right;">

16204

</td>

<td style="text-align:right;">

60

</td>

<td style="text-align:left;vertical-align: middle !important;" rowspan="3">

</td>

<td style="text-align:left;">

</td>

<td style="text-align:left;">

</td>

<td style="text-align:left;">

</td>

<td style="text-align:left;">

</td>

<td style="text-align:left;">

</td>

<td style="text-align:left;">

</td>

<td style="text-align:left;vertical-align: middle !important;" rowspan="3">

</td>

<td style="text-align:left;vertical-align: middle !important;" rowspan="3">

</td>

<td style="text-align:left;vertical-align: middle !important;" rowspan="3">

</td>

</tr>

<tr>

<td style="text-align:left; padding-left: 2em;" indentlevel="1">

Injury

</td>

<td style="text-align:right;">

26913

</td>

<td style="text-align:right;">

67379

</td>

<td style="text-align:right;">

828

</td>

<td style="text-align:left;">

</td>

<td style="text-align:left;">

</td>

<td style="text-align:left;">

</td>

<td style="text-align:left;">

</td>

<td style="text-align:left;">

</td>

<td style="text-align:left;">

</td>

</tr>

<tr>

<td style="text-align:left; padding-left: 2em;" indentlevel="1">

Fatality

</td>

<td style="text-align:right;">

0

</td>

<td style="text-align:right;">

13

</td>

<td style="text-align:right;">

45

</td>

<td style="text-align:left;">

</td>

<td style="text-align:left;">

</td>

<td style="text-align:left;">

</td>

<td style="text-align:left;">

</td>

<td style="text-align:left;">

</td>

<td style="text-align:left;">

</td>

</tr>

<tr grouplength="3">

<td colspan="14" style="border-bottom: 1px solid;">

<strong>Model 4 Ensemble. Hierarchical: Opponent</strong>

</td>

</tr>

<tr>

<td style="text-align:left; padding-left: 2em;" indentlevel="1">

No Injury

</td>

<td style="text-align:right;">

35553

</td>

<td style="text-align:right;">

16297

</td>

<td style="text-align:right;">

59

</td>

<td style="text-align:left;vertical-align: middle !important;" rowspan="3">

</td>

<td style="text-align:left;">

</td>

<td style="text-align:left;">

</td>

<td style="text-align:left;">

</td>

<td style="text-align:left;">

</td>

<td style="text-align:left;">

</td>

<td style="text-align:left;">

</td>

<td style="text-align:left;vertical-align: middle !important;" rowspan="3">

</td>

<td style="text-align:left;vertical-align: middle !important;" rowspan="3">

</td>

<td style="text-align:left;vertical-align: middle !important;" rowspan="3">

</td>

</tr>

<tr>

<td style="text-align:left; padding-left: 2em;" indentlevel="1">

Injury

</td>

<td style="text-align:right;">

26852

</td>

<td style="text-align:right;">

67271

</td>

<td style="text-align:right;">

827

</td>

<td style="text-align:left;">

</td>

<td style="text-align:left;">

</td>

<td style="text-align:left;">

</td>

<td style="text-align:left;">

</td>

<td style="text-align:left;">

</td>

<td style="text-align:left;">

</td>

</tr>

<tr>

<td style="text-align:left; padding-left: 2em;" indentlevel="1">

Fatality

</td>

<td style="text-align:right;">

0

</td>

<td style="text-align:right;">

10

</td>

<td style="text-align:right;">

46

</td>

<td style="text-align:left;">

</td>

<td style="text-align:left;">

</td>

<td style="text-align:left;">

</td>

<td style="text-align:left;">

</td>

<td style="text-align:left;">

</td>

<td style="text-align:left;">

</td>

</tr>

</tbody>

<tfoot>

<tr>

<td style="padding: 0; border: 0;" colspan="100%">

<span style="font-style: italic;">Note: </span>

</td>

</tr>

<tr>

<td style="padding: 0; border: 0;" colspan="100%">

<sup></sup> Bold numbers: best scores; underlined numbers: second best
scores; red numbers: worst scores

</td>

</tr>

<tr>

<td style="padding: 0; border: 0;" colspan="100%">

<sup>1</sup> \(B&gt;1\): class is overpredicted; \(B&lt;1\): class is
underpredicted;

</td>

</tr>

<tr>

<td style="padding: 0; border: 0;" colspan="100%">

<sup>2</sup> \(CSI = 1\): perfect score; \(CSI = 0\): no skill;

</td>

</tr>

<tr>

<td style="padding: 0; border: 0;" colspan="100%">

<sup>3</sup> \(F = 0\): perfect score;

</td>

</tr>

<tr>

<td style="padding: 0; border: 0;" colspan="100%">

<sup>4</sup> \(POD = 1\): perfect score;

</td>

</tr>

<tr>

<td style="padding: 0; border: 0;" colspan="100%">

<sup>5</sup> \(FAR = 0\): perfect score;

</td>

</tr>

<tr>

<td style="padding: 0; border: 0;" colspan="100%">

<sup>6</sup> \(HSS = 1\): perfect score; \(HSS = 0\): no skill;
\(HSS &lt; 0\): random is better;

</td>

</tr>

<tr>

<td style="padding: 0; border: 0;" colspan="100%">

<sup>7</sup> \(PSS = 1\): perfect score; \(PSS = 0\): no skill;

</td>

</tr>

<tr>

<td style="padding: 0; border: 0;" colspan="100%">

<sup>8</sup> \(GS = 1\): perfect score; \(GS = 0\): no skill.

</td>

</tr>

</tfoot>

</table>

### Backcasting: verification statistics

Table  presents the results of the verification exercise for the case of
our out-of-sample predictions (i.e., backasting). Qualitatively, the
results are similar to those of the nowcasting experiments, but with a
somewhat weaker performance of the ensemble models. This, again,
supports the idea that these models might be overfitting the process, as
discussed in reference to the aggregate forecasts (see Section ). Models
2 (full sample) and 4 (full sample) are again identified as the best
overall performers, and particularly Model 2 (full sample) performs
somewhat more adroitly with respect to Fatality in backcasting than it
did in nowcasting.

<table>

<caption>

Assessment of out-of-sample outcomes (backcasting using 2016 data set)

</caption>

<thead>

<tr>

<th style="border-bottom:hidden; padding-bottom:0; padding-left:3px;padding-right:3px;text-align: center; " colspan="1">

<div style="border-bottom: 1px solid #ddd; padding-bottom: 5px; ">

Predicted

</div>

</th>

<th style="border-bottom:hidden; padding-bottom:0; padding-left:3px;padding-right:3px;text-align: center; " colspan="3">

<div style="border-bottom: 1px solid #ddd; padding-bottom: 5px; ">

Observed Outcome

</div>

</th>

<th style="border-bottom:hidden; padding-bottom:0; padding-left:3px;padding-right:3px;text-align: center; " colspan="10">

<div style="border-bottom: 1px solid #ddd; padding-bottom: 5px; ">

Verification Statistics

</div>

</th>

</tr>

<tr>

<th style="text-align:left;">

Outcome

</th>

<th style="text-align:right;">

No Injury

</th>

<th style="text-align:right;">

Injury

</th>

<th style="text-align:right;">

Fatality

</th>

<th style="text-align:left;">

</th>

<th style="text-align:left;">

</th>

<th style="text-align:left;">

Bias\(^1\)

</th>

<th style="text-align:left;">

</th>

<th style="text-align:left;">

</th>

<th style="text-align:left;">

</th>

<th style="text-align:left;">

</th>

<th style="text-align:left;">

</th>

<th style="text-align:left;">

</th>

<th style="text-align:left;">

</th>

</tr>

</thead>

<tbody>

<tr grouplength="3">

<td colspan="14" style="border-bottom: 1px solid;">

<strong>Model 1. Single-level/No opponent</strong>

</td>

</tr>

<tr>

<td style="text-align:left; padding-left: 2em;" indentlevel="1">

No Injury

</td>

<td style="text-align:right;">

61684

</td>

<td style="text-align:right;">

27447

</td>

<td style="text-align:right;">

184

</td>

<td style="text-align:left;vertical-align: middle !important;" rowspan="3">

</td>

<td style="text-align:left;">

</td>

<td style="text-align:left;">

</td>

<td style="text-align:left;">

</td>

<td style="text-align:left;">

</td>

<td style="text-align:left;">

</td>

<td style="text-align:left;">

</td>

<td style="text-align:left;vertical-align: middle !important;" rowspan="3">

</td>

<td style="text-align:left;vertical-align: middle !important;" rowspan="3">

</td>

<td style="text-align:left;vertical-align: middle !important;" rowspan="3">

</td>

</tr>

<tr>

<td style="text-align:left; padding-left: 2em;" indentlevel="1">

Injury

</td>

<td style="text-align:right;">

35171

</td>

<td style="text-align:right;">

74073

</td>

<td style="text-align:right;">

915

</td>

<td style="text-align:left;">

</td>

<td style="text-align:left;">

</td>

<td style="text-align:left;">

</td>

<td style="text-align:left;">

</td>

<td style="text-align:left;">

</td>

<td style="text-align:left;">

</td>

</tr>

<tr>

<td style="text-align:left; padding-left: 2em;" indentlevel="1">

Fatality

</td>

<td style="text-align:right;">

5

</td>

<td style="text-align:right;">

85

</td>

<td style="text-align:right;">

10

</td>

<td style="text-align:left;">

</td>

<td style="text-align:left;">

</td>

<td style="text-align:left;">

</td>

<td style="text-align:left;">

</td>

<td style="text-align:left;">

</td>

<td style="text-align:left;">

</td>

</tr>

<tr grouplength="3">

<td colspan="14" style="border-bottom: 1px solid;">

<strong>Model 2. Single-level/Opponent attributes</strong>

</td>

</tr>

<tr>

<td style="text-align:left; padding-left: 2em;" indentlevel="1">

No Injury

</td>

<td style="text-align:right;">

62735

</td>

<td style="text-align:right;">

21013

</td>

<td style="text-align:right;">

106

</td>

<td style="text-align:left;vertical-align: middle !important;" rowspan="3">

</td>

<td style="text-align:left;">

</td>

<td style="text-align:left;">

</td>

<td style="text-align:left;">

</td>

<td style="text-align:left;">

</td>

<td style="text-align:left;">

</td>

<td style="text-align:left;">

</td>

<td style="text-align:left;vertical-align: middle !important;" rowspan="3">

</td>

<td style="text-align:left;vertical-align: middle !important;" rowspan="3">

</td>

<td style="text-align:left;vertical-align: middle !important;" rowspan="3">

</td>

</tr>

<tr>

<td style="text-align:left; padding-left: 2em;" indentlevel="1">

Injury

</td>

<td style="text-align:right;">

34125

</td>

<td style="text-align:right;">

80569

</td>

<td style="text-align:right;">

996

</td>

<td style="text-align:left;">

</td>

<td style="text-align:left;">

</td>

<td style="text-align:left;">

</td>

<td style="text-align:left;">

</td>

<td style="text-align:left;">

</td>

<td style="text-align:left;">

</td>

</tr>

<tr>

<td style="text-align:left; padding-left: 2em;" indentlevel="1">

Fatality

</td>

<td style="text-align:right;">

0

</td>

<td style="text-align:right;">

23

</td>

<td style="text-align:right;">

7

</td>

<td style="text-align:left;">

</td>

<td style="text-align:left;">

</td>

<td style="text-align:left;">

</td>

<td style="text-align:left;">

</td>

<td style="text-align:left;">

</td>

<td style="text-align:left;">

</td>

</tr>

<tr grouplength="3">

<td colspan="14" style="border-bottom: 1px solid;">

<strong>Model 3. Hierarchical: Traffic unit</strong>

</td>

</tr>

<tr>

<td style="text-align:left; padding-left: 2em;" indentlevel="1">

No Injury

</td>

<td style="text-align:right;">

62249

</td>

<td style="text-align:right;">

21133

</td>

<td style="text-align:right;">

107

</td>

<td style="text-align:left;vertical-align: middle !important;" rowspan="3">

</td>

<td style="text-align:left;">

</td>

<td style="text-align:left;">

</td>

<td style="text-align:left;">

</td>

<td style="text-align:left;">

</td>

<td style="text-align:left;">

</td>

<td style="text-align:left;">

</td>

<td style="text-align:left;vertical-align: middle !important;" rowspan="3">

</td>

<td style="text-align:left;vertical-align: middle !important;" rowspan="3">

</td>

<td style="text-align:left;vertical-align: middle !important;" rowspan="3">

</td>

</tr>

<tr>

<td style="text-align:left; padding-left: 2em;" indentlevel="1">

Injury

</td>

<td style="text-align:right;">

34609

</td>

<td style="text-align:right;">

80433

</td>

<td style="text-align:right;">

996

</td>

<td style="text-align:left;">

</td>

<td style="text-align:left;">

</td>

<td style="text-align:left;">

</td>

<td style="text-align:left;">

</td>

<td style="text-align:left;">

</td>

<td style="text-align:left;">

</td>

</tr>

<tr>

<td style="text-align:left; padding-left: 2em;" indentlevel="1">

Fatality

</td>

<td style="text-align:right;">

2

</td>

<td style="text-align:right;">

39

</td>

<td style="text-align:right;">

6

</td>

<td style="text-align:left;">

</td>

<td style="text-align:left;">

</td>

<td style="text-align:left;">

</td>

<td style="text-align:left;">

</td>

<td style="text-align:left;">

</td>

<td style="text-align:left;">

</td>

</tr>

<tr grouplength="3">

<td colspan="14" style="border-bottom: 1px solid;">

<strong>Model 4. Hierarchical: Opponent</strong>

</td>

</tr>

<tr>

<td style="text-align:left; padding-left: 2em;" indentlevel="1">

No Injury

</td>

<td style="text-align:right;">

62788

</td>

<td style="text-align:right;">

21247

</td>

<td style="text-align:right;">

102

</td>

<td style="text-align:left;vertical-align: middle !important;" rowspan="3">

</td>

<td style="text-align:left;">

</td>

<td style="text-align:left;">

</td>

<td style="text-align:left;">

</td>

<td style="text-align:left;">

</td>

<td style="text-align:left;">

</td>

<td style="text-align:left;">

</td>

<td style="text-align:left;vertical-align: middle !important;" rowspan="3">

</td>

<td style="text-align:left;vertical-align: middle !important;" rowspan="3">

</td>

<td style="text-align:left;vertical-align: middle !important;" rowspan="3">

</td>

</tr>

<tr>

<td style="text-align:left; padding-left: 2em;" indentlevel="1">

Injury

</td>

<td style="text-align:right;">

34071

</td>

<td style="text-align:right;">

80331

</td>

<td style="text-align:right;">

1000

</td>

<td style="text-align:left;">

</td>

<td style="text-align:left;">

</td>

<td style="text-align:left;">

</td>

<td style="text-align:left;">

</td>

<td style="text-align:left;">

</td>

<td style="text-align:left;">

</td>

</tr>

<tr>

<td style="text-align:left; padding-left: 2em;" indentlevel="1">

Fatality

</td>

<td style="text-align:right;">

1

</td>

<td style="text-align:right;">

27

</td>

<td style="text-align:right;">

7

</td>

<td style="text-align:left;">

</td>

<td style="text-align:left;">

</td>

<td style="text-align:left;">

</td>

<td style="text-align:left;">

</td>

<td style="text-align:left;">

</td>

<td style="text-align:left;">

</td>

</tr>

<tr grouplength="3">

<td colspan="14" style="border-bottom: 1px solid;">

<strong>Model 1 Ensemble. Single-level/No opponent</strong>

</td>

</tr>

<tr>

<td style="text-align:left; padding-left: 2em;" indentlevel="1">

No Injury

</td>

<td style="text-align:right;">

42896

</td>

<td style="text-align:right;">

20230

</td>

<td style="text-align:right;">

95

</td>

<td style="text-align:left;vertical-align: middle !important;" rowspan="3">

</td>

<td style="text-align:left;">

</td>

<td style="text-align:left;">

</td>

<td style="text-align:left;">

</td>

<td style="text-align:left;">

</td>

<td style="text-align:left;">

</td>

<td style="text-align:left;">

</td>

<td style="text-align:left;vertical-align: middle !important;" rowspan="3">

</td>

<td style="text-align:left;vertical-align: middle !important;" rowspan="3">

</td>

<td style="text-align:left;vertical-align: middle !important;" rowspan="3">

</td>

</tr>

<tr>

<td style="text-align:left; padding-left: 2em;" indentlevel="1">

Injury

</td>

<td style="text-align:right;">

34546

</td>

<td style="text-align:right;">

79692

</td>

<td style="text-align:right;">

962

</td>

<td style="text-align:left;">

</td>

<td style="text-align:left;">

</td>

<td style="text-align:left;">

</td>

<td style="text-align:left;">

</td>

<td style="text-align:left;">

</td>

<td style="text-align:left;">

</td>

</tr>

<tr>

<td style="text-align:left; padding-left: 2em;" indentlevel="1">

Fatality

</td>

<td style="text-align:right;">

15

</td>

<td style="text-align:right;">

91

</td>

<td style="text-align:right;">

15

</td>

<td style="text-align:left;">

</td>

<td style="text-align:left;">

</td>

<td style="text-align:left;">

</td>

<td style="text-align:left;">

</td>

<td style="text-align:left;">

</td>

<td style="text-align:left;">

</td>

</tr>

<tr grouplength="3">

<td colspan="14" style="border-bottom: 1px solid;">

<strong>Model 2 Ensemble. Single-level/Opponent attributes</strong>

</td>

</tr>

<tr>

<td style="text-align:left; padding-left: 2em;" indentlevel="1">

No Injury

</td>

<td style="text-align:right;">

43486

</td>

<td style="text-align:right;">

19937

</td>

<td style="text-align:right;">

95

</td>

<td style="text-align:left;vertical-align: middle !important;" rowspan="3">

</td>

<td style="text-align:left;">

</td>

<td style="text-align:left;">

</td>

<td style="text-align:left;">

</td>

<td style="text-align:left;">

</td>

<td style="text-align:left;">

</td>

<td style="text-align:left;">

</td>

<td style="text-align:left;vertical-align: middle !important;" rowspan="3">

</td>

<td style="text-align:left;vertical-align: middle !important;" rowspan="3">

</td>

<td style="text-align:left;vertical-align: middle !important;" rowspan="3">

</td>

</tr>

<tr>

<td style="text-align:left; padding-left: 2em;" indentlevel="1">

Injury

</td>

<td style="text-align:right;">

33953

</td>

<td style="text-align:right;">

80009

</td>

<td style="text-align:right;">

961

</td>

<td style="text-align:left;">

</td>

<td style="text-align:left;">

</td>

<td style="text-align:left;">

</td>

<td style="text-align:left;">

</td>

<td style="text-align:left;">

</td>

<td style="text-align:left;">

</td>

</tr>

<tr>

<td style="text-align:left; padding-left: 2em;" indentlevel="1">

Fatality

</td>

<td style="text-align:right;">

20

</td>

<td style="text-align:right;">

103

</td>

<td style="text-align:right;">

15

</td>

<td style="text-align:left;">

</td>

<td style="text-align:left;">

</td>

<td style="text-align:left;">

</td>

<td style="text-align:left;">

</td>

<td style="text-align:left;">

</td>

<td style="text-align:left;">

</td>

</tr>

<tr grouplength="3">

<td colspan="14" style="border-bottom: 1px solid;">

<strong>Model 3 Ensemble. Hierarchical: Traffic unit</strong>

</td>

</tr>

<tr>

<td style="text-align:left; padding-left: 2em;" indentlevel="1">

No Injury

</td>

<td style="text-align:right;">

43526

</td>

<td style="text-align:right;">

20032

</td>

<td style="text-align:right;">

92

</td>

<td style="text-align:left;vertical-align: middle !important;" rowspan="3">

</td>

<td style="text-align:left;">

</td>

<td style="text-align:left;">

</td>

<td style="text-align:left;">

</td>

<td style="text-align:left;">

</td>

<td style="text-align:left;">

</td>

<td style="text-align:left;">

</td>

<td style="text-align:left;vertical-align: middle !important;" rowspan="3">

</td>

<td style="text-align:left;vertical-align: middle !important;" rowspan="3">

</td>

<td style="text-align:left;vertical-align: middle !important;" rowspan="3">

</td>

</tr>

<tr>

<td style="text-align:left; padding-left: 2em;" indentlevel="1">

Injury

</td>

<td style="text-align:right;">

33915

</td>

<td style="text-align:right;">

79916

</td>

<td style="text-align:right;">

964

</td>

<td style="text-align:left;">

</td>

<td style="text-align:left;">

</td>

<td style="text-align:left;">

</td>

<td style="text-align:left;">

</td>

<td style="text-align:left;">

</td>

<td style="text-align:left;">

</td>

</tr>

<tr>

<td style="text-align:left; padding-left: 2em;" indentlevel="1">

Fatality

</td>

<td style="text-align:right;">

18

</td>

<td style="text-align:right;">

102

</td>

<td style="text-align:right;">

16

</td>

<td style="text-align:left;">

</td>

<td style="text-align:left;">

</td>

<td style="text-align:left;">

</td>

<td style="text-align:left;">

</td>

<td style="text-align:left;">

</td>

<td style="text-align:left;">

</td>

</tr>

<tr grouplength="3">

<td colspan="14" style="border-bottom: 1px solid;">

<strong>Model 4 Ensemble. Hierarchical: Opponent</strong>

</td>

</tr>

<tr>

<td style="text-align:left; padding-left: 2em;" indentlevel="1">

No Injury

</td>

<td style="text-align:right;">

43560

</td>

<td style="text-align:right;">

20160

</td>

<td style="text-align:right;">

94

</td>

<td style="text-align:left;vertical-align: middle !important;" rowspan="3">

</td>

<td style="text-align:left;">

</td>

<td style="text-align:left;">

</td>

<td style="text-align:left;">

</td>

<td style="text-align:left;">

</td>

<td style="text-align:left;">

</td>

<td style="text-align:left;">

</td>

<td style="text-align:left;vertical-align: middle !important;" rowspan="3">

</td>

<td style="text-align:left;vertical-align: middle !important;" rowspan="3">

</td>

<td style="text-align:left;vertical-align: middle !important;" rowspan="3">

</td>

</tr>

<tr>

<td style="text-align:left; padding-left: 2em;" indentlevel="1">

Injury

</td>

<td style="text-align:right;">

33876

</td>

<td style="text-align:right;">

79762

</td>

<td style="text-align:right;">

959

</td>

<td style="text-align:left;">

</td>

<td style="text-align:left;">

</td>

<td style="text-align:left;">

</td>

<td style="text-align:left;">

</td>

<td style="text-align:left;">

</td>

<td style="text-align:left;">

</td>

</tr>

<tr>

<td style="text-align:left; padding-left: 2em;" indentlevel="1">

Fatality

</td>

<td style="text-align:right;">

25

</td>

<td style="text-align:right;">

107

</td>

<td style="text-align:right;">

17

</td>

<td style="text-align:left;">

</td>

<td style="text-align:left;">

</td>

<td style="text-align:left;">

</td>

<td style="text-align:left;">

</td>

<td style="text-align:left;">

</td>

<td style="text-align:left;">

</td>

</tr>

</tbody>

<tfoot>

<tr>

<td style="padding: 0; border: 0;" colspan="100%">

<span style="font-style: italic;">Note: </span>

</td>

</tr>

<tr>

<td style="padding: 0; border: 0;" colspan="100%">

<sup></sup> Bold numbers: best scores; underlined numbers: second best
scores; red numbers: worst scores

</td>

</tr>

<tr>

<td style="padding: 0; border: 0;" colspan="100%">

<sup>1</sup> \(B&gt;1\): class is overpredicted; \(B&lt;1\): class is
underpredicted;

</td>

</tr>

<tr>

<td style="padding: 0; border: 0;" colspan="100%">

<sup>2</sup> \(CSI = 1\): perfect score; \(CSI = 0\): no skill;

</td>

</tr>

<tr>

<td style="padding: 0; border: 0;" colspan="100%">

<sup>3</sup> \(F = 0\): perfect score;

</td>

</tr>

<tr>

<td style="padding: 0; border: 0;" colspan="100%">

<sup>4</sup> \(POD = 1\): perfect score;

</td>

</tr>

<tr>

<td style="padding: 0; border: 0;" colspan="100%">

<sup>5</sup> \(FAR = 0\): perfect score;

</td>

</tr>

<tr>

<td style="padding: 0; border: 0;" colspan="100%">

<sup>6</sup> \(HSS = 1\): perfect score; \(HSS = 0\): no skill;
\(HSS &lt; 0\): random is better;

</td>

</tr>

<tr>

<td style="padding: 0; border: 0;" colspan="100%">

<sup>7</sup> \(PSS = 1\): perfect score; \(PSS = 0\): no skill;

</td>

</tr>

<tr>

<td style="padding: 0; border: 0;" colspan="100%">

<sup>8</sup> \(GS = 1\): perfect score; \(GS = 0\): no skill.

</td>

</tr>

</tfoot>

</table>

<!---
Best model {#sec:insights}
============

Create a table with the results of Model 2.


--->

# Further considerations

As discussed in Section , there is a rich selection of modelling
approaches that are applicable to crash severity analysis. Based on the
literature, we limited our empirical assessment of modelling strategies
to only one model, namely the ordinal logit. On the other hand, since
the modelling strategies discussed here all relate to the specification
of the latent function and data subsetting, it is a relatively simple
matter to extend them to other modelling approaches. For example, take
Expression  and add a random component
![\_{k}](https://render.githubusercontent.com/render/math?math=%5Cmu_%7Bk%7D)
as follows:

<!--
\begin{equation}
\label{eq:latent-function-with-opponent-variables-and-random-component}
y_{itk}^*=\sum_{l=1}^L\alpha_lp_{itkl} + \sum_{m=1}^M\beta_mu_{tkm} + \sum_{q=1}^Q\kappa_qc_{kq} + \sum_{r=1}^R\delta_ro_{jvkr} + \mu_{k} + \epsilon_{itk}
\end{equation}
-->

![](https://render.githubusercontent.com/render/math?math=%5Cbegin%7Bequation%7D%20%5Clabel%7Beq%3Alatent-function-with-opponent-variables-and-random-component%7D%20y_%7Bitk%7D%5E*%3D%5Csum_%7Bl%3D1%7D%5EL%5Calpha_lp_%7Bitkl%7D%20%2B%20%5Csum_%7Bm%3D1%7D%5EM%5Cbeta_mu_%7Btkm%7D%20%2B%20%5Csum_%7Bq%3D1%7D%5EQ%5Ckappa_qc_%7Bkq%7D%20%2B%20%5Csum_%7Br%3D1%7D%5ER%5Cdelta_ro_%7Bjvkr%7D%20%2B%20%5Cmu_%7Bk%7D%20%2B%20%5Cepsilon_%7Bitk%7D%20%5Cend%7Bequation%7D)

The addition of the random component in this fashion would help to
capture, when appropriate, unobserved heterogeneity at the level of the
crash (this is similar to the random intercepts approach in multi-level
modelling; also see Mannering, Shankar, and Bhat 2016). As a second
example, take Expressions  to  and add a random component to a
hierarchical coefficient, to obtain:

<!--
\begin{equation}
\label{eq:hierarchical-traffic-unit-coefficients-with-random-component}
\begin{array}{rcl}\
\beta_{m}u_{tkm} &=& \big( \beta_{m1} + \beta_{m2}p_{itk2} + \cdots + \beta_{mL}p_{itkL} + \mu_{mk}\big)u_{tkm}\\ 
&=& \beta_{m1}u_{tkm} + \beta_{m2}p_{itk2}u_{tkm} + \cdots + \beta_{mL}p_{itkL}u_{tkm} + \mu_{mk}u_{tkm}
\end{array}
\end{equation}
-->

![](https://render.githubusercontent.com/render/math?math=%5Cbegin%7Bequation%7D%20%5Clabel%7Beq%3Ahierarchical-traffic-unit-coefficients-with-random-component%7D%20%5Cbegin%7Barray%7D%7Brcl%7D%5C%20%5Cbeta_%7Bm%7Du_%7Btkm%7D%20%26%3D%26%20%5Cbig\(%20%5Cbeta_%7Bm1%7D%20%2B%20%5Cbeta_%7Bm2%7Dp_%7Bitk2%7D%20%2B%20%5Ccdots%20%2B%20%5Cbeta_%7BmL%7Dp_%7BitkL%7D%20%2B%20%5Cmu_%7Bmk%7D%5Cbig\)u_%7Btkm%7D%5C%5C%20%20%26%3D%26%20%5Cbeta_%7Bm1%7Du_%7Btkm%7D%20%2B%20%5Cbeta_%7Bm2%7Dp_%7Bitk2%7Du_%7Btkm%7D%20%2B%20%5Ccdots%20%2B%20%5Cbeta_%7BmL%7Dp_%7BitkL%7Du_%7Btkm%7D%20%2B%20%5Cmu_%7Bmk%7Du_%7Btkm%7D%20%5Cend%7Barray%7D%20%5Cend%7Bequation%7D)

This is similar to the random slopes strategy in multi-level modelling.

We do not report results regarding other modelling strategies. On the
one hand, more sophisticated modelling frameworks are generally capable
of improving the performance of a model. On the other hand, there are
well-known challenges in the estimation of more sophisticated models
(see Lenguerrand, Martin, and Laumon 2006, 47, for a discussion of
convergence issues in models with mixed effects; Mannering, Shankar, and
Bhat 2016, 13, for some considerations regarding the complexity and cost
of estimating more complex models; and Bogue, Paleti, and Balan 2017,
27, on computational demands of models with random components). The
additional cost and complexity of more sophisticated modelling
approaches would, in our view, have greatly complicated our empirical
assessment, particularly considering the large size of the sample
involved in this research (a data set with over 164,000 records in the
case of the full sample models). That said, we experimented with a model
with random components using monthly subsets of data to find that,
indeed, estimation takes considerably longer, is more demanding in terms
of fixing potential estimation quirks, and in the end resulted in
variance components that could not be reliably estimated as different
from zero (results can be consulted in the source R Notebook). For this
reason, we choose to leave the application of more sophisticated models
as a matter for future research.

<!--
model with random coefficients

This is a test of the `ordinal` package. Hierarchical or mixed models do not work well with large samples. I can subsample based on month? What is the distribution of crashes by month?

```r
ggplot(data = crash_data.df, aes(x = C_MNTH)) + geom_bar()
```

Estimate the model as follows, filtering by month:

```r
mod_2_rand <- clmm2(location = P_ISEV ~
                                  # INDIVIDUAL-LEVEL VARIABLES
                                  P1_AGE + 
                                  P1_AGE2 + 
                                  P1_SEX + 
                                  P1_SAFE +
                                  # TRAFFIC UNIT-LEVEL VARIABLES
                                  PASSENGER1 + 
                                  PEDESTRIAN1 +
                                  BICYCLIST1 +
                                  MOTORCYCLIST1 +
                                  LT +
                                  HV +
                                  # OPPONENT ATTRIBUTES
                                  P2_AGE + 
                                  P2_AGE2 +
                                  P2_SEX +
                                  xLD +
                                  xLT +
                                  xHV +
                                  # CASE-LEVEL VARIABLES
                                  C_CONF + C_RCFG + C_WTHR + C_RSUR + C_RALN + C_TRAF,
                    random = C_CASE,
                                data = filter(crash_data.df, C_MNTH == "January"),
                                Hess = TRUE,
                                link = "logistic")
```

Take a peek at the model:

```r
summary(mod_2_rand)
```

Another model: create a dataframe with the variables needed for estimation of the initial model. Notice that I wish to use age by role of participant 1:

```r
crash_data.df <- ncdb_2017_paired_records %>%
  transmute(P_ISEV,
            # AGE
            DRIVER1_AGE = P1_AGE * (P1_USER == "Driver"),
            PASSENGER1_AGE = P1_AGE * (P1_USER == "Passenger"),
            PEDESTRIAN1_AGE = P1_AGE * (P1_USER == "Pedestrian"),
            BICYCLIST1_AGE = P1_AGE * (P1_USER == "Bicyclist"),
            MOTORCYCLIST1_AGE = P1_AGE * (P1_USER == "Motorcyclist"),
            DRIVER1_AGE2 = P1_AGE^2 * (P1_USER == "Driver"),
            PASSENGER1_AGE2 = P1_AGE^2 * (P1_USER == "Passenger"),
            PEDESTRIAN1_AGE2 = P1_AGE^2 * (P1_USER == "Pedestrian"),
            BICYCLIST1_AGE2 = P1_AGE^2 * (P1_USER == "Bicyclist"),
            MOTORCYCLIST1_AGE2 = P1_AGE^2 * (P1_USER == "Motorcyclist"),
            # DRIVER: VEHICLE TYPE
            DRIVER1_VEHICLE_LT = P1_TYPE == "Driver: Light Truck",
            DRIVER1_VEHICLE_HV = P1_TYPE == "Driver: Heavy Vehicle",
            # PASSENGER: VEHICLE TYPE
            PASSENGER1_VEHICLE_LT = P1_TYPE == "Passenger: Light Truck",
            PASSENGER1_VEHICLE_HV = P1_TYPE == "Passenger: Heavy Vehicle",
            # CRASH-LEVEL VARIABLES
            C_CONF,
            C_RCFG,
            C_WTHR,
            C_RSUR,
            C_RALN,
            C_TRAF,
            C_MNTH,
            C_CASE)
```

Estimate initial model after filtering by month:

```r
mod0 <- clmm2(P_ISEV ~ 
                DRIVER1_AGE + DRIVER1_AGE2 +
                PEDESTRIAN1_AGE + PEDESTRIAN1_AGE2 +
                BICYCLIST1_AGE + BICYCLIST1_AGE2 +
                MOTORCYCLIST1_AGE + MOTORCYCLIST1_AGE2 +
                C_CONF + C_RCFG + C_WTHR + C_RSUR + C_RALN + C_TRAF, 
              random = C_CASE, 
              data = filter(crash_data.df, C_MNTH == "June"), 
              Hess = TRUE,
              method = "nlminb")
```

Summarize the results:

```r
summary(mod0)
```

Notice that the variance of the random effect is effectively zero (I tested this for Jan-June), which explains the NaN in the estimates. When the variance cannot be reliable estimated as different from zero, dropping the mixed effect makes sense [see @Lenguerrand2006modelling, p. 47, for a discussion of convergence issues in models with mixed effects].
-->

# Concluding remarks

The study of crash severity is an important component of accident
research, as seen from a large and vibrant literature and numerous
applications. Part of this literature covers different modelling
strategies that can be used to model complex hierarchical, multievent
outcomes such as the severity of injuries following a collision. In this
paper, our objective has been to assess the performance of different
strategies to model opponent effects in two-vehicle crashes. In broad
terms, three strategies were considered: 1) incorporating opponent-level
variables in the model; 2) single- versus multi-level model
specifications; and 3) sample subsetting and estimation of separate
models for different types of individual-opponent interactions. The
empirical evaluation was based on data from Canada’s National Crash
Database and the application of ordered probit models. A suite of models
that implemented the various strategies considered was estimated using
data from 2017. We then assessed the performance of the models using one
information criterion (AIC). Furthermore, the predictive performance of
the models was assessed in terms of both nowcasting (in-sample
predictions) and backcasting (out-of-sample predictions), the latter
using data from 2016.

The results of the empirical assessment strongly suggest that
incorporating opponent effects can greatly improve the goodness-of-fit
and predictive performance of a model. Two modelling strategies appear
to outperform the rest: a relatively simple single-level modelling
approach that incorporates opponent effects, and a hierarchical
modelling approach with nested opponent effects. There was some evidence
that subsetting the sample can improve the results in some cases (e.g.,
when modelling the severity of crashes involving active travelers or
motorcyclists), but possibly at the risk of overfitting the process. It
is well known that overfitting can increase the accuracy of in-sample
predictions at the expense of bias in out-of-sample predictions. Alas,
since the true data generating process is unknowable in this empirical
research, it is not possible to assess the extent of estimator bias. It
is also worthwhile noting that in this paper we did not compare
individual models in our ensemble approach, but we suggest that this is
an avenue for future research.

The results of this research should be informative to analysts
interested in crashes involving two parties, since it provides some
useful guidelines regarding the specification of opponent effects. Not
only do opponent effects improve the goodness of fit and performance of
models, they also add rich insights into their effects. The focus on
this paper was on performance, and for space reasons it is not possible
to include an examination of the best model without failing to do it
justice. We plan to report the results of the best-fitting model in a
future paper.

The analysis also opens up a few avenues for future research. First, for
reasons discussed in Section , we did not consider more sophisticated
modelling approaches, such as models with random components, partial
proportional odds, ranked ordered models, or multinomial models, to
mention just a few possibilities. Secondly, we only considered the
performance of the models when making predictions for the full sample.
That is, the submodels in the ensembles were not compared in detail,
just their aggregate results when predicting the full sample. However,
the goodness-of-fit was not uniformly better for any one modelling
strategy when the data were subset, and it is possible that individual
models perform better for a certain subset than competitors that are
part of a better ensemble, overall. For this reason, we suggest that
additional work with ensemble approaches is warranted. Finally, it is
clear that the models do not generally do well when predicting the least
frequent class of outcome, namely Fatality. It would be worthwhile to
further investigate approaches for so-called imbalanced learning, a task
that has received attention in the machine learning community (e.g.,
Haixiang et al. 2017; He and Garcia 2009), and where Torrao et
al. (2014) have already made some headway in crash severity analysis.

Finally, as an aside, this paper is, to the best of our knowledge, the
first example of reproducible research in crash severity analysis. By
providing the data *and* code for the analysis, it is our hope that this
will allow other researchers to easily verify the results, and to extend
them. A common practice in the machine learning community is to use
canonical data set to demonstrate the performance of new techniques.
Sharing code and data has remained relatively rare in transportation
research, and we would like to suggest that the data sets used in this
research could constitute one such canonical data set for future
methodological developments.

# Acknowledgments

This research was supported by a Research Excellence grant from McMaster
University. The authors wish to express their gratitude to the Office of
the Provost, the Faculty of Science, and the Faculty of Engineering for
their generous support. The comments of four anonymous reviewers are
also duly acknowledged: their feedback helped to improve the quality and
clarity of presentation of this paper.

# Appendix

Variable definitions in Canada’s National Collision Database.

<table>

<caption>

Contents of National Collision Database: Collision-level variables

</caption>

<thead>

<tr>

<th style="text-align:left;">

Variable

</th>

<th style="text-align:left;">

Description

</th>

<th style="text-align:left;">

Notes

</th>

</tr>

</thead>

<tbody>

<tr>

<td style="text-align:left;">

C\_CASE

</td>

<td style="text-align:left;">

Unique collision identifier

</td>

<td style="text-align:left;width: 32em; ">

Unique identifier for collisions

</td>

</tr>

<tr>

<td style="text-align:left;">

C\_YEAR

</td>

<td style="text-align:left;">

Year

</td>

<td style="text-align:left;width: 32em; ">

Last two digits of year.

</td>

</tr>

<tr>

<td style="text-align:left;">

C\_MNTH

</td>

<td style="text-align:left;">

Month

</td>

<td style="text-align:left;width: 32em; ">

14 levels: January - December; unknown; not reported by jurisdiction.

</td>

</tr>

<tr>

<td style="text-align:left;">

C\_WDAY

</td>

<td style="text-align:left;">

Day of week

</td>

<td style="text-align:left;width: 32em; ">

9 levels: Monday - Sunday; unknown; not reported by jurisdiction.

</td>

</tr>

<tr>

<td style="text-align:left;">

C\_HOUR

</td>

<td style="text-align:left;">

Collision hour

</td>

<td style="text-align:left;width: 32em; ">

25 levels: hourly intervals; unknown; not reported by jurisdiction.

</td>

</tr>

<tr>

<td style="text-align:left;">

C\_SEV

</td>

<td style="text-align:left;">

Collision severity

</td>

<td style="text-align:left;width: 32em; ">

4 levels: collision producing at least one fatality; collision producing
non-fatal injury; unknown; not reported by jurisdiction.

</td>

</tr>

<tr>

<td style="text-align:left;">

C\_VEHS

</td>

<td style="text-align:left;">

Number of vehicles involved in collision

</td>

<td style="text-align:left;width: 32em; ">

Number of vehicles: 1-98 vehicles involved; 99 or more vehicles
involved; unknown; not reported by jurisdiction.

</td>

</tr>

<tr>

<td style="text-align:left;">

C\_CONF

</td>

<td style="text-align:left;">

Collision configuration

</td>

<td style="text-align:left;width: 32em; ">

21 levels: SINGLE VEHICLE: Hit a moving object (e.g. a person or an
animal); Hit a stationary object (e.g. a tree); Ran off left shoulder;
Ran off right shoulder; Rollover on roadway; Any other single vehicle
collision configuration; TWO-VEHICLES SAME DIRECTION OF TRAVEL: Rear-end
collision; Side swipe; One vehicle passing to the left of the other, or
left turn conflict; One vehicle passing to the right of the other, or
right turn conflict; Any other two vehicle - same direction of travel
configuration; TWO-VEHICLES DIFFERENT DIRECTION OF TRAVEL: Head-on
collision; Approaching side-swipe; Left turn across opposing traffic;
Right turn, including turning conflicts; Right angle collision; Any
other two-vehicle - different direction of travel configuration;
TWO-VEHICLES, HIT A PARKED VEHICLE: Hit a parked motor vehicle; Choice
is other than the preceding values; unknown;not reported by
jurisdiction.

</td>

</tr>

<tr>

<td style="text-align:left;">

C\_RCFG

</td>

<td style="text-align:left;">

Roadway configuration

</td>

<td style="text-align:left;width: 32em; ">

15 levels: Non-intersection; At an intersection of at least two public
roadways; Intersection with parking lot entrance/exit, private driveway
or laneway; Railroad level crossing; Bridge, overpass, viaduct; Tunnel
or underpass; Passing or climbing lane; Ramp; Traffic circle; Express
lane of a freeway system; Collector lane of a freeway system; Transfer
lane of a freeway system; Choice is other than the preceding values;
unknown;not reported by jurisdiction.

</td>

</tr>

<tr>

<td style="text-align:left;">

C\_WTHR

</td>

<td style="text-align:left;">

Weather condition

</td>

<td style="text-align:left;width: 32em; ">

10 levels: Clear and sunny; Overcast, cloudy but no precipitation;
Raining; Snowing, not including drifting snow; Freezing rain, sleet,
hail; Visibility limitation; Strong wind; Choice is other than the
preceding values; unknown;not reported by jurisdiction.

</td>

</tr>

<tr>

<td style="text-align:left;">

C\_RSUR

</td>

<td style="text-align:left;">

Road surface

</td>

<td style="text-align:left;width: 32em; ">

12 levels: Dry, normal; Wet; Snow (fresh, loose snow); Slush, wet snow;
Icy, packed snow; Debris on road (e.g., sand/gravel/dirt); Muddy; Oil;
Flooded; Choice is other than the preceding values; unknown;not reported
by jurisdiction.

</td>

</tr>

<tr>

<td style="text-align:left;">

C\_RALN

</td>

<td style="text-align:left;">

Road alignment

</td>

<td style="text-align:left;width: 32em; ">

9 levels: Straight and level; Straight with gradient; Curved and level;
Curved with gradient; Top of hill or gradient; Bottom of hill or
gradient; Choice is other than the preceding values; unknown;not
reported by jurisdiction.

</td>

</tr>

<tr>

<td style="text-align:left;">

C\_TRAF

</td>

<td style="text-align:left;">

Traffic control

</td>

<td style="text-align:left;width: 32em; ">

21 levels: Traffic signals fully operational; Traffic signals in
flashing mode; Stop sign; Yield sign; Warning sign; Pedestrian
crosswalk; Police officer; School guard, flagman; School crossing;
Reduced speed zone; No passing zone sign; Markings on the road; School
bus stopped with school bus signal lights flashing; School bus stopped
with school bus signal lights not flashing; Railway crossing with
signals, or signals and gates; Railway crossing with signs only; Control
device not specified; No control present; Choice is other than the
preceding values; unknown; not reported by jurisdiction.

</td>

</tr>

</tbody>

<tfoot>

<tr>

<td style="padding: 0; border: 0;" colspan="100%">

<span style="font-style: italic;">Note: </span>

</td>

</tr>

<tr>

<td style="padding: 0; border: 0;" colspan="100%">

<sup></sup> Source NCDB available from
<https://open.canada.ca/data/en/data>
set/1eb9eba7-71d1-4b30-9fb1-30cbdab7e63a

</td>

</tr>

<tr>

<td style="padding: 0; border: 0;" colspan="100%">

<sup></sup> Source data files for analysis also available from
<https://drive.google.com/open?id=12aJtVBaQ4Zj0xa7mtfqxh0E48hKCb_XV>

</td>

</tr>

</tfoot>

</table>

<table>

<caption>

Contents of National Collision Database: Traffic unit-level variables

</caption>

<thead>

<tr>

<th style="text-align:left;">

Variable

</th>

<th style="text-align:left;">

Description

</th>

<th style="text-align:left;">

Notes

</th>

</tr>

</thead>

<tbody>

<tr>

<td style="text-align:left;">

V\_ID

</td>

<td style="text-align:left;">

Vehicle sequence number

</td>

<td style="text-align:left;width: 32em; ">

Number of vehicles: 1-98; Pedestrian sequence number: 99; unknown.

</td>

</tr>

<tr>

<td style="text-align:left;">

V\_TYPE

</td>

<td style="text-align:left;">

Vehicle type

</td>

<td style="text-align:left;width: 32em; ">

21 levels: Light Duty Vehicle (Passenger car, Passenger van, Light
utility vehicles and light duty pick up trucks); Panel/cargo van (\<=
4536 KG GVWR Panel or window type of van designed primarily for carrying
goods); Other trucks and vans (\<= 4536 KG GVWR); Unit trucks (\> 4536
KG GVWR); Road tractor; School bus; Smaller school bus (\< 25
passengers); Urban and Intercity Bus; Motorcycle and moped; Off road
vehicles; Bicycle; Purpose-built motorhome; Farm equipment; Construction
equipment; Fire engine; Snowmobile; Street car; Data element is not
applicable (e.g. dummy vehicle record created for pedestrian); Choice is
other than the preceding values; unknown; not reported by jurisdiction.

</td>

</tr>

<tr>

<td style="text-align:left;">

V\_YEAR

</td>

<td style="text-align:left;">

Vehicle model year

</td>

<td style="text-align:left;width: 32em; ">

Model year; dummy for pedestrians; unknown; not reported by
jurisdiction.

</td>

</tr>

</tbody>

<tfoot>

<tr>

<td style="padding: 0; border: 0;" colspan="100%">

<span style="font-style: italic;">Note: </span>

</td>

</tr>

<tr>

<td style="padding: 0; border: 0;" colspan="100%">

<sup></sup> Source NCDB available from
<https://open.canada.ca/data/en/data>
set/1eb9eba7-71d1-4b30-9fb1-30cbdab7e63a

</td>

</tr>

<tr>

<td style="padding: 0; border: 0;" colspan="100%">

<sup></sup> Source data files for analysis also available from
<https://drive.google.com/open?id=12aJtVBaQ4Zj0xa7mtfqxh0E48hKCb_XV>

</td>

</tr>

</tfoot>

</table>

<table>

<caption>

Contents of National Collision Database: Personal-level variables

</caption>

<thead>

<tr>

<th style="text-align:left;">

Variable

</th>

<th style="text-align:left;">

Description

</th>

<th style="text-align:left;">

Notes

</th>

</tr>

</thead>

<tbody>

<tr>

<td style="text-align:left;">

P\_ID

</td>

<td style="text-align:left;">

Person sequence number

</td>

<td style="text-align:left;width: 32em; ">

Sequence number: 1-99; Not applicable (dummy for parked vehicles); not
reported by jurisdiction.

</td>

</tr>

<tr>

<td style="text-align:left;">

P\_SEX

</td>

<td style="text-align:left;">

Person sex

</td>

<td style="text-align:left;width: 32em; ">

5 levels: Male; Female; Not applicable (dummy for parked vehicles);
unknown (runaway vehicle); not reported by jurisdiction.

</td>

</tr>

<tr>

<td style="text-align:left;">

P\_AGE

</td>

<td style="text-align:left;">

Person age

</td>

<td style="text-align:left;width: 32em; ">

Age: less than 1 year; 1-98 years old; 99 years or older; Not applicable
(dummy for parked vehicles); unknown (runaway vehicle); not reported by
jurisdiction.

</td>

</tr>

<tr>

<td style="text-align:left;">

P\_PSN

</td>

<td style="text-align:left;">

Person position

</td>

<td style="text-align:left;width: 32em; ">

Person position: Driver; Passenger front row, center; Passenger front
row, right outboard (including motorcycle passenger in sidecar);
Passenger second row, left outboard, including motorcycle passenger;
Passenger second row, center; Passenger second row, right outboard;
Passenger third row, left outboard;…; Position unknown, but the person
was definitely an occupant; Sitting on someone’s lap; Outside passenger
compartment; Pedestrian; Not applicable (dummy for parked vehicles);
Choice is other than the preceding values; unknown (runaway vehicle);
not reported by jurisdiction.

</td>

</tr>

<tr>

<td style="text-align:left;">

P\_ISEV

</td>

<td style="text-align:left;">

Medical treatment required

</td>

<td style="text-align:left;width: 32em; ">

6 levels: No Injury; Injury; Fatality; Not applicable (dummy for parked
vehicles); Choice is other than the preceding values; unknown (runaway
vehicle); not reported by jurisdiction.

</td>

</tr>

<tr>

<td style="text-align:left;">

P\_SAFE

</td>

<td style="text-align:left;">

Safety device used

</td>

<td style="text-align:left;width: 32em; ">

11 levels: No safety device used; Safety device used; Helmet worn;
Reflective clothing worn; Both helmet and reflective clothing used;
Other safety device used; No safety device equipped (e.g. buses); Not
applicable (dummy for parked vehicles); Choice is other than the
preceding values; unknown (runaway vehicle); not reported by
jurisdiction.

</td>

</tr>

<tr>

<td style="text-align:left;">

P\_USER

</td>

<td style="text-align:left;">

Road user class

</td>

<td style="text-align:left;width: 32em; ">

6 levels: Motor Vehicle Driver; Motor Vehicle Passenger; Pedestrian;
Bicyclist; Motorcyclist; Not stated/Other/Unknown.

</td>

</tr>

</tbody>

<tfoot>

<tr>

<td style="padding: 0; border: 0;" colspan="100%">

<span style="font-style: italic;">Note: </span>

</td>

</tr>

<tr>

<td style="padding: 0; border: 0;" colspan="100%">

<sup></sup> Source NCDB available from
<https://open.canada.ca/data/en/data>
set/1eb9eba7-71d1-4b30-9fb1-30cbdab7e63a

</td>

</tr>

<tr>

<td style="padding: 0; border: 0;" colspan="100%">

<sup></sup> Source data files for analysis also available from
<https://drive.google.com/open?id=12aJtVBaQ4Zj0xa7mtfqxh0E48hKCb_XV>

</td>

</tr>

</tfoot>

</table>

# References

<div id="refs" class="references">

<div id="ref-Amoh2017effect">

Amoh-Gyimah, R., E. N. Aidoo, M. A. Akaateba, and S. K. Appiah. 2017.
“The Effect of Natural and Built Environmental Characteristics on
Pedestrian-Vehicle Crash Severity in Ghana.” Journal Article.
*International Journal of Injury Control and Safety Promotion* 24 (4):
459–68. <https://doi.org/10.1080/17457300.2016.1232274>.

</div>

<div id="ref-Aziz2013exploring">

Aziz, H. M. A., S. V. Ukkusuri, and S. Hasan. 2013. “Exploring the
Determinants of Pedestrian-Vehicle Crash Severity in New York City.”
Journal Article. *Accident Analysis and Prevention* 50: 1298–1309.
<https://doi.org/10.1016/j.aap.2012.09.034>.

</div>

<div id="ref-Begueria2006validation">

Beguería, Santiago. 2006. “Validation and Evaluation of Predictive
Models in Hazard Assessment and Risk Management.” Journal Article.
*Natural Hazards* 37 (3): 315–29.
<https://doi.org/10.1007/s11069-005-5182-6>.

</div>

<div id="ref-Bogue2017modified">

Bogue, S., R. Paleti, and L. Balan. 2017. “A Modified Rank Ordered Logit
Model to Analyze Injury Severity of Occupants in Multivehicle Crashes.”
Journal Article. *Analytic Methods in Accident Research* 14: 22–40.
<https://doi.org/10.1016/j.amar.2017.03.001>.

</div>

<div id="ref-Casetti1972generating">

Casetti, E. 1972. “Generating Models by the Expansion Method:
Applications to Geographic Research.” Journal Article. *Geographical
Analysis* 4 (1): 81–91.

</div>

<div id="ref-Chang2006analysis">

Chang, L. Y., and H. W. Wang. 2006. “Analysis of Traffic Injury
Severity: An Application of Non-Parametric Classification Tree
Techniques.” Journal Article. *Accident Analysis and Prevention* 38 (5):
1019–27. <https://doi.org/10.1016/j.aap.2006.04.009>.

</div>

<div id="ref-Chen2019investigation">

Chen, F., M. T. Song, and X. X. Ma. 2019. “Investigation on the Injury
Severity of Drivers in Rear-End Collisions Between Cars Using a Random
Parameters Bivariate Ordered Probit Model.” Journal Article.
*International Journal of Environmental Research and Public Health* 16
(14). <https://doi.org/10.3390/ijerph16142632>.

</div>

<div id="ref-Chiou2013modeling">

Chiou, Y. C., C. C. Hwang, C. C. Chang, and C. Fu. 2013. “Modeling
Two-Vehicle Crash Severity by a Bivariate Generalized Ordered Probit
Approach.” Journal Article. *Accident Analysis and Prevention* 51:
175–84. <https://doi.org/10.1016/j.aap.2012.11.008>.

</div>

<div id="ref-Dissanayake2002factors">

Dissanayake, S., and J. J. Lu. 2002. “Factors Influential in Making an
Injury Severity Difference to Older Drivers Involved in Fixed
Object-Passenger Car Crashes.” Journal Article. *Accident Analysis and
Prevention* 34 (5): 609–18.
<https://doi.org/10.1016/s0001-4575(01)00060-4>.

</div>

<div id="ref-Duddu2018modeling">

Duddu, V. R., P. Penmetsa, and S. S. Pulugurtha. 2018. “Modeling and
Comparing Injury Severity of at-Fault and Not at-Fault Drivers in
Crashes.” Journal Article. *Accident Analysis and Prevention* 120:
55–63. <https://doi.org/10.1016/j.aap.2018.07.036>.

</div>

<div id="ref-Effati2015geospatial">

Effati, M., J. C. Thill, and S. Shabani. 2015. “Geospatial and Machine
Learning Techniques for Wicked Social Science Problems: Analysis of
Crash Severity on a Regional Highway Corridor.” Journal Article.
*Journal of Geographical Systems* 17 (2): 107–35.
<https://doi.org/10.1007/s10109-015-0210-x>.

</div>

<div id="ref-Gong2017modeling">

Gong, L. F., and W. D. Fan. 2017. “Modeling Single-Vehicle Run-Off-Road
Crash Severity in Rural Areas: Accounting for Unobserved Heterogeneity
and Age Difference.” Journal Article. *Accident Analysis and Prevention*
101: 124–34. <https://doi.org/10.1016/j.aap.2017.02.014>.

</div>

<div id="ref-Haixiang2017learning">

Haixiang, Guo, Li Yijing, Jennifer Shang, Gu Mingyun, Huang Yuanyue, and
Gong Bing. 2017. “Learning from Class-Imbalanced Data: Review of Methods
and Applications.” Journal Article. *Expert Systems with Applications*
73: 220–39.
<https://doi.org/https://doi.org/10.1016/j.eswa.2016.12.035>.

</div>

<div id="ref-Haleem2013effect">

Haleem, K., and A. Gan. 2013. “Effect of Driver’s Age and Side of Impact
on Crash Severity Along Urban Freeways: A Mixed Logit Approach.” Journal
Article. *Journal of Safety Research* 46: 67–76.
<https://doi.org/10.1016/j.jsr.2013.04.002>.

</div>

<div id="ref-He2009learning">

He, Haibo, and Edwardo A Garcia. 2009. “Learning from Imbalanced Data.”
Journal Article. *IEEE Transactions on Knowledge and Data Engineering*
21 (9): 1263–84.

</div>

<div id="ref-Hedeker1994random">

Hedeker, D., and R. D. Gibbons. 1994. “A Random-Effects Ordinal
Regression-Model for Multilevel Analysis.” Journal Article. *Biometrics*
50 (4): 933–44. [ISI:A1994QP05000003
C:/Papers/Biometrics/Biometrics (1994) 50
(4) 933-944.pdf](ISI:A1994QP05000003%0AC:/Papers/Biometrics/Biometrics%20\(1994\)%2050%20\(4\)%20933-944.pdf).

</div>

<div id="ref-Iranitalab2017comparison">

Iranitalab, A., and A. Khattak. 2017. “Comparison of Four Statistical
and Machine Learning Methods for Crash Severity Prediction.” Journal
Article. *Accident Analysis and Prevention* 108: 27–36.
<https://doi.org/10.1016/j.aap.2017.08.008>.

</div>

<div id="ref-Islam2014comprehensive">

Islam, Samantha, Steven L. Jones, and Daniel Dye. 2014. “Comprehensive
Analysis of Single- and Multi-Vehicle Large Truck at-Fault Crashes on
Rural and Urban Roadways in Alabama.” Journal Article. *Accident
Analysis & Prevention* 67: 148–58.
<https://doi.org/https://doi.org/10.1016/j.aap.2014.02.014>.

</div>

<div id="ref-Khan2015exploring">

Khan, G., A. R. Bill, and D. A. Noyce. 2015. “Exploring the Feasibility
of Classification Trees Versus Ordinal Discrete Choice Models for
Analyzing Crash Severity.” Journal Article. *Transportation Research
Part C-Emerging Technologies* 50: 86–96.
<https://doi.org/10.1016/j.trc.2014.10.003>.

</div>

<div id="ref-Kim2013driver">

Kim, J. K., G. F. Ulfarsson, S. Kim, and V. N. Shankar. 2013.
“Driver-Injury Severity in Single-Vehicle Crashes in California: A
Mixed Logit Analysis of Heterogeneity Due to Age and Gender.” Journal
Article. *Accident Analysis and Prevention* 50: 1073–81.
<https://doi.org/10.1016/j.aap.2012.08.011>.

</div>

<div id="ref-Kim1995personal">

Kim, K., L. Nitz, J. Richardson, and L. Li. 1995. “PERSONAL and
Behavioral Predictors of Automobile Crash and Injury Severity.” Journal
Article. *Accident Analysis and Prevention* 27 (4): 469–81.
<https://doi.org/10.1016/0001-4575(95)00001-g>.

</div>

<div id="ref-Lee2014analysis">

Lee, C., and X. C. Li. 2014. “Analysis of Injury Severity of Drivers
Involved in Single- and Two-Vehicle Crashes on Highways in Ontario.”
Journal Article. *Accident Analysis and Prevention* 71: 286–95.
<https://doi.org/10.1016/j.aap.2014.06.008>.

</div>

<div id="ref-Lenguerrand2006modelling">

Lenguerrand, E., J. L. Martin, and B. Laumon. 2006. “Modelling the
Hierarchical Structure of Road Crash Data - Application to Severity
Analysis.” Journal Article. *Accident Analysis and Prevention* 38 (1):
43–53. <https://doi.org/10.1016/j.aap.2005.06.021>.

</div>

<div id="ref-Li2017interplay">

Li, L., M. S. Hasnine, K. M. N. Habib, B. Persaud, and A. Shalaby. 2017.
“Investigating the Interplay Between the Attributes of at-Fault and
Not-at-Fault Drivers and the Associated Impacts on Crash Injury
Occurrence and Severity Level.” Journal Article. *Journal of
Transportation Safety & Security* 9 (4): 439–56.
<https://doi.org/10.1080/19439962.2016.1237602>.

</div>

<div id="ref-Ma2008multivariate">

Ma, J. M., K. M. Kockelman, and P. Damien. 2008. “A Multivariate
Poisson-Lognormal Regression Model for Prediction of Crash Counts by
Severity, Using Bayesian Methods.” Journal Article. *Accident Analysis
and Prevention* 40 (3): 964–75.
<https://doi.org/10.1016/j.aap.2007.11.002>.

</div>

<div id="ref-Maddala1986limited">

Maddala, Gangadharrao S. 1986. *Limited-Dependent and Qualitative
Variables in Econometrics*. 3. Cambridge university press.

</div>

<div id="ref-Mannering2016unobserved">

Mannering, F., V. Shankar, and C. R. Bhat. 2016. “Unobserved
Heterogeneity and the Statistical Analysis of Highway Accident Data.”
Journal Article. *Analytic Methods in Accident Research* 11: 1–16.
<https://doi.org/10.1016/j.amar.2016.04.001>.

</div>

<div id="ref-Montella2013crash">

Montella, A., D. Andreassen, A. P. Tarko, S. Turner, F. Mauriello, L. L.
Imbriani, and M. A. Romero. 2013. “Crash Databases in Australasia, the
European Union, and the United States Review and Prospects for
Improvement.” Journal Article. *Transportation Research Record*, no.
2386: 128–36. <https://doi.org/10.3141/2386-15>.

</div>

<div id="ref-Mooradian2013analysis">

Mooradian, J., J. N. Ivan, N. Ravishanker, and S. Hu. 2013. “Analysis of
Driver and Passenger Crash Injury Severity Using Partial Proportional
Odds Models.” Journal Article. *Accident Analysis and Prevention* 58:
53–58. <https://doi.org/10.1016/j.aap.2013.04.022>.

</div>

<div id="ref-Osman2018injury">

Osman, M., S. Mishra, and R. Paleti. 2018. “Injury Severity Analysis of
Commercially-Licensed Drivers in Single-Vehicle Crashes: Accounting for
Unobserved Heterogeneity and Age Group Differences.” Journal Article.
*Accident Analysis and Prevention* 118: 289–300.
<https://doi.org/10.1016/j.aap.2018.05.004>.

</div>

<div id="ref-Penmetsa2017examining">

Penmetsa, P., S. S. Pulugurtha, and V. R. Duddu. 2017. “Examining Injury
Severity of Not-at-Fault Drivers in Two-Vehicle Crashes.” Journal
Article. *Transportation Research Record*, no. 2659: 164–73.
<https://doi.org/10.3141/2659-18>.

</div>

<div id="ref-Provost1998glossary">

Provost, Foster, and R Kohavi. 1998. “Glossary of Terms.” *Journal of
Machine Learning* 30 (2-3): 271–74.

</div>

<div id="ref-Rana2010copula">

Rana, T. A., S. Sikder, and A. R. Pinjari. 2010. “Copula-Based Method
for Addressing Endogeneity in Models of Severity of Traffic Crash
Injuries Application to Two-Vehicle Crashes.” Journal Article.
*Transportation Research Record*, no. 2147: 75–87.
<https://doi.org/10.3141/2147-10>.

</div>

<div id="ref-Rifaat2007accident">

Rifaat, S. M., and H. C. Chin. 2007. “Accident Severity Analysis Using
Ordered Probit Model.” Journal Article. *Journal of Advanced
Transportation* 41 (1): 91–114.
<https://doi.org/10.1002/atr.5670410107>.

</div>

<div id="ref-Roorda2010trip">

Roorda, M. J., A. Paez, C. Morency, R. Mercado, and S. Farber. 2010.
“Trip Generation of Vulnerable Populations in Three Canadian Cities: A
Spatial Ordered Probit Approach.” Journal Article. *Transportation* 37
(3): 525–48. <https://doi.org/10.1007/s11116-010-9263-3>.

</div>

<div id="ref-Sasidharan2014partial">

Sasidharan, L., and M. Menendez. 2014. “Partial Proportional Odds
Model-an Alternate Choice for Analyzing Pedestrian Crash Injury
Severities.” Journal Article. *Accident Analysis and Prevention* 72:
330–40. <https://doi.org/10.1016/j.aap.2014.07.025>.

</div>

<div id="ref-Savolainen2007probabilistic">

Savolainen, P., and F. Mannering. 2007. “Probabilistic Models of
Motorcyclists’ Injury Severities in Single- and Multi-Vehicle Crashes.”
Journal Article. *Accident Analysis and Prevention* 39 (5): 955–63.
<https://doi.org/10.1016/j.aap.2006.12.016>.

</div>

<div id="ref-Savolainen2011statistical">

Savolainen, P. T., F. Mannering, D. Lord, and M. A. Quddus. 2011. “The
Statistical Analysis of Highway Crash-Injury Severities: A Review and
Assessment of Methodological Alternatives.” Journal Article. *Accident
Analysis and Prevention* 43 (5): 1666–76.
<https://doi.org/10.1016/j.aap.2011.03.025>.

</div>

<div id="ref-Shamsunnahar2014examining">

Shamsunnahar, Y., N. Eluru, A. R. Pinjari, and R. Tay. 2014. “Examining
Driver Injury Severity in Two Vehicle Crashes - a Copula Based
Approach.” Journal Article. *Accident Analysis and Prevention* 66:
120–35. <https://doi.org/10.1016/j.aap.2014.01.018>.

</div>

<div id="ref-Tang2019crash">

Tang, J. J., J. Liang, C. Y. Han, Z. B. Li, and H. L. Huang. 2019.
“Crash Injury Severity Analysis Using a Two-Layer Stacking Framework.”
Journal Article. *Accident Analysis and Prevention* 122: 226–38.
<https://doi.org/10.1016/j.aap.2018.10.016>.

</div>

<div id="ref-Tay2011multinomial">

Tay, R., J. Choi, L. Kattan, and A. Khan. 2011. “A Multinomial Logit
Model of Pedestrian-Vehicle Crash Severity.” Journal Article.
*International Journal of Sustainable Transportation* 5 (4): 233–49.
<https://doi.org/10.1080/15568318.2010.497547>.

</div>

<div id="ref-Torrao2014modeling">

Torrao, G. A., M. C. Coelho, and N. M. Rouphail. 2014. “Modeling the
Impact of Subject and Opponent Vehicles on Crash Severity in Two-Vehicle
Collisions.” Journal Article. *Transportation Research Record*, no.
2432: 53–64. <https://doi.org/10.3141/2432-07>.

</div>

<div id="ref-Train2009discrete">

Train, K. 2009. *Discrete Choice Methods with Simulation*. Book. 2nd
Edition. Cambridge: Cambridge University Press.

</div>

<div id="ref-Wang2015copula">

Wang, K., S. Yasmin, K. C. Konduri, N. Eluru, and J. N. Ivan. 2015.
“Copula-Based Joint Model of Injury Severity and Vehicle Damage in
Two-Vehicle Crashes.” Journal Article. *Transportation Research Record*,
no. 2514: 158–66. <https://doi.org/10.3141/2514-17>.

</div>

<div id="ref-Wang2005use">

Wang, X. K., and K. M. Kockelman. 2005. “Use of Heteroscedastic Ordered
Logit Model to Study Severity of Occupant Injury - Distinguishing
Effects of Vehicle Weight and Type.” Book Section. In *Statistical
Methods; Highway Safety Data, Analysis, and Evaluation; Occupant
Protection; Systematic Reviews and Meta-Analysis*, 195–204.
Transportation Research Record. [\<Go to
ISI\>://WOS:000234682300024](%3CGo%20to%20ISI%3E://WOS:000234682300024).

</div>

<div id="ref-White1972effects">

White, SB, and SB Clayton. 1972. “Some Effects of Alcohol, Age of
Driver, and Estimated Speed on the Likelihood of Driver Injury.” Journal
Article. *Accident Analysis & Prevention* 4 (1).

</div>

<div id="ref-Wu2014mixed">

Wu, Q., F. Chen, G. H. Zhang, X. Y. C. Liu, H. Wang, and S. M. Bogus.
2014. “Mixed Logit Model-Based Driver Injury Severity Investigations in
Single- and Multi-Vehicle Crashes on Rural Two-Lane Highways.” Journal
Article. *Accident Analysis and Prevention* 72: 105–15.
<https://doi.org/10.1016/j.aap.2014.06.014>.

</div>

<div id="ref-Yasmin2013evaluating">

Yasmin, Shamsunnahar, and Naveen Eluru. 2013. “Evaluating Alternate
Discrete Outcome Frameworks for Modeling Crash Injury Severity.” Journal
Article. *Accident Analysis & Prevention* 59: 506–21.
<https://doi.org/https://doi.org/10.1016/j.aap.2013.06.040>.

</div>

</div>
