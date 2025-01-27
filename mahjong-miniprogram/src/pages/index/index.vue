<template>
  <view class="content">
    <image class="logo" src="/static/logo.png"></image>
    <view class="title-container">
      <text class="title">麻将记分</text>
    </view>
    <view class="login-section">
      <button class="login-btn" type="primary" @click="handleLogin">
        <text class="btn-text">微信一键登录</text>
      </button>
    </view>
  </view>
</template>

<script>
export default {
  data() {
    return {
      userInfo: null
    }
  },
  onLoad() {
    // 检查是否已经登录
    const userInfo = uni.getStorageSync('userInfo')
    if (userInfo) {
      this.navigateToRoom()
    }
  },
  methods: {
    handleLogin() {
      uni.getUserProfile({
        desc: '用于完善会员资料',
        success: (res) => {
          this.userInfo = res.userInfo
          uni.setStorageSync('userInfo', {
            ...res.userInfo,
            id: Date.now().toString() // 临时使用时间戳作为用户ID
          })
          this.navigateToRoom()
        },
        fail: () => {
          uni.showToast({
            title: '请授权登录',
            icon: 'none'
          })
        }
    },
    navigateToRoom() {
      uni.redirectTo({
        url: '/pages/room/room'
      })
    }
  }
}
</script>

<style>
.content {
  display: flex;
  flex-direction: column;
  align-items: center;
  padding: 100rpx 50rpx;
}

.logo {
  width: 200rpx;
  height: 200rpx;
  margin-bottom: 50rpx;
}

.title-container {
  margin-bottom: 100rpx;
}

.title {
  font-size: 40rpx;
  font-weight: bold;
  color: #333;
}

.login-section {
  width: 100%;
}

.login-btn {
  width: 100%;
  height: 88rpx;
  line-height: 88rpx;
  background: #07c160;
  border-radius: 44rpx;
}

.btn-text {
  font-size: 32rpx;
  color: #fff;
}
</style>
