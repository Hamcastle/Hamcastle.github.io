---
title: "Task decoding from eye movement: BioEye 2015"
layout: post
date: 2019-01-27
projects: true
category: project
author: dylanrose
externalLink: false
---

If I had to summarize the focus of my scientific research, I'd say it hinges on the idea that human _eye movement_ turns out to be a pretty good window into human _cognition_. Better, it's one that doesn't require a fancy magnet and millions of research dollars, just increasingly cheap, consumer grade tech eye trackers and a form of behavior we've all gotten quite comfortable with: looking at pictures and watching movies. This is possible because the eye movement control system in the brain is plugged into a huge range of other brain networks. Like _really_ connected:

![itti_connection](/assets/images/bio_eye/itti_connection.png)
<figcaption class="caption">Itti, L. "New Eye-Tracking Techniques May Revolutionize Mental Health Screening. Neuron, 88(3),2015</figcaption>

As a result of all those connections, if something makes a _transient_ change to the state of someone's brain (e.g. trying to complete a specific task) or a more fundamental change to their structure and function (e.g. a developmental or clinical condition), it's likely to end up encoded into patterns of eye movements. We can then perhaps recover that information from eye movement behavior using machine learning techniques akin to those used in neuro-science with multi-voxel pattern analysis (**MVPA**).

The simplest form of data that can be used for these purposes are parameters of eye movements themselves. Under most conditions where what we're looking at is _static_, we make two primary kinds of eye movements: _fixations_ and _saccades_. _Fixations_ are periods of (relative) oculomotor stillness -- these help us keep the high-res/color oriented center of vision (anatomically, _fovea_) over things we want to inspect. _Saccades_ are large, ballistic movements that allow us to re-position the fovea on new targets. This is a nice summary image of what both of these movement types do and how they impact our vision taken from [Eyetracking.me](http://eyetracking.me/?page_id=9) :

![eye_movements](/assets/images/bio_eye/eye_movements.png)

Fixations are mostly defined in terms of their duration: how long we spend looking at particular image regions. Saccades have many more parameters, but arguably the most important are their magnitude and peak velocity (how big the movement and how fast they get). These features, along with a few others, have been shown in [several](https://www.ncbi.nlm.nih.gov/pubmed/21799023) [studies](https://www.ncbi.nlm.nih.gov/pubmed/19757945) to be sensitive to things like task directions.

The idea that we can extract fairly complex information like observer goals or health status from such a cheap, abundant source of data with very little in the way of burden to subjects is a natural bridge to a lot of application contexts, such as human-computer interfaces and computational medicine. No surprise then that there's been at least one public competition organized toward these ends: [BioEye](https://bioeye.cs.txstate.edu/) @ Texas State, organized by [Oleg Komogortsev](https://userweb.cs.txstate.edu/~ok11/).

The primary objective for the competition was to use provided eye movement data to discriminate between trials where subjects tracked a dot that randomly moved around the screen, and where they were reading a simple text passage:

Reading          |  Random Dot
:-------------------------:|:-------------------------:
![](/assets/images/bio_eye/reading.png)  |  ![](/assets/images/bio_eye/random_dot.gif)

## The Project

I sadly found BioEye during my first year of grad school too late to be able to compete. I did however find it just in time to use for my final project in the first data-related class I took in grad school: CS7280: _Statistics for Big Data_, taught by [Olga Vitek](https://www.khoury.northeastern.edu/people/olga-vitek/). This was probably one of the most challenging classes I've ever taken -- I'd never _really_ applied linear algebra before and had to learn how to do so as I went -- but I learned some cool stuff and met some [nice](https://github.com/mavezdabas) [folks](https://github.com/Pushpinder2751) outside of the psych department who I probably wouldn't have encountered, otherwise.

The assignment was simple: find a publically available dataset and apply some of what we'd learned (which was almost entirely about linear modeling variants and not flashier ML techniques like SVMs) to it. We ended up using both a simple and an adaptive LASSO variant of logistic regression cross-validated over the training data in an 80/20% training/test set split. We began with six variables:

1. Number of fixations in the trial.
2. Mean duration of fixations during the trial (in milliseconds).
3. Mean horizontal dispersion of fixations during the trial (in pixels).
4. Mean vertical dispersion of fixations (in pixels).
5. Peak horizontal velocity within recorded fixations.
6. Peak vertical velocity within recorded fixations.

Given that these parameters were likely to have significant sequential dependencies between trials (violating the assumption of independence of observations), we ended up using a reduced set of five parameters that were differences between the original set of six _between subsequent trials_:

1. Mean centered difference in mean duration of fixations between trials within each task.
2. Mean centered difference in mean horizontal dispersion between trials.
3. Mean centered difference difference in mean vertical dispersion between trial.
4. Mean centered difference in mean horizontal velocity within recorded fixations between trials.
5. Mean centered difference difference in mean vertical velocity within recorded fixations between trials.

After controlling for sequential dependencies in the data, we still had to deal with the evident correlations between the features:

![corrgram](/assets/images/bio_eye/feature_corrgram.png)


We therefore used the LASSO to try and shrink the set of variables included. As you can see, we did better than chance (50%) but not that much better (full model AUC: 0.69; LASSO reduced model: 0.68). These were the ROC curves:

![roc_curves](/assets/images/bio_eye/roc.png)
<figcaption class="caption">Red curve for the full model, black curve for the model with the reduced parameter set extracted using the LASSO.</figcaption>

I blame our relatively poor performance (we would've come [in fourth](https://bioeye.cs.txstate.edu/results.php)) on a couple of culprits:

1. Eye movement data is often nested, with repeated measurements for each observer under different conditions. When I work with eye movement data in scientific experiments, I typically do so used generalized linear models with subject-wise random effects parameters. As these weren't covered in the class, they were out. They probably would've improved the performance by accounting for the significant within and between subjects variability we tend to see in this type of dataset. GLMs also do away with the need for independent observations, so we could've avoided having to transform the data into an arbitrary and probably less informative set of trial-to-trial relationships that dealth with sequential dependencies at only a single time-scale.
2. It's certainly true that fancier ML methods get most of the hype and that linear models are powerful and probably underused, but compared to what [our nominal competition](https://arxiv.org/abs/1601.03333) was bringing, we probably didn't stand a chance with such simple tools.

## The Takeaway

This class and the final project were probably my first practical exposure to statistical learning and to actual "big data". It showed me that what I was trying to do was _possible_, even with fairly simple tools, but that doing so would require some special tools and a different way of thinking about data than I was used to in an experimental context. 

Most of all I learned that dealing with data like these was going to break most traditional "big data" tools, which couldn't always naturally accomodate relationships between repeated/nested observations without a lot of careful database design (these relationships are hell on wheels to get right in appropriately normalized SQL databases, for example). 

We were also dealing with the simplest case: our data were just eye movement type parameters, which do still behave more or less nicely in an ordinary spreadsheet or text file. Much of the research in this area has moved on to bindings between eye movements and various image feature type maps that for the sake of sanity should really be bound very directly to the gaze data themselves. It wasn't until I started playing with HDF and MongoDB many years later for a class that I found resources that dealt with these issues smoothly.

(We got an A on the project, anyway :stuck_out_tongue_winking_eye: ).