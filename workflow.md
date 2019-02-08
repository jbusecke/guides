# My Science Workflow
Current state: Semi-organized mess of folders, repos for projects, ‘clean’ repos for publications and other half-baked packages.

Goals: The ultimate reproducible, shareable, well tested, backed up and documented git repo the science world needs! 
…but in a fraction of the time this usually takes to set up.

### The Idea
Science usually starts as an idea, and once this idea takes form I will start a new `project`. This should generally be a broad topic (maybe resulting in several related publications, or moments of happiness), so it can grow over time.

### What goes where?
I try to collect *everything* that belongs to this project in this one central place.
Notes, ideas, formulas and References go into [jupyter notebooks](https://jupyter.org/) within the `notebooks`  folder. These are the central instrument for trying code and collecting thoughts.
Any kind of data belongs into the `data` folder and should be only accessed from there within the notebooks to make the whole project portable — I want to be able to set this up on another computer/cluster and be ready to go!

The original [cookiecutter-data-science](https://github.com/drivendata/cookiecutter-data-science) (the starting point for my template) goes as far as to block any files from the data folder. All input is generated via a script that downloads the data into the folder. 

I generally recommend to follow this approach, but sometimes it can be useful to just save a softlink to a datafile (e.g. on the archive of a large cluster). 
> Be aware that this most likely makes the repo machine specific. That is however,  often a given in my field anyways since most of the data I work with is very large and resides on clusters at universities or labs like NOAA/GFDL.   
> Just try to make your work as reproducible as possible. And **document** when things are machine specific.  

Since we are talking about documentation: **DO IT. AND DO IT RIGHT AWAY**. We have all been there, thinking “Ill write down where this idea came from, how I solved this specific problem etc…” and it is incredible how fast it is forgotten.
You can alleviate a lot of these problematic by using e.g. a script to set up your data folder. This kind self-explanatory documentation is very efficient, but at the very least document what you are doing somewhere. You **will** thank yourself later.

So far this workflow does not need a lot of fancy organization. Just keep a tidy folder (easier said then done) and be happy. But now we have to talk about the main tool for our scientific analysis: Our code.

### Code Organization and Workflow
Code development is a multi-step process, and [cookiecutter-science-project ](https://github.com/jbusecke/cookiecutter-science-project) helps making these steps as easy as possible.

Usually I start to write code in my notebooks, test it quickly look at results and repeat.
But eventually any logic that goes beyond ‘basic’ stuff and in particular if it should be reused in other notebooks, needs to be moved to a proper (installable) python module. Having an installable package makes the import process easy and someone else could just clone the whole repo install it and use your code anywhere on the system.
![](https://media.giphy.com/media/fDzM81OYrNjJC/giphy.gif)


Make sure the code in this package is properly formatted, documented (write the docstring!) and has unit .
This makes it easy to find and fix bugs so that all notebooks and analysises centrally profit from the development. 

If some of the code is so helpful that you want to use it across projects it will be moved to dedicated stand along python packages. This should be super easy now since you already have tests and basically just need to copy + paste and change your import location.

[cookiecutter-science-project ](https://github.com/jbusecke/cookiecutter-science-project) comes with everything set up to enable you to start right away, show you if your code broke the tests at some point (thanks to [Travis CI](https://travis-ci.org/) and also guilt-trips you into having your code covered by unit tests to keep that sweet, sweet green [codecov](https://codecov.io/) badge.

### Example
1. Have a grandiose idea 💡
2. Set up a project folder by following the [cookiecutter-science-project  instructions]() with the projectname `coolstuff` (obviously you chose the name, this is just an example!
3. Create your first awesome notebook within `notebooks` with a sensible name like `name_01_FirstSteps.ipynb`
4. Hack away and science the 💩 out of your dataset.
5. Whenever your done for the day, or just have discovered something cool, remember to commit your changes to github.
6. Over time your notebook gets full and you decide some of the code you wrote was actually useful. Well move ahead and copy the function `coolfunc` you wrote to a new python module `coolstuff/coolmodule.py`. Again the naming is up to you.
7. Now write a nice docstring, clean your code and write a simple unittest.
8. Once all of that works, you can install the package with `python setup.py develop` and within your notebook import your function with 
```
from coolstuff.coolmodule import coolfunc
```
While you are at it also add [autoimport] so that you dont have to reload the notebook every damn time you change something in your `coolmodule`.
9. Science and repeat until you have a story worth publishing, a cool video or something else that makes you happy or rich (if it makes you rich, dont keep it to yourself. Take some of your money and help some less fortunate! Its good karma!).



### Wishlist for the future
Just some stuff that is not in the repo yet
- Paper repos as submodules
- Versioning
	- [ ] Document and save all versions and packages with conda
	- [ ] Can this be automated when pushing a version tag?  
- How do I assign a temporary name and then have it changed once the pub is accepted (would be nice to have a script for that).
- I dont think I need a makefile…yet (lets see later).
- Cool would be to have a locked env file generated when I want to publish a new version (but that can be achieved in the future.
- 	Binder Deployment? Both for `project` and `paper`?

