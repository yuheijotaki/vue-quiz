<template>
  <section class="section question">
    <h2 class="section__title">設問（全{{qaLength}}問）</h2>
    <div class="question__block">
      <div class="question__list u-qa__list">
        <div class="question__item u-qa__item" v-for="(qa,qaIndex) in qaData" :key="qaIndex">
          <div class="u-qa__number">
            <p class="u-qa__numberLabel">{{qaIndex+1}}</p>
          </div>
          <div class="question__title u-qa__title">
            <p class="question__titleText u-qa__text">{{qa.queText}}</p>
          </div>
          <ul class="question__answer u-qa__answer">
            <li class="question__answerText u-qa__answerText" v-for="(choiceVal,choiceKey,choiceIndex) in qa.ansChoice" v-bind:key="choiceIndex">
              <label class="question__answerLabel" v-if="qa.ansType === 'single'">
                <input
                  type="radio"
                  class="question__answerLabelInput"
                  v-bind:name="[qaIndex]"
                  v-bind:value="[qaIndex+'_'+choiceKey]"
                  v-model="answerData.pickedAnswer[qaIndex]"
                  >
                <span class="question__answerLabelText">{{ choiceVal }}</span>
              </label>
              <label class="question__answerLabel" v-else-if="qa.ansType === 'multi'">
                <input
                  type="checkbox"
                  class="question__answerLabelInput"
                  v-bind:name="[qaIndex]"
                  v-bind:value="[qaIndex+'_'+choiceKey]"
                  v-bind:data-answer-correct="qa.ansCorrect"
                  v-model="answerData.checkedAnswer"
                  >
                <span class="question__answerLabelText">{{ choiceVal }}</span>
              </label>
            </li>
          </ul>
        </div>
      </div>
    </div>
    <!--
    <p>Picked answer: {{ answerData.pickedAnswer }}</p>
    <p>Checked answer: {{ answerData.checkedAnswer }}</p>
    <div v-for="(qa,qaIndex) in qaData" :key="qaIndex">
      <div v-for="(choiceVal,choiceKey,choiceIndex) in qa.ansChoice" v-bind:key="choiceIndex">
        <p>Checked answer{{qaIndex+'_'+choiceIndex}}: {{ answerData.checkedAnswer[qaIndex+'_'+choiceIndex] }}</p>
      </div>
    </div>
    -->
  </section>
</template>

<script>
import $ from 'jquery';

export default {
  name: 'question',
  props: [ // App.vue から質問/解答リストの受け取り
    'qaData',
    'answerData'
  ],
  data () {
    return {
    }
  },
  created: function(){
    // console.log(this.qaData);
  },
  methods: {
  },
  computed: {
    // 設問数を取得
    qaLength: function () {
      const qaLength = this.qaData.length;
      return qaLength;
    }
  },
  updated() {
    // 各設問のチェックボックスに上限を設ける
    // ref: https://codepen.io/vjandrei/pen/rAuam
    $('.question__answerLabel input:checkbox').on('click', function(){
      const answerCorrect = $(this).attr('data-answer-correct'); // この設問の正答を取得（カンマ区切り）
      const answerCorrectArray = answerCorrect.split(',');       // 正答を配列に入れ替え
      const answerVolume = answerCorrectArray.length;            // 配列に入っている総数を取得
      $('.question__answer').each(function(){
        const answerChecked = $('.question__answerLabel input:checkbox:checked' ,this).length >= answerVolume;
        $('.question__answerLabel input:checkbox' ,this).not(':checked').attr('disabled',answerChecked);
      });
    });
  }
}
</script>

<style lang="scss" scoped>
</style>
