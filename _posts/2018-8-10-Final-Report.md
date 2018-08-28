---
layout: post
title: Final Report Inside the Black Box
description:
modified:
category: GSOC'18
tags: []
comments: true
imagefeature: fhs.png
share: true
featured: true
---

## Overview
**Name**: Shubham Shukla  
**Project**: Inside The Black Box  
**Mentors**: Heiko Strathmann, Giovanni De Toni  
**Organization**: Shogun Machine Learning Toolbox   
###  Abstract
Shogun is a large scale machine learning toolkit developed by many diffrent minds and ideas. This means we have a lot of opportunities to optimize what goes on under the hood and create something simple and impactful. This project focuses a bit more on Iterative Algorithms among other things. The premature stopping framework was improved to make it more robust and natural to use and modify, the progress bar was improved to make it more verbose along with implementing it in some iterative algorithms, We also worked on making algorithms respect the provided feature types making them fully templated for a more generic behaviour.

### Table of Contents
* [StoppableSGObject class and progress bar](#stoppablesgobject-class-and-progress-bar)
* [Iterative Machine](#iterative-machine)
* [Feature type dispatching and generic nature](#feature-type-dispatching-and-generic-nature)
* [Other Contributions and Ideas](#other-contributions-and-ideas)

### StoppableSGObject class and progress bar
In shogun we have a ```SignalHandler``` that gives some control of what happens in case of an event, like premature cancellation (```CTRL+C```), back to the user for some algorithms. We take all the premature stopping code and make it accessible to non ```CMachine``` types as well by placing it in a new ```CStoppableSGObject``` class. My mentor also introduced ```m_callback``` data member which can accept a lambda function that can serve as a way to fire a cancel computation signal.  
##### Relevant PRs:
The class is implemented in [PR4280](https://github.com/shogun-toolbox/shogun/pull/4280). Other updates include 
* [#4286](https://github.com/shogun-toolbox/shogun/pull/4286): replacing cancel_computation calls with macro
* [#4287](https://github.com/shogun-toolbox/shogun/pull/4287): enable premature stopping in all machines
* [#4291](https://github.com/shogun-toolbox/shogun/pull/4291): use case for ```CStoppableSGObject``` class

##### Progress bar
We added a default prefix for ```class_name::method_name``` to the progress bar. This slightly changes the usage which is now done using ```SG_PROGRESS``` macro instead of ```progress``` method. We implemented it in most iterative algorithms. This helped us prepare a list of iterative algorithms  
**PR**: [#4305](https://github.com/shogun-toolbox/shogun/pull/4305): The new macro and its usage
**Future Work**: Finding more use cases for it and using it to extend the list of iterative algorithms.

### Iterative Machine
Previously an algorithm could define what happens when a training process is cancelled or paused with the help of methods like ```on_next```, ```on_pause``` etc. This is not flexible for a user behind an interface like shogun. We use the concept of *mixins* for the first time in shogun to write a new ```CIterativeMachine``` class which allows the user to cancel training anytime, execute some more code, and then resume it later if needed. The pre-trained model remains usable and concurrent. This is done by making sure the model updates its ```state``` in every iteration.  
##### Relevant PRs:
The mixin is implemented in [PR4335](https://github.com/shogun-toolbox/shogun/pull/4335). Related PRs are:
- [#4320](https://github.com/shogun-toolbox/shogun/pull/4320): update the ```state``` of Perceptron in every iteration
- [PR4335](https://github.com/shogun-toolbox/shogun/pull/4335): this also includes the implementation of ```CIterativeMachine``` in ```CPerceptron```
- [#4347](https://github.com/shogun-toolbox/shogun/pull/4347): ```CIterativeMachine``` in ```CNewtonSVM``` class.  

##### Future Work:
- Porting all algorithms from the [List of Iterative Algorithms](https://github.com/shogun-toolbox/shogun/wiki/List-of-iterative-algorithms) wiki page to this code style
- Systematic tests for all Iterative machines that ensure proper state update and concurrency.

### Feature type dispatching and generic nature
There is an implicit assumption in most algorithms that the provided feature type will be 64 bit dense. To introduce more generic behaviour in an automated way we have written some new classes. These are all *mixins* that use the curiously recursive template pattern. This means algorithms will inherit from themselves and the orignal base class. The concept is new to shogun and brings a lot more possibilities for similar ideas. The idea is to dispatch feature types from base class and then have subclasses implement a templated version of ```train_machine```.  

##### Relevant PRs: 
- [#4373](https://github.com/shogun-toolbox/shogun/pull/4373): This implements two mixin + crtp classes to dispatch dense and string feature types. We also implement dense featue types in ```CLDA```, and ```CLeastAngleRegression```. There are also unit tests for training a machine will a list of feature types.
- [#4389](https://github.com/shogun-toolbox/shogun/pull/4389): a meta example for dense dispatcher  
##### Future Work:
- Add more dispatchers with tests along with implementations.
- An automated way to create a new dispatcher class or maybe a workaround so that we don’t need to have as many dispatchers as the number of feature types.

### Other Contributions and Ideas:
##### Observer and put
Shogun has an API to set values of member parameters that are registered by algorithms. This API can be used together with parameter observers to record summaries of change in member variables. This can be done using a new ```put_observe``` method that will inform the parameter observers on anything that is being updated. This comes with a design change of using ```put``` to change member data in code instead of using assignment. To benchmark the overhead we will gain over direct assignment we have written a benchmark in [#4342](https://github.com/shogun-toolbox/shogun/pull/4342)  
**Future Work:** 
- Prototyping and benchmarking of ```put_observe```
- Infering type using ```Any``` instead of doing so in ```CObservedValue```

##### Systematic tests for Iterative machine
Writing seperate tests in multiple classes that aim to do the same thing is redundant. We can use ```TYPED_TESTS``` instead to do it for a number of classes. This has a few issues but will be possible in the future. An idea of how to do it is [#4327](https://github.com/shogun-toolbox/shogun/pull/4327) for serialization tests.  
**Future Work:**
- Using ```LibASTParser``` to provide more information on base classes
- Making a general dataset for proper testing
An idea of how these tests should look like is in ```Perceptron_unittest.cc``` 

##### NewtonSVM Refactoring
The implementation of ```CNewtonSVM``` was outdated. We refactored it to use ```SGVectors/SGMatrix``` for data storage and using ```linalg``` API for computations. We also ported it to the new IterativeMachine code style.  
**PRs**: We refactored most of the code in [#4347](https://github.com/shogun-toolbox/shogun/pull/4347). We added a new method to calculate pseudo inverse for matrices with two implementations in linalg [#4356](https://github.com/shogun-toolbox/shogun/pull/4356)

##### Meta examples and cookbook contributions
These are some contributions to meta examples and cookbooks

- **Neural Network Factory**: Adding the option to ```auto_initialize``` the neural network along with a new ```layer``` factory to create new layers. The example from python looks much more intuitive now.  
**PR**: [#4386](https://github.com/shogun-toolbox/shogun/pull/4386) contains the factory example along with a cookbook for training a Convolutional Neural Network on a dataset for mnist images of 0, 1, 2 in shogun. The corresponding dataset is [#165](https://github.com/shogun-toolbox/shogun-data/pull/165)
- [#4346](https://github.com/shogun-toolbox/shogun/pull/4346): NewtonSVM meta example
- [#4340](https://github.com/shogun-toolbox/shogun/pull/4340): Diffusion meta cookbook and example
- [#4310](https://github.com/shogun-toolbox/shogun/pull/4310): porting ```KRRNystrom``` and ```LeastAngleRegression``` to new API
- [#4297](https://github.com/shogun-toolbox/shogun/pull/4297): porting ```KernelRidgeRegression``` meta example to new API
- **Deleting CLabelsFactory**: The ```CLabelsFactory``` performed static casts which were not needed anymore so we deleted it and used ```as``` for conversions  
**PRs**: [#4281](https://github.com/shogun-toolbox/shogun/pull/4281), [#4277](https://github.com/shogun-toolbox/shogun/pull/4277)
- [#4278](https://github.com/shogun-toolbox/shogun/pull/4278): A few meta examples on using distance machines from factory
- [#4236](https://github.com/shogun-toolbox/shogun/pull/4236): factory methods in lda meta example

**Parallel computation of ```sample``` in CLogDetEstimator**: [#4235](https://github.com/shogun-toolbox/shogun/pull/4235) We used openMP to make the code parallel along with refactoring it for efficient memory usage
