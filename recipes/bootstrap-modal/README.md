#  問題

[試薬](https://github.com/reagent-project/reagent)のウェブアプリに、[モーダルウィンドウ](http://getbootstrap.com/javascript/)を追加したい。

# 解決策

[reagent-modals](https://github.com/Frozenlock/reagent-modals)というライブラリを使います。

*ステップ*

1. 新しいプロジェクトを作成します。
2. 必要な項目を `resources/public/index.html` に追加します。
3. `project.clj` に reagent-modals を追加する。
4. 名前空間 `src/cljs/bootstrap_modal/core.cljs` に reagent-modals を追加する。
5. `home` にモーダルウィンドウを追加する。
6. モーダルウィンドウを表示するボタンを作成する
7. `home` に `modal-window-button` を追加する。

#### Step 1: Create a new project

```
$ lein new rc bootstrap-modal
```

#### Step 2: Add necessary items to `resources/public/index.html`

```html
<!DOCTYPE html>
<html lang="en">
  <body>
    <div id="app"></div>

    <!-- ATTENTION \/ -->
    <!-- jQuery -->
    <script src="https://code.jquery.com/jquery-2.1.1.min.js"></script>
    <!-- Bootstrap -->
    <link rel="stylesheet" href="https://maxcdn.bootstrapcdn.com/bootstrap/3.2.0/css/bootstrap.min.css">
    <script src="https://maxcdn.bootstrapcdn.com/bootstrap/3.2.0/js/bootstrap.min.js"></script>
    <!-- ATTENTION /\ -->

    <script src="js/compiled/app.js"></script>
    <script>bootstrap_modal.core.main();</script>
  </body>
</html>
```

#### Step 3: Add reagent-modals to `project.clj`

以下を `:dependencies` ベクターに追加します。

```clojure
[org.clojars.frozenlock/reagent-modals "0.2.3"]
```

#### Step 4: Add reagent-modals to `src/cljs/bootstrap_modal/core.cljs` namespace

```clojure
(ns bootstrap-modal.core
    (:require [reagent.core :as reagent]
              [reagent-modals.modals :as reagent-modals]))
```

#### Step 5: Add modal-window to `home`

```clojure
(defn home []
  [:div
   [reagent-modals/modal-window]
   ])
```
#### Step 6: Create a button to bring up the modal window

```clojure
(defn modal-window-button []
  [:div.btn.btn-primary 
   {:on-click #(reagent-modals/modal! [:div "some message to the user!"])} 
   "My Modal"])
```

#### Step 7: Add `modal-window-button` to `home`

```clojure
(defn home []
  [:div
   [reagent-modals/modal-window]
   ;; ATTNETION \/
   [modal-window-button]
   ;; ATTENTION /\
   ])
```

# Usage

cljsファイルをコンパイルします。

```
$ lein clean
$ lein cljsbuild once prod
```

`resources/public/index.html`を開きます。
