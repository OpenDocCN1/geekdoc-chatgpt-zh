```
* 页面的初始数据
*/
data: {
    //头部广告条数据
    headerText:"美好的一天，从阅读开始~",
    //轮播图数据
    swiperData:[],
    //新闻数据
    dataList:[],
    //当前请求到的新闻页数
    currentPage:1,
},
/**
 *生命周期函数——监听页面加载
 */
//进行导航配置和初始数据请求
onLoad: function (options) {
    wx.setNavigationBarTitle({
        title: '精选',
    });
    this.getDictum();
    this.getWxNews();
},
//封装头部数据请求方法
getDictum:function() {
    network.getDictumData().then(
        (res) => {
            this.setData({
                headerText: `${res.newslist[0].content}\n—${res.newslist[0].mrname}`
            })
        }
    ).catch((error) => {
        console.log(error);
    });
},
//封装新闻列表数据请求方法
getWxNews:function() {
    wx.showLoading({
        title: '加载中',
    })
    network.getWxNewsData(this.data.currentPage).then((res) => {
        wx.hideLoading();
        wx.stopPullDownRefresh();
        if (this.data.currentPage == 1) {
            //下拉刷新的逻辑
            this.setData({
                //进行数据处理，拆分为轮播图数据和列表数据
                dataList : res.newslist.slice(3),
                swiperData: res.newslist.slice(0,3)
```