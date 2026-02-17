<template>
  <div class="preview-section">
    <h2>预览效果</h2>
    <div class="preview-container">
      <canvas ref="canvasRef" class="preview-canvas"></canvas>
    </div>
    
    <!-- 下载设置 -->
    <div class="download-section">
      <h3>下载设置</h3>
      <div class="download-controls">
        <div class="control-item">
          <label>图片格式</label>
          <select :value="config.downloadFormat" @change="updateDownloadFormat($event.target.value)">
            <option value="png">PNG</option>
            <option value="jpg">JPG</option>
          </select>
        </div>
        <button @click="downloadWallpaper" class="download-btn">下载壁纸</button>
      </div>
    </div>
  </div>
</template>

<script setup lang="ts">
import { ref, onMounted, watch, nextTick } from 'vue';

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

interface PresetLogos {
  [key: string]: HTMLImageElement;
}

// Props
const props = defineProps<{
  config: Config;
  presetLogos: PresetLogos;
  logoImage: HTMLImageElement | null;
}>();

// Emits
const emit = defineEmits<{
  'update:config': [config: Config];
  'download': [];
}>();

// 响应式状态
const canvasRef = ref<HTMLCanvasElement | null>(null);

// 更新下载格式
const updateDownloadFormat = (format: string) => {
  const newConfig = { ...props.config, downloadFormat: format as 'png' | 'jpg' };
  emit('update:config', newConfig);
};

// 下载壁纸
const downloadWallpaper = () => {
  emit('download');
};

// 渲染壁纸
const render = () => {
  console.log('🔄 开始渲染预览...');
  
  // 确保canvas元素存在
  if (!canvasRef.value) {
    console.error('❌ Canvas元素未找到！');
    return;
  }
  
  const canvas = canvasRef.value;
  const ctx = canvas.getContext('2d');
  if (!ctx) {
    console.error('❌ 无法获取canvas上下文！');
    return;
  }
  
  try {
    const { config, presetLogos, logoImage } = props;
    console.log('📝 渲染配置:', {
      bgColor: config.bgColor,
      aspectRatio: config.aspectRatio,
      centerText: config.centerText.substring(0, 20) + '...',
      logoPreset: config.logoPreset,
      hasLogoImage: !!logoImage
    });
    
    // 调整Canvas大小
    if (config.aspectRatio === 'custom') {
      canvas.width = config.width || 1920;
      canvas.height = config.height || 1080;
    } else {
      const [w, h] = config.aspectRatio.split(':').map(Number);
      canvas.width = 1920;
      canvas.height = Math.round(1920 * h / w);
    }
    
    console.log('📐 Canvas尺寸:', canvas.width, 'x', canvas.height);

    // 绘制背景
    ctx.fillStyle = config.bgColor;
    ctx.fillRect(0, 0, canvas.width, canvas.height);
    console.log('🎨 背景绘制完成:', config.bgColor);

    // 绘制中心文字
    drawCenterText(ctx, config, canvas);
    console.log('📄 中心文字绘制完成');

    // 绘制标志
    drawLogo(ctx, config, presetLogos, logoImage, canvas);
    console.log('🖼️ 标志绘制完成');

    // 绘制落款
    drawFooter(ctx, config, canvas);
    console.log('📝 落款绘制完成');
    
    console.log('✅ 预览渲染完成！');
  } catch (error) {
    console.error('❌ 渲染过程中出错:', error);
  }
};

// 绘制中心文字
const drawCenterText = (ctx: CanvasRenderingContext2D, config: Config, canvas: HTMLCanvasElement) => {
  if (!config.centerText) return;
  
  const lines = config.centerText.split('\n');
  const lineHeight = config.centerTextSize * config.centerTextLineHeight;
  const totalHeight = lineHeight * (lines.length - 1);
  const startY = (canvas.height * config.centerTextY / 100) - (totalHeight / 2);

  // 设置字体，添加备选字体确保兼容性
  ctx.font = `${config.centerTextSize}px ${config.centerTextFont}, Arial, sans-serif`;
  ctx.fillStyle = config.centerTextColor;
  ctx.textBaseline = 'top';

  lines.forEach((line, index) => {
    let x: number;
    switch (config.centerTextAlign) {
      case 'left':
        x = canvas.width * config.centerTextX / 100;
        break;
      case 'center':
        x = canvas.width * config.centerTextX / 100 - ctx.measureText(line).width / 2;
        break;
      case 'right':
        x = canvas.width * config.centerTextX / 100 - ctx.measureText(line).width;
        break;
    }
    const y = startY + (index * lineHeight);
    ctx.fillText(line, x, y);
  });
};

// 绘制标志
const drawLogo = (ctx: CanvasRenderingContext2D, config: Config, presetLogos: PresetLogos, logoImage: HTMLImageElement | null, canvas: HTMLCanvasElement) => {
  if (config.logoPreset !== 'none' || logoImage) {
    const x = canvas.width * config.logoX / 100;
    const y = canvas.height * config.logoY / 100;
    const size = config.logoSize;

    if (logoImage) {
      // 绘制上传的标志
      ctx.drawImage(logoImage, x, y - size, size, size);
    } else if (config.logoPreset && presetLogos[config.logoPreset]) {
      // 绘制预设标志
      ctx.drawImage(presetLogos[config.logoPreset], x, y - size, size, size);
    }
  }
};

// 绘制落款
const drawFooter = (ctx: CanvasRenderingContext2D, config: Config, canvas: HTMLCanvasElement) => {
  if (!config.footerText) return;
  
  const lines = config.footerText.split('\n');
  const lineHeight = config.footerTextSize * 1.2;
  const totalHeight = lineHeight * (lines.length - 1);
  const startY = (canvas.height * config.footerTextY / 100) - totalHeight;

  // 设置字体，添加备选字体确保兼容性
  ctx.font = `${config.footerTextSize}px ${config.footerTextFont}, Arial, sans-serif`;
  ctx.fillStyle = config.footerTextColor;
  ctx.textBaseline = 'top';

  lines.forEach((line, index) => {
    let x: number;
    switch (config.footerTextAlign) {
      case 'left':
        x = canvas.width * config.footerTextX / 100;
        break;
      case 'center':
        x = canvas.width * config.footerTextX / 100 - ctx.measureText(line).width / 2;
        break;
      case 'right':
        x = canvas.width * config.footerTextX / 100 - ctx.measureText(line).width;
        break;
    }
    const y = startY + (index * lineHeight);
    ctx.fillText(line, x, y);
  });
};

// 监听配置变化，触发渲染
watch(() => props.config, () => {
  console.log('🔧 配置发生变化，准备重渲染...');
  nextTick(() => {
    render();
  });
}, { deep: true });

// 监听标志图片变化，触发渲染
watch(() => props.logoImage, () => {
  console.log('🖼️ 标志图片发生变化，准备重渲染...');
  nextTick(() => {
    render();
  });
});

// 监听预设标志变化，触发渲染
watch(() => props.presetLogos, () => {
  console.log('🏷️ 预设标志发生变化，准备重渲染...');
  nextTick(() => {
    render();
  });
}, { deep: true });

// 组件挂载时初始化
onMounted(() => {
  console.log('🚀 PreviewSection组件挂载完成');
  nextTick(() => {
    render();
  });
});

// 导出渲染方法供父组件调用
defineExpose({
  render
});
</script>

<style scoped>
.preview-section {
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
  display: flex;
  flex-direction: column;
}

.preview-section h2 {
  font-size: 1.2rem;
  margin-bottom: 1rem;
  color: #333;
  text-align: center;
}

.preview-container {
  flex: 1;
  background: #f0f0f0;
  border-radius: 8px;
  padding: 1rem;
  display: flex;
  justify-content: center;
  align-items: center;
  box-shadow: inset 0 2px 6px rgba(0, 0, 0, 0.1);
  min-height: 300px;
}

.preview-canvas {
  max-width: 100%;
  max-height: 100%;
  border-radius: 6px;
  box-shadow: 0 3px 12px rgba(0, 0, 0, 0.15);
  background: white;
  object-fit: contain;
}

/* 桌面端布局 */
@media (min-width: 769px) {
  .preview-section {
    max-width: 600px;
  }
  
  .preview-container {
    min-height: 400px;
  }
  
  .preview-canvas {
    max-height: 500px;
  }
}

.download-section {
  margin-top: 1.5rem;
  padding-top: 1.5rem;
  border-top: 1px solid rgba(0, 0, 0, 0.1);
}

.download-section h3 {
  font-size: 1rem;
  margin-bottom: 1rem;
  color: #555;
  text-align: center;
}

.download-controls {
  display: flex;
  flex-direction: column;
  gap: 1rem;
}

.download-controls .control-item {
  margin-bottom: 0;
}

.download-controls label {
  display: block;
  margin-bottom: 0.3rem;
  font-size: 0.85rem;
  font-weight: 500;
  color: #555;
}

.download-controls select {
  width: 100%;
  padding: 0.6rem;
  border: 1px solid #ddd;
  border-radius: 6px;
  font-size: 0.9rem;
  box-sizing: border-box;
  transition: border-color 0.2s ease;
}

.download-controls select:focus {
  outline: none;
  border-color: #1e88e5;
  box-shadow: 0 0 0 2px rgba(30, 136, 229, 0.2);
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
  background: #333;
  color: white;
  box-shadow: 0 4px 12px rgba(0, 0, 0, 0.2);
}

.download-btn:hover {
  background: #222;
  transform: translateY(-2px);
  box-shadow: 0 6px 16px rgba(0, 0, 0, 0.3);
}

/* 桌面端布局 */
@media (min-width: 769px) {
  .download-controls {
    flex-direction: row;
    align-items: flex-end;
  }
  
  .download-controls .control-item {
    flex: 1;
    margin-right: 1rem;
  }
  
  .download-controls .control-item:last-child {
    margin-right: 0;
  }
  
  .download-btn {
    flex: 1;
    max-width: 200px;
  }
}

/* 移动端布局 */
@media (max-width: 768px) {
  .preview-section {
    padding: 1rem;
    border-radius: 8px;
  }
  
  .preview-section h2 {
    font-size: 1.1rem;
    margin-bottom: 0.8rem;
  }
  
  .preview-container {
    padding: 0.8rem;
    min-height: 250px;
  }
  
  .preview-canvas {
    max-height: 250px;
  }
  
  .download-section {
    margin-top: 1.2rem;
    padding-top: 1.2rem;
  }
  
  .download-section h3 {
    font-size: 0.95rem;
    margin-bottom: 0.8rem;
  }
  
  .download-controls {
    gap: 0.8rem;
  }
  
  .download-controls select {
    padding: 0.5rem;
    font-size: 0.85rem;
  }
  
  .download-btn {
    padding: 0.7rem;
    font-size: 0.9rem;
  }
}
</style>