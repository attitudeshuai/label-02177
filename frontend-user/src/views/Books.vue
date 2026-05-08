<template>
  <div class="books-page">
    <div class="page-header">
      <h1 class="page-title">图书列表</h1>
      <div class="header-actions">
        <el-input
          v-model="searchKeyword"
          placeholder="搜索书名/作者"
          :prefix-icon="Search"
          clearable
          style="width: 220px"
        />
        <el-select v-model="filterCategory" placeholder="分类筛选" clearable style="width: 130px">
          <el-option 
            v-for="cat in bookStore.categories.filter(c => c !== '全部')" 
            :key="cat" 
            :label="cat" 
            :value="cat" 
          />
        </el-select>
        <el-select v-model="priceRange" placeholder="价格区间" clearable style="width: 140px">
          <el-option 
            v-for="range in priceRanges" 
            :key="range.value" 
            :label="range.label" 
            :value="range.value" 
          />
        </el-select>
        <el-select v-model="sortBy" placeholder="排序" style="width: 140px">
          <el-option label="默认排序" value="default" />
          <el-option label="评分从高到低" value="rating-desc" />
          <el-option label="价格从低到高" value="price-asc" />
          <el-option label="价格从高到低" value="price-desc" />
          <el-option label="销量优先" value="sales" />
        </el-select>
      </div>
    </div>
    
    <div class="books-table card">
      <el-table :data="filteredBooks" stripe style="width: 100%">
        <el-table-column label="封面" width="100" align="center">
          <template #default="{ row }">
            <el-image :src="row.cover" fit="cover" class="book-cover">
              <template #error>
                <div class="image-error">
                  <el-icon><Picture /></el-icon>
                </div>
              </template>
            </el-image>
          </template>
        </el-table-column>
        <el-table-column prop="title" label="书名" min-width="180" align="center">
          <template #default="{ row }">
            <span class="book-title" @click="goToDetail(row.id)">{{ row.title }}</span>
          </template>
        </el-table-column>
        <el-table-column prop="author" label="作者" width="140" align="center" />
        <el-table-column prop="category" label="分类" width="100" align="center">
          <template #default="{ row }">
            <el-tag size="small">{{ row.category }}</el-tag>
          </template>
        </el-table-column>
        <el-table-column prop="price" label="价格" width="100" align="center">
          <template #default="{ row }">
            <span class="price">¥{{ row.price.toFixed(2) }}</span>
          </template>
        </el-table-column>
        <el-table-column prop="rating" label="评分" width="80" align="center">
          <template #default="{ row }">
            <span class="rating">⭐ {{ row.rating }}</span>
          </template>
        </el-table-column>
        <el-table-column prop="stock" label="库存" width="80" align="center" />
        <el-table-column prop="sales" label="销量" width="80" align="center" />
        <el-table-column label="操作" width="160" align="center" fixed="right">
          <template #default="{ row }">
            <el-button type="primary" text @click="goToDetail(row.id)">
              查看
            </el-button>
            <el-button 
              type="success" 
              text 
              :loading="addingId === row.id"
              @click="handleAddToCart(row)"
            >
              加购
            </el-button>
          </template>
        </el-table-column>
      </el-table>
    </div>
  </div>
</template>

<script setup>
import { ref, computed } from 'vue'
import { useRouter } from 'vue-router'
import { Search, Picture } from '@element-plus/icons-vue'
import { useBookStore } from '@/stores/book'
import { useCartStore } from '@/stores/cart'
import { ElMessage } from 'element-plus'

const router = useRouter()
const bookStore = useBookStore()
const cartStore = useCartStore()

const searchKeyword = ref('')
const filterCategory = ref('')
const priceRange = ref('')
const sortBy = ref('default')
const addingId = ref(null)

const priceRanges = [
  { value: 'below50', label: '50元以下' },
  { value: 'between50and100', label: '50-100元' },
  { value: 'above100', label: '100元以上' }
]

const filteredBooks = computed(() => {
  let books = bookStore.books
  
  if (filterCategory.value) {
    books = books.filter(book => book.category === filterCategory.value)
  }
  
  if (priceRange.value) {
    switch (priceRange.value) {
      case 'below50':
        books = books.filter(book => book.price < 50)
        break
      case 'between50and100':
        books = books.filter(book => book.price >= 50 && book.price <= 100)
        break
      case 'above100':
        books = books.filter(book => book.price > 100)
        break
    }
  }
  
  if (searchKeyword.value) {
    const keyword = searchKeyword.value.toLowerCase()
    books = books.filter(
      book =>
        book.title.toLowerCase().includes(keyword) ||
        book.author.toLowerCase().includes(keyword)
    )
  }
  
  if (sortBy.value !== 'default') {
    const sorted = [...books]
    switch (sortBy.value) {
      case 'rating-desc':
        sorted.sort((a, b) => b.rating - a.rating)
        break
      case 'price-asc':
        sorted.sort((a, b) => a.price - b.price)
        break
      case 'price-desc':
        sorted.sort((a, b) => b.price - a.price)
        break
      case 'sales':
        sorted.sort((a, b) => b.sales - a.sales)
        break
    }
    books = sorted
  }
  
  return books
})

function goToDetail(id) {
  router.push(`/book/${id}`)
}

async function handleAddToCart(book) {
  addingId.value = book.id
  await new Promise(resolve => setTimeout(resolve, 300))
  cartStore.addToCart(book)
  ElMessage.success('已添加到购物车')
  addingId.value = null
}
</script>

<style lang="scss" scoped>
.books-page {
  .page-header {
    display: flex;
    justify-content: space-between;
    align-items: center;
    margin-bottom: 24px;
  }
  
  .page-title {
    font-size: 24px;
    font-weight: 600;
    color: #303133;
  }
  
  .header-actions {
    display: flex;
    gap: 12px;
  }
}

.card {
  background: #fff;
  border-radius: 8px;
  box-shadow: 0 2px 12px 0 rgba(0, 0, 0, 0.1);
  padding: 24px;
}

.book-cover {
  width: 50px;
  height: 70px;
  border-radius: 4px;
  
  .image-error {
    width: 100%;
    height: 100%;
    display: flex;
    align-items: center;
    justify-content: center;
    background: #f5f7fa;
    color: #c0c4cc;
  }
}

.book-title {
  color: #409eff;
  cursor: pointer;
  
  &:hover {
    text-decoration: underline;
  }
}

.price {
  color: #f56c6c;
  font-weight: 600;
}

.rating {
  color: #f7ba2a;
  font-weight: 500;
}
</style>
