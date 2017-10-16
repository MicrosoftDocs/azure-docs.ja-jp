---
title: "Azure Cosmos DB Emulator を使用したローカルでの開発 | Microsoft Docs"
description: "Azure Cosmos DB Emulator を使用すると、Azure サブスクリプションを作成せずに無料で、アプリケーションの開発とテストをローカルで行うことができます。"
services: cosmos-db
documentationcenter: 
keywords: Azure Cosmos DB Emulator
author: arramac
manager: jhubbard
editor: 
ms.assetid: 90b379a6-426b-4915-9635-822f1a138656
ms.service: cosmos-db
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/22/2017
ms.author: arramac
ms.translationtype: HT
ms.sourcegitcommit: 25e4506cc2331ee016b8b365c2e1677424cf4992
ms.openlocfilehash: a0f6a845a345ebd4ef0a58abf4934ce400103109
ms.contentlocale: ja-jp
ms.lasthandoff: 08/24/2017

---
# <a name="use-the-azure-cosmos-db-emulator-for-local-development-and-testing"></a>ローカルの開発とテストでの Azure Cosmos DB Emulator の使用

<table>
<tr>
  <td><strong>バイナリ</strong></td>
  <td>[MSI のダウンロード](https://aka.ms/cosmosdb-emulator)</td>
</tr>
<tr>
  <td><strong>Docker</strong></td>
  <td>[Docker Hub](https://hub.docker.com/r/microsoft/azure-documentdb-emulator/)</td>
</tr>
<tr>
  <td><strong>Docker ソース</strong></td>
  <td>[Github](https://github.com/azure/azure-documentdb-emulator-docker)</td>
</tr>
</table>
  
Azure Cosmos DB Emulator では、Azure Cosmos DB サービスを開発目的でエミュレートするローカル環境を利用できます。 Azure Cosmos DB Emulator を使用すると、ローカルでのアプリケーションの開発とテストが、Azure サブスクリプションを作成したりコストをかけたりせずに実施できます。 Azure Cosmos DB Emulator でのアプリケーションの動作に満足できたら、クラウドでの Azure Cosmos DB アカウントの使用に切り替えることができます。

この記事に含まれるタスクは次のとおりです。 

> [!div class="checklist"]
> * エミュレーターのインストール
> * Docker for Windows でのエミュレーターの実行
> * 要求の認証
> * エミュレーターでのデータ エクスプローラーの使用
> * SSL 証明書のエクスポート
> * コマンド ラインからのエミュレーターの呼び出し
> * トレース ファイルの収集

最初に、Kirill Gavrylyuk が Azure Cosmos DB Emulator の使用のいろはを解説している次のビデオを視聴することをお勧めします。 このビデオでは、エミュレーターを DocumentDB Emulator と呼んでいますが、ビデオの撮影後にツール自体の名前が Azure Cosmos DB Emulator に変更されました。 ビデオのすべての情報は、Azure Cosmos DB Emulator にそのまま適用されます。 

> [!VIDEO https://channel9.msdn.com/Events/Connect/2016/192/player]
> 
> 

## <a name="how-the-emulator-works"></a>Emulator の機能
Azure Cosmos DB Emulator には、Azure Cosmos DB サービスの高忠実度エミュレーションが用意されています。 JSON ドキュメントの作成とクエリ、コレクションのプロビジョニングとスケーリング、ストアド プロシージャとトリガーの実行のサポートなど、Azure Cosmos DB と同じ機能がサポートされています。 Azure Cosmos DB Emulator を使用してアプリケーションの開発とテストを行い、Azure Cosmos DB の接続エンドポイントの構成を 1 つ変更するだけで、世界規模で Azure にデプロイすることができます。

Microsoft は実際の Azure Cosmos DB サービスを忠実に再現したローカル エミュレーションを作成しましたが、Azure Cosmos DB Emulator の実装は実際のサービスのそれとは異なります。 たとえば Azure Cosmos DB Emulator では、永続化用のローカル ファイル システムや接続用の HTTPS プロトコル スタックなど、標準的な OS コンポーネントが使用されます。 つまり、グローバル レプリケーション、読み取り/書き取りの 10 ミリ秒を下回る待機時間、調整可能な一貫性レベルなど、Azure インフラストラクチャを使用する一部の機能は、Azure Cosmos DB Emulator では使用できません。

> [!NOTE]
> 現時点では、エミュレーターのデータ エクスプローラーは、DocumentDB API コレクションと MongoDB コレクションの作成のみをサポートしています。 テーブルとグラフの作成は現在サポートされていません。 

## <a name="system-requirements"></a>システム要件
Azure Cosmos DB Emulator のハードウェア要件とソフトウェア要件は、次のとおりです。

* ソフトウェア要件
  * Windows Server 2012 R2、Windows Server 2016、または Windows 10
*   最小ハードウェア要件
  * 2 GB RAM
  * 10 GB のハードディスク空き容量

## <a name="installation"></a>インストール
[Microsoft ダウンロード センター](https://aka.ms/cosmosdb-emulator)で Azure Cosmos DB Emulator をダウンロードしてインストールできます。 

> [!NOTE]
> Azure Cosmos DB Emulator をインストール、構成、実行するには、コンピューターの管理特権が必要です。

## <a name="running-on-docker-for-windows"></a>Docker for Windows での実行

Azure Cosmos DB Emulator は、Docker for Windows 上で実行できます。 Emulator は、Docker for Oracle Linux では機能しません。

[Docker for Windows](https://www.docker.com/docker-windows) をインストールした後、お気に入りのシェル (cmd.exe、PowerShell など) から次のコマンドを実行することによって、Docker Hub から Emulator イメージをプルできます。

```      
docker pull microsoft/azure-cosmosdb-emulator 
```
イメージを起動するには、次のコマンドを実行します。

``` 
md %LOCALAPPDATA%\CosmosDBEmulatorCert 2>nul
docker run -v %LOCALAPPDATA%\CosmosDBEmulatorCert:c:\CosmosDBEmulator\CosmosDBEmulatorCert -P -t -i microsoft/azure-cosmosdb-emulator 
```

応答は次のようになります。

```
Starting Emulator
Emulator Endpoint: https://172.20.229.193:8081/
Master Key: C2y6yDjf5/R+ob0N8A7Cgv30VRDJIWEHLM+4QDU5DE2nQ9nDuVTqobD4b8mGGyPMbIZnqyMsEcaGQy67XIw/Jw==
Exporting SSL Certificate
You can import the SSL certificate from an administrator command prompt on the host by running:
cd /d %LOCALAPPDATA%\CosmosDBEmulatorCert
powershell .\importcert.ps1
--------------------------------------------------------------------------------------------------
Starting interactive shell
``` 

Emulator の起動後に対話型シェルを閉じると、Emulator のコンテナーがシャットダウンします。

クライアントで応答からのエンドポイントとマスター_キーを使用して、SSL 証明書をホストにインポートします。 SSL 証明書をインポートするには、admin コマンド プロンプトから以下を実行します。

```
cd %LOCALAPPDATA%\CosmosDBEmulatorCert
powershell .\importcert.ps1
```


## <a name="start-the-emulator"></a>エミュレーターの起動

Azure Cosmos DB Emulator を起動するには、[スタート] ボタンをクリックするか、Windows キーを押します。 「**Azure Cosmos DB Emulator**」と入力して、アプリケーションの一覧からエミュレーターを選択します。 

![[スタート] ボタンをクリックするか Windows キーを押して「**Azure Cosmos DB Emulator**」と入力し、アプリケーションの一覧からエミュレーターを選択する](./media/local-emulator/database-local-emulator-start.png)

エミュレーターの実行中は、Windows タスク バーの通知領域にアイコンが表示されます。 ![Azure Cosmos DB ローカル エミュレーターのタスク バーの通知](./media/local-emulator/database-local-emulator-taskbar.png)

Azure Cosmos DB Emulator は既定では、ポート 8081 でリッスンしているローカル コンピューター ("localhost") で実行されます。

Azure Cosmos DB Emulator は既定では `C:\Program Files\Azure Cosmos DB Emulator` ディレクトリにインストールされます。 コマンドラインを使ってエミュレーターを起動したり停止したりすることもできます。 詳細については、[コマンドライン ツールのリファレンス](#command-line)に関するセクションを参照してください。

## <a name="start-data-explorer"></a>データ エクスプローラーの起動

Azure Cosmos DB Emulator が起動すると、ブラウザーで Azure Cosmos DB データ エクスプローラーが自動的に開きます。 アドレスは、[https://localhost:8081/_explorer/index.html](https://localhost:8081/_explorer/index.html) になります。 エクスプローラーを閉じた後にもう一度開きたくなった場合は、ブラウザーで URL を開くか、次に示すように Windows トレイ アイコンの Azure Cosmos DB Emulator から起動することができます。

![Azure Cosmos DB ローカル エミュレーターのデータ エクスプローラー起動ツール](./media/local-emulator/database-local-emulator-data-explorer-launcher.png)

## <a name="checking-for-updates"></a>更新プログラムの確認
データ エクスプローラーでは、ダウンロード可能な新しい更新プログラムがあるかどうかが示されます。 

> [!NOTE]
> Azure Cosmos DB Emulator のあるバージョンで作成したデータは、他のバージョンを使用してアクセスできない可能性があります。 データを永続化して長期にわたって保持する必要がある場合、そのデータは Azure Cosmos DB Emulator ではなく Azure Cosmos DB アカウントに格納することをお勧めします。 

## <a name="authenticating-requests"></a>要求の認証
クラウドの Azure Cosmos DB と同様に、Azure Cosmos DB Emulator に対する各要求は認証される必要があります。 Azure Cosmos DB Emulator では、マスター キー認証について、単一の固定アカウントと既知の認証キーがサポートされています。 Azure Cosmos DB Emulator ではこのアカウントとキーのみが資格情報として使用できます。 次に例を示します。

    Account name: localhost:<port>
    Account key: C2y6yDjf5/R+ob0N8A7Cgv30VRDJIWEHLM+4QDU5DE2nQ9nDuVTqobD4b8mGGyPMbIZnqyMsEcaGQy67XIw/Jw==

> [!NOTE]
> Azure Cosmos DB Emulator でサポートされているマスター キーは、エミュレーターで使用することのみを目的としています。 Azure Cosmos DB Emulator で運用環境の Azure Cosmos DB アカウントとキーを使用することはできません。 

> [!NOTE] 
> /Key オプションを使用してエミュレーターを開始した場合は、"C2y6yDjf5/R+ob0N8A7Cgv30VRDJIWEHLM+4QDU5DE2nQ9nDuVTqobD4b8mGGyPMbIZnqyMsEcaGQy67XIw/Jw==" の代わりに生成されたキーを使用します。

さらに、Azure Cosmos DB サービスと同様に、Azure Cosmos DB Emulator では SSL 経由のセキュリティ保護された通信のみサポートされています。

## <a name="running-the-emulator-on-a-local-network"></a>ローカル ネットワークでのエミュレーターの実行

ローカル ネットワークでのエミュレーターを実行できます。 ネットワーク アクセスを有効にするには、[コマンド ライン](#command-line-syntax)で /AllowNetworkAccess オプションを指定します。また、/Key=key_string または /KeyFile=file_name を指定する必要もあります。 /GenKeyFile=file_name を使用すると、ランダム キーでファイルを事前に生成できます。  その後、/KeyFile=file_name または /Key=contents_of_file に渡すことができます。

ネットワーク アクセスを有効にするのが初めての場合、ユーザーはエミュレーターをシャットダウンし、エミュレーターのデータ ディレクトリ (C:\Users\user_name\AppData\Local\CosmosDBEmulator) を削除する必要があります。

## <a name="developing-with-the-emulator"></a>DocumentDB Emulator を使用した開発
デスクトップで Azure Cosmos DB Emulator を実行している間、サポートされている [Azure Cosmos DB SDK](documentdb-sdk-dotnet.md) または [Azure Cosmos DB REST API](/rest/api/documentdb/) を使用して Emulator を操作できます。 Azure Cosmos DB Emulator にはデータ エクスプローラーも組み込まれており、コードを記述することなく、DocumentDB API と MongoDB API のコレクションを作成したり、ドキュメントの表示と編集を行ったりできます。   

    // Connect to the Azure Cosmos DB Emulator running locally
    DocumentClient client = new DocumentClient(
        new Uri("https://localhost:8081"), 
        "C2y6yDjf5/R+ob0N8A7Cgv30VRDJIWEHLM+4QDU5DE2nQ9nDuVTqobD4b8mGGyPMbIZnqyMsEcaGQy67XIw/Jw==");

[MongoDB 向けの Azure Cosmos DB プロトコル サポート](mongodb-introduction.md)を使用している場合、次の接続文字列を使用してください。

    mongodb://localhost:C2y6yDjf5/R+ob0N8A7Cgv30VRDJIWEHLM+4QDU5DE2nQ9nDuVTqobD4b8mGGyPMbIZnqyMsEcaGQy67XIw/Jw==@localhost:10255/admin?ssl=true

Azure Cosmos DB Emulator には、[Azure DocumentDB Studio](https://github.com/mingaliu/DocumentDBStudio) などの既存のツールを使用して接続できます。 [Azure Cosmos DB データ移行ツール](https://github.com/azure/azure-documentdb-datamigrationtool)を使用して、Azure Cosmos DB Emulator と Azure Cosmos DB サービスの間でデータを移行することもできます。

> [!NOTE] 
> /Key オプションを使用してエミュレーターを開始した場合は、"C2y6yDjf5/R+ob0N8A7Cgv30VRDJIWEHLM+4QDU5DE2nQ9nDuVTqobD4b8mGGyPMbIZnqyMsEcaGQy67XIw/Jw==" の代わりに生成されたキーを使用します。

Azure Cosmos DB Emulator を使用して、既定で最大 25 個の単一パーティション コレクションまたは 1 つのパーティション分割コレクションを作成できます。 この値を変更する方法の詳細については、[PartitionCount 値の設定](#set-partitioncount)に関するトピックを参照してください。

## <a name="export-the-ssl-certificate"></a>SSL 証明書のエクスポート

.NET 言語とランタイムでは、Azure Cosmos DB ローカル エミュレーターに安全に接続するために Windows 証明書ストアが使用されます。 その他の言語では、証明書の管理と使用について独自の方法があります。 Java の場合は独自の[証明書ストア](https://docs.oracle.com/cd/E19830-01/819-4712/ablqw/index.html)が使用され、Python の場合は[ソケット ラッパー](https://docs.python.org/2/library/ssl.html)が使用されます。

Windows 証明書ストアと統合されていない言語およびランタイムで使用する証明書を取得するためには、Windows 証明書マネージャーを使用して証明書をエクスポートする必要があります。 これは certlm.msc を実行することで開始できます。または、「[Azure Cosmos DB Emulator 証明書のエクスポート](./local-emulator-export-ssl-certificates.md)」の手順に従ってください。 証明書マネージャーの実行中、次に示すように Personal フォルダーの Certificates を開き、"DocumentDBEmulatorCertificate" というフレンドリ名の証明書を BASE-64 encoded X.509 (.cer) ファイルとしてエクスポートします。

![Azure Cosmos DB ローカル エミュレーターの SSL 証明書](./media/local-emulator/database-local-emulator-ssl_certificate.png)

X.509 証明書を Java 証明書ストアにインポートするには、「[証明書を Java CA 証明書ストアに追加する方法](https://docs.microsoft.com/azure/java-add-certificate-ca-store)」の手順に従います。 証明書を証明書ストアにインポートしたら、Java アプリケーションと MongoDB アプリケーションは Azure Cosmos DB Emulator に接続できるようになります。

Python SDK および Node.js SDK からエミュレーターに接続すると、SSL 検証が無効になります。

## <a id="command-line"></a> コマンドライン ツールのリファレンス
インストール先では、コマンドラインを使用することで、エミュレーターの開始と停止やオプションの構成などの操作を実行できます。

### <a name="command-line-syntax"></a>コマンドライン構文

    CosmosDB.Emulator.exe [/Shutdown] [/DataPath] [/Port] [/MongoPort] [/DirectPorts] [/Key] [/EnableRateLimiting] [/DisableRateLimiting] [/NoUI] [/NoExplorer] [/?]

オプションの一覧を表示するには、コマンド プロンプトで「 `CosmosDB.Emulator.exe /?` 」と入力します。

<table>
<tr>
  <td><strong>オプション</strong></td>
  <td><strong>説明</strong></td>
  <td><strong>コマンド</strong></td>
  <td><strong>引数</strong></td>
</tr>
<tr>
  <td>[引数なし]</td>
  <td>既定の設定で Azure Cosmos DB Emulator を開始します。</td>
  <td>CosmosDB.Emulator.exe</td>
  <td></td>
</tr>
<tr>
  <td>[Help]</td>
  <td>サポートされるコマンド ライン引数の一覧を表示します。</td>
  <td>CosmosDB.Emulator.exe /?</td>
  <td></td>
</tr>
<tr>
  <td>Shutdown</td>
  <td>Azure Cosmos DB Emulator をシャットダウンします。</td>
  <td>CosmosDB.Emulator.exe /Shutdown</td>
  <td></td>
</tr>
<tr>
  <td>DataPath</td>
  <td>データ ファイルを格納するパスを指定します。 既定値は %LocalAppdata%\CosmosDBEmulator です。</td>
  <td>CosmosDB.Emulator.exe /DataPath=&lt;datapath&gt;</td>
  <td>&lt;datapath&gt;: アクセスできるパス</td>
</tr>
<tr>
  <td>ポート</td>
  <td>エミュレーターで使用するポート番号を指定します。  既定値は 8081 です。</td>
  <td>CosmosDB.Emulator.exe /Port=&lt;port&gt;</td>
  <td>&lt;port&gt;: 単一のポート番号</td>
</tr>
<tr>
  <td>MongoPort</td>
  <td>MongoDB 互換性 API に使用するポート番号を指定します。 既定値は 10255 です。</td>
  <td>CosmosDB.Emulator.exe /MongoPort=&lt;mongoport&gt;</td>
  <td>&lt;mongoport&gt;: 単一のポート番号</td>
</tr>
<tr>
  <td>DirectPorts</td>
  <td>直接接続に使用するポートを指定します。 既定値は、10251、10252、10253、10254 です。</td>
  <td>CosmosDB.Emulator.exe /DirectPorts:&lt;directports&gt;</td>
  <td>&lt;directports&gt;: 4 つのポートのコンマ区切りリスト</td>
</tr>
<tr>
  <td>キー</td>
  <td>エミュレーターの承認キーです。 キーは、64 バイト ベクトルの Base 64 エンコーディングが施されている必要があります。</td>
  <td>CosmosDB.Emulator.exe /Key:&lt;key&gt;</td>
  <td>&lt;key&gt;: キーは、64 バイト ベクトルの Base 64 エンコーディングが施されている必要があります。</td>
</tr>
<tr>
  <td>EnableRateLimiting</td>
  <td>要求レート制限の動作の有効化を指定します。</td>
  <td>CosmosDB.Emulator.exe /EnableRateLimiting</td>
  <td></td>
</tr>
<tr>
  <td>DisableRateLimiting</td>
  <td>要求レート制限の動作の無効化を指定します。</td>
  <td>CosmosDB.Emulator.exe /DisableRateLimiting</td>
  <td></td>
</tr>
<tr>
  <td>NoUI</td>
  <td>エミュレーターのユーザー インターフェイスを表示しません。</td>
  <td>CosmosDB.Emulator.exe /NoUI</td>
  <td></td>
</tr>
<tr>
  <td>NoExplorer</td>
  <td>起動時にドキュメント エクスプローラーを表示しません。</td>
  <td>CosmosDB.Emulator.exe /NoExplorer</td>
  <td></td>
</tr>
<tr>
  <td>PartitionCount</td>
  <td>パーティション分割コレクションの最大数を指定します。 詳細については、「[コレクションの数を変更する](#set-partitioncount)」を参照してください。</td>
  <td>CosmosDB.Emulator.exe /PartitionCount=&lt;partitioncount&gt;</td>
  <td>&lt;partitioncount&gt;: 単一パーティション コレクションで許容される最大の数です。 既定値は 25 です。 許容される最大値は 250 です。</td>
</tr>
<tr>
  <td>DefaultPartitionCount</td>
  <td>パーティション分割コレクションの既定のパーティション数を指定します。</td>
  <td>CosmosDB.Emulator.exe /DefaultPartitionCount=&lt;defaultpartitioncount&gt;</td>
  <td>&lt;defaultpartitioncount&gt; 既定値は 25 です。</td>
</tr>
<tr>
  <td>AllowNetworkAccess</td>
  <td>ネットワーク上でのエミュレーターへのアクセスを有効にします。 /Key=&lt;key_string&gt; または /KeyFile=&lt;file_name&gt; を渡して、ネットワーク アクセスを有効にする必要があります。</td>
  <td>CosmosDB.Emulator.exe /AllowNetworkAccess /Key=&lt;key_string&gt;<br><br>または<br><br>CosmosDB.Emulator.exe /AllowNetworkAccess /KeyFile=&lt;file_name&gt;</td>
  <td></td>
</tr>
<tr>
  <td>NoFirewall</td>
  <td>/AllowNetworkAccess が使用されているときは、ファイアウォール ルールを調整しないでください。</td>
  <td>CosmosDB.Emulator.exe /NoFirewall</td>
  <td></td>
</tr>
<tr>
  <td>GenKeyFile</td>
  <td>新しい承認キーを生成し、指定したファイルに保存します。 生成されたキーは、/Key オプションまたは/KeyFile オプションで使用できます。</td>
  <td>CosmosDB.Emulator.exe  /GenKeyFile=&lt;キー ファイルへのパス&gt;</td>
  <td></td>
</tr>
<tr>
  <td>整合性</td>
  <td>アカウントの既定の一貫性レベルを設定します。</td>
  <td>CosmosDB.Emulator.exe /Consistency=&lt;consistency&gt;</td>
  <td>&lt;consistency&gt;: [一貫性レベル](consistency-levels.md)の値には、Session、Strong、Eventual、BoundedStaleness のいずれかを使用する必要があります。  既定値は Session です。</td>
</tr>
<tr>
  <td>?</td>
  <td>ヘルプ メッセージを表示します。</td>
  <td></td>
  <td></td>
</tr>
</table>

## <a name="differences-between-the-azure-cosmos-db-emulator-and-azure-cosmos-db"></a>Azure Cosmos DB Emulator と Azure Cosmos DB の違い 
Azure Cosmos DB Emulator は環境をエミュレートし、ローカルの開発者ワークステーションで実行するものであり、このエミュレーターとクラウドの Azure Cosmos DB アカウントには機能にいくつかの違いがあります。

* Azure Cosmos DB Emulator は、1 つの固定アカウントと既知のマスター キーのみに対応しています。  Azure Cosmos DB Emulator では、キーを再生成できません。
* Azure Cosmos DB Emulator は拡張可能なサービスではなく、大量のコレクションをサポートはありません。
* Azure Cosmos DB Emulator は異なる [Azure Cosmos DB 整合性レベル](consistency-levels.md)をシミュレートしません。
* Azure Cosmos DB Emulator は[複数リージョンのレプリケーション](distribute-data-globally.md)をシミュレートしません。
* Azure Cosmos DB Emulator は Azure Cosmos DB サービスで利用できたサービス割り当てオーバーライド (ドキュメントのサイズ上限やパーティション分割コレクション ストレージ増加など) をサポートしません。
* ご利用の Azure Cosmos DB Emulator に Azure Cosmos DB サービスの最近の変更が反映されていない場合があるため、[Azure Cosmos DB Capacity Planner](https://www.documentdb.com/capacityplanner) を利用し、アプリケーションの実稼働スループット (RU) ニーズを正確に見積もってください。

## <a id="set-partitioncount"></a>コレクションの数を変更する

既定では、Azure Cosmos DB Emulator を利用し、最大 25 個の単一パーティション コレクションまたは 1 つのパーティション分割コレクションを作成できます。 **PartitionCount** の値を変更することにより、最大 250 個の単一パーティション コレクション、10 個のパーティション分割コレクション、または単一パーティションの数が 250 個を超えない範囲でこの 2 つの任意の組み合わせを作成できます (1 個のパーティション分割コレクション = 25 個の単一パーティション コレクション)。

現在のパーティション数を超えた後にコレクションを作成しようとすると、次のメッセージと共に ServiceUnavailable 例外がスローされます。

    Sorry, we are currently experiencing high demand in this region, 
    and cannot fulfill your request at this time. We work continuously 
    to bring more and more capacity online, and encourage you to try again. 
    Please do not hesitate to email docdbswat@microsoft.com at any time or 
    for any reason. ActivityId: 29da65cc-fba1-45f9-b82c-bf01d78a1f91

Azure Cosmos DB Emulator で利用できるコレクションの数は次のように変更します。

1. システム トレイの **Azure Cosmos DB Emulator** アイコンを右クリックし、**[Reset Data…]\(データのリセット…\)** をクリックし、ローカルの Azure Cosmos DB Emulator データをすべて削除します。
2. このフォルダー (C:\Users\user_name\AppData\Local\CosmosDBEmulator) 内のすべてのエミュレーター データを削除します。
3. システム トレイの **Azure Cosmos DB Emulator** アイコンを右クリックし、**[終了]** をクリックし、開いているインスタンスをすべて終了します。 すべてのインスタンスが終了するまでしばらく時間がかかる場合があります。
4. 最新版の [Azure Cosmos DB Emulator](https://aka.ms/cosmosdb-emulator) をインストールします。
5. PartitionCount フラグの値を 250 以下に設定して、エミュレーターを起動します。 (例: `C:\Program Files\Azure CosmosDB Emulator>CosmosDB.Emulator.exe /PartitionCount=100`)。

## <a name="troubleshooting"></a>トラブルシューティング

Azure Cosmos DB Emulator で遭遇する問題の解決には次のヒントが役立ちます。

- 新しいバージョンのエミュレーターをインストールしてエラーが発生している場合は、データをリセットしていることを確認します。 システム トレイの Azure Cosmos DB Emulator アイコンを右クリックし、[Reset Data….]\(データのリセット….\) をクリックすると、データをリセットできます。 それでエラーが解決しない場合は、アプリをアンインストールして、再インストールします。 手順については、「[ローカル エミュレーターのアンインストール](#uninstall)」をご覧ください。

- Azure Cosmos DB Emulator がクラッシュした場合、c:\Users\user_name\AppData\Local\CrashDumps フォルダーからダンプ ファイルを回収し、メールに添付して [askcosmosdb@microsoft.com](mailto:askcosmosdb@microsoft.com) に送信してください。

- CosmosDB.StartupEntryPoint.exe でクラッシュが発生した場合は、管理者コマンド プロンプトから `lodctr /R` コマンドを実行します。 

- 接続の問題が発生した場合は、[トレース ファイルを収集](#trace-files)し、それらを圧縮して、[askcosmosdb@microsoft.com](mailto:askcosmosdb@microsoft.com) への電子メールに添付します。

- "**サービス利用不可**" というメッセージが表示された場合、エミュレーターがネットワーク スタックの初期化に失敗している可能性があります。 Pulse Secure クライアントまたは Juniper Networks クライアントのネットワーク フィルター ドライバーが問題の原因である可能性があるため、これらのクライアントがインストールされているかどうかを確認してください。 通常、サード パーティのネットワーク フィルター ドライバーをアンインストールすると、問題が解決されます。

### <a id="trace-files"></a>トレース ファイルの収集

デバッグ トレースを収集するには、管理コマンド プロンプトから次のコマンドを実行します。

1. `cd /d "%ProgramFiles%\Azure Cosmos DB Emulator"`
2. `CosmosDB.Emulator.exe /shutdown`」を参照してください。 プログラムがシャットダウンしたことをシステム トレイで確認します。シャットダウンに 1 分かかる場合があります。 Azure Cosmos DB Emulator のユーザー インターフェイスで **[終了]** をクリックすることもできます。
3. `CosmosDB.Emulator.exe /starttraces`
4. `CosmosDB.Emulator.exe`
5. 問題を再現します。 データ エクスプローラーが動作していない場合は、エラーを捕捉するために、ブラウザーが開くまで数秒間待つだけです。
5. `CosmosDB.Emulator.exe /stoptraces`
6. `%ProgramFiles%\Azure Cosmos DB Emulator` に移動し、docdbemulator_000001.etl ファイルを見つけます。
7. デバッグのために、再現手順と共に .etl ファイルを [askcosmosdb@microsoft.com](mailto:askcosmosdb@microsoft.com) に送付します。

### <a id="uninstall"></a>ローカル エミュレーターのアンインストール

1. システム トレイの Azure Cosmos DB Emulator アイコンを右クリックし、[終了] をクリックし、開いているローカル エミュレーターのインスタンスをすべて終了します。 すべてのインスタンスが終了するまでしばらく時間がかかる場合があります。
2. Windows 検索ボックスに「**アプリと機能**」と入力し、**アプリと機能 (システム設定)** の検索結果をクリックします。
3. アプリの一覧で、**Azure Cosmos DB Emulator** までスクロールして選択し、**[アンインストール]** をクリックし、確認して再度、**[アンインストール]** をクリックします。
4. アプリがアンインストールされたら、C:\Users\<user>\AppData\Local\CosmosDBEmulator に移動し、フォルダーを削除します。 

## <a name="next-steps"></a>次のステップ

このチュートリアルでは、次の手順を行いました。

> [!div class="checklist"]
> * ローカル エミュレーターをインストールした
> * Docker for Windows でエミュレーターを実行した
> * 要求を認証した
> * エミュレーターでデータ エクスプローラーを使用した
> * SSL 証明書をエクスポートした
> * コマンド ラインからエミュレーターを呼び出した
> * トレース ファイルを収集した

このチュートリアルでは、無料のローカル開発のためにローカル エミュレーターを使用する方法について学習しました。 これで次のチュートリアルに進み、Emulator SSL 証明書をエクスポートする方法について学習できます。 

> [!div class="nextstepaction"]
> [Azure Cosmos DB Emulator 証明書をエクスポートする](local-emulator-export-ssl-certificates.md)

