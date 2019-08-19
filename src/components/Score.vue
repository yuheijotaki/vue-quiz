<template>
  <section class="section score">
    <h2 class="section__title">スコア</h2>
    <p class="hidden">{{getQuestionAnswer}}</p>
    <p class="hidden">{{getUserAnswer}}</p>
    <div class="score__block">
      <p class="score__result">{{getQuestionLength}}問中<span class="score__resultAttention">{{calcScore}}問</span>正解でした</p>
    </div>
  </section>
</template>

<script>
import $ from 'jquery';

export default {
  name: 'score',
  props: [ // App.vue から質問/解答リストの受け取り
    'qaData',
    'answerData'
  ],
  data () {
    return {
      correctAnswerArray: [],
      userAnswerArray: []
    }
  },
  methods: {
  },
  computed: {
    // 設問数の取得
    getQuestionLength: function() {
      const qaDataLength = this.qaData.length; // 設問データ数
      return qaDataLength;
    },
    // 設問の解答を配列に格納
    getQuestionAnswer: function() {
      const thisQaData = this.qaData;
      thisQaData.forEach((value) => {
        this.correctAnswerArray.push(value.ansCorrect);
      });
      return this.correctAnswerArray;
    },
    // ユーザーの解答を配列に格納
    getUserAnswer: function () {
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
      // ユーザーの解答を配列に格納
      this.userAnswerArray = []; // 初期化
      formattedAnswerArray.forEach((value) => {
        this.userAnswerArray.push(value.answer);
      });
      return this.userAnswerArray;
    },
    // スコアの計算
    calcScore: function () {
      const correctAnswer = this.correctAnswerArray;
      const userAnswer = this.userAnswerArray;
      let userScore = 0;
      correctAnswer.forEach((value,index) => {
        const correctAnswerValue = value;
        const userAnswerValue = userAnswer[index];
        if ( array_equal(correctAnswerValue,userAnswerValue) ) {
          userScore ++;
        }
      });
      return userScore;
    }
  }
}
// 配列の等価比較
// ref: https://marycore.jp/prog/js/array-equal/
function array_equal(a, b) {
  if (!Array.isArray(a))    return false;
  if (!Array.isArray(b))    return false;
  if (a.length != b.length) return false;
  for (var i = 0, n = a.length; i < n; ++i) {
    if (a[i] !== b[i]) return false;
  }
  return true;
}
</script>

<style lang="scss" scoped>
.score {
  display: none;
  &.is-active {
    display: block;
  }
}
.hidden {
  display: none;
}
.score__block {
  background: $lightYellow_01;
  border: $lightYellow_02 4px solid;
  margin-top: 20px;
  padding: 40px;
  box-shadow: 0 0 10px rgba($black_01,.1);
  border-radius: 2px;
  @include mq {
    padding: 30px;
  }
}
.score__result {
  @include title01;
  @include fontBold;
  text-align: center;
}
.score__resultAttention {
  color: $red_01;
  font-size: 42px;
  margin: 0 10px;
  @include mq {
    font-size: 36px;
    margin: 0 8px;
  }
}
</style>
