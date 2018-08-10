---
layout: post
title: Peer Review Continuous Detoxification
description:
modified:
category: GSOC'18
tags: []
comments: true
imagefeature:
share: true
featured: true
---

## Peer Review  
This is my review of the work wuwei has done during the summer and his blog post. I have found his work very interesting and there are a few things we can talk about.  

## Main ideas 
 
The Transformers API is nicely implemented. The old converter and [preprocessor](http://www.shogun-toolbox.org/api/6.0.0/classshogun_1_1CPreprocessor.html) API was essentially doing the same things hence a unified class was needed. ```.fit``` and ```.transform``` are simple to use from swig too [here](https://github.com/shogun-toolbox/shogun/blob/41888fe7c8dc3797063d674f452e13351f321338/examples/meta/src/converter/independent_component_analysis_fast.sg#L14). I agree the ref-ing is sometimes a lot of trouble to deal with (for example [here](https://github.com/shogun-toolbox/shogun/pull/4285/files#diff-90ffa0d34a7969080b14e84afd82eae7L58)), hence this work really shines in its simplicity inspite being huge.  

The pipeline API can be used to chain transformers and machines together as shown [here](https://github.com/vinx13/shogun/blob/56acfd9bb58ccd38b7f3ce8f177de042dfa056ca/examples/meta/src/pipeline/pipeline.sg#L24). Because something like  
```Python
p.over("submean", transformer("PruneVarSubMean")).then("kmeans", machine("KMeans"))
```  
is possible in Python the API is a lot similar to that of [sklearn](http://scikit-learn.org/stable/modules/generated/sklearn.pipeline.Pipeline.html). I really liked this. Also the keywords ```over``` and ```then``` make the fact that pipeline will need a pair of transformer and machine. Overall it is definitely user friendly.  
The pipeline is easy to use with cross validation [here](https://github.com/shogun-toolbox/shogun/blob/5bd958ca3accdd6e21da2fb10b06566c097addc5/examples/meta/src/evaluation/cross_validation_pipeline.sg#L25) too.  

The custom exceptions that were introduced will lead to some creative error handling in shogun. The idea of seperating ```logical_errors``` with ```out_of_range``` exceptions is an informative and clean. The idea is simply to throw a std exception instead of always using ```ShogunException``` with the help of two macros ```SG_THROW``` and ```REQUIRE_E```. I think we can write about using these exceptions all over shogun as a future work somewhere because this will make a nice task for contributors.
  
The view template for feature and labels is a nice step towards immutable features. Making a shadow copy of the features ensures features are not modified. The best thing is they are ```const``` during view [here](https://github.com/shogun-toolbox/shogun/pull/4352/files#diff-1c85d9d179a2ac86ac9808c1e1ea342eR25). I also liked returning ```Some``` as subset features. No one likes dealing with refs/ unrefs. The ```duplicate``` method [here](https://github.com/shogun-toolbox/shogun/pull/4352/files#diff-d635d9223d8ee233fa41029992b2d832R137) is not causing data copying overhead. Also it is more clear how to use this since ```add_subset``` and then ```remove_subset``` can be complicated to work with.
  
Untemplated linalg has a lot of ideas I found very interesting. The idea behind lazy evaluation is to deal with types at runtime. An untemplated Vector and Matrix which can be converted to templated instances of SGVector and SGMatrix using an operator. I like how the code for basic things like dot, add, multiply is already there demonstrating the idea's feasibility. I had some trouble about how ```Exp``` came into the picture [here](http://wuwei.io/post/2018/06/lazy-evaluation-with-expression-templates-1/) some hints for that will make it more clearer. I found it a bit difficult to diffrentiate between implicit and explicit evaluations of expressions ([here](https://github.com/vinx13/shogun-untemplated-demo/blob/162a9aceb28d231580f70e2cb1f8c7b45b073894/demo.cpp#L32) for example).   
This is where the lazy evaluation truly shines in the sense that the expression is not evaluated until it is "needed" to be assigned to a Vector. I think the more we eval implicitly the better a few thoughts about that will be helpful. The recursive simplicity of eval method is very intuitive. Solve lhs, solve rhs, apply the operator. [An explicit eval here](https://github.com/vinx13/shogun-untemplated-demo/blob/162a9aceb28d231580f70e2cb1f8c7b45b073894/demo.cpp#L39) is however a price we pay for being lazy it seems. 
  
Maybe we can add a few words about future works and ideas to the [blog](http://wuwei.io/post/2018/08/gsoc18-final-review/). This will help pick things up later.  Ideas like  meta example  for inverse transform API, using custom exceptions shogun-wide, or the systematic tests with all features and labels view can be a few good candidates candidates.
  

## Conclusion

I have enjoyed working at shogun with you this summer. I appreciate the complexity of work that has been done over the summer. Your work during the project has kept me excited and helped me work more efficiently. I look forward to writing more code together.
