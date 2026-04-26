# ex7の課題

## 課題の概要

本課題では，OpenSTARLab の `preprocessing` と `rlearn` を用いて，FIFA World Cup 2022 のイベントデータ・トラッキングデータから強化学習モデルを作成する．
サッカーの状態を SAR 形式に前処理し，観測データを作成したうえで，行動価値を表すQ値を学習・可視化することを目的とする．

対象ノートブック：

- `5.6_Soccer_Reinforcement_Learning_OpenSTARLab.ipynb`

## 課題

1. FIFA World Cup 2022 データを前処理し，学習に使う observation データを作成する
2. 複数場面のQ値を可視化し，どの行動が高く評価されているか説明する
3. class weights の有無や学習条件を変更し，Q値や評価結果の変化を比較する
4. 強化学習の状態・行動・報酬・Q値を，実際のプレー文脈と対応づけて考察する（発展）

## 課題の進め方

1. ノートブックの処理を確認する
   - Python 3.10 の環境を作成する
   - `openstarlab`，`preprocessing`，`rlearn` などの必要パッケージをインストールする
   - FIFA World Cup 2022 のデータを `/content/data` に配置する
   - `openstarlab-preprocessing` で SAR 形式の前処理を行う
   - `openstarlab-rlearn` でデータ分割，observation 作成，学習，推論を行う
   - 学習したモデルを使ってQ値を可視化する

2. 課題1：データ前処理と observation 作成
   - FIFA World Cup 2022 のイベントデータ，トラッキングデータ，メタデータ，ロスターデータを確認する
   - `preprocessing_fifawc2022.py` を実行し，SAR形式の前処理データを作成する
   - `split_data_fifawc2022.py` を実行し，train，validation，test に分割する
   - `observation_fifawc2022.py` を実行し，強化学習で使う observation データを作成する
   - 作成された observation の行数，列数，主な特徴量を確認する
   - train，validation，test の match_id が混ざっていないか確認する

   ノートブックでは，以下の match_id を対象に前処理している：

   ```text
   3812
   3813
   3814
   ```

   前処理では，以下の設定が使われている：

   ```python
   data_provider = "fifawc"
   state_def = "PVS"
   preprocess_method = "SAR"
   ```

   レポートでは，以下を説明する：

   - どのデータを入力として使ったか
   - SAR形式に変換する目的
   - train，validation，test の分割結果
   - observation のサンプルを1つ選び，状態と行動が何を表すか
   - observation データが強化学習で何を表しているか

3. 課題2：Q値の可視化・理解
   - `train_fifawc2022_class_weight_only.py` を実行し，class weights を計算する
   - `train_fifawc2022_with_cached_class_weight.py` を実行し，強化学習モデルを学習する
   - `visualize_qvalues.py` を実行し，少なくとも3場面のQ値を可視化する
   - 可視化されたQ値から，どの行動が高く評価されているかを説明する
   - 各場面について，最もQ値が高い行動と2番目に高い行動の差を比較する
   - Q値が高い行動が，ボール位置，味方・相手の配置，ゴール方向と整合しているか確認する

   ノートブックでは，以下のような可視化設定が使われている：

   ```python
   match_id = "3814"
   sequence_id = 0
   viz_style = "bar"
   keep_frames = True
   ```

   可視化結果の例として，以下の画像を表示している：

   ```text
   /content/output/frames/3814_0_Homam Ahmed/frame_0365.png
   ```

   レポートでは，以下を説明する：

   - 可視化した match_id，sequence_id，frame
   - Q値が高い行動と，2番目に高い行動
   - 1位と2位のQ値差が大きい場面・小さい場面
   - その場面で，なぜその行動の価値が高いと考えられるか
   - サッカーの文脈で自然な判断になっているか

4. 課題3：学習条件・可視化対象の比較
   - 元の設定と，自分で変更した設定を比較する
   - 少なくとも2条件を変更して比較する
   - 変更前後で，学習結果やQ値の可視化がどう変化するか確認する
   - 同じ場面に対して，変更前後のQ値ランキングを比較する
   - class weights を使う場合と使わない場合を比較する
   - 少なくとも1つ，結果が改善しなかった設定も取り上げる

   比較に使いやすい設定の例：

   - `max_epochs`
   - learning rate
   - `datamodule.batch_size`
   - `match_id`
   - `sequence_id`
   - `viz_style`
   - class weights を使う場合と使わない場合

   ノートブックでは，設定ファイル `/content/config/exp_fifawc2022.json` を読み込み，以下のような値を変更している：

   ```python
   config_data["max_epochs"] = 1
   config_data["model"]["optimizer"]["lr"] = 0.01
   config_data["datamodule"]["type"] = "rl_attacker"
   ```

   比較例：

   - `max_epochs=1` vs `max_epochs=5`
   - learning rate `0.01` vs `0.001`
   - class weights あり vs class weights なし
   - match_id `3814` の別 sequence のQ値比較
   - 同じ場面に対する棒グラフ表示と別の可視化形式の比較

5. 発表に向けて整理する
   - 強化学習における「状態」「行動」「報酬」「Q値」が，この課題では何に対応するか説明する
   - 前処理からQ値可視化までの流れを図や箇条書きで整理する
   - 可視化した場面で，モデルがどの行動を高く評価したか説明する
   - 設定変更によって結果がどう変化したか考察する
   - 結果が不自然だった場面があれば，データ，報酬設計，学習不足のどれが原因らしいか考察する

## レポートに含める内容

1. 課題1：前処理・observation 作成
   - 使用した match_id
   - 前処理後のデータ保存先
   - train，validation，test の作成結果
   - observation データの行数，列数，主な特徴量
   - observation データの役割

2. 課題2：Q値の可視化
   - 可視化した match_id，sequence_id，frame（少なくとも3場面）
   - Q値の可視化画像
   - Q値が高い行動と2番目に高い行動
   - Q値ランキングとプレー状況をまとめた表
   - その行動がサッカーの文脈で妥当かどうかの考察

3. 課題3：学習条件・可視化対象の比較
   - 変更した設定（少なくとも2条件）
   - 比較した結果
   - Q値や可視化結果の違い
   - どの条件が良かったか，その理由
   - 改善しなかった設定と，その理由の考察

4. 発展：強化学習としての考察
   - 状態，行動，報酬，Q値がこの課題で何に対応するか
   - モデルの判断がサッカーの文脈で自然だった場面
   - モデルの判断が不自然だった場面
   - 改善するなら，特徴量，報酬，データ量，モデルのどこを変えるべきか

## ヒント

- Pythonを使う場合
  - 設定ファイル操作：`json`
  - パス操作：`pathlib`
  - 学習・推論：`rlearn`
  - 前処理：`preprocessing`
  - 可視化：`matplotlib`
- 主な出力先は以下の通り

  ```text
  /content/data/fifawc/preprocess_data/
  /content/data/fifawc/observation_data/
  /content/output/
  ```

- class weights は以下に保存される

  ```text
  /content/output/class_weight/exp_fifawc2022_class_weights.pt
  ```

- Q値可視化では，checkpoint のパスが必要になる

  ```text
  /content/output/sarsa_attacker/test/checkpoints/
  ```

- 可視化対象を変える場合は，`visualize_qvalues.py` の `match_id`，`sequence_id`，`tracking_file_path` を確認する
- 設定比較を行う場合は，出力先を分けて前の結果を上書きしないようにする
- 比較表には，少なくとも以下を含める

  ```text
  条件名, max_epochs, learning rate, class weights の有無, match_id, sequence_id, frame, Q値1位の行動, Q値2位の行動
  ```

- Google ColabでGPUを使う場合は，ランタイムをT4 GPUに変更する

## 注意

- FIFA World Cup 2022 データは各自で配布元から入手し，利用条件を確認すること
- 本リポジトリにはデータ本体を同梱しない
- ノートブック内のパスは `/content/` を前提としているため，ローカル実行する場合はパスを修正すること
- GPUランタイムに切り替えるとColabの状態がリセットされる場合があるため，必要なファイルは保存しておくこと
- 学習には時間がかかるため，まずは `max_epochs=1` などの小さい設定で動作確認すること
- checkpoint 名は学習結果によって変わるため，`visualize_qvalues.py` の `checkpoint_path` を実際のファイル名に合わせること
- ランダム性の影響を受けるため，実行日時，変更した設定，使用した checkpoint を記録しておくこと
- 評価値やQ値だけでなく，実際のプレー状況を見てサッカーの文脈で考察すること
- 発展課題までできなくても，まずは前処理，observation 作成，Q値可視化までの一連の流れを完成させること
