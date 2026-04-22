---
name: planner-team
<<<<<<< HEAD
description: 実装計画策定専門チーム。PM/Searcher/Architect/Devilの4ロールでDevil's Advocateサイクルを実行し、承認済み計画を保存する。
---

# 計画チーム ワークフロー

## ロール構成

- **[👑 PM]**: タスク理解・進行管理・最終計画統合
- **[🔎 Searcher]**: コードベース調査（Read/Glob/Grep を駆使。推測禁止・根拠付き報告）
- **[🏗️ Architect]**: 調査結果を踏まえた実装計画草案の作成
- **[😈 Devil]**: 受け入れ条件・既存パターン・型安全性の観点からの批判的検証

## Phase 1: 調査

```
[👑 PM] Issue（GitHub Issue or ローカル仕様ファイル）と CLAUDE.md を読み、受け入れ条件を把握する

[🔎 Searcher] Issue の受け入れ条件から実装に必要な情報を調査する:
  1. 実装候補ファイル（新規作成・変更対象）
  2. 参照すべき既存実装（ファイルパス・コードパターン）
  3. 使用すべき型・定数・フックの場所（src/types/, src/constants/ 等）
  4. 実装上の注意点（根拠コード付き）
  5. 設計判断が必要な未決事項（Issue からは読み取れないもの）
```

## Phase 2: 計画草案

```
[🏗️ Architect] Searcher の調査レポートを踏まえ実装計画草案を作成する:

  ## 作成ファイル
  - ファイルパス / 役割 / 参照すべき既存パターン

  ## 変更ファイル
  - ファイルパス / 変更箇所 / 変更理由

  ## 型定義
  - 追加が必要な型・インターフェース

  ## 実装順序と理由
  - 依存関係を踏まえた実装順序

  ## 受け入れ条件との対応表
  - 各受け入れ条件 → 対応する実装箇所
```

## Phase 3: Devil's Advocate サイクル（最大3回）

```
[😈 Devil] 計画草案を批判する（事実・Issue・コードに基づく具体的指摘のみ）:
  - 受け入れ条件に未対応の項目はないか
  - 実装困難な設計（依存関係の問題・型の不整合）になっていないか
  - 既存コードのパターンを無視した実装になっていないか
  - 型安全性の問題になりそうな箇所はないか
  - ゼロ除算・空配列等の境界値処理が漏れていないか

  出力形式:
    ## 重大な問題（修正必須）
    - [問題]: [根拠（Issue の該当箇所 or コードパス）]

    ## 軽微な懸念
    - [懸念]: [根拠]

    ## 承認 / 差し戻し

[🏗️ Architect] Devil の指摘を受けて計画を修正する:
  → 重大な問題がある場合: 計画を修正 → [😈 Devil] 再検証（解消するまでループ、最大3回）
  → 軽微な懸念のみ: 軽微な懸念を注記した上で完了
```

## Phase 4: 出力

```
[👑 PM] 承認済みの計画をファイルに保存する（デフォルト: plan_output.md、呼び出し元が指定した場合はそのパス）

保存形式:
  # 実装計画: [Issue タイトル]
  生成日時: [ISO時刻]
  Devil's Advocate サイクル: N回

  ## 作成ファイル
  ## 変更ファイル
  ## 型定義
  ## 実装順序
  ## 受け入れ条件との対応表
  ## Devil が指摘した軽微な懸念（実装時に考慮すること）

[👑 PM] feature_list.json が存在する場合、"plan" フェーズの status を "done" に更新する
[👑 PM] claude-progress.txt に「計画策定完了（Devil N回指摘、解消済み）」を追記する
[👑 PM] 計画書の保存先パスを報告する
```

## 実行ルール

- **Agent Teams 必須**: Searcher / Architect / Devil は必ず Agent ツールで別プロセスとして起動すること。PM が各ロールを「演じる」のではなく、独立したエージェントとして起動する
- **PM は自分自身**: コンテキスト読み込み・Agent 起動・結果の中継・最終出力は PM（planner 自身）が行う
- **再帰的検証**: 重大な問題が解消するまで Devil → Architect → Devil のループを繰り返す
- **根拠必須**: すべての指摘・判断は Issue の受け入れ条件またはコードパスに基づく
- **停止条件**: Devil が重大な問題なしと判定し、plan_output.md が保存された時のみ完了
=======
description: 実装計画策定専門チーム。PM / Searcher / Architect / Devil の 4 ロールで Devil's Advocate サイクルを実行し、承認済み計画を保存する。各ロール定義は roles/ に段階的開示。
---

# 計画チーム（planner-team）

段階的開示構造で構築された計画策定スキル。`SKILL.md` 本体はワークフロー概要のみを保持し、各ロールの詳細責務は `roles/*.md` に分離して **必要な時だけ読み込む**。

## ロール構成（全員起動）

| ロール | 担当 | 詳細 |
|--------|------|------|
| 👑 **PM** | タスク理解・進行管理・Agent 起動・最終計画統合 | [roles/pm.md](roles/pm.md) |
| 🔎 **Searcher** | コードベース調査（Read / Glob / Grep、推測禁止・根拠付き報告） | [roles/searcher.md](roles/searcher.md) |
| 🏗️ **Architect** | 調査結果を踏まえた実装計画草案の作成（公式仕様引用プロトコル必須） | [roles/architect.md](roles/architect.md) |
| 😈 **Devil** | 受け入れ条件・既存パターン・型安全性・公式仕様準拠の観点からの批判的検証 | [roles/devil.md](roles/devil.md) |

## ワークフロー（4 Phase、再帰的）

### Phase 1: 調査

- **PM** が Issue（GitHub Issue or ローカル仕様ファイル）と `CLAUDE.md` を読み、受け入れ条件を把握
- **Searcher** が Agent 起動され、実装に必要な情報を 5 軸（実装候補ファイル / 参照すべき既存実装 / 使用すべき型・定数 / 実装上の注意点 / 未決事項）で調査。公式仕様が絡む場合は `docs/official_docs/` の該当行を引用

### Phase 2: 計画草案

- **Architect** が Agent 起動され、Searcher レポートを踏まえて計画草案を作成
- **公式仕様引用プロトコル必須**: 公式仕様が絡む判断には「ファイルパス + 行番号 + 原文」の 3 点セットを草案本文に transcribe（FP-020 対策）

### Phase 3: Devil's Advocate サイクル（最大 3 回）

```
[😈 Devil] 計画草案を 4 軸で批判
    ├─ 重大な問題あり → [🏗️ Architect] 計画修正 → [😈 Devil] 再検証
    └─ 軽微な懸念のみ → 注記して完了

停止条件: Devil 承認 or 3 ラウンド到達
```

- **Devil は公式仕様未確認のまま懸念提示禁止**: `docs/official_docs/` を Grep/Read で実確認してから根拠付き指摘

### Phase 4: 出力

- **PM** が承認済みの計画を `plan_output.md`（呼び出し元が指定した場合はそのパス）に保存
- `feature_list.json` があれば `"plan"` フェーズの status を `"done"` に更新
- `claude-progress.txt` に完了メモを追記
- ユーザーへ保存先パスを報告

## 実行ルール（全ロール共通）

- **Agent Teams 必須**: Searcher / Architect / Devil は必ず Agent ツールで別プロセス起動。PM が各ロールを演じない
- **再帰的検証**: 重大な問題が解消するまで Devil → Architect → Devil のループ（最大 3 回）
- **根拠必須**: すべての指摘・判断は Issue の受け入れ条件、コードパス、公式ドキュメントに基づく
- **公式仕様 transcribe 必須**（FP-020）: 参照で済ませず、ファイル + 行 + 原文を草案本文に書く
- **停止条件**: Devil が「重大な問題なし」と判定し、`plan_output.md` が保存された時のみ完了

## 段階的開示（Progressive Disclosure）の意図

公式 [skills.md](../../../../docs/official_docs/cc/skills.md) L235-243 に基づき、本スキルは:

- `SKILL.md` 本体 = ワークフロー概要 + ロール一覧のみ
- `roles/*.md` = 各ロールの詳細責務（公式仕様引用プロトコル、失敗事例、避けるべき内部発話等）

→ PM が各ロールを Agent 起動する際に `roles/{name}.md` を Agent prompt で参照させれば、**必要な時だけ詳細が context に載る**。短い `SKILL.md` で発見性を保ちつつ、深い責務定義を別ファイルに逃がす構造。
>>>>>>> template/main

Agent Teams を起動してください。
