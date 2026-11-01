# Code as Policies の蒸留：Gemini を教師にして小型モデルにロボット制御コードを書かせる

PAI 最終課題のコード一式です。

[Code as Policies](https://code-as-policies.github.io/)（Liang et al., 2022）の卓上マニピュレーションデモ（PyBullet 上の UR5e アームと 2 指グリッパー）を題材に、次の 2 つに取り組みました。

1. **Gemini API 版 CaP**：提供が終わった OpenAI `text-davinci-003` の代わりに Gemini でロボット制御コードを生成する
2. **CaP の蒸留**：Gemini 版 CaP を教師として成功したコードだけを自動で集め、小型のコード生成モデル（Qwen2.5-Coder-Instruct）を LoRA でファインチューニングする。生徒は few-shot 例なしの短い入力からコードを書く

## ノートブック

| ノートブック | 内容 | ランタイム | Colab |
|---|---|---|---|
| `Code_as_Policies_Interactive_Demo.ipynb` | 公式デモ（参照用・OpenAI 版） | CPU | [開く](https://colab.research.google.com/github/Rinnnnnnnnnnnnnnnnnnnnn/PAI_final/blob/main/Code_as_Policies_Interactive_Demo.ipynb) |
| `Code_as_Policies_Gemini_JP.ipynb` | Gemini API 版 CaP（日本語解説つき） | CPU | [開く](https://colab.research.google.com/github/Rinnnnnnnnnnnnnnnnnnnnn/PAI_final/blob/main/Code_as_Policies_Gemini_JP.ipynb) |
| `CaP_Distillation_Gemini_Teacher_JP.ipynb` | 教師データ作成（Part A）・LoRA 学習（B）・評価（C）・デモ（D）・結果まとめ（E） | A: CPU / B〜E: GPU | [開く](https://colab.research.google.com/github/Rinnnnnnnnnnnnnnnnnnnnn/PAI_final/blob/main/CaP_Distillation_Gemini_Teacher_JP.ipynb) |
| `CaP_Student_Demo_Video.ipynb` | 学習済み生徒モデルでタスクを実行し、提出用動画（成否・生成コードつき）を作成 | GPU | [開く](https://colab.research.google.com/github/Rinnnnnnnnnnnnnnnnnnnnn/PAI_final/blob/main/CaP_Student_Demo_Video.ipynb) |

## 実行の流れ

1. Colab のシークレットに `GEMINI_API_KEY` を登録する（[Google AI Studio](https://aistudio.google.com/apikey) で発行）
2. `CaP_Distillation_Gemini_Teacher_JP.ipynb` を **CPU ランタイム**（`RUN_CPU_PART = True`）で実行し、教師データを作る
3. 同じノートブックを **GPU ランタイム**（`RUN_CPU_PART = False` / `RUN_GPU_PART = True`）で実行し、学習・評価する
4. `CaP_Student_Demo_Video.ipynb` を GPU ランタイムで実行し、動画を作る

データ・学習済みアダプタ・結果・動画はすべて Google Drive の `MyDrive/cap_distill/` に保存されます。

## 主な工夫

- システム指示と出力の後処理で、チャット型の Gemini を補完型 LLM と同じように使う
- 9 種類のテンプレートで、指示文と成功判定関数を自動で作る（人手のラベル付けなし）
- 無限ループを防ぐシミュレーションのタイムアウトと配置の事前チェック、API のレート制限への対応と途中再開
- seed を固定して教師と生徒を同じシーンで比較し、エピソード単位で学習／検証を分割する
- 学習済みアダプタの設定からベースモデルを自動で判別して読み込み、提出用動画を自動で作る

## 提出物

- 説明資料：`report/`
- 動画（YouTube）：https://youtu.be/kXr9rSQjUgc （2 倍速再生）

## ライセンス

元のデモコードは Google LLC による Apache License 2.0 のコードです（各ノートブック冒頭の表記を参照）。
