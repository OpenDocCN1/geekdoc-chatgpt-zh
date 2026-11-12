```
$ cd ~/aiedge-exp/edge_smarthome
$ chmod 755 start_aicam.sh
$ conda activate py36_tfl14_torch15_cpu_cv345    //Ubuntu 20.04 操作系统下需要切换环境
$ ./start_aicam.sh
//开始运行脚本
* Serving Flask app "start_aicam" (lazy loading)
* Environment: production
   WARNING: Do not use the development server in a production environment.
   Use a production WSGI server instead.
* Debug mode: off
* Running on http://0.0.0.0:4001/ (Press CTRL+C to quit)
```

（2）在客户端或者边缘计算网关端打开Chrome浏览器，输入项目页面地址https：//192. 168. 100. 200：1446/static/edge_smarthome/index.html，即可查看项目内容。

# 5.1.2.2 智能家居系统开发验证

# 1) 智慧物联

（1）应用设置。第一次登录智能家居系统后，单击主页右上方的“设置”按钮可设置相应的参数（默认情况下会读取static/edge_smarthome/js/config.js内的初始配置），如智云账号、节点地址、百度AI账号等。设置参数后，在智云账号界面单击“连接”按钮即可连接到数据服务，连接成功后会弹出消息提示。智能家居系统的应用设置页面如图5.6所示。