---
title: Azure Cosmos DB Emulator を使用したローカルでのインストールと開発
description: Windows、Linux、macOS、および Windows Docker 環境に Azure Cosmos DB Emulator をインストールして使用する方法について説明します。 エミュレーターを使用すると、Azure サブスクリプションを作成せずに無料で、アプリケーションの開発とテストをローカルで行うことができます。
ms.service: cosmos-db
ms.topic: how-to
author: markjbrown
ms.author: mjbrown
ms.date: 09/22/2020
ms.custom: devx-track-csharp, contperf-fy21q1
ms.openlocfilehash: 89e82e210dbf7c404ebd5978d87bf391d8565ee2
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "97033971"
---
# <a name="install-and-use-the-azure-cosmos-db-emulator-for-local-development-and-testing"></a>ローカルでの開発とテストのために Azure Cosmos DB Emulator をインストールして使用する
[!INCLUDE[appliesto-all-apis](includes/appliesto-all-apis.md)]

Azure Cosmos DB Emulator では、Azure Cosmos DB サービスを開発目的でエミュレートするローカル環境を利用できます。 Azure Cosmos DB Emulator を使用すると、ローカルでのアプリケーションの開発とテストが、Azure サブスクリプションを作成したりコストをかけたりせずに実施できます。 Azure Cosmos DB Emulator でのアプリケーションの動作に満足できたら、クラウドでの Azure Cosmos アカウントの使用に切り替えることができます。 この記事では、Windows、Linux、macOS、および Windows Docker 環境にエミュレーターをインストールして使用する方法について説明します。

## <a name="download-the-emulator"></a>エミュレーターをダウンロードする

まず、最新バージョンの Azure Cosmos DB Emulator をローカル コンピューターにダウンロードしてインストールします。 [エミュレーター リリース ノート](local-emulator-release-notes.md)では、利用可能なすべてのバージョンと各リリースで行われた機能更新プログラムの一覧を示します。

:::image type="icon" source="media/local-emulator/download-icon.png" border="false"::: **[Azure Cosmos DB Emulator をダウンロードする](https://aka.ms/cosmosdb-emulator)**

Azure Cosmos DB Emulator を使ったアプリケーションの開発には、[SQL](local-emulator.md#sql-api)、[Cassandra](local-emulator.md#cassandra-api)、[MongoDB](local-emulator.md#azure-cosmos-dbs-api-for-mongodb)、[Gremlin](local-emulator.md#gremlin-api)、[Table](local-emulator.md#table-api) の API アカウントを利用できます。 現在、エミュレーターのデータ エクスプローラーで完全にサポートされているのは SQL データの表示のみです。現時点では、MongoDB、Gremlin/Graph、Cassandra の各クライアント アプリケーションを使用して作成されたデータを表示できません。 詳細については、さまざまな API から[エミュレーター エンドポイントに接続する方法](#connect-with-emulator-apis)に関するページを参照してください。

## <a name="how-does-the-emulator-work"></a>エミュレーターのしくみ

Azure Cosmos DB Emulator には、Azure Cosmos DB サービスの高忠実度エミュレーションが用意されています。 Azure Cosmos DB と同等の機能がサポートされています。たとえば、データの作成、データのクエリ、コンテナーのプロビジョニングとスケーリング、ストアド プロシージャとトリガーの実行などです。 Azure Cosmos DB Emulator を使用してアプリケーションを開発してテストし、Azure Cosmos DB 接続エンドポイントを更新することで、それらをグローバル規模で Azure にデプロイできます。

Azure Cosmos DB サービスのエミュレーションは忠実ですが、エミュレーターの実装はサービスとは異なります。 たとえば、エミュレーターでは、永続化用のローカル ファイル システムや接続用の HTTPS プロトコル スタックなど、標準的な OS コンポーネントが使用されます。 エミュレーターを使用する場合、グローバル レプリケーション、読み取りおよび書き取りの 10 ミリ秒を下回る待ち時間、調整可能な一貫性レベルなど、Azure インフラストラクチャに依存する機能は適用されません。

[Azure Cosmos DB データ移行ツール](https://github.com/azure/azure-documentdb-datamigrationtool)を使用すると、Azure Cosmos DB Emulator と Azure Cosmos DB サービスの間でデータを移行できます。

## <a name="differences-between-the-emulator-and-the-cloud-service"></a>エミュレーターとクラウド サービスの違い

Azure Cosmos DB Emulator は、ローカルの開発者ワークステーションに環境をエミュレートして提供するものであるため、このエミュレーターとクラウドにある Azure Cosmos アカウントとの間には機能にいくつかの違いがあります。

* 現在、エミュレーターの **[データ エクスプローラー]** ペインで完全にサポートされているのは、SQL API クライアントのみです。 MongoDB、Table、Graph、Cassandra などの API のような、Azure Cosmos DB 用の API については、**データ エクスプローラー** のビューと操作が完全にはサポートされていません。

* エミュレーターでサポートされるのは、1 つの固定アカウントと既知の主キーのみです。 Azure Cosmos DB Emulator を使用している場合、キーを再生成することはできませんが、[コマンド ライン](emulator-command-line-parameters.md) オプションを使用して既定のキーを変更できます。

* エミュレーターを使用すると、[プロビジョニングされたスループット](set-throughput.md) モードでのみ Azure Cosmos アカウントを作成できます。現在、[サーバーレス](serverless.md) モードはサポートされていません。

* エミュレーターはスケーラブルなサービスではなく、多数のコンテナーはサポートされていません。 Azure Cosmos DB Emulator を使用する場合、既定では、固定サイズのコンテナー (Azure Cosmos DB SDK を使用する場合にのみサポートされます) を 400 RU/秒で 25 個まで、容量無制限のコンテナーを 5 個まで作成できます。 この値を変更する方法の詳細については、[PartitionCount 値の設定](emulator-command-line-parameters.md#set-partitioncount)に関する記事をご覧ください。

* エミュレーターには、クラウド サービスのように複数の [Azure Cosmos DB 整合性レベル](consistency-levels.md)が用意されていません。

* エミュレーターでは、[マルチリージョン レプリケーション](distribute-data-globally.md)が提供されていません。

* Azure Cosmos DB Emulator には Azure Cosmos DB サービスの最近の変更が反映されていないことがあるため、常に [Azure Cosmos DB Capacity Planner](estimate-ru-with-capacity-planner.md) を参照し、アプリケーションのスループット (RU) のニーズを正確に見積もる必要があります。

* エミュレーターは、254 文字の最大 ID プロパティ サイズをサポートしています。

## <a name="install-the-emulator"></a>エミュレーターをインストールする

エミュレーターをインストールする前に、次のハードウェアおよびソフトウェア要件を満たしていることを確認します。

* ソフトウェア要件:
  * 現在、Windows Server 2016、2019、または Windows 10 ホスト OS がサポートされています。 Active Directory が有効なホスト OS は、現在サポートされていません。
  * 64 ビット オペレーティング システム

* 最小ハードウェア要件:
  * 2 GB の RAM
  * 10 GB のハードディスク空き容量

* Azure Cosmos DB Emulator をインストール、構成、実行するには、コンピューターの管理特権が必要です。 エミュレーターによって、サービスを実行するために証明書が追加され、ファイアウォール ルールも設定されます。 そのため、エミュレーターでそのような操作を実行できるようには、管理者権限が必要です。

まず、最新バージョンの [Azure Cosmos DB Emulator](https://aka.ms/cosmosdb-emulator) をローカル コンピューターにダウンロードしてインストールします。 エミュレーターのインストール時に問題が発生した場合は、[エミュレーターのトラブルシューティング](troubleshoot-local-emulator.md)の記事を参照してデバッグしてください。

この記事の次のセクションで説明するように、システム要件に応じて、[Windows](#run-on-windows)、[Docker for Windows](#run-on-windows-docker)、[Linux、または macOS](#run-on-linux-macos) 上でエミュレーターを実行できます。

## <a name="check-for-emulator-updates"></a>エミュレーターの更新プログラムを確認する

エミュレーターの各バージョンには、一連の機能更新プログラムまたはバグ修正が含まれています。 使用できるバージョンを確認するには、[エミュレーターのリリース ノート](local-emulator-release-notes.md)の記事を参照してください。

インストール後、既定の設定を使用した場合、エミュレーターに対応するデータは %LOCALAPPDATA%\CosmosDBEmulator の場所に保存されます。 オプションのデータ パス設定を使用して、別の場所を構成することができます。これは、[コマンドライン パラメーター](emulator-command-line-parameters.md)としての `/DataPath=PREFERRED_LOCATION` です。 Azure Cosmos DB Emulator のあるバージョンで作成したデータは、他のバージョンを使用してアクセスできない可能性があります。 データを永続化して長期にわたって保持する必要がある場合、そのデータは Azure Cosmos DB Emulator ではなく Azure Cosmos アカウントに格納することをお勧めします。

## <a name="use-the-emulator-on-windows"></a><a id="run-on-windows"></a>Windows 上でエミュレーターを使用する

Azure Cosmos DB Emulator は、既定で `C:\Program Files\Azure Cosmos DB Emulator` の場所にインストールされます。 Windows 上で Azure Cosmos DB Emulator を起動するには、 **[スタート]** ボタンを選択するか、Windows キーを押します。 「**Azure Cosmos DB Emulator**」と入力して、アプリケーションの一覧からエミュレーターを選択します。

:::image type="content" source="./media/local-emulator/database-local-emulator-start.png" alt-text="[スタート] ボタンを選択するか Windows キーを押して「Azure Cosmos DB Emulator」と入力し、アプリケーションの一覧からエミュレーターを選択する":::

エミュレーターが起動すると、Windows タスク バーの通知領域にアイコンが表示されます。 この URL `https://localhost:8081/_explorer/index.html` URL の Azure Cosmos データ エクスプローラーがブラウザーで自動的に開きます。

:::image type="content" source="./media/local-emulator/database-local-emulator-taskbar.png" alt-text="Azure Cosmos DB ローカル エミュレーターのタスク バーの通知":::

コマンドラインまたは PowerShell コマンドからエミュレーターを起動および停止することもできます。 詳細については、[コマンドライン ツールのリファレンス](emulator-command-line-parameters.md)の記事を参照してください。

Azure Cosmos DB Emulator は既定では、ポート 8081 でリッスンしているローカル コンピューター ("localhost") で実行されます。 アドレスは `https://localhost:8081/_explorer/index.html` として表示されます。 エクスプローラーを閉じた後にもう一度開く場合は、ブラウザーで URL を開くか、次に示すように Windows トレイ アイコンの Azure Cosmos DB Emulator から起動できます。

:::image type="content" source="./media/local-emulator/database-local-emulator-data-explorer-launcher.png" alt-text="Azure Cosmos ローカル エミュレーターのデータ エクスプローラー起動ツール":::

## <a name="use-the-emulator-on-docker-for-windows"></a><a id="run-on-windows-docker"></a>Docker for Windows 上でエミュレーターを使用する

Azure Cosmos DB Emulator は、Windows Docker コンテナー上で実行できます。 docker pull コマンドについては [Docker Hub](https://hub.docker.com/r/microsoft/azure-cosmosdb-emulator/)、`Dockerfile` と詳細については [GitHub](https://github.com/Azure/azure-cosmos-db-emulator-docker) を参照してください。 現在、エミュレーターは Docker for Oracle Linux 上では機能しません。 Docker for Windows 上でエミュレーターを実行するには、次の手順を実行します。

1. [Docker for Windows](https://www.docker.com/docker-windows) をインストールしたら、ツール バーの Docker アイコンを右クリックし、 **[Switch to Windows containers]\(Windows コンテナーに切り替える\)** を選択して Windows コンテナーに切り替えます。

1. 次に、普段利用しているシェルから次のコマンドを実行し、Docker Hub からエミュレーター イメージをプルします。

   ```bash
   docker pull mcr.microsoft.com/cosmosdb/windows/azure-cosmos-emulator
   ```

1. イメージを開始するには、コマンド ラインまたは PowerShell 環境に応じて次のコマンドを実行します。

   # <a name="command-line"></a>[コマンド ライン](#tab/cli)

   ```bash

   md %LOCALAPPDATA%\CosmosDBEmulator\bind-mount

   docker run --name azure-cosmosdb-emulator --memory 2GB --mount "type=bind,source=%LOCALAPPDATA%\CosmosDBEmulator\bind-mount,destination=C:\CosmosDB.Emulator\bind-mount" --interactive --tty -p 8081:8081 -p 8900:8900 -p 8901:8901 -p 8902:8902 -p 10250:10250 -p 10251:10251 -p 10252:10252 -p 10253:10253 -p 10254:10254 -p 10255:10255 -p 10256:10256 -p 10350:10350 mcr.microsoft.com/cosmosdb/windows/azure-cosmos-emulator
   ```
   Windows ベースの Docker イメージは、一般にすべての Windows ホスト OS と互換性があるとは限りません。 たとえば、既定の Azure Cosmos DB Emulator イメージは Windows 10 および Windows Server 2016 とのみ互換性があります。 Windows Server 2019 と互換性があるイメージが必要な場合は、代わりに次のコマンドを実行します。

   ```bash
   docker run --name azure-cosmosdb-emulator --memory 2GB --mount "type=bind,source=%hostDirectory%,destination=C:\CosmosDB.Emulator\bind-mount" --interactive --tty -p 8081:8081 -p 8900:8900 -p 8901:8901 -p 8902:8902 -p 10250:10250 -p 10251:10251 -p 10252:10252 -p 10253:10253 -p 10254:10254 -p 10255:10255 -p 10256:10256 -p 10350:10350 mcr.microsoft.com/cosmosdb/winsrv2019/azure-cosmos-emulator:latest
   ```

   # <a name="powershell"></a>[PowerShell](#tab/powershell)

   ```powershell

   md $env:LOCALAPPDATA\CosmosDBEmulator\bind-mount 2>null

   docker run --name azure-cosmosdb-emulator --memory 2GB --mount "type=bind,source=$env:LOCALAPPDATA\CosmosDBEmulator\bind-mount,destination=C:\CosmosDB.Emulator\bind-mount" --interactive --tty -p 8081:8081 -p 8900:8900 -p 8901:8901 -p 8902:8902 -p 10250:10250 -p 10251:10251 -p 10252:10252 -p 10253:10253 -p 10254:10254 -p 10255:10255 -p 10256:10256 -p 10350:10350 mcr.microsoft.com/cosmosdb/windows/azure-cosmos-emulator

   ```

   応答は次のようになります。

   ```bash
   Starting emulator
   Emulator Endpoint: https://172.20.229.193:8081/
   Primary Key: C2y6yDjf5/R+ob0N8A7Cgv30VRDJIWEHLM+4QDU5DE2nQ9nDuVTqobD4b8mGGyPMbIZnqyMsEcaGQy67XIw/Jw==
   Exporting SSL Certificate
   You can import the SSL certificate from an administrator command prompt on the host by running:
   cd /d %LOCALAPPDATA%\CosmosDBEmulatorCert
   powershell .\importcert.ps1
   --------------------------------------------------------------------------------------------------
   Starting interactive shell
   ```
   ---

   > [!NOTE]
   > `docker run` コマンドを実行するときに、ポートの競合エラーが表示された場合 (つまり、指定したポートが既に使用されている場合) は、ポート番号を変更してカスタム ポートを渡します。 たとえば、"-p 8081:8081" パラメーターを "-p 443:8081" に変更することができます

1. 次に、応答からエミュレーター エンドポイントと主キーを使用して、TLS/SSL 証明書をホストにインポートします。 TLS/SSL 証明書をインポートするには、管理者コマンド プロンプトから次の手順を実行します。

   # <a name="command-line"></a>[コマンド ライン](#tab/cli)

   ```bash
   cd  %LOCALAPPDATA%\CosmosDBEmulator\bind-mount
   powershell .\importcert.ps1
   ```

   # <a name="powershell"></a>[PowerShell](#tab/powershell)

   ```powershell
   cd $env:LOCALAPPDATA\CosmosDBEmulator\bind-mount
   .\importcert.ps1
   ```
   ---

1. エミュレーターの起動後に対話型シェルを閉じると、エミュレーターのコンテナーがシャットダウンされます。 データ エクスプローラーを開くには、ブラウザーで次の URL に移動します。 上記の応答メッセージにエミュレーター エンドポイントが表示されます。

   `https://<emulator endpoint provided in response>/_explorer/index.html`

Linux Docker コンテナーで実行されている .NET クライアント アプリケーションがあり、ホスト マシン上で Azure Cosmos DB Emulator を実行している場合は、次のセクションの手順に従って、Linux Docker コンテナーに証明書をインポートしてください。

### <a name="regenerate-the-emulator-certificates-when-running-on-a-docker-container"></a>Docker コンテナー上で実行するときに、エミュレーターの証明書を再生成します

Docker コンテナーでエミュレーターを実行する場合、エミュレーターに関連付けられた証明書は、対応するコンテナーを停止して再起動するたびに再生成されます。 そのため、各コンテナーの起動後に証明書を再インポートする必要があります。 この制限を回避するには、Docker Compose ファイルを使用して、Docker コンテナーを特定の IP アドレスとコンテナー イメージにバインドします。

たとえば、Docker Compose ファイル内で次の構成を使用できます。必要に応じて形式を変更してください。 

```yml
version: '2.4' # Do not upgrade to 3.x yet, unless you plan to use swarm/docker stack: https://github.com/docker/compose/issues/4513

networks:
  default:
    external: false
    ipam:
      driver: default
      config:
        - subnet: "172.16.238.0/24"

services:

  # First create a directory that will hold the emulator traces and certificate to be imported
  # set hostDirectory=C:\emulator\bind-mount
  # mkdir %hostDirectory%

  cosmosdb:
    container_name: "azurecosmosemulator"
    hostname: "azurecosmosemulator"
    image: 'mcr.microsoft.com/cosmosdb/windows/azure-cosmos-emulator'
    platform: windows
    tty: true
    mem_limit: 3GB
    ports:
        - '8081:8081'
        - '8900:8900'
        - '8901:8901'
        - '8902:8902'
        - '10250:10250'
        - '10251:10251'
        - '10252:10252'
        - '10253:10253'
        - '10254:10254'
        - '10255:10255'
        - '10256:10256'
        - '10350:10350'
    networks:
      default:
        ipv4_address: 172.16.238.246
    volumes:
        - '${hostDirectory}:C:\CosmosDB.Emulator\bind-mount'
```

## <a name="use-the-emulator-on-linux-or-macos"></a><a id="run-on-linux-macos"></a>Linux または macOS 上でエミュレーターを使用する

現在、Azure Cosmos DB Emulator は Windows でのみ実行できます。 Linux または macOS を使用している場合、ハイパーバイザー (Parallels や VirtualBox など) でホストされる Windows 仮想マシンでエミュレーターを実行できます。

> [!NOTE]
> ハイパーバイザーでホストされている Windows 仮想マシンを再起動するたびに、仮想マシンの IP アドレスは変わるため、証明書を再インポートする必要があります。 IP アドレスを保持するように仮想マシンを構成している場合、証明書をインポートする必要はありません。

Linux または macOS 環境上でエミュレーターを使用するには、次の手順を実行します。

1. Windows 仮想マシンから次のコマンドを実行し、IPv4 アドレスをメモします。

   ```bash
   ipconfig.exe
   ```

1. アプリケーション内で、`localhost` ではなく `ipconfig.exe` から返される IPv4 アドレスを使用するようにエンドポイント URL を変更します。

1. Windows 仮想マシンから、次のオプションを使用してコマンド ラインから Azure Cosmos DB Emulator を起動します。 コマンド ラインでサポートされているパラメーターの詳細については、[エミュレーターのコマンドライン ツール リファレンス](emulator-command-line-parameters.md)に関するページを参照してください。

   ```bash
   Microsoft.Azure.Cosmos.Emulator.exe /AllowNetworkAccess /Key=C2y6yDjf5/R+ob0N8A7Cgv30VRDJIWEHLM+4QDU5DE2nQ9nDuVTqobD4b8mGGyPMbIZnqyMsEcaGQy67XIw/Jw==
   ```

1. 最後に、Linux または Mac 環境で実行されているアプリケーションとエミュレーターの間の証明書の信頼プロセスを解決する必要があります。 次の 2 つのオプションのいずれかを使用して、証明書を解決できます。

   1. [エミュレーターの TLS/SSL 証明書を Linux または Mac 環境にインポートする](#import-certificate)、または
   2. [アプリケーションで TLS/SSL 検証を無効にする](#disable-ssl-validation)

### <a name="option-1-import-the-emulator-tlsssl-certificate"></a><a id="import-certificate"></a>オプション 1: エミュレーターの TLS/SSL 証明書をインポートする

以下のセクションでは、エミュレーターの TLS/SSL 証明書を Linux および macOS 環境にインポートする方法について説明します。

#### <a name="linux-environment"></a>Linux 環境

Linux 環境では、.NET が OpenSSL を使用して検証を行います。

1. [証明書を PFX 形式でエクスポートします](local-emulator-export-ssl-certificates.md#export-emulator-certificate)。 PFX オプションは、秘密キーのエクスポートを選択するときに使用できます。

1. PFX ファイルを Linux 環境にコピーします。

1. PFX ファイルを CRT ファイルに変換します。

   ```bash
   openssl pkcs12 -in YourPFX.pfx -clcerts -nokeys -out YourCTR.crt
   ```

1. ご利用の Linux ディストリビューションの、カスタム証明書が格納されているフォルダーに CRT ファイルをコピーします。 一般に、Debian ディストリビューションでは `/usr/local/share/ca-certificates/` に格納されます。

   ```bash
   cp YourCTR.crt /usr/local/share/ca-certificates/
   ```

1. TLS/SSL 証明書を更新します。これによって `/etc/ssl/certs/` フォルダーが更新されます。

   ```bash
   update-ca-certificates
   ```

#### <a name="macos-environment"></a>macOS 環境

Mac 環境では、次の手順に従います。

1. [証明書を PFX 形式でエクスポートします](local-emulator-export-ssl-certificates.md#export-emulator-certificate)。 PFX オプションは、秘密キーのエクスポートを選択するときに使用できます。

1. PFX ファイルを Mac 環境にコピーします。

1. *Keychain Access* アプリケーションを開いて PFX ファイルをインポートします。

1. 証明書の一覧を開き、`localhost` という名前の証明書を特定します。

1. その特定の項目のコンテキスト メニューを開いて *[項目の取得]* を選択し、 *[信頼]*  >  *[この証明書を使用するとき]* オプションの *[常に信頼]* を選択します。 

   :::image type="content" source="./media/local-emulator/mac-trust-certificate.png" alt-text="その特定の項目のコンテキスト メニューを開いて [項目の取得] を選択し、[信頼] の [この証明書を使用するとき] オプションで [常に信頼] を選択する":::
  
### <a name="option-2-disable-the-ssl-validation-in-the-application"></a><a id="disable-ssl-validation"></a>オプション 2: アプリケーションで SSL 検証を無効にする

SSL 検証の無効化は、開発目的の場合にのみお勧めします。運用環境で実行するときは無効にしないでください。 次の例は、.NET および Node.js アプリケーションの SSL 検証を無効にする方法を示しています。

# <a name="net-standard-21"></a>[.NET Standard 2.1+](#tab/ssl-netstd21)

.NET Standard 2.1 以降と互換性のあるフレームワークで実行されているアプリケーションの場合は、`CosmosClientOptions.HttpClientFactory` を活用できます。

[!code-csharp[Main](~/samples-cosmosdb-dotnet-v3/Microsoft.Azure.Cosmos.Samples/Usage/HttpClientFactory/Program.cs?name=DisableSSLNETStandard21)]

# <a name="net-standard-20"></a>[.NET Standard 2.0](#tab/ssl-netstd20)

.NET Standard 2.0 と互換性のあるフレームワークで実行されているアプリケーションの場合は、`CosmosClientOptions.HttpClientFactory` を活用できます。

[!code-csharp[Main](~/samples-cosmosdb-dotnet-v3/Microsoft.Azure.Cosmos.Samples/Usage/HttpClientFactory/Program.cs?name=DisableSSLNETStandard20)]

# <a name="nodejs"></a>[Node.js](#tab/ssl-nodejs)

Node.js アプリケーションの場合は、アプリケーションの起動中に `NODE_TLS_REJECT_UNAUTHORIZED` を設定するように `package.json` ファイルを変更できます。

```json
"start": NODE_TLS_REJECT_UNAUTHORIZED=0 node app.js
```
--- 

## <a name="enable-access-to-emulator-on-a-local-network"></a>ローカル ネットワーク上でエミュレーターへのアクセスを有効にする

1 つのネットワークを使用する複数のマシンがあり、1 つのマシンにエミュレーターを設定していて、他のマシンからエミュレーターにアクセスする場合があります。 このような場合、ローカル ネットワーク上でエミュレーターへのアクセスを有効にする必要があります。

ローカル ネットワークでのエミュレーターを実行できます。 ネットワーク アクセスを有効にするには、[コマンド ライン](emulator-command-line-parameters.md)で `/AllowNetworkAccess` オプションを指定します。また、`/Key=key_string` または `/KeyFile=file_name` を指定する必要もあります。 `/GenKeyFile=file_name` を使用すると、ランダムなキーを備えたファイルを事前に生成できます。 その後、それを `/KeyFile=file_name` または `/Key=contents_of_file` に渡すことができます。

ネットワーク アクセスを初めて有効にする場合は、エミュレーターをシャットダウンし、エミュレーターのデータ ディレクトリ ( *%LOCALAPPDATA%\CosmosDBEmulator*) を削除する必要があります。

## <a name="authenticate-connections-when-using-emulator"></a><a id="authenticate-requests"></a>エミュレーターの使用時に接続を認証する

クラウドの Azure Cosmos DB と同様に、Azure Cosmos DB Emulator に対する各要求は認証される必要があります。 Azure Cosmos DB Emulator では、TLS 経由のセキュリティで保護された通信のみがサポートされます。 Azure Cosmos DB Emulator では、主キー認証について、単一の固定アカウントと既知の認証キーがサポートされています。 Azure Cosmos DB Emulator ではこのアカウントとキーのみが資格情報として使用できます。 これらは次のとおりです。

```bash
Account name: localhost:<port>
Account key: C2y6yDjf5/R+ob0N8A7Cgv30VRDJIWEHLM+4QDU5DE2nQ9nDuVTqobD4b8mGGyPMbIZnqyMsEcaGQy67XIw/Jw==
```

> [!NOTE]
> Azure Cosmos DB Emulator でサポートされている主キーは、エミュレーターで使用することのみを想定したものです。 Azure Cosmos DB Emulator で運用環境の Azure Cosmos DB アカウントとキーを使用することはできません。

> [!NOTE]
> /Key オプションを使用してエミュレーターを開始した場合は、既定のキーである `C2y6yDjf5/R+ob0N8A7Cgv30VRDJIWEHLM+4QDU5DE2nQ9nDuVTqobD4b8mGGyPMbIZnqyMsEcaGQy67XIw/Jw==` の代わりに生成されたキーを使用します。 /Key オプションの詳細については、[コマンドライン ツールのリファレンス](emulator-command-line-parameters.md)を参照してください。

## <a name="connect-to-different-apis-with-the-emulator"></a><a id="connect-with-emulator-apis"></a>エミュレーターを使用してさまざまな API に接続する

### <a name="sql-api"></a>SQL API

デスクトップで Azure Cosmos DB Emulator を実行している間、サポートされている [Azure Cosmos DB SDK](sql-api-sdk-dotnet-standard.md) または [Azure Cosmos DB REST API](/rest/api/cosmos-db/) を使用してエミュレーターを操作できます。 Azure Cosmos DB Emulator には、SQL API または Azure Cosmos DB for Mongo DB API のコンテナーを作成できる組み込みのデータ エクスプローラーも含まれています。 データ エクスプローラーを使用すると、コードを書くことなく項目を表示および編集できます。

```csharp
// Connect to the Azure Cosmos DB Emulator running locally
CosmosClient client = new CosmosClient(
   "https://localhost:8081", 
    "C2y6yDjf5/R+ob0N8A7Cgv30VRDJIWEHLM+4QDU5DE2nQ9nDuVTqobD4b8mGGyPMbIZnqyMsEcaGQy67XIw/Jw==");

```

### <a name="azure-cosmos-dbs-api-for-mongodb"></a>Azure Cosmos DB の MongoDB 用 API

デスクトップ上で Azure Cosmos DB Emulator が実行中になったら、[Azure Cosmos DB の MongoDB 用 API](mongodb-introduction.md) を使用してエミュレーターを操作できます。 [コマンド プロンプト](emulator-command-line-parameters.md)から管理者としてエミュレーターを起動します。その際、一緒に "/EnableMongoDbEndpoint" を指定します。 その後、次の接続文字列を使用して MongoDB API アカウントに接続します。

```bash
mongodb://localhost:C2y6yDjf5/R+ob0N8A7Cgv30VRDJIWEHLM+4QDU5DE2nQ9nDuVTqobD4b8mGGyPMbIZnqyMsEcaGQy67XIw/Jw==@localhost:10255/admin?ssl=true
```

### <a name="table-api"></a>テーブル API

デスクトップ上で Azure Cosmos DB Emulator が実行中になったら、[Azure Cosmos DB Table API SDK](./tutorial-develop-table-dotnet.md) を使用してエミュレーターを操作できます。 [コマンド プロンプト](emulator-command-line-parameters.md)から管理者としてエミュレーターを起動します。その際、一緒に "/EnableTableEndpoint" を指定します。 続いて、次のコードを実行して Table API アカウントに接続します。

```csharp
using Microsoft.WindowsAzure.Storage;
using Microsoft.WindowsAzure.Storage.Table;
using CloudTable = Microsoft.WindowsAzure.Storage.Table.CloudTable;
using CloudTableClient = Microsoft.WindowsAzure.Storage.Table.CloudTableClient;

string connectionString = "DefaultEndpointsProtocol=http;AccountName=localhost;AccountKey=C2y6yDjf5/R+ob0N8A7Cgv30VRDJIWEHLM+4QDU5DE2nQ9nDuVTqobD4b8mGGyPMbIZnqyMsEcaGQy67XIw/Jw==;TableEndpoint=http://localhost:8902/;";

CloudStorageAccount account = CloudStorageAccount.Parse(connectionString);
CloudTableClient tableClient = account.CreateCloudTableClient();
CloudTable table = tableClient.GetTableReference("testtable");
table.CreateIfNotExists();
table.Execute(TableOperation.Insert(new DynamicTableEntity("partitionKey", "rowKey")));
```

### <a name="cassandra-api"></a>Cassandra API

管理者の[コマンド プロンプト](emulator-command-line-parameters.md)からエミュレーターを起動します。その際、一緒に "/EnableCassandraEndpoint" を指定します。 代わりに、環境変数 `AZURE_COSMOS_EMULATOR_CASSANDRA_ENDPOINT=true` を設定することもできます。

1. [Python 2.7 をインストールします](https://www.python.org/downloads/release/python-2716/)

1. [Cassandra CLI/CQLSH をインストールします](https://cassandra.apache.org/download/)

1. 通常のコマンド プロンプト ウィンドウで次のコマンドを実行します。

   ```bash
   set Path=c:\Python27;%Path%
   cd /d C:\sdk\apache-cassandra-3.11.3\bin
   set SSL_VERSION=TLSv1_2
   set SSL_VALIDATE=false
   cqlsh localhost 10350 -u localhost -p C2y6yDjf5/R+ob0N8A7Cgv30VRDJIWEHLM+4QDU5DE2nQ9nDuVTqobD4b8mGGyPMbIZnqyMsEcaGQy67XIw/Jw== --ssl
   ```

1. CQLSH シェルで次のコマンドを実行し、Cassandra エンドポイントに接続します。

   ```bash
   CREATE KEYSPACE MyKeySpace WITH replication = {'class':'MyClass', 'replication_factor': 1};
   DESCRIBE keyspaces;
   USE mykeyspace;
   CREATE table table1(my_id int PRIMARY KEY, my_name text, my_desc text);
   INSERT into table1 (my_id, my_name, my_desc) values( 1, 'name1', 'description 1');
   SELECT * from table1;
   EXIT
   ```

### <a name="gremlin-api"></a>Gremlin API

管理者の[コマンド プロンプト](emulator-command-line-parameters.md)からエミュレーターを起動します。その際、一緒に "/EnableGremlinEndpoint" を指定します。 代わりに、環境変数 `AZURE_COSMOS_EMULATOR_GREMLIN_ENDPOINT=true` を設定することもできます

1. [apache-tinkerpop-gremlin-console-3.3.4 をインストール](https://archive.apache.org/dist/tinkerpop/3.3.4)します。

1. エミュレーターのデータ エクスプローラーから、データベース "db1" とコレクション "coll1" を作成します。パーティション キーには "/name" を選択します

1. 通常のコマンド プロンプト ウィンドウで次のコマンドを実行します。

   ```bash
   cd /d C:\sdk\apache-tinkerpop-gremlin-console-3.3.4-bin\apache-tinkerpop-gremlin-console-3.3.4
  
   copy /y conf\remote.yaml conf\remote-localcompute.yaml
   notepad.exe conf\remote-localcompute.yaml
     hosts: [localhost]
     port: 8901
     username: /dbs/db1/colls/coll1
     password: C2y6yDjf5/R+ob0N8A7Cgv30VRDJIWEHLM+4QDU5DE2nQ9nDuVTqobD4b8mGGyPMbIZnqyMsEcaGQy67XIw/Jw==
     connectionPool: {
     enableSsl: false}
     serializer: { className: org.apache.tinkerpop.gremlin.driver.ser.GraphSONMessageSerializerV1d0,
     config: { serializeResultToString: true  }}

   bin\gremlin.bat
   ```

1. Gremlin シェルで次のコマンドを実行し、Gremlin エンドポイントに接続します。

   ```bash
   :remote connect tinkerpop.server conf/remote-localcompute.yaml
   :remote console
   :> g.V()
   :> g.addV('person1').property(id, '1').property('name', 'somename1')
   :> g.addV('person2').property(id, '2').property('name', 'somename2')
   :> g.V()
   ```

## <a name="uninstall-the-local-emulator"></a><a id="uninstall"></a>ローカル エミュレーターのアンインストール

次の手順に従ってエミュレーターをアンインストールします。

1. システム トレイの **Azure Cosmos DB Emulator** のアイコンを右クリックし、 **[終了]** を選択して、開いているローカル エミュレーターのインスタンスをすべて終了します。 すべてのインスタンスが終了するまでしばらく時間がかかる場合があります。

1. Windows 検索ボックスに「**アプリと機能**」と入力し、**アプリと機能 (システム設定)** の検索結果を選択します。

1. アプリの一覧で、**Azure Cosmos DB エミュレーター** までスクロールして選択し、 **[アンインストール]** を選択し、確認して再度、 **[アンインストール]** をクリックします。

## <a name="next-steps"></a>次のステップ

この記事では、無料のローカル開発のためにローカル エミュレーターを使用する方法について学習しました。 次の記事に進むことができます。

* [Java、Python、および Node.js アプリで使用する Azure Cosmos DB エミュレーター証明書のエクスポート](local-emulator-export-ssl-certificates.md)
* [コマンド ライン パラメーターと PowerShell コマンドを使用してエミュレーターを制御する](emulator-command-line-parameters.md)
* [エミュレーターに関する問題をデバッグする](troubleshoot-local-emulator.md)
