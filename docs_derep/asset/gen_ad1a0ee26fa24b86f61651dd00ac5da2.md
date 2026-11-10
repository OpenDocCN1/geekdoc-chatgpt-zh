93 

 94     jfloatArray jScores = env->NewFloatArray(size);

 95     env->SetFloatArrayRegion(jScores, 0, size, scores);

 96 

 97     delete[] scores;

 98 

 99     return jScores;

100 }

[PRE0]

 1 template <typename Dtype>

 2 class CConvolutionLayer : public BaseConvolutionLayer<Dtype> {

 3     public:

 4 

 5     explicit CConvolutionLayer(const LayerParameter& param)

 6         : BaseConvolutionLayer<Dtype>(param) {}

 7     virtual void LayerSetUp(const vector<Blob<Dtype>*>& bottom,

 8         const vector<Blob<Dtype>*>& top);

 9     virtual inline const char* type() const { return "CConvolution"; }

10 

11     protected:

12     virtual void Forward_cpu(const vector<Blob<Dtype>*>& bottom,

13         const vector<Blob<Dtype>*>& top);

14     virtual void Forward_gpu(const vector<Blob<Dtype>*>& bottom,

15         const vector<Blob<Dtype>*>& top);

16     virtual void Backward_cpu(const vector<Blob<Dtype>*>& top,

17         const vector<bool>& propagate_down, const vector<Blob<Dtype>*>& bottom);

18     virtual void Backward_gpu(const vector<Blob<Dtype>*>& top,

19         const vector<bool>& propagate_down, const vector<Blob<Dtype>*>& bottom);

20     virtual inline bool reverse_dimensions() { return false; }

21     virtual void compute_output_shape();

22 

23     private:

24     Blob<Dtype> weight_tmp_;

25     Blob<Dtype> bias_tmp_;

26     Blob<Dtype> rand_weight_m_;

27     Blob<Dtype> rand_bias_m_;

28     Dtype gamma,power;

29     Dtype crate;

30     Dtype mu,std;

31     int iter_stop_;

32 };

[PRE1]

  1 template <typename Dtype>

  2 void CConvolutionLayer<Dtype>::LayerSetUp(const vector<Blob<Dtype>*>& bottom,

  3         const vector<Blob<Dtype>*>& top) {

  4     BaseConvolutionLayer <Dtype>::LayerSetUp(bottom, top);

  5 

  6         CConvolutionParameter cconv_param = this->layer_param_.cconvolution_param();

  7 

  8     if(this->blobs_.size()==2 && (this->bias_term_)){

  9         this->blobs_.resize(4);

10         // 为权重掩码与偏置掩码准备初始填充
        this->blobs_[2].reset(new Blob<Dtype>(this->blobs_[0]->shape()));
        shared_ptr<Filler<Dtype> > weight_mask_filler(GetFiller<Dtype>(
            cconv_param.weight_mask_filler()));