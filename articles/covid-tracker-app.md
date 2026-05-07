---
title: "VUEで３ヶ国語対応するアプリを作ってみよう！"
emoji: "🔰"
type: "tech"
topics: ['vuejs','language','初心者']
published: true
---

こんにちは！
初心者向けのプロジェクトを紹介します。
VUEで作って３ヶ国語対応できる新型コロナトラッキングのアプリケーションです。
今回のプロジェクトでは全世界（Globalの部分）しか使わないのですが、こちらのAPIには２９５カ国の情報もあって、APIのデータの取得して表示する練習したい方ぜひぜひ使ってあげてください。

## 開発ツール
VUE CLI(v.2)
I18n (https://github.com/kazupon)
API：Covid Tracker (https://covid19api.com/）

## スタイリングについて
今回Tailwindに関する説明しないのですが、コードにスタイルを書き込んでいます。
public/index.htmlにCDNを貼る場合
```
<script src="https://cdn.tailwindcss.com"></script>
```
OR
下記のページに沿ってインストールすれば使っているスタイルが反映されます。（頑張って！）
https://medium.com/featurepreneur/set-up-tailwind-css-for-your-vue-js-app-5a8801fd0a55

インストール方法
```
npm install -g @vue/cli
```
CLIインストールできたら新規プロジェクトを作成しましょう。
```
vue create アプリ名
```
※Default ver.2を選んでいます。

きちんと走っているか確認します。
```
npm run serve
```
作っていただいたVUEプロジェクトに戻りI18nというパッケージをインストールしていきます。
```
npm install vue-i18n
```
インストール開始するとつの質問が出てきます。
写真をはる。

package.jsonのdependenciesが下記のパッケージを入っているか確認してください。
```
"dependencies": {
    "core-js": "^3.6.5",
    "vue": "^2.6.11",
    "vue-i18n": "^8.27.0"
},
```
src/main.js移動して設定して行きます。

今回とてもシンプルなアプリケーションなので、全ての翻訳はmain.jsでやっていきます。
大きいプロジェクトになるとlocalesというファイルを作成し言語毎にjsonをわけるのがおすすめです。
例）
locales
-en.json (英語)
-ja.json（日本語）
-es.json（スペイン語）

src/main.jsにi18nパッケージをimportしましょう。
```js
import Vue from 'vue'
import App from './App.vue'
import VueI18n from 'vue-i18n';

Vue.use(VueI18n);

const messages = {
  en: {
    titles:{
        title:"COVID-19 TRACKER",
        },
  },
  ja: {
    titles:{
        title:"新型コロナウイルスの状況",
       }
  },
  es: {
    titles:{
        title:"Estadisticas de COVID-19",
       }
  }

};

const i18n = new VueI18n({
  locale: 'ja',
  messages: messages
});

Vue.config.productionTip = false

new Vue({
  render: h => h(App),
  i18n
}).$mount('#app')
```

componentsフォルダーに移動して、2つコンポネートを作成します。

１つ目：Test.vue (言語をテストするためのもの)
```
<template>
  <div class="text-3xl text-center bg-blue-800 text-white p-4 mb-10 ">
    {{ $t('titles.title')}}
  </div>
</template>

<script>
    export default {
    name: 'Test'
    }
</script>
```

２つ目：SwitchLang.vue (言語切り替えしてくれるもの)
```
<template>
  <div>
    <select class="w-1/5 p-4 border-2 mb-4" v-model="$i18n.locale">
      <option
        v-for="(lang, i) in langs"
        :key="`lang-${i}`"
        :value="lang">
          {{ lang }}
      </option>
    </select>
  </div>
</template>

<script>
export default {
  name: 'SwitchLang',
  data() {
    return { langs: ['en', 'ja', 'es'] }
  }
}
</script>

```

最後にApp.jsにimportして、npm run serveでもう一度走らせましょう。
App.js
```js
<template>
  <div class="w-3/4 items-center m-auto">
    <SwitchLang />
    <Test />
    <DataBox/>
  </div>
</template>

<script>
  import Test from '@/components/Test.vue'
  import SwitchLang from '@/components/SwitchLang.vue'

  export default {
    name: 'App',
    components: {
      SwitchLang,
      Test
    }
  }
</script>
```
これで動いたらその他のコンポーネントを作りましょう。

### ROUND 2 

App.jsからApiを読んでコロナ感染状況に関するデータを取得します。

```
data(){
    return{
      lang:'ja',
      loading: true,
      title:'Global',
      countries: [],
      stats: {},
    }
  },
  methods:{
    async fetchCovidData(){
      const response = await fetch('https://api.covid19api.com/summary');
      const data = response.json();
      return data;
    },

    },
    async created(){
        const caseData = await this.fetchCovidData();
        this.countries = caseData.Countries
        this.stats = caseData.Global
        this.loading = false
    }

```
取得したデータを表示してくれるコンポーネントを作成します。

components
-DataBox

```
<template>
  <div>
    <div class="shadow-md rounded bg-blue-100 p-10 text-center">
        <h3 class="text-3xl font-bold mb-4 justify-center text-blue-900">{{$t("titles.cases")}}
        </h3>
      <div class="text-2xl mb-4">
        <span class="font-bold">{{$t("titles.newCases")}}</span> 
        {{addComma(stats.NewConfirmed)}} 
      </div>
      <div class="text-2xl mb-4">
        <span class="font-bold">{{$t("titles.total")}}</span> 
        {{addComma(stats.TotalConfirmed)}} 
      </div>
    </div>

    <div class="shadow-md rounded bg-blue-200 p-10 text-center">
        <h3 class="text-3xl font-bold mb-4 justify-center text-blue-900">{{$t("titles.deaths")}}
        </h3>
      <div class="text-2xl mb-4">
        <span class="font-bold">{{$t("titles.deathNum")}}</span> 
        {{addComma(stats.NewDeaths)}} 
      </div>
      <div class="text-2xl mb-4">
        <span class="font-bold">{{$t("titles.total")}}</span> 
        {{addComma(stats.TotalDeaths)}} 
      </div>
    </div>

  </div>
</template>

<script>
export default {
  name:"DataBox",
  props:{
  stats:{
    type: Object,
    default:()=>{}
  }
  },

  methods:{
    addComma(number) {
      return number.toString().replace(/\B(?=(\d{3})+(?!\d))/g, ",")
    }
  }

}
</script>

```
App.jsにDataBoxをインポートし、APIから取得したデータを渡します。

```
 <DataBox :stats="stats"/>
```

最後にmain.jsに戻って、残りの翻訳を入れましょう。

```
const messages = {
  en: {
    titles:{
        title:"COVID-19 TRACKER",
        cases: "Cases",
        newCases:"New Cases",
        deaths: "Deaths",
        deathNum:"New Deaths",
        total:"Total",
        select:"Select a country"
        },
  },
  ja: {
    titles:{
        title:"新型コロナウイルスの状況",
        cases: "感染者",
        newCases:"新規感染者",
        deaths: "死亡者",
        deathNum:"人数",
        total:"合計",
        select:"国を選択"
        }
  },
  es: {
    titles:{
        title:"Estadisticas de COVID-19",
        cases: "Infectados",
        newCases:"Nuevos casos",
        deaths: "Muertes",
        deathNum:"Numero de muertes",
        total:"Total",
        select:"Selecciona un pais"
        }
  }

}
```

お疲れさまでした！

参考にしたサイト
https://lokalise.com/blog/vue-i18n/
https://qiita.com/mi_upto/items/6d76dcd2d2c09b1bcb88