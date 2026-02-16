# Multi-Agent Development System

Claude Code の Agent Teams を活用したマルチエージェント開発フローです。
PdM（プロダクトマネージャー）が Team Lead として、複数の専門エージェントを編成して開発を進めます。

## 前提条件

- [Claude Code](https://docs.anthropic.com/en/docs/claude-code) がインストール済みであること
- Pencil MCP サーバーが設定済みであること（画面デザイン機能を使う場合）

## セットアップ

```bash
git clone git@github.com:kenchasonakai/claude_multi_agent_sample.git
cd claude_multi_agent_sample
```

Agent Teams の有効化は `.claude/settings.json` に設定済みです。追加の設定は不要です。

## 使い方

プロジェクトディレクトリで Claude Code を起動するだけです。

```bash
claude
```

起動すると PdM（プロダクトマネージャー）として振る舞います。
あとは作りたいものを伝えてください。

### 会話の例

```
あなた: ユーザーのプロフィール画像アップロード機能を作りたい

PdM:  要件を整理させてください。
      - 対象: ログイン済みユーザー
      - 画像形式: JPEG, PNG
      - サイズ上限: 5MB
      この理解で合っていますか？

あなた: はい

PdM:  要件を確定しました。画面デザインを作成します。
      （designer エージェントがデザインを作成）
      こちらのデザインで進めてよいですか？
      （スクリーンショットが表示される）

あなた: ボタンをもう少し大きくして

PdM:  修正しました。こちらでいかがですか？
      （修正後のスクリーンショット）

あなた: OK

PdM:  技術設計を進めます。
      （architect エージェントが設計）
      ...以降、各フェーズで確認を挟みながら進行
```

## 開発フロー

各フェーズでユーザーの承認を得てから次に進みます。

```
Phase 1: 要件定義        PdM がユーザーと対話して要件を固める
    ↓ 承認
Phase 2: 画面デザイン     designer が Pencil でデザインを作成（UI がある場合）
    ↓ 承認（スクリーンショットで確認）
Phase 3: 技術設計        architect がデザインを踏まえて技術設計
    ↓ 承認
Phase 4: タスク分解       pjm が実装タスクを分解
    ↓ 承認
Phase 5: 実装            engineer が並列で実装
    ↓
Phase 6: セキュリティレビュー  security が OWASP Top 10 基準でチェック
    ↓
Phase 7: 完了報告        PR をユーザーに提示
```

UI を伴わない機能の場合、Phase 2 はスキップされます。
小規模な変更では一部のフェーズが省略されることもあります。

## エージェント構成

| エージェント | 役割 | 担当フェーズ |
|---|---|---|
| **PdM** | ユーザー窓口、全体統括 | 全フェーズ（Team Lead） |
| **designer** | Pencil で画面デザイン作成 | Phase 2 |
| **architect** | 技術設計、ADR 作成 | Phase 3 |
| **pjm** | タスク分解、並列実行計画 | Phase 4 |
| **engineer** | 実装、テスト（複数人並列可） | Phase 5 |
| **security** | セキュリティレビュー | Phase 6 |

全エージェントが毎回使われるわけではありません。タスクの規模に応じて PdM が判断します。

## ディレクトリ構成

```
.
├── CLAUDE.md                      # PdM の役割 + プロジェクト共通ルール
├── .claude/
│   ├── settings.json              # Agent Teams 有効化
│   ├── team-guide.md              # チーム編成ガイドライン
│   ├── report-format.md           # ユーザーへの報告フォーマット
│   └── workflow.md                # ワークフロー詳細（Phase 1-7）
├── .agent-docs/
│   ├── features/                  # フィーチャー単位の成果物
│   │   └── {NNN-feature-name}/
│   │       ├── requirements.md    # 要件定義
│   │       ├── designs/           # 画面デザイン（.pen ファイル）
│   │       ├── design.md          # 技術設計書
│   │       ├── tasks.md           # タスク分解
│   │       └── reviews/           # セキュリティレビュー結果
│   └── decisions/                 # ADR（技術判断の記録）
│       └── {NNN-title}.md
└── src/                           # ソースコード
```

## カスタマイズ

| 変えたいこと | 編集するファイル |
|---|---|
| エージェントの役割を追加・変更 | `.claude/team-guide.md` |
| ワークフローの手順を変更 | `.claude/workflow.md` |
| 報告フォーマットを変更 | `.claude/report-format.md` |
| 技術スタック・コーディング規約 | `CLAUDE.md` |
| PdM の振る舞いを調整 | `CLAUDE.md` |
