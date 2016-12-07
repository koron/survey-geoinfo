# Generate GeoJSON for Japan

国土交通省から最新データをダウンロードする

<http://nlftp.mlit.go.jp/ksj/gml/datalist/KsjTmplt-N03.html>

アンケートが面倒なのと、ネットワークがえらく細い(=遅い)ので要注意。

以下、例として東京都用データ `N03-140401_13_GML.zip` をダウンロードした。

SHAPE から GeoJSON に変換する

変換ツール: <http://www.gdal.org/> から ogr2ogr を使って変換する。ogr2ogr
は gdal のたくさんあるツールのうちの1つである。Windows向けZIP版では、
`bin\gdal\app\ogr2ogr.exe` を `bin\` にコピーすることで、直接実行できるよう
になる。

データの zip ファイルを展開し、`N03-14_13_140401.shp` というファイルが存在
するディレクトリに移動して、以下の変換用コマンドを実行する。変換時には他の
ファイルも参照しているので、欠けないように要注意。

```
$ ogr2ogr -f GeoJSON out.geojson N03-14_13_140401.shp
```

これで `out.geojson` が得られる。
