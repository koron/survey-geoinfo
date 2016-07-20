# Survey Geo-Info

緯度経度から、地理情報を効率的に検索する方法を調査する。
ここでいう地理情報とは領域に対して与えられる。

## 調査ログ

### 前提

検索は [S2][s2] を使う前提。また言語はJava優先

*   [Java実装](https://github.com/google/s2-geometry-library-java)
*   [golang実装](https://github.com/golang/geo)

### S2 の概要

地球上をレベル0から30の31段階のサイズの矩形(セル)に分割し、
1つのセルに対して64bit整数値を1つ割り当てたもの。
割り当て方にヒルベルト曲線を用いるのが特徴で、
下位レベル(レベルが大きい)のセル(より狭い)が、
上位レベル(レベルが小さい)のセル(より広い)に
含まれているかどうかを計算しやすい。

地図上のある領域、例えば東京都港区などをS2のセルの組み合わせで示すことで、
ある緯度経度情報が与えられた時に、
その領域に含まれているかどうかを正確かつ高速に判定できる。
この時、領域情報をなるべく少ないセルで表現することで、
すなわちよりレベルの高いセルを多用することで、
高速に判定が可能になる。

### 領域情報

領域情報には GeoJSON から S2 cells を得る必要があり、これには
golang の実装 ([akhenakh/regionagogo][regionagogo]) が存在する。
関連記事: [A geo database for polygons, foundations](http://blog.nobugware.com/post/2016/geo_db_s2_region_polygon/)

GeoJSON ([仕様][geojson-ja]) は地図空間データ交換フォーマットである。

GeoJSON 関連資料:

*   [GeoJSONを使いこなすためのWebサービスまとめ](http://shimz.me/blog/web/3789)

### 日本の地理情報

日本の地理情報(GeoJSON)には [dataofjapan/land][japan/land] が存在した。
都道府県単位、及び東京都や福島県の市区町村名単位は存在したが、
その他の市区町村単位は含まれていない。
これらは [国土地理院のデータ][gm-jpn] から生成されているらしいので、
そちらから生成することはできるかもしれない。

また個人プロジェクトとしても以下が存在した。

*   [国土交通省のシェイプ形式データから全都道府県市町村郡区のGeoJsonとTopoJsonを作成](http://qiita.com/niiyz/items/4e98766172340a7ee929)

### Java における S2 の状況

[regionagogo][regionagogo] 相当の Java プロジェクトを探した。
手始めに maven での [s2-geometry-library-java](s2-java) の利用状況を調査した。
しかしそもそもプロジェクト内に pom.xml が無いこともあり、
[野良パッケージ](https://mvnrepository.com/artifact/org.isuper/s2-geometry-library-java) の登録しか無く、
あまり期待が持てそうにないため、捜索方法を変更した。

捜索方法を変更したが、良い結果を得られなかったため、
実際に使ってみる方向に軌道修正。

### s2-geometry-library-java を使ってみる

チェックアウトして `ant test` してみた。
一部テストが以下のとおり失敗している。
要検証かもしれない。

```
    [junit] Running com.google.common.geometry.S2PolygonTest
    [junit] Testsuite: com.google.common.geometry.S2PolygonTest
    [junit] Tests run: 1, Failures: 1, Errors: 0, Skipped: 0, Time elapsed: 0.015 sec
    [junit] Tests run: 1, Failures: 1, Errors: 0, Skipped: 0, Time elapsed: 0.015 sec
    [junit] 
    [junit] Testcase: testUnionSloppySuccess took 0.013 sec
    [junit] 	FAILED
    [junit] null
    [junit] junit.framework.AssertionFailedError: null
    [junit] 	at com.google.common.geometry.GeometryTestCase.assertDoubleNear(Unknown Source)
    [junit] 	at com.google.common.geometry.S2PolygonTest.assertPointApproximatelyEquals(Unknown Source)
    [junit] 	at com.google.common.geometry.S2PolygonTest.testUnionSloppySuccess(Unknown Source)
    [junit] 
```

### Java における GeoJSON の取り扱い

Java で GeoJSON を扱うには [GeoTools][geotools] を使うのが良さそうだ。

関連情報:

*   [ShapeファイルをGeoJSONに変換する](http://qiita.com/t-mat/items/562d71e62102cc906152)

[s2]:http://blog.christianperone.com/2015/08/googles-s2-geometry-on-the-sphere-cells-and-hilbert-curve/
[s2-java]:https://github.com/google/s2-geometry-library-java
[regionagogo]:https://github.com/akhenakh/regionagogo
[geojson-ja]:http://s.kitazaki.name/docs/geojson-spec-ja.html
[japan/land]:https://github.com/dataofjapan/land
[gm-jpn]:http://www.gsi.go.jp/kankyochiri/gm_jpn.html
[geotools]:http://geotools.org/
