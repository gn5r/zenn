---
title: "Windows 11でJIS/US配列を共存させる"
emoji: "⚙️"
type: "idea" # tech: 技術記事 / idea: アイデア
topics: ["windows"]
published: true
---

JIS 配列と US 配列の共存方法として色々情報がありますが、自分なりの簡単な変更方法を確立したので記事にしました
ざっくり以下のような流れになります

1. システム配列を「接続済みキーボードレイアウトを使用する」に変更し、レイアウト強制の設定値を削除
2. ノートパソコンに内蔵されているキーボードレイアウトを JIS へ変更する
3. JIS と US の共存を確認

今回の例では JIS/US の共存ですが、3 個以上を切り替えるケースは想定していないので別途対処が必要かもしれません

検証したノートパソコンの情報は下記の通り
尚、Windows 11 22H2 を rufus でクリーンインストールし Windows Update を実行した直後に今回の設定を行っています

| 名称 |        詳細         |
| :--: | :-----------------: |
| 機種 |   Dynabook R734/K   |
|  OS  |   Windows 11 22H2   |
| CPU  | Intel Core i5 4300M |
| RAM  |         8GB         |

# システムキーボードレイアウトを変更する

## 接続済みキーボードレイアウトを使用するへ変更

1. Win + I を押下
2. 左メニューの「時刻と言語」をクリック
3. 「言語と地域」をクリック
4. 日本語の三点リーダー(...)をクリックし「言語のオプション」をクリック
5. 画面中央より少し下のキーボード欄にあるキーボードレイアウト: 日本語キーボード(106/109 キー) -> 「レイアウトを変更する」をクリック
6. 新しくウィンドウが開くのでセレクトボックスをクリックし「接続済みキーボードレイアウトを使用する」をクリック
7. 「今すぐ再起動する」をクリック

下記画像を参照

![キーボードレイアウトの変更](/images/coexistence-jis-and-us/change_keyboard_layour.jpg)
_「接続済みキーボードレイアウトを使用する」をクリック_

# 内臓キーボードのレイアウトを変更する

## reg ファイルを作成する

reg ファイルを新規作成し、下記コードをコピペ
@@@になっている部分は後ほどコピーするデバイスインスタンスパスへ書き換えます

```reg:keyboard_layout_to_jis.reg
[HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Enum\@@@\Device Parameters]
"OverrideKeyboardType"=dword:00000007
"OverrideKeyboardSubtype"=dword:00000002
```

## デバイスマネージャーを開いてデバイスインスタンスパスをコピーする

Win + X -> M を押下してデバイスマネージャーを開く

下記画像の赤枠で囲った部分をコピー

![デバイスインスタンスパス](/images/coexistence-jis-and-us/device_instance_path.jpg)

先ほど作成した reg ファイルの「@@@」となっている部分に、コピーしたデバイスインスタンスパスをペーストして下さい
(改行が入ってしまった場合は削除)

エンコード を「UTF-16 LE」に変更し保存
作成した reg ファイルを実行してレジストリを登録する

regedit を開き「HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Enum\\@@@\Device Parameters」に下記キーが登録されていれば OK

- OverrideKeyboardType
- OverrideKeyboardSubtype

レジストリを弄ったため再起動

再起動後、JIS キーボードの半角/全角が機能するか、US キーボードの`が機能するかなどを確認し問題なければ作業完了です。
