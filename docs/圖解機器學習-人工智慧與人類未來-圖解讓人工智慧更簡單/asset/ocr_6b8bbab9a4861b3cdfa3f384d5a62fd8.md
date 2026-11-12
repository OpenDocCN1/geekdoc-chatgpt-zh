# # 附錄二 A Fast Training Algorithm for Multi-Layer Neural Network based on Extended Kalman Filter Approach

# ### TRAINING MULTILAYER PERCEPTRONS WITH THE EXTENDED KALMAN ALGORITHM

Sharad Singhal and Lance Wu
Bell Communications Research, Inc.
Morristown, NJ 07960

# ## ABSTRACT

A large fraction of recent work in artificial neural nets uses multilayer perceptrons trained with the back-propagation algorithm described by Rumelhart et. al. This algorithm converges slowly for large or complex problems such as speech recognition, where thousands of iterations may be needed for convergence even with small data sets. In this paper, we show that training multilayer perceptrons is an identification problem for a nonlinear dynamic system which can be solved using the Extended Kalman Algorithm. Although computationally complex, the Kalman algorithm usually converges in a few iterations. We describe the algorithm and compare it with back-propagation using two-dimensional examples.

# ## INTRODUCTION

Multilayer perceptrons are one of the most popular artificial neural net structures being used today. In most applications, the “back propagation” algorithm [Rumelhart et al, 1986] is used to train these networks. Although this algorithm works well for small nets or simple problems, convergence is poor if the problem becomes complex or the number of nodes in the network become large [Waibel et al, 1987]. In problems such as speech recognition, tens of thousands of iterations may be required for convergence even with relatively small data-sets. Thus there is much interest [Prager and Fallside, 1988; Irie and Miyake, 1988] in other “training algorithms” which can compute the parameters faster than back-propagation and/or can handle much more complex problems.

In this paper, we show that training multilayer perceptrons can be viewed as an identification problem for a nonlinear dynamic system. For linear dynamic