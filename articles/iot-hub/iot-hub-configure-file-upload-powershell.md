---
title: Azure PowerShell を使用してファイルのアップロードを構成する | Microsoft Docs
description: Azure PowerShell コマンドレットを使用して IoT Hub を構成し、接続されているデバイスからファイルのアップロードを有効にする方法。 対象の Azure ストレージ アカウントの構成に関する情報が含まれています。
author: robinsh
manager: philmea
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 08/08/2017
ms.author: robinsh
ms.openlocfilehash: e2b106e64aed08b0586575d4d77602329454a673
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "92536013"
---
# <a name="configure-iot-hub-file-uploads-using-powershell"></a>PowerShell を使用して IoT Hub ファイルのアップロードを構成する

[!INCLUDE [iot-hub-file-upload-selector](../../includes/iot-hub-file-upload-selector.md)]

[IoT Hub でファイルのアップロード機能](iot-hub-devguide-file-upload.md)を使用するには、最初に Azure ストレージ アカウントと IoT Hub を関連付ける必要があります。 既存のストレージ アカウントを使用するか、新しいストレージ アカウントを作成できます。

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

このチュートリアルを完了するには、以下が必要です。

* アクティブな Azure アカウントアカウントがない場合、Azure 試用版にサインアップして、最大 10 件の無料 Mobile Apps を入手できます。 アカウントがない場合は、 [無料アカウント](https://azure.microsoft.com/pricing/free-trial/) を数分で作成することができます。

* [Azure PowerShell コマンドレット](/powershell/azure/install-Az-ps)。

* Azure IoT Hub。 IoT Hub がない場合は、[New-AzIoTHub コマンドレット](/powershell/module/az.iothub/new-aziothub)を使用するか、ポータルを使用して、[IoT Hub を作成](iot-hub-create-through-portal.md)できます。

* Azure ストレージ アカウント。 Azure ストレージ アカウントがない場合は、[Azure Storage PowerShell コマンドレット](/powershell/module/az.storage/)を使用するか、ポータルを使用して[ストレージ アカウントを作成](../storage/common/storage-account-create.md)できます

## <a name="sign-in-and-set-your-azure-account"></a>サインインして Azure アカウントを設定する

Azure アカウントにサインインしてサブスクリプションを選択します。

1. PowerShell プロンプトで、**Connect-AzAccount** コマンドレットを実行します。

    ```powershell
    Connect-AzAccount
    ```

2. 複数の Azure サブスクリプションがある場合は、Azure にサインインすると、資格情報に関連付けられているすべての Azure サブスクリプションへのアクセスが許可されます。 次のコマンドで、使用できる Azure サブスクリプションの一覧を表示します。

    ```powershell
    Get-AzSubscription
    ```

    次のコマンドで、使用するサブスクリプションを選択して、IoT Hub を管理するためのコマンドを実行します。 前のコマンドの出力から、サブスクリプション名または ID のいずれかを使用できます。

    ```powershell
    Select-AzSubscription `
        -SubscriptionName "{your subscription name}"
    ```

## <a name="retrieve-your-storage-account-details"></a>ストレージ アカウントの詳細を取得する

次の手順は、ストレージ アカウントが、**クラシック** デプロイ モデルではなく **Resource Manager** デプロイ モデルを使用して作成されていることを前提としています。

デバイスからファイル アップロードを構成するには、Azure Storage アカウント用の接続文字列が必要です。 Azure Storage アカウントは、IoT Hub と同じサブスクリプション内にある必要があります。 また、ストレージ アカウントには BLOB コンテナーの名前も必要です。 ストレージ アカウント キーを取得するには、次のコマンドを使用します。

```powershell
Get-AzStorageAccountKey `
  -Name {your storage account name} `
  -ResourceGroupName {your storage account resource group}
```

**key1** ストレージ アカウント キーの値を書き留めておきます。 これは、以降の手順で必要です。

ファイル アップロード用の既存の BLOB コンテナーを使用するか、新しいものを作成します。

* ストレージ アカウントの既存の BLOB コンテナーの一覧を表示するには、次のコマンドを使用します。

    ```powershell
    $ctx = New-AzStorageContext `
        -StorageAccountName {your storage account name} `
        -StorageAccountKey {your storage account key}
    Get-AzStorageContainer -Context $ctx
    ```

* ストレージ アカウントに BLOB コンテナーを作成するには、次のコマンドを使用します。

    ```powershell
    $ctx = New-AzStorageContext `
        -StorageAccountName {your storage account name} `
        -StorageAccountKey {your storage account key}
    New-AzStorageContainer `
        -Name {your new container name} `
        -Permission Off `
        -Context $ctx
    ```

## <a name="configure-your-iot-hub"></a>IoT Hub を構成する

次は、ストレージ アカウントの詳細を使用して[ファイルを IoT Hub にアップロード](iot-hub-devguide-file-upload.md)するように、IoT Hub を構成します。

構成するには次の値が必要です。

* **ストレージ コンテナー**: IoT Hub に関連付ける、現在の Azure サブスクリプションの Azure ストレージ アカウントの BLOB コンテナー。 必要なストレージ アカウント情報は、前のセクションで取得しました。 IoT Hub により、ファイルをアップロードするときにデバイスで使用する、この BLOB コンテナーへの書き込みアクセス許可を含む SAS URI が自動的に生成します。

* **Receive notifications for uploaded files (アップロードされたファイルに関する通知を受け取る)** : ファイルのアップロードに関する通知を有効または無効にします。

* **SAS TTL**: IoT Hub によりデバイスに返される SAS URI の有効期間を設定します。 既定では 1 時間に設定されています。

* **File notification settings default TTL (ファイルの通知設定 既定の TTL)** : 有効期限が切れるまでのファイルのアップロード通知の有効期間です。 既定では 1 日に設定されています。

* **File notification maximum delivery count (ファイルの通知設定 最大配信回数)** : IoT Hub がファイルのアップロード通知の配信を試行する回数です。 既定では 10 に設定されています。

IoT Hub でファイルのアップロード設定を構成するには、次の PowerShell コマンドレットを使用します。

```powershell
Set-AzIotHub `
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

IoT Hub のファイル アップロード機能について詳しくは、[デバイスからのファイルのアップロード](iot-hub-devguide-file-upload.md)に関するページをご覧ください。

Azure IoT Hub の管理についてさらに学習するには、次のリンクを使用してください。

* [IoT デバイスの一括管理](iot-hub-bulk-identity-mgmt.md)
* [IoT Hub の監視](monitor-iot-hub.md)

IoT Hub の機能を詳しく調べるには、次のリンクを使用してください。

* [IoT Hub 開発者ガイド](iot-hub-devguide.md)
* [Azure IoT Edge でエッジ デバイスに AI をデプロイする](../iot-edge/quickstart-linux.md)
* [IoT ソリューションの徹底的なセキュリティ保護](../iot-fundamentals/iot-security-ground-up.md)