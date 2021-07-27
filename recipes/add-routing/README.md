#  問題

Webアプリケーション[reagent](https://github.com/reagent-project/reagent)に、[Secretary](https://github.com/gf3/secretary)を使ったルーティングを追加したい。

# 解決策

*ステップ

1. 新しいプロジェクトを作成します。
2. `project.clj` :dependencies vector に secretary を追加します。
3. `app-state`という名前の試薬アトムを追加する
4. secretary を `core.cljs` 名前空間に追加する。
5. ブラウザの履歴を追加
6. ルートの作成
7. 相互にリンクする`home`と`about`ページの作成
8. どのページコンポーネントを表示するかを `app-state` に基づいて返す `current-page` マルチメソッドを作成する。
9. `main` を更新して、`app-routes` を含め、`current-page` をレンダリングする。

#### Step 1: Create a new project

```
$ lein new rc add-routing
```

#### Step 2: Add Secretary to `project.clj` :dependencies vector

```clojure
[secretary "1.2.3"]
```

#### Step 3: Add a reagent atom called `app-state`

```clojure
(def app-state (reagent/atom {}))
```

#### Step 4: Add Secretary to `core.cljs` namespace

Navigate to `src/cljs/add_routing/core.cljs` and update the `ns` to the following.

```clojure
(ns add-routing.core
    (:require-macros [secretary.core :refer [defroute]])
    (:import goog.history.Html5History)
    (:require [secretary.core :as secretary]
              [goog.events :as events]
              [goog.history.EventType :as EventType]
              [reagent.core :as reagent]))
```

#### Step 5: Add browser history

Add browser history.

```clojure
(defn hook-browser-navigation! []
  (doto (Html5History.)
    (events/listen
     EventType/NAVIGATE
     (fn [event]
       (secretary/dispatch! (.-token event))))
    (.setEnabled true)))
```

#### Step 6: Create routes

Secretary is a client-side router for clojurscript. We are going to create two routes and prefix them with `#`.  When a user hits the `#/` route, then the key `:page` in `app-state` will get the value of `:home`.  When a user hits the `#/about` route, then the key `:page` in `app-state` will get the value of `:about`.  Later we will create a function that will dispatch to the correct reagent component based on the keys `:home` and `:about`.

```clojure
(defn app-routes []
  (secretary/set-config! :prefix "#")

  (defroute "/" []
    (swap! app-state assoc :page :home))

  (defroute "/about" []
    (swap! app-state assoc :page :about))
  
  (hook-browser-navigation!))
```

#### Step 7: Create a `home` and `about` pages that link to each other

```clojure
(defn home []
  [:div [:h1 "Home Page"]
   [:a {:href "#/about"} "about page"]])

(defn about []
  [:div [:h1 "About Page"]
   [:a {:href "#/"} "home page"]])
```

#### Step 8: Create a `current-page` multimethod that will return which page component to display based on `app-state`

```clojure
(defmulti current-page #(@app-state :page))
(defmethod current-page :home [] 
  [home])
(defmethod current-page :about [] 
  [about])
(defmethod current-page :default [] 
  [:div ])
```

#### Step 9: Update `main` to include `app-routes` and to render `current-page`

```clojure
(defn ^:export main []
  (app-routes)
  (reagent/render [current-page]
                  (.getElementById js/document "app")))
```

# Usage

Compile cljs files.

```
$ lein clean
$ lein cljsbuild once prod
```

Open `resources/public/index.html`.
