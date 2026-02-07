<template>
  <div class="book-card" @click="goToDetail">
    <div class="book-cover">
      <el-image :src="book.cover" fit="cover" lazy>
        <template #error>
          <div class="image-error">
            <el-icon :size="40"><Picture /></el-icon>
          </div>
        </template>
      </el-image>
      <div class="book-tag" v-if="discount > 0">
        {{ discount }}折
      </div>
    </div>
    
    <div class="book-info">
      <h3 class="book-title" :title="book.title">{{ book.title }}</h3>
      <p class="book-author">{{ book.author }}</p>
      <div class="book-price">
        <span class="current-price">¥{{ book.price.toFixed(2) }}</span>
        <span class="original-price" v-if="book.originalPrice > book.price">
          ¥{{ book.originalPrice.toFixed(2) }}
        </span>
      </div>
      <div class="book-sales">已售 {{ book.sales }} 本</div>
    </div>
    
    <div class="book-actions">
      <el-button 
        type="primary" 
        :icon="ShoppingCart" 
        :loading="loading"
        @click.stop="handleAddToCart"
      >
        加入购物车
      </el-button>
    </div>
  </div>
</template>

<script setup>
import { ref, computed } from 'vue'
import { useRouter } from 'vue-router'
import { ShoppingCart, Picture } from '@element-plus/icons-vue'
import { useCartStore } from '@/stores/cart'
import { ElMessage } from 'element-plus'

const props = defineProps({
  book: {
    type: Object,
    required: true
  }
})

const router = useRouter()
const cartStore = useCartStore()
const loading = ref(false)

const discount = computed(() => {
  if (props.book.originalPrice > props.book.price) {
    return Math.round((props.book.price / props.book.originalPrice) * 10)
  }
  return 0
})

function goToDetail() {
  router.push(`/book/${props.book.id}`)
}

async function handleAddToCart() {
  loading.value = true
  
  // 网络请求
  await new Promise(resolve => setTimeout(resolve, 300))
  
  cartStore.addToCart(props.book)
  ElMessage.success('已添加到购物车')
  loading.value = false
}
</script>

<style lang="scss" scoped>
.book-card {
  background: #fff;
  border-radius: 8px;
  box-shadow: 0 2px 12px 0 rgba(0, 0, 0, 0.1);
  overflow: hidden;
  cursor: pointer;
  transition: all 0.3s ease;
  display: flex;
  flex-direction: column;
  
  &:hover {
    transform: translateY(-4px);
    box-shadow: 0 4px 16px 0 rgba(0, 0, 0, 0.15);
  }
}

.book-cover {
  position: relative;
  height: 200px;
  background: #f5f7fa;
  
  .el-image {
    width: 100%;
    height: 100%;
  }
  
  .image-error {
    width: 100%;
    height: 100%;
    display: flex;
    align-items: center;
    justify-content: center;
    background: #f5f7fa;
    color: #c0c4cc;
  }
  
  .book-tag {
    position: absolute;
    top: 8px;
    right: 8px;
    background: #f56c6c;
    color: #fff;
    padding: 2px 8px;
    border-radius: 4px;
    font-size: 12px;
    font-weight: 600;
  }
}

.book-info {
  padding: 16px;
  flex: 1;
}

.book-title {
  font-size: 16px;
  font-weight: 600;
  color: #303133;
  margin-bottom: 8px;
  overflow: hidden;
  text-overflow: ellipsis;
  white-space: nowrap;
}

.book-author {
  font-size: 13px;
  color: #909399;
  margin-bottom: 8px;
}

.book-price {
  display: flex;
  align-items: baseline;
  gap: 8px;
  margin-bottom: 4px;
  
  .current-price {
    font-size: 18px;
    font-weight: 600;
    color: #f56c6c;
  }
  
  .original-price {
    font-size: 13px;
    color: #c0c4cc;
    text-decoration: line-through;
  }
}

.book-sales {
  font-size: 12px;
  color: #909399;
}

.book-actions {
  padding: 0 16px 16px;
  
  .el-button {
    width: 100%;
  }
}
</style>
