<template>
  <section class="section answer">
    <h2 class="section__title">解答・解説</h2>
    <!--
    <p>{{ userAnswer }}</p>
    -->
    <div class="answer__block">
      <div class="answer__list u-qa__list">
        <div class="answer__item u-qa__item" v-for="(qa,qaIndex) in qaData" :key="qaIndex">
          <div class="u-qa__number">
            <p class="u-qa__numberLabel">{{qaIndex+1}}</p>
          </div>
          <div class="answer__title u-qa__title">
            <p class="answer__titleText u-qa__text">{{qa.queText}}</p>
          </div>
          <ul class="answer__answer u-qa__answer">
            <li class="answer__answerText u-qa__answerText" v-for="(choiceVal,choiceKey,choiceIndex) in qa.ansChoice" v-bind:key="choiceIndex">
              <template v-if="answerChoiseCorrect([qa.ansCorrect][0],[choiceKey][0])">
                <span class="answer__answerTextLabel answer__answerTextLabel--correct">{{ choiceKey }}:{{ choiceVal }}</span>
              </template>
              <template v-else>
                <span class="answer__answerTextLabel">{{ choiceKey }}:{{ choiceVal }}</span>
              </template>
            </li>
          </ul>
          <div class="answer__result">
            <div v-for="(userAnswerResult) in userAnswer" :key="userAnswerResult.id">
              <template v-if="qaIndex === toNumber(userAnswerResult.id)">
                <template v-if="array_equal( qa.ansCorrect, userAnswerResult.answer)">
                  <p class="answer__resultText"><b>⭕正解っぽいです&nbsp;🙆🏻</b></p>
                </template>
                <template v-else>
                  <p class="answer__resultText"><b>❌不正解っぽいです&nbsp;🙅🏻</b> あなたの解答は <b>{{toString(userAnswerResult.answer)}}</b> でした&nbsp;😣</p>
                </template>
              </template>
            </div>
          </div>
          <div class="answer__commentary">
            <p class="answer__commentaryText" v-html="qa.ansCommentary"></p>
          </div>
        </div><!-- .answer__item -->
      </div>
    </div>
  </section>
</template>

<script>
import $ from 'jquery';

export default {
  name: 'answer',
  props: [ // App.vue から質問/解答リストの受け取り
    'qaData',
    'answerData'
  ],
  data () {
    return {
    }
  },
  created: function(){
    // console.table(this.answerData);
    // console.log(this.qaData);
  },
  methods: {
    // 解答リストの各選択肢の正誤判定
    answerChoiseCorrect(item,key) {
      return item.indexOf(key) > -1;
    },
    toNumber(string) {
      return Number(string);
    },
    toString(string) {
      return string.toString();
    },
    // 配列の等価比較
    // ref: https://marycore.jp/prog/js/array-equal/
    array_equal(a, b) {
      if (!Array.isArray(a))    return false;
      if (!Array.isArray(b))    return false;
      if (a.length != b.length) return false;
      for (var i = 0, n = a.length; i < n; ++i) {
        if (a[i] !== b[i]) return false;
      }
      return true;
    },
    // 配列 カンマ区切りに置き換え
    arrayImplode(array) {
      return implode(",",array);
    }
  },
  computed: {
    // answerData からユーザーの解答データ配列を作成する
    userAnswer: function () {
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
      // console.table(formattedAnswerArray);
      return formattedAnswerArray;
    }
  }
}
</script>

<style lang="scss" scoped>
.answer {
  display: none;
  &.is-active {
    display: block;
  }
}
.answer__answerTextLabel {
  color: $gray_02;
  &.answer__answerTextLabel--correct {
    color: $red_01;
    @include fontBold;
  }
}

.answer__resultText {
  @include text02;
  @include fontMedium;
  margin-top: 20px;
  @include mq {
    margin-top: 15px;
  }
}
.answer__commentary {
  margin-top: 5px;
  @include mq {
    margin-top: 3px;
  }
}
.answer__commentaryText {
  @include text01;
}
</style>
