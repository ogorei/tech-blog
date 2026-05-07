---
title: "（Vue）ライブラリなしで作るスクロール表示アニメーション"
emoji: "🌟"
type: "tech"
topics: ['VUEJS','アニメーション','css']
published: true
---
スクロールに合わせて要素が「ふわっ」と出てくる演出、ついライブラリに頼りがちですが、実は **CSS + ほんの少しの JavaScript** だけで十分作れます。

この記事では **CSS の `transition` と、短いスクロール判定コード** だけで、よく見る「スクロールで表示されるアニメーション」を Vue 上で実装します。

- **作るもの**: スクロールで `.reveal` に `.active` を付け外しして、表示/非表示を切り替える
- **メリット**: 依存が増えない / 読みやすい / どの案件でも流用できる

`transition` がまだ曖昧なら、先にこちらがおすすめです。  
`https://webst8.com/blog/css-transition/`

### 最終的にこうなります！

![](https://i.gyazo.com/cccb9f1008ad73b29be9c47e01c37db6.gif)


## Vue環境を整える

まずはスタートファイルをクローンします。
```
git clone git@github.com:ogorei/simple-scroll-starter.git
```
ダウンロードできたら必要なパッケージをインストールします。
```
npm install
```
ローカルで起動して表示を確認します。
```
npm run serve
```

### STEP 1
`<style>` に CSS を追加します。

ポイントは、通常時は「下にズラして透明」、`.active` が付いたら「元の位置に戻して表示」です。  
`transition` で変化を滑らかにします。

```
.reveal{
  position: relative;
  transform: translateY(150px);
  opacity: 0;
  transition: 2s all ease;
}
.reveal.active{
  transform: translateY(0);
  opacity: 1;
}
```

### STEP 2

`methods` に `reveal` 関数を書きます。

まずは `.reveal` が付いている要素をまとめて取得します。

```
methods:{
    reveal() {
      const reveals = document.querySelectorAll(".reveal");
    }
}
```

次に「要素が画面内に入ったか」を判定します。ここでは `getBoundingClientRect()` と `window.innerHeight` を使います。

Element.getBoundingClientRect() メソッドは、要素の寸法とそのビューポートに対する相対位置に関する情報を返します。(MDNにより)

innerHeight は Window インターフェイスの読み取り専用プロパティで、ウィンドウの内部の高さをピクセル単位で返します。水平スクロールバーがあれば、その高さを含みます。

<img src="https://developer.mozilla.org/en-US/docs/Web/API/Element/getBoundingClientRect/element-box-diagram.png">


```
for (let i = 0; i < reveals.length; i++) {
        const windowHeight = window.innerHeight;
        const elTop = reveals[i].getBoundingClientRect().top;
        const elVisible = 150;
        if (elTop < windowHeight - elVisible) {
          reveals[i].classList.add("active");
        } else {
          reveals[i].classList.remove("active");
        }
      }

```

補足）
- `windowHeight`: ビューポートの高さ
- `elTop`: 要素の上端がビューポート上端からどれくらい離れているか
- `elVisible`: 何px 手前で発火させるか（この例は 150px）


最後に、条件に応じて `.active` を付け外しします。

```
if (elTop < windowHeight - elVisible) {
  reveals[i].classList.add("active");
} else {
  reveals[i].classList.remove("active");
}

```

`reveal` 関数全体はこうなります。

```
  reveal() {
    const reveals = document.querySelectorAll(".reveal");
    for (let i = 0; i < reveals.length; i++) {
      const windowHeight = window.innerHeight;
      const elTop = reveals[i].getBoundingClientRect().top;
      const elVisible = 150;
      if (elTop < windowHeight - elVisible) {
        reveals[i].classList.add("active");
      } else {
        reveals[i].classList.remove("active");
      }
    }
  }

```

最後に `created()` のタイミングでイベントを登録して完了です。

```
    window.addEventListener("scroll", this.reveal);

```

#### 片付け（任意だけど推奨）
ページ遷移がある構成だと、イベントを付けっぱなしにしない方が安心です。`beforeUnmount`（Vue 3）や `beforeDestroy`（Vue 2）で解除しておくと事故が減ります。

```
window.removeEventListener("scroll", this.reveal);
```

## まとめ
やっていることはシンプルで、**「CSSで初期状態を隠す」→「スクロールで表示条件を満たしたら `.active` を付ける」** だけです。まずは `elVisible` の値を変えて、気持ちいい発火タイミングを探してみてください。

