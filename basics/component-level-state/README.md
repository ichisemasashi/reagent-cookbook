# コンポーネントレベルの状態

アプリケーションの状態のほとんど/すべてを、トップレベルの試薬アトム(しばしば `app-state` や `app-db` と呼ばれる)に保持することが推奨されます。しかし、時には、コンポーネントレベルのステート（例えば、ボタンがトグルされているかどうか）を持つことがより意味を持つことがあります。

コンポーネントレベルのステートを作成するために、[Form-2](https://github.com/Day8/re-frame/wiki/Creating-Reagent-Components#form-2--a-function-returning-a-function)の試薬コンポーネントを作成してみます。 言い換えれば、hiccupを返す関数を作るということです。


```clojure
;; Do this
(defn foo []  ;; A function
  (let [component-state (reagent/atom {:count 0})] ;; <-- not included in render function
    (fn []  ;; That returns a function  <-- render function is from here down
      [:div ;; That returns hiccup
       [:p "Current count is: " (get @component-state :count)]
       [:button {:on-click #(swap! component-state update-in [:count] inc)} "Increment"]])))
```

外側の `let` ブロック内の `component-state` の宣言は、*render* 関数には **含まれません** 。 これは重要なことです。これは、`component-state`が一度しか作成されず、レンダリングフェーズごとに再作成されないことを意味します。

内側の関数を含めないのは、よくある間違いです。

```clojure
;; Don't do this
(defn foo-mistake []
  (let [component-state (reagent/atom {:count 0})]
    [:div
     [:p "Current count is: " (get @component-state :count)]
     [:button {:on-click #(swap! component-state update-in [:count] inc)} "Increment"]]))
```

`foo-mistake` で増加ボタンをクリックしても、カウンタが増加することはありません。これは、試薬のアトムである `component-state` が再レンダリングのたびに再作成されるためです (そのため、`:count` が正常に増分されることはありません)。 コンポーネントが実際にクリックで再レンダリングされていることを確認するために、`console.log`を追加してみましょう。

```clojure
;; Don't do this
(defn foo-mistake2 []
  (let [component-state (reagent/atom {:count 0})]
    [:div
     [:p "Current count is: " (get @component-state :count)] ;; <-- This deref is causing the re-render
     (.log js/console (str "Foo Mistake 2 is being rendered"))
     [:button {:on-click #(swap! component-state update-in [:count] inc)} "Increment"]]))
```

確認しました。 ボタンをクリックすると、コンソールにメッセージが表示されます。

気づいたかもしれませんが、`foo` では `(get @component-state :count)` をインラインで行っていました。 これを `let` ブロックに取り出しても何の問題もありません......ただ、内部の関数の中であることを確認してください。

```clojure
;; Do this
(defn foo-inner-let []
  (let [component-state (reagent/atom {:count 0})]
    (fn [] ;; <-- `render` is from here down
      (let [count (get @component-state :count)] ;; let block is inside `render`
        [:div
         [:p "Current count is: " count]
         [:button {:on-click #(swap! component-state update-in [:count] inc)} "Increment"]]))))
```

---

プロジェクトを作成して試してみましょう。

```
$ lein new rc component-level-state
```

`src/cljs/component_level_state/core.cljs` に移動して、以下のようにします。

```clojure
(ns component-level-state.core
    (:require [reagent.core :as reagent]))

;; Do this
(defn foo []  ;; A function
  (let [component-state (reagent/atom {:count 0})] ;; <-- not included in `render`
    (fn []  ;; That returns a function  <-- `render` is from here down
      [:div ;; That returns hiccup
       [:p "Current count is: " (get @component-state :count)]
       [:button {:on-click #(swap! component-state update-in [:count] inc)} "Increment"]])))

;; Don't do this
(defn foo-mistake []
  (let [component-state (reagent/atom {:count 0})]
    [:div
     [:p "Current count is: " (get @component-state :count)]
     [:button {:on-click #(swap! component-state update-in [:count] inc)} "Increment"]]))

;; Don't do this
(defn foo-mistake2 []
  (let [component-state (reagent/atom {:count 0})]
    [:div
     [:p "Current count is: " (get @component-state :count)] ;; <-- This deref is causing the re-render
     (.log js/console (str "Foo Mistake 2 is being rendered")) ;; <- will print this on-click
     [:button {:on-click #(swap! component-state update-in [:count] inc)} "Increment"]]))

;; Do this
(defn foo-inner-let []
  (let [component-state (reagent/atom {:count 0})]
    (fn [] ;; <-- `render` is from here down
      (let [count (get @component-state :count)] ;; let block is inside `render`
        [:div
         [:p "Current count is: " count]
         [:button {:on-click #(swap! component-state update-in [:count] inc)} "Increment"]]))))

(defn home []
  [:div
   [:h1 "Foo"]
   [foo]
   [:h1 "Foo Mistake"]
   [foo-mistake]
   [:h1 "Foo Mistake 2"]
   [foo-mistake2]
   [:h1 "Foo Inner Let"]
   [foo-inner-let]
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
