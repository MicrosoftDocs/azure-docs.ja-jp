---
title: Azure Notification Hubs の登録情報を一括でエクスポートおよびインポートする | Microsoft Docs
description: Notification Hubs の一括サポートを使用して、通知ハブで多数の操作を実行したり、すべての登録情報をエクスポートしたりする方法について説明します。
services: notification-hubs
author: sethmanheim
manager: femila
ms.service: notification-hubs
ms.workload: mobile
ms.tgt_pltfrm: ''
ms.devlang: ''
ms.topic: article
ms.date: 08/04/2020
ms.author: sethm
ms.reviewer: thsomasu
ms.lastreviewed: 03/18/2019
ms.custom: devx-track-csharp
ms.openlocfilehash: 73c19b72c75d9638213f3c813a708a272cbe2a1b
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "102452341"
---
# <a name="export-and-import-azure-notification-hubs-registrations-in-bulk"></a>Azure Notification Hubs の登録情報を一括でエクスポートおよびインポートする

シナリオによっては、通知ハブで多数の登録情報を作成または変更する必要があります。 このようなシナリオとして、バッチ計算の後でタグを更新する場合や、Azure Notification Hubs を使用できるように既存のプッシュ実装を移行する場合などがあります。

この記事では、通知ハブで多数の操作を実行したり、すべての登録情報を一括でエクスポートしたりする方法について説明します。

## <a name="high-level-flow"></a>大まかな流れ

バッチ サポートは、数百万件の登録情報が関係する長時間実行ジョブをサポートできるように設計されています。 バッチ サポートでは、この規模に対応するため、Azure Storage を使用してジョブの詳細と出力を格納します。 一括更新操作の場合、ユーザーは、登録更新操作のリストを内容とするファイルを BLOB コンテナーに作成する必要があります。 ジョブの開始時に、ユーザーは入力 BLOB の URL と、出力ディレクトリ (これも BLOB コンテナー内に含まれる) の URL を提供します。 ジョブの開始後、ユーザーはジョブの開始時に提供した URL の場所にクエリを実行して、ステータスを確認できます。 各ジョブでは、特定の種類の操作 (作成、更新、削除) のみを実行できます。 エクスポート操作も同じように実行されます。

## <a name="import"></a>[インポート]

### <a name="set-up"></a>設定

ここでは、次のエンティティがあることを前提としています。

- プロビジョニングされた通知ハブ。
- Azure Storage BLOB コンテナー。
- [Azure Storage NuGet パッケージ](https://www.nuget.org/packages/windowsazure.storage/)および[Notification Hubs NuGet パッケージ](https://www.nuget.org/packages/Microsoft.Azure.NotificationHubs/)への参照。

### <a name="create-input-file-and-store-it-in-a-blob"></a>入力ファイルを作成して BLOB に格納する

入力ファイルには、XML 形式でシリアル化された登録情報のリストが 1 行につき 1 件ずつ含まれています。 次のコード例は、Azure SDK を使用して登録情報をシリアル化し、BLOB コンテナーにアップロードする方法を示しています。

```csharp
private static void SerializeToBlob(CloudBlobContainer container, RegistrationDescription[] descriptions)
{
    StringBuilder builder = new StringBuilder();
    foreach (var registrationDescription in descriptions)
    {
        builder.AppendLine(registrationDescription.Serialize());
    }

    var inputBlob = container.GetBlockBlobReference(INPUT_FILE_NAME);
    using (MemoryStream stream = new MemoryStream(Encoding.UTF8.GetBytes(builder.ToString())))
    {
        inputBlob.UploadFromStream(stream);
    }
}
```

> [!IMPORTANT]
> 上記のコードでは、登録情報がメモリ内でシリアル化され、ストリーム全体が BLOB にアップロードされます。 数 MB を超えるファイルをアップロードした場合は、Azure BLOB ガイダンスでこれらの手順の実行方法 ([ブロック BLOB](/rest/api/storageservices/Understanding-Block-Blobs--Append-Blobs--and-Page-Blobs) など) を参照してください。

### <a name="create-url-tokens"></a>URL トークンを作成する

入力ファイルをアップロードしたら、入力ファイルと出力ディレクトリの両方の URL を生成して、通知ハブに提供できるようにします。 入力と出力で 2 つの異なる BLOB コンテナーを使用できます。

```csharp
static Uri GetOutputDirectoryUrl(CloudBlobContainer container)
{
    SharedAccessBlobPolicy sasConstraints = new SharedAccessBlobPolicy
    {
        SharedAccessExpiryTime = DateTime.UtcNow.AddDays(1),
        Permissions = SharedAccessBlobPermissions.Write | SharedAccessBlobPermissions.List | SharedAccessBlobPermissions.Read
    };

    string sasContainerToken = container.GetSharedAccessSignature(sasConstraints);
    return new Uri(container.Uri + sasContainerToken);
}

static Uri GetInputFileUrl(CloudBlobContainer container, string filePath)
{
    SharedAccessBlobPolicy sasConstraints = new SharedAccessBlobPolicy
    {
        SharedAccessExpiryTime = DateTime.UtcNow.AddDays(1),
        Permissions = SharedAccessBlobPermissions.Read
    };
    string sasToken = container.GetBlockBlobReference(filePath).GetSharedAccessSignature(sasConstraints);
    return new Uri(container.Uri + "/" + filePath + sasToken);
}
```

### <a name="submit-the-job"></a>ジョブを送信する

入力と出力の 2 つの URL を生成したら、バッチ ジョブを開始できます。

```csharp
NotificationHubClient client = NotificationHubClient.CreateClientFromConnectionString(CONNECTION_STRING, HUB_NAME);
var createTask = client.SubmitNotificationHubJobAsync(
new NotificationHubJob {
        JobType = NotificationHubJobType.ImportCreateRegistrations,
        OutputContainerUri = outputContainerSasUri,
        ImportFileUri = inputFileSasUri
    }
);
createTask.Wait();

var job = createTask.Result;
long i = 10;
while (i > 0 && job.Status != NotificationHubJobStatus.Completed)
{
    var getJobTask = client.GetNotificationHubJobAsync(job.JobId);
    getJobTask.Wait();
    job = getJobTask.Result;
    Thread.Sleep(1000);
    i--;
}
```

この例では、入力 URL と出力 URL に加えて、`NotificationHubJob` オブジェクトを作成します。これには、次のいずれかのタイプを指定できる `JobType` オブジェクトが含まれます。

- `ImportCreateRegistrations`
- `ImportUpdateRegistrations`
- `ImportDeleteRegistrations`

この呼び出しが完了すると、通知ハブによってジョブが続行され、そのステータスを [GetNotificationHubJobAsync](/dotnet/api/microsoft.azure.notificationhubs.notificationhubclient.getnotificationhubjobasync) の呼び出しによって確認できます。

ジョブが完了したら、出力ディレクトリ内の次のファイルを調べて結果を確認できます。

- `/<hub>/<jobid>/Failed.txt`
- `/<hub>/<jobid>/Output.txt`

これらのファイルには、バッチで成功および失敗した操作のリストが含まれています。 ファイル形式は `.cvs` で、各行には元の入力ファイルの行番号と、操作の出力 (通常は作成または更新された登録の説明) が含まれています。

### <a name="full-sample-code"></a>完全なサンプル コード

次のサンプル コードでは、登録情報を通知ハブにインポートします。

```csharp
using Microsoft.Azure.NotificationHubs;
using Microsoft.WindowsAzure.Storage;
using Microsoft.WindowsAzure.Storage.Blob;
using System;
using System.Collections.Generic;
using System.Globalization;
using System.IO;
using System.Linq;
using System.Runtime.Serialization;
using System.Text;
using System.Threading;
using System.Threading.Tasks;
using System.Xml;

namespace ConsoleApplication1
{
    class Program
    {
        private static string CONNECTION_STRING = "---";
        private static string HUB_NAME = "---";
        private static string INPUT_FILE_NAME = "CreateFile.txt";
        private static string STORAGE_ACCOUNT = "---";
        private static string STORAGE_PASSWORD = "---";
        private static StorageUri STORAGE_ENDPOINT = new StorageUri(new Uri("---"));

        static void Main(string[] args)
        {
            var descriptions = new[]
            {
                new MpnsRegistrationDescription(@"http://dm2.notify.live.net/throttledthirdparty/01.00/12G9Ed13dLb5RbCii5fWzpFpAgAAAAADAQAAAAQUZm52OkJCMjg1QTg1QkZDMkUxREQFBlVTTkMwMQ"),
                new MpnsRegistrationDescription(@"http://dm2.notify.live.net/throttledthirdparty/01.00/12G9Ed13dLb5RbCii5fWzpFpAgAAAAADAQAAAAQUZm52OkJCMjg1QTg1QkZDMjUxREQFBlVTTkMwMQ"),
                new MpnsRegistrationDescription(@"http://dm2.notify.live.net/throttledthirdparty/01.00/12G9Ed13dLb5RbCii5fWzpFpAgAAAAADAQAAAAQUZm52OkJCMjg1QTg1QkZDMhUxREQFBlVTTkMwMQ"),
                new MpnsRegistrationDescription(@"http://dm2.notify.live.net/throttledthirdparty/01.00/12G9Ed13dLb5RbCii5fWzpFpAgAAAAADAQAAAAQUZm52OkJCMjg1QTg1QkZDMdUxREQFBlVTTkMwMQ"),
            };

            // Write to blob store to create an input file
            var blobClient = new CloudBlobClient(STORAGE_ENDPOINT, new Microsoft.WindowsAzure.Storage.Auth.StorageCredentials(STORAGE_ACCOUNT, STORAGE_PASSWORD));
            var container = blobClient.GetContainerReference("testjobs");
            container.CreateIfNotExists();

            SerializeToBlob(container, descriptions);

            // TODO then create Sas
            var outputContainerSasUri = GetOutputDirectoryUrl(container);
            var inputFileSasUri = GetInputFileUrl(container, INPUT_FILE_NAME);


            // Import this file
            NotificationHubClient client = NotificationHubClient.CreateClientFromConnectionString(CONNECTION_STRING, HUB_NAME);
            var createTask = client.SubmitNotificationHubJobAsync(
                new NotificationHubJob {
                    JobType = NotificationHubJobType.ImportCreateRegistrations,
                    OutputContainerUri = outputContainerSasUri,
                    ImportFileUri = inputFileSasUri
                }
            );
            createTask.Wait();

            var job = createTask.Result;
            long i = 10;
            while (i > 0 && job.Status != NotificationHubJobStatus.Completed)
            {
                var getJobTask = client.GetNotificationHubJobAsync(job.JobId);
                getJobTask.Wait();
                job = getJobTask.Result;
                Thread.Sleep(1000);
                i--;
            }
        }

        private static void SerializeToBlob(CloudBlobContainer container, RegistrationDescription[] descriptions)
        {
            StringBuilder builder = new StringBuilder();
            foreach (var registrationDescription in descriptions)
            {
                builder.AppendLine(registrationDescription.Serialize());
            }

            var inputBlob = container.GetBlockBlobReference(INPUT_FILE_NAME);
            using (MemoryStream stream = new MemoryStream(Encoding.UTF8.GetBytes(builder.ToString())))
            {
                inputBlob.UploadFromStream(stream);
            }
        }

        static Uri GetOutputDirectoryUrl(CloudBlobContainer container)
        {
            // Set the expiry time and permissions for the container.
            // In this case no start time is specified, so the shared access signature becomes valid immediately.
            SharedAccessBlobPolicy sasConstraints = new SharedAccessBlobPolicy
            {
                SharedAccessExpiryTime = DateTime.UtcNow.AddHours(4),
                Permissions = SharedAccessBlobPermissions.Write | SharedAccessBlobPermissions.List | SharedAccessBlobPermissions.Read
            };

            // Generate the shared access signature on the container, setting the constraints directly on the signature.
            string sasContainerToken = container.GetSharedAccessSignature(sasConstraints);

            // Return the URI string for the container, including the SAS token.
            return new Uri(container.Uri + sasContainerToken);
        }

        static Uri GetInputFileUrl(CloudBlobContainer container, string filePath)
        {
            // Set the expiry time and permissions for the container.
            // In this case no start time is specified, so the shared access signature becomes valid immediately.
            SharedAccessBlobPolicy sasConstraints = new SharedAccessBlobPolicy
            {
                SharedAccessExpiryTime = DateTime.UtcNow.AddHours(4),
                Permissions = SharedAccessBlobPermissions.Read
            };

            // Generate the shared access signature on the container, setting the constraints directly on the signature.
            string sasToken = container.GetBlockBlobReference(filePath).GetSharedAccessSignature(sasConstraints);

            // Return the URI string for the container, including the SAS token.
            return new Uri(container.Uri + "/" + filePath + sasToken);
        }

        static string GetJobPath(string namespaceName, string notificationHubPath, string jobId)
        {
            return string.Format(CultureInfo.InvariantCulture, @"{0}//{1}/{2}/", namespaceName, notificationHubPath, jobId);
        }
    }
}
```

## <a name="export"></a>[エクスポート]

登録情報のエクスポートは、インポートと似ていますが、次の点が異なります。

- 出力 URL のみが必要です。
- ExportRegistrations タイプの NotificationHubJob を作成します。

### <a name="sample-code-snippet"></a>サンプル コード スニペット

Java で登録をエクスポートするためのサンプル コード スニペットを次に示します。

```java
// Submit an export job
NotificationHubJob job = new NotificationHubJob();
job.setJobType(NotificationHubJobType.ExportRegistrations);
job.setOutputContainerUri("container uri with SAS signature");
job = hub.submitNotificationHubJob(job);

// Wait until the job is done
while(true){
    Thread.sleep(1000);
    job = hub.getNotificationHubJob(job.getJobId());
    if(job.getJobStatus() == NotificationHubJobStatus.Completed)
        break;
}

```

## <a name="next-steps"></a>次のステップ

登録について詳しくは、次の記事を参照してください。

- [登録管理](notification-hubs-push-notification-registration-management.md)
- [登録のタグ](notification-hubs-tags-segment-push-message.md)
- [テンプレートの登録](notification-hubs-templates-cross-platform-push-messages.md)
