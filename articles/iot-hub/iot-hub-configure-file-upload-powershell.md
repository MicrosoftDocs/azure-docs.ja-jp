---
title: "Azure PowerShell を使用してファイルのアップロードを構成する | Microsoft Docs"
description: "Azure PowerShell コマンドレットを使用して IoT Hub を構成し、接続されているデバイスからファイルのアップロードを有効にする方法。 対象の Azure ストレージ アカウントの構成に関する情報が含まれています。"
services: iot-hub
documentationcenter: 
author: dominicbetts
manager: timlt
editor: 
ms.assetid: 915f1597-272d-4fd4-8c5b-a0ccb1df0d91
ms.service: iot-hub
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/23/2017
ms.author: dobett
translationtype: Human Translation
ms.sourcegitcommit: 5e6ffbb8f1373f7170f87ad0e345a63cc20f08dd
ms.openlocfilehash: 5ae6057dbcbffa5d8496819d015ec060d7ca6cc9
ms.lasthandoff: 03/24/2017


---
# <a name="configure-iot-hub-file-uploads-using-powershell"></a>PowerShell を使用して IoT Hub ファイルのアップロードを構成する

[!INCLUDE [iot-hub-file-upload-selector](../../includes/iot-hub-file-upload-selector.md)]

[IoT Hub でファイルのアップロード機能][lnk-upload]を使用するには、最初に Azure ストレージ アカウントと IoT Hub を関連付ける必要があります。 既存のストレージ アカウントを使用するか、新しいストレージ アカウントを作成できます。

このチュートリアルを完了するには、以下が必要です。

* アクティブな Azure アカウント。 アカウントがない場合は、[無料アカウント][lnk-free-trial]を数分で作成することができます。
* [Azure PowerShell コマンドレット][lnk-powershell-install]。
* Azure IoT Hub。 IoT Hub がない場合は、[New-AzureRmIoTHub コマンドレット][lnk-powershell-iothub]を使用するか、ポータルを使用して [IoT Hub を作成][lnk-portal-hub]できます。
* Azure ストレージ アカウント。 Azure ストレージ アカウントがない場合は、[Azure Storage PowerShell コマンドレット][lnk-powershell-storage]を使用するか、ポータルを使用して[ストレージ アカウントを作成][lnk-portal-storage]できます。

## <a name="sign-in-and-set-your-azure-account"></a>サインインして Azure アカウントを設定する

Azure アカウントにサインインしてサブスクリプションを選択します。

1. PowerShell プロンプトで、**Login-AzureRmAccount** コマンドレットを実行します。

    ```powershell
    Login-AzureRmAccount
    ```

1. 複数の Azure サブスクリプションがある場合は、Azure にサインインすると、資格情報に関連付けられているすべての Azure サブスクリプションへのアクセスが許可されます。 次のコマンドで、使用できる Azure サブスクリプションの一覧を表示します。

    ```powershell
    Get-AzureRMSubscription
    ```

    次のコマンドで、使用するサブスクリプションを選択して、IoT Hub を管理するためのコマンドを実行します。 前のコマンドの出力から、サブスクリプション名または ID のいずれかを使用できます。

    ```powershell
    Select-AzureRMSubscription `
        -SubscriptionName "{your subscription name}"
    ```

## <a name="retrieve-your-storage-account-details"></a>ストレージ アカウントの詳細を取得する

次の手順は、ストレージ アカウントが、**クラシック** デプロイ モデルではなく **Resource Manager** デプロイ モデルを使用して作成されていることを前提としています。

デバイスからファイルのアップロードを構成するには、IoT Hub と同じサブスクリプションに Azure ストレージ アカウントの接続文字列が必要です。 また、ストレージ アカウントには BLOB コンテナーの名前も必要です。 ストレージ アカウント キーを取得するには、次のコマンドを使用します。

```powershell
Get-AzureRmStorageAccountKey `
  -Name {your storage account name} `
  -ResourceGroupName {your storage account resource group}
```

**key1** ストレージ アカウント キーの値を書き留めておきます。 これは、以降の手順で必要です。

ファイル アップロード用の既存の BLOB コンテナーを使用するか、新しいものを作成します。

* ストレージ アカウントの既存の BLOB コンテナーの一覧を表示するには、次のコマンドを使用します。

    ```powershell
    $ctx = New-AzureStorageContext `
        -StorageAccountName {your storage account name} `
        -StorageAccountKey {your storage account key}
    Get-AzureStorageContainer -Context $ctx
    ```

* ストレージ アカウントに BLOB コンテナーを作成するには、次のコマンドを使用します。

    ```powershell
    $ctx = New-AzureStorageContext `
        -StorageAccountName {your storage account name} `
        -StorageAccountKey {your storage account key}
    New-AzureStorageContainer `
        -Name {your new container name} `
        -Permission Off `
        -Context $ctx
    ```

## <a name="configure-your-iot-hub"></a>IoT Hub を構成する

これでストレージ アカウントの詳細を使用して、IoT Hub を構成することで、[ファイルのアップロード機能][lnk-upload]を有効化できるようになりました。

構成するには次の値が必要です。

**ストレージ コンテナー**: IoT Hub に関連付ける、現在の Azure サブスクリプションの Azure ストレージ アカウントの BLOB コンテナー。 必要なストレージ アカウント情報は、前のセクションで取得しました。 IoT Hub により、ファイルをアップロードするときにデバイスで使用する、この BLOB コンテナーへの書き込みアクセス許可を含む SAS URI が自動的に生成します。

**Receive notifications for uploaded files (アップロードされたファイルに関する通知を受け取る)**: ファイルのアップロードに関する通知を有効または無効にします。

**SAS TTL**: IoT Hub によりデバイスに返される SAS URI の有効期間を設定します。 既定では 1 時間に設定されています。

**File notification settings default TTL (ファイルの通知設定 既定の TTL)**: 有効期限が切れるまでのファイルのアップロード通知の有効期間です。 既定では 1 日に設定されています。

**File notification maximum delivery count (ファイルの通知設定 最大配信回数)**: IoT Hub がファイルのアップロード通知の配信を試行する回数です。 既定では 10 に設定されています。

IoT Hub でファイルのアップロード設定を構成するには、次の PowerShell コマンドレットを使用します。

```powershell
Set-AzureRmIotHub `
    -ResourceGroupName "{your iot hub resource group}" `
    -Name "{your iot hub name}" `
    -FileUploadNotificationTtl "01:00:00" `
    -FileUploadSasUriTtl "01:00:00" `
    -EnableFileUploadNotifications $true `
    -FileUploadStorageConnectionString "DefaultEndpointsProtocol=https;AccountName={your storage account name};AccountKey={your storage account key};EndpointSuffix=core.windows.net" `
    -FileUploadContainerName "{your blob container name}" `
    -FileUploadNotificationMaxDeliveryCount 10
```

## <a name="next-steps"></a>次のステップ
IoT Hub のファイルのアップロード機能に関する詳細については、IoT Hub 開発者ガイドの [Upload files from a device (デバイスからのファイルのアップロード)][lnk-upload] に関する記事をご覧ください。

Azure IoT Hub の管理についてさらに学習するには、次のリンクを使用してください。

* [IoT デバイスの一括管理][lnk-bulk]
* [IoT Hub メトリック][lnk-metrics]
* [操作の監視][lnk-monitor]

IoT Hub の機能を詳しく調べるには、次のリンクを使用してください。

* [IoT Hub 開発者ガイド][lnk-devguide]
* [IoT Gateway SDK を使用したデバイスのシミュレーション][lnk-gateway]
* [IoT ソリューションの徹底的なセキュリティ保護][lnk-securing]

[lnk-upload]: iot-hub-devguide-file-upload.md

[lnk-bulk]: iot-hub-bulk-identity-mgmt.md
[lnk-metrics]: iot-hub-metrics.md
[lnk-monitor]: iot-hub-operations-monitoring.md

[lnk-devguide]: iot-hub-devguide.md
[lnk-gateway]: iot-hub-linux-gateway-sdk-simulated-device.md
[lnk-securing]: iot-hub-security-ground-up.md
[lnk-powershell-install]: https://docs.microsoft.com/powershell/azureps-cmdlets-docs/
[lnk-powershell-storage]: https://docs.microsoft.com/powershell/storage/
[lnk-powershell-iothub]: https://docs.microsoft.com/powershell/resourcemanager/azurerm.iothub/v1.1.0/new-azurermiothub
[lnk-portal-hub]: iot-hub-create-through-portal.md
[lnk-free-trial]: http://azure.microsoft.com/pricing/free-trial/
[lnk-portal-storage]: ../storage/storage-create-storage-account.md
