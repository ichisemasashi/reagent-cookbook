# Creating a Basic Reagent Component

まず最初に、コンポーネントとは何か？React.jsの[documentation](https://facebook.github.io/react/docs/component-specs.html)を見てみましょう。

```
`React.createClass()`を呼び出してコンポーネントクラスを作成する際には、*render*メソッドを含むspecificationオブジェクトを提供する必要があり、オプションでここで説明する他のライフサイクルメソッドを含むことができます。
```

さて，コンポーネントは，`React.createClass()`メソッドを使って作成されます． 最低でも、render関数が必要です。 [Reagent](https://github.com/reagent-project/reagent/)には、`React.createClass()`メソッドのラッパーとして、`create-class`というものがあります。 create-class`関数は、ライフサイクルメソッド(`:reagent-render`, `:component-will-mount`, `:component-did-mount`など)のマップを取ります。[こちら](https://github.com/reagent-project/reagent/blob/e53a5c2b1357c0560f0c4c15b28f00d09e27237b/src/reagent/core.cljs#L110)を参照してください。 *注：（React.jsでは「render」、Reagentでは「:reagent-render」と呼んでいます）*。

つまり、素のコンポーネントを作りたい場合は、以下のようになります。

```clojure
;; Form-3 Component
(defn foo []
  (reagent/create-class {:reagent-render (fn [] [:div "Hello, world!"])}))
```

しかし、`:reagent-render`というライフサイクルメソッドだけを定義したコンポーネントを作ることは、非常によくあることです。Reagentはこのためのショートハンド、つまりhiccupを返す関数を提供しています。

```clojure
;; Form-1 Component
(defn bar []
  [:div "Hello, world!"])
```

通常、関数を呼び出すには、`( )`で囲みます。しかし，htmlを生成する試薬コンポーネントは，代わりに `[ ]` で包んで呼び出します．呼び出されると，`foo` と `bar` の両方が，同じ render メソッドを定義した `React.createClass()` を呼び出します．言い換えれば、`foo` と `bar` は実質的に同じ試薬コンポーネントであり、呼び出されると同じ html を生成することになります。

しかし、待ってください、ただの `render` ライフサイクルメソッドで試薬コンポーネントを作成する別の方法があります - hiccup を返す関数です。

```clojure
;; Form-2 Component
(defn baz []
  (fn []
    [:div "Hello, world!"]))
```

結局、`foo`、`bar`、`baz`はすべて同じ試薬コンポーネントを生成することになります! 詳しくは、[試薬コンポーネントの作成](https://github.com/ichisemasashi/reagent/blob/master/doc/CreatingReagentComponents.md)をご覧ください。

---

プロジェクトを作成して試してみましょう。

```
$ lein new rc basic-component
```

`src/cljs/basic_component/core.cljs`に移動して、以下のようにします。

```clojure
(ns basic-component.core
  (:require [reagent.core :as reagent]
            [reagent.dom :as rdom]))

;; Form-3 Component
(defn foo []
  (reagent/create-class {:reagent-render (fn [] [:div "Hello, world!"])}))

;; Form-1 Component
(defn bar []
  [:div "Hello, world!"])

;; Form-2 Component
(defn baz []
  (fn []
    [:div "Hello, world!"]))

(defn home []
  [:div
   [foo]
   [bar]
   [baz]
   ])

(defn ^:export main []
  (rdom/render [home]
               (.getElementById js/document "app")))
```

cljsファイルをコンパイルします。

```
$ lein clean
$ lein cljsbuild once prod
```

`resources/public/index.html`を開きます。
