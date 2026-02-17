<template>
  <div class="tabs-container">
    <div class="tabs">
      <button 
        v-for="tab in tabs" 
        :key="tab.id"
        class="tab-btn" 
        :class="{ active: activeTab === tab.id }"
        @click="switchTab(tab.id)"
      >
        {{ tab.name }}
      </button>
    </div>
  </div>
</template>

<script setup lang="ts">


// 类型定义
interface Tab {
  id: string;
  name: string;
}

// Props
const props = defineProps<{
  activeTab: string;
}>();

// Emits
const emit = defineEmits<{
  'update:activeTab': [tabId: string];
}>();

// 选项卡列表
const tabs: Tab[] = [
  { id: 'general', name: '总编' },
  { id: 'center-text', name: '中心文字' },
  { id: 'logo', name: '标志' },
  { id: 'footer', name: '落款' }
];

// 切换选项卡
const switchTab = (tabId: string) => {
  emit('update:activeTab', tabId);
};
</script>

<style scoped>
.tabs-container {
  position: fixed;
  top: 0;
  left: 0;
  right: 0;
  z-index: 1000;
  width: 100%;
  max-width: 1200px;
  margin: 0 auto;
  padding: 0.8rem 1rem;
  box-sizing: border-box;
  background: rgba(255, 255, 255, 0.95);
  backdrop-filter: blur(10px);
  box-shadow: 0 2px 12px rgba(0, 0, 0, 0.1);
  border-bottom: 1px solid rgba(0, 0, 0, 0.1);
}

.tabs {
  display: flex;
  gap: 0.5rem;
  background: rgba(255, 255, 255, 0.9);
  backdrop-filter: blur(10px);
  border-radius: 12px;
  padding: 0.4rem;
  box-shadow: 0 2px 8px rgba(0, 0, 0, 0.1);
  border: 1px solid rgba(0, 0, 0, 0.1);
  overflow-x: auto;
  scrollbar-width: none;
  -ms-overflow-style: none;
}

.tabs::-webkit-scrollbar {
  display: none;
}

.tab-btn {
  flex: 1;
  min-width: 80px;
  padding: 0.6rem 1rem;
  border: none;
  border-radius: 8px;
  background: transparent;
  color: #555;
  font-size: 0.9rem;
  font-weight: 500;
  cursor: pointer;
  transition: all 0.2s ease;
  white-space: nowrap;
  position: relative;
}

.tab-btn:hover {
  color: #333;
  background: rgba(0, 0, 0, 0.05);
  transform: translateY(-1px);
}

.tab-btn.active {
  color: white;
  background: #333;
  box-shadow: 0 2px 8px rgba(0, 0, 0, 0.2);
  transform: translateY(-1px);
}

.tab-btn.active:hover {
  background: #222;
}

/* 移动端布局 */
@media (max-width: 768px) {
  .tabs-container {
    padding: 0.6rem 0.8rem;
  }
  
  .tabs {
    padding: 0.3rem;
    border-radius: 8px;
  }
  
  .tab-btn {
    padding: 0.5rem 0.8rem;
    font-size: 0.85rem;
    min-width: 70px;
    border-radius: 6px;
  }
}
</style>