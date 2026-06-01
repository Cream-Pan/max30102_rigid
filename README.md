<div id="top"></div>

# MAX30102_rigid

MAX30102を用いた小型PPGセンサ用リジッド基板である．
耳装着型デバイスやウェアラブルデバイスへの組み込みを想定し，光電脈波（PPG）を取得するために設計した．

## 使用技術一覧

<p style="display: inline">
  <img src="https://img.shields.io/badge/-KiCad-314CB0.svg?logo=kicad&style=for-the-badge&logoColor=white">
  <img src="https://img.shields.io/badge/-MAX30102-0096D6.svg?style=for-the-badge">
  <img src="https://img.shields.io/badge/-I2C-555555.svg?style=for-the-badge">
  <img src="https://img.shields.io/badge/-JLCPCB-FDDB27.svg?style=for-the-badge&logoColor=black">
</p>

## 目次

1. [概要](#概要)
2. [基板仕様](#基板仕様)
3. [回路構成](#回路構成)
4. [ディレクトリ構成](#ディレクトリ構成)
5. [Git管理方針](#git管理方針)
6. [製造データ](#製造データ)
7. [設計上の注意](#設計上の注意)

## 概要

本リポジトリは，MAX30102を搭載したリジッド基板のKiCadプロジェクトを管理するためのものである．
MAX30102は，赤色LED，赤外LED，フォトダイオード，ADCを内蔵した光学式センサであり，PPG波形の取得や心拍推定に利用できる．

本基板では，MAX30102を小型基板上に実装し，外部マイコンからI2C経由でPPGデータを取得する構成としている．

<p align="right">(<a href="#top">トップへ</a>)</p>

---

## 基板仕様

| 項目      | 内容                     |
| ------- | ---------------------- |
| 基板名     | MAX30102_rigid         |
| センサ     | MAX30102               |
| 通信方式    | I2C                    |
| I2Cアドレス | 0x57                   |
| 取得データ   | Red / IR PPG           |
| 用途      | PPG取得，心拍推定，ウェアラブルセンシング |
| 設計ツール   | KiCad                  |

<p align="right">(<a href="#top">トップへ</a>)</p>

---

## 回路構成

本基板は，MAX30102と外部マイコンをI2Cで接続する構成である．

```text
MCU
├── SDA
├── SCL
└── INT

MAX30102
├── Red LED
├── IR LED
├── Photodiode
├── ADC
└── FIFO
```

MAX30102内部のFIFOに格納されたPPGデータをマイコン側で読み出し，心拍推定や波形解析に利用する．

<p align="right">(<a href="#top">トップへ</a>)</p>

---

## ディレクトリ構成

```text
.
├── .gitignore
├── .history/                         # VSCode等の履歴フォルダ，Git管理対象外
├── garbar/                           # Gerber出力用フォルダ
├── jlcpcb/                           # JLCPCB発注用データ
├── MAX30102_rigid-backups/           # KiCadバックアップ，Git管理対象外
├── fp-info-cache                     # KiCadキャッシュ，Git管理対象外
├── MAX30102_rigid.kicad_pcb          # PCBレイアウト
├── MAX30102_rigid.kicad_prl          # KiCadローカル設定，Git管理対象外
├── MAX30102_rigid.kicad_pro          # KiCadプロジェクト
├── MAX30102_rigid.kicad_sch          # 回路図
└── MAX30102_test1.kicad_prl          # KiCadローカル設定，Git管理対象外
```

基本的に，Gitで管理する主要ファイルは以下である．

```text
MAX30102_rigid.kicad_pro
MAX30102_rigid.kicad_sch
MAX30102_rigid.kicad_pcb
.gitignore
README.md
```

<p align="right">(<a href="#top">トップへ</a>)</p>

---

## Git管理方針

本リポジトリでは，KiCadプロジェクト本体をGitで管理し，自動生成ファイルや個人環境依存ファイルは管理対象外とする．

主に以下をGit管理対象外とする．

```text
*.kicad_prl
fp-info-cache
*-backups/
*_backups/
*.bak
*-bak
_autosave-*
*.lck
.history/
```

また，製造出力ファイルも基本的には自動生成物として扱う．

```text
*.gbr
*.drl
*.pos
*.rpt
*.zip
```

本プロジェクトでは，自作シンボルおよび自作フットプリントを `kicad_my_library` リポジトリで管理している．  
KiCadで本プロジェクトを開く前に，`kicad_my_library` をcloneし，KiCadのライブラリ設定に登録する必要がある．

```powershell
git clone https://github.com/Cream-Pan/kicad_my_library.git

登録するライブラリは以下である．

Footprint Library:
kicad_my_library/footprints/MyLibrary.pretty

Symbol Library:
kicad_my_library/symbols/MyLibrary.kicad_sym

KiCad上でのライブラリNicknameは以下に統一する．

MyLibrary

Nicknameを変更すると，MyLibrary:C0603 のような既存の参照が壊れる可能性がある．


発注時点の製造データを残す場合は，必要に応じてGitHub Releasesや別フォルダで管理する．

<p align="right">(<a href="#top">トップへ</a>)</p>

---

## 製造データ

`jlcpcb` フォルダには，JLCPCBでの基板製造および部品実装を想定したデータを配置する．

主な出力対象は以下である．

* Gerber
* Drill
* BOM
* CPL

現状の `.gitignore` では，`.gbr`，`.drl`，`.zip` などの製造出力はGit管理対象外としている．
そのため，発注データをGitHub上に残したい場合は，`.gitignore` の例外設定またはGitHub Releasesでの管理を検討する．

<p align="right">(<a href="#top">トップへ</a>)</p>

---

## 設計上の注意

### 1．電源

MAX30102では，LED駆動時にパルス的な電流が流れるため，電源ラインの安定性が重要である．
VLEDおよびVDD近傍にデカップリングコンデンサを配置し，電源ノイズがPPG信号に混入しにくい構成とする．

### 2．光学設計

PPG信号は，センサと皮膚の距離，接触状態，外光の混入に影響される．
そのため，実装時には以下に注意する．

* センサと皮膚の距離を一定に保つ
* 外光を遮断する
* 光学窓上にフラックス残渣や汚れを残さない
* 皮膚との接触圧が過度に変動しない構造にする

### 3．I2C通信

MAX30102はI2Cで通信するため，SDAおよびSCLのプルアップ抵抗，配線長，ノイズ混入に注意する．
複数センサと同一バス上で使用する場合は，I2Cアドレスの重複やバス容量にも注意する．

<p align="right">(<a href="#top">トップへ</a>)</p>

---

開発者情報
Name: Takato Ishii

Portfolio: https://takato-ishii.vercel.app/
