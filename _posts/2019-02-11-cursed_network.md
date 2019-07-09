---
title: "cursed_network.jpg: Detecting and Characterizing Cursed Images"
layout: post
date: 2019-04-22
projects: true
category: project
author: dylanrose
externalLink: false
---

One of my favorite internet hobby-horses are _"cursed" images_. These are pictures that a handful of internet communities have suggested are special in that they reliably induce a sense of unease or discomfort in the viewer. **KnowYourMeme** has a nice definition, too:

>They are generally pictures or photographs that are seen as disturbing to the viewer, either due to the poor photo quality or content within the image that is abnormal or illogical. Images of this or similar nature are sometimes seen as the visual equivalent to CreepyPasta. They have inspired several popular social media accounts devoted to posting various cursed images.

I think these two examples nicely demonstrate what we're dealing with:

cursed_bread          | cursed_queen
:-------------------------:|:-------------------------:
![](/assets/images/cursed_images/cursed_bread.jpg)  |  ![](/assets/images/cursed_images/cursed_queen.jpg)

# Why "cursed" images?

One of the projects I've been working on as part of my dissertation is a method for measuring how _semantically appropriate_ objects are to the kind of environment presented in an image. 

To help unpack this a bit, consider this famous drawing from [Loftus & Mackworth, (1978)](https://psycnet.apa.org/record/1980-22602-001):

![](/assets/images/cursed_images/cursed_octopus.png)

Most of us, on seeing this picture, can quickly i.d. that it contains a  strange but evidently very polite new friend. In my immediate scientific neck of the woods, we know him as Senor Pulpo: 

![](/assets/images/cursed_images/senor_pulpo.png)

Most adults known that octopuses are more likely to be found in aquariums or in the ocean than in farmyards. As a result, were he to formally receive us in either of the first two environments, we'd agree that Senor Pulpo knew himself well and had placed himself in his best social element accordingly. In terms of the relevant research, we'd say that he was "semantically appropriate" to those scene contexts. But if we met him instead in a farmyard like the one in the drawing, we'd probably also agree that having met him there was unlikely, and thus that he was "semantically inappropriate" to that context (not to mention probably in desperate need of emergency veterinary assistance, despite his brave and friendly mien).

This category of visual/perceptual relationships is referred to broadly as the study of _scene grammar_. Despite its nice conceptual appeal, there is significant ongoing scientific debate over the contents/functioning/existence of scene grammars. Arguably the main reason for this is simply how hard it is to manipulate scene grammatical relationships using realistic or lifelike image content. As an example of why this is important, consider that just as we know that octopi belong in oceans and aquaria and not in farmyards, we also know that drawings of those environments and their contents don't have to follow the same rules that their real counterparts do, such as things having to behave sensibly with respect to gravity/containing violations of organismic oxygen supplies/etc. 

So let's imagine we've done an eye tracking experiment to test whether Senor Pulpo's jaunty tentacular greeting is the first thing to "pop out" at us visually, drawing our eye movements towards him before we look at anything else. Irritatingly, we don't see this effect! And now because we've used a line-drawing as stimulus, we now don't know whether or not it's because he's just not odd enough a presence in _any_ representation of a farmyard to have created the necessary popout, or if we're just primed not to see anything as all that unusual in this sort of drawing altogether.

Most researchers have responded to this problem in one of two ways:

1. they just ignore it and use highly manipulable line-drawing/cg stimuli despite how unnatural these are.
2. [they do the sweaty, difficult, painstaking, time-consuming work of carefully photographing bog-rolls in the dishwasher](https://www.scenegrammarlab.com/research/scegram-database/) [or watering cans on the roof](http://info.ni.tu-berlin.de/photodb/).

Enter "cursed" images:

![](/assets/images/cursed_images/cursed_motorcycle.jpg)

Why drag a semantically inappropriate motorcycle into your own bed when someone on the internet has already done the dirty work for you?

To wit: almost as soon as I saw them, cursed images struck me as a beautiful naturally occuring semantic image anomaly dataset. This project was my first effort to demonstrate that that is in fact the case.

# The Project

From the beginning, I've had three goals for my work with cursed images:

1. to demonstrate that it is possible to reliably discriminate cursed from non-cursed images. Rather than deal with the headaches of getting IRB approval and recruiting subjects to show these to people, I would instead use a simple convolutional neural network.
2. if cursed images are in fact discriminable in this way, to characterize the differences between them and non-cursed images in terms of several slightly more readily interpretable feature-spaces. Specifically, these are:
    1. object-contextual semantic relationships.
    2. valence & arousal.
    3. color and spatial frequency information.
3. to test whether the features learned for discriminating between cursed and non-cursed images could facilitate transfer learning to a similar problem on an interesting related dataset.

## 0. The Data

### Cursed & Non-Cursed Images

To create a cursed image dataset, I just used the excellent [RipMe.jar](https://github.com/RipMeApp/ripme) to scrape from the [cursed images Twitter account](https://twitter.com/cursedimages?lang=en) and [subreddits](https://www.reddit.com/r/cursedimages/). Big 'ole NSFW warning on both links, btw -- consider yourself warned.

The Twitter is old and I think now mostly inactive, but the subreddit is going strong and has even recently had a bit of a bit of a public-facing renaissance/admin putsch over standards around things like content containing gore, etc. See e.g.: 

[![hwang](https://i.ytimg.com/vi/qZfA6Fb7JNk/hqdefault.jpg?sqp=-oaymwEjCPYBEIoBSFryq4qpAxUIARUAAAAAGAElAADIQj0AgKJDeAE=&rs=AOn4CLA5KuN-s5yadq6jLDX3XD5A1MBztg)](https://www.youtube.com/watch?v=qZfA6Fb7JNk&t=98s) 

So anyway there's a large archive of these things out there, and at least in some places people are continuing to add to the available pool. The only downside to using RipMe for scraping over this set is that the number of images scraped seems to be influenced by a lot of parameters I didn't have time to really play with, so the set it generated was small: just 448 pictures.

