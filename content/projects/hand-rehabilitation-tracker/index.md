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
| 回旋 | 既定方式では手首から親指MCP（親指と手のひらの境目）への画像上の方向を使い、上向きの基準からの変化を回旋の近似値として記録します。基準位置の保持後、内側・外側を交互に行い、両方向の一組を回数として数えます。 |

測定値はカメラ画像とランドマーク推定に基づく参考値です。特に手首角度は画像平面上の値、回旋は画像方向の近似値であり、解剖学的な三次元関節角度ではありません。

### レポートとメール

測定終了時に、実施時間、動作回数、角度指標・度数分布、各モードで記録された極値画像などをまとめたA4・2ページのPDFレポートを端末内に作成します。メール送信は利用者が送信画面で選んだ場合に行い、添付するのはPDFのみです。画像やCSVをメールに添付する仕様ではありません。認証設定・通信環境により送信できない場合があります。

### アプリの処理の流れ

<figure aria-label="測定からレポート作成までの処理" style="margin:24px 0;padding:20px;border:2px solid #64748b;border-radius:12px;background:#f8fafc;color:#172033">
<div style="display:flex;flex-wrap:wrap;align-items:stretch;gap:10px;text-align:center">
<div style="flex:1 1 145px;padding:14px;border:2px solid #2563eb;border-radius:8px;background:#eff6ff"><strong>カメラ入力</strong><br><small>映像を取り込み</small></div>
<div aria-hidden="true" style="align-self:center">→</div>
<div style="flex:1 1 145px;padding:14px;border:2px solid #7c3aed;border-radius:8px;background:#f5f3ff"><strong>ランドマーク推定</strong><br><small>手・指などの位置を推定</small></div>
<div aria-hidden="true" style="align-self:center">→</div>
<div style="flex:1 1 145px;padding:14px;border:2px solid #0f766e;border-radius:8px;background:#f0fdfa"><strong>モード別に計測</strong><br><small>角度・回数・極値を記録</small></div>
<div aria-hidden="true" style="align-self:center">→</div>
<div style="flex:1 1 145px;padding:14px;border:2px solid #b45309;border-radius:8px;background:#fffbeb"><strong>振り返り</strong><br><small>グラフ・PDFレポート</small></div>
</div>
<figcaption style="margin-top:14px;font-size:0.85em;color:#475569">処理の概略です。角度は画像上の推定値で、臨床測定値ではありません。</figcaption>
</figure>

### 実装と検証状況

Python、OpenCV、MediaPipeを用いたWindowsアプリです。角度データは端末内に記録し、画面上の時系列グラフとPDFで可視化します。自動テストでは角度計算、回数・集計処理、レポートやメール送信処理の一部を合成データ・模擬通信で確認しています。これらは実カメラによる精度検証や医療現場での評価を意味しません。

現在の制約として、推定結果は撮影角度、距離、照明、手や腕の遮蔽、ランドマーク検出状態に影響されます。第三関節は近似値、手首は画像平面上、回旋は画像方向を用いた推定です。臨床的な測定精度、再現性、医療上の有効性は検証されていません。

**本アプリは技術検証目的の試作であり、医療上の診断・治療を目的としません。臨床的な測定精度および医療上の有効性は未検証です。**

撮影画像や測定記録は公開ページに掲載していません。ソースコードは現在非公開で管理しており、公開は今後検討します。

[プロジェクト一覧](/projects/) · [実務経験](/experience/)
