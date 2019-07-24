---
title: Azure CLI を使用して IoT Hub へのファイルのアップロードを構成する | Microsoft Docs
description: クロスプラットフォームの Azure CLI を使用して、Azure IoT Hub へのファイルのアップロードを構成する方法です。
author: robinsh
manager: philmea
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 08/08/2017
ms.author: robinsh
ms.openlocfilehash: fe6ce23b9e87235521739b7808712a9d541dabf9
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/18/2019
ms.locfileid: "59048964"
---
# <a name="configure-iot-hub-file-uploads-using-azure-cli"></a>Azure CLI を使用して IoT Hub ファイルのアップロードを構成する

[!INCLUDE [iot-hub-file-upload-selector](../../includes/iot-hub-file-upload-selector.md)]

[デバイスからファイルをアップロード](iot-hub-devguide-file-upload.md)するには、最初に Azure ストレージ アカウントと IoT Hub を関連付ける必要があります。 既存のストレージ アカウントを使用するか、新しいストレージ アカウントを作成できます。

このチュートリアルを完了するには、以下が必要です。

* アクティブな Azure アカウントアカウントがない場合、Azure 試用版にサインアップして、最大 10 件の無料 Mobile Apps を入手できます。 アカウントがない場合は、 [無料アカウント](https://azure.microsoft.com/pricing/free-trial/) を数分で作成することができます。

* [Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest)。

* Azure IoT Hub。 IoT Hub がない場合は、[`az iot hub create` コマンド](https://docs.microsoft.com/cli/azure/iot/hub#az-iot-hub-create)を使用して作成するか、[ポータルを使用して IoT Hub を作成](iot-hub-create-through-portal.md)できます。

* Azure Storage のアカウント Azure Storage アカウントがない場合は、[Azure CLI のストレージ アカウントの管理](../storage/common/storage-azure-cli.md#manage-storage-accounts)に関するページを参照して作成するか、ポータルを使用して[ストレージ アカウントを作成](../storage/common/storage-create-storage-account.md)できます。

## <a name="sign-in-and-set-your-azure-account"></a>サインインして Azure アカウントを設定する

Azure アカウントにサインインしてサブスクリプションを選択します。

1. コマンド プロンプトで、[ログイン コマンド](https://docs.microsoft.com/cli/azure/get-started-with-azure-cli?view=azure-cli-latest)を実行します。

    ```azurecli
    az login
    ```

    指示に従って、コードを使用して認証し、Web ブラウザーで Azure アカウントにサインインします。

2. 複数の Azure サブスクリプションがある場合は、Azure にサインインすると、資格情報に関連付けられているすべてのAzure アカウントへのアクセスが許可されます。 以下の、利用できる [Azure アカウントを一覧表示するコマンド](https://docs.microsoft.com/cli/azure/account)を使用します。

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
az storage account show-connection-string --name {your storage account name} \
  --resource-group {your storage account resource group}
```

**接続文字列**値をメモします。 これは、以降の手順で必要です。

ファイル アップロード用の既存の BLOB コンテナーを使用するか、新しいものを作成します。

* ストレージ アカウントの既存の BLOB コンテナーの一覧を表示するには、次のコマンドを使用します。

    ```azurecli
    az storage container list --connection-string "{your storage account connection string}"
    ```

* ストレージ アカウントに BLOB コンテナーを作成するには、次のコマンドを使用します。

    ```azurecli
    az storage container create --name {container name} \
      --connection-string "{your storage account connection string}"
    ```

## <a name="file-upload"></a>ファイルのアップロード

これでストレージ アカウントの詳細を使用して、IoT Hub を構成することで、[ファイルを IoT Hub にアップロード](iot-hub-devguide-file-upload.md)する機能を有効化できるようになりました。

構成するには次の値が必要です。

* **ストレージ コンテナー**:お使いの IoT ハブに関連付ける、現在の Azure サブスクリプション内の Azure ストレージ アカウントの BLOB コンテナー。 必要なストレージ アカウント情報は、前のセクションで取得しました。 IoT Hub により、ファイルをアップロードするときにデバイスで使用する、この BLOB コンテナーへの書き込みアクセス許可を含む SAS URI が自動的に生成します。

* **アップロードされたファイルに関する通知を受け取る**:ファイルのアップロード通知を有効にするか無効にします。

* **SAS TTL**:IoT Hub によりデバイスに返される SAS URI の有効期間を設定します。 既定では 1 時間に設定されています。

* **File notification settings default TTL (ファイル通知設定の既定の TTL)**:有効期限が切れるまでのファイルのアップロード通知の有効期間です。 既定では 1 日に設定されています。

* **File notification maximum delivery count (ファイル通知設定の最大配信回数)**:IoT Hub がファイルのアップロード通知の配信を試行する回数です。 既定では 10 に設定されています。

IoT Hub でファイルのアップロード設定を構成するには、次の Azure CLI コマンドを使用します。

<!--Robinsh this is out of date, add cloud powershell -->

Bash シェルでは、次のコマンドを使用します。

```azurecli
az iot hub update --name {your iot hub name} \
  --set properties.storageEndpoints.'$default'.connectionString="{your storage account connection string}"

az iot hub update --name {your iot hub name} \
  --set properties.storageEndpoints.'$default'.containerName="{your storage container name}"

az iot hub update --name {your iot hub name} \
  --set properties.storageEndpoints.'$default'.sasTtlAsIso8601=PT1H0M0S

az iot hub update --name {your iot hub name} \
  --set properties.enableFileUploadNotifications=true

az iot hub update --name {your iot hub name} \
  --set properties.messagingEndpoints.fileNotifications.maxDeliveryCount=10

az iot hub update --name {your iot hub name} \
  --set properties.messagingEndpoints.fileNotifications.ttlAsIso8601=PT1H0M0S
```

次のコマンドを使用して、IoT Hub のファイルのアップロード構成を確認できます。

```azurecli
az iot hub show --name {your iot hub name}
```

## <a name="next-steps"></a>次の手順

IoT Hub のファイル アップロード機能について詳しくは、[デバイスからのファイルのアップロード](iot-hub-devguide-file-upload.md)に関するページをご覧ください。

Azure IoT Hub の管理についてさらに学習するには、次のリンクを使用してください。

* [IoT デバイスの一括管理](iot-hub-bulk-identity-mgmt.md)
* [IoT Hub メトリック](iot-hub-metrics.md)
* [操作の監視](iot-hub-operations-monitoring.md)

IoT Hub の機能を詳しく調べるには、次のリンクを使用してください。

* [IoT Hub 開発者ガイド](iot-hub-devguide.md)
* [Azure IoT Edge でエッジ デバイスに AI をデプロイする](../iot-edge/tutorial-simulate-device-linux.md)
* [IoT ソリューションの徹底的なセキュリティ保護](../iot-fundamentals/iot-security-ground-up.md)
