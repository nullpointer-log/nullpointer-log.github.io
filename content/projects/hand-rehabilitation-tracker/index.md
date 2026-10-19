---
title: "Hand Rehabilitation Tracker"
draft: false
ShowReadingTime: false
ShowPostNavLinks: false
---

**Computer Vision / Python**

**個人開発・検証中 / Python・Windowsアプリケーション**

カメラ映像から手のランドマークを推定し、指・手首・手の回旋に関する動きを記録・可視化するPythonの試作アプリです。Codexを開発支援に使用しています。

ソースコードは現在、GitHubのPrivateリポジトリで非公開管理しています。一般公開は今後検討します。

### 目的

日々の運動時に、カメラ画像から得られる角度や動作回数を記録し、本人が経過を振り返るための技術検証を目的としています。医療関係者には、画像ベースの簡易な運動記録・可視化の試作例としてご覧いただけます。

### 測定できる内容

アプリ起動時に、次の3モードから選択します。測定対象の手は右手が初期設定で、Rキーで左右を切り替えられます。

| モード | 記録内容 |
| --- | --- |
| 指の曲げ伸ばし | 薬指の3関節の推定角度、動作回数、時系列・角度分布。同一フレームで3角度がそろったときの合計最小値と、その画面画像を保存します。第三関節は手首を基準とした近似値です。 |
| 手首の曲げ伸ばし | 手首から薬指付け根、手首から肘への2線が画像平面で作る角度を記録します。まっすぐを0°とした符号付き角度で、プラス側・マイナス側の動作を一組として数え、確定した極値を画像とグラフに残します。肘の検出には姿勢推定モデルが必要です。 |
| 回旋 | 既定方式では手首から親指MCP（親指と手のひらの境目）への画像上の方向を使い、上向きの基準からの変化を回旋の近似値として記録します。前腕をカメラの法線方向に合わせ、親指を上向きにした基準位置の保持後、内側・外側を交互に行い、両方向の一組を1回として数えます。最大位置での停止は不要です。 |

測定画面は、カメラ映像と選択中のモードのグラフを一画面に表示します。起動時のボタンでモードを選び、測定中はSpaceキーで切り替えます。

指の角度は伸ばした状態を180°、直角を90°として扱います。測定値はカメラ画像とランドマーク推定に基づく参考値です。特に手首角度は画像平面上の値、回旋は画像方向の近似値であり、解剖学的な三次元関節角度ではありません。

### 3つの測定モードを画像で見る

以下は個人環境で保存した動作例です。画像内の日時・角度・回数は参考値で、診断や改善効果を示すものではありません。色付きの線・矢印はアプリの推定結果を表します。

#### 1．指の曲げ伸ばし

<figure style="margin:20px 0;padding:16px;border:1px solid #cbd5e1;border-radius:10px">
<img src="/images/hand-rehabilitation-tracker/mode-finger.jpg" alt="薬指の第一・第二・第三関節の推定角度を重ねた保存画像" width="900" height="824" loading="lazy" decoding="async" style="display:block;width:100%;max-width:620px;height:auto;margin:auto">
<figcaption style="margin-top:12px;font-size:0.9em">薬指の3関節を記録し、合計角度が最小になった画像を保存します。第三関節は手首を基準にした近似値です。</figcaption>
</figure>

**画像の見方：** 緑の線と赤い点は手の骨格推定、黄色のラベルは関節角度です。伸ばした状態を180°、直角を90°とし、伸展方向は200°まで扱います。曲げ伸ばしの回数と、動作ごとの合計角度の極小値・その内訳をグラフに残します。

#### 2．手首の曲げ伸ばし

<div style="display:flex;flex-wrap:wrap;gap:16px;margin:20px 0">
<figure style="flex:1 1 260px;min-width:0;margin:0;padding:12px;border:1px solid #cbd5e1;border-radius:10px">
<img src="/images/hand-rehabilitation-tracker/mode-wrist-positive.jpg" alt="手首のプラス方向の保存例。肘方向を黄色、薬指付け根方向を水色の矢印で表示" width="1484" height="951" loading="lazy" decoding="async" style="display:block;width:100%;height:auto">
<figcaption style="margin-top:10px;font-size:0.9em">プラス方向の保存例</figcaption>
</figure>
<figure style="flex:1 1 260px;min-width:0;margin:0;padding:12px;border:1px solid #cbd5e1;border-radius:10px">
<img src="/images/hand-rehabilitation-tracker/mode-wrist-negative.jpg" alt="手首のマイナス方向の保存例。肘方向と薬指付け根方向の2本の矢印を表示" width="1692" height="907" loading="lazy" decoding="async" style="display:block;width:100%;height:auto">
<figcaption style="margin-top:10px;font-size:0.9em">マイナス方向の保存例</figcaption>
</figure>
</div>

**画像の見方：** 黄色は手首から肘への方向、水色は手首から薬指付け根への方向です。この2線から、まっすぐを0°とした画像平面上の角度を求めます。肘と手首を画面に収めて測定します。

プラス・マイナスの確定極値を一組として1回と数え、極値の推移を折れ線グラフに表示します。同じ方向の極値が続くときは、絶対値が大きい方を採用します。画像の符号は画面上の方向に基づき、医学的な掌屈・背屈の名称を自動判定するものではありません。

#### 3．回旋（親指方向による画像上の近似）

<div style="display:flex;flex-wrap:wrap;gap:16px;margin:20px 0">
<figure style="flex:1 1 260px;min-width:0;margin:0;padding:12px;border:1px solid #cbd5e1;border-radius:10px">
<img src="/images/hand-rehabilitation-tracker/mode-rotation-negative.jpg" alt="親指方向がマイナス側へ回転した保存例。紫色の矢印は手首から親指MCPへの方向" width="900" height="922" loading="lazy" decoding="async" style="display:block;width:100%;height:auto">
<figcaption style="margin-top:10px;font-size:0.9em">一方の方向への回旋例</figcaption>
</figure>
<figure style="flex:1 1 260px;min-width:0;margin:0;padding:12px;border:1px solid #cbd5e1;border-radius:10px">
<img src="/images/hand-rehabilitation-tracker/mode-rotation-positive.jpg" alt="親指方向がプラス側へ回転した保存例。紫色の矢印は手首から親指MCPへの方向" width="900" height="733" loading="lazy" decoding="async" style="display:block;width:100%;height:auto">
<figcaption style="margin-top:10px;font-size:0.9em">反対方向への回旋例</figcaption>
</figure>
</div>

**画像の見方：** 紫色の矢印は手首から親指の付け根（MCP・点2）への方向です。前腕の軸をカメラの法線方向に合わせ、親指を上向きにして基準位置を2秒保持します。その後、内側・外側の最大方向を交互に行い、両方向を一組として1回と数えます。最大位置での停止は不要です。

画像の＋／−だけから内側・外側を医学的に判定しません。手首の曲がりや親指の開きにも影響される画像上の近似で、前腕軸まわりの実際の3D回旋角度を直接測定するものではありません。

### レポートとメール

測定終了時に、A4・3ページのPDFレポートを端末内に作成します。1ページ目は指、2ページ目は手首、3ページ目は回旋です。各ページに実施時間・動作回数・角度指標、記録された極値画像とグラフをまとめます。指は3関節合計が最小の画像、手首・回旋は両方向の極値画像を掲載します。指・手首には角度の度数分布も表示します。

各モードの結果に続けて、選択した手の過去最大10回を新しい順に掲載します。今回分も条件を満たす場合は先頭に加わり、最大11行です。履歴への掲載は、1回の測定で完了回数が5回以上の記録に限ります。手首・回旋は同じ測定方式の記録を抽出し、判定条件の差を注記します。指は旧方式や条件不明の履歴も注記付きで表示するため、角度や回数を単純比較できません。記録が不足する分の空行は表示しません。

メール送信は利用者が送信画面で選んだ場合に行います。Googleの認証画面でログインと送信許可を行い、短い本文とPDFレポート1件を送ります。画像・CSVを個別添付する仕様ではありませんが、PDFには撮影画像・測定値・過去履歴が含まれます。測定済みモードの必要画像が不足する場合は送信を止めます。認証設定・通信環境により送信できない場合があります。

### アプリの処理の流れ

<figure aria-label="現行アプリの処理の流れ" style="margin:24px 0;padding:20px;border:2px solid #64748b;border-radius:12px;background:#f8fafc;color:#172033">
<ol style="margin:0;padding-left:24px">
<li style="margin-bottom:14px"><strong>モードと測定する手を選択</strong><br>起動時に指・手首・回旋を選びます。右手が初期値で、Rキーで左右、Spaceキーでモードを切り替えます。</li>
<li style="margin-bottom:14px"><strong>カメラ画像を取得し、手を推定</strong><br>OpenCVで映像を取得し、RGBへ変換。MediaPipe Hand LandmarkerのVIDEOモードで手の21点と左右を推定します。未検出時は手付近を切り出して再検出を試みます。</li>
<li style="margin-bottom:14px"><strong>選択中のモードで角度・回数を計算</strong><br>指：薬指3関節と合計。手首：Pose Landmarkerで肘なども推定し、画像平面の角度を算出。既定の回旋：手首→親指MCPの画像方向を、2秒保持した基準からの変化として算出。欠測・揺れ・判定条件を確認して回数を確定します。</li>
<li style="margin-bottom:14px"><strong>画面表示とローカル記録を更新</strong><br>映像・骨格・方向・回数・グラフを一画面で表示します。表示用には手周辺を残して背景をぼかします。角度CSV、集計JSON、モード別の極値画像などを端末内に保存します。</li>
<li style="margin-bottom:14px"><strong>測定終了後に3ページのPDFを作成</strong><br>指→手首→回旋の順に、今回の結果・極値画像と、掲載条件を満たす過去最大10回の履歴をまとめます。未実施や画像不足は区別して表示します。</li>
<li><strong>利用者が選んだ場合だけメール送信</strong><br>結果確認画面からGoogleの認証・送信許可へ進み、短い本文とPDF1件をGmail APIで送信します。必要な極値画像などが不足する場合は送信を止めます。</li>
</ol>
<figcaption style="margin-top:16px;font-size:0.85em;color:#475569">通常の画像推論は端末内で実行します。背景ぼかしは推論用画像そのものへの前処理ではありません。既定の親指回旋は姿勢推定を使わず、手首モードでは肘の推定が必要です。メール送信時には画像・測定値を含むPDFが外部へ送られます。</figcaption>
</figure>

### 実装と検証状況

Python、OpenCV、MediaPipeを用いたWindowsアプリです。角度データは端末内に記録し、画面上の時系列グラフとPDFで可視化します。自動テストでは角度計算、回数・集計処理、レポートやメール送信処理の一部を合成データ・模擬通信で確認しています。これらは実カメラによる精度検証や医療現場での評価を意味しません。

現在の制約として、推定結果は撮影角度、距離、照明、手や腕の遮蔽、ランドマーク検出状態に影響されます。第三関節は近似値、手首は画像平面上、回旋は画像方向を用いた推定です。臨床的な測定精度、再現性、医療上の有効性は検証されていません。

**本アプリは技術検証目的の試作であり、医療上の診断・治療を目的としません。臨床的な測定精度および医療上の有効性は未検証です。**

本ページの3モード図解とトップページのサムネイルには、測定画面の例を掲載しています。画像内の数値は個人環境で得られた参考値であり、臨床的な評価や改善を示すものではありません。ソースコードは現在非公開で管理しており、公開は今後検討します。

[プロジェクト一覧](/projects/) · [実務経験](/experience/)
