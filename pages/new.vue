<script>
import { VITE_SERVER_ORIGIN } from "~/utils/env";
import trimAvoidCharacters from "~/utils/trimAvoidCharacters";

export default {
  setup() {
    //ヒント+回答を参照：useRouterは.nuxt内で定義されている。
    //バックエンド側にデータ転送するために必要
    const router = useRouter();
    // トレーナーの名前用の変数を用意
    const trainerName = ref("");
    // 名前のダイアログ
    const { dialog, onOpen, onClose } = useDialog();

    //回答を見て追加：利用できない文字を制御する関数をかましている。結果の文字列をsafeTrainerNameに入れている。
    //trainerNameを直接送ってもOKだと思うが、こっちの方が良い。
    const safeTrainerName = computed(() =>
      trimAvoidCharacters(trainerName.value)
    );
    const valid = computed(() => safeTrainerName.value.length > 0);


    // 決定ボタンを押した時の処理(onSubmit)
    // 回答を参照して記載　fetchの記載がまだ未理解
    // safeTrainerNameって何？-> 禁止文字チェック
    const onSubmit = async () => {
      const response = await fetch(`${VITE_SERVER_ORIGIN}/api/trainer`, {
        method: "POST",
        headers: {
          "Content-Type": "application/json",
        },
        body: JSON.stringify({
          name: safeTrainerName.value,
        }),
      });
      if (!response.ok) return;
      router.push(`/trainer/${safeTrainerName.value}`);
    };


    return {
      trainerName,
      onSubmit,
      dialog,
      onOpen,
      onClose,

      safeTrainerName,
      valid,
    };
  },
};
</script>

<template>
  <div>
    <h1>あたらしくはじめる</h1>
    <p>はじめに、君の名前を教えてもらおう！</p>
    <form @submit.prevent>
      <label for="trainer-name">なまえ</label>
      <input 
        id="trainer-name" 
        @keydown.enter="valid && onOpen(true)" 
        v-model="trainerName"
      />
      <GamifyButton 
        type="button" 
        @click="onOpen(true)" 
        :disabled="!valid">けってい</GamifyButton>
    </form>

    <!-- 名前確認のダイアログの処理 -->
    <!-- 回答見て追加：descriptionにダイアログ内の文字を入れる -->
    <GamifyDialog
      v-if="dialog"
      id="confirm-submit"
      title="かくにん"
      :description="`ふむ・・・君の名前は、${safeTrainerName}　と、言うんだな！`"
      @close="onClose"
    >
      <GamifyList :border="false" direction="horizon">
        <GamifyItem>
          <GamifyButton 
            type="button" 
            id="trainerNameDialog_No" 
            @click="onClose">いいえ</GamifyButton>
        </GamifyItem>
    
        <GamifyItem>
          <GamifyButton 
            type="button" 
            id="trainerNameDialog_Yes"
            @click="onSubmit">はい</GamifyButton>
        </GamifyItem>
      </GamifyList>
    </GamifyDialog>

  </div>
</template>

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
</style>
