# チーム編成ガイドライン

## 基本チーム構成

タスクに応じて以下の役割から必要な Teammate を選んで編成する。
全員を毎回出す必要はない。タスクの規模に合わせて最小構成にする。

---

### architect（設計）

- 技術的な設計判断を行う
- 既存コードベースとの整合性を確認する
- `.agent-docs/decisions/` の既存 ADR を読んでから設計する
- 成果物: `.agent-docs/features/{NNN}/design.md`
- 出力に含めること:
  - 設計概要
  - 技術選定と理由（却下案も含む）
  - 変更影響範囲
  - リスク・懸念
- 重要な技術判断は `.agent-docs/decisions/` に ADR を作成する

### designer（画面デザイン）

- Pencil MCP ツールを使って画面デザインを作成する
- architect の設計とユーザー要件に基づいてデザインする
- 成果物: `.agent-docs/features/{NNN}/designs/{screen-name}.pen`
- 作業手順:
  1. `requirements.md` と `design.md` を読む
  2. デザインガイドラインを取得する（get_guidelines, get_style_guide_tags, get_style_guide）
  3. 画面を設計する（batch_design）
  4. スクリーンショットを取得して仕上がりを確認する（get_screenshot）
  5. 問題があれば修正する
- デザインに含めること:
  - 主要画面のレイアウト
  - コンポーネント構成
  - レスポンシブ対応（必要な場合）
- PdM がスクリーンショットをユーザーに提示してデザイン承認を得る

### pjm（タスク分解）

- architect の設計をもとに実装タスクを分解する
- 各タスクの依存関係を整理する
- PR 単位をレビュー可能なサイズ（差分 300 行以下目安）に保つ
- 成果物: `.agent-docs/features/{NNN}/tasks.md`
- 出力に含めること:
  - タスク一覧（タスク名、PR 名、依存関係、推定差分行数）
  - 並列実行計画（どのタスクを同時着手できるか）
  - 実行順序と理由

### engineer（実装）

- 指示されたタスクを実装する
- テストを書く
- 指示されたスコープ以外のコードは変更しない
- 複数人を同時に立てて並列実装が可能
- 作業手順:
  1. `.agent-docs/features/{NNN}/tasks.md` から担当タスクを確認
  2. ブランチを切る（`feat/{feature-name}-step{N}`）
  3. 実装 + テスト
  4. commit + push
- ファイル競合を避けるため、複数 engineer が同じファイルを同時編集しない

### security（セキュリティレビュー）

- 実装済みコードを OWASP Top 10 基準でレビューする
- 成果物: `.agent-docs/features/{NNN}/reviews/{pr-name}.md`
- チェック項目:
  - SQL インジェクション
  - XSS
  - 認証・認可の不備
  - 機密情報のハードコード
  - 入力バリデーション
  - 依存パッケージの脆弱性
- 出力: 総合判定（問題なし / 軽微 / ブロッカー）+ 指摘事項 + 推奨対応

---

## 編成パターン

### 小規模（単機能追加、1ファイル程度の変更）

```
engineer 1名
```

設計不要なシンプルな変更。PdM が直接 engineer に指示。

### 中規模（複数ファイル変更、新しい技術判断を含む）

```
architect + engineer 1-2名
```

architect が設計 → PdM がタスク分解 → engineer が実装。

### 中規模 + UI（画面を伴う変更）

```
architect + designer + engineer 1-2名
```

architect が設計 → designer がデザイン → ユーザー承認 → engineer が実装。

### 大規模（新機能開発、多数のファイル変更）

```
architect + designer + pjm + engineer 2-3名 + security
```

フルフロー。architect が設計、designer がデザイン、pjm がタスク分解、engineer が並列実装、security がレビュー。

---

## Teammate 共通ルール

- model は opus を使う
- 作業開始前に `.agent-docs/features/{NNN}/` 内の関連ファイルを読む
- 成果物は必ず `.agent-docs/features/{NNN}/` に書き出す
- 他の Teammate が作成したファイルを上書きしない（読み取りのみ）
- 同じソースファイルを複数 Teammate が同時編集しない
- 日本語でコミュニケーションする
