<template>
  <section class="lead">
    <h1 class="lead__title" v-html="this.leadTitle"></h1>
    <div class="lead__text">
      <p class="lead__textContent" v-html="this.leadText"></p>
    </div>
  </section>
</template>

<script>

export default {
  name: 'lead',
  data () {
    return {
      leadTitle: '📙 AWS 認定クラウドプラクティショナー 模擬試験サンプル問題',
      leadText: '模擬試験のサンプル問題です。（2019年5月受験）<br>正答が公開されないため解答は確実ではありませんが、90%以上取れたので <span class="u-bold">だいたい</span> あってます。<br><span class="u-bold u-emphasis">試験問題オープン（Web上/SNSで共有など）にするとAWSに怒られるそうなので、クローズド（SNSで共有しない/個人観賞用）でお願いします。</span>'
    }
  }
}
</script>

<style lang="scss" scoped>
.lead {
}
.lead__title {
  @include title01;
}
.lead__text {
  margin-top: 20px;
}
.lead__textContent {
  @include text01;
}
</style>
