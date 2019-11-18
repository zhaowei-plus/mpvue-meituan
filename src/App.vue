
<script>
import {timestampToCommonDate} from "@/utils/formatTime"
import {_array} from "@/utils/arrayExtension"

export default {
  onLaunch(options) {
    if (!wx.cloud) {
      console.error('请使用 2.2.3或以上的基础库以使用云能力')
    } else {
      // 初始化云服务
      wx.cloud.init({
        traceUser: true,
      })
    }

    if (wx.canIUse('getUpdateManager')) {
      // 管理小程序的更新
      const updateManager = wx.getUpdateManager()
      // 监听向微信后台请求检查更新结果事件。微信在小程序冷启动时自动检查更新，不需由开发者主动触发
      updateManager.onCheckForUpdate(function (res) {
        if (res.hasUpdate) {
          updateManager.onUpdateReady(function () {
            // 强制小程序重启并使用新版本。在小程序新版本下载完成后（即收到 onUpdateReady 回调）调用。
             updateManager.applyUpdate()
          })
          // 监听小程序更新失败事件。小程序有新版本，客户端主动触发下载（无需开发者触发），下载失败（可能是网络原因等）后回调
          updateManager.onUpdateFailed(function () {
            wx.showModal({
              title: '更新提示',
              content: '发现新版本，请删除小程序重新搜索下载最新版本！',
            })
          })
        }
      })
    } else {
      wx.showModal({
        title: '温馨提示',
        content: '当前微信版本过低，无法更新小程序，请升级微信到最新版本！'
      })
    }
  },
  created() {
    console.log("app created and cache logs by setStorageSync");
  },
  mounted() {
    // 监听网络状态的变化
    wx.onNetworkStatusChange(function(res) {
      wx.setStorageSync('networkType', res.networkType)
      if (!res.isConnected) {
        wx.showToast({
          title: '网络链接失败，请检查网络！',
          icon: 'none',
          duration: 5000
        })
      }
    })
    wx.getNetworkType({
      success: function(res) {
        wx.setStorageSync('networkType', res.networkType)
      }
    })
  },
  onShow(options) {
    if (options.referrerInfo.appId) {
      console.log("options.referrerInfo", options.referrerInfo);
      wx.setStorageSync("referrerInfo", options.referrerInfo);
    }
  },
  onPageNotFound(res) {
    wx.redirectTo({
      url: 'pages/notFound/main'
    })
  },
  onError (err) {
    console.log('程序报错啦：', err)
    var pages = getCurrentPages()
    if (pages.length <= 0) return
    var currentPage = pages[pages.length - 1]
    var url = currentPage.route
    var options = JSON.stringify(currentPage.options)
    var time = timestampToCommonDate(new Date().getTime())
    var pageErrArr = wx.getStorageSync('pageErrArr') || []
    pageErrArr = _array.unshift(pageErrArr, {url, options, err, time})
    wx.setStorageSync('pageErrArr', pageErrArr)
  }
};
</script>

<style lang="scss">
@import "assets/iconfont.less";

page {
  box-sizing: border-box;
  background-color: $page-bgcolor;
  min-height: 100%;
}

.container {
  width: 100%;
  height: 100%;
  display: flex;
  flex-direction: column;
  box-sizing: border-box;
  background-color: $page-bgcolor;
}

/* this rule will be remove */
* {
  transition: width 2s;
  -moz-transition: width 2s;
  -webkit-transition: width 2s;
  -o-transition: width 2s;
}
</style>
