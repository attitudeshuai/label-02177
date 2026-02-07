<template>
  <div class="home-page">
    <!-- 分类筛选 -->
    <div class="category-section card">
      <div class="category-title">图书分类</div>
      <div class="category-list">
        <el-tag
          v-for="cat in bookStore.categories"
          :key="cat"
          :type="activeCategory === cat ? '' : 'info'"
          :effect="activeCategory === cat ? 'dark' : 'plain'"
          class="category-tag"
          @click="handleCategoryChange(cat)"
        >
          {{ cat }}
        </el-tag>
      </div>
    </div>
    
    <!-- 图书列表 -->
    <div class="books-section">
      <div class="section-header">
        <h2 class="section-title">
          {{ activeCategory === '全部' ? '全部图书' : activeCategory }}
          <span class="book-count">({{ filteredBooks.length }}本)</span>
        </h2>
        <el-select v-model="sortBy" placeholder="排序方式" style="width: 140px">
          <el-option label="默认排序" value="default" />
          <el-option label="价格从低到高" value="price-asc" />
          <el-option label="价格从高到低" value="price-desc" />
          <el-option label="销量优先" value="sales" />
        </el-select>
      </div>
      
      <div class="books-grid" v-if="filteredBooks.length > 0">
        <BookCard 
          v-for="book in sortedBooks" 
          :key="book.id" 
          :book="book" 
        />
      </div>
      
      <el-empty v-else description="暂无相关图书" />
    </div>
  </div>
</template>

<script setup>
import { ref, computed, watch, onMounted } from 'vue'
import { useRoute } from 'vue-router'
import { useBookStore } from '@/stores/book'
import BookCard from '@/components/BookCard.vue'

const route = useRoute()
const bookStore = useBookStore()

const activeCategory = ref('全部')
const sortBy = ref('default')

const filteredBooks = computed(() => {
  let books = bookStore.getBooksByCategory(activeCategory.value)
  
  // 搜索过滤
  const keyword = route.query.keyword
  if (keyword) {
    const lowerKeyword = keyword.toLowerCase()
    books = books.filter(
      book =>
        book.title.toLowerCase().includes(lowerKeyword) ||
        book.author.toLowerCase().includes(lowerKeyword)
    )
  }
  
  return books
})

const sortedBooks = computed(() => {
  const books = [...filteredBooks.value]
  
  switch (sortBy.value) {
    case 'price-asc':
      return books.sort((a, b) => a.price - b.price)
    case 'price-desc':
      return books.sort((a, b) => b.price - a.price)
    case 'sales':
      return books.sort((a, b) => b.sales - a.sales)
    default:
      return books
  }
})

function handleCategoryChange(category) {
  activeCategory.value = category
}

watch(() => route.query.keyword, () => {
  activeCategory.value = '全部'
})
</script>

<style lang="scss" scoped>
.home-page {
  display: flex;
  flex-direction: column;
  gap: 24px;
}

.card {
  background: #fff;
  border-radius: 8px;
  box-shadow: 0 2px 12px 0 rgba(0, 0, 0, 0.1);
  padding: 24px;
}

.category-section {
  display: flex;
  gap: 16px;
  align-items: center;
  .category-title {
    font-size: 16px;
    font-weight: 600;
    color: #303133;
  }
  
  .category-list {
    display: flex;
    flex-wrap: wrap;
    gap: 12px;
  }
  
  .category-tag {
    cursor: pointer;
    font-size: 14px;
    padding: 8px 16px;
    
    &:hover {
      opacity: 0.8;
    }
  }
}

.books-section {
  background: #fff;
  border-radius: 8px;
  box-shadow: 0 2px 12px 0 rgba(0, 0, 0, 0.1);
  padding: 24px;
}

.section-header {
  display: flex;
  justify-content: space-between;
  align-items: center;
  margin-bottom: 24px;
}

.section-title {
  font-size: 20px;
  font-weight: 600;
  color: #303133;
  
  .book-count {
    font-size: 14px;
    font-weight: 400;
    color: #909399;
    margin-left: 8px;
  }
}

.books-grid {
  display: grid;
  grid-template-columns: repeat(auto-fill, minmax(220px, 1fr));
  gap: 24px;
}
</style>
