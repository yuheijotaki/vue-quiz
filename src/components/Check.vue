<template>
  <section class="section check">
    {{userAnswerCheck}}
    <div class="check__block">
      <template v-if="this.checkButtonFlag === true">
        <p class="check__text">👻答え合わせをしてみましょう 👻</p>
        <button class="check__button" v-on:click="questionAnswerList">答え合わせ</button>
      </template>
      <template v-else>
        <p class="check__text">🙄すべての質問に解答してください 🙄</p>
        <button class="check__button" disabled>答え合わせ</button>
      </template>
    </div>
  </section>
</template>

<script>
import $ from 'jquery';

export default {
  name: 'check',
  props: [ // App.vue から質問/解答リストの受け取り
    'qaData',
    'answerData'
  ],
  data () {
    return {
      checkButtonFlag: false
    }
  },
  methods: {
    // 答え合わせボタンクリック時の挙動
    questionAnswerList: function (event) {
      // 設問の解答が完了しているとき
      const $check = $('.check');
      const checkHeight = $('.check').innerHeight();
      const $answer = $('.answer');
      const $score = $('.score');
      // スコアエリア / 解答エリアを表示
      $score.addClass('is-active');
      $answer.addClass('is-active');
      // スクロール移動
      const targetPosition = $check.offset().top + checkHeight + 10;
      $('html, body').stop().animate({ scrollTop: targetPosition }, 500, 'swing');
    }
  },
  computed: {
    // 設問の解答が完了しているかの判断
    userAnswerCheck: function () {
      // ユーザーの解答データの取得（Answer.vueの `userAnswer` と同じ）
      const pickedAnswerArray = this.answerData.pickedAnswer;   // ラジオボタンの解答取得
      const checkedAnswerArray = this.answerData.checkedAnswer; // チェックボックスの解答取得
      // ラジオボタン/チェックボックスの解答配列を結合
      // ref: https://qiita.com/takeharu/items/d75f96f81ff83680013f
      const concatAnswerArray = pickedAnswerArray.concat(checkedAnswerArray);
      // null を削除
      // ref: https://qiita.com/akameco/items/1636e0448e81e17e3646
      const deletedBlankAnswerArray = concatAnswerArray.filter(Boolean);
      // 昇順ソート
      // ref: https://qiita.com/PianoScoreJP/items/f0ff7345229871039672
      const sortAnswerArray = deletedBlankAnswerArray.sort((a,b) => {
        if( a < b ) return -1;
        if( a > b ) return 1;
        return 0;
      });
      // 各設問ごとのオブジェクトに格納
      // オブジェクトのキーに変数を使う
      // ref: https://qiita.com/kmagai/items/95481a3b9fd97e4616c9
      // オブジェクトへのpush
      // ref: https://stackoverflow.com/questions/8925820/javascript-object-push-function/8925849
      const objectAnswer = [];
      const objectAnswerArray = [];
      sortAnswerArray.forEach((value,key) => {
        const valueString = value.toString();
        const questionIndex = valueString.match(/\d{1,}_/g)[0].replace('_','');  // 正規表現で質問のインデックスを取得
        const questionAnswer = valueString.match(/_\D{1,}/g)[0].replace('_',''); // 正規表現で質問の回答を取得
        objectAnswer[key] = {
          id: Number(questionIndex),
          answer: questionAnswer
        }
        objectAnswerArray.push(objectAnswer[key]);
      });
      // 同じキー（質問ID）を持った答えは配列にまとめる
      // ref: https://stackoverflow.com/questions/57319602/
      const data = objectAnswerArray.reduce((acc, value) => {
        acc[value.id] = acc[value.id] ? acc[value.id] : [];
        acc[value.id] ? acc[value.id].push(value.answer) : [value.answer];
        return acc;
      }, {});
      let formattedAnswerArray = Object.entries(data).map(d => ({ id: d[0], answer: d[1] }) );

      // 解答のデータ数、設問データ数を比べて同じ数であれば答え合わせボタンを活性化
      const answerDataLength = formattedAnswerArray.length; // 解答のデータ数
      const qaDataLength = this.qaData.length;              // 設問データ数
      if ( answerDataLength === qaDataLength ) {
        // 答え合わせボタンを活性化
        this.checkButtonFlag = true;
      } else {
        // 答え合わせボタンは非活性
        this.checkButtonFlag = false;
        // スコアエリア / 解答エリアを非表示
        const $answer = $('.answer');
        const $score = $('.score');
        $score.removeClass('is-active');
        $answer.removeClass('is-active');
      }
    }
  }
}
</script>

<style lang="scss" scoped>
.check__block {
}
.check__text {
  @include text02;
  @include fontBold;
  text-align: center;
}
.check__button {
  margin-top: 15px;
  background: none;
  border: 0;
  width: 100%;
  padding: 18px 0;
  background: $red_01;
  box-shadow: 0 0 10px rgba($black_01,.3);
  border: $red_01 4px solid;
  border-radius: 2px;
  color: $white_01;
  font-size: 24px;
  line-height: 1;
  @include fontBold;
  @include anime;
  @include mq {
    margin-top: 10px;
    padding: 18px 0;
    font-size: 20px;
  }
  &:hover {
    cursor: pointer;
    background: $white_01;
    color: $red_01;
    @include mq {
      background: $red_01;
      color: $white_01;
    }
  }
  &[disabled="disabled"] {
    opacity: .4;
    &:hover {
      cursor: default;
      background: $red_01;
      color: $white_01;
    }
  }
}
</style>
