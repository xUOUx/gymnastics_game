<script setup>
import { ref, onMounted } from "vue";

const videoRef = ref(null);
const statusMessage = ref("正在初始化鏡頭與 AI 模型...");

onMounted(async () => {
  try {
    // 開啟網頁攝影機
    const stream = await navigator.mediaDevices.getUserMedia({ video: true });
    if (videoRef.value) {
      videoRef.value.srcObject = stream;
    }

    // 初始化 ml5.js PoseNet 模型
    if (window.ml5 && videoRef.value) {
      const poseNet = window.ml5.poseNet(videoRef.value, () => {
        statusMessage.value = "AI 模型載入完成，準備開始運動！";
      });

      // 監聽骨架偵測結果
      poseNet.on("pose", (results) => {
        if (results.length > 0) {
          // 這裡未來可以取得關節座標（例如鼻子、肩膀等）
          console.log("偵測到骨架：", results[0].pose);
        }
      });
    }
  } catch (error) {
    statusMessage.value = "無法存取攝影機，請檢查權限設定！";
    console.error(error);
  }
});
</script>

<template>
  <div
    class="min-h-screen bg-slate-950 text-slate-100 flex flex-col items-center justify-between p-6 selection:bg-emerald-500 selection:text-white"
  >
    <!-- 頂部導覽列 -->
    <header
      class="w-full max-w-5xl flex items-center justify-between py-4 border-b border-slate-800/80"
    >
      <h1
        class="text-xl sm:text-2xl font-bold bg-gradient-to-r from-emerald-400 to-cyan-400 bg-clip-text text-transparent tracking-wide"
      >
        AI 智慧深蹲教練
      </h1>
      <div
        class="px-4 py-1.5 bg-slate-900 border border-slate-800 rounded-full text-xs sm:text-sm text-emerald-400 flex items-center gap-2 shadow-inner"
      >
        <span class="w-2 h-2 rounded-full bg-emerald-400 animate-pulse"></span>
        系統運行中
      </div>
    </header>

    <!-- 主要內容區：左側攝影機與即時動態、右側計數與解析面板 -->
    <main
      class="w-full max-w-5xl grid grid-cols-1 lg:grid-cols-3 gap-6 my-auto py-6"
    >
      <!-- 左側：攝影機預覽區塊 (佔 2 欄) -->
      <div
        class="lg:col-span-2 relative bg-slate-900 rounded-2xl overflow-hidden border border-slate-800 shadow-2xl flex items-center justify-center aspect-video group"
      >
        <!-- 視訊串流標籤 -->
        <video
          ref="videoRef"
          autoplay
          playsinline
          class="w-full h-full object-cover transform -scale-x-100"
        ></video>

        <!-- 浮動狀態提示 (玻璃擬態效果) -->
        <div
          class="absolute bottom-4 left-4 right-4 bg-slate-900/80 backdrop-blur-md px-4 py-3 rounded-xl border border-slate-700/50 text-sm text-slate-200 text-center shadow-lg"
        >
          {{ statusMessage }}
        </div>
      </div>

      <!-- 右側：數據與控制面板 (佔 1 欄) -->
      <div class="flex flex-col gap-6">
        <!-- 計數卡片 -->
        <div
          class="bg-slate-900/90 border border-slate-800 rounded-2xl p-6 flex flex-col items-center justify-center shadow-xl relative overflow-hidden"
        >
          <div
            class="absolute top-0 right-0 w-32 h-32 bg-emerald-500/5 rounded-full blur-2xl pointer-events-none"
          ></div>
          <span
            class="text-xs uppercase tracking-widest text-slate-400 font-semibold mb-2"
            >目前完成次數</span
          >
          <span
            class="text-6xl sm:text-7xl font-black text-emerald-400 tracking-tight"
            >0</span
          >
          <div
            class="w-full bg-slate-800 h-2 rounded-full mt-6 overflow-hidden"
          >
            <div
              class="bg-gradient-to-r from-emerald-500 to-cyan-400 h-full w-0 transition-all duration-300"
            ></div>
          </div>
          <span class="text-xs text-slate-500 mt-2">目標設定：10 下</span>
        </div>

        <!-- 動作狀態與建議卡片 -->
        <div
          class="bg-slate-900/90 border border-slate-800 rounded-2xl p-6 flex flex-col gap-3 shadow-xl flex-1"
        >
          <h3
            class="text-sm font-semibold text-slate-300 flex items-center gap-2"
          >
            <svg
              class="w-4 h-4 text-emerald-400"
              fill="none"
              stroke="currentColor"
              viewBox="0 0 24 24"
            >
              <path
                stroke-linecap="round"
                stroke-linejoin="round"
                stroke-width="2"
                d="M13 16h-1v-4h-1m1-4h.01M21 12a9 9 0 11-18 0 9 9 0 0118 0z"
              ></path>
            </svg>
            即時動作解析
          </h3>
          <div
            class="p-4 rounded-xl bg-slate-950/60 border border-slate-800/80 text-xs sm:text-sm text-slate-400 leading-relaxed"
          >
            請將全身完整入鏡。當準備好時，保持背部打直並開始進行深蹲動作。
          </div>
        </div>
      </div>
    </main>

    <!-- 底部資訊 -->
    <footer
      class="w-full max-w-5xl text-center text-xs text-slate-500 py-4 border-t border-slate-800/80"
    >
      Vue 3 + Vite + ml5.js PoseNet &bull; Tailwind CSS 現代化前端專案
    </footer>
  </div>
</template>
