---
title: Azure Cosmos Emulator を使用してローカルで開発する
description: Azure Cosmos Emulator を使用すると、Azure サブスクリプションを作成せずに無料で、アプリケーションの開発とテストをローカルで行うことができます。
ms.service: cosmos-db
ms.topic: tutorial
author: markjbrown
ms.author: mjbrown
ms.date: 07/16/2019
ms.openlocfilehash: 3a03829c39deb954a8baa908de63b9ff6f31238e
ms.sourcegitcommit: a8b638322d494739f7463db4f0ea465496c689c6
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/17/2019
ms.locfileid: "68297848"
---
# <a name="use-the-azure-cosmos-emulator-for-local-development-and-testing"></a>ローカルでの開発とテストに Azure Cosmos Emulator を使用する

Azure Cosmos Emulator は、開発目的で Azure Cosmos DB サービスをエミュレートするローカル環境を提供するものです。 Azure Cosmos Emulator を使用すると、ローカルでのアプリケーションの開発とテストを、Azure サブスクリプションを作成したりコストをかけたりせずに実施できます。 Azure Cosmos Emulator でのアプリケーションの動作に満足できたら、クラウドでの Azure Cosmos アカウントの使用に切り替えることができます。

Azure Cosmos Emulator を使った開発には、[SQL](local-emulator.md#sql-api)、[Cassandra](local-emulator.md#cassandra-api)、[MongoDB](local-emulator.md#azure-cosmos-dbs-api-for-mongodb)、[Gremlin](local-emulator.md#gremlin-api)、[Table](local-emulator.md#table-api) の API アカウントを利用できます。 ただし、現時点でエミュレーター内のデータ エクスプローラー ビューで完全にサポートされるのは、SQL API のクライアントのみです。 

## <a name="how-the-emulator-works"></a>エミュレーターの機能

Azure Cosmos Emulator には、Azure Cosmos DB サービスの高忠実度エミュレーションが用意されています。 データの作成とクエリ、コンテナーのプロビジョニングとスケーリング、ストアド プロシージャとトリガーの実行のサポートなど、Azure Cosmos DB と同じ機能がサポートされています。 Azure Cosmos Emulator を使用してアプリケーションの開発とテストを行い、Azure Cosmos DB 用の接続エンドポイントの構成を 1 つ変更するだけで、世界規模で Azure にデプロイすることができます。

Azure Cosmos DB サービスのエミュレーションは忠実ですが、エミュレーターの実装はサービスとは異なります。 たとえば、エミュレーターでは、永続化用のローカル ファイル システムや接続用の HTTPS プロトコル スタックなど、標準的な OS コンポーネントが使用されます。 グローバル レプリケーション、読み取り/書き取りの 10 ミリ秒を下回る待ち時間、調整可能な一貫性レベルなど、Azure インフラストラクチャに依存する機能は適用されません。

[Azure Cosmos DB データ移行ツール](https://github.com/azure/azure-documentdb-datamigrationtool)を使用すると、Azure Cosmos Emulator と Azure Cosmos DB サービスの間でデータを移行できます。

Azure Cosmos Emulator は Windows Docker コンテナー上で実行できます。docker pull コマンドについては [Docker Hub](https://hub.docker.com/r/microsoft/azure-cosmosdb-emulator/)、エミュレーターのソース コードについては [GitHub](https://github.com/Azure/azure-cosmos-db-emulator-docker) を、それぞれ参照してください。

## <a name="differences-between-the-emulator-and-the-service"></a>エミュレーターとサービスの違い

Azure Cosmos Emulator は、ローカルの開発者ワークステーションに環境をエミュレートして提供するものであるため、このエミュレーターとクラウドにある Azure Cosmos アカウントとの間には機能にいくつかの違いがあります。

* エミュレーターのデータ エクスプローラーでは現在、SQL API 用のクライアントがサポートされています。 MongoDB API、Table API、Graph API、Cassandra API など、Azure Cosmos DB 用の API については、データ エクスプローラーのビューと操作が完全にはサポートされていません。
* Azure Cosmos Emulator は、1 つの固定アカウントと既知のマスター キーのみに対応しています。 Azure Cosmos Emulator ではキーを登録できませんが、コマンドライン オプションを使って既定のキーを変更することは可能です。
* Azure Cosmos Emulator はスケーラブルなサービスではなく、大量のコンテナーはサポートされません。
* Azure Cosmos Emulator では、異なる [Azure Cosmos DB 整合性レベル](consistency-levels.md)を利用できません。
* Azure Cosmos Emulator では、[複数リージョンのレプリケーション](distribute-data-globally.md)を利用できません。
* ご使用の Azure Cosmos Emulator には Azure Cosmos DB サービスの最近の変更が反映されていないことがあるため、[Azure Cosmos DB Capacity Planner](https://www.documentdb.com/capacityplanner) を参照し、アプリケーションの運用スループット (RU) のニーズを正確に見積もる必要があります。
* Azure Cosmos Emulator を使用する場合、作成できるコンテナーの数の既定値は、固定サイズのコンテナーであれば 25 個 (Azure Cosmos DB SDK を使用した場合にのみサポート)、容量無制限のコンテナーであれば 5 個までです。 この値を変更する方法の詳細については、[PartitionCount 値の設定](#set-partitioncount)に関するトピックを参照してください。

## <a name="system-requirements"></a>システム要件

Azure Cosmos Emulator のハードウェア要件とソフトウェア要件は、次のとおりです。

* ソフトウェア要件
  * Windows Server 2012 R2、Windows Server 2016、または Windows 10
  * 64 ビット オペレーティング システム
* 最小ハードウェア要件
  * 2 GB の RAM
  * 10 GB のハードディスク空き容量

## <a name="installation"></a>インストール

[Microsoft ダウンロード センター](https://aka.ms/cosmosdb-emulator)から Azure Cosmos Emulator をダウンロードしてインストールできます。または、Docker for Windows でこのエミュレーターを実行できます。 Docker for Windows でエミュレーターを使用する方法については、「[Docker で実行する](#running-on-docker)」を参照してください。

> [!NOTE]
> Azure Cosmos Emulator をインストール、構成、実行するには、コンピューターの管理特権が必要です。 エミュレーターでは、サービスを実行するために証明書の作成/追加と、ファイアウォール規則の設定が行われます。このため、エミュレーターがそのような操作を実行できるようになっている必要があります。

## <a name="running-on-windows"></a>Windows で実行する

Azure Cosmos Emulator を起動するには、[スタート] ボタンをクリックするか、Windows キーを押します。 「**Azure Cosmos Emulator**」と入力して、アプリケーションの一覧からエミュレーターを選択します。

![[スタート] ボタンをクリックするか Windows キーを押して「**Azure Cosmos Emulator**」と入力し、アプリケーションの一覧からエミュレーターを選択する](./media/local-emulator/database-local-emulator-start.png)

エミュレーターの実行中は、Windows タスク バーの通知領域にアイコンが表示されます。 ![Azure Cosmos DB ローカル エミュレーターのタスク バーの通知](./media/local-emulator/database-local-emulator-taskbar.png)

Azure Cosmos Emulator は、既定ではポート 8081 でリッスンしているローカル コンピューター ("localhost") で実行されます。

Azure Cosmos Emulator は、既定では `C:\Program Files\Azure Cosmos DB Emulator` にインストールされます。 コマンドラインを使ってエミュレーターを起動したり停止したりすることもできます。 詳しくは、「[コマンドライン ツールのリファレンス](#command-line)」をご覧ください。

## <a name="start-data-explorer"></a>データ エクスプローラーの起動

Azure Cosmos Emulator が起動すると、ブラウザーで Azure Cosmos データ エクスプローラーが自動的に開きます。 アドレスは `https://localhost:8081/_explorer/index.html` として表示されます。 エクスプローラーを閉じた後にもう一度開く場合は、ブラウザーでこの URL を開くか、次に示すように Windows トレイ アイコンの Azure Cosmos Emulator から起動できます。

![Azure Cosmos ローカル エミュレーターのデータ エクスプローラー起動ツール](./media/local-emulator/database-local-emulator-data-explorer-launcher.png)

## <a name="checking-for-updates"></a>更新プログラムの確認

データ エクスプローラーでは、ダウンロード可能な新しい更新プログラムがあるかどうかが示されます。

> [!NOTE]
> Azure Cosmos Emulator のあるバージョンで作成したデータ (%LOCALAPPDATA%\CosmosDBEmulator またはデータ パスに関するオプション設定を参照してください) は、他のバージョンを使用してアクセスできない可能性があります。 データを永続化して長期にわたって保持する必要がある場合、そのデータは Azure Cosmos Emulator ではなく Azure Cosmos アカウントに格納することをお勧めします。

## <a name="authenticating-requests"></a>要求の認証

クラウドの Azure Cosmos DB と同様に、Azure Cosmos Emulator に対する要求にはいずれも認証が必要です。 Azure Cosmos Emulator では、マスター キー認証について、単一の固定アカウントと既知の認証キーがサポートされています。 Azure Cosmos Emulator ではこのアカウントとキーのみ、資格情報として使用できます。 次に例を示します。

```bash
Account name: localhost:<port>
Account key: C2y6yDjf5/R+ob0N8A7Cgv30VRDJIWEHLM+4QDU5DE2nQ9nDuVTqobD4b8mGGyPMbIZnqyMsEcaGQy67XIw/Jw==
```

> [!NOTE]
> Azure Cosmos Emulator でサポートされているマスター キーは、エミュレーターで使用することのみを想定したものです。 Azure Cosmos Emulator で運用環境の Azure Cosmos DB アカウントとキーを使用することはできません。

> [!NOTE]
> /Key オプションを使用してエミュレーターを開始した場合は、`C2y6yDjf5/R+ob0N8A7Cgv30VRDJIWEHLM+4QDU5DE2nQ9nDuVTqobD4b8mGGyPMbIZnqyMsEcaGQy67XIw/Jw==` の代わりに生成されたキーを使用します。 /Key オプションの詳細については、「[コマンドライン ツールのリファレンス](#command-line-syntax)」を参照してください。

Azure Cosmos DB と同様に、Azure Cosmos Emulator では SSL 経由のセキュリティ保護された通信のみサポートされています。

## <a name="running-on-a-local-network"></a>ローカル ネットワークで実行する

ローカル ネットワークでのエミュレーターを実行できます。 ネットワーク アクセスを有効にするには、[コマンド ライン](#command-line-syntax)で `/AllowNetworkAccess` オプションを指定します。また、`/Key=key_string` または `/KeyFile=file_name` を指定する必要もあります。 `/GenKeyFile=file_name` を使用すると、ランダムなキーを備えたファイルを事前に生成できます。 その後、それを `/KeyFile=file_name` または `/Key=contents_of_file` に渡すことができます。

ネットワーク アクセスを初めて有効にする場合は、エミュレーターをシャットダウンし、エミュレーターのデータ ディレクトリ (%LOCALAPPDATA%\CosmosDBEmulator) を削除する必要があります。

## <a name="developing-with-the-emulator"></a>エミュレーターを使用した開発

### <a name="sql-api"></a>SQL API

デスクトップで Azure Cosmos Emulator が実行中になったら、サポートされている [Azure Cosmos DB SDK](sql-api-sdk-dotnet.md) または [Azure Cosmos DB REST API](/rest/api/cosmos-db/) を使用してエミュレーターを操作できます。 Azure Cosmos Emulator にはデータ エクスプローラーも組み込まれており、コードを記述することなく、SQL API または Cosmos DB for MongoDB API 用のコンテナーを作成したり、項目の表示と編集を行ったりできます。

```csharp
// Connect to the Azure Cosmos Emulator running locally
DocumentClient client = new DocumentClient(
   new Uri("https://localhost:8081"), "C2y6yDjf5/R+ob0N8A7Cgv30VRDJIWEHLM+4QDU5DE2nQ9nDuVTqobD4b8mGGyPMbIZnqyMsEcaGQy67XIw/Jw==");

```

### <a name="azure-cosmos-dbs-api-for-mongodb"></a>Azure Cosmos DB の MongoDB 用 API

[Azure Cosmos DB for MongoDB の API](mongodb-introduction.md) を使用している場合は、次の接続文字列を使用します。

```bash
mongodb://localhost:C2y6yDjf5/R+ob0N8A7Cgv30VRDJIWEHLM+4QDU5DE2nQ9nDuVTqobD4b8mGGyPMbIZnqyMsEcaGQy67XIw/Jw==@localhost:10255/admin?ssl=true
```

### <a name="table-api"></a>テーブル API

デスクトップで Azure Cosmos Emulator が実行中になったら、[Azure Cosmos DB Table API SDK](table-storage-how-to-use-dotnet.md) を使用してエミュレーターを操作できます。 コマンド プロンプトから管理者としてエミュレーターを起動します。その際、一緒に "/EnableTableEndpoint" を指定します。 続いて、次のコードを実行して Table API アカウントに接続します。

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

管理者のコマンド プロンプトからエミュレーターを起動します。その際、一緒に "/EnableCassandraEndpoint" を指定します。 代わりに、環境変数 `AZURE_COSMOS_EMULATOR_CASSANDRA_ENDPOINT=true` を設定することもできます。

* [Python 2.7 をインストールします](https://www.python.org/downloads/release/python-2716/)

* [Cassandra CLI/CQLSH をインストールします](https://cassandra.apache.org/download/)

* 通常のコマンド プロンプト ウィンドウで次のコマンドを実行します。

  ```bash
  set Path=c:\Python27;%Path%
  cd /d C:\sdk\apache-cassandra-3.11.3\bin
  set SSL_VERSION=TLSv1_2
  set SSL_VALIDATE=false
  cqlsh localhost 10350 -u localhost -p C2y6yDjf5/R+ob0N8A7Cgv30VRDJIWEHLM+4QDU5DE2nQ9nDuVTqobD4b8mGGyPMbIZnqyMsEcaGQy67XIw/Jw== --ssl
  ```

* CQLSH シェルで次のコマンドを実行し、Cassandra エンドポイントに接続します。

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

管理者のコマンド プロンプトからエミュレーターを起動します。その際、一緒に "/EnableGremlinEndpoint" を指定します。 代わりに、環境変数 `AZURE_COSMOS_EMULATOR_GREMLIN_ENDPOINT=true` を設定することもできます

* [apache-tinkerpop-gremlin-console-3.3.4 をインストールします](https://tinkerpop.apache.org/downloads.html)

* エミュレーター内で、データ エクスプローラーによりデータベース "db1" とコレクション "coll1" が作成されます。パーティション キーには "/name" を選択します

* 通常のコマンド プロンプト ウィンドウで次のコマンドを実行します。

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

* Gremlin シェルで次のコマンドを実行し、Gremlin エンドポイントに接続します。

  ```bash
  :remote connect tinkerpop.server conf/remote-localcompute.yaml
  :remote console
  :> g.V()
  :> g.addV('person1').property(id, '1').property('name', 'somename1')
  :> g.addV('person2').property(id, '2').property('name', 'somename2')
  :> g.V()
  ```

## <a name="export-the-ssl-certificate"></a>SSL 証明書のエクスポート

.NET 言語とランタイムでは、Azure Cosmos DB ローカル エミュレーターに安全に接続するために Windows 証明書ストアが使用されます。 その他の言語では、証明書の管理と使用について独自の方法があります。 Java の場合は独自の[証明書ストア](https://docs.oracle.com/cd/E19830-01/819-4712/ablqw/index.html)が使用され、Python の場合は[ソケット ラッパー](https://docs.python.org/2/library/ssl.html)が使用されます。

Windows 証明書ストアと統合されていない言語およびランタイムで使用する証明書を取得するには、Windows 証明書マネージャーを使用して証明書をエクスポートする必要があります。 これは certlm.msc を実行することで開始できます。または、[Azure Cosmos Emulator 証明書のエクスポート](./local-emulator-export-ssl-certificates.md)に関するページに記載されている手順に従ってください。 証明書マネージャーの実行中、次に示すように Personal フォルダーの Certificates を開き、"DocumentDBEmulatorCertificate" というフレンドリ名の証明書を BASE-64 encoded X.509 (.cer) ファイルとしてエクスポートします。

![Azure Cosmos DB ローカル エミュレーターの SSL 証明書](./media/local-emulator/database-local-emulator-ssl_certificate.png)

X.509 証明書を Java 証明書ストアにインポートするには、「[証明書を Java CA 証明書ストアに追加する方法](https://docs.microsoft.com/azure/java-add-certificate-ca-store)」の手順に従います。 証明書を証明書ストアにインポートすると、SQL API および Azure Cosmos DB の MongoDB 用 API のクライアントが Azure Cosmos Emulator に接続できるようになります。

Python SDK および Node.js SDK からエミュレーターに接続すると、SSL 検証が無効になります。

## <a id="command-line"></a> コマンドライン ツールのリファレンス
インストール先では、コマンドラインを使用することで、エミュレーターの開始と停止やオプションの構成などの操作を実行できます。

### <a name="command-line-syntax"></a>コマンドライン構文

    CosmosDB.Emulator.exe [/Shutdown] [/DataPath] [/Port] [/MongoPort] [/DirectPorts] [/Key] [/EnableRateLimiting] [/DisableRateLimiting] [/NoUI] [/NoExplorer] [/EnableMongoDbEndpoint] [/?]

オプションの一覧を表示するには、コマンド プロンプトで「 `CosmosDB.Emulator.exe /?` 」と入力します。

|**オプション** | **説明** | **コマンド**| **引数**|
|---|---|---|---|
|[引数なし] | 既定の設定で Azure Cosmos Emulator を起動します。 |CosmosDB.Emulator.exe| |
|[Help] |サポートされるコマンド ライン引数の一覧を表示します。|CosmosDB.Emulator.exe /? | |
| GetStatus |Azure Cosmos Emulator の状態を取得します。 この状態は、終了コードで示されます。終了コードは、1 = 開始中、2 = 実行中、3 = 停止済みです。 負の終了コードは、エラーが発生したことを示します。 その他の出力は生成されません。 | CosmosDB.Emulator.exe /GetStatus| |
| Shutdown| Azure Cosmos Emulator をシャットダウンします。| CosmosDB.Emulator.exe /Shutdown | |
|DataPath | データ ファイルを格納するパスを指定します。 既定値は %LocalAppdata%\CosmosDBEmulator です。 | CosmosDB.Emulator.exe /DataPath=\<datapath\> | \<datapath\>:アクセスできるパス |
|Port | エミュレーターで使用するポート番号を指定します。 既定値は 8081 です。 |CosmosDB.Emulator.exe /Port=\<port\> | \<port\>:単一のポート番号 |
| ComputePort | Compute Interop Gateway サービスに使用するポート番号を指定します。 ゲートウェイの HTTP エンドポイント プローブ ポートのポート番号は、ComputePort に 79 を加えた値として計算されます。 このため、ComputePort と ComputePort に 79 を加えた番号のポートは、開いて利用可能な状態にしておく必要があります。 既定値は 8900 と 8979 です。 | CosmosDB.Emulator.exe /ComputePort = \<computeport\> | \<computeport\>:単一のポート番号 |
| EnableMongoDbEndpoint | MongoDB API を有効にします | CosmosDB.Emulator.exe /EnableMongoDbEndpoint | |
| MongoPort | MongoDB 互換性 API に使用するポート番号を指定します。 既定値は 10255 です。 |CosmosDB.Emulator.exe /MongoPort= \<mongoport\>|\<mongoport\>:単一のポート番号|
| EnableCassandraEndpoint | Cassandra API を有効にします | CosmosDB.Emulator.exe /EnableCassandraEndpoint | |
| CassandraPort | Cassandra エンドポイントで使用するポート番号を指定します。 既定値は 10350 です。 | CosmosDB.Emulator.exe /CassandraPort = \<cassandraport\> | \<cassandraport\>:単一のポート番号 |
| EnableGremlinEndpoint | Gremlin API を有効にします | CosmosDB.Emulator.exe /EnableGremlinEndpoint | |
| GremlinPort | Gremlin エンドポイントに使用するポート番号です。 既定値は 8901 です。 | CosmosDB.Emulator.exe /GremlinPort=\<port\> | \<port\>:単一のポート番号 |
|EnableTableEndpoint | Azure Table API を有効にします | CosmosDB.Emulator.exe /EnableTableEndpoint | |
|TablePort | Azure Table エンドポイントに使用するポート番号です。 既定値は 8902 です。 | CosmosDB.Emulator.exe /TablePort=\<port\> | \<port\>:単一のポート番号|
| KeyFile | 指定されたファイルから承認キーを読み取ります。 キーファイルの生成には、/GenKeyFile オプションを使用します | CosmosDB.Emulator.exe /KeyFile=\<file_name\> | \<file_name\>:ファイルへのパス |
| ResetDataPath | 指定されたパスにある全部のファイルを、再帰的に削除します。 パスを指定しなかった場合には、既定値が %LOCALAPPDATA%\CosmosDbEmulator になります | CosmosDB.Emulator.exe /ResetDataPath[=\<path>] | \<path\>:ファイル パス  |
| StartTraces  |  デバッグ トレース ログの収集を開始します。 | CosmosDB.Emulator.exe /StartTraces | |
| StopTraces     | デバッグ トレース ログの収集を停止します。 | CosmosDB.Emulator.exe /StopTraces  | |
|FailOnSslCertificateNameMismatch | 証明書の SAN にエミュレーターのホストのドメイン名、ローカル IPv4 アドレス、"localhost"、"127.0.0.1" が含まれていない場合、既定では、エミュレーターにより自己署名 SSL 証明書が再生成されます。 このオプションを設定すると、その代わりにエミュレーターが起動に失敗します。 その後は /GenCert オプションを使って新しい自己署名 SSL 証明書を作成およびインストールする必要があります。 | CosmosDB.Emulator.exe /FailOnSslCertificateNameMismatch  | |
| GenCert | 新しい自己署名 SSL 証明書を生成およびインストールします。 オプションで、ネットワーク経由でエミュレーターにアクセスするための追加の DNS 名を列挙したコンマ区切りリストを含めることもできます。 | CosmosDB.Emulator.exe /GenCert[ \<comma-separated list of additional dns-names\>] | |
| DirectPorts |直接接続に使用するポートを指定します。 既定値は、10251、10252、10253、10254 です。 | CosmosDB.Emulator.exe /DirectPorts:\<directports\> | \<directports\>:4 つのポートのコンマ区切りリスト |
| キー |エミュレーターの承認キーです。 キーは、64 バイト ベクトルの Base 64 エンコーディングが施されている必要があります。 | CosmosDB.Emulator.exe /Key:\<key\> | \<key\>:キーは、64 バイト ベクトルの Base 64 エンコーディングが施されている必要があります|
| EnableRateLimiting | 要求レート制限の動作の有効化を指定します。 |CosmosDB.Emulator.exe /EnableRateLimiting | |
| DisableRateLimiting |要求レート制限の動作の無効化を指定します。 |CosmosDB.Emulator.exe /DisableRateLimiting | |
| NoUI | エミュレーターのユーザー インターフェイスを表示しません。 | CosmosDB.Emulator.exe /NoUI | |
| NoExplorer | 起動時にデータ エクスプローラーを表示しません。 |CosmosDB.Emulator.exe /NoExplorer | | 
| PartitionCount | パーティション分割されたコンテナーの最大数を指定します。 詳細については、「[コンテナーの数を変更する](#set-partitioncount)」を参照してください。 | CosmosDB.Emulator.exe /PartitionCount=\<partitioncount\> | \<partitioncount\>:許容される単一パーティション コンテナーの最大数です。 既定値は 25 です。 許容される最大値は 250 です。|
| DefaultPartitionCount| パーティション分割されたコンテナーの既定のパーティション数を指定します。 | CosmosDB.Emulator.exe /DefaultPartitionCount=\<defaultpartitioncount\> | \<defaultpartitioncount\> 既定値は 25 です。|
| AllowNetworkAccess | ネットワーク上でのエミュレーターへのアクセスを有効にします。 /Key=\<key_string\> または /KeyFile=\<file_name\> を渡して、ネットワーク アクセスを有効にする必要があります。 | CosmosDB.Emulator.exe /AllowNetworkAccess /Key=\<key_string\> または CosmosDB.Emulator.exe /AllowNetworkAccess /KeyFile=\<file_name\>| |
| NoFirewall | /AllowNetworkAccess オプションが使用されているときは、ファイアウォール規則を調整しないでください。 |CosmosDB.Emulator.exe /NoFirewall | |
| GenKeyFile | 新しい承認キーを生成し、指定したファイルに保存します。 生成されたキーは、/Key オプションまたは/KeyFile オプションで使用できます。 | CosmosDB.Emulator.exe /GenKeyFile=\<キー ファイルへのパス\> | |
| 整合性 | アカウントの既定の一貫性レベルを設定します。 | CosmosDB.Emulator.exe /Consistency=\<consistency\> | \<consistency\>:値は次のいずれかの[一貫性レベル](consistency-levels.md)である必要があります。Session、Strong、Eventual、または BoundedStaleness。 既定値は Session です。 |
| ? | ヘルプ メッセージを表示します。| | |

## <a id="set-partitioncount"></a>コンテナーの数を変更する

Azure Cosmos Emulator で作成できるコンテナーの数の既定値は、固定サイズのコンテナーであれば 25 個 (Azure Cosmos DB SDK を使用した場合にのみサポート)、容量無制限のコンテナーであれば 5 個までです。 **PartitionCount** の値を変更すると、固定サイズのコンテナーであれば 250 個、容量無制限のコンテナーであれば 50 個まで作成できるようになります。この 2 つの組み合わせは、固定サイズのコンテナー 250 個分を超えない範囲であれば、自由に決めることができます (容量無制限のコンテナーは、1 個につき固定サイズのコンテナー 5 個分として計算されます)。 ただし、固定サイズのコンテナーが 200 個以上の状態でエミュレーターを実行する設定にはしないことをお勧めします。 これは、ディスクの IO 操作にオーバーヘッドが加わり、エンドポイント API の使用中に予測し得ないタイムアウトが発生する原因となるためです。

現在のパーティション数を超えた後にコンテナーを作成しようとすると、次のメッセージと共に ServiceUnavailable 例外がスローされます。

"申し訳ありません。このリージョンでは現在需要が高まっており、要求にお応えすることができない状況です。 Microsoft では引き続きオンラインの容量を拡充し、もう一度お試しいただくご案内ができるよう取り組んでまいります。
ご不明な点がありましたら、内容を問わずいつでも askcosmosdb@microsoft.com までメールをお送りください。
ActivityId:12345678-1234-1234-1234-123456789abc"

Azure Cosmos Emulator で利用可能なコンテナーの数を変更する手順は次のとおりです。

1. システム トレイの **[Azure Cosmos DB Emulator]** アイコンを右クリックし、 **[Reset Data]\(データのリセット\)** をクリックして、ローカルの Azure Cosmos Emulator データをすべて削除します。
2. フォルダー `%LOCALAPPDATA%\CosmosDBEmulator` にあるエミュレーターのデータをすべて削除します。
3. システム トレイの **[Azure Cosmos DB Emulator]** アイコンを右クリックし、 **[終了]** をクリックし、開いているインスタンスをすべて終了します。 すべてのインスタンスが終了するまでしばらく時間がかかる場合があります。
4. 最新版の [Azure Cosmos Emulator](https://aka.ms/cosmosdb-emulator) をインストールします。
5. PartitionCount フラグの値を 250 以下に設定して、エミュレーターを起動します。 (例: `C:\Program Files\Azure Cosmos DB Emulator> CosmosDB.Emulator.exe /PartitionCount=100`)。

## <a name="controlling-the-emulator"></a>エミュレーターの制御

エミュレーターには、サービスの開始、停止、アンインストール、状態の取得のための PowerShell モジュールが付属しています。 この PowerShell モジュールを使用するには、次のコマンドレットを実行します。

```powershell
Import-Module "$env:ProgramFiles\Azure Cosmos DB Emulator\PSModules\Microsoft.Azure.CosmosDB.Emulator"
```

このほか、`PSModulesPath` に `PSModules` ディレクトリを追加したうえで、次のコマンドを使用してインポートする方法もあります。

```powershell
$env:PSModulesPath += "$env:ProgramFiles\Azure Cosmos DB Emulator\PSModules"
Import-Module Microsoft.Azure.CosmosDB.Emulator
```

PowerShell からエミュレーターを制御するためのコマンドの概要を次に示します。

### `Get-CosmosDbEmulatorStatus`

**構文**

`Get-CosmosDbEmulatorStatus`

**解説**

次のいずれかの ServiceControllerStatus 値が返されます。ServiceControllerStatus.StartPending、ServiceControllerStatus.Running、または ServiceControllerStatus.Stopped。

### `Start-CosmosDbEmulator`

**構文**

`Start-CosmosDbEmulator [-DataPath <string>] [-DefaultPartitionCount <uint16>] [-DirectPort <uint16[]>] [-MongoPort <uint16>] [-NoUI] [-NoWait] [-PartitionCount <uint16>] [-Port <uint16>] [<CommonParameters>]`

**解説**

エミュレーターを起動します。 既定では、このコマンドは、エミュレーターで要求を受け付ける準備ができるまで待ちます。 エミュレーターを起動したらすぐにコマンドレットから戻るようにする場合は、-NoWait オプションを使用します。

### `Stop-CosmosDbEmulator`

**構文**

 `Stop-CosmosDbEmulator [-NoWait]`

**解説**

エミュレーターを停止します。 既定では、このコマンドは、エミュレーターが完全に停止するまで待ちます。 エミュレーターが停止し始めたらすぐにコマンドレットから戻るようにする場合は、-NoWait オプションを使用します。

### `Uninstall-CosmosDbEmulator`

**構文**

`Uninstall-CosmosDbEmulator [-RemoveData]`

**解説**

エミュレーターをアンインストールし、オプションで $env:LOCALAPPDATA\CosmosDbEmulator のすべての内容を削除します。
このコマンドレットは、アンインストールする前にエミュレーターが停止されたことを確認します。

## <a name="running-on-docker"></a>Docker で実行する

Azure Cosmos Emulator は、Docker for Windows 上で実行できます。 エミュレーターは、Docker for Oracle Linux では機能しません。

[Docker for Windows](https://www.docker.com/docker-windows) をインストールしたら、ツール バーの Docker アイコンを右クリックし、 **[Switch to Windows containers]\(Windows コンテナーに切り替える\)** を選択して Windows コンテナーに切り替えます。

次に、普段利用しているシェルから次のコマンドを実行し、Docker Hub からエミュレーター イメージをプルします。

```bash
docker pull mcr.microsoft.com/cosmosdb/windows/azure-cosmos-emulator
```
イメージを起動するには、次のコマンドを実行します。

コマンド ラインから:
```cmd

md %LOCALAPPDATA%\CosmosDBEmulator\bind-mount

docker run --name azure-cosmosdb-emulator --memory 2GB --mount "type=bind,source=%LOCALAPPDATA%\CosmosDBEmulator\bind-mount,destination=C:\CosmosDB.Emulator\bind-mount" --interactive --tty -p 8081:8081 -p 8900:8900 -p 8901:8901 -p 8902:8902 -p 10250:10250 -p 10251:10251 -p 10252:10252 -p 10253:10253 -p 10254:10254 -p 10255:10255 -p 10256:10256 -p 10350:10350 mcr.microsoft.com/cosmosdb/windows/azure-cosmos-emulator --rm
```

> [!NOTE]
> docker run コマンドを実行するときにポートの競合エラー (指定されたポートが既に使用されている) が表示される場合は、ポート番号を変更してカスタム ポートを渡すことができます。 たとえば、"-p 8081:8081" を "-p 443:8081" に変更できます

PowerShell から:
```powershell

md $env:LOCALAPPDATA\CosmosDBEmulator\bind-mount 2>null

docker run --name azure-cosmosdb-emulator --memory 2GB --mount "type=bind,source=$env:LOCALAPPDATA\CosmosDBEmulator\bind-mount,destination=C:\CosmosDB.Emulator\bind-mount" --interactive --tty -p 8081:8081 -p 8900:8900 -p 8901:8901 -p 8902:8902 -p 10250:10250 -p 10251:10251 -p 10252:10252 -p 10253:10253 -p 10254:10254 -p 10255:10255 -p 10256:10256 -p 10350:10350 mcr.microsoft.com/cosmosdb/windows/azure-cosmos-emulator

```

応答は次のようになります。

```
Starting emulator
Emulator Endpoint: https://172.20.229.193:8081/
Master Key: C2y6yDjf5/R+ob0N8A7Cgv30VRDJIWEHLM+4QDU5DE2nQ9nDuVTqobD4b8mGGyPMbIZnqyMsEcaGQy67XIw/Jw==
Exporting SSL Certificate
You can import the SSL certificate from an administrator command prompt on the host by running:
cd /d %LOCALAPPDATA%\CosmosDBEmulatorCert
powershell .\importcert.ps1
--------------------------------------------------------------------------------------------------
Starting interactive shell
```

クライアントで応答からのエンドポイントとマスター キーを使用し、SSL 証明書をホストにインポートします。 SSL 証明書をインポートするには、admin コマンド プロンプトから以下を実行します。

コマンド ラインから:

```cmd
cd  %LOCALAPPDATA%\CosmosDBEmulator\bind-mount
powershell .\importcert.ps1
```

PowerShell から:
```powershell
cd $env:LOCALAPPDATA\CosmosDBEmulator\bind-mount
.\importcert.ps1
```

エミュレーターの起動後に対話型シェルを閉じると、エミュレーターのコンテナーがシャットダウンします。

データ エクスプローラーを開くには、ブラウザーで次の URL に移動します。 上記の応答メッセージにエミュレーター エンドポイントが表示されます。

    https://<emulator endpoint provided in response>/_explorer/index.html

## Mac または Linux 上での実行<a id="mac"></a>

現在、Cosmos エミュレーターは Windows でのみ実行できます。 Mac または Linux を実行するユーザーは、Windows 仮想マシンによってホストされる、Parallels や VirtualBox などのハイパーバイザー内でエミュレーターを実行できます。 これを有効にする手順は、次のとおりです。

Windows VM 内で以下のコマンドを実行して、IPv4 アドレスをメモします。

```cmd
ipconfig.exe
```

アプリケーション内で、`ipconfig.exe` によって返される IPv4 アドレスを使用するには、DocumentClient オブジェクトの URI を変更する必要があります。 次の手順では、DocumentClient オブジェクトを構築するときに、CA 検証を回避します。 このためには、HttpClientHandler を、ServerCertificateCustomValidationCallback のその独自の実装を持つ DocumentClient コンストラクターに指定する必要があります。

下は必要なコードの例です。

```csharp
using System;
using Microsoft.Azure.Documents;
using Microsoft.Azure.Documents.Client;
using System.Net.Http;

namespace emulator
{
    class Program
    {
        static async void Main(string[] args)
        {
            string strEndpoint = "https://10.135.16.197:8081/";  //IPv4 address from ipconfig.exe
            string strKey = "C2y6yDjf5/R+ob0N8A7Cgv30VRDJIWEHLM+4QDU5DE2nQ9nDuVTqobD4b8mGGyPMbIZnqyMsEcaGQy67XIw/Jw==";

            //Work around the CA validation
            var httpHandler = new HttpClientHandler()
            {
                ServerCertificateCustomValidationCallback = (req,cert,chain,errors) => true
            };

            //Pass http handler to document client
            using (DocumentClient client = new DocumentClient(new Uri(strEndpoint), strKey, httpHandler))
            {
                Database database = await client.CreateDatabaseIfNotExistsAsync(new Database { Id = "myDatabase" });
                Console.WriteLine($"Created Database: id - {database.Id} and selfLink - {database.SelfLink}");
            }
        }
    }
}
```

最後に、Windows VM 内から、次のオプションを使用してコマンド ラインから Cosmos エミュレーターを起動します。

```cmd
Microsoft.Azure.Cosmos.Emulator.exe /AllowNetworkAccess /Key=C2y6yDjf5/R+ob0N8A7Cgv30VRDJIWEHLM+4QDU5DE2nQ9nDuVTqobD4b8mGGyPMbIZnqyMsEcaGQy67XIw/Jw==
```

## <a name="troubleshooting"></a>トラブルシューティング

次のヒントは、Azure Cosmos Emulator で発生する問題のトラブルシューティングに役立ちます。

- 新しいバージョンのエミュレーターをインストールしてエラーが発生している場合は、データをリセットしていることを確認します。 システム トレイの [Azure Cosmos Emulator] アイコンを右クリックし、[Reset Data]\(データのリセット\) をクリックすると、データをリセットできます。 それでもエラーが解消しない場合には、エミュレーターを (旧バージョンがあれば、それも含めて) アンインストールし、"C:\Program files\Azure Cosmos DB Emulator" ディレクトリを削除してから、エミュレーターを再インストールします。 手順については、「[ローカル エミュレーターのアンインストール](#uninstall)」をご覧ください。

- Azure Cosmos Emulator がクラッシュした場合には、%LOCALAPPDATA%\CrashDumps フォルダーからダンプ ファイルを収集し、圧縮したうえでメールに添付して [askcosmosdb@microsoft.com](mailto:askcosmosdb@microsoft.com) にお送りください。

- `Microsoft.Azure.Cosmos.ComputeServiceStartupEntryPoint.exe` にクラッシュが発生した場合、パフォーマンス カウンターが破損していることを示す兆候である可能性が考えられます。 この問題は通常、管理者のコマンド プロンプトから次のコマンドを実行すると解消します。

  ```cmd
  lodctr /R
   ```

- 接続の問題が発生した場合は、[トレース ファイルを収集](#trace-files)し、それらを圧縮して、[askcosmosdb@microsoft.com](mailto:askcosmosdb@microsoft.com) への電子メールに添付します。

- "**サービス利用不可**" というメッセージが表示された場合、エミュレーターがネットワーク スタックの初期化に失敗している可能性があります。 Pulse Secure クライアントまたは Juniper Networks クライアントのネットワーク フィルター ドライバーが問題の原因である可能性があるため、これらのクライアントがインストールされているかどうかを確認してください。 通常、サード パーティのネットワーク フィルター ドライバーをアンインストールすると、問題が解決されます。 このほか、/DisableRIO オプションを指定してエミュレーターを起動する方法もあります。このオプションを使うと、エミュレーターのネットワーク通信が通常の Winsock に切り替わります。 

- エミュレーターの実行中に、ご利用のコンピューターがスリープ モードになった場合や、そのコンピューターで OS を更新した場合、"**サービスは現在使用できません**" というメッセージが表示される可能性があります。 Windows 通知トレイに表示されているアイコンを右クリックし、 **[Reset Data]\(データのリセット\)** を選択して、エミュレーターのデータをリセットします。

### <a id="trace-files"></a>トレース ファイルの収集

デバッグ トレースを収集するには、管理コマンド プロンプトから次のコマンドを実行します。

1. `cd /d "%ProgramFiles%\Azure Cosmos DB Emulator"`
2. `CosmosDB.Emulator.exe /shutdown` プログラムがシャットダウンしたことをシステム トレイで確認します。シャットダウンに 1 分かかる場合があります。 Azure Cosmos Emulator のユーザー インターフェイスで **[終了]** をクリックするだけでもかまいません。
3. `CosmosDB.Emulator.exe /starttraces`
4. `CosmosDB.Emulator.exe`
5. 問題を再現します。 データ エクスプローラーが動作していない場合は、エラーを捕捉するために、ブラウザーが開くまで数秒間待つだけです。
5. `CosmosDB.Emulator.exe /stoptraces`
6. `%ProgramFiles%\Azure Cosmos DB Emulator` に移動し、docdbemulator_000001.etl ファイルを見つけます。
7. デバッグのために、再現手順と共に .etl ファイルを [askcosmosdb@microsoft.com](mailto:askcosmosdb@microsoft.com) に送付します。

### <a id="uninstall"></a>ローカル エミュレーターのアンインストール

1. システム トレイの Azure Cosmos Emulator アイコンを右クリックし、[終了] をクリックして、開いているローカル エミュレーターのインスタンスをすべて終了します。 すべてのインスタンスが終了するまでしばらく時間がかかる場合があります。
2. Windows 検索ボックスに「**アプリと機能**」と入力し、**アプリと機能 (システム設定)** の検索結果をクリックします。
3. アプリの一覧で、**Azure Cosmos DB Emulator** までスクロールして選択し、 **[アンインストール]** をクリックし、確認して再度、 **[アンインストール]** をクリックします。
4. アプリがアンインストールされたら、`%LOCALAPPDATA%\CosmosDBEmulator` に移動して、フォルダーを削除します。

## <a name="next-steps"></a>次の手順

このチュートリアルでは、無料のローカル開発のためにローカル エミュレーターを使用する方法について学習しました。 これで次のチュートリアルに進み、エミュレーター SSL 証明書をエクスポートする方法について学習できます。

> [!div class="nextstepaction"]
> [Azure Cosmos Emulator 証明書をエクスポートする](local-emulator-export-ssl-certificates.md)
