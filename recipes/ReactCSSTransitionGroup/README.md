#  問題

あなたの[reagent](https://github.com/reagent-project/reagent)ウェブアプリケーションで[ReactCSSTransitionGroup](https://facebook.github.io/react/docs/animation.html)を使用したい。

(注: このレシピは、この試薬[issue](https://github.com/reagent-project/reagent/issues/55)の @jcreedcmu の gist に基づいています。)

# 解決策

この例では、アイテムの作成と削除の際にアニメーションで遷移するアイテムのリストを作成することにします。

*ステップ*

1. 新規プロジェクトの作成
2. `project.clj`のdependecies vectorにreact-with-addonsを追加します。
3. `src/cljs/animation/core.cljs` に移動して、reactのCSSTransitionGroupアドオンをreagentに適応させます。
4. トランジションのためのスタイルを作成します。
5. 初期のapp-stateを作成する
6. `add-item`関数の作成
7. `delete-item` 関数を作成する。
8. アイテムを追加したり、削除したり、アイテムを表示したりできる試薬コンポーネントを作成する。

#### Step 1: Create a new project

```
$ lein new rc animation
```

#### Step 2: Exclude react from reagent and add react-with-addons to dependecies vector in `project.clj`

```clojure
[reagent "0.5.1" :exclusions [cljsjs/react]]
[cljsjs/react-with-addons "0.13.3-0"]
```

#### Step 3: Navigate to `src/cljs/animation/core.cljs` and adapt react's CSSTransitionGroup addon to reagent

```clojure
(def css-transition-group
  (reagent/adapt-react-class js/React.addons.CSSTransitionGroup))
```

#### Step 4: Create the style for your transition

遷移名に `foo` を使っていることに注目してください。

```clojure
(def style
  "li {
  background-color: #44ee22; padding: 10px; margin: 1px; width: 150px;
  border-radius: 5px;
  font-size: 24px;
  text-align: center;
  list-style: none;
  color: #fff;
  height: 2em;
  line-height: 2em;
  padding: 0 0.5em;
  overflow: hidden;
  }

  .foo-enter {
  height: 0;
  transition: height 0.27s ease-out;
  }

  .foo-leave {
  height: 0;
  transition: height 0.27s ease-out;
  }

  .foo-enter-active {
  height: 2em;
  opacity: 1;
  }")
```

#### Step 5: Create the initial app-state

```clojure
(def app-state
  (reagent/atom {:items []
                 :items-counter 0}))
```

#### Step 6: Create an `add-item` function

ここでは2つのことをしています。1) アイテムの合計を記録すること、2) `app-state` に格納されているアイテムのリストに新しいアイテムを追加することです。

```clojure
(defn add-item []
  (let [items (:items @app-state)]
    (swap! app-state update-in [:items-counter] inc)
    (swap! app-state assoc :items (conj items (:items-counter @app-state)))))
```

#### Step 7: Create a `delete-item` function

ここでは、`app-state`に格納されているアイテムのリストに追加された最後のアイテムを削除しています。

```clojure
(defn delete-item []
  (let [items (:items @app-state)]
    (swap! app-state assoc :items (vec (butlast items)))))
```

#### Step 8: Create a reagent component that can add items, delete items, and displays the items

ステップ3では`css-transition-group`を使用し、ステップ4では`"foo"`という名前を使用していることに注目してください。

```clojure
(defn home []
  [:div
   [:div (str "Total list items to date:  " (:items-counter @app-state))]
   [:button {:on-click #(add-item)} "add"]
   [:button {:on-click #(delete-item)} "delete"]
   [:style style]
   [:ul
   [css-transition-group {:transition-name "foo"}
    (map-indexed (fn [i x]
                   ^{:key i} [:li (str "List Item " x)])
                 (:items @app-state))]]
   ])
```

# Usage

cljsファイルをコンパイルします。

```
$ lein clean
$ lein cljsbuild once prod
```

Open `resources/public/index.html`.
