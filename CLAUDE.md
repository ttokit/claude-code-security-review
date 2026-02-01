# Claude Code Security Review

OAuth ベースのセキュリティレビュー GitHub Action。

## アーキテクチャ

このアクションは `anthropics/claude-code-action@v1` をラップし、セキュリティレビュー専用のプロンプトを提供する。

### ファイル構成

```
action.yml                           # メインアクション定義
.github/workflows/sast.yml           # デモ/テスト用ワークフロー
.claude/commands/security-review.md  # ローカル CLI 用
```

## action.yml の構成

### 入力パラメータ

- `claude_code_oauth_token`: Claude Code の OAuth トークン（必須）
- `model`: 使用する Claude モデル（デフォルト: Opus 4.5）

### 許可ツール

- `mcp__github__create_pull_request_review_comment`: インラインコメント用
- `Bash(gh pr diff:*)`, `Bash(gh pr view:*)`: PR 情報取得
- `Bash(gh pr comment:*)`: サマリコメント投稿
- `Read`, `Glob`, `Grep`, `LS`, `Task`: ファイル探索

## セキュリティレビュープロンプトの設計意図

### 偽陽性削減

プロンプトには詳細なハード除外リストと precedents（判例）を含む。これにより：

1. DoS、レート制限、メモリ消費などの低インパクト問題を自動除外
2. React の XSS、クライアントサイド SSRF など誤検知しやすいパターンを除外
3. confidence 0.7 未満の発見は報告しない

### 出力形式

- **インラインコメント**: 脆弱性のあるコード行に直接コメント
- **サマリコメント**: PR 全体への発見数サマリ（発見なし時も投稿）

## 開発ガイドライン

- プロンプト変更時は既存の精度を維持するよう注意
- 新しい除外パターン追加時は HARD EXCLUSIONS または PRECEDENTS に追記
