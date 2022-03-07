# Isolation Forest Anomaly Detection - Identify Outliers

![Introduction](https://raw.githubusercontent.com/youngdataspace/K-Means-Clustering-Fixed-Effects/master/Introduction.gif)

## Outline
In this notebook, I explain and implement Isolation Forest. My goal is to explain using plain English so that non-technical readers can understand. This notebook includes the following topics:
- Why and how to look for outliers.
- How Isolation Forest works.
- The benefits and drawbacks of Isolation Forest.
- The implementation of Isolation Forest.

If you have any comments or suggestions, email me at y.s.yoon@berkeley.edu.

Let's get started!

## Introduction
In <a href="https://github.com/youngdataspace/treat-outliers/blob/main/detect_outliers.ipynb">this notebook</a>, I detect outliers using the Isolation Forest method. I use US public firm data, which are also used in my UC Berkeley Haas PhD Dissertation (<a href="https://papers.ssrn.com/sol3/papers.cfm?abstract_id=3689446">Yoon 2022</a>). Although I detect outliers to treat them before I conduct the analysis on the data, the anomaly detection technique can be applied to many business settings, such as detecting fraudulent credit card spending.

Figure 1 shows US public firms' characteristics shown in 2-dimensions. The goal of this notebook is to detect outliers, as shown in red in Figure 2.

## Why and how to look for outliers
Many machine learning algorithms and regression models are susceptible to outliers. An outlier is a data point that significantly deviates from other points. Unless they are properly taken care of, the inferences obtained from statistical models conducted on the data may not be useful.

There are many popular methods to detect outliers, namely, the Z-Score and Interquartile Range methods. These methods are effective when the underlying data follows a normal distribution (a distribution where most data points are closer to the mean and become less frequent as the distance to the mean increases). However, if the data is not normally distributed, then these methods may incorrectly classify normal observations as outliers. On the other hand, Isolation Forest is non-parametic, which simply means that we don't have to make assumptions about how the underlying data is distributed.

Furthermore, the Z-Score and Interquartile Range methods identify at the variable level. If you have reason to believe that multiple variables interact with each other and create outliers, these methods will not be able to detect those outliers. For example, an SAT score of 1350/1600 (90th percentile) does not seem to be an outlier by itself. However, if we introduce another dimension, age, and find that a 12-year-old got 1350/1600, then this observation is likely an outlier for a sub-sample of 12-year-olds. Unlike single-variable outlier detection methods, Isolation Forest detects outliers at multidimensional space.

## Isolation Forest
Isolation Forest is a tree ensemble method of detecting anomalies first proposed by <a href="https://ieeexplore.ieee.org/abstract/document/4781136?casa_token=A5ZM3TQZHhsAAAAA:DPITalJ8ZZ-5KnuBufXLZkFg6fsICEyyi0vfXmuGejd8gFtAldJ2ZFuS0JUoBAS8GPoF0JG5Kg">Liu, Ting, and Zhou (2008)</a>. Unlike other methods that first try to understand the normal points and classify abnormal points as anomalies, Isolation Forest explicitly isolates anomalies.

Anomalies have two characteristics. They are distanced from normal points and there are only a few of them. The Isolation Forest algorithm exploits these two characteristics. 

#### Plain English
Isolation Forest randomly cuts a given sample until a point is isolated. The intuition is that outliers are relatively easy to isolate. Take a look at the following GIF.

It took 3 times to randomly cut the sample and isolate the red point, which is clearly an outlier.

Now, take a look at the next GIF, which attempts to cut the sample until the blue point (normal point) is isolated.

This time, the algorithm took 15 cuts.

As you can infer from the above, a data point is likely an outlier if it can be isolated only with a few random sample cuts. 

#### Step by Step
Here are the steps involving the Random Forest algorithm.

First, the algorithm creates an isolation tree by going through the following steps:<br>
[1] Randomly select a sub-sample (Sci-kit learn default: 100 instances/data points)
[1] Select a point to isolate.<br>
[2] Randomly select a feature (i.e., variable) from the set of features X.<br>
[3] Randomly select a threshold between the minimum and the maximum value of the feature x.<br>
[4] If the data point is less (greater) than the threshold, then it flows through the left branch of the tree (right). In other words, define the new minimum (maximum) of the range to the threshold for the next iteration.<br>
[5] Repeat steps 2 through 4 until the point is isolated or until a pre-defined max number of iterations is reached.
[6] Record the number of times the steps 2 through 4 were repeated.

Prediction process: Isolation Forest is created by computing the following score based on a collection trees (like 100 trees).

![image](https://user-images.githubusercontent.com/99772400/157103276-c0c2f9fa-1d35-4584-a41d-80e6f5c45f77.png)

where E[h(x)] is the average number of successful iterations for instance x and c(n) is the average iterations for uncussessful iterations.

## Benefits and drawbacks of using Isolation Forest
#### Benefits
As I noted above, Isolation Forest does not assume normal distribution and is able to detect outliers at a multidimentional level. More importantly, Isolation Forest 

#### Costs
As I will discuss more in the implementation step, the Isolation Forest algorithm requires us to pick the percentage of anomalies in the dataset. Thus, we need to have some idea of this. 

Second, axis-parallel splits create some artificial normal regions. I won't go into details but this issue is addressed by the follow up study <a href="https://ieeexplore.ieee.org/document/8888179">Hariri, Kind, and Brunner (2021)</a>. And here are more resources: <a href="https://github.com/sahandha/eif">GitHub</a> and <a href="https://medium.datadriveninvestor.com/lets-find-some-outliers-with-isolation-forest-4ed22175a8d3">blog</a>. I will post notebook on this topic when I get a chance.

## 필요없는 ##
https://towardsdatascience.com/multi-variate-outlier-detection-in-python-e900a338da10
https://medium.com/codex/isolation-forest-outlier-detection-simplified-5d938548bb5c

## References
https://ieeexplore.ieee.org/abstract/document/4781136?casa_token=A5ZM3TQZHhsAAAAA:DPITalJ8ZZ-5KnuBufXLZkFg6fsICEyyi0vfXmuGejd8gFtAldJ2ZFuS0JUoBAS8GPoF0JG5Kg

https://quantdare.com/isolation-forest-algorithm/

https://towardsdatascience.com/outlier-detection-with-isolation-forest-3d190448d45e

https://www.analyticsvidhya.com/blog/2021/07/anomaly-detection-using-isolation-forest-a-complete-guide/

## References
[1] <a href="https://ieeexplore.ieee.org/abstract/document/4781136?casa_token=A5ZM3TQZHhsAAAAA:DPITalJ8ZZ-5KnuBufXLZkFg6fsICEyyi0vfXmuGejd8gFtAldJ2ZFuS0JUoBAS8GPoF0JG5Kg">Liu, Ting, and Zhou (2008) Isolation Forest</a><br>
[2] <a href="https://quantdare.com/isolation-forest-algorithm/">Fuertes (2018) Isolation forest: the art of cutting off from the world</a><br>
[3] <a href="https://towardsdatascience.com/outlier-detection-with-isolation-forest-3d190448d45e">Lewinson (2018) Outlier Detection with Isolation Forest</a><br>
[4] <a href="https://www.analyticsvidhya.com/blog/2021/07/anomaly-detection-using-isolation-forest-a-complete-guide/">Akshara (2021) Anomaly detection using Isolation Forest - A Complete Guide</a>

