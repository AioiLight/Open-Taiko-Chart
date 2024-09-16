# What is "Open Taiko Chart"?
Open Taiko Chart is a format for notating Taiko charts. Although file formats such as ".tjf" and ".tja" exist for notating Taiko charts, they have the disadvantage of becoming complicated text files when notating multiple difficulties and 2-player charts, as well as being difficult for software to parse. In addition, the encoding being used in these file formats is often Shift-JIS, which is very difficult to handle nowadays due to the significance of software internationalization. Open Taiko Chart, using UTF-8 as the encoding, is published to be an open format which can be used in any programming language and any environment.

# File Structure of Open Taiko Chart

An Open Taiko Chart consists of multiple files to form a "single work".

## "Open Taiko Chart Information" (.tci)

This file contains information about the "work", such as the name, the artist, and the filename of the audio used of/for the Taiko chart, as well as the information of "Open Taiko Chart Course" for each difficulty.

## "Open Taiko Chart Course" (.tcc)

This file is to be required from the Open Taiko Chart Information file. The actual chart contents and particular information about the difficulty are notated in this file.

## "Open Taiko Chart Medley" (.tcm)

This file is for the functionality that uses multiple Open Taiko Chart Information files and plays them in a form of a medley. The medley can also function as a dan-i certification challenge when dan-i conditions are specified.

## Other Files

This includes audio files, video files, *etc*. which are used for this chart. They need to be put in the same folder as the Open Taiko Chart Information file. The supported codecs and container formats may vary depending on the implementation of individual software.

## Constraints of These Files

* NO more than one Open Taiko Chart Information file may be created for a "single work".
* At least one Open Taiko Chart Information file needs to be notated for an Open Taiko Chart Medley.

# Format of Open Taiko Chart Information

* The encoding is "UTF-8 without BOM". The newline sequence is unspecified (explained below).
* For Open Taiko Chart Information, the JSON format is adopted. The encoding is UTF-8 without BOM because it has been decided as such in the JSON format (not ***HAVE TO***, but ***MUST***). The presence/non-presence of newlines is insignificant.
* Since the JSON format is adopted, the data can be deserialized in many programming languages with easy.

## Notation of Open Taiko Chart Information

Example:

```json
{
  "title": "The Sample M@ster",
  "subtitle": "Sample Song",
  "artist": [ "Tanaka Ichiro", "Tanaka Jiro", "Tanaka Saburo"],
  "creator": [ "Suzuki Ichiro" ],
  "audio": "Audio-Source.wav",
  "songpreview": 68.2,
  "background": "Background-Image.png",
  "bpm": 160,
  "offset": 2.416,
  "courses":
  [
    {
      "difficulty": "oni",
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

Fields used to specify the basic information about the chart:

| Variable Name | Description | Type | Example |
| --- | --- | --- | --- |
| title | The title of the chart. | string | ``"name": "The Sample M@ster"`` |
| subtitle | The subtitle of the chart. | string | ``"subtitle": "Sample Song"`` |
| albumart | The album art of the chart. The supported formats may vary depending on the software. | string | ``"albumart": "Album-Art.png"`` |
| artist | The artist(s) of the song. | array, string | ``"artist": [ "Tanaka Ichiro", "Tanaka Jiro", "Tanaka Saburo"]`` |
| creator | The creator(s) of the chart. | array, string | ``"creator": [ "Suzuki Ichiro" ]`` |
| audio | The filename of the song audio. The supported codecs and container formats may vary depending on the software. | string | ``"audio": "Audio-Source.wav"`` |
| songpreview | The time position of the song from which the preview should start. | number | ``"songpreview": 68.2`` |
| background | The background image/video to be displayed during gameplay. The supported codecs and container formats may vary depending on the software. | string | ``"background": "Background-Image.png"`` |
| movieoffset | The time when the background movie should start. | number | ``"movieoffset": -1.2`` |
| bpm | The base BPM of the song. | number | ``"bpm": 160`` |
| offset | The time when the first measure of the song should begin. | number | ``"offset": 2.416`` |
| courses | Information about each difficulty. | array, object | (see below) |

* The album art may be automatically scaled depending on the software. The recommended aspect ratio is 1:1.
* ``movieoffset`` should be specified as the time difference from the position of ``offset``.

Fields available for each object in the field "courses" to specify the information for each difficulty:

| Variable Name | Description | Type | Example |
| --- | --- | --- | --- |
| difficulty | The difficulty name of the difficulty. | string | ``"difficulty": "oni"`` |
| level | The difficulty star rating of the difficulty (0-10). | number | ``"level": 9`` |
| single | The chart for 1-player gameplay of the difficulty. | string | ``"single": "Oni.tcc"`` |
| multiple | The set of charts for n-player gameplay of the difficulty. | array, string | ``"multiple": [ "Edit_1P.tcc", "Edit_2P.tcc" ]`` |

# Format of Open Taiko Chart Course

* The encoding is "UTF-8 without BOM". The newline sequence is unspecified (explained below).
* For Open Taiko Chart Course, the JSON format is adopted. The encoding is UTF-8 without BOM because it has been decided as such in the JSON format (not ***HAVE TO***, but ***MUST***). The presence/non-presence of newlines is insignificant.
* Since the JSON format is adopted, the data can be deserialized in many programming languages with easy.

## Notation of Open Taiko Chart Course

Example:

```json
{
    "scoreinit": 800,
    "scorediff": 200,
    "scoreshinuchi": 1200,
    "balloon": [ 6, 10, 24 ],
    "measures":
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

| Variable Name | Description | Type | Example |
| --- | --- | --- | --- |
| scoreinit | The initial term of per-GOOD score for the difficulty. | number | ``"scoreinit": 800`` |
| scorediff | The common difference of per-GOOD score for the difficulty. | number | ``"scorediff": 200`` |
| scoreshinuchi | The per-GOOD score in shin-uchi mode for the difficulty. | number | ``"scoreshinuchi": 1200`` |
| balloon | The pop count of each balloon roll for the difficulty. | array, number | ``"balloon": [ 6, 10, 24 ]`` |
| measures | The chart content for the difficulty. | array, array, string | (see below) |

### Notation within Field "measures"

* Each inner array specifies a measure.
* To place a command in the middle of a measure, split the measure into multiple array cells, and the command also needs to be directly placed in an array cell.
* And use digits to write the chart as follows.
  * 0: blank
  * 1: Don
  * 2: Katsu
  * 3: Don (Big)
  * 4: Katsu (Big)
  * 5: drumroll
  * 6: drumroll (Big)
  * 7: balloon roll
  * 8: end of roll
* Digits other than 0 may NOT be inserted during rolls (e.g. ``50001008``).
* The notation of using consecutive digits for the roll until the end may NOT be used (e.g. `666668`).

#### Commands

Commands, each in the format of ``#<command> <params>``, can be placed to change the chart:

| Command Name | Description | Parameters | Example |
| --- | --- | --- | --- |
| ``#bpm n1`` | Changes the BPM. | n1: BPM. | ``#bpm 120.00`` |
| ``#scroll n1`` | Changes the scroll speed. | n1: scroll speed (where 1 equals to the default speed). | ``#scroll 3.9`` |
| ``#tsign n1/n2`` | Changes the time signature to contain n1 n2-th notes per measure. | n1: numerator. n2: denominator. | ``#tsign 3/4`` |
| ``#gogobegin`` | Starts a go-go time section. | - | ``#gogobegin`` |
| ``#gogoend`` | Ends a go-go time section. | - | ``#gogoend`` |
| ``#rotate n1`` | Rotates the scroll velocity of future notes in the chart by n1 degrees. | n1: amount of degrees to rotate in degrees. | ``#rotate 180`` |
| ``#delay n1`` | Offsets/delays future notes in the chart by n1 seconds. | n1: amount of seconds to offset. | ``#delay 3.14`` |
| ``#bar visible`` | Shows or hides future bar lines. | visible: show (show bar lines) / hide (hide bar lines) | ``#bar hide`` |

## Deserialization

Methods such as ``eval()`` and ``JSON.parse`` for JavaScript, or libraries such as [Json.NET](https://github.com/JamesNK/Newtonsoft.Json) for C# can be used to deserialize the data out-of-box. Of course, the data can also be used out-of-box in any other language as long as a JSON serializer is available in the language.

```cs
public class OpenTaikoChartInformation
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
    public OpenTaikoChartInformation_Courses[] Courses { get; set; }
}

public class OpenTaikoChartInformation_Courses
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

# Format of Open Taiko Chart Medley

* The encoding is "UTF-8 without BOM". The newline sequence is unspecified (explained below).
* For Open Taiko Chart Medley, the JSON format is adopted. The encoding is UTF-8 without BOM because it has been decided as such in the JSON format (not ***HAVE TO***, but ***MUST***). The presence/non-presence of newlines is insignificant.
* Since the JSON format is adopted, the data can be deserialized in many programming languages with easy.

## Notation of Open Taiko Chart Medley

```json
{
    "title": "Sample Medley",
    "subtitle": "A Sample Medley.",
    "albumart": "albumart.png",
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

| Variable Name | Description | Type | Example |
| --- | --- | --- | --- |
| title | The title of the medley. | string | ``"title": "Sample Medley"`` |
| subtitle | The subtitle of the medley. | string | ``"subtitle": "A Sample Medley."`` |
| albumart | The album art of the medley. The supported formats may vary depending on the software. | string | ``"albumart": "Album-Art.png"`` |
| exams | The dan-i conditions during the medley gameplay. | array, object | - |
| charts | The songs in the medley. | array, object | - |

Parameters available for each object in the field "exams":

| Variable Name | Description | Type | Example |
| --- | --- | --- | --- |
| type | The type of the dan-i condition.<br>``gauge``: value of gauge<br>``judgeperfect``: count of GOODs<br>``judgegood``: count of OKs<br>``judgebad``: count of BADs<br>``score``: score<br>``roll``: count of drumroll hits<br>``hit``: count of hits<br>``combo``: count of combo | string | ``"type": "gauge"`` |
| range | The range of the dan-i condition.<br>``more``: equal to or more than<br>``less``: less than | string | ``"range": "more"`` |
| value | The values of the dan-i condition. One value alone is also acceptable. How the 2nd and subsequent values are handled ​​may vary depending on the software. | array, number | ``"value": [ 95, 100 ]`` |

Parameters available for each object in the field "charts":

| Variable Name | Description | Type | Example |
| --- | --- | --- | --- |
| file | The filename of the Open Taiko Chart Information file. | string | ``"file": "Song-1\\Sample-Master.tci"`` |
| difficulty | Name of the difficulty. If the difficulty does not exist for the specified song, the existing difficulty with the name indicating the lowest difficulty is used instead. | string | ``"difficulty": "oni"`` |

* The filename should be specified in a relative path from the current .tcm file itself. Due to the JSON format, the path separator ``\`` needs to be written instead as ``\\``.