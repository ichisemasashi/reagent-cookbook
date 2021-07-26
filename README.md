# Reagent Cookbook

![Reagent-Project](logo-rounded.jpg)

このリポジトリの目的は、[reagent](https://github.com/reagent-project/reagent)というウェブアプリで特定のタスクを実行する方法のレシピを提供することです。

最新情報については、twitterでフォローしてください。[@ReagentProject](https://twitter.com/ReagentProject). reagentについてつぶやくときは、`#reagent #cljs`を付けてください。

ビデオチュートリアルについては、youtubeで[subscribe](https://www.youtube.com/channel/UC1UP5LiNNNf0a45dA9eDA0Q)してください。

## Basics

* [Basic Component](https://github.com/ichisemasashi/reagent-cookbook/tree/master/basics/basic-component)
* [Component-level State](https://github.com/ichisemasashi/reagent-cookbook/tree/master/basics/component-level-state)
* [Cursors](https://github.com/ichisemasashi/reagent-cookbook/tree/master/basics/cursors)

## Recipes

* Animation
    * [mojs animation](https://github.com/ichisemasashi/reagent-cookbook/tree/master/recipes/mojs-animation)
    * [ReactCSSTransitionGroup](https://github.com/ichisemasashi/reagent-cookbook/tree/master/recipes/ReactCSSTransitionGroup)
* Bootstrap
    * [bootstrap modal window](https://github.com/ichisemasashi/reagent-cookbook/tree/master/recipes/bootstrap-modal)
    * [bootstrap-datepicker](https://github.com/ichisemasashi/reagent-cookbook/tree/master/recipes/bootstrap-datepicker)
* Canvas
    * [Canvas fills div](https://github.com/ichisemasashi/reagent-cookbook/tree/master/recipes/canvas-fills-div)
* Charting
    * [highcharts](https://github.com/ichisemasashi/reagent-cookbook/tree/master/recipes/highcharts)
    * [morris](https://github.com/ichisemasashi/reagent-cookbook/tree/master/recipes/morris)
* Images
    * [google-street-view](https://github.com/ichisemasashi/reagent-cookbook/tree/master/recipes/google-street-view)
* jQuery UI
    * [autocomplete](https://github.com/ichisemasashi/reagent-cookbook/tree/master/recipes/autocomplete)
    * [draggable element](https://github.com/ichisemasashi/reagent-cookbook/tree/master/recipes/draggable)
    * [droppable element](https://github.com/ichisemasashi/reagent-cookbook/tree/master/recipes/droppable)
    * [sortable portlets](https://github.com/ichisemasashi/reagent-cookbook/tree/master/recipes/sortable-portlets)
* Maps
    * [Leaflet](https://github.com/ichisemasashi/reagent-cookbook/tree/master/recipes/leaflet)
    * [Google Maps](https://github.com/ichisemasashi/reagent-cookbook/tree/master/recipes/google-maps)
* Misc.
    * [compare argv](https://github.com/ichisemasashi/reagent-cookbook/tree/master/recipes/compare-argv)
    * [Live Markdown Editor](https://github.com/ichisemasashi/reagent-cookbook/tree/master/recipes/markdown-editor)
	* [server-side-rendering](https://github.com/ichisemasashi/reagent-cookbook/tree/master/recipes/reagent-server-rendering)
	* [toggle class](https://github.com/ichisemasashi/reagent-cookbook/tree/master/recipes/toggle-class)
	* [file upload with filestack](https://github.com/ichisemasashi/reagent-cookbook/tree/master/recipes/file-upload)
	* [typeahead](https://github.com/ichisemasashi/reagent-cookbook/tree/master/recipes/typeaheadjs)
	* [editable label](https://github.com/ichisemasashi/reagent-cookbook/tree/master/recipes/editable-label)
* Routing
    * [add routing](https://github.com/ichisemasashi/reagent-cookbook/tree/master/recipes/add-routing) with secretary
* Sidebar
    * [Simple Sidebar](https://github.com/ichisemasashi/reagent-cookbook/tree/master/recipes/simple-sidebar)
* State
    * [local-storage](https://github.com/ichisemasashi/reagent-cookbook/tree/master/recipes/local-storage) with storage-atom
    * [undo](https://github.com/ichisemasashi/reagent-cookbook/tree/master/recipes/undo) with historian
* Tables
    * [DataTables](https://github.com/ichisemasashi/reagent-cookbook/tree/master/recipes/data-tables)
	* [sortable table](https://github.com/ichisemasashi/reagent-cookbook/tree/master/recipes/sort-table)
	* [filter table](https://github.com/ichisemasashi/reagent-cookbook/tree/master/recipes/filter-table)
* Validation
    * [input validation (color-coded)](https://github.com/ichisemasashi/reagent-cookbook/tree/master/recipes/input-validation)

## 一般的なレシピの出発点

試薬料理(reagent-cookbook)のレシピの出発点は、[reagent-cookbook-template](https://github.com/gadfly361/reagent-cookbook-template)です。

```
$ lein new rc <name of recipe>
```

注：reagent-cookbook-templateは、特にレシピに沿って従うために作られました。 もしあなたが、いくつかのバッテリーを含んだ新しい試薬アプリケーションを始めることに興味があるなら、[reagent-template](https://github.com/reagent-project/reagent-template)が良いスタートの設定を提供してくれます: `$ lein new reagent <アプリの名前>`.

## Contributing

レシピは大歓迎です。 フォーク、ブランチ、そしてプルリクエストを提出してください。

また、以下のようなPRもお待ちしています。

*nvd3* レシピの高度なコンパイルのための適切な externs の追加 (今のところ old-recipes の中にあります)
*mermaid* レシピの高度なコンパイルのための正しい externs の追加 (これは今のところ old-recipes の中にあります)

## LICENSE

Copyright © 2015 Matthew Jaoudi

Distributed under the The MIT License (MIT).

www.DeepL.com/Translator（無料版）で翻訳しました。