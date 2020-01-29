# README
Let's study & enjoy Vue.js

- <a href="https://github.com/NakatsuboYusuke/dev-Vuejs#chapter-1">Chapter 1 Basic</a>
- <a href="https://github.com/NakatsuboYusuke/dev-Vuejs#chapter-2">Chapter 2 Data</a>
- <a href="https://github.com/NakatsuboYusuke/dev-Vuejs#chapter-3">Chapter 3 Event, Form</a>
- <a href="https://github.com/NakatsuboYusuke/dev-Vuejs#chapter-4">Chapter 4 Watch, Processing Data</a>
- <a href="https://github.com/NakatsuboYusuke/dev-Vuejs#chapter-5">Chapter 5 Component</a>


## Chapter 1

- フレームワーク<br>
アプリケーションを設計するための骨組みのことで、基本的な機能やルールを備えている。

- Vue.js のコンセプト<br>
データ駆動。データを中心としたアプリケーション設計のこと。<br>
具体的には、画面を描画する構造の本体は、DOMではなくJavaScriptのデータとなる。

- データバインディング<br>
データと描画を同期させる仕組みのこと。<br>
ex) v-if, v-bind, key, ...<br>

```
v-bind:value.sync = "message"
```

・v-bind => ディレクティブ<br>
・:value => 引数<br>
・.sync => 修飾子<br>
・"message" => 値

- リアクティブデータ<br>
Vue.js が取得したとき(get)と代入したとき(set)のフック処理が登録されたデータのこと。<br>
Vue.js 自体が実行する様々な処理の仕組みをリアクティブシステムという。

- ディレクティブ<br>
テンプレートとロジックを関連づける機能のこと。

- マウント(mount)<br>
配置する要素とアプリケーションを紐づけること。

- コンポーネント<br>
機能ごとにJavaScriptとテンプレートを1つのセットにして、他の機能とは分離して開発できるようにする仕組みのこと。

- インスタンスの生成

```
var app = new Vue({
  // オプション
})
```

※Vue => コンストラクタ関数

- ライフサイクルフック<br>
あらかじめ登録した処理を、Vueインスタンスの特定のタイミングで自動的に呼び出す仕組みのこと。


## Chapter 2

### テキストへのデータバインディング

#### マスタッシュ記法
テキストコンテンツをデータバインディングするための記法のこと。<br>
属性には使用することができない。

```
<div id="app">
  <p>{{ message }}</p>
</div>

var app = new Vue({
  el: 'app',
  data: {
    message: 'Hello, Vue.js!'
  }
})

// => Hello, Vue.js!
```

{{ message }} は、dataのmessageプロパティと常に同期する。

```
<div id="app">
  <input type="text" value="{{ message }}">
  <input type="text" v-bind:value="message.value">
  <input type="text" :value="message.value">
</div>

var app = new Vue({
  el: 'app',
  data: {
    message: 'Hello, Vue.js!'
  }
})

// => Error
// => Hello, Vue.js!
// => Hello, Vue.js!
```

属性にマスタッシュ記法を使う場合、v-bindディレクティブを使う。


### this が何を指すのか

```
methods: {
  increment: function() {
    // インスタンスのthisを参照
    this.count += 1
  }

  increment: () => {
    // アロー関数はインスタンスのthisを参照できない
    this.count += 1
  }
  // メソッドの外側部分のスコープがthisとなってしまう

  // 省略する場合の書き方
  increment() {
    // インスタンスのthisを参照
    this.count += 1
  }
}
```

### クラスへのデータバインディング

```
<p v-bind:class="{ child: isChild, 'is-active': isActive }">Text</p>

data: {
  isChild: true,
  isActive: true
}

// =>
<p class="child is-active">Text</p>

// 三項演算子を使う場合(非推奨)
<p v-bind:class="[isActive ? 'active' : 'normal', otherClass]">Text</p>

// =>
<p class="active">Text</p>
```

### スタイルへのデータバインディング

```
<p v-bind:style="{ color: textColor, backgroundColor: bgColor }">Text</p>

data: {
  textColor: 'red',
  bgColor: 'lightgray'
}

// =>
<p style="color: red; background-color: lightgray;">Text</p>
```

### クラスやスタイルは、オブジェクトデータを渡すとスマート

```
<p v-bind:class="classObjext">Text</p>
<p v-bind:style="styleObject">Text</p>

data: {
  classObjext: {
    child: true,
    'is-active': true
  },
  styleObject: {
    color: 'red',
    backgroundColor: 'lightgray'
  }
}

// =>
<p class="child is-active">Text</p>
<p style="color: red; background-color: lightgray;">Text</p>
```

### 複数のデータへのデータバインディング

```
<img v-bind="item">
<img v-bind="item" v-bind:id="'thumb-' + item.id">

data: {
  item: {
    id: 1,
    src: 'item1.jpg',
    alt: '商品1サムネイル',
    width: 200,
    height: 200
  }
}

// =>
<img id="1" src="item1.jpg" alt="商品1サムネイル" width="200" height="200">
<img id="thumb-1" src="item1.jpg" alt="商品1サムネイル" width="200" height="200">
```

### SVGデータへのデータバインディング

```
<svg xmlns="http://www.w3.org/2000/svg" version="1.1">
  <circle cx="100" cy="75" v-bind:r="radius" fill="lightpink" />
</svg>
<input type="range" min="0" max="100" v-model="radius">
```

### 条件分岐(if文)
v-ifでは要素はDOMレベルで削除される。v-showでは要素はスタイル(display: none;)で隠蔽される。

```
<div v-if="ok">v-if条件による描画</div>
<div v-show="ok">v-show条件による描画</div>

data: {
  ok: true // or false
}
```

- templateタグでv-ifをグループ化

```
<template v-if="ok">
  <header>タイトル</header>
  <div>コンテンツ</div>
</template>

data: {
  ok: true // or false
}
```

- v-if, v-else-if, v-elseでグループ化

```
<div v-if="type === 'A'">
  type は A
</div>
<div v-else-if="type === 'B'">
  type は B
</div>
<div v-else>
  タイプはありません。
</div>

data: {
  ok: true // or false
}
```
- v-ifのグループ内で同じ要素を使用する場合、ユニークなkeyを付与しエスケイプする

```
<div v-if="loaded" key="content-visible">
  content
</div>
<div v-else key="content-loading">
  loading now...
</div>

data: {
  loaded: true // or false
}
```

### 繰り返し処理(for文)

- リストデータはオブジェクトを要素とした配列にする

```
data: {
  list: [
    { id:1, name: 'スライム', hp: 100 },
    { id:2, name: 'ゴブリン', hp: 200 },
    { id:3, name: 'ドラゴン', hp: 500 }
  ]
}
```
- リストデータのデータバインディング
繰り返し描画させたいタグに対して、v-forディレクティブを使用する。<br>
要素の識別と効率的な描画処理のために要素にはユニークなIDを指定する。ただし、同じ親要素内でキーの重複はできない。

```
<div id="app">
  <ul>
    <li v-for="item in list" v-bind:key="item.id">
      ID.{{ item.id }} {{ item.name }} HP.{{ item.hp }}
    </li>
    </ul>
</div>

data: {
  list: [
    { id:1, name: 'スライム', hp: 100 },
    { id:2, name: 'ゴブリン', hp: 200 },
    { id:3, name: 'ドラゴン', hp: 500 }
  ]
}

// =>
<ul>
  <li>ID.1 スライム HP.100</li>
  <li>ID.2 ゴブリン HP.200</li>
  <li>ID.3 ドラゴン HP.500</li>
</ul>
```

### if文とfor文
v-ifディレクティブの条件に合致しないものは、コメントアウトを出力する。

```
<ul>
  <li v-for="item in list"
      v-bind:key="item.id"
      v-bind:class="{ tsuyoi: item.hp > 300 }">
    ID.{{ item.id }} {{ item.name }} HP.{{ item.hp }}
    <span v-if="item.hp > 300">つよい！</span>
  </li>
</ul>

// =>
<ul>
  <li class="">ID.1 スライム HP.100<!----></li>
  <li class="">ID.2 ゴブリン HP.200<!----></li>
  <li class="tsuyoi">ID.3 ドラゴン HP.500<span>つよい！</span></li>
</ul>
```

- for文に直接if文を記述する場合

```
<ul>
  <li v-for="item in list"
      v-bind:key="item.id"
      v-if="item.hp > 300">
    ID.{{ item.id }} {{ item.name }} HP.{{ item.hp }}
  </li>
</ul>

// =>
<ul>
  <!---->
  <!---->
  <li>ID.3 ドラゴン HP.500</li>
</ul>
```

### メソッドとfor文
- v-modelディレクティブを使って、listプロパティに新しい要素を追加

```
名前 <input v-model="name">
<button v-on:click="doAdd">モンスターを追加</button>
<ul>
  <li v-for="item in list" v-bind:key="item.id">
    ID.{{ item.id }} {{ item.name }} HP.{{ item.hp }}
  </li>
</ul>

var app = new Vue({
  el: "#app",
  data: {
    // 初期値
    name: 'キマイラ',
    list: [
      { id:1, name: 'スライム', hp: 100 },
      { id:2, name: 'ゴブリン', hp: 200 },
      { id:3, name: 'ドラゴン', hp: 500 }
    ]
  },
  methods: {
    doAdd: function() {
      // リスト内で一番大きいIDを取得する
      var max = this.list.reduce(function(a, b) {
        return a > b.id ? a : b.id
      }, 0)
      // リストに追加
      this.list.push({
        id: max + 1,
        name: this.name,
        hp: 500
      })
    }
  }
})

// =>
<ul>
  <li id="1">
    ID.1 スライム HP.100
  </li>
  <li id="2">
    ID.2 ゴブリン HP.200
  </li>
  <li id="3">
    ID.3 ドラゴン HP.500
  </li>
  <li id="4">
    ID.4 キマイラ HP.500
  </li>
</ul>
```

- listプロパティから要素を削除

```
名前 <input v-model="name">
<button v-on:click="doAdd">モンスターを追加</button>
<ul>
  <li v-for="(item, index) in list" v-bind:key="item.id">
    ID.{{ item.id }} {{ item.name }} HP.{{ item.hp }}
    <!-- for文の中にボタンを作成 -->
    <button v-on:click="doRemove(index)">モンスターを削除</button>
  </li>
</ul>

var app = new Vue({
  el: "#app",
  data: {
    // 初期値
    name: 'キマイラ',
    list: [
      { id:1, name: 'スライム', hp: 100 },
      { id:2, name: 'ゴブリン', hp: 200 },
      { id:3, name: 'ドラゴン', hp: 500 }
    ]
  },
  methods: {
    doAdd: function() {
      // リスト内で一番大きいIDを取得する
      var max = this.list.reduce(function(a, b) {
        return a > b.id ? a : b.id
      }, 0)
      // リストに追加
      this.list.push({
        id: max + 1,
        name: this.name,
        hp: 500
      })
    },
    doRemove: function(index) {
      // インデックスの位置から要素を1個削除
      this.list.splice(index, 1)
    }
  }
})

// =>
<ul>
  <li>
    ID.1 スライム HP.100
    <button>モンスターを削除</button>
  </li>
  <li>
    ID.2 ゴブリン HP.200
    <button>モンスターを削除</button>
  </li>
  <li>
    ID.3 ドラゴン HP.500
    <button>モンスターを削除</button>
  </li>
</ul>
```

#### 配列メソッド
- push<br>
配列の末尾に要素を追加する
- pop<br>
配列の末尾の要素を削除する
- shift<br>
配列の末尾に要素を追加する
- unshift<br>
配列の先頭に要素を削除する
- splice<br>
配列の指定位置から要素を取り出し、要素を追加する
- sort<br>
配列を比較関数にしたがってソートする
- reverse<br>
配列の並び順を逆にする

- listプロパティを追加する
this.$set メソッドでリアクティブデータにプロパティを追加する。

```
this.$set(更新するデータ, インデックス or キー, 新しい値)

// =>
this.$set(this.list, 0, { id: 1, name: 'キングスライム', hp: 500 })
```

```
data: {
  ~
  created: function() {
    this.list.forEach(function(item) {
      this.$set(item, 'active', false)
    }, this)
  }
}
```

- listプロパティを更新する

```
<span v-if="item.hp < 50">瀕死！</span>
<button v-on:click="doAttack(index)">攻撃する</button>

methods: {
  ~
  doAttack: function(index) {
    this.list[index].hp -= 10
  }
}
```

- listそのものを更新する
filterメソッドでlistデータそのものを更新する

```
this.list = this.list.filter(function(el) {
  return el >= 100
})
```

- ユニークキーを持たないv-forディレクティブ

```
<select>
  <option v-for="item in list">{{ item }}</option>
</select>

data: {
  list: [
    'スライム', 'ゴブリン', 'ドラゴン'
  ]
}

// =>
<select>
  <option>スライム</option>
  <option>ゴブリン</option>
  <option>ドラゴン</option>
</select>
```

- オプションにデータを持たないv-forディレクティブ

```
<span v-for="item in 15">{{ item }}</span>
<span v-for="item in [1, 5, 10, 15]">{{ item }}</span>

// =>
<span>1</span>
<span>2</span>
<span>3</span>
<span>4</span>
<span>5</span>
<span>6</span>
<span>7</span>
<span>8</span>
<span>9</span>
<span>10</span>
<span>11</span>
<span>12</span>
<span>13</span>
<span>14</span>
<span>15</span>

<span>1</span>
<span>5</span>
<span>10</span>
<span>15</span>
```

- 文字列に対するv-forディレクティブ

```
<span v-for="item in text">{{ item }}</span>

<span>H</span>
<span>e</span>
<span>l</span>
<span>l</span>
<span>o</span>
```

### 外部からデータを取得する(JSON, WebAPI)

```
<div id="app">
  <ul>
    <li v-for="(item, index) in list" v-bind:key="item.id">
      ID.{{ item.id }} {{ item.name }} HP.{{ item.hp }}
    </li>
  </ul>
</div>
<script src="https://cdn.jsdelivr.net/npm/vue/dist/vue.js"></script>
<script src="https://cdn.jsdelivr.net/npm/axios/dist/axios.min.js"></script>

var app = new Vue({
  el: '#app',
  data: {
    // 空配列をセットしておく
    list: []
  },
  // ローカルで動かす場合は、ローカルサーバーを立てる
  created: function() {
    axios.get('list.json').then(function(response) {
      this.list = response.data
      console.log(response.data)
    }.bind(this)).catch(function(e) {
      console.log(e)
    })
  }
})
```

- 簡易的なローカルサーバー

```
$ npm install -g live-server
$ cd ルートディレクトリ
$ live-server .
```

### DOMに直接アクセスする
DOMにアクセスするためにはインスタンスプロパティ$el, $refsを使用する。

- $el ルート要素へアクセスする

```
<div id="app"></div>

var app = new Vue({
  el: '#app',
  mounted: function() {
    console.log(this.$el)
  }
})

// =>
<div id="app"></div>
```

- $refs ルート以外の要素へアクセスする

```
<div id="app">
  <p ref="hello">Hello, World!</p>
</div>

var app = new Vue({
  el: '#app',
  mounted: function() {
    console.log(this.$refs.hello)
  }
})

// =>
<p>Hello, World!</p>
```

- $el, $refsは一時的な変更
$el, $refsによるDOMの操作が仮想DOMによって上書きされる点に注意する。

```
<div id="app">
  <button v-on:click="handClick">カウントアップ</button>
  <button v-on:click="show=!show">表示/非表示</button>
  <span ref="count" v-if="show">0</span>
</div>

var app = new Vue({
  el: '#app',
  data: {
    show: true
  },
  methods: {
    handClick: function() {
      var count = this.$refs.count
      if (count) {
        // parseInt 文字列型を数値型に変換する
        count.innerText = parseInt(count.innerText, 10) + 1
      }
    }
  }
})
```

### テンプレートを制御するディレクティブ

- v-pre HTMLコンパイルをスキップ

```
<a v-bind:href="url" v-pre>Hello {{ message }}</a>

data: {
  url: 'https://www.google.com/',
  message: 'Vue.js!'
}

// =>
<a v-bind:href="url" v-pre="">Hello {{ message }}</a>
```

- v-once 一度だけバインディングを行い以降はスキップ

```
<a v-bind:href="url" v-once>Hello {{ message }}</a>

data: {
  url: 'https://www.google.com/',
  message: 'Vue.js!'
}

// =>
<a href="https://www.google.com/">Hello Vue.js!</a>
```

- v-text マスタッシュの代わりにテキストコンテンツをバインディング

```
<span v-text="message"></span>

data: {
  message: 'Hello Vue.js!'
}

// =>
<span>Hello Vue.js!</span>
```

- v-html HTMLをバインディング
XSS脆弱性を引き起こすため、信頼できるテンプレートのみに使用する。

```
<span v-html="message"></span>

data: {
  message: 'Hello <strong>Vue.js</strong>'
}

// =>
<span>Hello <strong>Vue.js</strong></span>
```

- v-cloak インスタンスの準備が終わると取り除かれる。スタイルも同時に定義して使用する。

```
<div id="app" v-cloak>
  {{ message }}
</div>

[v-cloak] {
  display: none;
}
@keyframes cloak-in {
  0% {
    opacity: 0;
  }
}
#app {
  animation: cloak-in 1s;
}
#app[v-cloak] {
  opacity: 0;
}

data: {
  message: 'Hello Vue.js!'
}
```


## Chapter 3

### イベントハンドラ
JavaScriptのaddEventListennerメソッドは、Vue.jsではv-onディテクティブを使用する。jQueryでは$(element).onメソッド。

- メソッドイベントハンドラ<br>
コンポーネントのmethodsオプションに定義したメソッド名を指定する。

```
<button v-on:click="handleClick">クリック</button>
<button @click="handleClick">クリック</button>
```

```
<button v-on:click="handleClick">クリック</button>

var app = new Vue({
  el: '#app',
  methods: {
    handleClick: function() {
      alert('クリックしたよ');
    }
    // 省略形
    // handleClick() {
      // alert('クリックしたよ');
    //}
  }
})
```

- インラインメソッドハンドラ<br>
ディレクティブの値にJavaScriptの式を直接定義する。

```
<button v-on:click="count++">クリック</button>
<button v-on:click="handleClick($event, item)">クリック</button>
```

イベントオブジェクトとスコープ内のitemプロパティを引数として持ち、メソッドを呼び出す。

- 使用可能なDOMイベント例

```
<!-- 要素のスクロールイベント -->
<div v-on:scroll="handler">
  コンテンツ
</div>
<!-- マウスのホイールイベント -->
<div v-on:mousewheel="handler">
  コンテンツ
</div>
<!-- 要素のドラッグイベント -->
<div v-on:dragstrat="handler" draggable>
  ドラッグ可能
</div>
<!-- フォーム -->
<input v-bind:value="message" v-on:click="handleInput">
methods: {
  handleInput: function(event) {
  // フック処理
  this.message = event.target.value
  }
}
// => v-onディレクティブを使うことでフック処理を行う
```

### イベント修飾子
DOMイベントの振る舞いを変更する。<br>
通常のHTMLでは、同じイベントをハンドルしたDOMがネストされている場合、event.target要素から順に親要素に向かってバブリング(イベントの伝播)される。

|修飾子|振る舞い|
|---|---|
|.stop|event.stopPropagation()を呼ぶ|
|.prevent|event.preventDefault()を呼ぶ|
|.capture|キャプチャモードでイベントを発生する|
|.self|event.target要素がそのもの自身のときのみ発火する|
|.native|コンポーネントで直接イベントを発火する|
|.once|一回のみイベントを呼ぶ|
|.passive|{ passive: true }でイベントを明示的に呼び出さない|

- stopPropagation()メソッド<br>
イベントが親要素に伝播するのをキャンセルするためのメソッド。

- preventDefault()メソッド<br>
イベントをキャンセルするためのメソッド。

- マウスイベントの修飾子

|修飾子|振る舞い|
|---|---|
|.left|マウスの左ボタンが押す|
|.right|マウスの右ボタンが押す|
|.middle|マウスの中央ボタンが押す|

- サンプル

```
<div v-on:click.rigth="handler">example</div>
<div v-on:click.right.prevent="handler">example</div>

methods: {
  handler: function(comment) {
  console.log(comment)
  }
}

// =>
MouseEvent {isTrusted: true, screenX: 182, screenY: 171, clientX: 47, clientY: 24, …}
// =>

```

- .stop イベントが親要素に伝播するのをキャンセルする

```
<div v-on:click="handler('div1')">
  div1
  <a href="#" v-on:click.stop="handler('div2')">div2</a>
</div>

// div2 をクリック =>
div2
```

- .prevent イベントをキャンセルする

```
<div v-on:click="handler('div1')">
  div1
  <a href="#" v-on:click.prevent="handler('div2')">div2</a>
</div>

// div2 をクリック =>
div2
div1
```

- .capture キャプチャモードでイベントを発生する<br>
ルート要素からevent.targetまでDOMツリーを探すキャプチャ中(キャプチャフェーズ)にイベントが発生する。<br>
バブリングモードより先に発生する。

```
<div v-on:click.capture="handler('div1')">
  div1
  <div v-on:click="handler('div2')">
    div2
    <div v-on:click="handler('div3')">div3</div>
  </div>
</div>

// div3 をクリック =>
div1
div3
div2
```

- .self event.target要素がそのもの自身のときのみ発火する。

```
<div class="overlay" v-on:click.self="close">...</div>
```

- .native コンポーネントで直接イベントを発火する

```
<!-- コンポーネントをクリックするとイベントが発火 -->
<my-component v-on:click.native="handler"></my-component>
<!-- コンポーネントをクリックしてもイベントが発火しない -->
<my-component v-on:click="handler"></my-component>
```

### キー修飾子
特定のキーコードの入力のみイベントを呼び出す。

```
<input v-on:keydown.13="handler">
<input v-on:keydown.enter="handler">
// いずれかのarrowキーが押されたとき
<input v-on:keydown.up.down.left.rigth="handler">
```

|修飾子|振る舞い|
|-----|-----|
|.enter|エンターキーを押す|
|.tab|tabキーを押す|
|.delete|deleteキーを押す|
|.esc|escキーを押す|
|.space|spaceキーを押す|
|.up|arrowキーの上を押す|
|.down|arrowキーの下を押す|
|.left|arrowキーの左を押す|
|.right|arrowキーの右を押す|

### システム修飾子
特定のキーコードが押されているときのみイベントを呼び出す。

|修飾子|振る舞い|
|-----|-----|
|.ctrl|ctrlキーが押されている|
|.alt|altキーが押されている|
|.shift|shiftキーが押されている|
|.meta|metaキーが押されている|

```
<button v-on:click.shift="doDelete">削除ボタン</button>
```

### フォーム
v-modelディレクティブを使いフォームの値をデータと同期させる双方向データバインディングを行う。<br>
v-modelディレクディブは、DOMのデータバインディングとリアクティブデータの処理を自動化する。<br>
入力フォームでは値は全て文字列型に、複数選択した値は配列型になる。<br>
なお、v-modelディレクティブを使用すると、value, checked, selected などの設定は無視される。

1. データバインディングで要素のvalue属性を更新する
2. イベントハンドリングで受け取った値をデータに代入する

```
<div id="app">
  <input v-model="message">
  <p>{{ message }}</p>
</div>

var app = new Vue({
  el: '#app',
  data: {
    // 初期値をセット
    message: 'Hello, World!'
  }
})
```

```
<!-- インプット -->
<input v-model="message">
<p>{{ message }}</p>

data: {
  // 初期値をセット
  message: 'Hello, World!'
}

// => Hello, World!

<!-- テキストエリア -->
<textarea v-model="message"></textarea>
<pre>{{ message }}</pre>

data: {
  // 初期値をセット
  message: 'Hello, World!'
}

// => Hello, World!

<!-- チェックボックス -->
<label><input type="checkbox" v-model="val"> {{ val }}</label>
<label><input type="checkbox" v-model="val" true-value="yes" false-value="no"> {{ val }}</label>

data: {
  val: true
}

// => true / false
// => yes / no

<label><input type="checkbox" v-model="val" value="A">A</label>
<label><input type="checkbox" v-model="val" value="B">B</label>
<label><input type="checkbox" v-model="val" value="C">C</label>
<p>{{ val }}</p>

data: {
  val: []
}

// => ["A", "C"]

<!-- ラジオボタン -->
<label><input type="radio" v-model="val" value="a">A</label>
<label><input type="radio" v-model="val" value="b">B</label>
<label><input type="radio" v-model="val" value="c">C</label>
<p>{{ val }}</p>

data: {
  val: ''
}

// => 'a'

<!-- セレクトボックス -->
<select v-model="val">
  <option disabled="disabled">選択して下さい</option>
  <option value="a">A</option>
  <option value="b">B</option>
  <option value="c">C</option>
</select>
<p>{{ val }}</p>

data: {
  val: ''
}

// => 'a'

<select v-model="val" multiple>
  <option value="a">A</option>
  <option value="b">B</option>
  <option value="c">C</option>
</select>
<p>{{ val }}</p>

data: {
  val: []
}

// => ["a"]

<!-- 画像ファイル -->
<!-- v-modelディレクティブを使用できなため、changeイベントをハンドルする -->
<input type="file" v-on:change="handleChange">
<div v-if="preview">
  <img v-bind:src="preview">
</div>

data: {
  preview: ''
},
methods: {
  handleChange: function(event) {
    var file = event.target.files[0]
    if (file && file.type.match(/^image\/(png|jpeg)$/)) {
      this.preview = window.URL.createObjectURL(file)
    }
  }
}

// => <img src="blob:null/ff38863e-ab28-4c4e-ae8c-25d7dbc7e343">

<!-- その他 -->
<input type="range" v-model.number="val"> {{ val }}

data: {
  val: 50
}
```

### フォーム修飾子

|修飾子|振る舞い|
|-----|-----|
|.lazy|changeイベントで同期するよう変更する|
|.number|値を数値に変更する|
|.trim|余分なスペースを削除する|

```
<!-- 修飾子 -->
<input type="text" v-model.number="price"> {{ price }}

data: {
  val: 100
}
```

### マウント要素外のイベント操作
windowやbodyにはv-onディレクティブが使用できないため、addEventLitenerメソッドを使用する。<br>
イベントはフックを使って解除する。

```
<div id="app">
  <!-- スクロールイベントの取得 -->
  <header v-bind:class="{ compact: scrollY > 200 }">
    200pxより下にスクロールしたら.compactを付与
  </header>
</div>

var app = new Vue({
  el: '#app',
  data: {
    scrollY: 0,
    timer: null
  },
  created: function () {
    // ハンドラを登録
    window.addEventListener('scroll', this.handleScroll)
  },
  beforeDestroy: function () {
    // ハンドラを解除
    window.removeEventListener('scroll', this.handleScroll)
  },
  methods: {
    handleScroll: function () {
      if (this.timer === null) {
        this.timer = setTimeout(function () {
          this.scrollY = window.scrollY
          clearTimeout(this.timer)
          this.timer = null
        }.bind(this), 200)
      }
    }
  }
})
```

#### スムーススクロールのサンプル

```
<script src="https://cdn.jsdelivr.net/npm/smooth-scroll@12.1.5"></script>
<div id="app">
  <div class="content">...</div>
  <div v-on:click="scrollTop">
    ページ上部へ移動
  </div>
</div>

var scroll = new SmoothScroll()
new Vue({
  el: '#app',
  methods: {
    scrollTop: function () {
      scroll.animateScroll(0)
    }
  }
})
```

## Chapter4

### 算出プロパティ
任意に処理を含めることのできるデータのこと。<br>
computedで定義した関数は、Vueインスタンスを初期化した際に、Object.definePropertyによってVueインスタンスのプロパティとして定義される。

```
<p>{{ width }} の半分は {{ halfWidth }}</p>

var app = new Vue({
  el: '#app',
  data: {
    width: 800
  },
  computed: {
    halfWidth: function() {
      return this.width / 2
    }
  }
})

// => <p>800 の半分は 400</p>
```

- 算出プロパティを組み合わせる

```
<p>X: {{ halfPoint.x }}</p>
<p>Y: {{ halfPoint.y }}</p>

var app = new Vue({
  el: '#app',
  data: {
    width: 800,
    height: 600
  },
  computed: {
    halfWidth: function() {
      return this.width / 2
    },
    halfHeight: function() {
      return this.height / 2
    },
    halfPoint: function() {
      return {
        x: this.halfWidth,
        y: this.halfHeight
      }
    }
  }
})

// =>
<p>X: 400</p>
<p>Y: 300</p>
```

### ゲッターとセッター
※ JavaScript Section 266

- ゲッター<br>
ある属性の値を取得するためのメソッド。
- セッター<br>
属性に値をセットするためのメソッド。

```
<input v-model.number="width"> {{ width }}
<input v-model.number="halfWidth"> {{ halfWidth }}

var app = new Vue({
  el: '#app',
  data: {
    width: 800
  },
  computed: {
    halfWidth: {
      // ゲッター
      get: function() { return this.width / 2 },
      // セッター
      set: function(val) { this.width = val * 2 }
    }
  }
})
```

### 算出プロパティのキャッシュ機能
算出プロパティはリアクティブな依存データに基づき結果をキャッシュする。

```
<!-- 算出プロパティ -->
<ol>
  <li>{{ computedData }}</li>
  <li>{{ computedData }}</li>
</ol>
<!-- メソッド -->
<ol>
  <li>{{ methodsData() }}</li>
  <li>{{ methodsData() }}</li>
</ol>

computed: {
  computedData: function() { return Math.random() }
},
methods: {
  methodsData: function() { return Math.random() }
}

// =>
<ol>
  <li>0.6101415668728649</li>
  <li>0.6101415668728649</li>
</ol>
// =>
<ol>
  <li>0.9289635049644003</li>
  <li>0.46988487693472347</li>
</ol>
```

- リストの絞込み機能<br>
キャッシュ機能により、算出プロパティはリアクティブデータに変更があるまで何度使用しても関数内の処理は一度しか実行されない。

```
<input v-model.number="budget"> 円以下に絞り込む
<input v-model.number="limit"> 件を表示
<p>{{ matched.length }} 件中 {{ limited.length }} 件を表示中</p>
<ul>
  <li v-for="item in limited" v-bind:key="limited.id">
    {{ item.name }} {{ item.price }} 円
  </li>
</ul>

var app = new Vue({
  el: '#app',
  data: {
    budget: 300,
    limit: 2,
    list: [
      { id: 1, name: 'りんご', price: 100 },
      { id: 2, name: 'バナナ', price: 200 },
      { id: 3, name: 'いちご', price: 400 },
      { id: 4, name: 'オレンジ', price: 300 },
      { id: 5, name: 'メロン', price: 500 }
    ]
  },
  computed: {
    matched: function() {
      return this.list.filter(function(el) {
        return el.price <= this.budget
      }, this)
    },
    limited: function() {
      return this.matched.slice(0, this.limit)
    }
  }
})
```

- ソート機能
sortは配列を直接操作するため、元のデータの順番を書き換えてしまう。<br>
シャローコピーしたり、Lodashなどのライブラリを使用する。

```
<script src="https://cdn.jsdelivr.net/npm/lodash@4.17.5/lodash.min.js"></script>

<input v-model.number="budget"> 円以下に絞り込む
<input v-model.number="limit"> 件を表示
<button v-on:click="order=!order">切り替え</button>
<p>{{ matched.length }} 件中 {{ limited.length }} 件を表示中</p>
<ul>
  <li v-for="item in limited" v-bind:key="limited.id">
    {{ item.name }} {{ item.price }} 円
  </li>
</ul>

var app = new Vue({
  el: '#app',
  data: {
    order: false,
    budget: 300,
    limit: 2,
    list: [
      { id: 1, name: 'りんご', price: 100 },
      { id: 2, name: 'バナナ', price: 200 },
      { id: 3, name: 'いちご', price: 400 },
      { id: 4, name: 'オレンジ', price: 300 },
      { id: 5, name: 'メロン', price: 500 }
    ]
  },
  computed: {
    matched: function() {
      return this.list.filter(function(el) {
        return el.price <= this.budget
      }, this)
    },
    sorted: function() {
      return _.orderBy(this.matched, 'price', this.order ? 'desc' : 'asc')
    },
    limited: function() {
      // return this.matched.slice(0, this.limit)
      return this.sorted.slice(0, this.limit)
    }
  }
})
```

### ウォッチャ
特定のデータ、または算出プロパティの状態を監視して、変化があったとき登録した処理を自動的に実行する(データの変化をトリガーにしたフック)<br>
<br>
コンポーネントのwatchオプションに、監視するデータの名前と変化したときに呼び出されるハンドラを定義する。

```
var app = new Vue({
  :<snip>
  watch: {
    監視するデータ: function(新しい値, 古い値) {
      // 比較関数に新しい値と古い値をセット
      // valueが変化したときに行いたい処理
    }
  }
})
```

- オプション

|プロパティ|値|振る舞い|
|-----|-----|-----|
|deep|Boolean|ネストされたオブジェクトも監視する|
|immediate|Boolean|初期読み込み時にも呼び出す|


```
watch: {
  list: {
    handler: function(newVal, oldVal) {
      // 比較関数にnewValとoldValをセット
      // listが変化したときに行いたい処理
    },
    deep: true,
    immediate: true
  }
}
```

- インスタンスメソッド this.$watch<br>
this.$watchを使用することで、各メソッド内でウォッチャを登録できる。

```
created: function() {
  this.$watch('value', function(newVal, oldVal) {
    // ...
  })
}
```

- インスタンスメソッド this.$watchの引数

```
[
  監視するデータ,
  ハンドラ,
  オプション
]

created: function() {
  this.$watch(
    'value',function(newVal, oldVal) {
    // ...
  }, {
    immediate: true
  })
}

// ウォッチャの解除
var unwatch = this.$watch('value', handler)
// valueの監視を解除
unwatch()

// 一度だけ動作するウォッチャ
new Vue({
  el: '#app',
  data: {
    edited: false,
    list: [
      { id: 1, name: 'りんご', price: 100 },
      { id: 2, name: 'ばなな', price: 200 },
    ]
  },
  created: function() {
    var unwatch = this.$watch('list', function () {
      // listが編集されたことを記録する
      this.edited = true
      // 監視を解除
      unwatch()
    }, {
      deep: true
    })
  }
})
```

- 実行頻度の制御<br>
フォームの入力など監視するが高頻度で変化する場合、setTimeoutやLodashなどのライブラリを使用して、パフォーマンスが下がらないようにする。

```
// debounce => Lodashのメソッド。実行から指定ミリ秒がすぎた場合に、コールバックを呼び出す。

<script src="https://cdn.jsdelivr.net/npm/lodash@4.17.5/lodash.min.js"></script>

<input type="text" v-model="value">

var app = new Vue({
  el: '#app',
  data: {
    value: '初期値'
  },
  watch: {
    value: _.debounce(function (newVal) {
        // コストの高い処理
        console.log(newVal)
      },
      // valueの変化が終わるのを待つ時間をミリ秒で指定
      500)
  }
})
```

- 複数の値を監視する

```
this.$watch(function() {
  return [this.width, this.height]
}, function() {
  // widthまたはheightが変化したら処理
})

// 算出プロパティを監視すると同じことができる
computed: {
  watchedTarget: function() {
    return [this.width, this.height]
  }
},
watch:
  watchedTarget: function() {
  // ...
}
```

- 監視対象がオブジェクト型だった場合

```
watch: {
  list: function(newVal, oldVal) {
    console.log(newVal.length, oldVal.length)
    // => 監視対象がオブジェクト型の場合、古い値は参照、保持されるので結果は同じ
  }
}

// 監視対象をコピー or ディープコピーする
this.$watch(function() {
  return Object.assign([], this.list)
}, function(newVal, oldVal) {
  console.log(newVal.length, oldVal.length)
})

// 監視対象にプロパティを含める
this.$watch(function() {
  return { value: this.list, length: this.list.length }
}, function(newVal, oldVal) {
  console.log(newVal.length, oldVal.length)
})
```

### フォームを監視してAPIからデータを取得

```
<script src="https://cdn.jsdelivr.net/npm/axios@0.17.1/dist/axios.min.js"></script>

<select v-model="current">
  <option v-for="topic in topics" v-bind:value="topic.value">
    {{ topic.name }}
  </option>
</select>
<div v-for="item in list">{{ item.full_name }}</div>

var app = new Vue({
  el: '#app',
  data: {
    list: [],
    current: '',
    topics: [
      { value: 'Vue', name: 'Vue.js' },
      { value: 'jQuery', name: 'jQuery' }
    ]
  },
  watch: {
    current: function(value) {
      axios.get('https://api.github.com/search/repositories', {
        params: { q: 'topic:' + value }
      }).then(function(response) {
        this.list = response.data.items
      }.bind(this))
    }
  }
})

// =>
<select>
  <option value="Vue">Vue.js</option>
  <option value="jQuery">jQuery</option>
</select>
```

### フィルタを使ったテキストの変換処理

```
// マスタッシュ
{{ 対象のデータ | フィルタの名前 }}

// v-bind
<div v-bind:id="対象のデータ | フィルタの名前"></div>
```

```
<div v-bind:id=" price | localNum"></div>

var app = new Vue({
  el: '#app',
  data: {
    price: 19800
  },
  filters: {
    localNum :function(value) {
      return value.toLocalString()
    }
  }
})
```

- グローバルへ登録<br>
グローバルメソッド Vue.filterを使ってすべてのコンポーネントに使用できるようにする。


```
Vue.filter('localNum', function(value) {
  return value.toLocalString()
})
```

- フィルタに引数を持たせる

```
{{ message | filter(foo, 100) }}

filters: {
  filter: function(messsage, foo, num) {
    console.log(message, foo, num)
  }
}
```

- 複数のフィルタをつなげる

```
{{ value | filter1 | filter2 }}

filters: {
  round: function(value) {
    return Math.round(val * 100) / 100
  },
  radian: function(value) {
    return value * Math.PI * 100
  }
}
```

### カスタムディレクティブ
v-bindのようなディレクティブを自作する機能。原則、データバインディングだけではDOMを操作できない場合に使用する。<br>
カスタムディレクティブは、メソッド名にv-プレフィックスをつける。

```
<div v-directive>example</div>
<!-- valueに変化があると呼び出される -->
<div v-directive="value">example</div>

<input type="text" v-focus>

var app = new Vue({
  el: '#app',
  directives: {
    focus: {
      inserted: function(el) {
        el.focus()
      }
    }
  }
})
```

- グローバルへ登録<br>
グローバルメソッド Vue.directiveを使ってすべてのコンポーネントに使用できるようにする。


```
Vue.directive('focus', {
  inserted: function(el) {
    el.focus()
  }
})
```

- 使用可能なフック

|メソッド|振る舞い|
|-----|-----|
|bind|はじめて要素と紐づいたとき|
|inserted|紐づいた要素が親Nodeに挿入されたとき|
|update|紐づいた要素を包含しているコンポーネントのVNodeが更新されたとき|
|componentUpdated|包含しているコンポーネントと子コンポーネントのVNodeが更新されたとき|
|unbind|紐づいた要素が削除されたとき|


```
Vue.directive('example', {
  bind: function(el, binding) {
    console.log('v-exapmle bind')
  },
  inserted: function(el, binding) {
    console.log('v-exmaple inserted')
  },
  update: function(el, binding) {
    console.log('v-exmaple update')
  },
  componentUpdated: function(el, binding) {
    console.log('v-exmaple componentUpdated')
  },
  unbind: function(el, binding) {
    console.log('v-exmaple unbind')
  }
})
```

- フックの引数

|引数|内容|
|-----|-----|
|el|ディレクティブが付与されている要素|
|binding|バインドされた値、引数、修飾子のオブジェクト|
|vnode|要素に対応するVNode|
|oldVnode|更新前のVNode|

```
// フックの関数
Vue.directive('example', function(el, binding, vonode, oldVnode) {
  // 第二引数に関数を渡すと、bindとupdateにフックし、同じ処理を呼び出す。
})
```

#### 動画の再生を操作するサンプル

```
<button v-on:click="video1=true">再生</button>
<button v-on:click="video1=false">停止</button>
<video src="movie1.mp4" v-video="video1"></video>
<button v-on:click="video2=true">再生</button>
<button v-on:click="video2=false">停止</button>
<video src="movie2.mp4" v-video="video2"></video>

var app = new Vue({
  el: '#app',
  data: {
    video1: true,
    video2: false
  },
  directives: {
    video(el, binding) {
      binding.value ? el.play() : el.pause()
    }
  }
})
// => updateフックで、video1プロパティ(el)の値(binding)が変化すると、video2プロパティ(el)の値(binding)も変化する
```

- 前の状態と比較して処理を行う<br>
オブジェクトbindingが含むプロパティ

|プロパティ|内容|
|-----|-----|
|arg|引数|
|modifilers|修飾子のオブジェクト|
|value|新しい値|
|oldValue|古い値|

```
var app = new Vue({
  el: '#app',
  data: {
    video1: true,
    video2: false
  },
  directives: {
    video(el, binding) {
      if (binding.value !== binding.oldValue) {
        binding.value ? el.play() : el.pause()
      }
    }
  }
})
// => updateフックで、video1プロパティ(el)の値(binding)が変化すると、video2プロパティ(el)の値(binding)も変化する
// => 関係のない呼び出しをスキップする
```

- nextTick
データの更新前/更新後のDOMへのアクセスを待ち受ける仕組みのこと。<br>
非同期通信時に発生する誤差に対応する。

```
this.$nextTick(function()) {
  // DOMの更新後に実行する処理
}
```

#### 更新後のDOMへアクセスするサンプル

```
<button v-on:click="list.push(list.length+1)">追加</button>
<ul ref="list">
  <li v-for="item in list">{{ item }}</li>
</ul>

var app = new Vue({
  el: '#app',
  data: {
    list: []
  },
  watch: {
    list: function() {
      // 更新後のul要素の高さを取得できない
      console.log('通常:', this.$refs.list.offsetHeight)
      // nextTickで更新後のul要素の高さを取得する
      this.$nextTick(function() {
        console.log('nextTick:', this.$refs.list.offsetHeight)
      })
    }
  }
})

// => 通常: 0
// => nextTick: 24
// => 通常: 24
// => nextTick: 48
// => ...
```

# Chapter 5

## コンポーネント
機能ごとにJavaScriptとテンプレートを1つのセットにして、他の機能とは分離して開発できるようにする仕組みのこと。

### コンポーネントの定義

```
// コンポーネントの定義
<div id="app">
    <!-- DOMとして認識させるには、テンプレート名はケバブケースで記述する制約 -->
  <my-component></my-component>
</div>

// コンポーネントを使用
Vue.component('my-component', {
  template: '<p>MyComponet</p>'
})

// => 実行結果
<div id="app">
  <my-component></my-component>
</div>

// => インスタンスを生成する

// コンポーネントの定義
<div id="app">
    <!-- DOMとして認識させるには、テンプレート名はケバブケースで記述する制約 -->
  <my-component></my-component>
</div>

// コンポーネントを使用
var myComponent = {
  template: '<p>MyComponet</p>'
}
// コンポーネントはインスタンスが作成される前に定義する
var app = new Vue({
  el: '#app',
  components: {
    'my-component': myComponet
  }
})

// => 実行結果
<div id="app">
  <p>MyComponent</p>
</div>
```

### コンポーネントのオプション
データやメソッドも定義できる。<br>
<strong>component オプションはコンポーネントをローカルに登録し、そのスコープでのみ使う場合付与する。</strong>

```
var myComponent = Vue.component('my-component', {
  template: '<p>{{ message }}</p>',
  data: function() {
    return {
      message: 'Hello, World'
    }
  },
  methods: {
    // 処理を記述する
  }
})
var app = new Vue({
  el: '#app',
  components: {
    'my-component': myComponent
  }
})

// => 実行結果
<div id="app">
  <p>Hello, World</p>
</div>
```

- data はオブジェクトを返す関数にする

```
:<snip>
data: function() {
  return {
    message: 'Hello, World'
  }
},
:<snip>
```

- ルートは単数にする

```
template: <span>Hello</span><span>, World</span>
// => NG
template: <div><span>Hello</span><span>, World</span></div>
// => OK
```

- コンポーネントは別々のインスタンス

```
<div id="app">
  <my-component></my-component>
  // ↑と↓は別々のインスタンスでそれぞれスコープをもつ
  <my-component></my-component>
</div>
```

### コンポーネント間の通信

1. 親子間の通信(props + カスタムイベント)
1. 非親子間の通信(イベントバス)
1. 状態管理(VueX)

#### 親子間の通信(props + カスタムイベント)

```
// 親 => my-component
// 子 => component-child

Vue.component('my-component', {
  template: '<p><component-child>MyComponet</component-child></p>'
})
```

- 親から子へデータを渡す => 属性で渡し、propsで受け取る

```
// 親がルートインスタンスの場合
<div id="app">
  <component-child val="これは子A"></component-child>
  <component-child val="これは子B"></component-child>
</div>

var componentChild = Vue.component('component-child', {
  template: '<p>{{ val }}</p>',
  props: ['val']
})
var app = new Vue({
  el: '#app'
})

// => 実行結果
<div id="app">
  <p>これは子A</p>
  <p>これは子B</p>
</div>
```

- props down 親から子へリアクティブデータを渡す

```
// 親がルートインスタンスの場合
<div id="app">
  <component-child v-bind:val="valA"></component-child>
  <component-child v-bind:val="valB"></component-child>
</div>

var componentChild = Vue.component('component-child', {
  template: '<p>{{ val }}</p>',
  props: ['val']
})
var app = new Vue({
  el: '#app',
  data: {
    valA: 'これは子A',
    valB: 'これは子B'
  }
})

// => 実行結果
<div id="app">
  <p>これは子A</p>
  <p>これは子B</p>
</div>
```

- 子コンポーネントに属性を渡す

```
<component-child id="parent" class="parent"></component-child>

var componentChild = Vue.component('component-child', {
  template: '<p id="child" class="child">componentChild</p>',
})
var app = new Vue({
  el: '#app'
})

// => 実行結果
<p id="parent" class="child parent">componentChild</p>
// => 単一の値しか指定できないもの(id など)は、上書き
// => 複数の値が設定できるものはマージ

// => 今更だが、わざわざ変数に入れる必要性はない
// => component オプションはコンポーネントをローカルに登録し、そのスコープでのみ使う場合付与する
Vue.component('component-child', {
  template: '<p id="child" class="child">componentChild</p>',
})
new Vue({
  el: '#app'
})
```

- 子コンポーネントでfor文を使う

```
<ul>
  <component-child v-for="item in list"
                   v-bind:key="item.id"
                   v-bind:name="item.name"
                   v-bind:hp="item.hp"
  >
  </component-child>
</ul>

Vue.component('component-child', {
  template: '<li>{{ name }} HP.{{ hp }}</li>',
  props: [
    'name',
    'hp'
  ]
})
new Vue({
  el: '#app',
  data: {
    list: [
      { id: 1, name: 'スライム', hp: 100},
      { id: 2, name: 'ゴブリン', hp: 200},
      { id: 3, name: 'ドラゴン', hp: 500}
    ]
  }
})

// => 実行結果
<ul>
  <li>スライム HP.100</li>
  <li>ゴブリン HP.200</li>
  <li>ドラゴン HP.500</li>
</ul>
```

- 子コンポーネントはpropsで受け取ったデータを書き換えてはいけない

```
Vue.component('component-child', {
  template: '<li>{{ name }} HP.{{ hp }}\
            <button v-on:click="doAttack">攻撃する</button></li>',
  props: [
    'name',
    'hp'
  ],
  methods: {
    doAttack: function() {
      this.hp -= 10
    }
  }
})
new Vue({
  el: '#app',
  data: {
    list: [
      { id: 1, name: 'スライム', hp: 100},
      { id: 2, name: 'ゴブリン', hp: 200},
      { id: 3, name: 'ドラゴン', hp: 500}
    ]
  }
})
// => [Vue warn]: ~
```

- propsの受け取りデータ型を指定する

```
Vue.component('component-child', {
  props: {
    val: String
  }
})

// 1 + '1' => 11 :String として出力されてしまう
// propsの許容範囲はしっかり定義しておく
```

#### propsの受け取りデータ型のサンプル

```
Vue.component('example', {
  props: {
    // Nullはどんなデータ型も受け取る
    propA: Number,
    propB: [String, Number],
    propC: {
      type: String,
      required: true
    },
    // デフォルト値
    propD: {
      type: Number,
      dafault: 100
    },
    propE: {
      type: Object,
      default: function() {
        return { message: 'hello' }
      }
    },
    // カスタムでバリデーションを設定
    propF: {
      validator: function(value) {
        return value > 10
      }
    }
  }
})
```

- propsに記述するとコードの見通しが悪くなるので、属性に記述した方がスマート

```
<component-child v-bind="object"></component-child>
```

- event up 子から親へデータを渡す => カスタムメソッドと$emitで渡し、onで受け取る


```
<component-child v-on:childs-event="parentsMethod"></component-child>

Vue.component('component-child', {
  template: '<button v-on:click="handleClick">イベント発火</button>',
  methods: {
    handleClick: function() {
      this.$emit('childs-event')
    }
  }
})

new Vue({
  el: '#app',
  methods: {
    parentsMethod: function() {
      alert('イベントをキャッチ')
    }
  }
})

// => 実行結果
<div id="app">
  <button>イベント発火</button>
</div>
```

```
<component-child v-for="item in list"
                 v-bind="item"
                 v-bind:key="item.id"
                 v-on:attack="handleAttack"
>
</component-child>

// 子テンプレート
Vue.component('component-child',{
  template: '<li>{{ name }} HP.{{ hp }}\
            <button v-on:click="doAttack">攻撃する</button></li>',
  // props の受け取りデータ型を指定
  props: {
    id: Number,
    name: String,
    hp: Number
  },
  methods: {
    doAttack: function() {
      this.$emit('attack', this.id)
    }
  }
})

// 親テンプレート
new Vue({
  el: '#app',
  // データは親に持たせておく
  data: {
    list: [
      { id: 1, name: 'スライム', hp: 100},
      { id: 2, name: 'ゴブリン', hp: 200},
      { id: 3, name: 'ドラゴン', hp: 500}
    ]
  },
  methods: {
    handleAttack: function(id) {
      // 引数のIDから要素を検索
      var item = this.list.find(function(el) {
        return el.id === id
      })
      if (item.hp !== undefined && item.hp > 0) {
        item.hp -= 10
      }
    }
  }
})
```

- カスタムタグのイベントハンドリング

```
// 通常では発火しない
<my-icon v-on:click="handleClick"></my-icon>

// .native 修飾子を使用し、発火させる
<my-icon v-on:click.native="handleClick"></my-icon>
```

#### 非親子間の通信(イベントバス)
Vueインスタンスをイベントバスとして使用しデータを渡す。<br>
Vuexを導入した方がスマート。

```
// インスタンスの初期化時にデータがセットされるよう算出プロパティを使う

var bus = new Vue({
  data: {
    count: 0
  }
})

Vue.component('component-other', {
  template: '<p>bus: {{ bus.count }}</p>',
  computed: {
    // busのデータを算出プロパティに使用する
    bus: function() {
      return bus.$data
    }
  },
  created: function() {
    // $on で子は自分自身のイベントを発火する
    bus.$on('event-bus', function() {
      this.count++
    })
  }
})
```

- 親から直接子コンポーネントを参照する

```
<component-child ref="child"></component-child>

// 親メソッドで発火させる
this.$refs.child.$emit('open')
```

- コンポーネントの属性のスコープ<br>
コンポーネントの属性の値は、親のスコープになる

```
// 親のデータを受け取る
<component-child v-on:childs-event="parentMethods"></component-child>
// => parentMethods は親のスコープ

// 親と子両方のデータを受け取る
// $event変数を使う
<component-child v-on:childs-event="parentMethods($event, parentsData)"></component-child>
// => $event で子コンポーネントの引数を使用する

new Vue({
  data: {
    parentsData: ''
  },
  methods: {
    parentsMethod: function(childsArg, parentsArg) {
      // childsArg => 子のデータ(= $event)
      // parentsArg => 親のデータ(= parentsData)
    }
  }
})

// $event変数は第一引数しか持たないので、複数の値を引き渡す場合、オブジェクトにする
this.$emit('childs-event', { id:1 name: 'Name' })
```

#### 状態管理(VueX)
:<snip>

### スロット
親コンポーネント側から子コンポーネントのテンプレートの一部を差し込む機能。

#### デフォルトスロット

```
// 親テンプレート
<component-child>
  実はここがスロットコンテンツ
</component-child>

// 子テンプレート
<div class="component-child">
  ここにスロットを埋め込む => <slot></slot>
</div>

// => 実行結果
<div class="component-child">
  ここにスロットを埋め込む => 実はここがスロットコンテンツ
</div>
```

#### 名前付きスロット
親側でコンテンツを囲むタグに slot="name"属性をつける。<br>
子側でslotタグに slot="name"属性をつける。<br>
スコープは親コンポーネント。

```
// header タグの中身が変わる
<component-child>
  <header slot="header">
    Hello, Vue.js!
  </header>
  Vue.jsはJavaScriptのフレームワークです。
</component-child>

Vue.component('component-child', {
  template: '<section class="component-child">\
            <slot name="header">default title</slot>\
            <div class="content">\
            <slot>default content</slot>\
            </div>\
            <footer name="footer">\
            </footer>\
            </section>'
})
new Vue({
  el: '#app',
})

// => 実行結果
<section class="component-child">
  <header>
    Hello, Vue.js!
  </header>
  <div class="content">
    Vue.jsはJavaScriptのフレームワークです。
  </div>
  <footer name="footer"></footer>
</section>
```

#### スコープ付きスロット
属性slot-scopeで子側のスコープにアクセスする。<br>
親側でコンテンツを囲むタグに slot-scope="name"属性をつける。<br>
子側でslotタグに 親側に渡したいデータの属性をつける。<br>
※スコープ付きslotを使うとコンポーネント構造が細かくなるので、一旦機能を把握するところで留めておく。

```
// slot-scope名は重複しないようにする
<component-child>
  <p slot-scope="props">
    スロットから受け取ったtext -> {{ props.text }}
  </p>
</component-child>

Vue.component('component-child', {
  template: '<div class="props-child">\
            <slot text="Hello, Vue.js"></slot>\
            </div>'
})
new Vue({
  el: '#app'
})

// => 出力結果
<div class="props-child">
  <p>
    スロットから受け取ったtext -> Hello, Vue.js
  </p>
</div>
// => スロットから受け取ったtext => Hello, Vue.js
```
```
<component-child>
  <li slot-scope="props">{{ props.item_name }}</li>
</component-child>

Vue.component('component-child', {
  template: '<ul class="component-child">\
            <slot v-for="item in list" v-bind:item_name="item.name"></slot>\
            </ul>',
  // dataを関数にして、listをreturnする
  data: function() {
    return {
      list: [
        { id: 1, name: 'スライム', hp: 100},
        { id: 2, name: 'ゴブリン', hp: 200},
        { id: 3, name: 'ドラゴン', hp: 500}
      ]
    }
  }
})
new Vue({
  el: '#app'
})

// => 出力結果
<ul class="component-child">
  <li>スライム</li>
  <li>ゴブリン</li>
  <li>ドラゴン</li>
</ul>
```

### 双方向のデータバインディング
原則コンポーネントは、props down, event upのどちらかのデータフローであるが、この二つを自動化することができる。

```
// v-modelを使うと、dataを動的に書き換えることができる
<my-calender v-model="date"></my-calender>
// ↑は↓と同義
<my-calender v-on:input="date = $event" v-bind:value="date"></my-calender>

this.$emit('input', '2020-01-01')
// my-calenderコンポーネントは、$emitを使ってinputイベントを発火させる
// 自動でdateプロパティに子コンポーネントの引数($event)を代入する
```

#### .sync修飾子 を使用し、一つのコンポーネントに複数の値を同期
v-model, .sync修飾子の値は、暗黙的に親コンポーネント側が更新される。

```
<my-component v-bind:name.sync="name"
              v-bind:hp.sync="hp"
>
</my-component>

Vue.component('my-component', {
  template: '<div class="my-component">\
            <p>名前.{{ name }} HP.{{ hp }}</p>\
            <p>名前 <input v-model="localName"></p>\
            <p>HP <input size="5" v-model.number="localHp"></p>\
            </div>',
  props: {
    name: String,
    hp: Number
  },
  computed: {
    // 算出プロパティのゲッター(取得)とセッター(代入)を使う
    localName: {
      // 値を取得
      get: function() {
        return this.name
      },
      // 値を代入
      set: function(val) {
        this.$emit('update:name', val)
      }
    },
    localHp: {
      // 値を取得
      get: function() {
        return this.hp
      },
      // 値を代入
      set: function(val) {
        this.$emit('update:hp', val)
      }
    }
  }
})

new Vue({
  el: '#app',
  data: {
    name: 'スライム',
    hp: 100
  }
})

// => 出力結果
<div class="my-component">
  <p>名前.スライム HP.100</p>
  <p>名前 <input></p>
  <p>HP <input size="5"></p>
</div>
// => 値が空になるとエラーを吐き出す
// => Invalid prop: type check failed for prop ...
```

### テンプレートの定義方法

- template オプション<br>
tempalteオプションに直接記述する。

```
Vue.component({
  template: '<p>テンプレート</p>'
})

// EC2015環境下では、テンプレートリテラルでも良い
Vue.component({
  template: `<p>テンプレート</p>`
})
```

- inline-template<br>
カスタムタグ inline-template内の記述をHTMLテンプレートとして使用する。非推奨なので原則使用しない。

```
<my-component inline-template>
  <p>テンプレート</p>
</my-component>
```

- text/x-templateタイプ + セレクタ<br>
MINEタイプ text/x-templateタイプを使うと、DOMがブラウザに認識されない。非推奨なので原則使用しない。

```
<script type="text/x-template" id="component-child">
  <p>テンプレート</p>
</script>

Vue.component('my-component', {
  template: '#component-child'
})
```

#### 単一ファイルコンポーネント
HTML + CSS + JavaScript をセットにし、.vue 拡張子をファイルに記述する。<br>
プリコンパイルが必要となるため、ビルド環境が必要。

#### 描画関数
プリコンパイルの工程をスキップし、JavaScriptを使って動的に仮想DOMを構築する。上級者向けなので、必要となったら知識を仕入れる。

```
Vue.component('my-component', {
  render: function(createElement) {
    return createElement('element', { options })
  }
})
```

#### テンプレートがDOMと認識されるケース
以下のルールの他、コンポーネントの名前は、ケバブケースで記述し、有効なカスタムタグを使う必要がる。

- マウントした要素の内側に直接記述したテンプレート
- inline-template で記述したテンプレート
- テンプレートの中で記述した、slot のコンテンツ

#### テンプレートがDOMと認識されないケース
以下のルール下では、HTMLの制約を受けない

- 単一コンポーネントファイル(.vueファイル)
- template オプションに直接記述したテンプレート
- text/x-template で記述したテンプレート

#### 関数型コンポーネント
状態とインスタンスを持たないので、ライフサイクルや監視が行われないが、propsは使うことができる。<br>
関数型コンポーネントはVue DevToolsに表示されない。

```
Vue.component('functional-component', {
  // function オプションを付ける
  functional: true,
  render: function(createElement, context) {
    return createElement('div', context.props.message)
  },
  props: {
    message: String
  }
})
```

#### 動的コンポーネント
属性<strong> is </strong>を使いコンポーネントを指定すると、複数のコンポーネントを切り替えられる。

```
// コンポーネントを定義
Vue.component('my-component-a', {
  template: '<div class="my-component-a">component A</div>'
})
Vue.component('my-component-b', {
  template: '<div class="my-component-b">component B</div>'
})

// 親コンポーネント
<button v-on:click="current^=1">toggle</button>
<div v-bind:is="component"></div>
new Vue({
  el: '#app',
  data: {
    componentTypes: ['my-component-a', 'my-component-b'],
    current: 0
  },
  computed: {
    component: function() {
      return this.componentTypes[this.current]
    }
  }
})
```
