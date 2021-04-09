---
title: Azure Cosmos DB Emulator のコマンドラインと PowerShell リファレンス
description: Azure Cosmos DB Emulator のコマンドライン パラメーター、PowerShell によるエミュレーターの制御方法、エミュレーター内で作成できるコンテナー数の変更方法について説明します。
ms.service: cosmos-db
ms.topic: how-to
author: markjbrown
ms.author: mjbrown
ms.date: 09/17/2020
ms.custom: contperf-fy21q1
ms.openlocfilehash: e0c795484bf860402d05c1dc5779633962a44ec2
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "99979506"
---
# <a name="command-line-and-powershell-reference-for-azure-cosmos-db-emulator"></a>Azure Cosmos DB Emulator のコマンドラインと PowerShell リファレンス
[!INCLUDE[appliesto-all-apis](includes/appliesto-all-apis.md)]

Azure Cosmos DB Emulator は、ローカル開発目的で Azure Cosmos DB サービスをエミュレートするローカル環境を提供します。 [エミュレーターのインストール](local-emulator.md)後、コマンド ラインと PowerShell コマンドでエミュレーターを制御できます。 この記事では、コマンドラインと PowerShell コマンドを使用してエミュレーターを起動、停止し、オプションを構成し、その他の操作を実行する方法について説明します。 インストール場所からコマンドを実行する必要があります。

##  <a name="manage-the-emulator-with-command-line-syntax"></a><a id="command-line"></a>コマンドライン構文を使用してエミュレーターを管理する

```cmd
Microsoft.Azure.Cosmos.Emulator.exe [/Shutdown] [/DataPath] [/Port] [/MongoPort] [/DirectPorts] [/Key] [/EnableRateLimiting] [/DisableRateLimiting] [/NoUI] [/NoExplorer] [/EnableMongoDbEndpoint] [/?]
```

オプションの一覧を表示するには、コマンド プロンプトで「 `Microsoft.Azure.Cosmos.Emulator.exe /?` 」と入力します。

|**オプション** | **説明** | **コマンド**| **引数**|
|---|---|---|---|
|[引数なし] | 既定の設定で Azure Cosmos DB Emulator を開始します。 |Microsoft.Azure.Cosmos.Emulator.exe| |
|[Help] |サポートされるコマンド ライン引数の一覧を表示します。|Microsoft.Azure.Cosmos.Emulator.exe /? | |
| GetStatus |Azure Cosmos DB Emulator の状態を取得します。 この状態は、終了コードで示されます。終了コードは、1 = 開始中、2 = 実行中、3 = 停止済みです。 負の終了コードは、エラーが発生したことを示します。 その他の出力は生成されません。 | Microsoft.Azure.Cosmos.Emulator.exe /GetStatus| |
| Shutdown| Azure Cosmos DB Emulator をシャットダウンします。| Microsoft.Azure.Cosmos.Emulator.exe /Shutdown | |
|DataPath | データ ファイルを格納するパスを指定します。 既定値は %LocalAppdata%\CosmosDBEmulator です。 | Microsoft.Azure.Cosmos.Emulator.exe /DataPath=\<datapath\> | \<datapath\>:アクセスできるパス |
|Port | エミュレーターで使用するポート番号を指定します。 既定値は 8081 です。 |Microsoft.Azure.Cosmos.Emulator.exe /Port=\<port\> | \<port\>:単一のポート番号 |
| ComputePort | Compute Interop Gateway サービスに使用するポート番号を指定します。 ゲートウェイの HTTP エンドポイント プローブ ポートのポート番号は、ComputePort に 79 を加えた値として計算されます。 このため、ComputePort と ComputePort に 79 を加えた番号のポートは、開いて利用可能な状態にしておく必要があります。 既定値は 8900 です。 | Microsoft.Azure.Cosmos.Emulator.exe /ComputePort=\<computeport\> | \<computeport\>:単一のポート番号 |
| EnableMongoDbEndpoint=3.2 | MongoDB API 3.2 を有効にします | Microsoft.Azure.Cosmos.Emulator.exe /EnableMongoDbEndpoint=3.2 | |
| EnableMongoDbEndpoint=3.6 | MongoDB API 3.6 を有効にします | Microsoft.Azure.Cosmos.Emulator.exe /EnableMongoDbEndpoint=3.6 | |
| MongoPort | MongoDB 互換性 API に使用するポート番号を指定します。 既定値は 10255 です。 |Microsoft.Azure.Cosmos.Emulator.exe /MongoPort=\<mongoport\>|\<mongoport\>:単一のポート番号|
| EnableCassandraEndpoint | Cassandra API を有効にします | Microsoft.Azure.Cosmos.Emulator.exe /EnableCassandraEndpoint | |
| CassandraPort | Cassandra エンドポイントで使用するポート番号を指定します。 既定値は 10350 です。 | Microsoft.Azure.Cosmos.Emulator.exe /CassandraPort=\<cassandraport\> | \<cassandraport\>:単一のポート番号 |
| EnableGremlinEndpoint | Gremlin API を有効にします | Microsoft.Azure.Cosmos.Emulator.exe /EnableGremlinEndpoint | |
| GremlinPort | Gremlin エンドポイントに使用するポート番号です。 既定値は 8901 です。 | Microsoft.Azure.Cosmos.Emulator.exe /GremlinPort=\<port\> | \<port\>:単一のポート番号 |
|EnableTableEndpoint | Azure Table API を有効にします | Microsoft.Azure.Cosmos.Emulator.exe /EnableTableEndpoint | |
|TablePort | Azure Table エンドポイントに使用するポート番号です。 既定値は 8902 です。 | Microsoft.Azure.Cosmos.Emulator.exe /TablePort=\<port\> | \<port\>:単一のポート番号|
| KeyFile | 指定されたファイルから承認キーを読み取ります。 キーファイルの生成には、/GenKeyFile オプションを使用します | Microsoft.Azure.Cosmos.Emulator.exe /KeyFile=\<file_name\> | \<file_name\>:ファイルへのパス |
| ResetDataPath | 指定されたパスにある全部のファイルを、再帰的に削除します。 パスを指定しなかった場合には、既定値が %LOCALAPPDATA%\CosmosDbEmulator になります | Microsoft.Azure.Cosmos.Emulator.exe /ResetDataPath=\<path> | \<path\>:[ファイル パス]  |
| StartTraces  |  LOGMAN を使用して、デバッグ トレース ログの収集を開始します。 | Microsoft.Azure.Cosmos.Emulator.exe /StartTraces | |
| StopTraces     | LOGMAN を使用して、デバッグ トレース ログの収集を停止します。 | Microsoft.Azure.Cosmos.Emulator.exe /StopTraces  | |
| StartWprTraces  |  Windows パフォーマンス記録ツールを使用したデバッグ トレース ログの収集を開始します。 | Microsoft.Azure.Cosmos.Emulator.exe /StartWprTraces | |
| StopWprTraces     | Windows パフォーマンス記録ツールを使用したデバッグ トレース ログの収集を停止します。 | Microsoft.Azure.Cosmos.Emulator.exe /StopWprTraces  | |
|FailOnSslCertificateNameMismatch | 証明書の SAN にエミュレーターのホストのドメイン名、ローカル IPv4 アドレス、"localhost"、"127.0.0.1" が含まれていない場合、既定では、エミュレーターにより自己署名 TLS/SSL 証明書が再生成されます。 このオプションを設定すると、その代わりにエミュレーターが起動に失敗します。 その後は /GenCert オプションを使って新しい自己署名 TLS/SSL 証明書を作成およびインストールする必要があります。 | Microsoft.Azure.Cosmos.Emulator.exe /FailOnSslCertificateNameMismatch  | |
| GenCert | 新しい自己署名 TLS/SSL 証明書を生成およびインストールします。 オプションで、ネットワーク経由でエミュレーターにアクセスするための追加の DNS 名を列挙したコンマ区切りリストを含めることもできます。 | Microsoft.Azure.Cosmos.Emulator.exe /GenCert=\<dns-names\> |\<dns-names\>:追加の DNS 名のコンマ区切りリスト (省略可能)  |
| DirectPorts |直接接続に使用するポートを指定します。 既定値は、10251、10252、10253、10254 です。 | Microsoft.Azure.Cosmos.Emulator.exe /DirectPorts:\<directports\> | \<directports\>:4 つのポートのコンマ区切りリスト |
| Key |エミュレーターの承認キーです。 キーは、64 バイト ベクトルの Base 64 エンコーディングが施されている必要があります。 | Microsoft.Azure.Cosmos.Emulator.exe /Key:\<key\> | \<key\>:キーは、64 バイト ベクトルの Base 64 エンコーディングが施されている必要があります|
| EnableRateLimiting | 要求レート制限の動作の有効化を指定します。 |Microsoft.Azure.Cosmos.Emulator.exe /EnableRateLimiting | |
| DisableRateLimiting |要求レート制限の動作の無効化を指定します。 |Microsoft.Azure.Cosmos.Emulator.exe /DisableRateLimiting | |
| NoUI | エミュレーターのユーザー インターフェイスを表示しません。 | Microsoft.Azure.Cosmos.Emulator.exe /NoUI | |
| NoExplorer | 起動時にデータ エクスプローラーを表示しません。 |Microsoft.Azure.Cosmos.Emulator.exe /NoExplorer | | 
| PartitionCount | パーティション分割されたコンテナーの最大数を指定します。 詳細については、「[コンテナーの数を変更する](#set-partitioncount)」を参照してください。 | Microsoft.Azure.Cosmos.Emulator.exe /PartitionCount=\<partitioncount\> | \<partitioncount\>:許容される単一パーティション コンテナーの最大数です。 既定値は 25 です。 許容される最大値は 250 です。|
| DefaultPartitionCount| パーティション分割されたコンテナーの既定のパーティション数を指定します。 | Microsoft.Azure.Cosmos.Emulator.exe /DefaultPartitionCount=\<defaultpartitioncount\> | \<defaultpartitioncount\> 既定値は 25 です。|
| AllowNetworkAccess | ネットワーク上でのエミュレーターへのアクセスを有効にします。 ネットワーク アクセスを有効にするには、/Key=\<key_string\> または /KeyFile=\<file_name\> も渡す必要があります。 | Microsoft.Azure.Cosmos.Emulator.exe /AllowNetworkAccess /Key=\<key_string\> または  Microsoft.Azure.Cosmos.Emulator.exe /AllowNetworkAccess /KeyFile=\<file_name\>| |
| NoFirewall | /AllowNetworkAccess オプションが使用されているときは、ファイアウォール規則を調整しないでください。 |Microsoft.Azure.Cosmos.Emulator.exe /NoFirewall | |
| GenKeyFile | 新しい承認キーを生成し、指定したファイルに保存します。 生成されたキーは、/Key オプションまたは/KeyFile オプションで使用できます。 | Microsoft.Azure.Cosmos.Emulator.exe /GenKeyFile=\<path to key file\> | |
| 一貫性 | アカウントの既定の一貫性レベルを設定します。 | Microsoft.Azure.Cosmos.Emulator.exe /Consistency=\<consistency\> | \<consistency\>:値は次のいずれかの[一貫性レベル](consistency-levels.md)である必要があります。Session、Strong、Eventual、または BoundedStaleness。 既定値は Session です。 |
| ? | ヘルプ メッセージを表示します。| | |

## <a name="manage-the-emulator-with-powershell"></a>PowerShell でエミュレーターを管理する

エミュレーターには、サービスの開始、停止、アンインストール、状態の取得のための PowerShell モジュールが付属しています。 この PowerShell モジュールを使用するには、次のコマンドレットを実行します。

```powershell
Import-Module "$env:ProgramFiles\Azure Cosmos DB Emulator\PSModules\Microsoft.Azure.CosmosDB.Emulator"
```

このほか、`PSModulePath` に `PSModules` ディレクトリを追加したうえで、次のコマンドを使用してインポートする方法もあります。

```powershell
$env:PSModulePath += "$env:ProgramFiles\Azure Cosmos DB Emulator\PSModules"
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

## <a name="change-the-number-of-default-containers"></a><a id="set-partitioncount"></a>既定のコンテナー数を変更する

Azure Cosmos DB Emulator で作成できるコンテナーの数の既定値は、固定サイズのコンテナーであれば 25 個 (Azure Cosmos DB SDK を使用した場合にのみサポート)、容量無制限のコンテナーであれば 5 個までです。 **PartitionCount** の値を変更すると、固定サイズのコンテナーであれば 250 個、容量無制限のコンテナーであれば 50 個まで作成できるようになります。この 2 つの組み合わせは、固定サイズのコンテナー 250 個分を超えない範囲であれば、自由に決めることができます (容量無制限のコンテナーは、1 個につき固定サイズのコンテナー 5 個分として計算されます)。 ただし、固定サイズのコンテナーが 200 個以上の状態でエミュレーターを実行する設定にはしないことをお勧めします。 これは、ディスクの IO 操作にオーバーヘッドが加わり、エンドポイント API の使用中に予測し得ないタイムアウトが発生する原因となるためです。

現在のパーティション数を超えた後にコンテナーを作成しようとすると、次のメッセージと共に ServiceUnavailable 例外がスローされます。

> 申し訳ありません。このリージョンでは現在需要が高まっており、要求にお応えすることができない状況です。 Microsoft では引き続きオンラインの容量を拡充し、もう一度お試しいただくご案内ができるよう取り組んでまいります。
> ActivityId:12345678-1234-1234-1234-123456789abc

Azure Cosmos DB Emulator で利用可能なコンテナーの数を変更するには、次の手順を実行します。

1. システム トレイの **[Azure Cosmos DB Emulator]** アイコンを右クリックし、**[Reset Data…]\(データのリセット…\)** をクリックし、ローカルの Azure Cosmos DB Emulator データをすべて削除します。

1. フォルダー `%LOCALAPPDATA%\CosmosDBEmulator` にあるエミュレーターのデータをすべて削除します。

1. システム トレイの **[Azure Cosmos DB Emulator]** アイコンを右クリックし、 **[終了]** をクリックし、開いているインスタンスをすべて終了します。 すべてのインスタンスが終了するまでしばらく時間がかかる場合があります。

1. 最新版の [Azure Cosmos DB Emulator](https://aka.ms/cosmosdb-emulator) をインストールします。

1. PartitionCount フラグの値を 250 以下に設定して、エミュレーターを起動します。 (例: `C:\Program Files\Azure Cosmos DB Emulator> Microsoft.Azure.Cosmos.Emulator.exe /PartitionCount=100`)。
 
## <a name="next-steps"></a>次のステップ

* [Java、Python、および Node.js アプリで使用する Azure Cosmos DB エミュレーター証明書のエクスポート](local-emulator-export-ssl-certificates.md)
* [エミュレーターを使用して問題をデバッグする](troubleshoot-local-emulator.md)
