<template>
  <view class="room-container">
    <view class="header">
      <text class="welcome">欢迎, {{userInfo.nickName}}</text>
    </view>
    
    <view class="options-container">
      <view class="option-card" @click="createRoom">
        <text class="option-title">创建房间</text>
        <text class="option-desc">创建新的麻将房间</text>
      </view>
      
      <view class="option-card" @click="showJoinRoom">
        <text class="option-title">加入房间</text>
        <text class="option-desc">输入房间号加入</text>
      </view>
    </view>

    <!-- 加入房间弹窗 -->
    <uni-popup ref="joinPopup" type="center">
      <view class="popup-content">
        <view class="popup-title">加入房间</view>
        <input 
          class="room-input" 
          type="number" 
          v-model="roomNumber"
          placeholder="请输入房间号"
        />
        <view class="popup-btns">
          <button class="popup-btn cancel" @click="closeJoinPopup">取消</button>
          <button class="popup-btn confirm" @click="joinRoom">确认</button>
        </view>
      </view>
    </uni-popup>
  </view>
</template>

<script>
export default {
  data() {
    return {
      userInfo: {},
      roomNumber: ''
    }
  },
  onLoad() {
    const userInfo = uni.getStorageSync('userInfo')
    if (!userInfo) {
      uni.redirectTo({
        url: '/pages/index/index'
      })
      return
    }
    this.userInfo = userInfo
  },
  methods: {
    createRoom() {
      // 创建房间并跳转到游戏页面
      uni.navigateTo({
        url: '/pages/game/game?type=create'
      })
    },
    showJoinRoom() {
      this.$refs.joinPopup.open()
    },
    closeJoinPopup() {
      this.$refs.joinPopup.close()
      this.roomNumber = ''
    },
    joinRoom() {
      if (!this.roomNumber) {
        uni.showToast({
          title: '请输入房间号',
          icon: 'none'
        })
        return
      }
      // 加入房间并跳转到游戏页面
      uni.navigateTo({
        url: `/pages/game/game?type=join&roomId=${this.roomNumber}`
      })
      this.closeJoinPopup()
    }
  }
}
</script>

<style>
.room-container {
  padding: 40rpx;
}

.header {
  margin-bottom: 60rpx;
}

.welcome {
  font-size: 36rpx;
  font-weight: bold;
  color: #333;
}

.options-container {
  display: flex;
  flex-direction: column;
  gap: 30rpx;
}

.option-card {
  background: #fff;
  padding: 40rpx;
  border-radius: 20rpx;
  box-shadow: 0 2rpx 12rpx rgba(0, 0, 0, 0.1);
}

.option-title {
  font-size: 32rpx;
  font-weight: bold;
  color: #333;
  margin-bottom: 10rpx;
  display: block;
}

.option-desc {
  font-size: 28rpx;
  color: #666;
}

.popup-content {
  background: #fff;
  width: 600rpx;
  padding: 40rpx;
  border-radius: 20rpx;
}

.popup-title {
  font-size: 32rpx;
  font-weight: bold;
  text-align: center;
  margin-bottom: 30rpx;
}

.room-input {
  width: 100%;
  height: 80rpx;
  border: 2rpx solid #ddd;
  border-radius: 10rpx;
  padding: 0 20rpx;
  margin-bottom: 30rpx;
}

.popup-btns {
  display: flex;
  justify-content: space-between;
  gap: 20rpx;
}

.popup-btn {
  flex: 1;
  height: 80rpx;
  line-height: 80rpx;
  text-align: center;
  border-radius: 10rpx;
  font-size: 28rpx;
}

.cancel {
  background: #f5f5f5;
  color: #666;
}

.confirm {
  background: #07c160;
  color: #fff;
}
</style>