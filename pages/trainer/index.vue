<script>
export default {
  async setup() {
    // サーバーに接続してfetchする関数の宣言(composables/useTrainers)
    const { data: trainers } = await useTrainers();
    return { trainers };
  },
};
</script>

<template>
  <div>
    <h1>つづきからはじめる</h1>
    <p>はじめるトレーナーを選択するのじゃ！</p>
    <!-- <form @submit.prevent>

      <GamifyButton>決定</GamifyButton>
    </form> -->

    <GamifyList>
      <GamifyItem v-for="trainer in trainers" :key="trainer">
        <NuxtLink :to="`/trainer/${trainer}`">{{ trainer }}</NuxtLink>
      </GamifyItem>
    </GamifyList>
  </div>
</template>

<!-- 
<style scoped>
form {
  border-radius: 0.5rem;
  border: solid 4px #555;
  padding: 1.5rem 3rem;
}

form > :not(:last-child) {
  display: block;
  margin-bottom: 1rem;
}

.item > label,
.item > span {
  display: block;
  margin-bottom: 0.25rem;
}
.item > span {
  font-size: 0.875rem;
}
</style> -->