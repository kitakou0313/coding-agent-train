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

バックグラウンド実行にした場合は親子関係が途切れる
```
        "hooks": [
          {
            "type": "command",
            "command": "PROCESS_NAME='node1'; pgrep \"$PROCESS_NAME\" > /dev/null && echo \"${PROCESS_NAME} is running\" || sleep 10 &"
          }
        ]
```

```
vscode    1454  0.0  0.1  11408  8080 pts/0    Ss   07:59   0:00          \_ /bin/bash --init-file ...
vscode    8131 66.3  5.3 74006940 428716 pts/0 Sl+  08:19   0:01          |   \_ claude
.
.
.
vscode    8243  0.0  0.0   5260  1744 pts/0    S+   08:19   0:00 sleep 10
```

### /bin/shでバックグラウンド実行する -> エンターキーの入力を待つ + stdout, stderrは親プロセス側と共有する
エンターを押すと再度入力できるように
```
vscode ➜ /workspaces/coding-agent-train (main) $ /bin/sh -c 'npx http-server &'
Starting up http-server, serving ./

http-server version: 14.1.1

http-server settings: 
CORS: disabled
Cache: 3600 seconds
Connection Timeout: 120 seconds
Directory Listings: visible
AutoIndex: visible
Serve GZIP Files: false
Serve Brotli Files: false
Default File Extension: none

Available on:
  http://127.0.0.1:8080
  http://172.17.0.2:8080
Hit CTRL-C to stop the server


vscode ➜ /workspaces/coding-agent-train (main) $ 
```
`pts/0`を共有している
```
vscode    1454  0.0  0.1  11408  8080 pts/0    Ss+  07:59   0:00          \_ /bin/bash --init-file ...
vscode    5826  0.0  0.1  11408  8120 pts/1    Ss   08:14   0:00          \_ /bin/bash --init-file ...
vscode    9812  0.0  0.0  10732  4108 pts/1    R+   08:24   0:00              \_ ps auxf
.
.
.
vscode    9747  6.4  1.2 1469708 97424 pts/0   Sl   08:24   0:00 npm exec http-server
vscode    9770  0.0  0.0   2388  1432 pts/0    S    08:24   0:00  \_ sh -c "http-server"
vscode    9771  0.9  0.7 1143312 62900 pts/0   Sl   08:24   0:00      \_ http-server
```

### stdout, steerrを/dev/nullにリダイレクトする -> こちらの入力を待つ
```
$ /bin/sh -c 'npx http-server > /dev/null 2>&1'

```

```
vscode    2750  0.0  0.1  11408  8148 pts/2    Ss   08:12   0:00          \_ /bin/bash --init-file ...
vscode    3731  0.0  0.0  10732  4108 pts/2    R+   08:14   0:00              \_ ps auxf
vscode    2166  0.1  1.0 1452532 83692 pts/1   Sl   08:09   0:00 npm exec http-server
vscode    2189  0.0  0.0   2388  1432 pts/1    S    08:09   0:00  \_ sh -c "http-server"
vscode    2190  0.0  0.7 1143288 61564 pts/1   Sl   08:09   0:00      \_ http-server
```

### stdout, steerrを/dev/nullにリダイレクトする+バックグラウンド実行する -> 待たずに終了する
```
$ /bin/sh -c 'npx http-server > /dev/null 2>&1 &'
```

```
vscode    2941  7.8  1.2 1469816 98276 pts/1   Sl   05:50   0:00 npm exec http-server
vscode    2963  0.0  0.0   2388  1432 pts/1    S    05:50   0:00  \_ sh -c "http-server"
vscode    2964  1.1  0.7 1148152 63024 pts/1   Sl   05:50   0:00      \_ http-server
```


## 推測メモ
- shの実装として、バックグラウンドプロセスのstdout, stderrを待つ
    - stdout, stderrに何が接続されているのかを管理しており、子プロセスから送信されるものがなくなるまで実行される