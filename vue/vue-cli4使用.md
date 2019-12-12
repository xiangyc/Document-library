### 一  环境

    windows: 10
    vue-cli: 4
    编辑器： vsCode/sublime/webstorm
    npm: 6.11.3 ＃去nodejs网安装https://npm.taobao.org/mirrors/node/v12.12.0/node-v12.12.0-x64.msi


### 二 安装

    >npm install -g @vue/cli
    # 查看版本
    PS D:\vue\project-3> vue -V
    @vue/cli 4.0.4


### 三  创建项目
  添加淘宝镜像：
    npm install -g cnpm --registry=https://registry.npm.taobao.org
    创建项目
    vue create project-3

    #这里可以上下选择， 我们选 手动
    Vue CLI v4.0.4
    ? Please pick a preset:    
      default (babel, eslint)  
    > Manually select features 

    # 然后根据自己的需求选组件
    Vue CLI v4.0.4
    ? Please pick a preset: Manually select features
    ? Check the features needed for your project: 
     (*) Babel
     ( ) TypeScript
     ( ) Progressive Web App (PWA) Support        
     (*) Router
     (*) Vuex
     (*) CSS Pre-processors
    >(*) Linter / Formatter
     ( ) Unit Testing
     ( ) E2E Testing


### 四  列表数据绑定
  1.  页面部分
  
    <block wx:for="{{bannerList}}" wx:key="{{banner}}">
        <swiper-item class="banner" >
        <image src="{{item.imgurl}}" data-activityType='{{item.activityType}}' data-relativeId='{{item.relativeId}}'   data-type='{{item.type}}' bindtap="openActivity"/>
        </swiper-item>
    </block>

  2.  js部分
  
    wx.request({
        url: app.globalData.requestUri + "/banners",
        header: {
         "Content-Type": "application/json"
        },
        method: "GET",
        data: {
          start: start,
          maxResults: maxResults,
          bannerType: bannerType
        },
        success: function (res) {
         that.setData({
             bannerList: res.data.items
          })
        }
    })

  3.  method为GET时"Content-Type": "application/json"；POST时"Content-Type": "application/x-www-form-urlencoded"

  4.  点击事件、传递参数，使用bindtap绑定方法。参数用data-*的形式传递。记得全部小写。默认会放在dataset中。
  
    openActivity: function (event) {
        var params = event.currentTarget.dataset;
        //dataset中多个单词由连字符-链接，不能有大写(大写会自动转成小写)
        //底部菜单要使用wx.switchTab 来跳转界面
        var type = params.type;
        var activityType = params.activitytype;
        var relativeId = params.relativeid;
    }


### 五  详情数据绑定
  1. js部分代码
  
    onLoad: function (options) { 
    var id = options.id;
    var that = this;
    wx.request({
      url: app.globalData.requestUri + "/infos/" + id,
      header: {
        "Content-Type": "application/json"
      },
      method: "GET",
      success: function (res) {
        that.setData({
          news: res.data,
          imgurl: app.globalData.domain + res.data.imgurl,
          publishTime: util.formatTime1(res.data.publishTime,'Y-M-D h:m:s')
        })
      }
    })
    }

  2.  页面部分代码
  
    <view class='text1'>
    <text>{{news.title}}</text>
    </view>

    <view class='date1'>
    <text>{{publishTime}}</text>
    </view>

    <view class='img'>
    <image src="{{imgurl}}" class="image" />
    </view>


  3.  页面引用公用js
  
    var util = require("../../../utils/util.js");
    var app = getApp();


### 六  上拉刷新、下拉加载
  1.  上拉刷新
  
    onPullDownRefresh: function () {
    var that = this;
    //下拉刷新，将pageNumber和pageSize分别设置成0和10，并初始化数据，让数据重新通过loadRoom()获取
    that.setData({
      start: 0,
      maxResults: 10,
      infoList: []
    })
    that.loadRooms();
    wx.stopPullDownRefresh();
    }

  2.  下拉加载
  
    onReachBottom: function () {
      //上拉分页,将页码加1，然后调用分页函数
      var that = this;
      var start = that.data.start;
      that.setData({
        start: ++start
      });

      setTimeout(function () {
        wx.showToast({
          title: '加载中..',
        }),
        that.loadRooms();

      }, 1000)
    }

  3.  需要在json中配置启用下拉事件
  
    {
    "enablePullDownRefresh": true
    }


### 七  表单提交
  1. bindsubmit表单提交事件；bindinput输入框监控事件；

  2. 获得表单提交数据
  
    formSubmit: function (e) {
      //获得表单数据
      var objData = e.detail.value;
      var password = objData.password;
      var mobile = objData.mobile;
      var code = objData.code;
    }


### 八  缓存数据读取
  1.  缓存写入
  
    wx.setStorageSync('password', password);
    wx.setStorageSync('mobile', mobile);

  2.  缓存读取、移除、清除所有
  
    var mobile = wx.getStorageSync('mobile'); 
    wx.removeStorageSync('mobile');
    wx.clearStorage();


### 九  提示信息，弹出框
  1.  提示信息和弹框，有icon时最多显示7个字，icon为none时可显示全部信息。
  
    wx.showToast({
        title: '登录成功',
        success: function () {
          setTimeout(function () {
            //要延时执行的代码
            //跳转到成功页面
            wx.switchTab({
              url: '../index/index'
            })
          }, 2000) //延迟时间
        }
    })

  2.  模态框，确认取消对话框
  
    wx.showModal({
        title: '确认',
        content: '确认提交订单',
        success: function (res) {
            if (res.confirm) {
                console.log('确定')
            }else{
               console.log(取消')
            }
        }
    })

### 十  分享
  1. 分享，imageUrl非必填
  
    onShareAppMessage: function () {
    return {
      title: '金算子',
      desc: '新闻资讯',
      imageUrl: '../../images/home/home-add-01.png',
      path: '../activity/news/detail',
      success: function (res) {

      },
      fail: function () {

      }
    }
    }


### 十一  打电话
  1. 打电话调用wx.makePhoneCall

    phoneCall: function (mpbile) {
    wx.makePhoneCall({
      phoneNumber: "13724201432"
    })
    }
    
    
### 十二  组件、API   

[组件]https://developers.weixin.qq.com/miniprogram/dev/component/
[API]https://developers.weixin.qq.com/miniprogram/dev/api/
