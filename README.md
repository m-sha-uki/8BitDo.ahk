# 8BitDo.ahk 日本語ガイド

AutoHotkey v2を使い、8BitDo Microなどのボタンへ、アプリ・ウィンドウ・Webサイト別の処理を割り当てるスクリプトです。

> このリポジトリは[thehafenator/8BitDo.ahk](https://github.com/thehafenator/8BitDo.ahk)のフォークです。このREADMEは日本語利用者向けに再構成しました。

## できること

- `a`、`b`、`up`など、物理ボタン名で割り当てを記述
- タップと長押しへ別々の処理を設定
- アプリ、ウィンドウタイトル、ウィンドウクラス、ブラウザー、URLごとに設定
- 個別設定がない場合はグローバル設定へフォールバック
- コントローラー側のキー割り当てを1か所で管理

本体の設定領域を書き換えるツールではありません。8BitDo Ultimate Softwareなどで各ボタンへF13～F24等を割り当て、その入力をWindows上で変換します。

## 必要なもの

- Windows
- [AutoHotkey v2](https://www.autohotkey.com/)
- Kモード（キーボードモード）で接続した8BitDo Microなど

URL別判定を使う場合は、付属の`OnWebsite.ahk`とUI Automation関連ファイルも必要です。

## 導入

1. このリポジトリをZIPでダウンロードして展開します。
2. AutoHotkey v2をインストールします。
3. `8BitDo.ahk`をテキストエディターで開きます。
4. コントローラー側のキー設定と`8BitDoClass.ahk`内の`keyMap`を一致させます。
5. `8BitDo.ahk`を実行します。

最初はF13～F24を使う構成がおすすめです。通常のキーボード操作と衝突しにくいためです。

## 推奨キー配置

```ahk
static keyMap := Map(
    "a", "F13", "b", "F14", "x", "F15", "y", "F16",
    "l", "F17", "r", "F18", "l2", "F19", "r2", "F20",
    "select", "F21", "start", "F22", "star", "F23", "home", "F24",
    "left", "F1", "down", "F2", "right", "F3", "up", "F4"
)
```

この表はコントローラーへ実際に書き込んだキーと完全に一致させてください。

## 基本的な書き方

### タップ

```ahk
_8.a["global"] := (*) => MsgBox("Aを押しました")
```

### 長押し

変数名の末尾に`H`を付けます。

```ahk
_8.aH["global"] := (*) => MsgBox("Aを長押ししました")
```

同じ条件にタップと長押しの両方がある場合、スクリプトが押下時間を判定して実行します。既定の長押し判定は約200msです。

### グローバル設定

```ahk
_8.up["global"] := (*) => Send("{Up down}")
_8.down["global"] := (*) => Send("{Down down}")
_8.left["global"] := (*) => Send("{Left}")
_8.right["global"] := (*) => Send("{Right}")
_8.start["global"] := (*) => Send("^z")
```

## アプリ別設定

AutoHotkeyのウィンドウ条件をキーとして指定します。

```ahk
_8.a["ahk_exe CLIPStudioPaint.exe"] := (*) => Send("p")
_8.b["ahk_exe CLIPStudioPaint.exe"] := (*) => Send("e")
_8.x["ahk_exe CLIPStudioPaint.exe"] := (*) => Send("{Space down}")
_8.start["ahk_exe CLIPStudioPaint.exe"] := (*) => Send("^s")
```

ウィンドウタイトル、クラス、実行ファイルを組み合わせることもできます。

```ahk
_8.a["Spotify Premium", "ahk_exe Spotify.exe ahk_class Chrome_WidgetWin_1"] := (*) => Send("{Media_Play_Pause}")
```

## ブラウザー／Webサイト別設定

`OnWebsite.ahk`を利用するとURL単位の割り当てができます。

```ahk
_8.a["youtube.com"] := (*) => Send("{Space}")
_8.left["youtube.com"] := (*) => Send("{Left}")
_8.right["youtube.com"] := (*) => Send("{Right}")
_8.y["youtube.com"] := (*) => Send("f")
```

ブラウザー全体へ適用する場合は`browsers`を使います。

```ahk
_8.a["browsers"] := (*) => Send("{Enter}")
_8.b["browsers"] := (*) => Send("{Escape}")
```

## 条件の優先順位

ボタンを押すと、概ね次の順番で具体的な条件が優先されます。

1. Win32メニュー（`ahk_class #32768`）
2. URL固有設定
3. ウィンドウタイトル
4. ウィンドウクラス
5. 実行ファイル名
6. ブラウザー共通設定
7. `global`設定

たとえばYouTube固有設定があればそれを使い、なければブラウザー設定、さらにそれもなければグローバル設定が使われます。

## Win32メニューの操作例

```ahk
_8.up["ahk_class #32768"] := (*) => Send("{Up}")
_8.down["ahk_class #32768"] := (*) => Send("{Down}")
_8.left["ahk_class #32768"] := (*) => Send("{Left}")
_8.right["ahk_class #32768"] := (*) => Send("{Enter}")
_8.start["ahk_class #32768"] := (*) => Send("{Escape}")
```

## 別のキー配置案

### Win＋ファンクションキー

通常のアプリショートカットと衝突しにくい構成です。

```ahk
static keyMap := Map(
    "a", "#F1", "b", "#F2", "x", "#F3", "y", "#F4",
    "l", "#F5", "r", "#F6", "l2", "#F7", "r2", "#F8",
    "select", "#F9", "start", "#F10", "star", "#F11", "home", "#F12",
    "left", "F1", "down", "F2", "right", "F3", "up", "F4"
)
```

### テンキー

テンキーを文字入力に使わない場合に便利です。

```ahk
static keyMap := Map(
    "a", "NumpadInsert", "b", "NumpadEnd", "x", "NumpadDown", "y", "NumpadPgDn",
    "l", "NumpadPgUp", "r", "NumpadDiv", "l2", "NumpadMult", "r2", "NumpadSub",
    "select", "NumpadAdd", "start", "NumpadClear", "star", "NumpadEnter", "home", "NumpadHome",
    "left", "NumpadLeft", "down", "NumpadDel", "right", "NumpadRight", "up", "NumpadUp"
)
```

## トラブルシューティング

### ボタンを押しても反応しない

- AutoHotkey v2で実行しているか確認します。
- コントローラーをKモードで接続します。
- Ultimate Softwareで設定したキーと`keyMap`が一致しているか確認します。
- Window Spyやキー履歴で、実際に送信されているキーを確認します。

### 一部のアプリだけ反応しない

対象アプリが管理者権限で起動している場合、AHK側にも同等の権限が必要です。`ahk_exe`のファイル名が正しいかWindow Spyで確認してください。

### URL判定が動かない

`OnWebsite.ahk`とUI Automation関連ファイルが正しく配置され、対応ブラウザーを使用しているか確認してください。

## 注意事項

- `Alt+F4`など、誤操作の影響が大きいキーは本体へ直接割り当てないことを推奨します。
- 設定変更中は、未保存の作業を先に保存してください。
- スクリプト例には作者の個人環境向け関数が含まれる場合があります。未定義関数は自分の環境に合わせて置き換えてください。

## ライセンス

ライセンスおよび原作者の条件は、リポジトリ内の記載と上流リポジトリを確認してください。
