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
      // 从本地存储获取房间信息
      const roomInfo = uni.getStorageSync(`room_${this.roomId}`)
      if (roomInfo && roomInfo.players) {
        this.players = roomInfo.players.map(player => ({
          ...player,
          inputScore: ''
        }))
      } else {
        uni.showToast({
          title: '获取玩家信息失败',
          icon: 'none'
        })
        setTimeout(() => {
          uni.navigateBack()
        }, 1500)
      }
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

      // 更新房间信息中的分数
      const roomInfo = uni.getStorageSync(`room_${this.roomId}`)
      if (roomInfo) {
        const updatedPlayers = roomInfo.players.map(player => {
          if (player.id === this.currentUserId) {
            return {
              ...player,
              score: (player.score || 0) + scoreData.totalScore
            }
          }
          const playerScore = scoreData.scores.find(s => s.userId === player.id)
          if (playerScore) {
            return {
              ...player,
              score: (player.score || 0) + playerScore.score
            }
          }
          return player
        })

        // 保存更新后的房间信息
        uni.setStorageSync(`room_${this.roomId}`, {
          ...roomInfo,
          players: updatedPlayers,
          updateTime: Date.now()
        })

        uni.showToast({
          title: '记分成功',
          icon: 'success'
        })

        // 返回上一页
        setTimeout(() => {
          uni.navigateBack()
        }, 1500)
      }
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