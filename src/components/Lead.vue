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
      leadTitle: 'vue-quiz',
      leadText: '<a href="https://d1.awsstatic.com/training-and-certification/Docs%20-%20Cloud%20Practitioner/AWS%20Certified%20Cloud%20Practioner_Sample%20Questions_v1.1_FINAL.PDF" target="_blank">AWS Certified Cloud Practitioner – Foundational (CLF-C01) Sample Exam Questions</a> からの抜粋です。'
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
