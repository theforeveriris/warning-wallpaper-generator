<template>
  <div class="container">
    <div class="main-content">
      <!-- 标签栏 -->
      <Tabs v-model:activeTab="activeTab" />

      <!-- 内容区域 -->
      <div class="content-area">
        <!-- 左侧控制面板 -->
        <ControlPanel 
          :activeTab="activeTab"
          :config="config"
          :logoImage="logoImage"
          @update:config="(newConfig) => Object.assign(config, newConfig)"
          @update:logoImage="(newLogoImage) => logoImage = newLogoImage"
          @render="renderPreview"
          @download="downloadWallpaper"
        />

        <!-- 右侧预览区 -->
        <PreviewSection 
          ref="previewSection"
          :config="config"
          :presetLogos="presetLogos"
          :logoImage="logoImage"
          @update:config="(newConfig) => Object.assign(config, newConfig)"
          @download="downloadWallpaper"
        />
      </div>
    </div>
  </div>
</template>

<script setup lang="ts">
import { ref, reactive, onMounted, nextTick } from 'vue';
import Tabs from './components/Tabs.vue';
import ControlPanel from './components/ControlPanel.vue';
import PreviewSection from './components/PreviewSection.vue';

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

// 响应式状态
const activeTab = ref<string>('general');
const logoImage = ref<HTMLImageElement | null>(null);
const previewSection = ref<InstanceType<typeof PreviewSection> | null>(null);
const presetLogos = reactive<PresetLogos>({});

// 配置对象
const config = reactive<Config>({
  // 壁纸设置
  bgColor: '#bb252d',
  aspectRatio: '16:9',
  width: 1920,
  height: 1080,

  // 中心文字设置
  centerText: '此计算机因违规外联已被阻断\n请等待安全部门并接受B级记忆清除',
  centerTextColor: '#ffffff',
  centerTextFont: 'SimHei',
  centerTextSize: 60,
  centerTextX: 50,
  centerTextY: 39,
  centerTextAlign: 'center',
  centerTextLineHeight: 1.5,

  // 标志设置
  logoPreset: 'Site-ZH-322.jpg',
  logoSize: 85,
  logoX: 11,
  logoY: 88,

  // 落款设置
  footerText: 'Site-CH-322 保密委员会\nSite-CH-322 逆模因收容物处置议会',
  footerTextColor: '#ffffff',
  footerTextFont: 'Arial',
  footerTextSize: 21,
  footerTextX: 88,
  footerTextY: 85,
  footerTextAlign: 'center',

  // 下载设置
  downloadFormat: 'png'
});

// 预加载标志图片
const preloadLogos = () => {
  const logoFiles = ['Site-ZH-12.jpg', 'Site-ZH-322.jpg'];
  let loadedCount = 0;
  const totalLogos = logoFiles.length;

  logoFiles.forEach(file => {
    const img = new Image();
    img.onload = () => {
      presetLogos[file] = img;
      loadedCount++;
      if (loadedCount === totalLogos) {
        renderPreview();
      }
    };
    img.src = `./icon/${file}`;
  });
};

// 渲染预览
const renderPreview = () => {
  console.log('renderPreview called');
  console.log('previewSection.value:', previewSection.value);
  if (previewSection.value) {
    console.log('Calling previewSection.value.render()');
    nextTick(() => {
      previewSection.value!.render();
    });
  } else {
    console.error('previewSection.value is null!');
  }
};

// 下载壁纸
const downloadWallpaper = () => {
  const canvas = previewSection.value?.$refs.canvasRef as HTMLCanvasElement | undefined;
  if (!canvas) {
    console.error('❌ 无法获取canvas元素进行下载');
    return;
  }

  const format = config.downloadFormat;
  const filename = `wallpaper-${new Date().toISOString().slice(0, 10)}.${format}`;

  console.log('💾 开始下载壁纸:', filename, format);
  
  if (format === 'png') {
    canvas.toBlob((blob) => {
      if (blob) {
        console.log('✅ PNG文件生成成功，开始保存');
        saveBlob(blob, filename);
      } else {
        console.error('❌ 无法生成PNG文件');
      }
    });
  } else if (format === 'jpg') {
    canvas.toBlob((blob) => {
      if (blob) {
        console.log('✅ JPG文件生成成功，开始保存');
        saveBlob(blob, filename);
      } else {
        console.error('❌ 无法生成JPG文件');
      }
    }, 'image/jpeg', 0.95);
  }
};

// 保存Blob为文件
const saveBlob = (blob: Blob, filename: string) => {
  const url = URL.createObjectURL(blob);
  const a = document.createElement('a');
  a.href = url;
  a.download = filename;
  document.body.appendChild(a);
  a.click();
  document.body.removeChild(a);
  URL.revokeObjectURL(url);
};

// 组件挂载时初始化
onMounted(() => {
  preloadLogos();
});
</script>

<style scoped>
.container {
  width: 100%;
  min-height: 100vh;
  padding: 6rem 1rem 1rem;
  box-sizing: border-box;
  background: #f5f5f5;
}

/* 移动端布局 */
@media (max-width: 768px) {
  .container {
    padding: 5rem 0.5rem 0.5rem;
  }
}

.main-content {
  width: 100%;
  max-width: 1200px;
  margin: 0 auto;
}

.content-area {
  display: flex;
  gap: 1.5rem;
  margin-top: 0;
}

/* 桌面端布局 */
@media (min-width: 769px) {
  .content-area {
    flex-direction: row;
  }
}

/* 移动端布局 */
@media (max-width: 768px) {
  .container {
    padding: 4.5rem 0.5rem 0.5rem;
  }
  
  .content-area {
    flex-direction: column;
    gap: 1rem;
  }
}
</style>