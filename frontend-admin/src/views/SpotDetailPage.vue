<template>
  <div v-if="spot" class="spot-detail-page">
    <!-- 返回按钮 -->
    <button class="back-btn" @click="goBack">← 返回列表</button>

    <!-- 主图区域 -->
    <div class="hero-image">
      <img :src="imageUrl" :alt="spot.name">
      <div class="hero-overlay">
        <span class="spot-category">{{ spot.categoryName }}</span>
        <h1 class="spot-name">{{ spot.name }}</h1>
        <RatingStars :rating="spot.rating" />
      </div>
    </div>

    <!-- 详情内容 -->
    <div class="detail-content">
      <!-- 基本信息卡片 -->
      <BaseCard class="info-card">
        <template #header>📋 基本信息</template>
        <div class="info-grid">
          <div class="info-item">
            <span class="info-label">📍 地址</span>
            <span class="info-value">{{ spot.address }}</span>
          </div>
          <div class="info-item">
            <span class="info-label">🕐 开放时间</span>
            <span class="info-value">{{ spot.openTime }}</span>
          </div>
          <div class="info-item">
            <span class="info-label">🎫 门票</span>
            <span class="info-value">{{ spot.ticket }}</span>
          </div>
        </div>
      </BaseCard>

      <!-- 景点介绍 -->
      <BaseCard class="intro-card">
        <template #header>📖 景点介绍</template>
        <p class="description">{{ spot.fullDescription }}</p>
      </BaseCard>

      <!-- 游玩攻略 -->
      <BaseCard class="tips-card">
        <template #header>💡 游玩攻略</template>
        <ul class="tips-list">
          <li v-for="(tip, index) in spot.tips" :key="index" class="tip-item">
            <span class="tip-icon">✓</span>
            {{ tip }}
          </li>
        </ul>
      </BaseCard>
    </div>
  </div>

  <!-- 404状态 -->
  <div v-else class="not-found">
    <span class="not-found-icon">😕</span>
    <h2>景点不存在</h2>
    <p>您访问的景点可能已被移除</p>
    <BaseButton variant="primary" @click="goBack">返回列表</BaseButton>
  </div>
</template>

<script setup>
import { computed } from 'vue'
import { useRoute, useRouter } from 'vue-router'
import { useSpotsStore } from '@/stores/spots'
import { getPlaceholderImage } from '@/utils/helpers'
import BaseCard from '@/components/common/BaseCard.vue'
import BaseButton from '@/components/common/BaseButton.vue'
import RatingStars from '@/components/common/RatingStars.vue'

const route = useRoute()
const router = useRouter()
const spotsStore = useSpotsStore()

const spot = computed(() => spotsStore.getSpotById(route.params.id))
const imageUrl = computed(() => spot.value ? getPlaceholderImage(spot.value.image, 800, 400) : '')

function goBack() {
  router.push('/spots')
}
</script>

<style scoped>
.spot-detail-page {
  display: flex;
  flex-direction: column;
  gap: var(--spacing-lg);
  margin-top: 24px;
}

.back-btn {
  display: inline-flex;
  align-items: center;
  gap: var(--spacing-xs);
  padding: var(--spacing-sm) var(--spacing-md);
  font-size: var(--font-size-sm);
  color: var(--text-secondary);
  background: var(--bg-primary);
  border: 1px solid var(--border-color);
  border-radius: var(--border-radius-md);
  cursor: pointer;
  transition: all 0.2s;
  width: fit-content;
}

.back-btn:hover {
  color: var(--primary);
  border-color: var(--primary);
}

/* Hero Image */
.hero-image {
  position: relative;
  border-radius: var(--border-radius-xl);
  overflow: hidden;
}

.hero-image img {
  width: 100%;
  height: 300px;
  object-fit: cover;
}

.hero-overlay {
  position: absolute;
  bottom: 0;
  left: 0;
  right: 0;
  padding: var(--spacing-xl);
  background: linear-gradient(transparent, rgba(0, 0, 0, 0.8));
  color: white;
}

.spot-category {
  display: inline-block;
  padding: var(--spacing-xs) var(--spacing-sm);
  font-size: var(--font-size-xs);
  background: var(--primary);
  border-radius: var(--border-radius-sm);
  margin-bottom: var(--spacing-sm);
}

.spot-name {
  font-size: var(--font-size-2xl);
  font-weight: 700;
  margin-bottom: var(--spacing-sm);
  color: #fff;
}

.favorite-btn {
  position: absolute;
  top: var(--spacing-md);
  right: var(--spacing-md);
  padding: var(--spacing-sm) var(--spacing-md);
  font-size: var(--font-size-sm);
  background: rgba(255, 255, 255, 0.95);
  border: none;
  border-radius: var(--border-radius-md);
  cursor: pointer;
  transition: all 0.2s;
}

.favorite-btn:hover {
  transform: scale(1.05);
}

.favorite-btn.active {
  background: #fee2e2;
}

/* Detail Content */
.detail-content {
  display: flex;
  flex-direction: column;
  gap: var(--spacing-lg);
}

/* 基本信息卡片 - 与首页分类浏览协调的深蓝色调 */
.info-card {
  background: linear-gradient(135deg, #f0f9ff 0%, #e0f2fe 100%);
  border: 1px solid #bae6fd;
}

.info-card :deep(.card-header) {
  background: linear-gradient(135deg, #1e3a5f 0%, #2d4a6f 100%);
  color: white;
  border-bottom: none;
}

/* 景点介绍卡片 - 与首页Hero区域协调的绿色调 */
.intro-card {
  background: linear-gradient(135deg, #f0fdf4 0%, #ecfeff 100%);
  border: 1px solid #a7f3d0;
}

.intro-card :deep(.card-header) {
  background: linear-gradient(135deg, #047857 0%, #0f766e 100%);
  color: white;
  border-bottom: none;
}

/* 游玩攻略卡片 - 与首页精选路线协调的暖黄色调 */
.tips-card {
  background: linear-gradient(135deg, #fffbeb 0%, #fef3c7 100%);
  border: 1px solid #fde68a;
}

.tips-card :deep(.card-header) {
  background: linear-gradient(135deg, #d97706 0%, #b45309 100%);
  color: white;
  border-bottom: none;
}

.info-grid {
  display: grid;
  grid-template-columns: repeat(auto-fit, minmax(200px, 1fr));
  gap: var(--spacing-md);
}

.info-item {
  display: flex;
  flex-direction: column;
  gap: var(--spacing-xs);
}

.info-label {
  font-size: var(--font-size-sm);
  color: var(--text-muted);
}

.info-value {
  font-size: var(--font-size-md);
  color: var(--text-primary);
  font-weight: 500;
}

.description {
  font-size: var(--font-size-md);
  color: var(--text-secondary);
  line-height: 1.8;
}

.tips-list {
  display: flex;
  flex-direction: column;
  gap: var(--spacing-md);
}

.tip-item {
  display: flex;
  align-items: flex-start;
  gap: var(--spacing-sm);
  font-size: var(--font-size-md);
  color: var(--text-secondary);
}

.tip-icon {
  color: #d97706;
  font-weight: bold;
  background: #fef3c7;
  padding: 2px 6px;
  border-radius: 4px;
}

/* Not Found */
.not-found {
  display: flex;
  flex-direction: column;
  align-items: center;
  gap: var(--spacing-md);
  padding: var(--spacing-xl);
  background: var(--bg-primary);
  border-radius: var(--border-radius-xl);
  text-align: center;
}

.not-found-icon {
  font-size: 64px;
}

.not-found h2 {
  font-size: var(--font-size-xl);
  color: var(--text-primary);
}

.not-found p {
  color: var(--text-muted);
}

@media (max-width: 768px) {
  .hero-image img {
    height: 200px;
  }

  .action-buttons {
    flex-direction: column;
  }

  .action-buttons .btn {
    width: 100%;
  }
}
</style>
