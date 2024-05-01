# PhysiWrapper

## An addon wrapper, addon factory, base_custom_class for glueing c++ class objects to cells inside physicell and some example code for how to implement it.

These are files that can be added to PhysiCell http://physicell.org/ (or https://github.com/MathCancer/PhysiCell ) that let you use an addon wrapper and addon factory. 

This is mostly a development tool since many of the use cases can be created using already existing physicell features. 

The addon wrapper and factory allow you to attach arbitrarilly many custom classes to cells with relatively light overhead in a (mostly*) thread safe way. You can even detach them during a simulation (but you shouldn't).

The largest component of the addon wrapper overhead is that the addon wrapper can be easily set to watch for PhysiCell events ie. are the cells still in the domain, did they produce daughters, etc. I'll include a daughter tracker addon here as soon as I have a bit more time to debug it. By default it checks domain, and live/dead.

This is mostly a tool for my own use to build modules that may either become XML custom_variables or part of an inherited agent, but I will be releasing a few addons that I am using for my PhD research in this style since it makes development/debugging of independant simulation components much faster and easier for me.

Also, calling clean_up_Addons(); in main.cpp will take care of the garbage collection of these heap allocated custom classes.

### *Mostly Thread Safe
The addon factory is run in a setup function, this function creates an Addon type which is the wrapper that glues a particular custom class. These are added in the order they are created to an external vector called Addon_list. This uses push_back(), so this part needs to be run either with a #pragma omp critical or in the setup function that is run in serial in main.cpp.

Each custom base class has an update_state() virtual function that when called using the Addon->update_custom_class(Cell* pCell) function can be run inside a parallel section when an Addon type is created it populates an internal set of vectors that store the custom_class_instances.  As long as the vectors inside an Addon type aren't forced to resize post creation then everything stays thread safe (I didn't make it impossible to do this, but I did make it impossible to copy/assign a particular Addon). On creation of an Addon type, the internal vectors get sized to what is hopefully the maximum number of agents your simulation will ever have and they hold a "blank" custom_class instance that gets overwritten when the desired custom_class is attached to a cell. These internal vectors are indexed by pCell->index to make sure the right cell gets the correct thing.

I've included #pragma omp critical in (I think) all the places where a necessary resize or pushback occurs just in case.

I've tested this with 100,000 cells and the overhead was minimal, (almost no net time difference between using a Custom_Variable and my example class on my laptop) but at 1,000,000 cells the overhead is quite noticeable. 
