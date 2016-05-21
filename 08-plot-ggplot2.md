---
layout: page
title: R for reproducible scientific analysis
subtitle: Creating publication quality graphics
minutes: 70
---



> ## Learning Objectives {.objectives}
>
> * To be able to use ggplot2 to generate publication quality graphics
> * To understand the basics of the grammar of graphics:
>   - The aesthetics layer
>   - The geometry layer
>   - Adding statistics
>   - Transforming scales
>   - Coloring or paneling by groups.
>

Plotting our data is one of the best ways to
quickly explore it and the various relationships
between variables.

There are three main plotting systems in R,
the [base plotting system][base], the [lattice][lattice]
package, and the [ggplot2][ggplot2] package.

[base]: http://www.statmethods.net/graphs/
[lattice]: http://www.statmethods.net/advgraphs/trellis.html
[ggplot2]: http://www.statmethods.net/advgraphs/ggplot2.html

Today we'll be learning about the ggplot2 package, because
it is the most effective for creating publication quality
graphics.

ggplot2 is built on the grammar of graphics, the idea that any plot can be
expressed from the same set of components: a **data** set, a
**coordinate system**, and a set of **geoms**--the visual representation of data
points.

The key to understanding ggplot2 is thinking about a figure in layers: just like
you might do in an image editing program like Photoshop, Illustrator, or
Inkscape.

Let's start off with an example:


~~~{.r}
library("ggplot2")
ggplot(data = healthData, aes(x = selfRatedHealth, y = health)) +
  geom_point()
~~~

<img src="fig/08-plot-ggplot2-lifeExp-vs-gdpPercap-scatter-1.png" title="plot of chunk lifeExp-vs-gdpPercap-scatter" alt="plot of chunk lifeExp-vs-gdpPercap-scatter" style="display: block; margin: auto;" />

So the first thing we do is call the `ggplot` function. This function lets R
know that we're creating a new plot, and any of the arguments we give the
`ggplot` function are the *global* options for the plot: they apply to all
layers on the plot.

We've passed in two arguments to `ggplot`. First, we tell `ggplot` what data we
want to show on our figure, in this example the health data we read in
earlier. For the second argument we passed in the `aes` function, which
tells `ggplot` how variables in the **data** map to *aesthetic* properties of
the figure, in this case the **x** and **y** locations. Here we told `ggplot` we
want to plot the "education" column of the gapminder data frame on the x-axis, and
the "health" column on the y-axis. Notice that we didn't need to explicitly
pass `aes` these columns (e.g. `x = healthData[, "education"]`), this is because
`ggplot` is smart enough to know to look in the **data** for that column!

By itself, the call to `ggplot` isn't enough to draw a figure:


~~~{.r}
ggplot(healthData,aes(x=selfRatedHealth,y=health))
~~~

<img src="fig/08-plot-ggplot2-unnamed-chunk-2-1.png" title="plot of chunk unnamed-chunk-2" alt="plot of chunk unnamed-chunk-2" style="display: block; margin: auto;" />

We need to tell `ggplot` how we want to visually represent the data, which we
do by adding a new **geom** layer. In our example, we used `geom_point`, which
tells `ggplot` we want to visually represent the relationship between **x** and
**y** as a scatterplot of points:


~~~{.r}
ggplot(healthData,aes(x=selfRatedHealth,y=health)) +
  geom_point()
~~~

<img src="fig/08-plot-ggplot2-education-vs-health-scatter2-1.png" title="plot of chunk education-vs-health-scatter2" alt="plot of chunk education-vs-health-scatter2" style="display: block; margin: auto;" />

> ## Challenge 1 {.challenge}
>
> Modify the following example so that the figure visualises how illnessReversed 
> varies with health:
>
> 
> ~~~{.r}
> ggplot(healthData,aes(x=selfRatedHealth,y=health)) +
>   geom_point()
> ~~~
>

> ## Challenge 2 {.challenge}
>
> In the previous examples and challenge we've used the `aes` function to tell
> the scatterplot **geom** about the **x** and **y** locations of each point.
> Another *aesthetic* property we can modify is the point *color*. Modify the
> code from the previous challenge to **color** the points by the "education"
> column.
>
> **HINT**: transform the education column to a factor using the `as.factor()` function.

## Layers

We could also make a line plot.
Instead, let's tell `ggplot` to visualise the data as a line plot:


~~~{.r}
ggplot(healthData,aes(x=health,y=illnessReversed,color=as.factor(education),by=sex)) + geom_line()
~~~

<img src="fig/08-plot-ggplot2-health-line-1.png" title="plot of chunk health-line" alt="plot of chunk health-line" style="display: block; margin: auto;" />

Instead of adding a `geom_point` layer, we've added a `geom_line` layer. We've
added the **by** *aesthetic*, which tells `ggplot` to draw a line for each
sex.

But what if we want to visualise both lines and points on the plot? We can
simply add another layer to the plot:


~~~{.r}
ggplot(healthData,aes(x=health,y=illnessReversed,color=as.factor(education),by=sex)) + geom_point() + geom_line()
~~~

<img src="fig/08-plot-ggplot2-health-line-point-1.png" title="plot of chunk health-line-point" alt="plot of chunk health-line-point" style="display: block; margin: auto;" />

It's important to note that each layer is drawn on top of the previous layer. In
this example, the points have been drawn *on top of* the lines. Here's a
demonstration:


~~~{.r}
ggplot(healthData,aes(x=health,y=illnessReversed,by=sex)) + geom_line(aes(color=as.factor(education))) + geom_point()
~~~

<img src="fig/08-plot-ggplot2-health-layer-example-1-1.png" title="plot of chunk health-layer-example-1" alt="plot of chunk health-layer-example-1" style="display: block; margin: auto;" />

In this example, the *aesthetic* mapping of **color** has been moved from the
global plot options in `ggplot` to the `geom_line` layer so it no longer applies
to the points. Now we can clearly see that the points are drawn on top of the
lines.

> ## Challenge 3 {.challenge}
>
> Switch the order of the point and line layers from the previous example. What
> happened?
>

## Transformations and statistics

Ggplot also makes it easy to overlay statistical models over the data. To
demonstrate we'll go back to our first example:


~~~{.r}
ggplot(data = healthData, aes(x = selfRatedHealth, y = health,color=as.factor(education))) +
  geom_point()
~~~

<img src="fig/08-plot-ggplot2-health-vs-gdpPercap-scatter3-1.png" title="plot of chunk health-vs-gdpPercap-scatter3" alt="plot of chunk health-vs-gdpPercap-scatter3" style="display: block; margin: auto;" />

<!-- not useful ???

Currently it's hard to see the relationship between the points due to some strong
outliers in GDP per capita. We can change the scale of units on the y axis using
the *scale* functions. These control the mapping between the data values and
visual values of an aesthetic.


~~~{.r}
ggplot(data = healthData, aes(x = health, y = education, color=sex)) +
  geom_point() + scale_y_log10()
~~~

<img src="fig/08-plot-ggplot2-axis-scale-1.png" title="plot of chunk axis-scale" alt="plot of chunk axis-scale" style="display: block; margin: auto;" />

The `log10` function applied a transformation to the values of the gdpPercap
column before rendering them on the plot, so that each multiple of 10 now only
corresponds to an increase in 1 on the transformed scale, e.g. a GDP per capita
of 1,000 is now 3 on the y axis, a value of 10,000 corresponds to 4 on the y
axis and so on. This makes it easier to visualise the spread of data on the
y-axis. -->

We can fit a simple relationship to the data by adding another layer,
`geom_smooth`:


~~~{.r}
ggplot(data = healthData, aes(x = selfRatedHealth, y = health,color=as.factor(education))) +
  geom_point() + geom_smooth(method="lm")
~~~

<img src="fig/08-plot-ggplot2-lm-fit-1.png" title="plot of chunk lm-fit" alt="plot of chunk lm-fit" style="display: block; margin: auto;" />

We can make the line thicker by *setting* the **size** aesthetic in the
`geom_smooth` layer:


~~~{.r}
ggplot(data = healthData, aes(x = selfRatedHealth, y = health,color=as.factor(education))) +
  geom_point() + geom_smooth(method="lm", size = 1.5)
~~~

<img src="fig/08-plot-ggplot2-lm-fit2-1.png" title="plot of chunk lm-fit2" alt="plot of chunk lm-fit2" style="display: block; margin: auto;" />

There are two ways an *aesthetic* can be specified. Here we *set* the **size**
aesthetic by passing it as an argument to `geom_smooth`. Previously in the
lesson we've used the `aes` function to define a *mapping* between data
variables and their visual representation.

> ## Challenge 4 {.challenge}
>
> Modify the color and size of the points on the point layer in the previous
> example.
>
> Hint: do not use the `aes` function.
>

## Multi-panel figures

Earlier we visualised the change in health with selfRatedHealth across education levels
in one plot. Alternatively, we can split out different groups in the data into multiple panels
by adding a layer of **facet** panels:


~~~{.r}
ggplot(data = healthData, aes(x = selfRatedHealth, y = health,color=paste(sex,HIGroup))) + geom_smooth(method="lm") + facet_wrap( ~ alcoholUseInYoungAdulthood)
~~~

<img src="fig/08-plot-ggplot2-facet-1.png" title="plot of chunk facet" alt="plot of chunk facet" style="display: block; margin: auto;" />

The `facet_wrap` layer took a "formula" as its argument, denoted by the tilde
(~). This tells R to draw a panel for each unique value in the alcohol column
of the healthData dataset.

## Modifying text

To clean this figure up for a publication we need to change some of the text
elements. 

We can do this by adding a couple of different layers. The **theme** layer
controls the axis text, and overall text size, and there are special layers
for changing the axis labels. To change the legend title, we need to use the
**scales** layer.


~~~{.r}
ggplot(data = healthData, aes(x = selfRatedHealth, y = health,color=paste(sex,HIGroup))) + geom_smooth(method="lm") + facet_wrap( ~ alcoholUseInYoungAdulthood) +
  xlab("Self-reported health") + ylab("Health rating from teacher") + ggtitle("Figure 1") +
  scale_colour_discrete(name="Study group and gender", labels= c("Hawaii - Female","Teman - Female","Hawaii - Male","Teman - Male")) +
  theme(axis.text.x=element_blank(), axis.ticks.x=element_blank())
~~~

<img src="fig/08-plot-ggplot2-theme-1.png" title="plot of chunk theme" alt="plot of chunk theme" style="display: block; margin: auto;" />


This is just a taste of what you can do with `ggplot2`. RStudio provides a
really useful [cheat sheet][cheat] of the different layers available, and more
extensive documentation is available on the [ggplot2 website][ggplot-doc].
Finally, if you have no idea how to change something, a quick google search will
usually send you to a relevant question and answer on Stack Overflow with reusable
code to modify!

[cheat]: http://www.rstudio.com/wp-content/uploads/2015/03/ggplot2-cheatsheet.pdf
[ggplot-doc]: http://docs.ggplot2.org/current/


> ## Challenge 5 {.challenge}
>
> What would you like to visualise from the dataset?
> Go ahead and try to do it using ggplot!
>

Lastly, `ggplot2` has a very handy function, `ggsave()` for saving the current plot to a file:


~~~{.r}
ggsave("fig/Figure1.pdf")
~~~



~~~{.output}
Saving 7 x 7 in image

~~~

## Challenge solutions

> ## Solution to challenge 1 {.challenge}
>
> Modify the example so that the figure visualises how illnessReversed 
> varies with health:
>
> 
> ~~~{.r}
> ggplot(healthData, aes(x = health, y = illnessReversed)) + geom_point()
> ~~~
> 
> <img src="fig/08-plot-ggplot2-ch1-sol-1.png" title="plot of chunk ch1-sol" alt="plot of chunk ch1-sol" style="display: block; margin: auto;" />
>

> ## Solution to challenge 2 {.challenge}
>
> In the previous examples and challenge we've used the `aes` function to tell
> the scatterplot **geom** about the **x** and **y** locations of each point.
> Another *aesthetic* property we can modify is the point *color*. Modify the
> code from the previous challenge to **color** the points by the "education"
> column.
>
> 
> ~~~{.r}
> ggplot(healthData, aes(x = health, y = illnessReversed, colour=as.factor(education))) + 
> geom_point()
> ~~~
> 
> <img src="fig/08-plot-ggplot2-ch2-sol-1.png" title="plot of chunk ch2-sol" alt="plot of chunk ch2-sol" style="display: block; margin: auto;" />
>

> ## Solution to challenge 3 {.challenge}
>
> Switch the order of the point and line layers from the previous example. What
> happened?
>
> 
> ~~~{.r}
> ggplot(healthData,aes(x=health,y=illnessReversed,by=sex)) + geom_point() + geom_line(aes(color=as.factor(education)))
> ~~~
> 
> <img src="fig/08-plot-ggplot2-ch3-sol-1.png" title="plot of chunk ch3-sol" alt="plot of chunk ch3-sol" style="display: block; margin: auto;" />
> 
> The lines now get drawn over the points!
>


> ## Solution to challenge 4 {.challenge}
>
> Modify the color and size of the points on the point layer in the previous
> example.
>
> Hint: do not use the `aes` function.
>
> 
> ~~~{.r}
> ggplot(data = healthData, aes(x = selfRatedHealth, y = health)) +
>   geom_point(size=3,color="red") + geom_smooth(method="lm", size = 1.5)
> ~~~
> 
> <img src="fig/08-plot-ggplot2-ch4-sol-1.png" title="plot of chunk ch4-sol" alt="plot of chunk ch4-sol" style="display: block; margin: auto;" />
>

