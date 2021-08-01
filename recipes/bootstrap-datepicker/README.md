#  問題

[bootstrap-datepicker](https://github.com/eternicode/bootstrap-datepicker)を [reagent](https://github.com/reagent-project/reagent)のWebアプリケーションに追加したい。

# 解決策

次の[例](http://runnable.com/UmOlOZbXvZRqAABU/bootstrap-datepicker-example-text-input-with-specifying-date-format2)を参考にしてください。

*ステップ*

1. 新しいプロジェクトを作成します。
2. 必要な項目を `resources/public/index.html` に追加する。
3. `home-render`にテキスト入力を追加する。
4. javascriptをclojurescriptに変換して、`home-did-mount`という*did-mount*関数の中に入れる。
5. `home-render` と `home-did-mount` を使用して、`home` という名前の試薬コンポーネントを作成します。
6. エクスターンの追加

#### Step 1: Create a new project

```
$ lein new rc bootstrap-datepicker
```

#### Step 2: Add necessary items to `resources/public/index.html`

```html
<!DOCTYPE html>
<html lang="en">
  <body>
    <div id="app"></div>

    <!-- ATTENTION \/ -->
    <!-- jQuery -->
    <script src="https://ajax.googleapis.com/ajax/libs/jquery/1.11.1/jquery.min.js"></script>
    <!-- Bootstrap -->
    <link rel="stylesheet" href="https://maxcdn.bootstrapcdn.com/bootstrap/3.2.0/css/bootstrap.min.css">
    <script src="https://maxcdn.bootstrapcdn.com/bootstrap/3.2.0/js/bootstrap.min.js"></script>
    <!-- datepicker -->
    <link rel="stylesheet" href="http://cdnjs.cloudflare.com/ajax/libs/bootstrap-datepicker/1.7.1/css/datepicker3.min.css">
    <script src="http://cdnjs.cloudflare.com/ajax/libs/bootstrap-datepicker/1.7.1/js/bootstrap-datepicker.min.js"></script>
    <!-- ATTENTION /\ -->

    <script src="js/compiled/app.js"></script>
    <script>bootstrap_datepicker.core.main();</script>
  </body>
</html>
```

#### Step 3: Add text input to `home-render`

`src/cljs/bootstrap_datepicker/core.cljs` に移動します。

```clojure
(defn home-render []
  [:input {:type "text" :placeholder "click to show datepicker"}])
```

#### Step 4: Convert javascript to clojurescript and put inside a *did-mount* function called `home-did-mount`

これが必要なjavascriptです。

```javascript
$(document).ready(function () {
    $('#example1').datepicker({
        format: "dd/mm/yyyy"
    });
});
```

これをclojurescriptに変換して、`home-did-mount`に入れてみましょう。

```clojure
(defn home-did-mount []
  (.ready (js/$ js/document)
          (fn [] (.datepicker (js/$ "#example1") (clj->js {:format "dd/mm/yyyy"})))))
```

`.ready`メソッドは、`.datepicker`メソッドを実行する前に、DOMノードがページ上に存在することを保証するために使用されます。しかし、今回はコンポーネントのdid-mountのライフサイクルを利用しているので、コンポーネントが存在することはすでに保証されています。さらに、jQueryを使ってidで要素を選択するのではなく、React/Reagentを使ってDOMノードを直接取得することができます。 コードを以下のようにリファクタリングしてみましょう。

```clojure
(defn home-did-mount [this]
  (.datepicker (js/$ (reagent/dom-node this)) (clj->js {:format "dd/mm/yyyy"})))
```

#### Step 5: Use `home-render` and `home-did-mount` to create a reagent component called `home`

```clojure
(defn home []
  (reagent/create-class {:reagent-render home-render
                         :component-did-mount home-did-mount}))
```

#### Step 6: Add externs

高度なコンパイルのためには、`$.datepicker` がリネームされないように保護する必要があります。`externs.js` ファイルを追加します。

```js
var $ = function(){};
$.datepicker = function(){};
```

`project.clj`を開き、cljsbuildの部分にexternsへの参照を追加します。

```clojure
:externs ["externs.js"]
```

# Usage

cljsファイルをコンパイルします。

```
$ lein clean
$ lein cljsbuild once prod
```

`resources/public/index.html`を開きます。
