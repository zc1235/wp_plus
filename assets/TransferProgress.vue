<template>
  <div class="transfer-progress-container" v-if="showTransferProgress">
    <button class="transfer-progress-button" @click="toggleTransferProgress">
      <svg width="20" height="20" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2">
        <path d="M21 15v4a2 2 0 0 1-2 2H5a2 2 0 0 1-2-2v-4"/>
        <polyline points="17,8 12,3 7,8"/>
        <line x1="12" y1="3" x2="12" y2="15"/>
      </svg>
      <span>{{ activeTransfers > 0 ? activeTransfers + ' 项' : '' }}</span>
    </button>
    
    <Teleport to="body">
      <div 
        v-if="isOpen" 
        class="transfer-progress-popup"
        @click.self="closePopup"
      >
        <div class="transfer-progress-content">
          <div class="transfer-header">
            <h3>文件传输</h3>
            <button class="close-btn" @click="closePopup">&times;</button>
          </div>
          
          <div class="transfer-list" v-if="transfers.length > 0">
            <div 
              v-for="(transfer, index) in transfers" 
              :key="index" 
              class="transfer-item"
            >
              <div class="transfer-info">
                <div class="transfer-name">{{ transfer.name }}</div>
                <div class="transfer-status">
                  <span class="transfer-percent">{{ Math.round(transfer.progress) }}%</span>
                  <span class="transfer-size">{{ formatFileSize(transfer.loaded) }} / {{ formatFileSize(transfer.total) }}</span>
                </div>
              </div>
              
              <div class="progress-bar-container">
                <div 
                  class="progress-bar" 
                  :style="{ width: transfer.progress + '%' }"
                  :class="transfer.status"
                ></div>
              </div>
            </div>
          </div>
          
          <div class="no-transfers" v-else>
            <p>暂无传输任务</p>
          </div>
        </div>
      </div>
    </Teleport>
  </div>
</template>

<script>
export default {
  name: 'TransferProgress',
  props: {
    transfers: {
      type: Array,
      default: () => []
    },
    showTransferProgress: {
      type: Boolean,
      default: true
    }
  },
  data() {
    return {
      isOpen: false
    };
  },
  computed: {
    activeTransfers() {
      return this.transfers.filter(transfer => transfer.status !== 'completed').length;
    }
  },
  methods: {
    toggleTransferProgress() {
      this.isOpen = !this.isOpen;
    },
    closePopup() {
      this.isOpen = false;
    },
    formatFileSize(bytes) {
      if (bytes === 0) return '0 Bytes';
      const k = 1024;
      const sizes = ['Bytes', 'KB', 'MB', 'GB'];
      const i = Math.floor(Math.log(bytes) / Math.log(k));
      return parseFloat((bytes / Math.pow(k, i)).toFixed(2)) + ' ' + sizes[i];
    }
  }
};
</script>

<!-- 方式1：在 TransferProgress.vue 中修改根节点样式 -->
<style scoped>
.transfer-progress-container {
  /* 替换原有的 display: inline-block */
  display: block !important; 
  /* 强制固定在页面右上角，避免位置偏移 */
  position: fixed !important;
  top: 20px !important;
  right: 20px !important;
  /* 加高z-index，避免被其他元素遮挡 */
  z-index: 9999 !important;
  /* 加红色边框，可视化元素范围（调试用） */
  border: 1px solid red !important;
  padding: 10px !important;
}

/* 同时确保按钮本身可见 */
.transfer-progress-button {
  /* 加背景色，避免和页面背景融合 */
  background-color: #f0f0f0 !important;
  /* 强制显示按钮内的SVG图标 */
  color: #333 !important; /* SVG的stroke是currentColor，设置颜色确保可见 */
  /* 加最小宽高，避免按钮塌陷 */
  min-width: 40px !important;
  min-height: 40px !important;
}
</style>