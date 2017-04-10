---
title: "Azure CLI (azure.js) を使用した IoT ハブの作成 | Microsoft Docs"
description: "クロスプラットフォームの Azure CLI (azure.js) を使用して Azure IoT ハブを作成する方法。"
services: iot-hub
documentationcenter: .net
author: BeatriceOltean
manager: timlt
editor: 
ms.assetid: 46a17831-650c-41d9-b228-445c5bb423d3
ms.service: iot-hub
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/24/2017
ms.author: boltean
translationtype: Human Translation
ms.sourcegitcommit: 5e6ffbb8f1373f7170f87ad0e345a63cc20f08dd
ms.openlocfilehash: 8ac82da36b2edb71fcd0599dac12a3ed18e33b6f
ms.lasthandoff: 03/24/2017


---
# <a name="create-an-iot-hub-using-the-azure-cli"></a>Azure CLI を使用して IoT ハブを作成する

[!INCLUDE [iot-hub-resource-manager-selector](../../includes/iot-hub-resource-manager-selector.md)]

## <a name="introduction"></a>はじめに

Azure CLI (azure.js) を使用すると、Azure IoT ハブをプログラムによって作成および管理できます。 この記事では、Azure CLI (azure.js) を使用して IoT ハブを作成する方法を説明します。

次のいずれかの CLI バージョンを使用してタスクを完了できます。

* Azure CLI (azure.js) - クラシック デプロイメント モデルと Resource Manager デプロイメント モデル用の CLI (この記事で説明)。
* [Azure CLI 2.0 (az.py)](iot-hub-create-using-cli.md) - Resource Manager デプロイメント モデル用の次世代 CLI。

このチュートリアルを完了するには、以下が必要です。

* アクティブな Azure アカウント。 アカウントがない場合は、[無料アカウント][lnk-free-trial]を数分で作成することができます。
* [Azure CLI 0.10.4][lnk-CLI-install] 以降。 既に Azure CLI がインストールされている場合は、コマンド プロンプトで次のコマンドを使用して、現在のバージョンを確認できます。

```azurecli
azure --version
```

> [!NOTE]
> Azure には、リソースの作成と操作に関して、[Azure Resource Manager とクラシックの](../azure-resource-manager/resource-manager-deployment-model.md) 2 種類のデプロイメント モデルがあります。 Azure CLI は、Azure Resource Manager モードである必要があります。
>
> ```azurecli
> azure config mode arm
> ```

## <a name="set-your-azure-account-and-subscription"></a>Azure アカウントとサブスクリプションを設定する

1. コマンド プロンプトで、次のコマンドを入力してログインします。

   ```azurecli
    azure login
   ```

   認証には、推奨される Web ブラウザーとコードを使用します。
1. 複数の Azure サブスクリプションがある場合は、Azure に接続すると、資格情報に関連付けられているすべての Azure サブスクリプションへのアクセスが許可されます。 次のコマンドを使用すると、すべての Azure サブスクリプションを表示でき、既定のサブスクリプションも識別できます。

   ```azurecli
    azure account list
   ```

   コマンドの残りの部分を実行するサブスクリプションのコンテキストを設定するには、次を使用します。

   ```azurecli
    azure account set <subscription name>
   ```

1. リソース グループがない場合は、**exampleResourceGroup** という名前のリソース グループを作成できます。

   ```azurecli
    azure group create -n exampleResourceGroup -l westus
   ```

> [!TIP]
> 「[Azure CLI を使用して Azure のリソースとリソース グループを管理する][lnk-CLI-arm]」の記事に、Azure CLI を使用して Azure リソースを管理する方法の詳細が記載されています。

## <a name="create-an-iot-hub"></a>IoT Hub の作成

必要なパラメーターは以下のとおりです。

```azurecli
azure iothub create -g <resource-group> -n <name> -l <location> -s <sku-name> -u <units>
```

* **resource-group**。 リソース グループ名。 形式は、英数字 (大文字と小文字を区別しない)、アンダー スコア、ハイフン、長さが 1 ~ 64 です。
* **name**。 作成する IoT Hub の名前。 形式は、英数字 (大文字と小文字を区別しない)、アンダー スコア、ハイフン、長さが 3 ~ 50 です。
* **location**。 IoT Hub をプロビジョニングする場所 (Azure リージョン/データ センター)。
* **sku-name**。 SKU の名前。次のいずれかから選択: [F1, S1, S2, S3]。 最新の完全な一覧については、IoT Hub の料金に関するページを参照してください。
* **units**。 プロビジョニングしたユニットの数。 範囲は: F1 [1-1] : S1, S2 [1-200] : S3 [1-10]。 IoT Hub 単位は、メッセージの合計数と接続するデバイスの数に基づきます。

作成に使用できるすべてのパラメーターを表示するには、コマンド プロンプトで help コマンドを使用します。

```azurecli
azure iothub create -h
```

簡単な例: リソース グループ **exampleResourceGroup** に **exampleIoTHubName** という名前の IoT Hub を作成するには、次のコマンドを実行します。

```azurecli
azure iothub create -g exampleResourceGroup -n exampleIoTHubName -l westus -k s1 -u 1
```

> [!NOTE]
> この Azure CLI コマンドでは、課金の対象とする S1 Standard IoT Hub を作成します。 次のコマンドを使用すると、IoT Hub **exampleIoTHubName** を削除できます。
>
> ```azurecli
> azure iothub delete -g exampleResourceGroup -n exampleIoTHubName
> ```

## <a name="next-steps"></a>次のステップ

IoT Hub の開発に関する詳細は、以下の記事をご覧ください。

* [IoT SDK][lnk-sdks]

IoT Hub の機能を詳しく調べるには、次のリンクを使用してください。

* [Azure Portal を使用した IoT Hub の管理][lnk-portal]

<!-- Links -->
[lnk-free-trial]: https://azure.microsoft.com/pricing/free-trial/
[lnk-azure-portal]: https://portal.azure.com/
[lnk-status]: https://azure.microsoft.com/status/
[lnk-CLI-install]:../cli-install-nodejs.md
[lnk-rest-api]: https://msdn.microsoft.com/library/mt589014.aspx
[lnk-CLI-arm]: ../azure-resource-manager/xplat-cli-azure-resource-manager.md

[lnk-sdks]: iot-hub-devguide-sdks.md
[lnk-portal]: iot-hub-create-through-portal.md 

