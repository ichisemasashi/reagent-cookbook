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

Normally, you'd call a function by wrapping it in `( )`. However, reagent components, which produce html, are called by wrapping it in `[ ]` instead. When called, both `foo` and `bar` will invoke `React.createClass()` with the same render method defined. In other words, both `foo` and `bar` are effectively the same reagent component, and, when called, will produce the same html.

But wait, there is yet another way to create a reagent component with just a `render` lifecycle method - a function returning a function that returns hiccup.

```clojure
;; Form-2 Component
(defn baz []
  (fn []
    [:div "Hello, world!"]))
```

In the end, `foo` `bar` and `baz` all produce the same reagent component! For more detail, please read [creating reagent components](https://github.com/ichisemasashi/reagent/blob/master/doc/CreatingReagentComponents.md).

---

Let's create a project and try this out.

```
$ lein new rc basic-component
```

Navigate to `src/cljs/basic_component/core.cljs` and make it look like this.

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

Compile cljs files.

```
$ lein clean
$ lein cljsbuild once prod
```

Open `resources/public/index.html`.
