# coding-agent-train
Repo for Coding Agent test

## install coding agents
### cline
- https://code.claude.com/docs/ja/overview#macos%2Flinux
```

```

# メモ
- skill
    - 繰り返し呼び出せる一連の手続き、必要なリソースのまとまり
- sub agent
    - メインのAgentから呼び出せる独立した処理
    - コンテキストがメインのAgentから分離されているので、コンテキストの節約ができる
- 違い
    - Skillが関数、Sub Agentが別プロセスのイメージ
- hook関連の資料
    - https://code.claude.com/docs/en/hooks-guide
    - https://code.claude.com/docs/en/hooks
    - https://code.claude.com/docs/en/hooks#security-considerations
        - セキュリティのリファレンス
    - https://code.claude.com/docs/en/hooks#hook-output
        - stdout, stderrの取り扱い
    - コマンドをバックグラウンド実行する場合は、stdout, stderrを破棄した上でバックグラウンド実行する（なんで？）

# 資料
- https://code.claude.com/docs/en/settings
    - 設定の一覧
- https://www.claude.com/blog
    - blog

# ToDo
- clineでのpermissionの掛け方
    - https://code.claude.com/docs/en/security を読む
    - https://code.claude.com/docs/en/iam#permission-system を読む
- subagentについて調べる
    - https://code.claude.com/docs/en/sub-agents
- Skillについて調べる
    - https://code.claude.com/docs/ja/skills
- hookについて調べる
    - https://code.claude.com/docs/en/hooks-guide
    - https://code.claude.com/docs/en/hooks
    - https://code.claude.com/docs/en/hooks#security-considerations
        - セキュリティのリファレンス
    - https://code.claude.com/docs/en/hooks#hook-output
    - 起動時、任意のプロセスが動いているか確認する方法