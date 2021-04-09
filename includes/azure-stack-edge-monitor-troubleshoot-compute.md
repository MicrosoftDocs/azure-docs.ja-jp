---
author: alkohli
ms.service: databox
ms.topic: include
ms.date: 08/30/2020
ms.author: alkohli
ms.openlocfilehash: 92ccb6127e624ace9e719ffd23324b3a1b971f72
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "89272173"
---
コンピューティング ロールが構成されている Azure Stack Edge デバイスでは、2 つの異なるコマンド セットを利用してデバイスをトラブルシューティングしたり、監視したりできます。

- `iotedge` コマンドの使用。 これらのコマンドはデバイスの基本操作に利用できます。
- `dkrdbe` コマンドの使用。 これらのコマンドはデバイスのさまざまな操作に利用できます。

上記のコマンド セットのいずれかを実行するには、[PowerShell インターフェイスに接続する](#connect-to-the-powershell-interface)必要があります。

### <a name="use-iotedge-commands"></a>`iotedge` コマンドの使用

利用できるコマンドの一覧を表示するには、[PowerShell インターフェイスに接続し](#connect-to-the-powershell-interface)、`iotedge` 関数を使用します。

```powershell
[10.100.10.10]: PS>iotedge -?                                                                                                                                                                                                 Usage: iotedge COMMAND

Commands:
   check
   list
   logs
   restart

[10.100.10.10]: PS>
```

次の表は、`iotedge` で利用できるコマンドの説明を簡単にまとめたものです。

|command  |説明 |
|---------|---------|
|`check`     | 構成と接続に関する一般的な問題について自動チェックを実行する       |
|`list`     | モジュールの一覧を表示する         |
|`logs`     | モジュールのログを取得する        |
|`restart`     | モジュールの停止と再開         |


### <a name="use-dkrdbe-commands"></a>`dkrdbe` コマンドの使用

利用できるコマンドの一覧を表示するには、[PowerShell インターフェイスに接続し](#connect-to-the-powershell-interface)、`dkrdbe` 関数を使用します。

```powershell
[10.100.10.10]: PS>dkrdbe -?
Usage: dkrdbe COMMAND

Commands:
   image [prune]
   images
   inspect
   login
   logout
   logs
   port
   ps
   pull
   start
   stats
   stop
   system [df]
   top

[10.100.10.10]: PS>
```
次の表は、`dkrdbe` で利用できるコマンドの説明を簡単にまとめたものです。

|command  |説明 |
|---------|---------|
|`image`     | イメージを管理する。 未使用のイメージを削除するには、以下を使用します: `dkrdbe image prune -a -f`       |
|`images`     | イメージの一覧表示         |
|`inspect`     | Docker オブジェクトに関する詳細な情報を返す         |
|`login`     | Docker レジストリにサインインする         |
|`logout`     | Docker レジストリからサインアウトする         |
|`logs`     | コンテナーのログを取得する        |
|`port`     | ポート マッピングまたはコンテナーに対するある特定のマッピングを一覧表示する        |
|`ps`     | コンテナーの一覧表示        |
|`pull`     | レジストリからイメージまたはリポジトリをプルする         |
|`start`     | 停止している 1 つまたは複数のコンテナーを開始する         |
|`stats`     | コンテナーのリソース使用統計のライブ ストリームを表示する         |
|`stop`     | 実行中の 1 つまたは複数のコンテナーを停止する        |
|`system`     | Docker の管理         |
|`top`     | コンテナーの実行中のプロセスを表示する         |

利用可能なコマンドのヘルプを表示するには、`dkrdbe <command-name> --help` を使用します。

たとえば、`port` コマンドの使い方を知るには、次のように入力します。

```powershell
[10.100.10.10]: P> dkrdbe port --help

Usage:  dkr port CONTAINER [PRIVATE_PORT[/PROTO]]

List port mappings or a specific mapping for the container
[10.100.10.10]: P> dkrdbe login --help

Usage:  docker login [OPTIONS] [SERVER]

Log in to a Docker registry.
If no server is specified, the default is defined by the daemon.

Options:
  -p, --password string   Password
      --password-stdin    Take the password from stdin
  -u, --username string   Username
[10.100.10.10]: PS>
```

`dkrdbe` 関数に利用できるコマンドでは、通常の Docker コマンドに使用されるものと同じパラメーターが使用されます。 Docker コマンドと共に使用されるオプションとパラメーターについては、[Docker コマンドラインの使用](https://docs.docker.com/engine/reference/commandline/docker/)に関するページを参照してください。

### <a name="to-check-if-the-module-deployed-successfully"></a>モジュールが正常にデプロイされているかどうかを確認するには

コンピューティング モジュールは、ビジネス ロジックが実装されたコンテナーです。 コンピューティング モジュールが正常にデプロイされているかどうかを確認するには、`ps` コマンドを実行し、コンテナー (コンピューティング モジュールに対応) が実行されているかどうかを確認します。

(一時停止されているものも含め) すべてのコンテナーの一覧を表示するには、`ps -a` コマンドを実行します。

```powershell
[10.100.10.10]: P> dkrdbe ps -a
CONTAINER ID        IMAGE                                                COMMAND                   CREATED             STATUS              PORTS                                                                  NAMES
d99e2f91d9a8        edgecompute.azurecr.io/filemovemodule2:0.0.1-amd64   "dotnet FileMoveModuâ€¦"    2 days ago          Up 2 days                                                                                  movefile
0a06f6d605e9        edgecompute.azurecr.io/filemovemodule2:0.0.1-amd64   "dotnet FileMoveModuâ€¦"    2 days ago          Up 2 days                                                                                  filemove
2f8a36e629db        mcr.microsoft.com/azureiotedge-hub:1.0               "/bin/sh -c 'echo \"$â€¦"   2 days ago          Up 2 days           0.0.0.0:443->443/tcp, 0.0.0.0:5671->5671/tcp, 0.0.0.0:8883->8883/tcp   edgeHub
acce59f70d60        mcr.microsoft.com/azureiotedge-agent:1.0             "/bin/sh -c 'echo \"$â€¦"   2 days ago          Up 2 days                                                                                  edgeAgent
[10.100.10.10]: PS>
```

コンテナー イメージの作成中またはイメージのプル中にエラーが発生した場合、`logs edgeAgent` を実行します。  `EdgeAgent` は、他のコンテナーのプロビジョニングを担う IoT Edge ランタイム コンテナーです。

`logs edgeAgent` はすべてのログをダンプするため、最近のエラーを表示する方法としては、オプション `--tail 20` の利用をお勧めします。


```powershell
[10.100.10.10]: PS>dkrdbe logs edgeAgent --tail 20
2019-02-28 23:38:23.464 +00:00 [DBG] [Microsoft.Azure.Devices.Edge.Util.Uds.HttpUdsMessageHandler] - Connected socket /var/run/iotedge/mgmt.sock
2019-02-28 23:38:23.464 +00:00 [DBG] [Microsoft.Azure.Devices.Edge.Util.Uds.HttpUdsMessageHandler] - Sending request http://mgmt.sock/modules?api-version=2018-06-28
2019-02-28 23:38:23.464 +00:00 [DBG] [Microsoft.Azure.Devices.Edge.Agent.Core.Agent] - Getting edge agent config...
2019-02-28 23:38:23.464 +00:00 [DBG] [Microsoft.Azure.Devices.Edge.Agent.Core.Agent] - Obtained edge agent config
2019-02-28 23:38:23.469 +00:00 [DBG] [Microsoft.Azure.Devices.Edge.Agent.Edgelet.ModuleManagementHttpClient] - Received a valid Http response from unix:///var/run/iotedge/mgmt.soc
k for List modules
--------------------CUT---------------------
--------------------CUT---------------------
08:28.1007774+00:00","restartCount":0,"lastRestartTimeUtc":"2019-02-26T20:08:28.1007774+00:00","runtimeStatus":"running","version":"1.0","status":"running","restartPolicy":"always
","type":"docker","settings":{"image":"edgecompute.azurecr.io/filemovemodule2:0.0.1-amd64","imageHash":"sha256:47778be0602fb077d7bc2aaae9b0760fbfc7c058bf4df192f207ad6cbb96f7cc","c
reateOptions":"{\"HostConfig\":{\"Binds\":[\"/home/hcsshares/share4-dl460:/home/input\",\"/home/hcsshares/share4-iot:/home/output\"]}}"},"env":{}}
2019-02-28 23:38:28.480 +00:00 [DBG] [Microsoft.Azure.Devices.Edge.Agent.Core.Planners.HealthRestartPlanner] - HealthRestartPlanner created Plan, with 0 command(s).
```

### <a name="to-get-container-logs"></a>コンテナー ログを取得するには

特定のコンテナーのログを取得するには、まず、コンテナーを一覧表示し、次に、関心があるコンテナーのログを取得します。

1. [PowerShell インターフェイスに接続する](#connect-to-the-powershell-interface)。
2. 実行中のコンテナーの一覧を取得するには、`ps` コマンドを実行します。

    ```powershell
    [10.100.10.10]: P> dkrdbe ps
    CONTAINER ID        IMAGE                                                COMMAND                   CREATED             STATUS              PORTS                                                                  NAMES
    d99e2f91d9a8        edgecompute.azurecr.io/filemovemodule2:0.0.1-amd64   "dotnet FileMoveModuâ€¦"    2 days ago          Up 2 days                                                                                  movefile
    0a06f6d605e9        edgecompute.azurecr.io/filemovemodule2:0.0.1-amd64   "dotnet FileMoveModuâ€¦"    2 days ago          Up 2 days                                                                                  filemove
    2f8a36e629db        mcr.microsoft.com/azureiotedge-hub:1.0               "/bin/sh -c 'echo \"$â€¦"   2 days ago          Up 2 days           0.0.0.0:443->443/tcp, 0.0.0.0:5671->5671/tcp, 0.0.0.0:8883->8883/tcp   edgeHub
    acce59f70d60        mcr.microsoft.com/azureiotedge-agent:1.0             "/bin/sh -c 'echo \"$â€¦"   2 days ago          Up 2 days                                                                                  edgeAgent
    ```

3. ログが必要なコンテナーのコンテナー ID をメモします。

4. 特定のコンテナーのログを取得するには、コンテナー ID を指定した上で `logs` コマンドを実行します。

    ```powershell
    [10.100.10.10]: PS>dkrdbe logs d99e2f91d9a8
    02/26/2019 18:21:45: Info: Opening module client connection.
    02/26/2019 18:21:46: Info: Initializing with input: /home/input, output: /home/output.
    02/26/2019 18:21:46: Info: IoT Hub module client initialized.
    02/26/2019 18:22:24: Info: Received message: 1, SequenceNumber: 0 CorrelationId: , MessageId: 081886a07e694c4c8f245a80b96a252a Body: [{"ChangeType":"Created","ShareRelativeFilePath":"\\__Microsoft Data Box Edge__\\Upload\\Errors.xml","ShareName":"share4-dl460"}]
    02/26/2019 18:22:24: Info: Moving input file: /home/input/__Microsoft Data Box Edge__/Upload/Errors.xml to /home/output/__Microsoft Data Box Edge__/Upload/Errors.xml
    02/26/2019 18:22:24: Info: Processed event.
    02/26/2019 18:23:38: Info: Received message: 2, SequenceNumber: 0 CorrelationId: , MessageId: 30714d005eb048e7a4e7e3c22048cf20 Body: [{"ChangeType":"Created","ShareRelativeFilePath":"\\f [10]","ShareName":"share4-dl460"}]
    02/26/2019 18:23:38: Info: Moving input file: /home/input/f [10] to /home/output/f [10]
    02/26/2019 18:23:38: Info: Processed event.
    ```

### <a name="to-monitor-the-usage-statistics-of-the-device"></a>デバイスの使用状況統計を監視するには

デバイスのメモリ、CPU 使用率、IO を監視するには、`stats` コマンドを使用します。

1. [PowerShell インターフェイスに接続する](#connect-to-the-powershell-interface)。
2. `stats` コマンドを実行し、ライブ ストリームを無効にし、最初の結果のみをプルします。

   ```powershell
   dkrdbe stats --no-stream
   ```

   このコマンドレットの使用例を次に示します。

    ```
    [10.100.10.10]: P> dkrdbe stats --no-stream
    CONTAINER ID        NAME          CPU %         MEM USAGE / LIMIT     MEM %         NET I/O             BLOCK I/O           PIDS
    d99e2f91d9a8        movefile      0.0           24.4MiB / 62.89GiB    0.04%         751kB / 497kB       299kB / 0B          14
    0a06f6d605e9        filemove      0.00%         24.11MiB / 62.89GiB   0.04%         679kB / 481kB       49.5MB / 0B         14
    2f8a36e629db        edgeHub       0.18%         173.8MiB / 62.89GiB   0.27%         4.58MB / 5.49MB     25.7MB / 2.19MB     241
    acce59f70d60        edgeAgent     0.00%         35.55MiB / 62.89GiB   0.06%         2.23MB / 2.31MB     55.7MB / 332kB      14
    [10.100.10.10]: PS>
    ```

