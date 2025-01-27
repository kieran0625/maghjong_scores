<template>
  <view class="game-container">
    <!-- 房间信息 -->
    <view class="room-info">
      <view class="room-id">房间号：{{roomId}}</view>
      <button class="share-btn" @click="showSharePopup">邀请好友</button>
    </view>

    <!-- 分享弹窗 -->
    <uni-popup ref="sharePopup" type="center">
      <view class="popup-content share-popup">
        <view class="popup-title">微信扫描二维码登录</view>
        <view class="qr-container">
          <text class="room-number">房间号：{{roomId}}</text>
          <image class="qr-code" :src="qrCodeUrl" mode="aspectFit"></image>
        </view>
        <button class="share-btn-large" open-type="share">转发给好友</button>
        <button class="close-btn" @click="closeSharePopup">关闭</button>
      </view>
    </uni-popup>

    <!-- 玩家列表 -->
    <view class="players-list">
      <view v-for="(player, index) in players" :key="index" class="player-card">
        <view class="crown-container" v-if="player.isWinner">
          <image class="crown" src="/static/crown.png" mode="aspectFit"></image>
        </view>
        <image class="avatar" :src="player.avatarUrl" mode="aspectFit"></image>
        <text class="nickname">{{player.nickName}}</text>
        <text class="score" :class="{positive: player.score > 0, negative: player.score < 0}">{{player.score}}</text>
      </view>
      <view class="add-player" @click="showSharePopup" v-if="players.length < 4">
        <text class="add-icon">+</text>
        <text>邀请好友</text>
      </view>
    </view>

    <!-- 底部导航 -->
    <view class="bottom-nav">
      <view class="nav-item" @click="navigateToExpense">
        <text class="nav-text">支出</text>
      </view>
      <view class="nav-item" @click="showFeedback">
        <text class="nav-text">客服反馈</text>
      </view>
      <view class="nav-item" @click="navigateToProfile">
        <text class="nav-text">我的</text>
      </view>
    </view>
  </view>
</template>

<script>
export default {
  data() {
    return {
      roomId: '',
      qrCodeUrl: '',
      players: [],
      isCreator: false
    }
  },
  onLoad(options) {
    if (options.type === 'create') {
      this.isCreator = true
      this.createNewRoom()
    } else if (options.type === 'join') {
      this.roomId = options.roomId
      this.joinRoom()
    }
  },
  methods: {
    createNewRoom() {
      // 创建新房间的逻辑
      this.roomId = Math.random().toString().slice(2, 8)
      this.generateQRCode()
      this.addCurrentPlayer()
    },
    joinRoom() {
      // 加入房间的逻辑
      this.generateQRCode()
      this.addCurrentPlayer()
    },
    generateQRCode() {
      // 生成二维码的逻辑
      // 这里需要后端支持，暂时使用示例图片
      this.qrCodeUrl = '/static/qr-code.png'
    },
    addCurrentPlayer() {
      const userInfo = uni.getStorageSync('userInfo')
      if (userInfo) {
        this.players.push({
          ...userInfo,
          score: 0,
          isWinner: false
        })
      }
    },
    showSharePopup() {
      this.$refs.sharePopup.open()
    },
    closeSharePopup() {
      this.$refs.sharePopup.close()
    },
    navigateToExpense() {
      uni.navigateTo({
        url: '/pages/expense/expense'
      })
    },
    showFeedback() {
      uni.showToast({
        title: '功能开发中',
        icon: 'none'
      })
    },
    navigateToProfile() {
      uni.navigateTo({
        url: '/pages/profile/profile'
      })
    }
  }
}
</script>

<style>
.game-container {
  padding: 40rpx;
  padding-bottom: 120rpx;
}

.room-info {
  display: flex;
  justify-content: space-between;
  align-items: center;
  margin-bottom: 40rpx;
}

.room-id {
  font-size: 32rpx;
  font-weight: bold;
  color: #333;
}

.share-btn {
  font-size: 28rpx;
  color: #07c160;
  background: none;
  border: 2rpx solid #07c160;
  border-radius: 30rpx;
  padding: 10rpx 30rpx;
}

.share-popup {
  width: 600rpx;
}

.qr-container {
  display: flex;
  flex-direction: column;
  align-items: center;
  margin: 30rpx 0;
}

.room-number {
  font-size: 28rpx;
  color: #666;
  margin-bottom: 20rpx;
}

.qr-code {
  width: 300rpx;
  height: 300rpx;
}

.share-btn-large {
  width: 100%;
  height: 88rpx;
  line-height: 88rpx;
  background: #07c160;
  color: #fff;
  border-radius: 44rpx;
  margin-bottom: 20rpx;
}

.close-btn {
  width: 100%;
  height: 88rpx;
  line-height: 88rpx;
  background: #f5f5f5;
  color: #666;
  border-radius: 44rpx;
}

.players-list {
  display: grid;
  grid-template-columns: repeat(2, 1fr);
  gap: 30rpx;
}

.player-card {
  position: relative;
  background: #fff;
  padding: 30rpx;
  border-radius: 20rpx;
  box-shadow: 0 2rpx 12rpx rgba(0, 0, 0, 0.1);
  display: flex;
  flex-direction: column;
  align-items: center;
}

.crown-container {
  position: absolute;
  top: -20rpx;
  left: -20rpx;
}

.crown {
  width: 60rpx;
  height: 60rpx;
}

.avatar {
  width: 120rpx;
  height: 120rpx;
  border-radius: 60rpx;
  margin-bottom: 20rpx;
}

.nickname {
  font-size: 28rpx;
  color: #333;
  margin-bottom: 10rpx;
}

.score {
  font-size: 36rpx;
  font-weight: bold;
}

.positive {
  color: #07c160;
}

.negative {
  color: #ff4d4f;
}

.add-player {
  background: #f5f5f5;
  border-radius: 20rpx;
  display: flex;
  flex-direction: column;
  align-items: center;
  justify-content: center;
  padding: 30rpx;
  color: #666;
  font-size: 28rpx;
}

.add-icon {
  font-size: 48rpx;
  margin-bottom: 10rpx;
}

.bottom-nav {
  position: fixed;
  bottom: 0;
  left: 0;
  right: 0;
  height: 100rpx;
  background: #fff;
  display: flex;
  justify-content: space-around;
  align-items: center;
  box-shadow: 0 -2rpx 12rpx rgba(0, 0, 0, 0.1);
}

.nav-item {
  flex: 1;
  display: flex;
  justify-content: center;
  align-items: center;
}

.nav-text {
  font-size: 28rpx;
  color: #333;
}
</style>