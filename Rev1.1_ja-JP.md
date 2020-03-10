# 「Open Taiko Chart」とは？
太鼓譜を記述するためのフォーマットです。太鼓譜を記述するためのフォーマットとして、「.tjf」や「.tja」等がありますが、複数の難易度、ダブルプレイを記述する場合複雑なテキストファイルとなり、さらにソフトウェア側でのパースも難解な処理になってしまうという欠点がありました。また、これらのファイルフォーマットで使用されているエンコーディングはShift-JISであり、ソフトウェアの国際化が顕著な今日では、とても扱いが難しいエンコーディングでもあります。「Open Taiko Chart」では、エンコーディングをUTF-8とし、どの言語、どの環境でも扱えるオープンなフォーマットとして公開します。

# 「Open Taiko Chart」のファイル構成

「Open Taiko Chart」では複数のファイルを使用し、「ひとつの作品」として成り立ちます。

## 「Open Taiko Chart Infomation」 (.tci)

このファイルには、その太鼓譜の名前やアーティスト、使用する音源のファイル名などの作品の情報と、各種難易度である「Open Taiko Chart Course」の情報が格納されます。

## 「Open Taiko Chart Course」 (.tcc)

このファイルは、「Open Taiko Chart Infomation」から呼び出されます。このファイルには、実際の譜面内容と固有の情報が記述されます。

## 「Open Taiko Chart Medley」 (.tcm)

このファイルは、複数の「Open Taiko Chart Infomation」を使用して、メドレー形式で再生する機能です。また、条件を付与することで段位認定としても機能します。

## その他のファイル

譜面で使用する音源ファイルや、動画ファイルなどがこれに当てはまります。「Open Taiko Chart Infomation」と同じフォルダに入れておく必要があります。対応するコーデックやコンテナは、各ソフトウェアの実装で変わります。

## これらのファイルの制約

* 「Open Taiko Chart Infomation」は、「ひとつの作品」に対して2つ以上作成してはいけません。
* 「Open Taiko Chart Medley」は、1つ以上の「Open Taiko Chart Infomation」を記述する必要があります。

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
  "offset": 2.416,
  "courses":
  [
    {
      "difficluty": "oni",
      "level": 9,
      "single": "Oni.tcc"
    },
    {
      "difficulty": "edit",
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
| albumart | その譜面のアルバムアートを指定する。対応しているフォーマットはソフトウェアによって異なる。 | 文字列 | ``"albumart": "Album-Art.png"`` |
| artist | その曲のアーティストを指定する。 | 配列、文字列 | ``"artist": [ "Tanaka Ichiro", "Tanaka Jiro", "Tanaka Saburo"]`` |
| creator | その譜面の作成者を指定する。 | 配列、文字列 | ``"creator": [ "Suzuki Ichiro" ]`` |
| audio | その曲のファイル名を指定する。対応しているコーデック、コンテナはソフトウェアによって異なる。 | 文字列 | ``"audio":  "Audio-Source.wav"`` |
| background | 再生時に表示される背景画像・動画を指定する。対応しているコーデック、コンテナはソフトウェアによって異なる。 | 文字列 | ``"background": "Background-Image.png"`` |
| movieoffset | 背景動画が始まる時間を指定する。 | 数値 | ``"movieoffset": -1.2`` |
| bpm | その曲の基本BPMを指定する。 | 数値 | ``"bpm": 160`` |
| offset | その曲の1小節目が始まる時間を指定する。 | 数値 | ``"offset": 2.416`` |
| courses | コースを情報を格納する。 | 配列、オブジェクト | (後述) |

* アルバムアートはソフトウェアによって自動的に拡大縮小されます。推奨されるアスペクト比は1:1です。
* ``movieoffset``は``offset``の位置からの時間の差を入力します。

これらは、coursesの中のオブジェクトで使用可能なものです。各難易度の情報を指定するものです。

| 変数名 | 説明 | 種類 | 例 |
| --- | --- | --- | --- |
| difficulty | その難易度の難易度を指定します。 | 文字列 | ``"difficulty": "oni"`` |
| level | その難易度の難易度を指定します(0～10)。 | 数値 | ``"level": 9`` |
| single | その難易度の1人用譜面を指定します。 | 文字列 | ``"single": "Oni.tcc"`` |
| multiple | その難易度のn人用譜面を指定します。 | 配列、文字列 | ``"multiple": [ "Edit_1P.tcc", "Edit_2P.tcc" ]`` |

# 「Open Taiko Chart Course」の記法

* エンコーディングは「BOM無し UTF-8」です。改行コードの指定はありません(理由は後述)。
* 「Open Taiko Chart Course」では、フォーマットにJSONを採用しています。エンコーディングが「BOM無し UTF-8」である理由は、このJSONフォーマットで決まっているからです(HAVE TO ではなく MUST)。
* JSONフォーマット採用しているため、多くのプログラミング言語ではデシリアライズが可能です。

## 「Open Taiko Chart Course」の書き方

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
        [ "40", "#gogobegin", "40" ],
        [ "#gogoend", "70008010" ],
        [ "7" ],
        [ "8" ],
        [ "#scroll 2", "1011102010111070" ],
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
* また譜面の書き方として数字を使い、次のように記述します。
   * 0: 空白
   * 1: ドン
   * 2: カツ
   * 3: ドン(大)
  * 4: カツ(大)
  * 5: 連打
  * 6: 連打(大)
  * 7: ふうせん連打
  * 8: 連打終了
* 連打中は0以外の数字を入れてはいけません(e.g. ``50001008``)。
* 連打の数字を終端まで続けるような書き方をしてはいけません(e.g. `666668`)。

#### 命令

``#<command> <params>`` という書き方で譜面を変化させる命令を付与することができます。

| 命令名 | 説明 | パラメータ | 例 |
| --- | --- | --- | --- |
| ``#bpm n1`` | BPMを変更します。 | n1: BPM。 | ``#bpm 120.00`` |
| ``#scroll n1`` | スクロール速度を変更します。 | n1: スクロール速度(1を等倍とする)。 | ``#scroll 3.9`` |
| ``#tsign n1/n2`` | n2分のn1拍子を変更します。 | n1: 分子。n2: 分母。 | ``#tsign 3/4`` |
| ``#gogobegin`` | ゴーゴータイムを開始します。 | - | ``#gogobegin`` |
| ``#gogoend`` | ゴーゴータイムを終了します。 | - | ``#gogoend`` |
| ``#delay n1`` | n1秒その後に続く譜面をずらします。 | n1: ずらす秒数。 | ``#delay 3.14`` |

## デシリアライズ

JavaScriptの場合``eval()``や``JSON.parse``、C#の場合、[Json.NET](https://github.com/JamesNK/Newtonsoft.Json)等を使用することで、そのままデシリアライズすることが可能です。もちろん、他の言語でもシリアライザーがあればそのままデータを利用することができます。

```cs
public class OpenTaikoChartInfomation
{
    [JsonProperty("title")]
    public string Title { get; set; }
    [JsonProperty("subtitle")]
    public string SubTitle { get; set; }
    [JsonProperty("artist")]
    public string[] Artist { get; set; }
    [JsonProperty("creator")]
    public string[] Creator { get; set; }
    [JsonProperty("audio")]
    public string Audio { get; set; }
    [JsonProperty("background")]
    public string Background { get; set; }
    [JsonProperty("bpm")]
    public double? BPM { get; set; }
    [JsonProperty("offset")]
    public double? Offset { get; set; }
    [JsonProperty("courses")]
    public OpenTaikoChartInfomation_Courses[] Courses { get; set; }
}

public class OpenTaikoChartInfomation_Courses
{
    [JsonProperty("difficulty")]
    public string Difficulty { get; set; }
    [JsonProperty("level")]
    public int? Level { get; set; }
    [JsonProperty("single")]
    public string Single { get; set; }
    [JsonProperty("multiple")]
    public string[] Multiple { get; set; }
}

public class OpenTaikoChartCourse
{
    [JsonProperty("scoreinit")]
    public int? ScoreInit { get; set; }
    [JsonProperty("scorediff")]
    public int? ScoreDiff { get; set; }
    [JsonProperty("scoreshinuchi")]
    public int? ScoreShinuchi { get; set; }
    [JsonProperty("balloon")]
    public int?[] Balloon { get; set; }
    [JsonProperty("measures")]
    public string[][] Measures { get; set; }
}
```

# 「Open Taiko Chart Medley」の記法

* エンコーディングは「BOM無し UTF-8」です。改行コードの指定はありません(理由は後述)。
* 「Open Taiko Chart Medley」では、フォーマットにJSONを採用しています。エンコーディングが「BOM無し UTF-8」である理由は、このJSONフォーマットで決まっているからです(HAVE TO ではなく MUST)。改行してもしなくても問題ありません。
* JSONフォーマット採用しているため、多くのプログラミング言語ではデシリアライズが可能です。

## 「Open Taiko Chart Medley」の書き方

```json
{
    "title": "サンプル メドレー",
    "exams":
    [
        {
          "type": "gauge",
          "range": "more",
          "value": [ 95, 100 ]
        },
        {
          "type": "combo",
          "range": "more",
          "value": [ 400, 450 ]
        },
        {
          "type": "judgebad",
          "range": "less",
          "value": [ 20, 10 ]
        }
    ],
    "charts":
    [
        {
          "file": "Song-1\\Sample-Master.tci",
          "difficulty": "oni"
        },
        {
          "file": "Song-2\\Sample-Theme-Song.tci",
          "difficulty": "oni"
        },
        {
          "file": "Song-3\\Sample-Boss-Song.tci",
          "difficulty": "edit"
        }
    ]
}
```

| 変数名 | 説明 | 種類 | 例 |
| --- | --- | --- | --- |
| title | メドレーのタイトル。 | 文字列 | ``"title": "サンプル メドレー"`` |
| exams | メドレー再生中の条件。 | 配列、オブジェクト | - |
| charts | メドレーの曲。 | 配列、オブジェクト | - |

examsの中はこのようなパラメータがあります。

| 変数名 | 説明 | 種類 | 例 |
| --- | --- | --- | --- |
| type | 条件種別。<br>``gauge``: ゲージの値<br>``judgeperfect``: 良の数<br>``judgegood``: 可の数<br>``judgebad``: 不可の数<br>``score``: スコア<br>``roll``: 連打数<br>``hit``: たたけた数<br>``combo``: コンボ数 | 文字列 | ``"type": "gauge"`` |
| range | 条件範囲。<br>``more``: 以上<br>``less``: 以下 | 文字列 | ``"range": "more"`` |
| value | 条件の値。1つのみでもよい。2つめ以降の扱いはソフトウェアによって異なる。 | 配列、数値 | ``"value": [ 95, 100 ]`` |

chartsの中はこのようなパラメータがあります。

| 変数名 | 説明 | 種類 | 例 |
| --- | --- | --- | --- |
| file | Open Taiko Chart Infomation のファイル名。 | 文字列 | ``"file": "Song-1\\Sample-Master.tci"`` |
| difficulty | 難易度。その難易度が存在しなければ、1番難易度が低いコースを使用する。 | 文字列 | ``"difficulty": "oni"`` |

* ファイル名は、自分自身(.tcmファイル)からの相対パスを入力します。JSONフォーマットの使用上、パスの区切りである``\``は``\\``と記述する必要があります。
