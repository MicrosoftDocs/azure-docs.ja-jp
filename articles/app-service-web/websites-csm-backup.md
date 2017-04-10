---
title: "REST を使用して App Service アプリのバックアップと復元を実行する"
description: "RESTful API 呼び出しを使用して、Azure App Service でアプリのバックアップと復元を行う方法について説明します。"
services: app-service
documentationcenter: 
author: NKing92
manager: erikre
editor: 
ms.assetid: f94d0aea-edc1-43ab-9b51-ea7375859276
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/10/2016
ms.author: nicking
translationtype: Human Translation
ms.sourcegitcommit: dcda8b30adde930ab373a087d6955b900365c4cc
ms.openlocfilehash: 1ad2911f809a17e4a6c0f2fe9087e1d9eb2da39e
ms.lasthandoff: 02/16/2017


---
# <a name="use-rest-to-back-up-and-restore-app-service-apps"></a>REST を使用して App Service アプリのバックアップと復元を実行する
> [!div class="op_single_selector"]
> * [PowerShell](../app-service/app-service-powershell-backup.md)
> * [REST API](websites-csm-backup.md)
> 
> 

[App Service アプリ](https://azure.microsoft.com/services/app-service/web/)は Azure Storage に BLOB としてバックアップできます。 バックアップには、アプリのデータベースを含めることもできます。 アプリが誤って削除された場合、あるいは以前のバージョンに戻す必要がある場合、前のバックアップから復元できます。 バックアップは必要に応じていつでも実行できます。あるいは、適切な間隔でバックアップのスケジュールを作成できます。

この記事では、RESTful API 要求を使用してアプリのバックアップと復元を実行する方法について説明します。 Azure ポータルでアプリのバックアップを視覚的に作成し、管理する場合は、「 [Azure App Service での Web アプリのバックアップ](web-sites-backup.md)

<a name="gettingstarted"></a>

## <a name="getting-started"></a>Getting Started (概要)
REST 要求を送信するには、アプリの**名前**、**リソース グループ**、**サブスクリプション ID** を把握しておく必要があります。 この情報は、 **Azure ポータル** の [[App Service]](https://portal.azure.com)ブレードでアプリをクリックすると確認できます。 この記事の例では、Web サイト **backuprestoreapiexamples.azurewebsites.net**を構成します。 これは Default-Web-WestUS リソース グループに保存され、ID 00001111-2222-3333-4444-555566667777 のサブスクリプションで実行されます。

![サンプル Web サイト情報][SampleWebsiteInformation]

<a name="backup-restore-rest-api"></a>

## <a name="backup-and-restore-rest-api"></a>REST API のバックアップと復元
REST API を利用してアプリをバックアップし、復元する方法の例をこれからいくつか取り上げます。 それぞれの例に、URL と HTTP の要求本文が含まれます。 サンプル URL には、{subscription-id} のように、中かっこで閉じられたプレースホルダーが含まれます。 このプレースホルダーをアプリの対応する情報に置き換えます。 参照のために、サンプル URL の各プレースホルダーの説明を記載します。

* subscription-id – アプリを含む Azure サブスクリプションの ID
* resource-group-name – アプリを含むリソース グループの名前
* name – アプリの名前
* backup-id – アプリのバックアップの ID

HTTP 要求に追加できる任意のパラメーターを含む、API の完全ドキュメントについては、 [Azure リソース エクスプローラー](https://resources.azure.com/)を参照してください。

<a name="backup-on-demand"></a>

## <a name="backup-an-app-on-demand"></a>アプリのオンデマンド バックアップ
アプリをすぐにバックアップするには、**POST** 要求を **https://management.azure.com/subscriptions/{subscription-id}/resourceGroups/{resource-group-name}/providers/Microsoft.Web/sites/{name}/backup/** に送信します。

Microsoft のサンプル Web サイトを利用した URL は次のようになります。 **https://management.azure.com/subscriptions/00001111-2222-3333-4444-555566667777/resourceGroups/Default-Web-WestUS/providers/Microsoft.Web/sites/backuprestoreapiexamples/backup/**

バックアップの保存に使用するストレージ アカウントを指定するには、要求の本文で JSON オブジェクトを指定します。 JSON オブジェクトには、**storageAccountUrl** という名前のプロパティを含める必要があります。このプロパティには [SAS URL](../storage/storage-dotnet-shared-access-signature-part-1.md) を保持し、バックアップ BLOB を保持する Azure Storage コンテナーに書き込み許可を与えます。 データベースをバックアップする場合、バックアップするデータベースの名前、種類、接続文字列を含む一覧を提供する必要もあります。

```
{
    "properties":
    {
        "storageAccountUrl": “https://account.blob.core.windows.net/backups?sv=2015-02-21&sr=c&sig=DzlkBl7h32C8qCv%2BifdBRxE63r4iv0kZ9L7E0qP16sY%3D&se=2016-09-15T22%3A46%3A54Z&sp=rwdl”,
        “databases”: [
            {
                “databaseType”: “SqlAzure”,
                “name”: “MyDatabase1”,
                "connectionString": "<your database connection string>"
            }
        ]
    }
}
```

要求が受信されると、すぐにアプリのバックアップが開始されます。 バックアップ プロセスの完了には時間がかかる場合があります。 HTTP 応答には ID が含まれます。この ID を別の要求で利用して、バックアップの状態を確認できます。 次に、バックアップ要求に対する HTTP 応答の本文の例を示します。

```
{
    "id": "/subscriptions/00001111-2222-3333-4444-555566667777/resourceGroups/Default-Web-WestUS/providers/Microsoft.Web/sites/backuprestoreapiexamples",
    "name": " backuprestoreapiexamples ",
    "type": "Microsoft.Web/sites",
    "location": "WestUS",
    "properties":    {
        "id": 1,
        "storageAccountUrl": “https://account.blob.core.windows.net/backups?sv=2015-02-21&sr=c&sig=DzlkBl7h32C8qCv%2BifdBRxE63r4iv0kZ9L7E0qP16sY%3D&se=2016-09-15T22%3A46%3A54Z&sp=rwdl”,
        "blobName": “backup_201509291825.zip”,
        "name": "backup_201509291825",
        "status": 4,
        "sizeInBytes": 0,
        "created": "2015-09-29T18:25:26.3992707Z",
        "log": null,
        "databases": [
            {
                "databaseType": "SqlAzure",
                "name": "MyDatabase1",
                "connectionString": "<your database connection string>"
            }
        ],
        "scheduled": false,
        "correlationId": "ea730f4d-dd06-4f7f-a090-9aa09k662h36",
    }
}
```

> [!NOTE]
> エラー メッセージは HTTP 応答のログ プロパティにあります。
> 
> 

<a name="schedule-automatic-backups"></a>

## <a name="schedule-automatic-backups"></a>自動バックアップのスケジュールを作成する
アプリは、オンデマンド バックアップするだけでなく、バックアップのスケジュールを作成し、自動実行することもできます。

### <a name="set-up-a-new-automatic-backup-schedule"></a>新しい自動バックアップ スケジュールを設定する
バックアップ スケジュールを設定するには、**PUT** 要求を **https://management.azure.com/subscriptions/{subscription-id}/resourceGroups/{resource-group-name}/providers/Microsoft.Web/sites/{name}/config/backup**に送信します。

Microsoft のサンプル Web サイトを利用した URL は次のようになります。 **https://management.azure.com/subscriptions/00001111-2222-3333-4444-555566667777/resourceGroups/Default-Web-WestUS/providers/Microsoft.Web/sites/backuprestoreapiexamples/config/backup**

要求本文には、バックアップ構成を指定する JSON オブジェクトを含める必要があります。 すべての必須パラメーターが含まれている例を次に示します。

```
{
    "location": "WestUS",
    "properties": // Represents an app restore request
    {
        "backupSchedule": { // Required for automatically scheduled backups
            "frequencyInterval": "7",
            "frequencyUnit": "Day",
            "keepAtLeastOneBackup": "True",
            "retentionPeriodInDays": "10",
        },
        "enabled": "True", // Must be set to true to enable automatic backups
        "name": “mysitebackup”,
        "storageAccountUrl": "https://account.blob.core.windows.net/backups?sv=2015-02-21&sr=c&sig=DzlkBl7h32C8qCv%2BifdBRxE63r4iv0kZ9L7E0qP16sY%3D&se=2016-09-15T22%3A46%3A54Z&sp=rwdl"
    }
}
```

この例では、7 日ごとに自動的にバックアップするようにアプリを設定します。 パラメーターの **frequencyInterval** と **frequencyUnit** の両方によりバックアップの頻度が決定されます。 **frequencyUnit** の有効な値は **hour** と **day** です。 たとえば、12 時間ごとにアプリをバックアップするには、frequencyInterval を「12」に設定し、frequencyUnit を「hour」に設定します。

古いバックアップはストレージ アカウントから自動的に削除されます。 **retentionPeriodInDays** パラメーターでバックアップの保存期間を指定できます。 保存期間に関係なく、常に少なくとも 1 つのバックアップを保存する場合、 **keepAtLeastOneBackup** を「true」に設定します。

### <a name="get-the-automatic-backup-schedule"></a>自動バックアップ スケジュールを取得する
アプリのバックアップ構成を取得するには、**POST** 要求を URL **https://management.azure.com/subscriptions/{subscription-id}/resourceGroups/{resource-group-name}/providers/Microsoft.Web/sites/{name}/config/backup/list** に送信します。

サンプル サイトの URL は **https://management.azure.com/subscriptions/00001111-2222-3333-4444-555566667777/resourceGroups/Default-Web-WestUS/providers/Microsoft.Web/sites/backuprestoreapiexamples/config/backup/list**です。

<a name="get-backup-status"></a>

## <a name="get-the-status-of-a-backup"></a>バックアップのステータスを取得する
アプリの規模によっては、バックアップの完了に時間がかかる場合があります。 バックアップは失敗したり、タイムアウトになったり、部分的に成功したりすることもあります。 アプリのバックアップの状態を表示するには、**GET** 要求を URL **https://management.azure.com/subscriptions/{subscription-id}/resourceGroups/{resource-group-name}/providers/Microsoft.Web/sites/{name}/backups** に送信します。

特定のバックアップの状態を表示するには、GET 要求を URL **https://management.azure.com/subscriptions/{subscription-id}/resourceGroups/{resource-group-name}/providers/Microsoft.Web/sites/{name}/backups/{backup-id}**に送信します。

Microsoft のサンプル Web サイトを利用した URL は次のようになります。 **https://management.azure.com/subscriptions/00001111-2222-3333-4444-555566667777/resourceGroups/Default-Web-WestUS/providers/Microsoft.Web/sites/backuprestoreapiexamples/backups/1**

応答本文には、この例のような JSON オブジェクトが含まれます。

```
{
    "properties":  {
        "id": 1,
        "storageAccountUrl": "https://account.blob.core.windows.net/backups?sv=2015-02-21&sr=c&sig=DzlkBl7h32C8qCv%2BifdBRxE63r4iv0kZ9L7E0qP16sY%3D&se=2016-09-15T22%3A46%3A54Z&sp=rwdl",
        "blobName": "backup_201509291734.zip",
        "name": "backup_201509291734",
        "status": 2,
        "sizeInBytes": 151973,
        "created": "2015-09-29T17:34:57.2091605",
        "scheduled": false,
        "lastRestoreTimeStamp": null,
        "finishedTimeStamp": "2015-09-29T17:35:11.3293602",
        "correlationId": "2379fc13-418a-4b9c-920d-d06e73c5028d",
        "websiteSizeInBytes": 209920
    }
}
```

バックアップの状態は列挙型です。 次のような状態があります。

* 0 – InProgress: バックアップは開始されていますが、まだ完了していません。
* 1 – Failed: バックアップは失敗しました。
* 2 – Succeeded: バックアップは完了しました。
* 3 – TimedOut: バックアップは時間どおり完了せず、取り消されました。
* 4 – Created: バックアップ要求が待ち行列に入っていますが、開始されていません。
* 5 – Skipped: スケジュールで始動したバックアップの数が多すぎたため、バックアップが進行しませんでした。
* 6 – PartiallySucceeded: バックアップは完了しましたが、読み取れなかったため、バックアップされなかったファイルがあります。 これは通常、ファイルの排他ロックに起因して発生します。
* 7 – DeleteInProgress: バックアップの削除が要求されていますが、まだ削除されていません。
* 8 – DeleteFailed: バックアップを削除できませんでした。 バックアップの作成に使用された SAS URL が失効している可能性があります。
* 9 – Deleted: バックアップは削除されました。

<a name="restore-app"></a>

## <a name="restore-an-app-from-a-backup"></a>アプリをバックアップから復元する
アプリが削除された場合、または以前のバージョンにアプリを戻す場合は、バックアップからアプリを復元できます。 復元を呼び出すには、**POST** 要求を URL **https://management.azure.com/subscriptions/{subscription-id}/resourceGroups/{resource-group-name}/providers/Microsoft.Web/sites/{name}/backups/{backup-id}/restore** に送信します。

Microsoft のサンプル Web サイトを利用した URL は次のようになります。 **https://management.azure.com/subscriptions/00001111-2222-3333-4444-555566667777/resourceGroups/Default-Web-WestUS/providers/Microsoft.Web/sites/backuprestoreapiexamples/backups/1/restore**

要求本文で、復元操作のプロパティを含む JSON オブジェクトを送信します。 次の例にすべての必須プロパティが含まれています。

```
{
    "location": "WestUS",
    "properties":
    {
        "blobName": "backup_201509280431.zip",
        "databases": [ // Not required unless you’re restoring databases
            {
                “databaseType”: “SqlAzure”,
                “name”: “MyDatabase1”
        }],
        "overwrite": "true",
        "storageAccountUrl": "https://account.blob.core.windows.net/backups?sv=2015-02-21&sr=c&sig=DzlkBl7h32C8qCv%2BifdBRxE63r4iv0kZ9L7E0qP16sY%3D&se=2016-09-15T22%3A46%3A54Z&sp=rwdl" // SAS URL to storage container containing your website backup
    }
}
```

### <a name="restore-to-a-new-app"></a>新しいアプリに復元する
バックアップを復元するとき、既存のアプリを上書きするのではなく、新しいアプリを作成した方が便利な場合があります。 その場合は、作成する新しいアプリを指すように要求 URL を変更し、JSON の **overwrite** プロパティを **false** に変更します。

<a name="delete-app-backup"></a>

## <a name="delete-an-app-backup"></a>アプリのバックアップを削除する
バックアップを削除するには、**DELETE** 要求を URL **https://management.azure.com/subscriptions/{subscription-id}/resourceGroups/{resource-group-name}/providers/Microsoft.Web/sites/{name}/backups/{backup-id}**に送信します。

Microsoft のサンプル Web サイトを利用した URL は次のようになります。 **https://management.azure.com/subscriptions/00001111-2222-3333-4444-555566667777/resourceGroups/Default-Web-WestUS/providers/Microsoft.Web/sites/backuprestoreapiexamples/backups/1**

<a name="manage-sas-url"></a>

## <a name="manage-a-backups-sas-url"></a>バックアップの SAS URL を管理する
Azure App Service は、バックアップの作成時に指定された SAS URL を利用して、Azure Storage からバックアップを削除しようとします。 この SAS URL が無効になっている場合、REST API でバックアップを削除することはできません。 ただし、バックアップに関連付けられている SAS URL は、**POST** 要求を URL **https://management.azure.com/subscriptions/{subscription-id}/resourceGroups/{resource-group-name}/providers/Microsoft.Web/sites/{name}/backups/{backup-id}/list** に送信することで更新できます。

Microsoft のサンプル Web サイトを利用した URL は次のようになります。 **https://management.azure.com/subscriptions/00001111-2222-3333-4444-555566667777/resourceGroups/Default-Web-WestUS/providers/Microsoft.Web/sites/backuprestoreapiexamples/backups/1/list**

要求本文で、新しい SAS URL を含む JSON オブジェクトを送信します。 たとえば次のようになります。

```
{
    "properties":
    {
        "storageAccountUrl": "https://account.blob.core.windows.net/backups?sv=2015-02-21&sr=c&sig=DzlkBl7h32C8qCv%2BifdBRxE63r4iv0kZ9L7E0qP16sY%3D&se=2016-09-15T22%3A46%3A54Z&sp=rwdl"
    }
}
```

> [!NOTE]
> セキュリティ上の理由から、特定のバックアップの GET 要求を送信するとき、バックアップに関連付けられている SAS URL は返されません。 バックアップに関連付けられている SAS URL を表示する場合、POST 要求を上記と同じ URL に送信します。 空の JSON オブジェクトを要求本文に追加します。 サーバーからの応答には、SAS URL を含む、そのバックアップのすべての情報が含まれます。
> 
> 

<!-- IMAGES -->
[SampleWebsiteInformation]: ./media/websites-csm-backup/01siteconfig.png

