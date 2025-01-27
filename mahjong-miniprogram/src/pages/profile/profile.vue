<template>
  <view class="profile-container">
    <view class="ranking-section">
      <view class="section-title">积分排名</view>
      <view class="player-list">
        <view v-for="(player, index) in sortedPlayers" :key="index" class="player-item">
          <view class="rank-number" :class="{'top-three': index < 3}">{{index + 1}}</view>
          <image class="avatar" :src="player.avatarUrl" mode="aspectFit"></image>
          <view class="player-info">
            <text class="nickname">{{player.nickName}}</text>
            <text class="score" :class="{positive: player.score > 0, negative: player.score < 0}">{{player.score}}</text>
          </view>
          <view class="winner-badge" v-if="index === 0">
            <image class="crown" src="/static/crown.png" mode="aspectFit"></image>
          </view>
        </view>
      </view>
    </view>

    <view class="stats-section">
      <view class="section-title">游戏统计</view>
      <view class="stats-grid">
        <view class="stats-item">
          <text class="stats-value">{{totalGames}}</text>
          <text class="stats-label">总局数</text>
        </view>
        <view class="stats-item">
          <text class="stats-value">{{winRate}}%</text>
          <text class="stats-label">胜率</text>
        </view>
        <view class="stats-item">
          <text class="stats-value">{{maxScore}}</text>
          <text class="stats-label">最高分</text>
        </view>
      </view>
    </view>
  </view>
</template>

<script>
export default {
  data() {
    return {
      players: [],
      totalGames: 0,
      winRate: 0,
      maxScore: 0
    }
  },
  computed: {
    sortedPlayers() {
      return [...this.players].sort((a, b) => b.score - a.score)
    }
  },
  onLoad() {
    // 模拟获取玩家数据
    this.players = [
      {
        id: '1',
        nickName: '玩家1',
        avatarUrl: '/static/avatar1.png',
        score: 100
      },
      {
        id: '2',
        nickName: '玩家2',
        avatarUrl: '/static/avatar2.png',
        score: -50
      }
    ]

    // 模拟统计数据
    this.totalGames = 10
    this.winRate = 60
    this.maxScore = 150
  }
}
</script>

<style>
.profile-container {
  padding: 20rpx;
}

.section-title {
  font-size: 32rpx;
  font-weight: bold;
  margin-bottom: 20rpx;
}

.ranking-section {
  background-color: #fff;
  border-radius: 10rpx;
  padding: 20rpx;
  margin-bottom: 20rpx;
}

.player-list {
  margin-top: 20rpx;
}

.player-item {
  display: flex;
  align-items: center;
  padding: 20rpx;
  border-bottom: 1rpx solid #eee;
  position: relative;
}

.rank-number {
  width: 40rpx;
  height: 40rpx;
  background-color: #999;
  color: #fff;
  border-radius: 50%;
  display: flex;
  align-items: center;
  justify-content: center;
  margin-right: 20rpx;
  font-size: 24rpx;
}

.rank-number.top-three {
  background-color: #FFB800;
}

.avatar {
  width: 80rpx;
  height: 80rpx;
  border-radius: 50%;
  margin-right: 20rpx;
}

.player-info {
  flex: 1;
}

.nickname {
  font-size: 28rpx;
  margin-bottom: 5rpx;
}

.score {
  font-size: 32rpx;
  font-weight: bold;
}

.positive {
  color: #07C160;
}

.negative {
  color: #FF3B30;
}

.winner-badge {
  position: absolute;
  right: 20rpx;
}

.crown {
  width: 40rpx;
  height: 40rpx;
}

.stats-section {
  background-color: #fff;
  border-radius: 10rpx;
  padding: 20rpx;
}

.stats-grid {
  display: flex;
  justify-content: space-around;
  margin-top: 20rpx;
}

.stats-item {
  text-align: center;
}

.stats-value {
  font-size: 36rpx;
  font-weight: bold;
  color: #007AFF;
  display: block;
  margin-bottom: 10rpx;
}

.stats-label {
  font-size: 24rpx;
  color: #666;
}
</style>