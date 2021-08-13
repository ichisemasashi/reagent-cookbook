#  問題

ブラウザのウィンドウサイズが変更されても、少なくとも一次元的にdivを埋めるキャンバスが欲しい。

# 解決策

`create-class`とReagent `atom`を使って、div内のcanvasが包含するdivを確実に埋めるために必要なイベントをリッスンしてみましょう。ウィンドウのサイズが変わると 

*ステップ*

1. 新しいプロジェクトを作成する
2. CSSを `resources/public/index.html` に追加する。
3. ウィンドウのサイズをトラッキングするためのアトムとイベントハンドラを追加する
4. コンポーネント `div-with-canvas` を作成する。
5. キャンバスの内容を描画する関数を作成します。
6. `home` の定義で `div-with-canvas` コンポーネントを参照する。

#### Step 1: Create a new project

```
$ lein new rc canvas-fills-div
```

#### Step 2: Add CSS to `resources/public/index.html`

簡単な CSS スタイルを追加して、ウィンドウを埋める div を作成します。このスタイルのほとんどは、`with-canvas` の div が利用可能なすべての垂直方向のスペースを満たすようにするためのものです。幅いっぱいの div/canvas が必要なだけであれば、`display: block` 以外はすべて削除できます。


```html
<head>
  <style>
    html, body {
      margin:0;
      padding:0;
      height:100%;
    }

    div#app {
        height: 100%;
    }

    div.with-canvas {
        margin: 0px;
        padding: 0px;
        border: 0px;

        height: 100%;
    }

    div.with-canvas canvas {
        display: block;
    }
  </style>
</head>
```

#### Step 3: ウィンドウのサイズを追跡するためのアトムとイベントハンドラの追加

ウィンドウの現在の幅を追跡するために、Reagentアトムを定義します。 キャンバスコンポーネントはこのアトムを参照し、ウィンドウのサイズが変わると再レンダリングされるようにします。

```clojure
(def window-width (reagent/atom nil))
```

ウィンドウのサイズが変更されたときに `window-width` を更新するイベント ハンドラを定義します。キャンバスのサイズは囲んでいる div から取得しているので、特定の値ではなくアトムが更新されるのがポイントです。

```clojure
(defn on-window-resize [ evt ]
  (reset! window-width (.-innerWidth js/window)))
```

リサイズイベントのウィンドウイベントリスナーとして、`on-window-resize`を追加しました。

```clojure
(defn ^:export main []
  (reagent/render [home]
                  (.getElementById js/document "app"))
  (.addEventListener js/window "resize" on-window-resize))
```

#### Step 4: Create a `div-with-canvas` component

`src/cljs/div_fills_canvasc/core.cljs` に移動します。これは、コンポーネントがマウントされた時点でDOMノードをキャプチャし、コンポーネントが更新されたときにキャンバスにレンダリングする必要があるため、`creat-class`を使用しています。

```clojure
(defn div-with-canvas [ ]
  (let [dom-node (reagent/atom nil)]
    (reagent/create-class
     {:component-did-update
      (fn [ this ]
        (draw-canvas-contents (.-firstChild @dom-node)))

      :component-did-mount
      (fn [ this ]
        (reset! dom-node (reagent/dom-node this)))

      :reagent-render
      (fn [ ]
        @window-width ;; Trigger re-render on window resizes
        [:div.with-canvas
         ;; reagent-render is called before the compoment mounts, so
         ;; protect against the null dom-node that occurs on the first
         ;; render
         [:canvas (if-let [ node @dom-node ]
                    {:width (.-clientWidth node)
                     :height (.-clientHeight node)})]])})))
```

#### Step 5: キャンバスの内容を描画する関数を作成する

```clojure
(defn draw-canvas-contents [ canvas ]
  (let [ ctx (.getContext canvas "2d")
        w (.-clientWidth canvas)
        h (.-clientHeight canvas)]
    (.beginPath ctx)
    (.moveTo ctx 0 0)
    (.lineTo ctx w h)
    (.moveTo ctx w 0)
    (.lineTo ctx 0 h)
    (.stroke ctx)))
```

#### Step 6: `home`の定義にある`div-with-canvas`コンポーネントを参照してください。

```clojure
(defn home []
  [div-with-canvas])
```

# Usage

Compile cljs files.

```
$ lein clean
$ lein cljsbuild once prod
```

Open `resources/public/index.html`.
