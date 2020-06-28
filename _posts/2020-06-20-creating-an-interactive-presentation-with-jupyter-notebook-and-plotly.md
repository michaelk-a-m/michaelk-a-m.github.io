---
id: 131
title: Creating an Interactive Presentation with Jupyter Notebook and Plotly
date: 2020-06-29
author: michaelkam
layout: post
categories:
  - Data-Visualisation
tags:
  - plotly
  - jupyter notebook
  - presentation
  - jupyter slides
  - reveal.js
  - python
  - nbconvert
description:
  Let's say you have performed some analysis using Jupyter notebook and produced some great visualisations to back up your finding. How would you present this to your stakeholders and other people? One great way to ace this is to convert your jupyter notebook and plotly graphs to an interactive presentation that can impress people.
published: true
---

Let's say you have performed some analysis using Jupyter notebook and produced some great visualisations to back up your finding. How would you present this to your stakeholders and other people? One great way to ace this is to convert your jupyter notebook and plotly graphs to an interactive presentation that can impress people.

# Introduction
I learned on creating slides from Jupyter Notebook from [Tahsin Mayeesha's medium post](https://medium.com/learning-machine-learning/present-your-data-science-projects-with-jupyter-slides-75f20735eb0f). However, I was curious to see if I can incorporate interactive graphs from Plotly in the slides. Therefore, I wrote this post to share what I've learned. 

For this post, I will use the [Titanic dataset from Kaggle](https://www.kaggle.com/c/titanic). This dataset is a classic dataset used to learn machine learning models on classification problems.

In this case, my aim was to present my finding from an Exploratory Data Analysis on the Titanic dataset. I would include several visualisations and slides to show what you can do with Jupyter markdown and slides. Although I would use Plotly mostly for the slides, I have also included one plot with Seaborn and one plot from Pandas Profiling library to show the possibilities you can do.

For my presentation, I imported the following visualisation libraries.
* Plotly, including the Plotly Express and Plotly Graph Objects
* Matplotlib
* Seaborn
* Pandas Profiling visualisation for missing values in the dataframe.

All the libraries above are not required to make the slides work, you can just simply use `Plotly`, `Seaborn`, or even simply `Matplotlib`. You can check the full notebook on my GitHub repo [here](https://github.com/michaelk-a-m/TitanicSurvival/blob/master/jupyter-slides/titanic_survival_slides.ipynb).


# Jupyter Notebook Slideshow View and Setting
In Jupyter Notebook, each cell can play a part in the presentation slides. To set the role each cell play, you have to change the cell toolbar view by `View -> Cell Toolbar -> Slideshow`. 
![alt text]({{ site.url }}/images/jupyter-presentation/change-cell-toolbar-view.png "Change the Cell Toolbar View to Slideshow")


Changing the view will allow you to set what type of slides you want for each cell. There are 5 Slide Types you can select:
* **Slide**. This is the main content of the slide. You can place markdown, visuals, or even code here.
* **Sub-Slide**. I will consider this as a new slide with transition animation. You have to use the down arrow navigation to access it from the main slide.
* **Fragment**. This will be a part of the main slide. For example, one cell with the heading will be set as Slide and the visualisation will be set as Fragment. The fragment will be inside the main slide but with fade in transition.
* **Skip**. This cell will not be shown on the slides. Perfect for code and comment cells that you won't show. Be aware that you can't put skip for your visualisation code because the output will not be shown as well.
* **Notes**. This will create Speaker notes, but I haven't tried this function. From the [NbConvert reference](https://nbconvert.readthedocs.io/en/latest/usage.html), you need to have a local copy of the Reveal.JS file to allow you having a presenter view with the notes. 


### Skip type for Non-Visualisation Code Cell
Now, you can select Skip for the Slide Type of the library import and initial dataset load cells.
![alt text]({{ site.url }}/images/jupyter-presentation/set-skip-type.png "Set the Slide Type to be Skip")


### Slide type for Title and Markdown Cell
For the first slide or the cover, I will only use one markdown cell with title, subtitle, and author.
```markdown
# Titanic Survival Dataset
## Exploratory Data Analysis
By: Michael Kam
```
After you run the cell, set the Slide Type of the cell to Slide like the following.  
![alt text]({{ site.url }}/images/jupyter-presentation/first-slide.png "Set the Title Markdown as Slide")


### Slide + Fragment types for Mixture of Markdown and Code Cells
On this slide, I use a combination of Slide and Fragment types. The Slide type would be used to give a header for the visualisation. This is just a simple markdown cell. Afterwards, the second cell contains the code to visualise an interactive bar chart with Plotly Express. Since this will be on the same page as the header, I set the Slide Type as Fragment. You can see it on the screenshot below.
![alt text]({{ site.url }}/images/jupyter-presentation/set-fragment-type.png "Set the Visualisation Code Cell into Fragment")


### Hiding the Codes on the Slide
From searching online, I found tips from [Github forum](https://github.com/jupyterlab/jupyterlab/issues/4100#issuecomment-370938358) to ensure that the code won't show on the slides, especially if you are plotting graphs. However, I found a simpler way to do what he suggested. To do this, we need to change the Cell Toolbar View into Tags (`View -> Cell Toolbar View -> Tags`). For each code cell that has visualisation element, you can enter in `to_remove` (or it can be anything, really) on the field and press `Add Tag` as shown below.
![alt text]({{ site.url }}/images/jupyter-presentation/add-tag.png "Add Tag to Code Cell")

# Specific for Plotly
If you use plotly, then you need to pay attention in this section. Otherwise, your plotly graphs won't show up in the slides.

Plotly will require its JavaScript file (`plotly.js`) to produce the graph. There are three options you can choose:
1. Local copy of JavaScript file. This is the easiest and simplest way to ensure the Plotly graphs to work. You can download the latest JS file from this [link](https://cdn.plot.ly/plotly-latest.min.js) and rename it into plotly.js. This file needs to be in the same folder or directory with the resulting HTML slides file. 
2. Refer to CDN online. This is a great option if you want a lightweight HTML file and have a constant access to the Internet. This is perfect if you want to embed the slides on a website. To do this, create a new code cell at the beginning, include this code here:
```python
from IPython.display import Javascript
Javascript(
"""require.config({
    paths: { 
        plotly: 'https://cdn.plot.ly/plotly-latest.min'
    }
});"""
)
``` 
3. Include the JavaScript in the notebook. The Plotly JS needs to be inserted inline within the jupyter notebook. You can create a cell at the beginning with the following code.
```python
from IPython.display import Javascript
from plotly.offline import get_plotlyjs
Javascript(get_plotlyjs())
```
The `get_plotlyjs` function would return the whole minified Plotly JS script and by using the `Javascript` function from IPython we include this in the notebook. The resulting file would be 3MB larger because of the inclusion. However, you will only have one HTML file that you can just send or use anytime.

# Converting the Notebook to Slides
To produce the slides from the jupyter notebook, run the following code on your command prompt or bash:

```bash
jupyter nbconvert [jupyter_notebook_name].ipynb --to slides --no-prompt --TagRemovePreprocessor.remove_input_tags={\"to_remove\"} --post serve 
```

Here we are calling `nbconvert` function that can be used to convert the notebook into slides (HTML format with Reveal.js) or other formats as can be referred on the [documentation](https://nbconvert.readthedocs.io/en/latest/usage.html). 

The `TagRemovePreprocessor` will remove any code with `to_remove` tag as we specified. Again, this can be replaced with any other tag you specified before hand.

If you check your directory, the html version of your notebook will be there. You can open this on the web browser and use the directional pad on the screen or arrow keys on your keyboard to navigate. I was able to open the file and interact with Plotly graphs successfully using Chrome, Firefox, Opera, and Microsoft Edge. I heard this might not work so well with Safari but I can't confirm this.

For me personally, I found that  using `--post serve`, I could not terminate the server using Ctrl-C as described even after many tries. However, I also found that I can omit this in my command prompt and still get the HTML slides file.  

# Final Output of the Slides
Here is the following slides I produced from my jupyter notebook that you can find on [GitHub](https://github.com/michaelk-a-m/TitanicSurvival/blob/master/jupyter-slides/titanic_survival_slides.ipynb)
<iframe src="{{ site.url }}/uploads/jupyter-slides/titanic_survival_slides.slides.html" width="100%" height="700rem"></iframe>

