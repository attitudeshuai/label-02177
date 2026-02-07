<template>
  <footer class="app-footer">
    <div class="footer-content">
      <div class="footer-info">
        <el-icon :size="24" color="#409eff"><Reading /></el-icon>
        <span>图书购物商城</span>
      </div>
      <div class="copyright">
        © 2024 图书商城 版权所有
      </div>
    </div>
  </footer>
</template>

<script setup>
import { Reading } from '@element-plus/icons-vue'
</script>

<style lang="scss" scoped>
.app-footer {
  background: #fff;
  border-top: 1px solid #e4e7ed;
  margin-top: auto;
}

.footer-content {
  max-width: 1200px;
  margin: 0 auto;
  padding: 12px;
  text-align: center;
}

.footer-info {
  display: flex;
  align-items: center;
  justify-content: center;
  gap: 8px;
  font-size: 18px;
  font-weight: 600;
  color: #303133;
  margin-bottom: 16px;
}

.footer-links {
  margin-bottom: 16px;
  
  a {
    color: #606266;
    font-size: 14px;
    
    &:hover {
      color: #409eff;
    }
  }
}

.copyright {
  color: #909399;
  font-size: 12px;
}
</style>
