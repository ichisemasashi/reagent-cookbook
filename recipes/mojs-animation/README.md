#  問題

[mo - js](https://github.com/legomushroom/mojs)のアニメーションを[Reagent](https://github.com/reagent-project/reagent)のウェブアプリケーションに追加したい。

# 解決策

*ステップ*

1. 新しいプロジェクトを作成します。
2. `mo .js`ライブラリを`resources/public/vendor/js`に追加する。
3. 必要な項目を `resources/public/index.html` に追加する。
4. cssを `resources/public/css/animation.css` に追加する。
5. DOMノードを使ってTweenをレンダリングするために、`animation-did-mount`関数を追加する。
6. アニメーション中にノードを更新するための `translate-y` 関数を追加する。
7. `div` 要素をレンダリングする `animation` コンポーネントを作成します。
8. `project.clj` に `mo .js` ライブラリを使用して、externsを追加します。

#### Step 1: Create a new project

```
$ lein new rc mojs-animation
```

#### Step 2: Add the `mo . js` library to `resources/public/vendor/js`

```
wget -O resources/public/vendor/js/mo.min.js http://cdn.jsdelivr.net/mojs/latest/mo.min.js
```

#### Step 3: Add necessary items to `resources/public/index.html`

```html
<!DOCTYPE html>
<html lang="en">
  <body>
    <div id="app"></div>    
    <!-- mo.js -->
     <link rel="stylesheet" href="css/animation.css">
    <script src="vendor/js/mo.min.js"></script>
    <script src="js/compiled/app.js"></script>
    <script>mojs_animation.core.main();</script>
  </body>
</html>
```

#### Step 4: Add some css to `resources/public/css/animation.css`

```css
body {
  max-width: 600px;
  margin: 0 auto;
  padding-top: 72px;
}

.square {
  width:      50px;
  height:     50px;
  background: #F64040;
  position:   absolute;
  top:        10px;
  left:       50%;
  margin-left: -25px;
  margin-top:  -25px;
}
```

#### Step 5: Add `animation-did-mount` function to render a Tween using the DOM node

```clojure
(defn animation-did-mount [this]    
  (.run
   (js/mojs.Tween.
    (clj->js
     {:repeat 999
      :delay 2000
      :onUpdate (translate-y (reagent/dom-node this))}))))
```

#### Step 6: Add a `translate-y` function to update the node during animation

```clojure
(defn translate-y [node]
  (fn [progress]
    (set! (-> node .-style .-transform)
     (str "translateY(" (* 200 progress) "px)"))))
```

#### Step 7: Create an `animation` component that will render a `div` element

コンポーネントは、ブラウザのDOMにノードがマウントされると、外部のJsコードを呼び出します。これは、`component-did-mount`の状態で行う必要があります。

```clojure
(defn animation []
  (reagent/create-class {:render (fn [] [:div.square])
                         :component-did-mount animation-did-mount}))

(defn ^:export main []
  (reagent/render [animation]
                  (.getElementById js/document "app")))

```

#### Step 8: Add externs

高度なコンパイルのためには、`mojs` の名前空間化された関数がリネームされないように保護する必要があります。`project.clj` を開いて、コンパイラ部分の `:cljsbuild` キーの下に、エクスターンへの参照を追加します。また、`:closure-warnings`キーで、ライブラリからのエクスターンを解析する際の警告を抑制します。

```clojure
:externs ["resources/public/vendor/js/mo.min.js"]
:closure-warnings {:externs-validation :off
                   :non-standard-jsdoc :off}
```

# Usage

cljsファイルをコンパイルします。

```
$ lein clean
$ lein cljsbuild once
```

Open `resources/public/index.html`.
