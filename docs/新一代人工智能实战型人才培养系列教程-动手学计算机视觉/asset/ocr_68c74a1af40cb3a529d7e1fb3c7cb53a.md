```
for i in range(W-w+1):
    for j in range(H-h+1):
        res[i,j] = np.sum(temp*img[j:j+h, i:i+w])
        # 在这里进行归一化操作
        if normalize:
            res[i,j] = res[i,j] / t / np.sqrt(np.sum(img[j:j+h, i:i+w]**2))
    return res

img = cv2.imread('lena_tri.jpg')
template = cv2.imread('lena_small.png')

test = temp_match_single(img, template)

test.match()
```

# Matching Result by CCORR

![](bbox=[0.273, 0.371, 0.748, 0.642])

由结果可见，通过归一化，消除了输入图像子图整体亮度对互相关计算的影响，提升了互相关度量的鲁棒性。

# 4.3 多目标模板匹配

与单目标模板匹配不同的是，在多目标匹配任务中，需要设定一个阈值。当模板与某个输入图像子图的相似度大于该阈值时，即可认为该输入图像子图即为需要匹配的对象。

这里，我们需要对阈值的选择进行说明。如果阈值设定过小，那么图像中会有很多子图被认为和模板匹配，造成很多虚假匹配；而一旦阈值设定过大，图像中也许没有任何子图可以和模板匹配。因此，在多目标模板匹配中，需要选择一个合适的阈值。设定需要匹配的子图个数 k，并将阈值设定为第 k+1 大的相似度的值。