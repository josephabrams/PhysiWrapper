# PhysiWrapper

## An addon wrapper, addon factory, base_custom_class for glueing c++ class objects to cells inside physicell and some example code for how to implement it.

These are files that can be added to PhysiCell http://physicell.org/ (or https://github.com/MathCancer/PhysiCell ) that let you use an addon wrapper and addon factory. 

This is mostly a development tool since many of the use cases can be created using already existing physicell features. 

The addon wrapper and factory allow you to attach arbitrarilly many custom classes to cells with relatively light overhead in a (mostly) thread safe way. You can even detach them during a simulation (but you shouldn't).

The largest component of the addon wrapper overhead is that the addon wrapper can be easily set to watch for PhysiCell events ie. are the cells still in the domain, did they produce daughters, etc. I'll include a daughter tracker addon here as soon as I have a bit more time to debug it. By default it checks domain, and live/dead.

This is mostly a tool for my own use to build modules that may either become XML custom_variables or part of an inherited agent, but I will be releasing a few addons that I am using for my PhD research in this style since it makes development/debugging of independant simulation components much faster and easier for me.

Also, calling clean_up_Addons(); in main.cpp will take care of the garbage collection of these heap allocated custom classes.
