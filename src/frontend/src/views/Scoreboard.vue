<script setup>
import { onMounted, ref } from "vue";
import { useUserStore } from "@/stores/user.js";
import { ElMessage, ElButton, ElTable, ElTableColumn } from "element-plus";
import { useRouter } from "vue-router";

const listData = ref([]);
const user = useUserStore().user;
const router = useRouter();
const sendScore = () => {
  if (!user) {
    router.push({
      path: "/",
    });
    return ElMessage.error("请先输入昵称");
  }
  const message = {
    type: "UserClick",
    nickname: user,
  };
  window.$ws.sendMessage(message);
};
const goHome = () => {
    router.push("/");
  };

onMounted(() => {
  console.log(window.$ws);
  window.$ws.onMessage((message) => {
    listData.value = message;
  });
});
</script>

<template>
  <div class="scoreboard-container">
    <div class="button-area">
      <ElButton type="primary" @click="goHome">返回</ElButton>
      <ElButton type="success" @click="sendScore">点击!</ElButton>
    </div>
    <ElAlert
      title="注意：如果点击间隔时间超过20秒，你将会退出排行榜"
      type="warning"
      show-icon
    ></ElAlert>
    <ElTable :data="listData" style="width: 100%" stripe>
      <ElTableColumn prop="ID" label="昵称" width="180"></ElTableColumn>
      <ElTableColumn prop="Score" label="得分" width="180"></ElTableColumn>
      <ElTableColumn prop="ClickTime" label="上次点击时间" width="180"></ElTableColumn>
      <ElTableColumn prop="ClickInterval" label="点击间隔时间" width="180"></ElTableColumn>
    </ElTable>
  </div>
</template>

<style scoped>
.scoreboard-container {
  width: 800px;
  margin: 0 auto;
  padding: 20px;
}

.button-area {
  display: flex;
  justify-content: space-between;
  margin-bottom: 20px;
}
</style>