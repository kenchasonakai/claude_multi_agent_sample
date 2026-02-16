# ワークフロー詳細

PdM が各フェーズを進行する際の詳細な手順。

---

## Phase 1: 要件定義（PdM <-> ユーザー）

1. ユーザーから機能要望を聞く
2. 要件を整理し、不明点があれば質問する
3. `.agent-docs/features/{NNN-name}/` ディレクトリを作成する
   - 番号は既存の最大値 + 1（初回は 001）
4. `requirements.md` を作成する
5. report-format.md の「要件確認」フォーマットでユーザーに提示する
6. ユーザーの承認を得る（修正があれば反映してから再提示）

---

## Phase 2: 画面デザイン（designer）

### 前提
- Phase 1 でユーザー承認済みであること
- UI を伴う機能の場合のみ実施（バックエンドのみの変更はスキップして Phase 3 へ）

### 手順
1. designer Teammate をスポーンする
2. designer への指示に含めること:
   - `requirements.md` のパス
   - `.agent-docs/features/{NNN}/designs/` に .pen ファイルを作成すること
   - Pencil の get_guidelines, get_style_guide_tags, get_style_guide でデザイン方針を確認すること
   - 完成後に get_screenshot でスクリーンショットを取得すること
3. designer の完了を待つ
4. .pen ファイルのスクリーンショットを取得し、デザインの品質を確認する
5. report-format.md の「デザイン報告」フォーマットでユーザーに提示する
   - スクリーンショットをユーザーに見せて承認を得る
6. ユーザーのフィードバックがあれば designer に修正を依頼する
7. ユーザーの承認を得る

---

## Phase 3: 技術設計（architect）

### 前提
- Phase 2 でユーザー承認済みであること（UI がない場合は Phase 1 承認済み）

### 手順
1. architect Teammate をスポーンする
2. architect への指示に含めること:
   - `requirements.md` のパス
   - デザインがある場合は .pen ファイルのパス（画面構成を踏まえて設計するため）
   - `.agent-docs/decisions/` を読んで既存の技術判断と整合を取ること
   - `design.md` に成果物を書き出すこと
3. architect の完了を待つ
4. `design.md` の内容を確認する
5. 重要な新しい技術判断があれば、architect に `.agent-docs/decisions/` へ ADR を作成させる
6. report-format.md の「設計報告」フォーマットでユーザーに提示する
7. ユーザーの承認を得る

---

## Phase 4: タスク分解（pjm）

### 前提
- Phase 3 でユーザー承認済みであること

### 手順
1. pjm Teammate をスポーンする
2. pjm への指示に含めること:
   - `requirements.md` と `design.md` のパス
   - デザインがある場合は .pen ファイルのパスも含める
   - `tasks.md` に成果物を書き出すこと
   - 各タスクの推定差分行数を 300 行以下に保つこと
3. pjm の完了を待つ
4. `tasks.md` の内容を確認する
5. report-format.md の「タスク分解報告」フォーマットでユーザーに提示する
6. ユーザーの承認を得る

---

## Phase 5: 実装（engineer x N）

### 前提
- Phase 4 でユーザー承認済みであること

### 手順
1. `tasks.md` の並列実行計画に従い、同時着手可能なタスクの engineer をスポーンする
2. 各 engineer への指示に含めること:
   - 担当タスクの番号と内容
   - `design.md` のパス（設計意図の理解のため）
   - デザインがある場合は .pen ファイルのパス（画面実装の参照用）
   - ブランチ名: `feat/{feature-name}-step{N}`
   - commit + push まで行うこと
3. ファイル競合を避けるため、同じファイルを触るタスクは逐次実行する
4. 各 engineer の完了を確認する
5. 依存タスクが解放されたら、次の engineer をスポーンする
6. report-format.md の「実装進捗」フォーマットで都度ユーザーに報告する

---

## Phase 6: セキュリティレビュー（security）

### 前提
- Phase 5 の実装が完了していること

### 手順
1. security Teammate をスポーンする
2. security への指示に含めること:
   - 対象の PR / ブランチ名
   - `git diff main..{branch}` で差分を確認すること
   - `.agent-docs/features/{NNN}/reviews/` にレビュー結果を書き出すこと
3. security の完了を待つ
4. ブロッカーがあれば engineer に修正を依頼する（Phase 5 に戻る）
5. report-format.md の「セキュリティレビュー結果」フォーマットでユーザーに報告する

---

## Phase 7: 完了報告（PdM -> ユーザー）

1. 全 PR が作成済みであることを確認する
2. 全テストが通っていることを確認する
3. report-format.md の「完了報告」フォーマットでユーザーに報告する
4. ユーザーに PR レビューを依頼する

---

## .agent-docs/ ファイル運用ルール

### ディレクトリ構造

```
.agent-docs/
├── features/
│   └── {NNN-feature-name}/
│       ├── requirements.md     # PdM が作成
│       ├── design.md           # architect が作成
│       ├── designs/            # designer が作成
│       │   └── {screen-name}.pen
│       ├── tasks.md            # pjm が作成
│       └── reviews/
│           └── {pr-name}.md    # security が作成
└── decisions/
    └── {NNN-title}.md          # architect が作成（ADR）
```

### ファイルヘッダ

全ての成果物ファイルには以下のヘッダを含める:

```
- 作成者: {役割名}
- 作成日: YYYY-MM-DD
- 関連フィーチャー: {NNN-feature-name}
```

### ADR フォーマット

```
# ADR-{NNN}: {タイトル}

- 日付: YYYY-MM-DD
- ステータス: 採用 / 却下 / 保留
- 関連フィーチャー: {NNN-feature-name}

## 背景
（なぜこの判断が必要か）

## 選択肢
1. ...
2. ...

## 決定
（何を選んだか、なぜか）

## 影響
（この決定が今後の設計に与える影響）
```
