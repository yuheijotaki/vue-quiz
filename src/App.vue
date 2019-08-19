<template>
  <div id="app">
    <div class="wrapper">
      <lead></lead>
      <question
        v-bind:qa-data="qaData"
        v-bind:answerData="answerData"
      ></question>
      <check
        v-bind:qa-data="qaData"
        v-bind:answer-data="answerData"
      ></check>
      <score
        v-bind:qa-data="qaData"
        v-bind:answer-data="answerData"
      ></score>
      <answer
        v-bind:qa-data="qaData"
        v-bind:answer-data="answerData"
      ></answer>
    </div><!-- .wrapper -->
  </div><!-- #app -->
</template>



<script>
import 'normalize.css';
import $ from 'jquery';
import axios from 'axios';
import lead from './components/lead'
import question from './components/question'
import check from './components/check'
import score from './components/score'
import answer from './components/answer'

export default {
  name: 'App',
  components: {
    lead,
    question,
    check,
    score,
    answer
  },
  data () {
    return {
      // 質問・解答リスト
      qaData: [],
      // ユーザーの解答データ（入力された生のデータ）
      answerData: {
        pickedAnswer: [],
        checkedAnswer: []
      }
    }
  },
  created: function(){
    this.request();
  },
  methods: {
    // JSON から問題集を取得する
    request: function(){
      axios.get('./static/assets/qa.json')
      .then( response => {
        this.qaData = response.data; // JSONデータの取得
        // console.table(this.qaData);
      })
      .catch( error => {
        console.log(error);
      });
    }
  }
}
</script>



<style lang="scss">
// .testing {
//   @include mq {
//   };
// }

// Noto Sans （Google Fonts）の読み込み
@import url('https://fonts.googleapis.com/css?family=Noto+Sans+JP:400,500,700&display=swap');
// 400: regular
// 500: medium
// 700: bold

// リセット・グローバル設定
html,* {
  margin: 0;
  padding: 0;
}
body {
  background: $white_02;
  font-family: 'Noto Sans JP', -apple-system, 'BlinkMacSystemFont', Helvetica Neue, Helvetica, Arial, 'Hiragino Kaku Gothic ProN', Meiryo, sans-serif;
  font-weight: 400;
  -webkit-text-size-adjust: 100%;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-rendering: optimizeLegibility;
  font-feature-settings : "palt";
  @include ls005;
}
h1,h2,h3,h4,h5,h6 {
  margin: 0;
  padding: 0;
}
a {
  @include textLink01;
}
.wrapper {
  @include pcInner;
  padding-top: 60px;
  padding-bottom: 100px;
  @include mq {
    @include spInner;
    padding-top: 20px;
    padding-bottom: 60px;
  }
}
.section {
  margin-top: 40px;
  @include mq {
    margin-top: 30px;
  }
  &:first-child {
    margin-top: 0;
  }
}

// 共通スタイル
.section__title {
  @include text02;
}

// utility
.u-bold {
  font-weight: 700;
}
.u-emphasis {
  color: $red_01;
}

// QAリスト
.u-qa__list {
  margin-top: 20px;
  @include mq {
    margin-top: 15px;
  }
}
.u-qa__item {
  background: $white_01;
  margin-top: 20px;
  padding: 40px;
  box-shadow: 0 0 10px rgba($black_01,.1);
  border-radius: 2px;
  position: relative;
  @include mq {
    margin-top: 15px;
    padding: 30px 20px 20px;
  }
  &:first-child {
    margin-top: 0;
  }
}
.u-qa__number {
  position: absolute;
  top: 0;
  left: 0;
  width: 24px;
  height: 24px;
  background: $gray_01;
  border-radius: 2px 0 2px 0;
  text-align: center;
  @include mq {
    width: 20px;
    height: 20px;
  }
}
.u-qa__numberLabel {
  color: $black_02;
  font-size: 11px;
  line-height: 24px;
  @include fontBold;
  @include mq {
    font-size: 10px;
    line-height: 20px;
  }
}
.u-qa__title {
}
.u-qa__text {
  @include text02;
  @include fontMedium;
}
.u-qa__answer {
  list-style: none;
  margin-top: 20px;
  @include mq {
    margin-top: 12px;
  }
}
.u-qa__answerText {
  @include text01;
  margin-top: 6px;
  @include mq {
    margin-top: 4px;
  }
  &:first-child {
    margin-top: 0;
  }
}

.question__answerLabel {
  display: flex;
  align-items: flex-start;
  align-content: flex-start;
}
.question__answerLabelInput {
  display: block;
  height: 23px;
  margin-right: 6px;
  @include mq {
    height: 19px;
    margin-right: 5px;
  }
}
.question__answerLabelText {
  display: block;
}
</style>
