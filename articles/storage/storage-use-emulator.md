---
title: "開発とテストのための Azure Storage エミュレーター使用 | Microsoft Docs"
description: "Azure ストレージ エミュレーターは、Azure Storage に対する開発とテストのための無料のローカル開発環境を提供します。 要求の認証方法、アプリケーションからエミュレーターへの接続方法、コマンド ライン ツールの使用方法など、ストレージ エミュレーターについて説明します。"
services: storage
documentationcenter: 
author: mmacy
manager: timlt
editor: tysonn
ms.assetid: f480b059-df8a-4a63-b05a-7f2f5d1f5c2a
ms.service: storage
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/08/2016
ms.author: marsma
translationtype: Human Translation
ms.sourcegitcommit: 72b2d9142479f9ba0380c5bd2dd82734e370dee7
ms.openlocfilehash: fe1d7abf3585efab67a7dbc10afa7bf3c4d466e5
ms.lasthandoff: 03/08/2017


---
# <a name="use-the-azure-storage-emulator-for-development-and-testing"></a>開発とテストのための Azure のストレージ エミュレーター使用
## <a name="overview"></a>概要
Microsoft Azure ストレージ エミュレーターでは、Azure の BLOB、キュー、テーブル サービスを開発用にエミュレートするローカル環境が利用できます。 ストレージ エミュレーターを使用すると、ストレージ サービスに対するアプリケーションのローカル テストが、Azure サブスクリプションを作成したりコストをかけたりせずに実施できます。 エミュレーターでのアプリケーションの動作に満足できたら、クラウドでの Azure ストレージ アカウントの使用に切り替えることができます。

> [!NOTE]
> ストレージ エミュレーターは、 [Microsoft Azure SDK](https://azure.microsoft.com/downloads/)に付属しています。 また、 [スタンドアロンのインストーラー](https://go.microsoft.com/fwlink/?linkid=717179&clcid=0x409)を使用して、ストレージ エミュレーターをインストールすることも可能です。 ストレージ エミュレーターをインストールするには、お使いのコンピューターで管理者権限が与えられている必要があります。
>
> ストレージ エミュレーターは、現在、Windows でのみ実行されます。
>
> ストレージ エミュレーターの特定のバージョンで作成されたデータには、別のバージョンを使用しているとアクセスできない場合があります。 データを永続化して長期にわたって保持する必要がある場合、そのデータはストレージ エミュレーターではなく Azure ストレージ アカウントに格納することをお勧めします。
>
> ストレージ エミュレーターは、OData ライブラリ バージョン 5.6 に依存します。 ストレージ エミュレーターで使用される OData DLL を新しいバージョンに置き換えることはサポートされていません。この置き換えを行うと、予期しない動作が発生します。 ただし、ストレージ サービスでサポートされる OData のバージョンを使用してエミュレーターに要求を送信することは可能です。
>
>

## <a name="how-the-storage-emulator-works"></a>ストレージ エミュレーターのしくみ
ストレージ エミュレーターは、ローカルの Microsoft SQL Server インスタンスとローカル ファイル システムを使用して、Azure ストレージ サービスをエミュレートします。 既定では、ストレージ エミュレーターは、Microsoft SQL Server 2012 Express LocalDB のデータベースを使用します。  LocalDB インスタンスではなく、SQL Server のローカル インスタンスにアクセスするようにストレージ エミュレーターを構成することもできます。 詳細については、下の「[ストレージ エミュレーターの起動と初期化](#start-and-initialize-the-storage-emulator)」を参照してください。

LocalDB のインストールを管理するには、SQL Server Management Studio Express をインストールします。 ストレージ エミュレーターは、Windows 認証を使用して SQL Server または LocalDB に接続します。

ストレージ エミュレーターと Azure のストレージ サービスには、いくつかの機能上の違いがあります。 これらの違いの詳細については、「 [ストレージ エミュレーターと Azure ストレージとの違い](#differences-between-the-storage-emulator-and-azure-storage)」を参照してください。

## <a name="authenticating-requests-against-the-storage-emulator"></a>ストレージ エミュレーターに対する要求の認証
クラウドの Azure Storage の場合と同様に、ストレージ エミュレーターに対する各要求は、匿名である場合を除いて、認証される必要があります。 ストレージ エミュレーターに対する要求の認証には、共有キー認証を使用するか、共有アクセス署名 (SAS) を使用することができます。

### <a name="authentication-with-shared-key-credentials"></a>共有キー資格情報での認証
[!INCLUDE [storage-emulator-connection-string-include](../../includes/storage-emulator-connection-string-include.md)]

接続文字列の詳細については、「[Azure Storage への接続文字列を構成する](storage-configure-connection-string.md)」を参照してください。

### <a name="authentication-with-a-shared-access-signature"></a>共有アクセス署名での認証
Xamarin ライブラリなど、一部の Azure ストレージ クライアント ライブラリでは、共有アクセス署名 (SAS) トークンでの認証だけがサポートされています。 この SAS トークンを、共有キー認証をサポートしているツールまたはアプリケーションを使用して作成します。 SAS トークンは、Azure PowerShell を通じて、簡単に生成することができます。

1. Azure PowerShell がまだインストールされていない場合は、インストールします。 Azure PowerShell コマンドレットの最新バージョンを使用することをお勧めします。 インストールの手順については、「 [Azure PowerShell のインストールと構成の方法](/powershell/azureps-cmdlets-docs) 」を参照してください。
2. Azure PowerShell を開き、次のコマンドを実行します。*ACCOUNT_NAME* と *ACCOUNT_KEY==* は使用する資格情報に置き換え、*CONTAINER_NAME* は任意の名前に置き換えます。

```powershell
$context = New-AzureStorageContext -StorageAccountName "ACCOUNT_NAME" -StorageAccountKey "ACCOUNT_KEY=="

New-AzureStorageContainer CONTAINER_NAME -Permission Off -Context $context

$now = Get-Date

New-AzureStorageContainerSASToken -Name CONTAINER_NAME -Permission rwdl -ExpiryTime $now.AddDays(1.0) -Context $context -FullUri
```

新しいコンテナーの共有アクセス署名 URI は、次のようになります。

    https://storageaccount.blob.core.windows.net/sascontainer?sv=2012-02-12&se=2015-07-08T00%3A12%3A08Z&sr=c&sp=wl&sig=t%2BbzU9%2B7ry4okULN9S0wst%2F8MCUhTjrHyV9rDNLSe8g%3Dsss

この例で作成した共有アクセス署名は、1 日間有効です。 この署名は、コンテナー内の BLOB へのフル アクセス (つまり、読み取り、書き込み、削除、一覧表示) を許可します。

Shared Access Signature の詳細については、「 [Shared Access Signature (SAS) の使用](storage-dotnet-shared-access-signature-part-1.md)」を参照してください。

## <a name="start-and-initialize-the-storage-emulator"></a>ストレージ エミュレーターの起動と初期化
Azure ストレージ エミュレーターを起動するには、[スタート] ボタンをクリックするか、Windows キーを押します。 「 **Azure ストレージ エミュレーター**」と入力し始め、アプリケーションの一覧からエミュレーターを選択します。

エミュレーターの実行中は、Windows タスク バーの通知領域にアイコンが表示されます。

ストレージ エミュレーターが起動すると、コマンド ライン ウィンドウが表示されます。 このコマンド ライン ウィンドウを使用して、ストレージ エミュレーターを起動または停止したり、データのクリア、状態の取得、およびエミュレーターの初期化を実行したりできます。 詳細については、「 [ストレージ エミュレーター コマンド ライン ツールのリファレンス](#storage-emulator-command-line-tool-reference)」を参照してください。

コマンド ライン ウィンドウを終了しても、ストレージ エミュレーターは引き続き実行されています。 コマンド ラインを再度表示するには、ストレージ エミュレーターを起動する場合と同じように前述の手順に従います。

ストレージ エミュレーターを初めて実行すると、ローカル ストレージ環境が初期化されます。 初期化プロセスでは、LocalDB にデータベースが作成され、各ローカル ストレージ サービス用として HTTP ポートが予約されます。

ストレージ エミュレーターは、既定で C:\Program Files(x86)\Microsoft SDKs\Azure\Storage Emulator ディレクトリにインストールされています。

### <a name="initialize-the-storage-emulator-to-use-a-different-sql-database"></a>別の SQL データベースを使用するための、ストレージ エミュレーターの初期化
既定の LocalDB インスタンスとは別の SQL データベース インスタンスを参照するようにストレージ エミュレーターを初期化するには、ストレージ エミュレーター コマンド ライン ツールを使用します。

1. **[スタート]** ボタンをクリックするか、**[Windows]** キーを押します。 「 `Azure Storage Emulator` 」と入力し始め、ストレージ エミュレーター コマンド ライン ツールが表示されるようになったら、それを選択します。
2. コマンド プロンプト ウィンドウで、次のコマンドを入力します。ここで `<SQLServerInstance>` は、SQL Server インスタンスの名前です。 LocalDb を使用するには、SQL Server インスタンスとして `(localdb)\MSSQLLocalDb` を指定します。

        AzureStorageEmulator init /server <SQLServerInstance>

    次のコマンドを使うこともできます。このコマンドを指定すると、エミュレーターは既定の SQL Server インスタンスを使用します。

        AzureStorageEmulator init /server .\\

    または、データベースを既定の LocalDB インスタンスに再初期化する次のコマンドを使うこともできます。

        AzureStorageEmulator init /forceCreate

これらのコマンドの詳細については、「 [ストレージ エミュレーター コマンド ライン ツールのリファレンス](#storage-emulator-command-line-tool-reference)」を参照してください。

## <a name="addressing-resources-in-the-storage-emulator"></a>ストレージ エミュレーターでのリソースのアドレス指定
ストレージ エミュレーターのサービス エンドポイントは、Azure ストレージ アカウントのものとは異なります。 異なる理由は、ローカル コンピューターがドメイン名解決を行わず、ストレージ エミュレーターのエンドポイントがローカル アドレスでなければならない点にあります。

Azure ストレージ アカウントのリソースをアドレス指定する場合は、次のスキームを使用します。 アカウント名が URI ホスト名の一部になり、アドレス指定するリソースが URI パスの一部になります。

    <http|https>://<account-name>.<service-name>.core.windows.net/<resource-path>

たとえば、以下の URI は、Azure ストレージ アカウント内の BLOB の有効なアドレスです。

    https://myaccount.blob.core.windows.net/mycontainer/myblob.txt

ストレージ エミュレーターでは、ローカル コンピューターがドメイン名の解決を実行しないため、アカウント名は、ホスト名ではなく URI パスの一部になります。 ストレージ エミュレーターで実行されているリソースには、次のスキームを使用します。

    http://<local-machine-address>:<port>/<account-name>/<resource-path>

たとえば、ストレージ エミュレーターの BLOB にアクセスするには次のアドレスを使用します。

    http://127.0.0.1:10000/myaccount/mycontainer/myblob.txt

ストレージ エミュレーターのサービス エンドポイントは、次のとおりです。

    Blob Service: http://127.0.0.1:10000/<account-name>/<resource-path>
    Queue Service: http://127.0.0.1:10001/<account-name>/<resource-path>
    Table Service: http://127.0.0.1:10002/<account-name>/<resource-path>

### <a name="addressing-the-account-secondary-with-ra-grs"></a>RA-GRS を使用した、アカウントのセカンダリ拠点のアドレス指定
Version 3.1 以降では、ストレージ エミュレーター アカウントで読み取りアクセスの geo 冗長レプリケーション (RA-GRS) がサポートされます。 クラウド内のストレージ リソースとローカル エミュレーター内のストレージ リソースの場合、2 次拠点にアクセスするにはアカウント名に -secondary を付加します。 たとえば、ストレージ エミュレーターで読み取り専用の&2; 次拠点を使用して BLOB にアクセスするには、次のアドレスを使用します。

    http://127.0.0.1:10000/myaccount-secondary/mycontainer/myblob.txt

> [!NOTE]
> ストレージ エミュレーターを使用した、プログラムによるセカンダリへのアクセスには、.NET 用ストレージ クライアント ライブラリの Version 3.2 以降を使用してください。 詳細については、「 [.NET 用の Microsoft Azure Storage クライアント ライブラリ](https://msdn.microsoft.com/library/azure/dn261237.aspx) 」を参照してください。
>
>

## <a name="storage-emulator-command-line-tool-reference"></a>ストレージ エミュレーター コマンド ライン ツールのリファレンス
バージョン 3.0 からは、ストレージ エミュレーターの起動時にコマンド ライン ウィンドウがポップアップ表示されます。 このコマンド ライン ウィンドウを使用して、エミュレーターを起動または停止したり、状態のクエリやその他の操作を実行したりします。

> [!NOTE]
> Microsoft Azure コンピューティング エミュレーターがインストール済みの場合は、ストレージ エミュレーターの起動時にシステム トレイ アイコンが表示されます。 このアイコンを右クリックすると、直感的な方法でストレージ エミュレーターを起動や停止できます。
>
>

### <a name="command-line-syntax"></a>コマンド ライン構文
    AzureStorageEmulator [start] [stop] [status] [clear] [init] [help]

### <a name="options"></a>オプション
オプションの一覧を表示するには、コマンド プロンプトで「 `/help` 」と入力します。

| オプション | Description | コマンド | 引数 |
| --- | --- | --- | --- |
| **Start** |ストレージ エミュレーターを起動します。 |`AzureStorageEmulator start [-inprocess]` |*-inprocess*: 新しいプロセスを作成せずに、現在のプロセスでエミュレーターを起動します。 |
| **Stop** |ストレージ エミュレーターを停止します。 |`AzureStorageEmulator stop` | |
| **状態** |ストレージ エミュレーターの状態を出力します。 |`AzureStorageEmulator status` | |
| **Clear** |コマンド ラインで指定されたすべてのサービス内のデータを消去します。 |`AzureStorageEmulator clear [blob] [table] [queue] [all]                                                    ` |*blob*: BLOB データを消去します。 <br/>*queue:*キュー データを消去します。 <br/>*table*: テーブル データを消去します。 <br/>*all*: すべてのサービスのすべてのデータを消去します。 |
| **Init** |エミュレーターをセットアップするために、1 回限りの初期化を実行します。 |<code>AzureStorageEmulator.exe init [-server serverName] [-sqlinstance instanceName] [-forcecreate&#124;-skipcreate] [-reserveports&#124;-unreserveports] [-inprocess]</code> |*-server serverName\instanceName*: SQL インスタンスをホストしているサーバーを指定します。 <br/>*-sqlinstance instanceName*: 既定のサーバー インスタンスで使用される SQL インスタンスの名前を指定します。 <br/>*-forcecreate*: SQL データベースが既に存在していても、強制的に作成します。 <br/>*-skipcreate*: SQL データベースの作成をスキップします。 これは -forcecreate に優先します。<br/>*-reserveports*: サービスに関連付けられている HTTP ポートの予約を試行します。<br/>*-unreserveports*: サービスに関連付けられている HTTP ポートの予約の削除を試行します。 これは -reserveports に優先します。<br/>*-inprocess*: 新しいプロセスを生成せずに、現在のプロセスで初期化を行います。 ポートの予約を変更する場合は、管理者特権のアクセス許可で現在のプロセスを起動する必要があります。 |

## <a name="differences-between-the-storage-emulator-and-azure-storage"></a>ストレージ エミュレーターと Azure ストレージとの違い
ストレージ エミュレーターは、ローカルの SQL インスタンスで実行される、エミュレートされた環境であるため、エミュレーターとクラウド上の Azure ストレージ アカウントの機能には違いがあります。

* ストレージ エミュレーターでは、単一の固定アカウントと既知の認証キーのみがサポートされます。
* ストレージ エミュレーターはスケーラブルなストレージ サービスではなく、多数の同時クライアントはサポートされません。
* 「 [ストレージ エミュレーターでのリソースのアドレス指定](#addressing-resources-in-the-storage-emulator)」で説明したように、ストレージ エミュレーターでは、リソースは Azure ストレージ アカウントとは異なる方法でアドレス指定されます。 この違いは、クラウドではドメイン名解決が使用できても、ローカル コンピューターでは使用できないことが原因です。
* Version 3.1 以降では、ストレージ エミュレーター アカウントで読み取りアクセスの geo 冗長レプリケーション (RA-GRS) がサポートされます。 エミュレーターでは、すべてのアカウントで RA-GRS が有効になっていて、プライマリ レプリカとセカンダリ レプリカの間に時間差が生じることはありません。 Get Blob Service Stats、Get Queue Service Stats、および Get Table Service Stats 操作は、アカウントのセカンダリ拠点でサポートされており、常に `LastSyncTime` 応答要素の値を、基になる SQL データベースに準じた現在時刻として返します。

    ストレージ エミュレーターを使用した、プログラムによるセカンダリへのアクセスには、.NET 用ストレージ クライアント ライブラリの Version 3.2 以降を使用してください。 詳細については、「 [.NET 用の Microsoft Azure Storage クライアント ライブラリ](https://msdn.microsoft.com/library/azure/dn261237.aspx) 」を参照してください。
* File サービスおよび SMB プロトコル サービス エンドポイントは、ストレージ エミュレーターでは現在サポートされていません。
* エミュレーターでサポートされていないストレージ サービスのバージョンを使用する場合、ストレージ エミュレーターは VersionNotSupportedByEmulator エラー (HTTP 状態コード 400 - Bad Request) を返します。

### <a name="differences-for-blob-storage"></a>BLOB ストレージに対する相違点
以下の相違点が、エミュレーターの BLOB ストレージに該当します。

* ストレージ エミュレーターでは、最大で 2 GB のサイズの BLOB だけがサポートされます。
* 増分コピーを使用すると、上書きされた BLOB からのスナップショットをコピーできます。これにより、サービスでエラーが返されます。
* Incremental Copy BLOB を使用してコピーされたスナップショット間では、ページ範囲の差分の取得は機能しません。
* アクティブなリースを持つストレージ エミュレーター内に存在する BLOB に対する Put Blob 操作が、要求でリース ID が指定されなかった場合でも、成功することがあります。
* エミュレーターでは追加 BLOB の操作はサポートされません。 追加 BLOB で操作をしようとすると、FeatureNotSupportedByEmulator エラー (HTTP ステータス コード 400 - Bad Request) が返されます。

### <a name="differences-for-table-storage"></a>テーブル ストレージに対する相違点
以下の相違点が、エミュレーターのテーブル ストレージに該当します。

* ストレージ エミュレーターの Table service での日付プロパティは、SQL Server 2005 でサポートされている範囲だけをサポートします (*つまり*、1753 年 1 月 1 日より後である必要があります)。 1753 年 1 月 1 日より前のすべての日付は、この値に変更されます。 日付の精度は、SQL Server 2005 の精度までに制限されます。つまり、日付の精度は 1/300 秒です。
* ストレージ エミュレーターでは、それぞれ 512 バイト未満のパーティション キーと行キーのプロパティ値がサポートされます。 また、アカウント名、テーブル名、およびキー プロパティ名の合計サイズが 900 バイトを超えることはできません。
* ストレージ エミュレーターのテーブル内の行の合計サイズは、1 MB 未満に制限されます。
* ストレージ エミュレーターで、データ型 `Edm.Guid` または `Edm.Binary` のプロパティは、クエリ フィルター文字列で `Equal (eq)` および `NotEqual (ne)` 比較演算子だけをサポートします。

### <a name="differences-for-queue-storage"></a>キュー ストレージに対する相違点
エミュレーターのキュー ストレージに固有の違いはありません。

## <a name="storage-emulator-release-notes"></a>ストレージ エミュレーター リリース ノート
### <a name="version-51"></a>バージョン 5.1
* 一部の応答でサービスが返していなかった `DataServiceVersion` ヘッダーをストレージ エミュレーターが返していたバグを修正しました。

### <a name="version-50"></a>バージョン 5.0
* ストレージ エミュレーターのインストーラーが既存の MSSQL や .NET Framework のインストールをチェックしなくなりました。
* ストレージ エミュレーターのインストーラーがインストールの一部としてデータベースを作成しなくなりました。  データベースはスタートアップの一部として必要な場合は作成されます。
* データベースの作成に管理者特権が不要になりました。
* スタートアップでポートの予約が不要になりました。
* *init* に -reserveports (管理者特権が必要)、-unreserveports (管理者特権が必要)、-skipcreate のオプションを追加しました。
* システム トレイ アイコンのストレージ エミュレーター UI オプションがコマンド ライン インターフェイスを起動するようになりました。  古い GUI は使用できなくなりました。
* 一部の DLL が削除または名前が変更されました。

### <a name="version-46"></a>バージョン 4.6
* ストレージ エミュレーターで、BLOB、Queue、および Table service エンドポイント上のストレージ サービスのバージョン 2016-05-31 がサポートされるようになりました。

### <a name="version-45"></a>バージョン 4.5
* バックアップ データベースの名前が変更された場合に、ストレージ エミュレーターの初期化とインストールが失敗する原因となったバグを修正しました。

### <a name="version-44"></a>バージョン 4.4
* ストレージ エミュレーターで、BLOB、Queue、および Table service エンドポイント上のストレージ サービスのバージョン 2015-12-11 がサポートされるようになりました。
* ストレージ エミュレーターの BLOB データに対するガベージ コレクションが、多数の BLOB を処理する場合に、より効率的になりました。
* コンテナー ACL XML の検証が、ストレージ サービスによる検証と少し異なる方法で行われる原因となるバグを修正しました。
* 最大および最小 DateTime 値が不適切なタイム ゾーンで報告される場合があるバグを修正しました。

### <a name="version-43"></a>バージョン 4.3
* ストレージ エミュレーターで、BLOB、Queue、および Table サービス エンドポイント上のストレージ サービスのバージョン 2015-07-08 がサポートされるようになりました。

### <a name="version-42"></a>バージョン 4.2
* ストレージ エミュレーターで、BLOB、Queue、および Table サービス エンドポイント上のストレージ サービスのバージョン 2015-04-05 がサポートされるようになりました。

### <a name="version-41"></a>Version 4.1
* ストレージ エミュレーターでは、新しい追加 BLOB 機能を除く BLOB、Queue、Table service エンドポイント上のストレージ サービスのバージョン 2015-02-21 がサポートされるようになりました。
* エミュレーターでサポートされていないストレージ サービスのバージョンを使用する場合でも、エミュレーターでは意味のあるエラー メッセージを返します。 最新バージョンのエミュレーターの使用をお勧めします。 VersionNotSupportedByEmulator エラー (HTTP ステータス コード 400 - Bad Request) が発生する場合、最新バージョンのストレージ エミュレーターをダウンロードしてください。
* 競争状態のバグが原因となる同時マージ操作時のテーブル エンティティ データの間違いが修正されました。

### <a name="version-40"></a>Version 4.0
* ストレージ エミュレーターの実行可能ファイルの名前が *AzureStorageEmulator.exe*に変更されました。

### <a name="version-32"></a>Version 3.2
* ストレージ エミュレーターで、BLOB、Queue、および Table サービス エンドポイント上のストレージ サービスのバージョン 2014-02-14 がサポートされるようになりました。 File サービス エンドポイントは、ストレージ エミュレーターでは現在サポートされていません。 バージョン 2014-02-14 の詳細については、 [Azure Storage サービスのバージョン管理](https://msdn.microsoft.com/library/azure/dd894041.aspx) に関するページを参照してください。

### <a name="version-31"></a>Version 3.1
* 読み取りアクセス地理冗長ストレージ (RA-GRS) が、ストレージ エミュレーターでサポートされるようになりました。 Get Blob Service Stats、Get Queue Service Stats、および Get Table Service Stats API は、アカウントのセカンダリ拠点でサポートされており、常に LastSyncTime 応答要素の値を、基になる SQL データベースに準じた現在時刻として返します。 ストレージ エミュレーターを使用した、プログラムによるセカンダリへのアクセスには、.NET 用ストレージ クライアント ライブラリの Version 3.2 以降を使用してください。 詳細については、.NET リファレンス用の Microsoft Azure Storage クライアント ライブラリを参照してください。

### <a name="version-30"></a>Version 3.0
* Azure ストレージ エミュレーターが、コンピューティング エミュレーターと同じパッケージには同梱されないようになりました。
* ストレージ エミュレーターのグラフィカル ユーザー インターフェイスが、スクリプト可能なコマンド ライン インターフェイスを優先して、廃止されました。 コマンド ライン インターフェイスの詳細については、ストレージ エミュレーター コマンド ライン ツールのリファレンスを参照してください。 グラフィカル インターフェイスはバージョン 3.0 までは引き続き存在しますが、計算エミュレーターがインストールされている場合にシステム トレイ アイコンを右クリックして [ストレージ エミュレーター UI の表示] を選択することによってのみアクセスできます。
* Azure ストレージ サービスのバージョン 2013-08-15 が、完全にサポートされるようになりました。 (以前は、このバージョンはストレージ エミュレーター バージョン 2.2.1 プレビューだけでサポートされていました。)

