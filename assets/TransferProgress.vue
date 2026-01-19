<template>
  <div class="transfer-progress-container" v-if="showTransferProgress">
    <button class="transfer-progress-button" @click="toggleTransferProgress">
      <svg width="20" height="20" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2">
        <path d="M21 15v4a2 2 0 0 1-2 2H5a2 2 0 0 1-2-2v-4"/>
        <polyline points="17,8 12,3 7,8"/>
        <line x1="12" y1="3" x2="12" y2="15"/>
      </svg>
      <span v-if="activeTransfers > 0">{{ activeTransfers }} 项</span>
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

<style scoped>
.transfer-progress-container {
  display: inline-block;
}

.transfer-progress-button {
  display: flex;
  align-items: center;
  gap: 6px;
  padding: 8px 12px;
  background-color: #f0f0f0;
  border: 1px solid #ddd;
  border-radius: 20px;
  cursor: pointer;
  font-size: 14px;
  color: #333;
  transition: all 0.3s ease;
}

.transfer-progress-button:hover {
  background-color: #e0e0e0;
}

.transfer-progress-popup {
  position: fixed;
  top: 0;
  left: 0;
  width: 100%;
  height: 100%;
  background-color: rgba(0, 0, 0, 0.5);
  display: flex;
  justify-content: flex-end;
  z-index: 1000;
}

.transfer-progress-content {
  width: 350px;
  max-width: 90vw;
  height: 100%;
  background-color: white;
  box-shadow: -2px 0 10px rgba(0, 0, 0, 0.1);
  display: flex;
  flex-direction: column;
  animation: slideInRight 0.3s ease;
}

@keyframes slideInRight {
  from {
    transform: translateX(100%);
  }
  to {
    transform: translateX(0);
  }
}

.transfer-header {
  display: flex;
  justify-content: space-between;
  align-items: center;
  padding: 16px;
  border-bottom: 1px solid #eee;
  background-color: #f9f9f9;
}

.transfer-header h3 {
  margin: 0;
  font-size: 18px;
  color: #333;
}

.close-btn {
  background: none;
  border: none;
  font-size: 24px;
  cursor: pointer;
  color: #999;
  padding: 0;
  width: 30px;
  height: 30px;
  display: flex;
  align-items: center;
  justify-content: center;
}

.close-btn:hover {
  color: #333;
  background-color: #e0e0e0;
  border-radius: 50%;
}

.transfer-list {
  flex: 1;
  overflow-y: auto;
  padding: 16px;
}

.no-transfers {
  display: flex;
  align-items: center;
  justify-content: center;
  height: 100%;
  color: #999;
  font-style: italic;
}

.transfer-item {
  margin-bottom: 16px;
  padding-bottom: 16px;
  border-bottom: 1px solid #f0f0f0;
}

.transfer-item:last-child {
  margin-bottom: 0;
  padding-bottom: 0;
  border-bottom: none;
}

.transfer-info {
  margin-bottom: 8px;
}

.transfer-name {
  font-weight: 500;
  color: #333;
  margin-bottom: 4px;
  white-space: nowrap;
  overflow: hidden;
  text-overflow: ellipsis;
}

.transfer-status {
  display: flex;
  justify-content: space-between;
  font-size: 12px;
  color: #666;
}

.transfer-percent {
  font-weight: bold;
  color: #007bff;
}

.progress-bar-container {
  width: 100%;
  height: 6px;
  background-color: #f0f0f0;
  border-radius: 3px;
  overflow: hidden;
}

.progress-bar {
  height: 100%;
  width: 0%;
  background-color: #007bff;
  transition: width 0.3s ease;
}

.progress-bar.uploading {
  background-color: #007bff;
}

.progress-bar.completed {
  background-color: #28a745;
}

.progress-bar.failed {
  background-color: #dc3545;
}
</style>