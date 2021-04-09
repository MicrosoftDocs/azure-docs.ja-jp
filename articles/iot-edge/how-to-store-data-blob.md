---
title: デバイスでのブロック blob の格納 -Azure IoT Edge | Microsoft Docs
description: 階層化機能と Time-To-Live 機能を理解してから、サポートされている Blob Storage の操作を確認し、その後、ご自身の Blob Storage アカウントに接続します。
author: kgremban
ms.author: kgremban
ms.reviewer: arduppal
ms.date: 12/13/2019
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: 5954c3083afc73fb25c796086f8fb8809af03ec1
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "103200662"
---
# <a name="store-data-at-the-edge-with-azure-blob-storage-on-iot-edge"></a>IoT Edge 上の Azure Blob Storage を使用してエッジにデータを格納する

[!INCLUDE [iot-edge-version-all-supported](../../includes/iot-edge-version-all-supported.md)]

IoT Edge の Azure Blob Storage では、エッジで[ブロック BLOB](/rest/api/storageservices/understanding-block-blobs--append-blobs--and-page-blobs#about-block-blobs) および[追加 BLOB](/rest/api/storageservices/understanding-block-blobs--append-blobs--and-page-blobs#about-append-blobs) ストレージのソリューションが提供されます。 ご利用の IoT Edge デバイス上の BLOB ストレージ モジュールは Azure の BLOB サービスのように動作しますが、その BLOB はご利用の IoT Edge デバイス上でローカルに格納されます。 同じ Azure Storage SDK メソッドまたは既に慣れている BLOB API 呼び出しを使用して、ご自分の BLOB にアクセスできます。 この記事では、ご利用の IoT Edge デバイス上で Blob service を実行する IoT Edge コンテナー上の Azure Blob Storage に関連する概念について説明します。

このモジュールは、次のシナリオで役立ちます。

* データを処理するかクラウドに転送できる状態になるまでデータをローカルに格納する必要のある場合。 そうしたデータには、動画、画像、ファイナンス データ、病院データなど、あらゆる非構造化データが考えられます。
* 接続が制限されている場所にデバイスが配置されている場合。
* 緊急事態にできるだけ迅速に対応できるように、データをローカルで効率的に処理し、データに短い待機時間でアクセスしたい場合。
* 帯域幅のコストを削減し、テラバイト単位のデータをクラウドに転送しないようにする場合。 データをローカルで処理し、処理されたデータのみをクラウドに送信することができます。

概要紹介のビデオを見る
> [!VIDEO https://www.youtube.com/embed/xbwgMNGB_3Y]

このモジュールには、**deviceToCloudUpload** 機能と **deviceAutoDelete** 機能が付属しています。

**deviceToCloudUpload** は構成可能な機能です。 この機能は、断続的なインターネット接続のサポートによりローカルの Blob Storage から Azure にデータを自動的にアップロードすることができます。 これにより次の操作を行うことができます。

* deviceToCloudUpload 機能のオン/オフを切り替える。
* データを Azure にコピーする順序 (NewestFirst や OldestFirst など) を選択する。
* データのアップロード先の Azure Storage アカウントを選択する。
* Azure にアップロードするコンテナーを指定する。 このモジュールでは、ソースとターゲットの両方のコンテナー名を指定できます。
* クラウド ストレージへのアップロードが完了した後ですぐに BLOB を削除する機能を選択する
* 完全な BLOB アップロード (`Put Blob` 操作を使用) とブロック レベルのアップロード (`Put Block`、`Put Block List`、および `Append Block` 操作を使用) を行う。

このモジュールは、BLOB がブロックで構成されている場合、ブロック レベルのアップロードを使用します。 一般的なシナリオのいくつかを次に示します。

* アプリケーションにより、以前にアップロードしたブロック BLOB のいくつかのブロックが更新されるか、追加 BLOB に新しいブロックが追加されると、このモジュールでは、BLOB 全体ではなく更新されたブロックのみがアップロードされます。
* BLOB のアップロード中にインターネット接続がなくなると、接続が戻ったときに、BLOB 全体ではなく残りのブロックのみアップロードされます。

BLOB のアップロード中に予期しないプロセスの終了 (電源障害など) が発生すると、モジュールがオンラインに戻ったときに、アップロード予定だったすべてのブロックが再度アップロードされます。

**deviceAutoDelete** は構成可能な機能です。 この機能では、指定した期限 (分単位) が過ぎると、モジュールによって BLOB がローカル ストレージから自動的に削除されます。 これにより次の操作を行うことができます。

* deviceAutoDelete 機能のオン/オフを切り替える。
* BLOB が自動的に削除されるまでの分単位の時間 (deleteAfterMinutes) を指定する。
* deleteAfterMinutes 値が期限切れになった場合に、アップロード中は BLOB を保持する機能を選択する。

## <a name="prerequisites"></a>前提条件

Azure IoT Edge デバイス:

* [Linux デバイス](quickstart-linux.md) または [Windows デバイス](quickstart.md)のクイック スタートに記載された手順に従って、開発マシンまたは仮想マシンを IoT Edge デバイスとして使用できます。

* サポートされているオペレーティング システムおよびアーキテクチャの一覧については、「[Azure IoT Edge のサポートされるシステム](support.md#operating-systems)」を参照してください。 IoT Edge モジュールの Azure Blob Storage では、次のアーキテクチャがサポートされます。
  * Windows AMD64
  * Linux AMD64
  * Linux ARM32
  * Linux ARM64 (プレビュー)

クラウド リソース:

Azure の Standard レベルの [IoT Hub](../iot-hub/iot-hub-create-through-portal.md)。

## <a name="devicetocloudupload-and-deviceautodelete-properties"></a>deviceToCloudUpload および deviceAutoDelete のプロパティ

このモジュールの必要なプロパティを使用して、**deviceToCloudUploadProperties** と **deviceAutoDeleteProperties** を設定します。 必要なプロパティは、デプロイ中に設定したり、モジュール ツインを編集することで、再デプロイすることなく後で変更したりできます。 値が確実に正しく反映されるよう、`reported configuration` と `configurationValidation` の "モジュール ツイン" をチェックすることをお勧めします。

### <a name="devicetoclouduploadproperties"></a>deviceToCloudUploadProperties

この設定の名前は `deviceToCloudUploadProperties` です。 IoT Edge シミュレーターを使用している場合は、値を、これらのプロパティの関連する環境変数 (説明セクションに記載) に設定します。

| プロパティ | 指定できる値 | 説明 |
| ----- | ----- | ---- |
| uploadOn | true、false | 既定では `false` に設定されています。 この機能をオンにする場合は、このフィールドを `true` に設定します。 <br><br> 環境変数: `deviceToCloudUploadProperties__uploadOn={false,true}` |
| uploadOrder | NewestFirst、OldestFirst | データを Azure にコピーする順序を選択できます。 既定では `OldestFirst` に設定されています。 順序は BLOB の最終更新時刻によって決定されます。 <br><br> 環境変数: `deviceToCloudUploadProperties__uploadOrder={NewestFirst,OldestFirst}` |
| cloudStorageConnectionString |  | `"DefaultEndpointsProtocol=https;AccountName=<your Azure Storage Account Name>;AccountKey=<your Azure Storage Account Key>;EndpointSuffix=<your end point suffix>"` は、データのアップロード先のストレージ アカウントを選択するための接続文字列です。 `Azure Storage Account Name`、`Azure Storage Account Key`、`End point suffix` を指定します。 データのアップロード先の Azure の適切な EndpointSuffix を追加します。これはグローバル Azure、政府機関向け Azure、および Microsoft Azure Stack ごとに異なります。 <br><br> ここで Azure Storage SAS 接続文字列を指定できます。 ただし、有効期限が切れた場合は、このプロパティを更新する必要があります。 SAS のアクセス許可には、コンテナーの作成アクセスと、BLOB の作成、書き込み、追加アクセスが含まれる場合があります。  <br><br> 環境変数: `deviceToCloudUploadProperties__cloudStorageConnectionString=<connection string>` |
| storageContainersForUpload | `"<source container name1>": {"target": "<target container name>"}`,<br><br> `"<source container name1>": {"target": "%h-%d-%m-%c"}`, <br><br> `"<source container name1>": {"target": "%d-%c"}` | Azure にアップロードするコンテナーの名前を指定できます。 このモジュールでは、ソースとターゲットの両方のコンテナー名を指定できます。 ターゲット コンテナー名を指定しない場合、コンテナー名は自動的に `<IoTHubName>-<IotEdgeDeviceID>-<ModuleName>-<SourceContainerName>` として割り当てられます。 ターゲット コンテナー名のテンプレート文字列を作成して、使用可能な値の列をチェックアウトできます。 <br>* %h -> IoT Hub 名 (3 ～ 50 文字)。 <br>* %d -> IoT Edge デバイス ID (1 ～ 129 文字)。 <br>* %m -> モジュール名 (1 ～ 64 文字)。 <br>* %c -> ソース コンテナー名 (3 ～ 63 文字)。 <br><br>コンテナー名の最大サイズは 63 文字です。ターゲット コンテナー名を自動的に割り当てる場合は、コンテナーのサイズが 63 文字を超えると、各セクション (IoTHubName、IotEdgeDeviceID、ModuleName、SourceContainerName) が 15 文字まで削除されます。 <br><br> 環境変数: `deviceToCloudUploadProperties__storageContainersForUpload__<sourceName>__target=<targetName>` |
| deleteAfterUpload | true、false | 既定では `false` に設定されています。 `true` に設定すると、クラウド ストレージへのアップロードが完了したときにデータが自動的に削除されます。 <br><br> **注意**:追加 BLOB を使用している場合、この設定は、アップロードの成功後にローカル ストレージから追加 BLOB を削除し、それ以降、それらの BLOB へのブロック追加操作はすべて失敗します。 この設定は注意して使用してください。アプリケーションで追加操作がまれにしか行われない、または連続追加操作がサポートされていない場合は、これを有効にしないでください<br><br> 環境変数: `deviceToCloudUploadProperties__deleteAfterUpload={false,true}`。 |

### <a name="deviceautodeleteproperties"></a>deviceAutoDeleteProperties

この設定の名前は `deviceAutoDeleteProperties` です。 IoT Edge シミュレーターを使用している場合は、値を、これらのプロパティの関連する環境変数 (説明セクションに記載) に設定します。

| プロパティ | 指定できる値 | 説明 |
| ----- | ----- | ---- |
| deleteOn | true、false | 既定では `false` に設定されています。 この機能をオンにする場合は、このフィールドを `true` に設定します。 <br><br> 環境変数: `deviceAutoDeleteProperties__deleteOn={false,true}` |
| deleteAfterMinutes | `<minutes>` | 時間を分単位で指定します。 この値が期限切れになると、モジュールが自動的にローカル ストレージから BLOB を削除します。 現在許可されている最大時間 (分単位) は 35791 です。 <br><br> 環境変数: `deviceAutoDeleteProperties__ deleteAfterMinutes=<minutes>` |
| retainWhileUploading | true、false | 既定では `true` に設定されていて、deleteAfterMinutes が期限切れになった場合に、クラウド ストレージへのアップロード中は BLOB が保持されます。 `false` に設定することができ、その場合は deleteAfterMinutes が期限切れになるとすぐにデータが削除されます。 注:このプロパティを機能させるには、UploadOn が true に設定されている必要があります。  <br><br> **注意**:追加 BLOB を使用している場合、この設定は、値の有効期限が切れるとローカル ストレージから追加 BLOB を削除し、それ以降、それらの BLOB へのブロック追加操作はすべて失敗します。 アプリケーションによって実行される追加操作の予想される頻度に対して、有効期限の値が十分に大きいことを確認してください。<br><br> 環境変数: `deviceAutoDeleteProperties__retainWhileUploading={false,true}`|

## <a name="using-smb-share-as-your-local-storage"></a>ローカル ストレージとして SMB 共有を使用する

Windows ホストにこのモジュールの Windows コンテナーをデプロイするときに、ローカル ストレージ パスとして SMB 共有を指定できます。

SMB 共有と IoT デバイスが相互に信頼されたドメインにあることを確認してください。

`New-SmbGlobalMapping` PowerShell コマンドを実行して、Windows を実行している IoT デバイス上でローカルに SMB 共有をマップすることができます。

構成手順は次のとおりです。

```PowerShell
$creds = Get-Credential
New-SmbGlobalMapping -RemotePath <remote SMB path> -Credential $creds -LocalPath <Any available drive letter>
```

次に例を示します。

```powershell
$creds = Get-Credential
New-SmbGlobalMapping -RemotePath \\contosofileserver\share1 -Credential $creds -LocalPath G:
```

このコマンドは、資格情報を使用してリモート SMB サーバーで認証を行います。 次に、リモート共有パスを G: ドライブ文字にマップします (他の使用可能なドライブ文字を指定できます)。 これで、IoT デバイスのデータ ボリュームが G: ドライブのパスにマップされました。

IoT デバイスのユーザーがリモート SMB 共有に対して読み取りおよび書き込みできることを確認してください。

実際のデプロイでは、`<storage mount>` の値として **G:/ContainerData:C:/BlobRoot** を指定できます。

## <a name="granting-directory-access-to-container-user-on-linux"></a>Linux のコンテナー ユーザーにディレクトリ アクセスを許可する

Linux コンテナーの作成オプションでストレージに[ボリューム マウント](https://docs.docker.com/storage/volumes/)を使用した場合は、追加の手順を実行する必要はありませんが、[バインド マウント](https://docs.docker.com/storage/bind-mounts/)を使用した場合は、サービスを正しく実行するために次の手順が必要になります。

ユーザーのアクセス権を作業の実行に必要な最小限のアクセス許可に制限する最小限の特権の原則に従って、このモジュールには、ユーザー (名前: absie、ID:11000) とユーザー グループ (名前: absie、ID:11000) が含まれています。 コンテナーが **ルート** として開始された場合 (既定のユーザーは **ルート**)、サービスは低い特権の **absie** ユーザーとして開始されます。

この動作により、サービスが正常に動作するために、ホスト パス バインドのアクセス許可の構成が重要になります。構成によっては、アクセス拒否エラーが発生してサービスがクラッシュします。 ディレクトリ バインディングで使用されるパスには、コンテナー ユーザー (例: absie 11000) がアクセスできる必要があります。 ホストで次のコマンドを実行して、コンテナー ユーザーにディレクトリへのアクセス権を付与できます。

```terminal
sudo chown -R 11000:11000 <blob-dir>
sudo chmod -R 700 <blob-dir>
```

次に例を示します。

```terminal
sudo chown -R 11000:11000 /srv/containerdata
sudo chmod -R 700 /srv/containerdata
```

**absie** 以外のユーザーとしてサービスを実行する必要がある場合は、配置マニフェストの createOptions の "User" プロパティでカスタム ユーザー ID を指定できます。 このような場合は、既定値またはルート グループ ID `0` を使用する必要があります。

```json
"createOptions": {
  "User": "<custom user ID>:0"
}
```

ここで、コンテナー ユーザーにディレクトリへのアクセスを許可します。

```terminal
sudo chown -R <user ID>:<group ID> <blob-dir>
sudo chmod -R 700 <blob-dir>
```

## <a name="configure-log-files"></a>ログ ファイルを構成する

お使いのモジュールのログ ファイルの構成については、こちらの[運用環境のベスト プラクティス](./production-checklist.md#set-up-logs-and-diagnostics)をご覧ください。

## <a name="connect-to-your-blob-storage-module"></a>ご自分の BLOB ストレージ モジュールに接続する

ご自分のモジュールに対して構成したアカウント名とアカウントキーを使用して、ご利用の IoT Edge デバイス上の BLOB ストレージにアクセスできます。

作成する任意のストレージ要求に対する BLOB エンドポイントとして、ご利用の IoT Edge デバイスを指定します。 構成した IoT Edge デバイス情報とアカウント名を使用して、[明示的なストレージ エンドポイントへの接続文字列を作成](../storage/common/storage-configure-connection-string.md#create-a-connection-string-for-an-explicit-storage-endpoint)できます。

* Azure Blob Storage on IoT Edge モジュールが実行されているデバイスにデプロイされているモジュールの場合、BLOB エンドポイントは `http://<module name>:11002/<account name>` になります。
* 別のデバイスで実行されているモジュールまたはアプリケーションの場合、実際のネットワークの適切なエンドポイントを選択する必要があります。 ネットワーク設定に応じて、外部モジュールまたはアプリケーションからのデータ トラフィックが Azure Blob Storage on IoT Edge モジュールを実行しているデバイスに到達できるようにエンドポイントの形式を選択します。 このシナリオの BLOB エンドポイントは次のいずれかです。
  * `http://<device IP >:11002/<account name>`
  * `http://<IoT Edge device hostname>:11002/<account name>`
  * `http://<fully qualified domain name>:11002/<account name>`
 
 > [!IMPORTANT]
 > Azure IoT Edge は、モジュールを呼び出すときに大文字と小文字を区別し、Storage SDK も既定で小文字になります。 [Azure Marketplace](how-to-deploy-modules-portal.md#deploy-modules-from-azure-marketplace) でのモジュールの名前は **AzureBlobStorageonIoTEdge** ですが、名前を小文字に変更すると、IoT Edge モジュールの Azure Blob Storage への接続が中断されないことが保証されます。
 
## <a name="azure-blob-storage-quickstart-samples"></a>Azure Blob Storage のクイックスタートのサンプル

Azure Blob Storage のドキュメントには、複数の言語のクイック スタートのサンプル コードが含まれています。 ご利用のローカル Blob Storage モジュールに接続するように BLOB エンドポイントを変更して、これらのサンプルを実行し、IoT Edge の Azure Blob Storage をテストできます。

次のクイック スタート サンプルでは IoT Edge からもサポートされる言語を使用するため、BLOB ストレージ モジュールと共に IoT Edge モジュールとして言語をデプロイできます。

* [.NET](../storage/blobs/storage-quickstart-blobs-dotnet.md)
  * Iot Edge モジュール (v1.4.0 以前) 上の Azure Blob Storage は、WindowsAzure.Storage 9.3.3 SDK と互換性があります。また、v1.4.1 では、Azure.Storage.Blobs 12.8.0 SDK もサポートされています。
* [Python](../storage/blobs/storage-quickstart-blobs-python.md)
  * バージョン 2.1 より前の Python SDK には、このモジュールが BLOB の作成時刻を返さないという既知の問題があります。 この問題により、list_blobs (BLOB の一覧表示) などの一部のメソッドが機能しません。 回避策として、BLOB クライアントで API バージョンを「'2017-04-17'」に明示的に設定します。 例: `block_blob_service._X_MS_VERSION = '2017-04-17'`
  * [追加 BLOB のサンプル](https://github.com/Azure/azure-storage-python/blob/master/samples/blob/append_blob_usage.py)
* [Node.js](../storage/blobs/storage-quickstart-blobs-nodejs-legacy.md)
* [JS/HTML](../storage/blobs/storage-quickstart-blobs-javascript-client-libraries-legacy.md)
* [Ruby](../storage/blobs/storage-quickstart-blobs-ruby.md)
* [Go](../storage/blobs/storage-quickstart-blobs-go.md)
* [PHP](../storage/blobs/storage-quickstart-blobs-php.md)

## <a name="connect-to-your-local-storage-with-azure-storage-explorer"></a>Azure Storage Explorer を使用してお使いのローカル ストレージに接続する

[Azure Storage Explorer](https://azure.microsoft.com/features/storage-explorer/) を使用して、ご自身のローカル ストレージ アカウントに接続できます。

1. Azure Storage Explorer をダウンロードしてインストールします

1. 接続文字列を使用して Azure Storage に接続します

1. 接続文字列 (`DefaultEndpointsProtocol=http;BlobEndpoint=http://<host device name>:11002/<your local account name>;AccountName=<your local account name>;AccountKey=<your local account key>;`) を指定します

1. 接続する手順を実行します。

1. ご自分のローカル ストレージ アカウント内にコンテナーを作成します

1. ブロック BLOB または追加 BLOB としてファイルのアップロードを開始します。
   > [!NOTE]
   > このモジュールでは、ページ BLOB はサポートされていません。

1. Storage Explorer で Azure Storage アカウントを接続するように選択することもできます。 この構成により、1 つのビューにローカル ストレージ アカウントと Azure ストレージ アカウントの両方を表示できます。

## <a name="supported-storage-operations"></a>サポートされるストレージ操作

IoT Edge 上の BLOB ストレージ モジュールでは Azure Storage SDK が使用され、ブロック BLOB エンドポイント用の 2017-04-17 バージョンの Azure Storage API と一貫性があります。

すべての Azure Blob Storage の操作が、IoT Edge の Azure Blob Storage でサポートされるわけではないため、このセクションでは、それぞれの状態の一覧を示します。

### <a name="account"></a>Account

サポート対象:

* コンテナーの一覧表示

サポート外:

* Blob service プロパティの取得と設定
* BLOB 要求のプレフライト
* Blob service の統計情報の取得
* アカウント情報の取得

### <a name="containers"></a>Containers

サポート対象:

* コンテナーの作成と削除
* コンテナーのプロパティとメタデータの取得
* BLOB を一覧表示する
* コンテナー ACL の取得と設定
* コンテナー メタデータの設定

サポート外:

* コンテナーのリース

### <a name="blobs"></a>BLOB

サポート対象:

* BLOB の配置、取得、削除
* BLOB プロパティの取得と設定
* BLOB メタデータの取得と設定

サポート外:

* BLOB のリリース
* BLOB のスナップショット
* BLOB のコピーとコピーの中止
* BLOB の削除の取り消し
* BLOB レベルの設定

### <a name="block-blobs"></a>ブロック blob

サポート対象:

* ブロックの配置
* ブロック一覧の配置と取得

サポート外:

* URL からブロックの配置

### <a name="append-blobs"></a>追加 BLOB

サポート対象:

* ブロックの追加

サポート外:

* URL からブロックの追加

## <a name="event-grid-on-iot-edge-integration"></a>Event Grid on IoT Edge の統合

> [!CAUTION]
> Event Grid on IoT Edge との統合はプレビュー段階です。

この Azure Blob Storage on IoT Edge モジュールを Event Grid on IoT Edge と統合できるようになりました。 この統合の詳細については、[モジュールのデプロイ、イベントの発行、イベント配信の確認に関するチュートリアル](../event-grid/edge/react-blob-storage-events-locally.md)を参照してください。

## <a name="release-notes"></a>リリース ノート

こちらは、このモジュール用の [Docker Hub のリリース ノート](https://hub.docker.com/_/microsoft-azure-blob-storage)です。 バグの修正と修復に関連する詳細情報は、特定のバージョンのリリース ノートで確認できる場合があります。

## <a name="suggestions"></a>検索候補

お客様のフィードバックは、このモジュールとその機能を便利で使いやすいものにするために、Microsoft にとって重要です。 Microsoft では、今後の改良の参考とするために、皆様からのフィードバックをお待ちしています。

absiotfeedback@microsoft.com までお寄せください

## <a name="next-steps"></a>次のステップ

[Azure Blob Storage を IoT Edge にデプロイする](how-to-deploy-blob.md)方法を学習する

[IoT Edge 上のAzure Blob Storage ブログ](https://aka.ms/abs-iot-blogpost)の最新の更新とお知らせによって最新情報を得る
