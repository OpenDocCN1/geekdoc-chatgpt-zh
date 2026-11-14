```
C:\Users\Administrator\Desktop\data\mysamples_1204. vec
```

# 5 ) 训练 cascade 分类器

生成用于训练的 vec 文件后,就可以开始 cascade 分类器训练。首先查看分类器训练工具 opencv_traincascade 的参数和用法：

```
-data < cascade_dir_name >
-vec < vec_file_name > 刚刚创建的 vec 文件
-bg < background_file_name >
    [-numPos < number_of_positive_samples = 2000 > ]
    [-numNeg < number_of_negative_samples = 1000 > ]
    [-numStages < number_of_stages = 20 > ]
    [-precalcValBufSize < precalculated_vals_buffer_size_in_Mb = 1024 > ]
    [-precalcIdxBufSize < precalculated_idxs_buffer_size_in_Mb = 1024 > ]
    [-baseFormatSave]
    [-numThreads < max_number_of_threads = 9 > ]
    [-acceptanceRatioBreakValue < value > = -1 > ]
--cascadeParams--
    [-stageType < BOOST( default ) > ]
    [-featureType < | HAAR( default ) , LBP, HOG| > ]
    [-w < sampleWidth = 24 > ]
    [-h < sampleHeight = 24 > ]
--boostParams--
    [-bt < | DAB, RAB, LB, GAB( default ) | > ]
    [-minHitRate < min_hit_rate > = 0.995 > ]
    [-maxFalseAlarmRate < max_false_alarm_rate > = 0.5 > ]
    [-weightTrimRate < weight_trim_rate = 0.95 > ]
    [-maxDepth < max_depth_of_weak_tree = 1 > ]
    [-maxWeakCount < max_weak_tree_count = 100 > ]
--haarFeatureParams--
    [-mode < BASIC( default ) | CORE | ALL
--lbpFeatureParams--
--HOGFeatureParams--
```

opencv_traincascade . exe 工具参数设置和使用方法说明如下：

+   -data 指定生成的文件目录；