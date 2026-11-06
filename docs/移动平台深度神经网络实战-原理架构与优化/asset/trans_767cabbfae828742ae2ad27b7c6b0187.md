

# 池化层实现

## 源代码

```
  1 #include "pooling.h"
  2 #include <float.h>
  3 #include <algorithm>
  4 
  5 DEFINE_LAYER_CREATOR(Pooling)
  6 
  7 Pooling::Pooling()
  8 {
  9     one_blob_only = true;
 10 }
 11 
 12 int32_t Pooling::load_param(const ParamDict& pd)
 13 {
 14     pooling_type = pd.get(0, 0);
 15     kernel_w = pd.get(1, 0);
 16     kernel_h = pd.get(11, kernel_w);
 17     stride_w = pd.get(2, 1);
 18     stride_h = pd.get(12, stride_w);
 19     pad_left = pd.get(3, 0);
 20     pad_right = pd.get(14, pad_left);
 21     pad_top = pd.get(13, pad_left);
 22     pad_bottom = pd.get(15, pad_top);
 23     global_pooling = pd.get(4, 0);
 24 
 25     return 0;
 26 }
 27 
 28 int32_t Pooling::forward(const Mat& bottom_blob, Mat& top_blob, const Option& opt) const
 29 {
 30     int32_t w = bottom_blob.w;
 31     int32_t h = bottom_blob.h;
 32     int32_t channels = bottom_blob.c;
 33     size_t elemsize = bottom_blob.elemsize;
 34 
 35     if (global_pooling)
 36     {
 37         top_blob.create(channels, elemsize);
 38         if (top_blob.empty())
 39             return -100;
 40 
 41         int32_t size = w * h;
 42 
 43         if (pooling_type == PoolMethod_MAX)
 44         {
 45             for (int32_t q=0; q<channels; q++)
 46             {
 47                 const float* ptr = bottom_blob.channel(q);
 48 
 49                 float max = ptr[0];
 50                 for (int32_t i=0; i<size; i++)
 51                 {
 52                     max = std::max(max, ptr[i]);
 53                 }
 54 
 55                 top_blob[q] = max;
 56             }
 57         }
 58         else if (pooling_type == PoolMethod_AVE)
 59         {
 60             for (int32_t q=0; q<channels; q++)
 61             {
 62                 const float* ptr = bottom_blob.channel(q);
 63 
 64                 float sum = 0.f;
 65                 for (int32_t i=0; i<size; i++)
 66                 {
 67                     sum += ptr[i];
 68                 }
 69 
 70                 top_blob[q] = sum / size;
 71             }
 72         }
 73 
 74         return 0;
 75     }
 76 
 77     Mat bottom_blob_bordered = bottom_blob;
 78 
 79     float pad_value = 0.f;
 80     if (pooling_type == PoolMethod_MAX)
 81     {
 82         pad_value = -FLT_MAX;
 83     }
 84     else if (pooling_type == PoolMethod_AVE)
 85     {
 86         pad_value = 0.f;
 87     }
 88 
 89     int32_t wtailpad = 0;
 90     int32_t htailpad = 0;
 91 
 92     int32_t wtail = (w + pad_left + pad_right - kernel_w) % stride_w;
 93     int32_t htail = (h + pad_top + pad_bottom - kernel_h) % stride_h;
 94 
 95     if (wtail != 0)
 96         wtailpad = stride_w - wtail;
 97     if (htail != 0)
 98         htailpad = stride_h - htail;
 99 
100     copy_make_border(bottom_blob, bottom_blob_bordered, pad_top, pad_bottom + htailpad, pad_left, pad_right + wtailpad, BORDER_CONSTANT, pad_value);
101     if (bottom_blob_bordered.empty())
102         return -100;
103 
104     w = bottom_blob_bordered.w;
105     h = bottom_blob_bordered.h;
106 
107     int32_t outw = (w - kernel_w) / stride_w + 1;
108     int32_t outh = (h - kernel_h) / stride_h + 1;
109 
110     top_blob.create(outw, outh, channels, elemsize);
111     if (top_blob.empty())
112         return -100;
113 
114     const int32_t maxk = kernel_w * kernel_h;
115 
116     // 计算池化核每个位置在待池化矩阵中相对于起始点的偏移
117     std::vector<int32_t> _space_ofs(maxk);
118     int32_t* space_ofs = &_space_ofs[0];
119     {
120         int32_t p1 = 0;
121         int32_t p2 = 0;
122         int32_t gap = w - kernel_w;
123         for (int32_t i = 0; i < kernel_h; i++)
124         {
125             for (int32_t j = 0; j < kernel_w; j++)
126             {
127                 space_ofs[p1] = p2;
128                 p1++;
129                 p2++;
130             }
131             p2 += gap;
132         }
133     }
134 
135     if (pooling_type == PoolMethod_MAX)
136     {
137         for (int32_t q=0; q<channels; q++)
138         {
139             const Mat m = bottom_blob_bordered.channel(q);
140             float* outptr = top_blob.channel(q);
141 
142             for (int32_t i = 0; i < outh; i++)
143             {
144                 for (int32_t j = 0; j < outw; j++)
145                 {
146                     const float* sptr = m.row(i*stride_h) + j*stride_w;
147 
148                     float max = sptr[0];
149 
150                     for (int32_t k = 0; k < maxk; k++)
151                     {
152                         float val = sptr[ space_ofs[k] ];
153                         max = std::max(max, val);
154                     }
155 
156                     outptr[j] = max;
157                 }
158 
159                 outptr += outw;
160             }
161         }
162     }
163     else if (pooling_type == PoolMethod_AVE)
164     {
165         for (int32_t q=0; q<channels; q++)
166         {
167             const Mat m = bottom_blob_bordered.channel(q);
168             float* outptr = top_blob.channel(q);
169 
170             for (int32_t i = 0; i < outh; i++)
171             {
172                 for (int32_t j = 0; j < outw; j++)
173                 {
174                     const float* sptr = m.row(i*stride_h) + j*stride_w;
175 
176                     float sum = 0;
177 
178                     for (int32_t k = 0; k < maxk; k++)
179                     {
180                         float val = sptr[ space_ofs[k] ];
181                         sum += val;
182                     }
183 
184                     outptr[j] = sum / maxk;
185                 }
186 
187                 outptr += outw;
188             }
189 
190             // 修正填充边距
191             if (pad_top != 0)
192             {
193                 const float scale = (float)kernel_h / (kernel_h - pad_top);
194 
195                 outptr = top_blob.channel(q).row(0);
196                 for (int32_t i = 0; i < outw; i++)
197                 {
198                     outptr[i] *= scale;
199                 }
200             }
201             if (pad_bottom + htailpad != 0)
202             {
203                 const float scale = (float)kernel_h / (kernel_h - pad_bottom - htailpad);
204 
205                 outptr = top_blob.channel(q).row(outh - 1);
206                 for (int32_t i = 0; i < outw; i++)
207                 {
208                     outptr[i] *= scale;
209                 }
210             }
211             if (pad_left != 0)
212             {
213                 const float scale = (float)kernel_w / (kernel_w - pad_left);
214 
215                 outptr = top_blob.channel(q);
216                 for (int32_t i = 0; i < outh; i++)
217                 {
218                     *outptr *= scale;
219                     outptr += outw;
220                 }
221             }
222             if (pad_right + wtailpad != 0)
223             {
224                 const float scale = (float)kernel_w / (kernel_w - pad_right - wtailpad);
225 
226                 outptr = top_blob.channel(q);
227                 outptr += outw - 1;
228                 for (int32_t i = 0; i < outh; i++)
229                 {
230                     *outptr *= scale;
231                     outptr += outw;
232                 }
233             }
234         }
235     }
236 
237     return 0;
238 }
```

## 实现说明

第5行，我们使用`DEFINE_LAYER_CREATOR`（层名）来注册我们定义的层。

第12～26行，定义了`load_param()`成员函数，分别从`ParamDict`对象中获取了`pooling_type`、`kernel_w`、`kernel_h`、`stride_w`、`stride_h`、`pad_left`、`pad_right`、`pad_top`、`pad_bottom`和`global_pooling`。

第28～238行，定义了`forward()`函数，这是池化层的实现核心，因此代码较长，我们需要详细阐述。

第30～33行，从输入数据块中获取输入的宽度`w`、高度`h`、通道数`channels`和元素尺寸`elemsize`。这里数据块占用在空间理论上是宽度与高度以及元素尺寸的乘积，但是为了效率起见，我们对每行数据都做了数据对齐，因此实际的每通道字节数可能会大于理论的数据量。

第35行，如果模型设置了`global_pooling`，说明需要对待池化矩阵中的所有数据都进行池化操作，那么就会执行第36～75行的全局池化操作，否则会执行77行之后的局部池化。

第37～39行，根据输入数据的尺寸计算输出数据尺寸，并创建输出数据块。

第43行，根据`pooling_type`判断是执行最大池化还是平均池化。

第45～56行，执行最大池化操作，其实就是使用双重循环，遍历数据中的每个数据，然后取池化核窗口范围内的最大值，并记录到输出矩阵中。

第60～71行，执行平均池化操作，其实就是使用双重循环，遍历数据中的每个数据，然后取池化核窗口范围内数据之和，最后计算平均值并记录到输出矩阵中。

第74行，执行完全局池化操作后直接返回。

