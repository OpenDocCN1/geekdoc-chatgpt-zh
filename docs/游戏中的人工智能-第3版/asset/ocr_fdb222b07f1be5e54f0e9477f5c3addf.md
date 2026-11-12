然后，再次将范围(0, 1)按 \( n^2 = 9 \) 拆分出 \( \frac{1}{9} \) 部分，并再次按特定的顺序使用，那么到目前为止，生成的序列是：

\( \frac{1}{3}, \frac{2}{3}, \frac{1}{9}, \frac{4}{9}, \frac{7}{9}, \frac{2}{9}, \frac{5}{9}, \frac{8}{9} \)

你可能会奇怪，这个序列中并没有 \( \frac{3}{9} \) 和 \( \frac{6}{9} \)。注意，拆分的数字放在序列中有一个前提条件，那就是之前的序列中未返回过该值。\( \frac{3}{9} = \frac{1}{3} \)，\( \frac{6}{9} = \frac{2}{3} \)，它们在之前的序列中已经出现了，所以就不能再出现了。

有些心思缜密的人可能会想到另外一个问题：为什么该序列在 \( \frac{1}{9} \) 之后就是 \( \frac{4}{9} \) 呢？难道不应该是 \( \frac{2}{9} \) 吗？

请注意，该序列是按 \( \frac{1}{3} \) 递增的。

也就是说，对于 \( \frac{k}{9} \) 部分而言，其顺序从 \( \frac{1}{9} \) 开始，然后递增 \( \frac{1}{3} \)，得到 \( \frac{4}{9} \)，再递增 \( \frac{1}{3} \)，得到 \( \frac{7}{9} \)，再递增 \( \frac{1}{3} \)，得到 \( \frac{10}{9} \)，但是该值不在 (0, 1) 范围之内，所以舍弃，然后再从 \( \frac{2}{9} \) 开始，以此类推。

使用控制数字 2 和 3 的二维序列开始部分如下所示：

\( \left( \frac{1}{2}, \frac{1}{3} \right), \left( \frac{1}{2}, \frac{2}{3} \right), \left( \frac{3}{4}, \frac{1}{9} \right), \left( \frac{1}{4}, \frac{4}{9} \right), \left( \frac{5}{8}, \frac{7}{9} \right), \left( \frac{3}{8}, \frac{2}{9} \right), \left( \frac{7}{8}, \frac{5}{9} \right) \)

该序列的实现比解释它更容易。其一维实现的伪代码如下：

```
function haltonSequenceId(base, index):
    result = 0
    denominator = 1
    while index > 0:
        denominator *= base
        result += (index % base) / denominator
        index = floor(index / base)
    return result
```