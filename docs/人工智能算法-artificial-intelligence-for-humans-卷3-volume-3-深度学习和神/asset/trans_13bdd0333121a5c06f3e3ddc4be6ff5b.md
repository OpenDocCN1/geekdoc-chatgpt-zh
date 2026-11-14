
We can use ensembles to combine multiple models into a single model. Typically, the resulting ensemble model will score better than any of the individual ensemble methods. We also looked at how to bag 10 neural networks together and generate a Kaggle-acceptable CSV file.

Having analyzed neural networks and deep learning, we hope you have gained useful new knowledge. If you have any comments on this book, please write and let us know. In the future, we plan to write other volumes in this book series to include more techniques. Therefore, we are interested in knowing which techniques you would like us to explore in future volumes. You can contact us at the following website:

```
http://www.jeffheaton.com
```

# Appendix A: Example Code Usage Guide

## A.1 Series Introduction

These example codes are all part of a book series still in writing. You can visit the URL given in the book's introduction to follow the writing and publishing status of the series. This book series includes the following volumes:

*   Volume 0: AI Mathematical Foundations;
*   Volume 1: Fundamental Algorithms;
*   Volume 2: Nature-Inspired Algorithms;
*   Volume 3: Deep Learning and Neural Networks.

## A.2 Staying Updated

This appendix describes how to obtain the example code for this book series.

This is likely the fastest-changing part of the entire book series. Programming languages are constantly changing and releasing new versions. I will update these codes in a timely manner and fix known issues. Therefore, it is best to use the latest version of the example code.

Because the example code updates frequently, providing it as files would quickly become outdated. It is recommended that you go to the following URL to download the latest version of the files:

```
https://github.com/jeffheaton/aifh
```

## A.3 Obtaining Example Code

The example code for this book provides implementations in multiple programming languages, and most volumes' main code packages include `Java`, `C#`, `C/C++`, `Python`, and `R` language forms. At the time of Volume 2's release, it included `Java`, `C#`, `Python`, and `Scala`. Since the book's release, versions in other languages may have been added. The community may also contribute implementations in other languages. All example code can be found in the following GitHub open-source repository:

```
https://github.com/jeffheaton/aifh
```

After entering the repository, there are two different methods to download the example code.

### A.3.1 Download Compressed File

![Image 650](Images/15.png)

GitHub has an icon, as shown in Figure A-1, to download a `ZIP` compressed file containing all example code for this book series—one compressed file contains all the code. Because the file content changes rapidly, it is best to download the latest version of the file before reading each volume. To download, visit the following URL:

```
https://github.com/jeffheaton/aifh
```

You will see the download link shown in Figure A-1.

### A.3.2 Clone Git Repository

![Image 650](Images/15.png)

If you have the version control software `Git` installed on your computer, all example code can be obtained via `Git`. The following command will clone the example code locally. (So-called "cloning" is actually the process of copying and transferring the entire repository files.)

```
git clone https://github.com/jeffheaton/aifh.git
```

You can also pull the latest updates with the following command:

```
git pull
```

If you need a `Git` guide, you can visit the official `Git` website.

![Image 472](Images/251.jpg)

Figure A-1 GitHub Corresponding Code Repository Page

## A.4 Example Code Contents

If you obtain the example code by downloading the file, all example code for this book series is in one compressed file.

Opening the file reveals the contents shown in Figure A-2.

![Image 471](Images/252.jpg)

Figure A-2 Downloaded Example Code Files

The `LICENSE.txt` file contains information about the open-source license used for the example code. All example code in this book series is released under the open-source license `Apache 2.0`. This is a free and open-source software license. This license means I do not retain copyright to these files, and you can also use the files in commercial projects without obtaining further permission.

The book source code is free to obtain, but the book content is not. These books are products I sell in various forms. Although I release them in DRM-free (`Digital Rights Management`, `DRM`) formats, you have no right to redistribute the specific book content, whether in `PDF`, `MOBI`, `EPUB`, or any other format. Your support is my greatest motivation and the guarantee for the successful completion of this book series.

The downloaded files contain two `README` files^q[^(\[1\])](#appendixA.html#anchorfla1). `README.md` is a `Markdown` file containing images and formatted text, while `README.txt` is a plain text file; both contain similar information. To learn more about `Markdown` files, visit the following URL:

```
https://help.github.com/articles/github-flavored-markdown
```

In the downloaded example code files, you can see `README` files in several folders. The `README` file in the top-level folder contains information about this book series.

You can also see that the files contain separate folders for each volume, named `vol1`, `vol2`, etc. You may not see all volume folders because the entire series is not yet finished. Each volume folder has the same structure. If you open the folder corresponding to Volume 1, you will see the contents shown in Figure A-3.

![Image 470](Images/253.jpg)

Figure A-3 Volume 1 Corresponding Folder Contents

In this folder, you can see two `README` files containing information about this volume. The most important information in the `README` file is the current status of the example code. Because the community often submits example code, some example code may not be complete. The `README` file corresponding to that volume provides this important information. In addition, each volume's `README` file contains the errata and FAQ for that volume.

You should also see a file named `chart.R`, which contains the source code I used to create many of the charts in this book. I used the `R` language to create almost all the charts in this book. This file allows readers to see the formulas behind the charts. Since this `R` code was only used for my writing process, I did not convert this file to other languages. If I used another programming language, such as `Python`, to create charts, you would see a file named `chart.py` containing the corresponding `Python` code.

You can also see that Volume 1 contains example code written in `C`, `C#`, `Java`, `Python`, and `R`, among other languages. These are the primary languages I strive to provide complete code for, but you can also see other languages added later. Once again, be sure to check the `README` file for the latest information on language ports.

Figure A-4 shows the contents of a typical language package.

![Image 469](Images/254.jpg)

Figure A-4 Java Language Package

Note the `README` file. The `README` files in each language folder are very important. The `README` file in Figure A-4 contains guidelines for using the example code in a `Java` environment. If you encounter problems using the book's example code in a certain language, you should first check the `README` file. The other files in Figure A-4 are unique to the `Java` folder, and the `README` file provides more relevant details.

## A.5 How to Contribute to the Project

Would you like to port the example code to another new language? Did you find any omissions, typos, or other issues? I suspect there might be. Now, simply by forking a branch based on this project and pushing commits on GitHub, you can become a member of this growing community of project collaborators.

The whole process begins with a "fork" operation. You create a GitHub account and fork the AIFH project, creating a new project that is essentially a copy of the AIFH project. Then clone your new project in a manner similar to cloning the main AIFH project. After making changes to the new project, you can submit a "pull request". Upon receiving your request, I will review your changes or additions and merge them into the main project.

For more detailed information on collaborating on GitHub, please see the following URL:

```
https://help.github.com/articles/fork-a-repo
```

---

[\[1\]](#appendixA.html#acfla1) Actually, now there is only the `README.md` file left.—Translator's Note

# References

Here are listed references relevant to the content of this book.

[1] Ackley H, Hinton E, Sejnowski J. A learning algorithm for Boltzmann machines[J]. Cognitive Science, 1985: 147-169.

[2] Bergstra J, Breuleux O, Bastien F, et al. Theano: a CPU and GPU math expression compiler[C]// Proceedings of the python for scientific computing conference, 2010: 18-24.

[3] Broomhead D, Lowe D. Multivariable functional interpolation and adaptive networks[J]. Complex Systems, 1988, 2(3), 321-355.

[4] Chung J, Gulcehre C, Cho K, et al. Empirical evaluation of gated recurrent neural networks on sequence modeling[J]. arXiv, 2014.

[5] Elman J L. Finding structure in time[J]. Cognitive Science, 1990, 14 (2): 179-211.

[6] Fukushima K. Neocognitron: A self-organizing neural network model for a mechanism of pattern recognition unaffected by shift in position[J]. Biological Cybernetics, 1980, 36: 193-202.

[7] Garey M R, Johnson D S. Computers and intractability; a guide to the theory of np-completeness[M]. New York: W. H. Freeman & Co, 1990.

[8] Glorot X, Bordes A, Bengio Y. Deep sparse rectifier neural networks[J]. Journal of Machine Learning Research, 2011, 15: 315-323.

[9] Hebb D. The organization of behavior: a neuropsychological theory[M]. Mahwah N.J.: L. Erlbaum Associates, 2002.

[10] Hinton G E, Srivastava N, Krizhevsky A, et al. Improving neural networks by preventing co-adaptation of feature detectors[J]. Computer Science, 2012, 3(4): 212-223.

[11] Anderson J A, Rosenfeld E. Neurocomputing: Foundations of research[M]. Cambridge, MA: MIT Press, 1988.

[12] Hopfield J J, Tank D W. "Neural" computation of decisions in optimization problems[J]. Biological Cybernetics, 1985, 52: 141-152.

[13] Hornik K. Approximation capabilities of multilayer feedforward networks[J]. Neural Networks, 1991, 4 (2), 251-257.

[14] Jacobs R A. Increased rates of convergence through learning rate adaptation[J]. Neural Networks, 1988, 1 (4), 295-307.

[15] Jacobs R, Jordan M. Learning piecewise control strategies in a modular neural network architecture[J]. IEEE Transactions on Systems, Man and Cybernetics, 1993, 23 (2), 337-345.

[16] Jordan M I. Serial order: A parallel distributed processing approach[J]. Institute for Cognitive Science Report, University of California, 1986: 8604.

[17] Kalman B, Kwasny S. Why TANH: choosing a sigmoidal function[C]// International Joint Conference on Neural Networks. 1992, 4: 571-581.

[18] Kamiyama N, Iijima N, Taguchi A, et al. Tuning of learning rate and momentum on back-propagation[C]// ICCS.ISITA'92 Communications on the move, 1992, 2, 528-532.

[19] Keogh E, Chu S, Hart D, et al. Segmenting time series: A survey and novel approach[M]// Data mining in time series databases. World Scientific Publishing Company, 1993: 1-22.

[20] Krizhevsky A, Sutskever I, Hinton G E. Imagenet classification with deep convolutional neural networks[J]. Advances in neural information processing systems, 2012, 25(2).

[21] LeCun Y, Bottou L, Bengio Y, et al. Gradient-based learning applied to document recognition[J]. Proceedings of the IEEE, 1998, 86(11): 2278-2324.

[22] Maas A L, Hannun A Y, Ng A Y. Rectifier nonlinearities improve neural network acoustic models[C]// International conference on machine learning. 2013.

[23] Van der Maaten L, Hinton G. Visualizing high-dimensional data using t-SNE[J]. Journal of Machine Learning Research, 2008, 9(2), 2579-2605.

[24] Marquardt D. An algorithm for least-squares estimation of nonlinear parameters[J]. SIAM Journal on Applied Mathematics, 1963, 11(2), 431-441.

[25] Matviykiv O, Faitas O. Data classification of spectrum analysis using neural network. Lviv Polytechnic National University, 2012.

[26] McCulloch W, Pitts W. A logical calculus of the ideas immanent in nervous activity[J]. Bulletin of Mathematical Biology, 1943, 5 (4), 115-133.

[27] Mozer M C. Backpropagation[M]. Hillsdale, NJ, USA: L. Erlbaum Associates Inc, 1995.

[28] Nesterov, Y. Introductory lectures on convex optimization: a basic course[M]. Kluwer Academic Publishers, 2004.

[29] Ng A Y. Feature selection, l1 vs. l2 regularization, and rotational invariance[C]// The Twenty First International Conference on Machine Learning. New York, NY, USA: ACM, 2004.

[30] Neal R M. Connectionist learning of belief networks[J]. Artificial Intelligence, 1992, 56 (1): 71-113.
