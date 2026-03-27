<template>
  <BaseCard hoverable clickable @click="goToDetail">
    <template #image>
      <img v-lazy="imageUrl" :alt="spot.name" class="spot-image">
      <span class="spot-category">{{ spot.categoryName }}</span>
      <button @click.stop="toggleFavorite" class="favorite-button">
        <span :class="['star-icon', { 'is-favorite': isFavorite }]">⭐</span>
      </button>
    </template>

    <div class="spot-info">
      <h3 class="spot-name">{{ spot.name }}</h3>
      <RatingStars :rating="spot.rating" />
      <p class="spot-address">📍 {{ spot.address }}</p>
      <p class="spot-desc">{{ spot.description }}</p>
    </div>

    <template #footer>
      <div class="spot-footer">
        <span class="spot-ticket">🎫 {{ spot.ticket }}</span>
        <span class="spot-time">🕐 {{ spot.openTime }}</span>
      </div>
    </template>
  </BaseCard>
</template>

<script setup>
import { computed, ref, onMounted } from 'vue'
import { useRouter } from 'vue-router'
import { getPlaceholderImage } from '@/utils/helpers'
import BaseCard from '@/components/common/BaseCard.vue'
import RatingStars from '@/components/common/RatingStars.vue'

const props = defineProps({
  spot: {
    type: Object,
    required: true
  }
})

const router = useRouter()
const isFavorite = ref(false)

const imageUrl = computed(() => getPlaceholderImage(props.spot.image))

// 从localStorage读取收藏状态
function loadFavoriteStatus() {
  try {
    const favorites = JSON.parse(localStorage.getItem('spotFavorites') || '[]')
    isFavorite.value = favorites.includes(props.spot.id)
  } catch (e) {
    console.error('Error loading favorites from localStorage:', e)
  }
}

// 切换收藏状态
function toggleFavorite() {
  try {
    let favorites = JSON.parse(localStorage.getItem('spotFavorites') || '[]')

    if (isFavorite.value) {
      // 移除收藏
      favorites = favorites.filter(id => id !== props.spot.id)
    } else {
      // 添加收藏
      favorites.push(props.spot.id)
    }

    localStorage.setItem('spotFavorites', JSON.stringify(favorites))
    isFavorite.value = !isFavorite.value
  } catch (e) {
    console.error('Error saving favorite to localStorage:', e)
  }
}

function goToDetail() {
  router.push(`/spot/${props.spot.id}`)
}

// 组件挂载时加载收藏状态
onMounted(loadFavoriteStatus)
</script>

<style scoped>
.spot-image {
  width: 100%;
  height: 180px;
  object-fit: cover;
}

.spot-category {
  position: absolute;
  top: var(--spacing-sm);
  left: var(--spacing-sm);
  padding: var(--spacing-xs) var(--spacing-sm);
  font-size: var(--font-size-xs);
  font-weight: 500;
  color: white;
  background: rgba(0, 0, 0, 0.6);
  border-radius: var(--border-radius-sm);
}

.favorite-button {
  position: absolute;
  top: var(--spacing-sm);
  right: var(--spacing-sm);
  background: rgba(0, 0, 0, 0.6);
  border: none;
  border-radius: var(--border-radius-sm);
  padding: var(--spacing-xs);
  cursor: pointer;
  transition: all 0.2s ease;
  z-index: 10;
}

.favorite-button:hover {
  background: rgba(0, 0, 0, 0.8);
  transform: scale(1.1);
}

.star-icon {
  font-size: var(--font-size-lg);
  color: rgba(255, 255, 255, 0.6);
  transition: color 0.2s ease;
}

.star-icon.is-favorite {
  color: #ffc107;
  text-shadow: 0 0 8px rgba(255, 193, 7, 0.6);
}

.spot-info {
  display: flex;
  flex-direction: column;
  gap: var(--spacing-sm);
}

.spot-name {
  font-size: var(--font-size-lg);
  font-weight: 600;
  color: var(--text-primary);
}

.spot-address {
  font-size: var(--font-size-sm);
  color: var(--text-secondary);
}

.spot-desc {
  font-size: var(--font-size-sm);
  color: var(--text-muted);
  display: -webkit-box;
  -webkit-line-clamp: 2;
  -webkit-box-orient: vertical;
  overflow: hidden;
}

.spot-footer {
  display: flex;
  justify-content: space-between;
  font-size: var(--font-size-xs);
  color: var(--text-secondary);
}
</style>
