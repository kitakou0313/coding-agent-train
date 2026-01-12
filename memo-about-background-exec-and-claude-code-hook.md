# hook周りの調査
Q. なぜClaude codeのhookは実行されたコマンドがバックグラウンドで実行されていても完了を待つのか？

## 前提
### Claude Codeはhookのコマンドをシェルを介して実行している
```
        "hooks": [
          {
            "type": "command",
            "command": "PROCESS_NAME='node1'; pgrep \"$PROCESS_NAME\" > /dev/null && echo \"${PROCESS_NAME} is running\" || sleep 10 "
          }
        ]
```

```
vscode    1454  0.0  0.1  11408  8080 pts/0    Ss   07:59   0:00          \_ /bin/bash --init-file ...
vscode    6790 59.5  5.4 74076644 438340 pts/0 Sl+  08:15   0:01          |   \_ claude
vscode    6903  0.0  0.0   2384  1432 pts/0    S+   08:15   0:00          |       \_ /bin/sh -c PROCESS_NAME='node1'; pgrep "$PROCESS_NAME" > /dev/null && echo "${PROCESS_NAME} is running" || sleep 10 
vscode    6907  0.0  0.0   5260  1740 pts/0    S+   08:15   0:00          |           \_ sleep 10
```

### /bin/shでバックグラウンド実行したとき、stdout, stderrは

### 


## 推測メモ
- 子プロセス全てのshの実装としてバックグラウンドプロセスのstdout, stderrを待つ