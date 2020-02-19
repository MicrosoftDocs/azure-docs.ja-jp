---
title: Azure IoT Hub デバイス ID のインポートとエクスポート | Microsoft Docs
description: Azure IoT service SDK を使用して ID レジストリに対して一括操作を実行し、デバイス ID をインポートおよびエクスポートする方法。 インポート操作を実行すると、デバイス ID を一括で作成、更新、および削除できます。
author: robinsh
manager: philmea
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 10/02/2019
ms.author: robinsh
ms.openlocfilehash: 0d0643adc56a3dcdeef163708c26f2425ab8af43
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/25/2019
ms.locfileid: "75429253"
---
# <a name="import-and-export-iot-hub-device-identities-in-bulk"></a>IoT Hub デバイス ID の一括でのインポートおよびエクスポート

各 IoT ハブには、デバイスごとのリソースをサービス内に作成するために使用できる ID レジストリがあります。 この ID レジストリを使って、デバイス向けエンドポイントへのアクセスを制御することもできます。 この記事では、ID レジストリとの間でデバイス ID を一括でインポートおよびエクスポートする方法について説明します。 実際に動作する C# のサンプルを見て、ハブを別のリージョンで複製するときにこの機能を使用する方法を学習するには、[IoT Hub を複製する方法](iot-hub-how-to-clone.md)に関するページを参照してください。

[!INCLUDE [iot-hub-basic](../../includes/iot-hub-basic-whole.md)]

インポートおよびエクスポート操作は、IoT Hub に対する一括サービス操作を実行するのを可能にする "*ジョブ*" のコンテキストで行われます。

**RegistryManager** クラスには、**ジョブ** フレームワークを使用する **ExportDevicesAsync** および **ImportDevicesAsync** メソッドが含まれています。 これらのメソッドを使用すると、IoT Hub ID レジストリ全体のエクスポート、インポート、および同期化を行うことができます。

このトピックでは、**RegistryManager** クラスと**ジョブ** システムを使用して、デバイスの一括インポートおよびエクスポートを IoT Hub の ID レジストリとの間で実行する方法について説明します。 また、Azure IoT Hub Device Provisioning サービスを使用して、1 つまたは複数の IoT ハブに対してノータッチの Just-In-Time プロビジョニングを実現できるため、人の手を介する必要がなくなります。 詳しくは、[Provisioning Service のドキュメント](/azure/iot-dps)をご覧ください。


## <a name="what-are-jobs"></a>ジョブとは

ID レジストリの操作では、次の場合に **ジョブ** システムを使用します。

* 操作の実行時間が、標準のランタイム操作と比べて長くなる可能性がある。

* 操作で大量のデータがユーザーに返される。

結果が得られるまで単一の API 呼び出しを待機させたりブロックしたりするのでなく、該当する IoT Hub 用に**ジョブ**を非同期に作成し、 その後すぐに **JobProperties** オブジェクトを返します。

次の C# コード スニペットでは、エクスポート ジョブの作成方法を示します。

```csharp
// Call an export job on the IoT Hub to retrieve all devices
JobProperties exportJob = await 
  registryManager.ExportDevicesAsync(containerSasUri, false);
```

> [!NOTE]
> C# コードで **RegistryManager** クラスを使用するには、プロジェクトに **Microsoft.Azure.Devices** NuGet パッケージを追加します。 **RegistryManager** クラスは、**Microsoft.Azure.Devices** 名前空間にあります。

**RegistryManager** クラスを使用すると、返された **JobProperties** メタデータを基に**ジョブ**の状態を照会することができます。 **RegistryManager** クラスのインスタンスを作成するには、**CreateFromConnectionString** メソッドを使用します。

```csharp
RegistryManager registryManager =
  RegistryManager.CreateFromConnectionString("{your IoT Hub connection string}");
```

IoT ハブ の接続文字列を取得するには、Azure Portal で次の操作を行います。

- IoT Hub に移動します。

- **[共有アクセス ポリシー]** を選択します。

- 必要なアクセス許可を考慮して、ポリシーを選択します。

- 画面の右側にあるパネルから接続文字列をコピーします。

次の C# コード スニペットでは、5 秒ごとにポーリングして、ジョブの実行が完了したかどうかを確認する方法を示します。

```csharp
// Wait until job is finished
while(true)
{
  exportJob = await registryManager.GetJobAsync(exportJob.JobId);
  if (exportJob.Status == JobStatus.Completed || 
      exportJob.Status == JobStatus.Failed ||
      exportJob.Status == JobStatus.Cancelled)
  {
    // Job has finished executing
    break;
  }

  await Task.Delay(TimeSpan.FromSeconds(5));
}
```

## <a name="device-importexport-job-limits"></a>デバイスのインポート ジョブまたはエクスポート ジョブの制限

すべての IoT Hub のレベルにわたり、一度に 1 つのアクティブなデバイスのインポート ジョブまたはエクスポート ジョブしか実行できません。 また、IoT Hub では、ジョブ操作の速度に対する制限もあります。 詳細については、「[参照 - IoT Hub のクォータと調整](iot-hub-devguide-quotas-throttling.md)」をご覧ください。

## <a name="export-devices"></a>デバイスのエクスポート

**ExportDevicesAsync** メソッドでは、Shared Access Signature (SAS) を使用して IoT ハブの ID レジストリ全体を Azure Storage BLOB コンテナーにエクスポートすることができます。 Shared Access Signature の詳細については、「[Shared Access Signatures (SAS) を使用して Azure Storage リソースへの制限付きアクセスを許可する](../storage/common/storage-sas-overview.md)」を参照してください。

このメソッドでは、制御対象の BLOB コンテナーにデバイス情報のバックアップを確実に作成することができます。

**ExportDevicesAsync** メソッドには、次の 2 つのパラメーターが必要です。

* BLOB コンテナーの URI が格納される*文字列*。 この URI には、コンテナーに対する書き込みアクセスを付与する SAS トークンを含める必要があります。 ジョブでは、デバイスのシリアル化されたエクスポート データを格納するために、このコンテナー内にブロック BLOB を作成します。 SAS トークンには、次のアクセス許可を含める必要があります。

   ```csharp
   SharedAccessBlobPermissions.Write | SharedAccessBlobPermissions.Read 
     | SharedAccessBlobPermissions.Delete
   ```

* エクスポート データから認証キーを除外するかどうかを示す*ブール値*。 **false** の場合、認証キーはエクスポート出力に含められます。 それ以外の場合、キーは **null** としてエクスポートされます。

次の C# コード スニペットは、エクスポート データにデバイスの認証キーを含むエクスポート ジョブを開始し、ポーリングを実行して完了する方法を示します。

```csharp
// Call an export job on the IoT Hub to retrieve all devices
JobProperties exportJob = 
  await registryManager.ExportDevicesAsync(containerSasUri, false);

// Wait until job is finished
while(true)
{
    exportJob = await registryManager.GetJobAsync(exportJob.JobId);
    if (exportJob.Status == JobStatus.Completed || 
        exportJob.Status == JobStatus.Failed ||
        exportJob.Status == JobStatus.Cancelled)
    {
    // Job has finished executing
    break;
    }

    await Task.Delay(TimeSpan.FromSeconds(5));
}
```

ジョブは、その出力を、指定された BLOB コンテナー内に **devices.txt**という名前のブロック BLOB として格納します。 出力データは、JSON のシリアル化されたデバイス データで構成され、1 行につき 1 つのデバイスが配置されます。

次の例は、出力データを示します。

```json
{"id":"Device1","eTag":"MA==","status":"enabled","authentication":{"symmetricKey":{"primaryKey":"abc=","secondaryKey":"def="}}}
{"id":"Device2","eTag":"MA==","status":"enabled","authentication":{"symmetricKey":{"primaryKey":"abc=","secondaryKey":"def="}}}
{"id":"Device3","eTag":"MA==","status":"disabled","authentication":{"symmetricKey":{"primaryKey":"abc=","secondaryKey":"def="}}}
{"id":"Device4","eTag":"MA==","status":"disabled","authentication":{"symmetricKey":{"primaryKey":"abc=","secondaryKey":"def="}}}
{"id":"Device5","eTag":"MA==","status":"enabled","authentication":{"symmetricKey":{"primaryKey":"abc=","secondaryKey":"def="}}}
```

デバイスにツイン データがある場合は、デバイスのデータと共にツイン データもエクスポートされます。 次の例は、この形式を示しています。 "twinETag" 行から最後の行までがツイン データです。

```json
{
   "id":"export-6d84f075-0",
   "eTag":"MQ==",
   "status":"enabled",
   "statusReason":"firstUpdate",
   "authentication":null,
   "twinETag":"AAAAAAAAAAI=",
   "tags":{
      "Location":"LivingRoom"
   },
   "properties":{
      "desired":{
         "Thermostat":{
            "Temperature":75.1,
            "Unit":"F"
         },
         "$metadata":{
            "$lastUpdated":"2017-03-09T18:30:52.3167248Z",
            "$lastUpdatedVersion":2,
            "Thermostat":{
               "$lastUpdated":"2017-03-09T18:30:52.3167248Z",
               "$lastUpdatedVersion":2,
               "Temperature":{
                  "$lastUpdated":"2017-03-09T18:30:52.3167248Z",
                  "$lastUpdatedVersion":2
               },
               "Unit":{
                  "$lastUpdated":"2017-03-09T18:30:52.3167248Z",
                  "$lastUpdatedVersion":2
               }
            }
         },
         "$version":2
      },
      "reported":{
         "$metadata":{
            "$lastUpdated":"2017-03-09T18:30:51.1309437Z"
         },
         "$version":1
      }
   }
}
```

コード内のこのデータにアクセスする必要がある場合、 **ExportImportDevice** クラスを使用すると、データを簡単に逆シリアル化できます。 次の C# コード スニペットでは、以前にブロック BLOB にエクスポートしたデバイス情報を読み取る方法を示します。

```csharp
var exportedDevices = new List<ExportImportDevice>();

using (var streamReader = new StreamReader(await blob.OpenReadAsync(AccessCondition.GenerateIfExistsCondition(), null, null), Encoding.UTF8))
{
  while (streamReader.Peek() != -1)
  {
    string line = await streamReader.ReadLineAsync();
    var device = JsonConvert.DeserializeObject<ExportImportDevice>(line);
    exportedDevices.Add(device);
  }
}
```

## <a name="import-devices"></a>デバイスのインポート

**RegistryManager** クラスの **ImportDevicesAsync** メソッドを使用すると、IoT Hub ID レジストリの一括インポートおよび同期化操作を実行することができます。 **ExportDevicesAsync** メソッドと同様に、**ImportDevicesAsync** メソッドでも**ジョブ** フレームワークを使用します。

**ImportDevicesAsync** メソッドを使用する場合は注意が必要です。このメソッドでは、ID レジストリ内に新しいデバイスをプロビジョニングするほか、既存のデバイスを更新および削除する可能性もあるためです。

> [!WARNING]
> インポート操作は元に戻すことができません。 ID レジストリに対して一括変更を加える場合は、必ず事前に **ExportDevicesAsync** メソッドを使用して既存のデータを別の BLOB コンテナーにバックアップしておく必要があります。

**ImportDevicesAsync** メソッドには、次の 2 つのパラメーターが必要です。

* [Azure Storage](../storage/index.yml) BLOB コンテナーの URI を、ジョブへの*入力*として使用するために格納する*文字列*。 この URI には、コンテナーに対する読み取りアクセスを付与する SAS トークンを含める必要があります。 このコンテナーには、ID レジストリにインポートするシリアル化されたデバイス データが入っている **devices.txt** という名前の BLOB を含める必要があります。 インポート データには、**ExportImportDevice** ジョブが **devices.txt** BLOB を作成する際に使用するのと同じ JSON 形式でデバイス情報を含める必要があります。 SAS トークンには、次のアクセス許可を含める必要があります。

   ```csharp
   SharedAccessBlobPermissions.Read
   ```

* [Azure Storage](https://azure.microsoft.com/documentation/services/storage/) BLOB コンテナーの URI を、ジョブからの*出力*として格納する*文字列*。 ジョブは、このコンテナー内にブロック BLOB を作成して、完了したインポート **ジョブ**からのエラー情報を格納します。 SAS トークンには、次のアクセス許可を含める必要があります。

   ```csharp
   SharedAccessBlobPermissions.Write | SharedAccessBlobPermissions.Read 
     | SharedAccessBlobPermissions.Delete
   ```

> [!NOTE]
> この 2 つのパラメーターは、同じ BLOB コンテナーを指すことができます。 出力コンテナーで追加のアクセス許可が必要な場合は、個々のパラメーターでデータのより細かな制御を簡単に実現できます。

次の C# コード スニペットでは、インポート ジョブの開始方法を示します。

```csharp
JobProperties importJob = 
   await registryManager.ImportDevicesAsync(containerSasUri, containerSasUri);
```

この方法を使用して、デバイス ツインのデータをインポートすることもできます。 データ入力の形式は、**ExportDevicesAsync** セクションに示されている形式と同じです。 この方法で、エクスポートされたデータを再インポートすることができます。 **$metadata** はオプションです。

## <a name="import-behavior"></a>インポートの動作

**ImportDevicesAsync** メソッドを使用して、ID レジストリで次の一括操作を実行することができます。

* 新しいデバイスの一括登録
* 既存のデバイスの一括削除
* 状態の一括変更 (デバイスの有効化または無効化)
* 新しいデバイス認証キーの一括割り当て
* デバイス認証キーの一括自動再生成
* ツイン データの一括更新

上記の操作の任意の組み合わせを 1 回の **ImportDevicesAsync** 呼び出しで実行できます。 たとえば、新しいデバイスの登録と、既存のデバイスの削除または更新とを同時に行うことができます。 **ExportDevicesAsync** メソッドと一緒に使用すると、すべてのデバイスを一つの IoT Hub から別の IoT Hub へ完全に移行できます。

インポート ファイルにツイン メタデータが含まれている場合は、このメタデータが既存のツイン メタデータを上書きします。 インポート ファイルにツイン メタデータが含まれていない場合は、`lastUpdateTime` メタデータのみが現在の時刻を使用して更新されます。

デバイスごとにインポート プロセスを制御するには、デバイスごとのインポート シリアル化データにオプションの **importMode** プロパティを使用します。 **importMode** プロパティには、次のオプションが用意されています。

| importMode | 説明 |
| --- | --- |
| **createOrUpdate** |指定した **ID** を持つデバイスが存在しない場合は、新たに登録されます。 <br/>該当するデバイスが既に存在する場合、既存の情報は、 **ETag** 値に関係なく、指定した入力データで上書きされます。 <br> 必要に応じて、デバイス データと共にツイン データを指定できます。 ツインの etag が指定された場合は、デバイスの etag とは別に処理されます。 既存のツインの etag と一致しない場合は、ログ ファイルにエラーが書き込まれます。 |
| **create** |指定した **ID** を持つデバイスが存在しない場合は、新たに登録されます。 <br/>該当するデバイスが既に存在する場合は、エラーがログ ファイルに書き込まれます。 <br> 必要に応じて、デバイス データと共にツイン データを指定できます。 ツインの etag が指定された場合は、デバイスの etag とは別に処理されます。 既存のツインの etag と一致しない場合は、ログ ファイルにエラーが書き込まれます。 |
| **update** |指定した **ID** を持つデバイスが既に存在する場合、**ETag** 値に関係なく、既存の情報は指定した入力データで上書きされます。 <br/>該当するデバイスが存在しない場合は、エラーがログ ファイルに書き込まれます。 |
| **updateIfMatchETag** |指定した **ID** を持つデバイスが既に存在する場合、**ETag** 値が一致した場合に限り、既存の情報は指定した入力データで上書きされます。 <br/>該当するデバイスが存在しない場合は、エラーがログ ファイルに書き込まれます。 <br/>**ETag** 値が不一致である場合は、ログ ファイルにエラーが書き込まれます。 |
| **createOrUpdateIfMatchETag** |指定した **ID** を持つデバイスが存在しない場合は、新たに登録されます。 <br/>該当するデバイスが既に存在する場合、 **ETag** 値が一致した場合に限り、既存の情報は指定した入力データで上書きされます。 <br/>**ETag** 値が不一致である場合は、ログ ファイルにエラーが書き込まれます。 <br> 必要に応じて、デバイス データと共にツイン データを指定できます。 ツインの etag が指定された場合は、デバイスの etag とは別に処理されます。 既存のツインの etag と一致しない場合は、ログ ファイルにエラーが書き込まれます。 |
| **delete** |指定した **ID** を持つデバイスが既に存在する場合、そのデバイスは **ETag** 値に関係なく削除されます。 <br/>該当するデバイスが存在しない場合は、エラーがログ ファイルに書き込まれます。 |
| **deleteIfMatchETag** |指定した **ID** を持つデバイスが既に存在する場合、そのデバイスは **ETag** 値が一致した場合に限り削除されます。 該当するデバイスが存在しない場合は、エラーがログ ファイルに書き込まれます。 <br/>ETag 値が不一致である場合は、ログ ファイルにエラーが書き込まれます。 |

> [!NOTE]
> シリアル化データが、デバイスの **importMode** フラグを明確に定義していない場合、インポート操作中に **createOrUpdate** が既定値に設定されます。

## <a name="import-devices-example--bulk-device-provisioning"></a>デバイスのインポートの例 – デバイスの一括プロビジョニング

次の C# コード サンプルでは、複数のデバイス ID を生成して以下の操作を行う方法を示します。

* 認証キーを含める。
* ブロック BLOB にデバイスの情報を書き込む。
* ID レジストリにデバイスをインポートする。

```csharp
// Provision 1,000 more devices
var serializedDevices = new List<string>();

for (var i = 0; i < 1000; i++)
{
  // Create a new ExportImportDevice
  // CryptoKeyGenerator is in the Microsoft.Azure.Devices.Common namespace
  var deviceToAdd = new ExportImportDevice()
  {
    Id = Guid.NewGuid().ToString(),
    Status = DeviceStatus.Enabled,
    Authentication = new AuthenticationMechanism()
    {
      SymmetricKey = new SymmetricKey()
      {
        PrimaryKey = CryptoKeyGenerator.GenerateKey(32),
        SecondaryKey = CryptoKeyGenerator.GenerateKey(32)
      }
    },
    ImportMode = ImportMode.Create
  };

  // Add device to the list
  serializedDevices.Add(JsonConvert.SerializeObject(deviceToAdd));
}

// Write the list to the blob
var sb = new StringBuilder();
serializedDevices.ForEach(serializedDevice => sb.AppendLine(serializedDevice));
await blob.DeleteIfExistsAsync();

using (CloudBlobStream stream = await blob.OpenWriteAsync())
{
  byte[] bytes = Encoding.UTF8.GetBytes(sb.ToString());
  for (var i = 0; i < bytes.Length; i += 500)
  {
    int length = Math.Min(bytes.Length - i, 500);
    await stream.WriteAsync(bytes, i, length);
  }
}

// Call import using the blob to add new devices
// Log information related to the job is written to the same container
// This normally takes 1 minute per 100 devices
JobProperties importJob =
   await registryManager.ImportDevicesAsync(containerSasUri, containerSasUri);

// Wait until job is finished
while(true)
{
  importJob = await registryManager.GetJobAsync(importJob.JobId);
  if (importJob.Status == JobStatus.Completed || 
      importJob.Status == JobStatus.Failed ||
      importJob.Status == JobStatus.Cancelled)
  {
    // Job has finished executing
    break;
  }

  await Task.Delay(TimeSpan.FromSeconds(5));
}
```

## <a name="import-devices-example--bulk-deletion"></a>デバイスのインポートの例 – 一括削除

次のコード サンプルでは、前述のコード サンプルを使用して追加したデバイスを削除する方法を示します。

```csharp
// Step 1: Update each device's ImportMode to be Delete
sb = new StringBuilder();
serializedDevices.ForEach(serializedDevice =>
{
  // Deserialize back to an ExportImportDevice
  var device = JsonConvert.DeserializeObject<ExportImportDevice>(serializedDevice);

  // Update property
  device.ImportMode = ImportMode.Delete;

  // Re-serialize
  sb.AppendLine(JsonConvert.SerializeObject(device));
});

// Step 2: Write the new import data back to the block blob
await blob.DeleteIfExistsAsync();
using (CloudBlobStream stream = await blob.OpenWriteAsync())
{
  byte[] bytes = Encoding.UTF8.GetBytes(sb.ToString());
  for (var i = 0; i < bytes.Length; i += 500)
  {
    int length = Math.Min(bytes.Length - i, 500);
    await stream.WriteAsync(bytes, i, length);
  }
}

// Step 3: Call import using the same blob to delete all devices
importJob = await registryManager.ImportDevicesAsync(containerSasUri, containerSasUri);

// Wait until job is finished
while(true)
{
  importJob = await registryManager.GetJobAsync(importJob.JobId);
  if (importJob.Status == JobStatus.Completed || 
      importJob.Status == JobStatus.Failed ||
      importJob.Status == JobStatus.Cancelled)
  {
    // Job has finished executing
    break;
  }

  await Task.Delay(TimeSpan.FromSeconds(5));
}
```

## <a name="get-the-container-sas-uri"></a>コンテナーの SAS URI の取得

次のコード サンプルでは、BLOB コンテナーに対する読み取り、書き込み、および削除アクセス許可を使用して [SAS URI](../storage/common/storage-dotnet-shared-access-signature-part-1.md) を生成する方法を示します。

```csharp
static string GetContainerSasUri(CloudBlobContainer container)
{
  // Set the expiry time and permissions for the container.
  // In this case no start time is specified, so the
  // shared access signature becomes valid immediately.
  var sasConstraints = new SharedAccessBlobPolicy();
  sasConstraints.SharedAccessExpiryTime = DateTime.UtcNow.AddHours(24);
  sasConstraints.Permissions = 
    SharedAccessBlobPermissions.Write | 
    SharedAccessBlobPermissions.Read | 
    SharedAccessBlobPermissions.Delete;

  // Generate the shared access signature on the container,
  // setting the constraints directly on the signature.
  string sasContainerToken = container.GetSharedAccessSignature(sasConstraints);

  // Return the URI string for the container,
  // including the SAS token.
  return container.Uri + sasContainerToken;
}
```

## <a name="next-steps"></a>次のステップ

この記事では、IoT Hub の ID レジストリに対して一括操作を実行する方法について説明しました。 ハブ間でデバイスを移動する方法など、こうした操作の多くは、[IoT Hub を複製する方法に関するページの「IoT Hub に登録されているデバイスを管理する」セクション](iot-hub-how-to-clone.md#managing-the-devices-registered-to-the-iot-hub)で使用されています。 

複製に関する記事には、実際に動作するサンプルが含まれています。このページの IoT C# サンプルにあります: [C# の Azure IoT サンプル](https://azure.microsoft.com/resources/samples/azure-iot-samples-csharp/)。プロジェクトは ImportExportDevicesSample です。 サンプルをダウンロードし、試してみることができます。[IoT Hub を複製する方法](iot-hub-how-to-clone.md)に関する記事に手順が記載されています。

Azure IoT Hub の管理の詳細については、次の記事をご覧ください。

* [IoT Hub メトリック](iot-hub-metrics.md)
* [IoT Hub ログ](iot-hub-monitor-resource-health.md)

IoT Hub の機能を詳しく調べるには、次のリンクを使用してください。

* [IoT Hub 開発者ガイド](iot-hub-devguide.md)
* [Azure IoT Edge でエッジ デバイスに AI をデプロイする](../iot-edge/tutorial-simulate-device-linux.md)

IoT Hub Device Provisioning サービスを使用してノータッチの Just-In-Time プロビジョニングを実現する方法については、次を参照してください。 

* [Azure IoT Hub Device Provisioning Service](/azure/iot-dps)
