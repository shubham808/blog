---
layout: post
title: Some more ideas around shogun
description:
modified:
category: GSOC'18
tags: []
comments: true
imagefeature:
share: true
---


### Overview:
This is a collection of some spin-off stories we worked on during the project. They are some nice future ideas and other cool stuff we played with and would like to realize into something better.

### Iterative Machine automated tests:
A simple and elegant way to test Iterative Machines is by using 3 reference models. We train the first model till say 5 iterations, the second model till 10 iterations, the third model is to be trained till 10 iterations but is prematurely stopped at 5. The pre-trained model must produce same result as the first reference model. We then call continue_train till the model completes training at 10 iterations. The result must be same as the second reference model.
To automate these tests, we will create a ```generator``` python file which will collect all ```CIterativeMachine``` and create various ```type lists```. These can be used to write ```TYPED_TESTS``` which will implement the above idea.
##### Future work:
- Using an AST parser or something similar to generate graphs that provide more information about inheritance
- Choosing dataset such that the tests can remain *general* for all machines that need it.

One test of similar approach is available in ```Perceptron_unittest.cc```
### Put and Observe:
The Parameter Observer Framework is a way to *observe* how the various members change during training. To implement this in an algorithm we would need to call the ```observe``` method periodically. The new idea was to use ```put``` api to update member variables state and then observe whenever some variable is being ```put```. Basically, we will try to add a new ```put_observe``` api that uses the orignal api and also calls ```observe``` to update the parameter observers. From an algorithms view, this means we will not update member variables directly (using equals ```=```) instead we will replace the updates with a call to ```put_observe```. We wrote a benchmark to quantify the overhead using put generates with and without the observer.
##### Future work:
- Since the ```Any``` api is now better we do can use it in the ```ObservedValue``` class for some runtime magic and type deductions.
- Prototyping ```put_observe```.

### Neural Networks:
We added a new dataset based on mnist images of numbers ```0, 1, 2``` along with a factory for neural networks. The model trains nicely however using factory does not allow us to ```connect``` neural layers in a custom manner.
##### Future work: 
- A work around for custom connect of layers. This work  around will also be very helpful in porting more meta examples like ```featureblock_logistic_regression``` etc.
- Remove ```NeuralNets.i``` from swig.

### NewtonSVM Refactoring:
This was a big spin-off. ```CNewtonSVM``` was implemented in an obsolete manner and we wrote it all over again making it new and shinier. This included using ```linalg``` for most operations, removing the use of raw pointers and using ```SGVector```, ```SGMatrix``` instead, and also implementing our ```CIterativeMachine``` code style here. The class is now a lot more readable along with being a classic example for how ```CIterativeMachine``` works.
We also added pseudo inverse of matrices(seperate self-adjoing and general implementions) to complete the refactor.
##### Future works:
- Enabling the use of ```svd_bdc``` in ```linalg::pinv``` for faster singular value decomposition, also providing api to get *all* decomposed matrices.
- Efficient memory allocation by making more data members.
- Refactoring more such classes 
