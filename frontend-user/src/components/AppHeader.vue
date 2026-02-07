<template>
  <header class="app-header">
    <div class="header-content">
      <div class="header-left">
        <div class="logo" @click="router.push('/')">
          <el-icon :size="28" color="#409eff"><Reading /></el-icon>
          <span class="logo-text">图书商城</span>
        </div>
        
        <el-menu
          :default-active="activeMenu"
          mode="horizontal"
          :ellipsis="false"
          class="nav-menu"
          @select="handleMenuSelect"
        >
          <el-menu-item index="/">首页</el-menu-item>
          <el-sub-menu index="books">
            <template #title>图书管理</template>
            <el-menu-item index="/books">图书列表</el-menu-item>
          </el-sub-menu>
          <el-sub-menu index="orders">
            <template #title>订单管理</template>
            <el-menu-item index="/orders">订单列表</el-menu-item>
          </el-sub-menu>
        </el-menu>
      </div>
      
      <div class="header-right">
        <div class="search-box">
          <el-input
            v-model="searchKeyword"
            placeholder="搜索图书..."
            :prefix-icon="Search"
            clearable
            @keyup.enter="handleSearch"
            @clear="handleSearch"
          />
        </div>
        
        <div class="header-actions">
          <el-badge :value="cartStore.totalCount" :hidden="cartStore.totalCount === 0" class="cart-badge">
            <el-button :icon="ShoppingCart" @click="router.push('/cart')">
              购物车
            </el-button>
          </el-badge>
          
          <template v-if="userStore.isLoggedIn">
            <el-dropdown @command="handleCommand">
              <span class="user-info">
                <el-icon><User /></el-icon>
                {{ userStore.userInfo?.nickname }}
                <el-icon class="el-icon--right"><ArrowDown /></el-icon>
              </span>
              <template #dropdown>
                <el-dropdown-menu>
                  <el-dropdown-item command="profile">个人中心</el-dropdown-item>
                  <el-dropdown-item command="orders">我的订单</el-dropdown-item>
                  <el-dropdown-item command="logout">退出登录</el-dropdown-item>
                </el-dropdown-menu>
              </template>
            </el-dropdown>
          </template>
        </div>
      </div>
    </div>
  </header>
</template>

<script setup>
import { ref, computed } from 'vue'
import { useRouter, useRoute } from 'vue-router'
import { Search, ShoppingCart, User, ArrowDown, Reading } from '@element-plus/icons-vue'
import { useCartStore } from '@/stores/cart'
import { useUserStore } from '@/stores/user'
import { ElMessage } from 'element-plus'

const router = useRouter()
const route = useRoute()
const cartStore = useCartStore()
const userStore = useUserStore()

const searchKeyword = ref('')

const activeMenu = computed(() => {
  return route.path
})

function handleMenuSelect(index) {
  router.push(index)
}

function handleSearch() {
  router.push({
    path: '/',
    query: searchKeyword.value ? { keyword: searchKeyword.value } : {}
  })
}

function handleCommand(command) {
  if (command === 'profile') {
    router.push('/profile')
  } else if (command === 'orders') {
    router.push('/orders')
  } else if (command === 'logout') {
    userStore.logout()
    ElMessage.success('已退出登录')
    router.push('/login')
  }
}
</script>

<style lang="scss" scoped>
.app-header {
  background: #fff;
  box-shadow: 0 2px 12px 0 rgba(0, 0, 0, 0.1);
  position: sticky;
  top: 0;
  z-index: 100;
}

.header-content {
  max-width: 1400px;
  margin: 0 auto;
  padding: 0 24px;
  display: flex;
  align-items: center;
  justify-content: space-between;
  height: 60px;
}

.header-left {
  display: flex;
  align-items: center;
  gap: 24px;
}

.logo {
  display: flex;
  align-items: center;
  gap: 8px;
  cursor: pointer;
  flex-shrink: 0;
  
  .logo-text {
    font-size: 20px;
    font-weight: 600;
    color: #303133;
  }
}

.nav-menu {
  border-bottom: none;
  
  :deep(.el-menu-item),
  :deep(.el-sub-menu__title) {
    height: 60px;
    line-height: 60px;
  }
}

.header-right {
  display: flex;
  align-items: center;
  gap: 24px;
}

.search-box {
  width: 280px;
}

.header-actions {
  display: flex;
  align-items: center;
  gap: 16px;
  flex-shrink: 0;
}

.cart-badge {
  :deep(.el-badge__content) {
    top: 8px;
    right: 14px;
  }
}

.user-info {
  display: flex;
  align-items: center;
  gap: 4px;
  cursor: pointer;
  color: #606266;
  
  &:hover {
    color: #409eff;
  }
}
</style>
