<template>
  <div class="control-panel">
    <!-- 选项卡内容 -->
    <div class="tab-content">
      <!-- 总编选项卡 -->
      <div class="tab-pane" v-show="activeTab === 'general'">
        <div class="control-section compact">
          <div class="control-row">
            <div class="control-item inline">
              <label>背景颜色</label>
              <input type="color" :value="config.bgColor" @input="handleInput('bgColor', $event)" class="color-input">
            </div>
            <div class="control-item inline">
              <label>壁纸比例</label>
              <select :value="config.aspectRatio" @change="handleInput('aspectRatio', $event)" class="ratio-select">
                <option value="16:9">16:9</option>
                <option value="4:3">4:3</option>
                <option value="1:1">1:1</option>
                <option value="16:10">16:10</option>
                <option value="custom">自定义</option>
              </select>
            </div>
          </div>
          <div class="control-item" v-show="config.aspectRatio === 'custom'">
            <label>自定义比例</label>
            <input type="number" :value="config.width" @input="handleInput('width', $event)" placeholder="宽度">
            <input type="number" :value="config.height" @input="handleInput('height', $event)" placeholder="高度">
          </div>
        </div>
        
        <div class="control-section compact">
          <h3>配置管理</h3>
          <div class="control-item">
            <button @click="exportConfig" class="btn">导出配置 (JSON)</button>
          </div>
          <div class="control-item">
            <label>导入配置</label>
            <input type="file" @change="handleConfigImport" accept=".json">
          </div>
          <div class="control-item">
            <button @click="resetConfig" class="btn">重置配置</button>
          </div>
        </div>
        
        <div class="control-section compact">
          <h3>自定义远程字体</h3>
          <div class="control-item">
            <label>字体名称</label>
            <input type="text" v-model="customFontName" placeholder="例如：My Custom Font">
          </div>
          <div class="control-item">
            <label>字体值（CSS使用）</label>
            <input type="text" v-model="customFontValue" placeholder="例如：'My Custom Font', sans-serif">
          </div>
          <div class="control-item">
            <label>字体URL</label>
            <input type="url" v-model="customFontUrl" placeholder="例如：https://fonts.googleapis.com/css2?family=...">
          </div>
          <div class="control-item">
            <button @click="addCustomFont" class="btn">添加字体</button>
          </div>
          <div class="control-item">
            <h4>已添加的自定义字体</h4>
            <ul class="custom-font-list">
              <li v-for="font in customFonts" :key="font.value">
                <span>{{ font.label }}</span>
                <button @click="removeCustomFont(font.value)" class="remove-font-btn">删除</button>
              </li>
            </ul>
          </div>
        </div>
      </div>

      <!-- 中心文字选项卡 -->
      <div class="tab-pane" v-show="activeTab === 'center-text'">
        <div class="control-section compact">
          <div class="control-item">
            <label>文字内容</label>
            <textarea :value="config.centerText" @input="handleInput('centerText', $event)" rows="3"></textarea>
          </div>
          <div class="control-item">
            <label>文字颜色</label>
            <input type="color" :value="config.centerTextColor" @input="handleInput('centerTextColor', $event)">
          </div>
          <div class="control-item">
            <label>字体</label>
            <select :value="config.centerTextFont" @change="handleInput('centerTextFont', $event)">
              <option value="Arial">Arial</option>
              <option value="SimSun">宋体</option>
              <option value="SimHei">黑体</option>
              <option value="Microsoft YaHei">微软雅黑</option>
              <option value="Roboto">Roboto</option>
              <option value="Noto Sans SC">Noto Sans SC</option>
              <option value="Open Sans">Open Sans</option>
            </select>
          </div>
          <div class="control-row">
            <div class="control-item inline">
              <label>字体大小</label>
              <div class="input-with-unit">
                <input type="number" :value="config.centerTextSize" @input="handleInput('centerTextSize', $event)" min="20" max="100" class="number-input">
              </div>
            </div>
            <div class="control-item inline">
              <label>行高</label>
              <div class="input-with-unit">
                <input type="number" :value="config.centerTextLineHeight" @input="handleInput('centerTextLineHeight', $event)" min="0.5" max="3" step="0.1" class="number-input">
              </div>
            </div>
            <div class="control-item inline">
              <label>文本对齐</label>
              <select :value="config.centerTextAlign" @change="handleInput('centerTextAlign', $event)" class="small-select">
                <option value="left">左对齐</option>
                <option value="center">居中</option>
                <option value="right">右对齐</option>
              </select>
            </div>
          </div>
          <div class="control-row">
            <div class="control-item inline">
              <label>水平位置</label>
              <div class="input-with-unit">
                <input type="number" :value="config.centerTextX" @input="updateConfig('centerTextX', Number($event.target.value))" min="0" max="100" class="number-input">
              </div>
            </div>
            <div class="control-item inline">
              <label>垂直位置</label>
              <div class="input-with-unit">
                <input type="number" :value="config.centerTextY" @input="updateConfig('centerTextY', Number($event.target.value))" min="0" max="100" class="number-input">
              </div>
            </div>
          </div>
        </div>
      </div>

      <!-- 标志选项卡 -->
      <div class="tab-pane" v-show="activeTab === 'logo'">
        <div class="control-section compact">
          <div class="control-item">
            <label>预设标志</label>
            <select :value="config.logoPreset" @change="updateConfig('logoPreset', $event.target.value)">
              <option value="none">无</option>
              <option value="Site-ZH-12.jpg">Site-ZH-12</option>
              <option value="Site-ZH-322.jpg">Site-ZH-322</option>
            </select>
          </div>
          <div class="control-item">
            <label>上传标志</label>
            <input type="file" @change="handleLogoUpload" accept="image/*">
          </div>
          <div class="control-row">
            <div class="control-item inline">
              <label>标志大小</label>
              <div class="input-with-unit">
                <input type="number" :value="config.logoSize" @input="updateConfig('logoSize', Number($event.target.value))" min="10" max="200" class="number-input">
              </div>
            </div>
            <div class="control-item inline">
              <label>水平位置</label>
              <div class="input-with-unit">
                <input type="number" :value="config.logoX" @input="updateConfig('logoX', Number($event.target.value))" min="0" max="100" class="number-input">
              </div>
            </div>
            <div class="control-item inline">
              <label>垂直位置</label>
              <div class="input-with-unit">
                <input type="number" :value="config.logoY" @input="updateConfig('logoY', Number($event.target.value))" min="0" max="100" class="number-input">
              </div>
            </div>
          </div>
        </div>
      </div>

      <!-- 落款选项卡 -->
      <div class="tab-pane" v-show="activeTab === 'footer'">
        <div class="control-section compact">
          <div class="control-item">
            <label>落款内容</label>
            <textarea :value="config.footerText" @input="updateConfig('footerText', $event.target.value)" rows="2"></textarea>
          </div>
          <div class="control-item">
            <label>字体</label>
            <select :value="config.footerTextFont" @change="updateConfig('footerTextFont', $event.target.value)">
              <option value="Arial">Arial</option>
              <option value="SimSun">宋体</option>
              <option value="SimHei">黑体</option>
              <option value="Microsoft YaHei">微软雅黑</option>
              <option value="Roboto">Roboto</option>
              <option value="Noto Sans SC">Noto Sans SC</option>
              <option value="Open Sans">Open Sans</option>
            </select>
          </div>
          <div class="control-item">
            <label>文字颜色</label>
            <input type="color" :value="config.footerTextColor" @input="updateConfig('footerTextColor', $event.target.value)">
          </div>
          <div class="control-row">
            <div class="control-item inline">
              <label>字体大小</label>
              <div class="input-with-unit">
                <input type="number" :value="config.footerTextSize" @input="updateConfig('footerTextSize', Number($event.target.value))" min="10" max="40" class="number-input">
              </div>
            </div>
            <div class="control-item inline">
              <label>水平位置</label>
              <div class="input-with-unit">
                <input type="number" :value="config.footerTextX" @input="updateConfig('footerTextX', Number($event.target.value))" min="0" max="100" class="number-input">
              </div>
            </div>
            <div class="control-item inline">
              <label>垂直位置</label>
              <div class="input-with-unit">
                <input type="number" :value="config.footerTextY" @input="updateConfig('footerTextY', Number($event.target.value))" min="0" max="100" class="number-input">
              </div>
            </div>
          </div>
          <div class="control-item">
            <label>文本对齐</label>
            <select :value="config.footerTextAlign" @change="updateConfig('footerTextAlign', $event.target.value)">
              <option value="left">左对齐</option>
              <option value="center">居中</option>
              <option value="right">右对齐</option>
            </select>
          </div>
        </div>
      </div>


    </div>


  </div>
</template>

<script setup lang="ts">
import { ref } from 'vue';

// 类型定义
interface Config {
  // 壁纸设置
  bgColor: string;
  aspectRatio: string;
  width: number;
  height: number;
  
  // 中心文字设置
  centerText: string;
  centerTextColor: string;
  centerTextFont: string;
  centerTextSize: number;
  centerTextX: number;
  centerTextY: number;
  centerTextAlign: 'left' | 'center' | 'right';
  centerTextLineHeight: number;
  
  // 标志设置
  logoPreset: string;
  logoSize: number;
  logoX: number;
  logoY: number;
  
  // 落款设置
  footerText: string;
  footerTextColor: string;
  footerTextFont: string;
  footerTextSize: number;
  footerTextX: number;
  footerTextY: number;
  footerTextAlign: 'left' | 'center' | 'right';
  
  // 下载设置
  downloadFormat: 'png' | 'jpg';
}

interface CustomFont {
  label: string;
  value: string;
  url: string;
}

// Props
const props = defineProps<{
  activeTab: string;
  config: Config;
  logoImage: HTMLImageElement | null;
}>();

// Emits
const emit = defineEmits<{
  'update:config': [config: Config];
  'update:logoImage': [logoImage: HTMLImageElement | null];
  'render': [];
  'download': [];
}>();

// 自定义字体相关
const customFontName = ref<string>('');
const customFontValue = ref<string>('');
const customFontUrl = ref<string>('');
const customFonts = ref<CustomFont[]>([]);

// 更新配置
const updateConfig = <K extends keyof Config>(key: K, value: Config[K]) => {
  console.log(`Updating config.${key} to:`, value);
  const newConfig = { ...props.config, [key]: value };
  emit('update:config', newConfig);
  emit('render');
};

// 处理输入事件
const handleInput = <K extends keyof Config>(key: K, event: Event) => {
  const target = event.target as HTMLInputElement | HTMLSelectElement | HTMLTextAreaElement;
  if (target) {
    if (key === 'width' || key === 'height' || key === 'centerTextSize' || key === 'centerTextLineHeight' || key === 'centerTextX' || key === 'centerTextY' || key === 'logoSize' || key === 'logoX' || key === 'logoY' || key === 'footerTextSize' || key === 'footerTextX' || key === 'footerTextY') {
      updateConfig(key, Number(target.value) as Config[K]);
    } else {
      updateConfig(key, target.value as Config[K]);
    }
  }
};

// 处理标志上传
const handleLogoUpload = (event: Event) => {
  const input = event.target as HTMLInputElement;
  const file = input.files?.[0];
  if (file) {
    const reader = new FileReader();
    reader.onload = (e) => {
      const img = new Image();
      img.src = e.target?.result as string;
      img.onload = () => {
        emit('update:logoImage', img);
        emit('render');
      };
    };
    reader.readAsDataURL(file);
  }
};

// 处理配置导入
const handleConfigImport = (event: Event) => {
  const input = event.target as HTMLInputElement;
  const file = input.files?.[0];
  if (file) {
    const reader = new FileReader();
    reader.onload = (e) => {
      try {
        const importedConfig = JSON.parse(e.target?.result as string) as Config;
        emit('update:config', importedConfig);
        emit('render');
        alert('配置导入成功！');
      } catch (error) {
        alert('配置文件格式错误，请检查文件内容。');
        console.error('配置导入失败:', error);
      }
    };
    reader.readAsText(file);
  }
};

// 导出配置
const exportConfig = () => {
  const exportData = {
    ...props.config,
    customFonts: customFonts.value
  };
  
  const configJson = JSON.stringify(exportData, null, 2);
  const blob = new Blob([configJson], { type: 'application/json' });
  const url = URL.createObjectURL(blob);
  const a = document.createElement('a');
  a.href = url;
  a.download = `wallpaper-config-${new Date().toISOString().slice(0, 10)}.json`;
  document.body.appendChild(a);
  a.click();
  document.body.removeChild(a);
  URL.revokeObjectURL(url);
};

// 重置配置
const resetConfig = () => {
  if (confirm('确定要重置所有配置为默认值吗？')) {
    const defaultConfig: Config = {
      bgColor: '#1e88e5',
      aspectRatio: '16:9',
      width: 1920,
      height: 1080,
      centerText: '此计算机因违规外联已被阻断\n请等待安全部门与你联系',
      centerTextColor: '#ffffff',
      centerTextFont: 'Arial',
      centerTextSize: 48,
      centerTextX: 50,
      centerTextY: 50,
      centerTextAlign: 'center',
      centerTextLineHeight: 1.5,
      logoPreset: 'none',
      logoSize: 80,
      logoX: 10,
      logoY: 90,
      footerText: '宏山科学院保密委员会办公室\n终末地工作领导小组办公室',
      footerTextColor: '#ffffff',
      footerTextFont: 'Arial',
      footerTextSize: 16,
      footerTextX: 90,
      footerTextY: 90,
      footerTextAlign: 'right',
      downloadFormat: 'png'
    };
    
    emit('update:config', defaultConfig);
    customFonts.value = [];
    emit('render');
  }
};

// 添加自定义字体
const addCustomFont = () => {
  if (customFontName.value && customFontValue.value && customFontUrl.value) {
    customFonts.value.push({
      label: customFontName.value,
      value: customFontValue.value,
      url: customFontUrl.value
    });
    
    // 清空输入框
    customFontName.value = '';
    customFontValue.value = '';
    customFontUrl.value = '';
  }
};

// 删除自定义字体
const removeCustomFont = (fontValue: string) => {
  customFonts.value = customFonts.value.filter(font => font.value !== fontValue);
};

// 下载壁纸
const download = () => {
  emit('download');
};
</script>

<style scoped>
.control-panel {
  flex: 1;
  min-width: 300px;
  max-width: 100%;
  background: rgba(255, 255, 255, 0.95);
  backdrop-filter: blur(10px);
  border-radius: 12px;
  padding: 1.2rem;
  box-shadow: 0 4px 20px rgba(0, 0, 0, 0.1);
  border: 1px solid rgba(0, 0, 0, 0.1);
  box-sizing: border-box;
}

.tab-content {
  margin-bottom: 1.5rem;
}

.tab-pane {
  animation: fadeIn 0.3s ease-in-out;
}

@keyframes fadeIn {
  from { opacity: 0; transform: translateY(10px); }
  to { opacity: 1; transform: translateY(0); }
}

.control-section {
  margin-bottom: 1.5rem;
  padding-bottom: 1.5rem;
  border-bottom: 1px solid rgba(0, 0, 0, 0.1);
}

.control-section.compact {
  margin-bottom: 1rem;
  padding-bottom: 1rem;
}

.control-section:last-child {
  border-bottom: none;
}

.control-section h2 {
  font-size: 1.2rem;
  margin-bottom: 1rem;
  color: #333;
  text-align: center;
}

.control-section h3 {
  font-size: 1rem;
  margin-bottom: 0.8rem;
  color: #555;
}

.control-section h4 {
  font-size: 0.9rem;
  margin-bottom: 0.6rem;
  color: #666;
}

.control-item {
  margin-bottom: 0.8rem;
}

.control-item.inline {
  display: inline-block;
  margin-right: 1rem;
  margin-bottom: 0.5rem;
}

.control-row {
  display: flex;
  flex-wrap: wrap;
  gap: 0.8rem;
  margin-bottom: 0.8rem;
}

.control-item label {
  display: block;
  margin-bottom: 0.3rem;
  font-size: 0.85rem;
  font-weight: 500;
  color: #555;
}

.control-item input[type="text"],
.control-item input[type="number"],
.control-item input[type="url"],
.control-item textarea,
.control-item select {
  width: 100%;
  padding: 0.6rem;
  border: 1px solid #ddd;
  border-radius: 6px;
  font-size: 0.9rem;
  box-sizing: border-box;
  transition: border-color 0.2s ease;
}

.control-item input[type="text"]:focus,
.control-item input[type="number"]:focus,
.control-item input[type="url"]:focus,
.control-item textarea:focus,
.control-item select:focus,
.small-select:focus {
  outline: none;
  border-color: #1e88e5;
  box-shadow: 0 0 0 2px rgba(30, 136, 229, 0.2);
}

.small-select {
  width: 100%;
  padding: 0.6rem;
  border: 1px solid #ddd;
  border-radius: 6px;
  font-size: 0.9rem;
  box-sizing: border-box;
  transition: border-color 0.2s ease;
}

/* 移动端布局 */
@media (max-width: 768px) {
  .small-select {
    padding: 0.5rem;
    font-size: 0.85rem;
  }
}

.control-item textarea {
  resize: vertical;
  min-height: 60px;
}

.control-item input[type="color"] {
  width: 100%;
  height: 40px;
  padding: 0.2rem;
  border: 1px solid #ddd;
  border-radius: 6px;
  cursor: pointer;
}

.control-item input[type="file"] {
  font-size: 0.85rem;
}

.input-with-unit {
  position: relative;
}

.number-input {
  width: 100%;
}

.btn {
  display: inline-block;
  padding: 0.6rem 1.2rem;
  border: none;
  border-radius: 6px;
  font-size: 0.9rem;
  font-weight: 500;
  cursor: pointer;
  transition: all 0.2s ease;
  background-color: #555;
  color: white;
}

.btn:hover {
  background-color: #333;
  transform: translateY(-1px);
  box-shadow: 0 2px 8px rgba(0, 0, 0, 0.3);
}

.remove-font-btn {
  margin-left: 0.5rem;
  padding: 0.2rem 0.6rem;
  font-size: 0.8rem;
  background-color: #666;
  color: white;
  border: none;
  border-radius: 4px;
  cursor: pointer;
}

.remove-font-btn:hover {
  background-color: #444;
}

.custom-font-list {
  list-style: none;
  padding: 0;
  margin: 0;
}

.custom-font-list li {
  display: flex;
  justify-content: space-between;
  align-items: center;
  padding: 0.4rem 0;
  border-bottom: 1px solid #f0f0f0;
}

.custom-font-list li:last-child {
  border-bottom: none;
}

.download-btn {
  width: 100%;
  padding: 0.8rem;
  border: none;
  border-radius: 8px;
  font-size: 1rem;
  font-weight: 600;
  cursor: pointer;
  transition: all 0.2s ease;
  background: linear-gradient(135deg, #1e88e5 0%, #1565c0 100%);
  color: white;
  box-shadow: 0 4px 12px rgba(30, 136, 229, 0.3);
}

.download-btn:hover {
  transform: translateY(-2px);
  box-shadow: 0 6px 16px rgba(30, 136, 229, 0.4);
}

/* 桌面端布局 */
@media (min-width: 769px) {
  .control-panel {
    max-width: 400px;
  }
  
  .control-item.inline {
    width: calc(50% - 0.5rem);
  }
  
  .control-row {
    gap: 1rem;
  }
}

/* 移动端布局 */
@media (max-width: 768px) {
  .control-panel {
    padding: 1rem;
    border-radius: 8px;
  }
  
  .control-section {
    margin-bottom: 1.2rem;
    padding-bottom: 1.2rem;
  }
  
  .control-section.compact {
    margin-bottom: 0.8rem;
    padding-bottom: 0.8rem;
  }
  
  .control-item {
    margin-bottom: 0.6rem;
  }
  
  .control-row {
    flex-direction: row;
    flex-wrap: wrap;
    gap: 0.5rem;
  }
  
  .control-item.inline {
    width: calc(33.333% - 0.333rem);
    margin-right: 0;
    margin-bottom: 0.5rem;
  }
  
  .control-item.inline:nth-child(3n) {
    margin-right: 0;
  }
  
  .control-item input[type="text"],
  .control-item input[type="number"],
  .control-item input[type="url"],
  .control-item textarea,
  .control-item select {
    padding: 0.5rem;
    font-size: 0.85rem;
  }
  
  .btn {
    padding: 0.5rem 1rem;
    font-size: 0.85rem;
  }
  
  .download-btn {
    padding: 0.7rem;
    font-size: 0.9rem;
  }
}
</style>