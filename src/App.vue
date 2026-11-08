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
  <div>
    <video ref="videoRef" autoplay playsinline width="640" height="480"></video>
    <p>{{ statusMessage }}</p>
  </div>
</template>
