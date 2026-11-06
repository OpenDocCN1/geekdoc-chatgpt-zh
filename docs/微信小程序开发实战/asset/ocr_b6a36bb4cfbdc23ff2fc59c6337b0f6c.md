```
} else {
    dbHomes.add({
        data: { books: collection },
        success: () => { res() },
        fail: () => {
            wx.showToast({
                title: '收藏失败',
            })
            rej("error");
            return;
        }
    });
},
fail: function (error) {
    wx.showToast({
        title: '收藏失败',
    })
    rej("error");
    return;
}
});
}).catch((error)=>{
    wx.showToast({
        title: '收藏失败',
    });
}).catch(()=>{
    wx.showToast({
        title: '登录失败',
    });
    rej("error");
    return;
});
} else {
database.getUserHome(user.openId).then((homes) => {
    let collection;
    if (homes.length > 0) {
        collection = homes[0].books;
    }
    if (!collection) {
        collection = [];
    }
    if (collection.indexOf(isbn) > -1) {
        wx.showToast({
            title: '已经收藏过啦，不要重复添加哦~',
        })
    } else {
        collection.push(isbn);
        let db = wx.cloud.database();
```