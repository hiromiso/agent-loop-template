# agent-loop-template

Coding agentに、
仕様の確認、タスク分解、実装、テスト、レビュー、修正、
commit / pushまでを自律的に繰り返させるための
GitHub Template Repositoryです。

CodexやClaude Codeなどのcoding agentで、
ある程度長時間の自律開発を行うことを想定しています。

このテンプレートでは、
プロジェクト固有の仕様と、
agentの開発手順・停止条件を分離して管理します。

## ファイル構成

```text
.
├── .gitignore
├── AGENTS.md
├── SPEC.md
├── TASK.md
├── WORKFLOW.md
└── examples
    └── smoke-test
        └── SPEC.md
```

各ファイルの役割は以下のとおりです。

### AGENTS.md

agentが守る基本方針を定義します。

主に以下を扱います。

- 正本となるファイル
- 親agentとsubagentの責任
- delegationの方針
- 品質方針
- Git運用
- セキュリティ
- 人間へ判断を求める条件
- 最終応答と停止マーカー

通常はプロジェクトごとに大きく変更する必要はありません。

### SPEC.md

プロジェクト固有の仕様を記述します。

新しいプロジェクトでは、
このファイルを実際の開発対象に合わせて書き換えます。

主に以下を記述します。

- 概要
- 機能要件
- 技術要件
- 品質要件
- 完了条件

### TASK.md

開発タスクと進捗を管理します。

初期状態では具体的なタスクを持たず、
agentが `SPEC.md` とrepositoryの状態を確認して
必要なタスクを生成することを想定しています。

### WORKFLOW.md

agentが自律開発を進めるための
具体的な開発ループを定義します。

主に以下を扱います。

- 初期調査
- タスク選択
- 実装計画
- テスト計画
- 実装
- deterministic verification
- independent review
- test gap analysis
- failure recovery
- decision gate
- commit / push
- milestone verification
- 停止条件
- 最終報告

### examples/smoke-test/SPEC.md

このテンプレート自体が正常に機能するか確認するための
smoke test用仕様です。

逆ポーランド記法（RPN）のCLI電卓を、
agentに自律的に実装させる仕様になっています。

外部APIや外部データを必要とせず、
比較的小規模ながら、

- タスク分解
- CLI実装
- 状態を持つ対話モード
- エラー処理
- rollback
- pytest
- ruff
- mypy
- README作成
- review
- commit / push

まで一通り確認できます。

## 新しいプロジェクトを作る

GitHub上でこのrepositoryを開き、

`Use this template`

から新しいrepositoryを作成します。

ForkではなくTemplate Repositoryとして作成することで、
元repositoryとは独立した新しいGit履歴を持つrepositoryになります。

repositoryを作成したらcloneします。

例:

```bash
git clone git@github.com:USER/PROJECT.git
cd PROJECT
```

次に、ルートの `SPEC.md` を
実際に作りたいプロジェクトの仕様へ書き換えます。

必要に応じて `.gitignore` なども
プロジェクト固有の内容へ調整します。

`AGENTS.md`、`TASK.md`、`WORKFLOW.md` は、
特別な理由がなければそのまま利用できます。

仕様を記述したらcommitします。

```bash
git add .
git commit -m "Define initial project specification"
git push
```

その後、coding agentへ開発を依頼します。

## agentへ開発を開始させる

agentには、
`AGENTS.md`、`SPEC.md`、`TASK.md`、`WORKFLOW.md`
を正本として扱わせます。

例えば以下のように依頼します。

```text
/goal AGENTS.md、SPEC.md、TASK.md、WORKFLOW.mdをすべて読み、
それらを正本としてこのプロジェクトを完成させてください。

まず現在のrepositoryの状態と仕様を確認し、
SPEC.mdを満たすために必要な作業をTASK.mdへ整理してください。

その後はWORKFLOW.mdに従い、
実装、テスト、検証、独立レビュー、test gap analysis、必要な修正、
TASK.mdの更新、commit、pushまで自律的に進めてください。

各タスクの完了条件を実際の動作とテストで確認し、
単にテストが通ったことだけを完成の根拠にしないでください。

WORKFLOW.mdで定義された停止条件に到達するまで、
人間への途中確認を求めず自律的に作業を継続してください。

停止する場合は、AGENTS.mdおよびWORKFLOW.mdで定義された
停止マーカーの形式を厳密に守ってください。
```

利用しているagentに、
長期タスクやgoalを保持する機能がある場合は、
その機能と組み合わせて使用します。

## Smoke test

### Smoke testとは

Smoke testは、
詳細な機能検証を行う前に、
システム全体が最低限正常に動作するか確認する試験です。

このrepositoryでは、
RPN電卓の小規模な開発をagentへ実行させることで、

```text
SPEC確認
  ↓
TASK生成
  ↓
実装
  ↓
テスト
  ↓
レビュー
  ↓
修正
  ↓
commit / push
  ↓
milestone verification
  ↓
[MILESTONE COMPLETE]
```

という自律開発ループ全体が
正常に機能するか確認します。

RPN電卓そのものを作ることが
smoke testの主目的ではありません。

### Smoke test用repositoryを作る

まず、このTemplate Repositoryから
テスト用repositoryを新しく作成します。

GitHub上で、

`Use this template`

を選択し、
例えば以下のような名前でrepositoryを作成します。

```text
agent-loop-smoke-test
```

作成したrepositoryをcloneします。

```bash
git clone git@github.com:USER/agent-loop-smoke-test.git
cd agent-loop-smoke-test
```

smoke test用SPECをルートのSPECへコピーします。

```bash
cp examples/smoke-test/SPEC.md SPEC.md
```

変更をcommitしてpushします。

```bash
git add SPEC.md
git commit -m "Configure RPN calculator smoke test"
git push
```

これでagentへ渡す準備が完了します。

### Smoke testを実行する

coding agentをrepositoryのルートで起動し、
通常の開発と同じように自律開発を依頼します。

例:

```text
AGENTS.md、SPEC.md、TASK.md、WORKFLOW.mdをすべて読み、
それらを正本としてこのプロジェクトを完成させてください。

SPEC.mdを満たすために必要な作業をTASK.mdへ整理し、
WORKFLOW.mdに従って実装、検証、レビュー、修正、
commit、pushまで自律的に進めてください。

WORKFLOW.mdで定義された停止条件に到達するまで
自律的に作業を継続してください。
```

正常に最後まで進めば、
最終応答は以下から始まります。

```text
[MILESTONE COMPLETE]
```

途中で人間の判断や外部リソースが必要になった場合は、
`WORKFLOW.md` で定義された別の停止マーカーで停止します。

### Smoke test後

smoke test用repositoryは
テンプレートそのものとは独立しています。

そのため、
テスト中に生成されたソースコード、TASK.mdの更新、
commit履歴などが
元の `agent-loop-template` に混入することはありません。

smoke testが不要になれば、
テスト用repositoryは削除して構いません。

## 停止マーカー

現在、以下の停止マーカーを定義しています。

```text
[DECISION REQUIRED]
[BLOCKED]
[SECURITY APPROVAL REQUIRED]
[USAGE LIMIT PAUSE]
[MILESTONE COMPLETE]
```

停止時には、
親agentの最終応答の先頭行へ
対応するマーカーを出力します。

この形式は、
外部通知システムなどから
agentの停止状態を機械的に検出する用途も想定しています。

## 設計方針

このテンプレートでは、
テスト件数やtoken使用量を減らすこと自体を目的とせず、
完成した成果物の品質を優先します。

一方で、
不要なsubagent利用や、
根拠のない試行錯誤を繰り返すことも目的としていません。

問題が発生した場合は、
観測された事実をもとに仮説や設計を見直し、
根拠のある次の手段が存在する限り開発を継続します。

人間の判断が本当に必要な場合のみ、
定義された停止条件で作業を停止します。

