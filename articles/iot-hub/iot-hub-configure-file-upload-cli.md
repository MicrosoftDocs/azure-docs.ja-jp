---
title: "Azure CLI (az.py) を使用して IoT Hub へのファイルのアップロードを構成する | Microsoft Docs"
description: "クロスプラットフォームの Azure CLI 2.0 (az.py) を使用して、Azure IoT Hub へのファイルのアップロードを構成する方法です。"
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
ms.date: 08/08/2017
ms.author: dobett
ms.translationtype: HT
ms.sourcegitcommit: f9003c65d1818952c6a019f81080d595791f63bf
ms.openlocfilehash: e2cd7eae50006717dfc0da358436ae3553a81d00
ms.contentlocale: ja-jp
ms.lasthandoff: 08/09/2017

---

# <a name="configure-iot-hub-file-uploads-using-azure-cli"></a>Azure CLI を使用して IoT Hub ファイルのアップロードを構成する

[!INCLUDE [iot-hub-file-upload-selector](../../includes/iot-hub-file-upload-selector.md)]

[IoT Hub でファイルのアップロード機能][lnk-upload]を使用するには、最初に Azure ストレージ アカウントと IoT Hub を関連付ける必要があります。 既存のストレージ アカウントを使用するか、新しいストレージ アカウントを作成できます。

このチュートリアルを完了するには、以下が必要です。

* アクティブな Azure アカウント。 アカウントがない場合は、[無料アカウント][lnk-free-trial]を数分で作成することができます。
* [Azure CLI 2.0][lnk-CLI-install]。
* Azure IoT Hub。 IoT Hub がない場合は、`az iot hub create` [コマンド][lnk-cli-create-iothub]を使用して作成するか、ポータルを使用して [IoT Hub を作成][lnk-portal-hub] できます。
* Azure Storage のアカウント Azure Storage アカウントがない場合は、「[Azure CLI 2.0 - ストレージ アカウントの管理][lnk-manage-storage]」に従って作成するか、ポータルを使用して[ストレージ アカウントを作成][lnk-portal-storage]できます。

## <a name="sign-in-and-set-your-azure-account"></a>サインインして Azure アカウントを設定する

Azure アカウントにサインインしてサブスクリプションを選択する。

1. コマンド プロンプトで、[ログイン コマンド][lnk-login-command]を実行します。

    ```azurecli
    az login
    ```

    指示に従って、コードを使用して認証し、Web ブラウザーで Azure アカウントにサインインします。

1. 複数の Azure サブスクリプションがある場合は、Azure にサインインすると、資格情報に関連付けられているすべてのAzure アカウントへのアクセスが許可されます。 以下の、利用できる [Azure アカウントを一覧表示するコマンド][lnk-az-account-command] を使用します。

    ```azurecli
    az account list
    ```

    以下のコマンドを使用して、コマンドを実行して IoT ハブを作成するために使用するサブスクリプションを選択します。 前のコマンドの出力から、サブスクリプション名または ID のいずれかを使用できます。

    ```azurecli
    az account set --subscription {your subscription name or id}
    ```

## <a name="retrieve-your-storage-account-details"></a>ストレージ アカウントの詳細を取得する

次の手順は、ストレージ アカウントが、**クラシック** デプロイ モデルではなく **Resource Manager** デプロイ モデルを使用して作成されていることを前提としています。

デバイスからファイル アップロードを構成するには、Azure Storage アカウント用の接続文字列が必要です。 Azure Storage アカウントは、IoT Hub と同じサブスクリプション内にある必要があります。 また、ストレージ アカウントには BLOB コンテナーの名前も必要です。 ストレージ アカウント キーを取得するには、次のコマンドを使用します。

```azurecli
az storage account show-connection-string --name {your storage account name} --resource-group {your storage account resource group}
```

**接続文字列**値をメモします。 これは、以降の手順で必要です。

ファイル アップロード用の既存の BLOB コンテナーを使用するか、新しいものを作成します。

* ストレージ アカウントの既存の BLOB コンテナーの一覧を表示するには、次のコマンドを使用します。

    ```azurecli
    az storage container list --connection-string "{your storage account connection string}"
    ```

* ストレージ アカウントに BLOB コンテナーを作成するには、次のコマンドを使用します。

    ```azurecli
    az storage container create --name {container name} --connection-string "{your storage account connection string}"
    ```

## <a name="file-upload"></a>ファイルのアップロード

これでストレージ アカウントの詳細を使用して、IoT Hub を構成することで、[ファイルのアップロード機能][lnk-upload]を有効化できるようになりました。

構成するには次の値が必要です。

**ストレージ コンテナー**: IoT Hub に関連付ける、現在の Azure サブスクリプションの Azure ストレージ アカウントの BLOB コンテナー。 必要なストレージ アカウント情報は、前のセクションで取得しました。 IoT Hub により、ファイルをアップロードするときにデバイスで使用する、この BLOB コンテナーへの書き込みアクセス許可を含む SAS URI が自動的に生成します。

**Receive notifications for uploaded files (アップロードされたファイルに関する通知を受け取る)**: ファイルのアップロードに関する通知を有効または無効にします。

**SAS TTL**: IoT Hub によりデバイスに返される SAS URI の有効期間を設定します。 既定では 1 時間に設定されています。

**File notification settings default TTL (ファイルの通知設定 既定の TTL)**: 有効期限が切れるまでのファイルのアップロード通知の有効期間です。 既定では 1 日に設定されています。

**File notification maximum delivery count (ファイルの通知設定 最大配信回数)**: IoT Hub がファイルのアップロード通知の配信を試行する回数です。 既定では 10 に設定されています。

IoT Hub でファイルのアップロード設定を構成するには、次の Azure CLI コマンドを使用します。

Bash シェルでは、次のコマンドを使用します。

```azurecli
az iot hub update --name {your iot hub name} --set properties.storageEndpoints.'$default'.connectionString="{your storage account connection string}"
az iot hub update --name {your iot hub name} --set properties.storageEndpoints.'$default'.containerName="{your storage container name}"
az iot hub update --name {your iot hub name} --set properties.storageEndpoints.'$default'.sasTtlAsIso8601=PT1H0M0S

az iot hub update --name {your iot hub name} --set properties.enableFileUploadNotifications=true
az iot hub update --name {your iot hub name} --set properties.messagingEndpoints.fileNotifications.maxDeliveryCount=10
az iot hub update --name {your iot hub name} --set properties.messagingEndpoints.fileNotifications.ttlAsIso8601=PT1H0M0S
```

Windows コマンド プロンプトでは、次のコマンドを使用します。

```azurecli
az iot hub update --name {your iot hub name} --set "properties.storageEndpoints.$default.connectionString="{your storage account connection string}""
az iot hub update --name {your iot hub name} --set "properties.storageEndpoints.$default.containerName="{your storage container name}""
az iot hub update --name {your iot hub name} --set "properties.storageEndpoints.$default.sasTtlAsIso8601=PT1H0M0S"

az iot hub update --name {your iot hub name} --set properties.enableFileUploadNotifications=true
az iot hub update --name {your iot hub name} --set properties.messagingEndpoints.fileNotifications.maxDeliveryCount=10
az iot hub update --name {your iot hub name} --set properties.messagingEndpoints.fileNotifications.ttlAsIso8601=PT1H0M0S
```

次のコマンドを使用して、IoT Hub のファイルのアップロード構成を確認できます。

```azurecli
az iot hub show --name {your iot hub name}
```

## <a name="next-steps"></a>次のステップ

IoT Hub のファイル アップロード機能に関する詳細については、[デバイスからのファイルのアップロード][lnk-upload]に関するページをご覧ください。

Azure IoT Hub の管理についてさらに学習するには、次のリンクを使用してください。

* [IoT デバイスの一括管理][lnk-bulk]
* [IoT Hub メトリック][lnk-metrics]
* [操作の監視][lnk-monitor]

IoT Hub の機能を詳しく調べるには、次のリンクを使用してください。

* [IoT Hub 開発者ガイド][lnk-devguide]
* [IoT Edge を使用したデバイスのシミュレーション][lnk-iotedge]
* [IoT ソリューションの徹底的なセキュリティ保護][lnk-securing]

[13]: ./media/iot-hub-configure-file-upload/file-upload-settings.png
[14]: ./media/iot-hub-configure-file-upload/file-upload-container-selection.png
[15]: ./media/iot-hub-configure-file-upload/file-upload-selected-container.png

[lnk-upload]: iot-hub-devguide-file-upload.md

[lnk-bulk]: iot-hub-bulk-identity-mgmt.md
[lnk-metrics]: iot-hub-metrics.md
[lnk-monitor]: iot-hub-operations-monitoring.md

[lnk-devguide]: iot-hub-devguide.md
[lnk-iotedge]: iot-hub-linux-iot-edge-simulated-device.md
[lnk-securing]: iot-hub-security-ground-up.md


[lnk-free-trial]: https://azure.microsoft.com/pricing/free-trial/
[lnk-CLI-install]: https://docs.microsoft.com/cli/azure/install-az-cli2
[lnk-login-command]: https://docs.microsoft.com/cli/azure/get-started-with-az-cli2
[lnk-az-account-command]: https://docs.microsoft.com/cli/azure/account
[lnk-az-register-command]: https://docs.microsoft.com/cli/azure/provider
[lnk-az-addcomponent-command]: https://docs.microsoft.com/cli/azure/component
[lnk-az-resource-command]: https://docs.microsoft.com/cli/azure/resource
[lnk-az-iot-command]: https://docs.microsoft.com/cli/azure/iot
[lnk-iot-pricing]: https://azure.microsoft.com/pricing/details/iot-hub/
[lnk-manage-storage]:../storage/common/storage-azure-cli.md#manage-storage-accounts
[lnk-portal-storage]:../storage/common/storage-create-storage-account.md
[lnk-cli-create-iothub]: https://docs.microsoft.com/cli/azure/iot/hub#az_iot_hub_create
