---
title: "Hand Rehabilitation Tracker"
draft: false
ShowReadingTime: false
ShowPostNavLinks: false
---

**Computer Vision / Python**

**個人開発・検証中 / Python・Windowsアプリケーション**

カメラ映像から手指ランドマークと関節角度を推定し、運動の記録・可視化を行う試作です。Codexを開発支援に使用しています。

### アプリの処理の流れ

<figure aria-label="カメラ入力、手指ランドマーク検出、角度解析、表示と記録の処理フロー" style="margin:24px 0;padding:20px;border:2px solid #64748b;border-radius:12px;background:#f8fafc;color:#172033;text-align:center">
<div style="padding:12px;border:2px solid #2563eb;border-radius:8px;background:#eff6ff;color:#172033"><strong>① カメラ映像を取得</strong><br><small>OpenCVで手の動きをリアルタイムに取り込む</small></div>
<div aria-hidden="true" style="padding:6px">↓</div>
<div style="padding:12px;border:2px solid #7c3aed;border-radius:8px;background:#f5f3ff;color:#172033"><strong>② 手指のランドマークを検出</strong><br><small>MediaPipeで関節などの位置を推定</small></div>
<div aria-hidden="true" style="padding:6px">↓</div>
<div style="padding:12px;border:2px solid #0f766e;border-radius:8px;background:#f0fdfa;color:#172033"><strong>③ 薬指の関節角度・曲げ伸ばし回数を算出</strong><br><small>伸ばした状態は180°、直角は90°として角度を表示</small></div>
<div aria-hidden="true" style="padding:6px">↓</div>
<div style="display:flex;flex-wrap:wrap;gap:12px;text-align:left">
<div style="flex:1 1 200px;padding:12px;border:2px solid #0284c7;border-radius:8px;background:#e0f2fe;color:#172033"><strong>画面で確認</strong><br><small>骨格・角度を映像に重ねて表示<br>現在値・最小値・回数を表示<br>時系列グラフで動きを確認</small></div>
<div style="flex:1 1 200px;padding:12px;border:2px solid #b45309;border-radius:8px;background:#fffbeb;color:#172033"><strong>記録・レポート</strong><br><small>角度データをCSV保存<br>右手の角度合計が最小の画像を保存<br>日次の回数・角度分布を集計</small></div>
</div>
<figcaption style="margin-top:14px;font-size:0.85em;color:#475569">処理の概略図です。実際のアプリ画面や測定結果ではありません。</figcaption>
</figure>

### 画面構成

![右手薬指の角度合計が最小になった時点の保存画面](/images/hand-rehabilitation-tracker/min-total-screen.jpg)

- **左側**：背景をぼかしたカメラ映像に、手の骨格と薬指の各関節角度を重ねて表示します。
- **右側**：関節角度の一覧と、左右の薬指の現在値・最小値・曲げ伸ばし回数を表示します。
- **上部**：保存時の右手薬指3関節の角度と合計、開始からの経過時間を表示します。

実際の保存画面です。角度はAIによる推定値で、第三関節は手首を基準とする近似値です。画像内のカウント閾値は撮影時の設定であり、現在の仕様とは異なります。

### 構成と実装内容

- PythonによるWindows向け画像処理アプリケーション。
- OpenCV・MediaPipeを使った手指ランドマーク解析と関節角度の推定。
- 角度データのCSV記録、時系列グラフ、関節別回数・度数分布のレポートによる可視化。
- 合成データと模擬通信を用いた自動テストの実施。
- Codexを用いた実装・テスト・改善の開発支援。

| 使用技術 | 用途 |
| --- | --- |
| Python / NumPy | 角度計算・集計・記録 |
| OpenCV / MediaPipe | 画像取得・画像処理・骨格推定 |
| Pillow / Tkinter | 日本語描画・結果確認画面 |
| unittest / Git / Codex | テスト・変更管理・開発支援 |

### 計測・記録の仕様

| 項目 | 現行実装 |
| --- | --- |
| 角度 | 伸ばした状態180°、直角90°の内角。第三関節は手首を基準とする近似値 |
| 回数 | 関節ごとに160°以上 → 140°以下 → 160°以上で1回。150°±10°のヒステリシスで境界付近の揺れによる重複カウントを抑制 |
| 画面表示 | 左右の薬指の現在角度・最小角度・回数。Rキーで表示側の最小値・回数をリセット |
| 測定全体の集計 | 右手薬指の関節別の最小角度・最大角度・回数を保存。Rキーによる表示リセットとは独立 |
| 自動保存画像 | 同じフレームで右手薬指3関節が有効なとき、内角合計が最小の画像を保存。各関節の個別最小値を足したものではない |
| グラフ・CSV | グラフは直近30秒。CSVは測定全体の右手薬指の角度を記録 |
| 日次レポート | 保存済みの測定から関節別回数と角度の度数分布を集計。分布は検出サンプル数に基づく |

### 検証範囲と制約

Codexによる実装支援と自動テストを、開発者による実機・実運用確認とは区別しています。単体テストでは角度計算、ヒステリシスによる回数計算、同時刻の最小合計画像の選択、日次集計、メール内容・添付、OAuthコールバックと背景ぼかしを確認します。合成データと模擬通信を使用し、実カメラの認識精度、実Google認証、メール配送を保証しません。第三関節は手首を基準とした近似値です。

**技術検証目的の試作であり、医療上の診断・治療を目的としません。** 背景ぼかしは匿名化を保証しません。掲載画像以外の撮影画像・実測データおよび認証情報は公開しません。

ソースコードの公開リンクは現在掲載していません。

[プロジェクト一覧](/projects/) · [実務経験](/experience/)
