# 「Open Taiko Chart」とは？
太鼓譜を記述するためのフォーマットです。太鼓譜を記述するためのフォーマットとして、「.tjf」や「.tja」等がありますが、複数の難易度、ダブルプレイを記述する場合複雑なテキストファイルとなり、さらにソフトウェア側でのパースも難解な処理になってしまうという欠点がありました。また、これらのファイルフォーマットで使用されているエンコーディングはShift-JISであり、ソフトウェアの国際化が顕著な今日では、とても扱いが難しいエンコーディングでもあります。「Open Taiko Chart」では、推奨するエンコーディングをUTF-8とし、どの言語、どの環境でも扱えるオープンなフォーマットとして公開します。

# 「Open Taiko Chart」のファイル構成
「Open Taiko Chart」では複数のファイルを使用し、「ひとつの作品」として成り立ちます。
## 「Open Taiko Chart Infomation」 (.tci)
このファイルには、その太鼓譜の名前やアーティスト、使用する音源のファイル名などの作品の情報と、各種難易度である「Open Taiko Chart Course」の情報が格納されます。
## 「Open Taiko Chart Course」 (.tcc)
このファイルは、「Open Taiko Chart Infomation」から呼び出されます。このファイルには、実際の譜面内容と固有の情報が記述されます。
## その他のファイル
譜面で使用する音源ファイルや、動画ファイルなどがこれに当てはまります。「Open Taiko Chart Infomation」と同じフォルダに入れておく必要があります。対応するコーデックやコンテナは、各ソフトウェアの実装で変わります。

## これらのファイルの制約
「Open Taiko Chart Infomation」は、「ひとつの作品」に対して2つ以上作成してはいけません。
「Open Taiko Chart Course」は、難易度の数より多く作成することができますが、難易度の数分しか有効ではありません。

# 「Open Taiko Chart Infomation」の記法

* エンコーディングは「BOM無し UTF-8」です。改行コードの指定はありません(理由は後述)。
* 「Open Taiko Chart Infomation」では、フォーマットにJSONを採用しています。エンコーディングが「BOM無し UTF-8」である理由は、このJSONフォーマットで決まっているからです(HAVE TO ではなく MUST)。改行してもしなくても問題ありません。
* JSONフォーマット採用しているため、多くのプログラミング言語ではデシリアライズが可能です。

## 「Open Taiko Chart Infomation」の書き方

サンプル

```json
{
  "title": "The Sample M@ster",
  "subtitle": "Sample Song",
  "artist": [ "Tanaka Ichiro", "Tanaka Jiro", "Tanaka Saburo"],
  "creator": [ "Suzuki Ichiro" ],
  "audio": "Audio-Source.wav",
  "background": "Background-Image.png",
  "bpm": 160,
  "courses":
  [
    {
      "difficluty": "oni",
      "level": 9,
      "single": "Oni.tcc"
    },
    {
      "difficluty": "edit",
      "level": 10,
      "single": "Edit.tcc",
      "multiple": [ "Edit_1P.tcc", "Edit_2P.tcc" ]
    }
  ]
}
```

これらは、譜面の基本的な情報を指定するものです。

| 変数名 | 説明 | 種類 | 例 |
| --- | --- | --- | --- |
| title | その譜面のタイトルを指定する。 | 文字列 | ``"name": "The Sample M@ster"`` |
| subtitle | その譜面のサブタイトルを指定する。 | 文字列 | ``"subtitle": "Sample Song"`` |
| artist | その曲のアーティストを指定する。 | 配列、文字列 | ``"artist": [ "Tanaka Ichiro", "Tanaka Jiro", "Tanaka Saburo"]`` |
| creator | その譜面の作成者を指定する。 | 配列、文字列 | ``"creator": [ "Suzuki Ichiro" ]`` |
| audio | その曲のファイル名を指定する。対応しているコーデック、コンテナはソフトウェアによって異なる。 | 文字列 | ``"audio":  "Audio-Source.wav"`` |
| background | 再生時に表示される背景画像・動画を指定する。対応しているコーデック、コンテナはソフトウェアによって異なる。 | 文字列 | ``"background": "Background-Image.png"`` |
| bpm | その曲の基本BPMを指定する。 | 数値 | ``"bpm": 160`` |
| courses | コースを情報を格納する。 | 配列、オブジェクト | (後述) |

これらは、coursesの中のオブジェクトで使用可能なものです。各難易度の情報を指定するものです。

| 変数名 | 説明 | 種類 | 例 |
| --- | --- | --- | --- |
| difficluty | その難易度の難易度を指定します。 | 文字列 | ``"difficluty": "oni"`` |
| level | その難易度の難易度を指定します。 | 数値 | ``"level": 9`` |
| single | その難易度の1人用譜面を指定します。 | 文字列 | ``"single": "Oni.tcc"`` |
| multiple | その難易度のn人用譜面を指定します。 | 配列、文字列 | ``"multiple": [ "Edit_1P.tcc", "Edit_2P.tcc" ]`` |

# 「Open Taiko Chart Course」の記法

* エンコーディングは「BOM無し UTF-8」です。改行コードの指定はありません(理由は後述)。
* 「Open Taiko Chart Course」では、フォーマットにJSONを採用しています。エンコーディングが「BOM無し UTF-8」である理由は、このJSONフォーマットで決まっているからです(HAVE TO ではなく MUST)。
* JSONフォーマット採用しているため、多くのプログラミング言語ではデシリアライズが可能です。

## 「Open Taiko Chart Infomation」の書き方

サンプル

```json
{
    "scoreinit": 800,
    "scorediff": 200,
    "scoreshinuchi": 1200,
    "balloon": [ 6, 10, 24 ],
    "measure":
    [
        [ "1" ],
        [ "3030" ],
        [ "40", "#GOGOSTART", "40" ],
        [ "#GOGOEND", "70008010" ],
        [ "7" ],
        [ "8" ],
        [ "#SCROLL 2", "1011102010111070" ],
        [ "0" ],
        [ "0" ],
        [ "8" ]
    ]
}
```

| 変数名 | 説明 | 種類 | 例 |
| --- | --- | --- | --- |
| scoreinit | その難易度の初項を指定します。 | 数値 | ``"scoreinit": 800`` |
| scorediff | その難易度の公差を指定します。 | 数値 | ``"scorediff": 200`` |
| scoreshinuchi | その難易度の真打配点を指定します。 | 数値 | ``"scoreshinuchi": 1200`` |
| balloon | その難易度のふうせん連打を指定します。 | 配列、数値 | ``"balloon": [ 6, 10, 24 ]`` |
| measures | その難易度の譜面を記述します。 | 配列、配列、文字列 | (後述) |

### measures内の書き方

* 配列1つにつき1小節とします。
* 小節の途中で命令を挟む場合、複数の配列に分割します、命令も1つの配列に単体で入れる必要があります。
