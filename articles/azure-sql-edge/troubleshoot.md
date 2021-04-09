---
title: Azure SQL Edge のデプロイのトラブルシューティング
description: Azure SQL Edge をデプロイするときに発生する可能性のあるエラーについて説明します
keywords: SQL Edge, トラブルシューティング, デプロイ エラー
services: sql-edge
ms.service: sql-edge
ms.topic: troubleshooting
author: SQLSourabh
ms.author: sourabha
ms.reviewer: sstein
ms.date: 09/22/2020
ms.openlocfilehash: 517fed0dd9eb1736344546bde9f79e52ee17182f
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "91333105"
---
# <a name="troubleshooting-azure-sql-edge-deployments"></a>Azure SQL Edge のデプロイのトラブルシューティング 

この記事では、Azure SQL Edge コンテナーをデプロイおよび使用するときに発生する可能性のあるエラーに関する情報を提供し、これらの問題を解決するためのトラブルシューティング手法について説明します。 

Azure SQL Edge では、2 つのデプロイ モデルがサポートされています。 
- Azure IoT Edge を使用して接続されたデプロイ: Azure SQL Edge は Azure Marketplace で提供されており、[Azure IoT Edge](../iot-edge/about-iot-edge.md) のモジュールとしてデプロイできます。 詳細については、[Azure SQL Edge のデプロイ](deploy-portal.md)に関するページを参照してください。<br>

- 切断されたデプロイ: Azure SQL Edge コンテナー イメージは Docker Hub からプルし、スタンドアロンの Docker コンテナーとして、または Kubernetes クラスター上にデプロイできます。 詳細については、[Docker を使用した Azure SQL Edge のデプロイ](disconnected-deployment.md)に関するページおよび [Kubernetes での Azure SQL Edge コンテナーのデプロイ](deploy-kubernetes.md)に関するページを参照してください。

## <a name="troubleshooting-iot-edge-device-and-deployments"></a>IoT Edge のデバイスとデプロイに関するトラブルシューティング

Azure IoT Edge を使用して SQL Edge をデプロイするときにエラーが発生する場合は、`iotedge` サービスが正しく構成され、実行されていることを確認します。 次のドキュメントは、Azure IoT Edge に関連する問題のトラブルシューティングを行うときに役立ちます:
- 「[Azure IoT Edge での一般的な問題と解決](../iot-edge/troubleshoot-common-errors.md)」。
- [IoT Edge デバイスのトラブルシューティング](../iot-edge/troubleshoot.md)

## <a name="docker-command-errors"></a>Docker コマンドのエラー

いずれかの `docker` コマンドでエラーが発生した場合は、docker サービスが実行されていることを確認し、高度な権限を使用して実行してみてください。

たとえば、Linux 上では、`docker` コマンドの実行時に次のエラーが表示される場合があります。

```output
Cannot connect to the Docker daemon. Is the docker daemon running on this host?
```

Linux 上でこのエラーが発生した場合は、前に `sudo` を付けて同じコマンドを実行してみてください。 これが失敗した場合は、docker サービスが実行されていることを確認し、必要に応じて開始します。

```bash
sudo systemctl status docker
sudo systemctl start docker
```

Windows では、PowerShell またはコマンド プロンプトを管理者として起動していることを確認します。

## <a name="azure-sql-edge-container-startup-errors"></a>Azure SQL Edge コンテナーのスタートアップ エラー

SQL Edge コンテナーの実行が失敗する場合は、次のテストを試してください。

- Azure IoT Edge を使用している場合は、モジュールのイメージが正常にダウンロードされたこと、および環境変数とコンテナー作成オプションがモジュールのマニフェストで正しく指定されていることを確認します。

- Docker または Kubernetes ベースのデプロイを使用している場合は、`docker run` コマンドの形式が正しいことを確認します。 詳細については、[Docker を使用した Azure SQL Edge のデプロイ](disconnected-deployment.md)に関するページおよび [Kubernetes での Azure SQL Edge コンテナーのデプロイ](deploy-kubernetes.md)に関するページを参照してください。

- `failed to create endpoint CONTAINER_NAME on network bridge. Error starting proxy: listen tcp 0.0.0.0:1433 bind: address already in use.` などのエラーが表示される場合、既に使用されているポートにコンテナー ポート 1433 をマップしようとしています。 これは、ホスト マシン上で SQL Edge をローカルに実行している場合に発生する可能性があります。 また、2 つの SQL Edge コンテナーを開始し、両方を同じホスト ポートにマップしようとした場合にも、発生する可能性があります。 この状況が発生した場合は、`-p` パラメーターを使用して、コンテナー ポート 1433 を別のホスト ポートにマップします。 次に例を示します。 

    ```bash
    sudo docker run --cap-add SYS_PTRACE -e 'ACCEPT_EULA=1' -e 'MSSQL_SA_PASSWORD=yourStrong(!)Password' -p 1433:1433 --name azuresqledge -d mcr.microsoft.com/azure-sql-edge-developer.
    ```

- コンテナーを起動しようとしたときに `Got permission denied while trying to connect to the Docker daemon socket at unix:///var/run/docker.sock: Get http://%2Fvar%2Frun%2Fdocker.sock/v1.30tdout=1&tail=all: dial unix /var/run/docker.sock: connect: permission denied` のようなエラーが表示される場合、Ubuntu の docker グループに自分のユーザーを追加します。 その後、この変更が新しいセッションに影響するため、ログアウトしてもう一度ログインします。 

   ```bash
    usermod -aG docker $USER
   ```

- コンテナーからのエラー メッセージがあるかどうかを確認します。

   ```bash
   docker logs e69e056c702d
   ```

- コンテナー管理ソフトウェアを使用している場合は、ルートとして実行されているコンテナー プロセスがサポートされていることを確認します。 コンテナー内の sqlservr プロセスは、ルートとして実行されます。

- 既定では、Azure SQL Edge コンテナーは、`mssql` という名前の非ルート ユーザーとして実行されます。 マウント ポイントまたはデータ ボリュームを使用してデータを保持している場合は、`mssql` ユーザーにボリュームに対する適切なアクセス許可があることを確認します。 詳細については、[非 root ユーザーとしての実行](configure.md#run-azure-sql-edge-as-non-root-user)および[データの永続化](configure.md#persist-your-data)に関するページを参照してください。

- SQL Edge Docker コンテナーが開始直後に終了する場合は、Docker のログを確認します。 Windows の PowerShell で `docker run` コマンドを使用する場合、単一引用符ではなく二重引用符を使用します。 PowerShell Core では、単一引用符を使用します。

- [SQL Edge のエラー ログ](#errorlogs)を確認します。

## <a name="sql-edge-connection-failures"></a>SQL Edge 接続エラー

コンテナー内で実行されている SQL Edge インスタンスに接続できない場合は、次のテストを試してください。

- `docker ps -a` の出力の **STATUS** 列を見て、SQL Edge コンテナーが実行されていることを確認します。 そうでない場合は、`docker start <Container ID>` を使用して開始します。

- (1433 ではなく) 既定以外のホスト ポートにマップした場合は、接続文字列内でポートを指定していることを確認してください。 ポート マッピングは、`docker ps -a` 出力の **PORTS** 列に表示されます。 Azure SQL Edge への接続の詳細については、「[Azure SQL Edge の接続とクエリ](connect.md)」を参照してください

- 以前に、マップされたデータ ボリュームまたはデータ ボリューム コンテナーを使用して SQL Edge をデプロイしたことがあり、現在は既存のマップされたデータ ボリュームまたはデータ ボリューム コンテナーを使用している場合、SQL Edge では `MSSQL_SA_PASSWORD` 環境変数の値は無視されます。 代わりに、以前に構成した SA ユーザー パスワードが使用されます。 これは、SQL Edge によって、マップされたボリュームまたはデータ ボリューム コンテナー内の既存の master データベース ファイルが再利用されるために発生します。 この問題が発生する場合は、次のオプションを使用できます。

    - まだ使用可能な場合は、以前に使用していたパスワードを使用して接続します。
    - 別のマップされたボリュームまたはデータ ボリューム コンテナーを使用するように、SQL Edge を構成します。
    - マップされたボリュームまたはデータ ボリューム コンテナーから、既存の master データベース ファイル (master.mdf と mastlog.mdf) を削除します。

- [SQL Edge のエラー ログ](#errorlogs)を確認します。

## <a name="sql-edge-setup-and-error-logs"></a><a id="errorlogs"></a> SQL Edge のセットアップ ログとエラー ログ

既定では、SQL Edge エラー ログはコンテナー内の **/var/opt/mssql/log** ディレクトリに存在し、次のいずれかの方法でアクセスできます。

- コンテナーの作成時にホスト ディレクトリを **/var/opt/mssql** にマウントした場合は、代わりに、ホスト上のマップされたパスにある **log** サブディレクトリを調べることができます。
- 対話型のコマンド プロンプトを使用して、コンテナーに接続します。 コンテナーが実行されていない場合は、まずコンテナーを開始します。 次に、対話型のコマンド プロンプトを使用して、ログを検査します。 コンテナー ID は、`docker ps` コマンドの実行で取得できます。

    ```bash
    docker start <ContainerID>
    docker exec -it <ContainerID> "/bin/bash"
    ```

    コンテナーの内部の bash セッションから、次のコマンドを実行します。

    ```bash
    cd /var/opt/mssql/log
    cat errorlog
    ```
- SQL Edge コンテナーが起動して実行されており、クライアント ツールを使用してインスタンスに接続できる場合は、ストアド プロシージャ `sp_readerrorlog` を使用して、SQL Edge エラー ログの内容を読み取ることができます。

## <a name="execute-commands-in-a-container"></a>コンテナー内でコマンドを実行する

実行中のコンテナーがある場合は、ホスト ターミナルからコンテナー内でコマンドを実行できます。

コンテナー ID を取得するには、次のように実行します。

```bash
docker ps -a
```

コンテナー内で bash ターミナルを開始するには、次のように実行します。

```bash
docker exec -it <Container ID> /bin/bash
```

これで、コンテナーの内部のターミナルで実行する場合と同じようにコマンドを実行できるようになりました。 終わったら、`exit` と入力します。 これにより対話型コマンド セッションが終了しますが、コンテナーは引き続き実行されます。

### <a name="enabling-verbose-logging"></a>詳細ログ記録の有効化

ストリーミング エンジンの既定のログ レベルで十分な情報が得られない場合は、SQL Edge でストリーミング エンジンのデバッグ ログを有効にすることができます。 デバッグ ログを有効にするには、SQL Edge のデプロイに `RuntimeLogLevel=debug` 環境変数を追加します。 デバッグ ログを有効にした後、問題の再現を試み、ログで関連するメッセージまたは例外を確認します。 

> [!NOTE]
> 詳細ログ記録オプションは、通常の運用ワークロードではなく、トラブルシューティングにのみ使用してください。 


## <a name="next-steps"></a>次のステップ

- [SQL Edge での ONNX を使用した機械学習と人工知能](onnx-overview.md)
- [Azure SQL Edge でのデータ ストリーミング](stream-data.md)
- [データ保有とクリーンアップ](data-retention-overview.md)
- [時間のギャップを埋めて欠損値を補完する](imputing-missing-values.md)







