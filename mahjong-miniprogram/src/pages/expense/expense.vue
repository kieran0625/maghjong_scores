<template>
  <view class="expense-container">
    <view class="player-list">
      <view v-for="(player, index) in players" :key="index" class="player-item" v-if="player.id !== currentUserId">
        <view class="player-info">
          <image class="avatar" :src="player.avatarUrl" mode="aspectFit"></image>
          <text class="nickname">{{player.nickName}}</text>
        </view>
        <view class="score-input">
          <input 
            type="number" 
            v-model="player.inputScore" 
            class="input" 
            placeholder="输入分数"
          />
        </view>
      </view>
    </view>

    <view class="submit-section">
      <button class="submit-btn" @click="submitScores">确认记分</button>
    </view>
  </view>
</template>

<script>
export default {
  data() {
    return {
      players: [],
      currentUserId: '',
      roomId: ''
    }
  },
  onLoad(options) {
    this.roomId = options.roomId
    const userInfo = uni.getStorageSync('userInfo')
    if (userInfo) {
      this.currentUserId = userInfo.id
    }
    // 获取房间内所有玩家信息
    this.getPlayers()
  },
  methods: {
    getPlayers() {
      // 这里应该从全局状态或后端获取玩家列表
      // 暂时使用模拟数据
      this.players = [
        {
          id: '1',
          nickName: '玩家1',
          avatarUrl: '/static/avatar.png',
          inputScore: ''
        },
        {
          id: '2',
          nickName: '玩家2',
          avatarUrl: '/static/avatar.png',
          inputScore: ''
        }
      ]
    },
    submitScores() {
      // 验证输入
      let totalScore = 0
      let hasInvalidInput = false

      this.players.forEach(player => {
        if (player.id !== this.currentUserId) {
          const score = Number(player.inputScore)
          if (isNaN(score)) {
            hasInvalidInput = true
          } else {
            totalScore += score
          }
        }
      })

      if (hasInvalidInput) {
        uni.showToast({
          title: '请输入有效的分数',
          icon: 'none'
        })
        return
      }

      // 更新分数
      const scoreData = {
        roomId: this.roomId,
        scores: this.players.map(player => ({
          userId: player.id,
          score: Number(player.inputScore) || 0
        })),
        currentUserId: this.currentUserId,
        totalScore: -totalScore // 当前用户的分数为负
      }

      // 这里应该调用后端API更新分数
      console.log('提交分数：', scoreData)

      // 返回上一页
      uni.navigateBack()
    }
  }
}
</script>

<style>
.expense-container {
  padding: 40rpx;
}

.player-list {
  margin-bottom: 60rpx;
}

.player-item {
  display: flex;
  justify-content: space-between;
  align-items: center;
  padding: 30rpx;
  background: #fff;
  border-radius: 20rpx;
  margin-bottom: 20rpx;
  box-shadow: 0 2rpx 12rpx rgba(0, 0, 0, 0.1);
}

.player-info {
  display: flex;
  align-items: center;
}

.avatar {
  width: 80rpx;
  height: 80rpx;
  border-radius: 40rpx;
  margin-right: 20rpx;
}

.nickname {
  font-size: 32rpx;
  color: #333;
}

.score-input {
  flex: 1;
  max-width: 200rpx;
  margin-left: 20rpx;
}

.input {
  width: 100%;
  height: 80rpx;
  border: 2rpx solid #ddd;
  border-radius: 10rpx;
  padding: 0 20rpx;
  text-align: right;
}

.submit-section {
  padding: 40rpx 0;
}

.submit-btn {
  width: 100%;
  height: 88rpx;
  line-height: 88rpx;
  background: #07c160;
  color: #fff;
  border-radius: 44rpx;
  font-size: 32rpx;
}
</style>