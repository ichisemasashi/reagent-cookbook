# Reagent Cursors

Reagentでは、アプリケーションのすべての状態を含む単一の試薬アトム(しばしば `app-state` または `app-db` と呼ばれる)を作成するのが一般的なパターンです。 [Cursors](https://github.com/reagent-project/reagent/blob/e53a5c2b1357c0560f0c4c15b28f00d09e27237b/src/reagent/core.cljs#L248)は、`app-state`のような、より大きな試薬アトムの中のポインターを得るための効果的な方法です。

まずは、試薬アトムを作ってみましょう。

```clojure
(def app-state (reagent/atom {:foo {:bar "Hello, world!"
                                    :baz {:quux "Woot"}}}))
```

`app-state`の中を見るために、アトムを参照して文字列に変換する試薬コンポーネントを作ることができます。

```clojure
(defn inside-app-state []
  [:div (str "Inside app-state: " @app-state)])

;; Inside app-state: {:foo {:bar "Hello, world!", :baz {:quux "Woot"}}}
```

次に、カーソルと中を見るための試薬部品を作成してみましょう。

```clojure
(def foo-cursor (reagent/cursor app-state [:foo]))

(defn inside-foo-cursor []
  [:div (str "Inside foo-cursor: " @foo-cursor)])

;; Inside foo-cursor: {:bar "Hello, world!", :baz {:quux "Woot"}}
```

関数 `reagent/cursor` は，試薬アトムと，その試薬アトムの中のパスを受け取ります． ここで指定したパスは `[:foo]` です。これはネストしたマップ構造をトラバースするのに便利な方法なのです。 もう少し理解を深めるために、いくつかのカーソルを作ってみましょう。

```clojure
(def foobar-cursor (reagent/cursor app-state [:foo :bar]))

(defn inside-foobar-cursor []
  [:div (str "Inside foobar-cursor: " @foobar-cursor)])

;; Inside foobar-cursor: Hello, world!


(def foobaz-cursor (reagent/cursor app-state [:foo :baz]))

(defn inside-foobaz-cursor []
  [:div (str "Inside foobaz-cursor: " @foobaz-cursor)])

;; Inside foobaz-cursor: {:quux "Woot"}


(def foobazquux-cursor (reagent/cursor app-state [:foo :baz :quux]))

(defn inside-foobazquux-cursor []
  [:div (str "Inside foobazquux-cursor: " @foobazquux-cursor)])

;; Inside foobazquux-cursor: Woot
```

foobazquux-cursor を見てみましょう。パスは `[:foo :baz :quux]` で、"Woot" を返すことがわかります。 `app-state`を振り返ってみると、このパスがどのように機能しているかがわかると思います。

---

プロジェクトを作成して試してみましょう。

```
$ lein new rc cursors
```

`src/cljs/cursors/core.cljs` に移動して、以下のようにします。

```clojure
(ns cursors.core
    (:require [reagent.core :as reagent]))

(def app-state (reagent/atom {:foo {:bar "Hello, world!"
                                    :baz {:quux "Woot"}}}))

(defn inside-app-state []
  [:div (str "Inside app-state: " @app-state)])

(def foo-cursor (reagent/cursor app-state [:foo]))

(defn inside-foo-cursor []
  [:div (str "Inside foo-cursor: " @foo-cursor)])

(def foobar-cursor (reagent/cursor app-state [:foo :bar]))

(defn inside-foobar-cursor []
  [:div (str "Inside foobar-cursor: " @foobar-cursor)])

(def foobaz-cursor (reagent/cursor app-state [:foo :baz]))

(defn inside-foobaz-cursor []
  [:div (str "Inside foobaz-cursor: " @foobaz-cursor)])

(def foobazquux-cursor (reagent/cursor app-state [:foo :baz :quux]))

(defn inside-foobazquux-cursor []
  [:div (str "Inside foobazquux-cursor: " @foobazquux-cursor)])

(defn home []
  [:div
   [inside-app-state]
   [inside-foo-cursor]
   [inside-foobar-cursor]
   [inside-foobaz-cursor]
   [inside-foobazquux-cursor]
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
