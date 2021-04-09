---
title: クイック スタート - Azure CLI を使用して Azure 通知ハブを作成する | Microsoft Docs
description: このチュートリアルでは、Azure CLI を使用して Azure 通知ハブを作成する方法を学習します。
services: notification-hubs
author: dbradish-microsoft
manager: barbkess
editor: sethmanheim
ms.service: notification-hubs
ms.devlang: azurecli
ms.workload: mobile
ms.topic: quickstart
ms.date: 05/27/2020
ms.author: dbradish
ms.reviewer: thsomasu
ms.lastreviewed: 03/18/2020
ms.custom: devx-track-azurecli
ms.openlocfilehash: 6c565be3aea43fc4dc86fd793e0dc2dea0b08d4c
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "94563751"
---
# <a name="quickstart-create-an-azure-notification-hub-using-the-azure-cli"></a>Azure CLI を使用して Azure 通知ハブを作成する

Azure Notification Hubs は、任意のバックエンド (クラウドまたはオンプレミス) から任意のプラットフォーム (iOS、Android、Windows、Kindle、Baidu など) に通知を送信できる、使いやすく、かつスケールアウトされたプッシュ エンジンを提供します。 このサービスの詳細については、「[Azure Notification Hubs とは](notification-hubs-push-notification-overview.md)」を参照してください。

このクイック スタートでは、Azure CLI を使用して通知ハブを作成します。 最初のセクションでは、Notification Hubs 名前空間を作成する手順について説明します。 2 つ目のセクションでは、既存の名前空間に通知ハブを作成する手順について説明します。 カスタム アクセス ポリシーを作成する方法についても学習します。

Azure サブスクリプションをお持ちでない場合は、開始する前に [無料アカウント](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) を作成してください。

[!INCLUDE [azure-cli-prepare-your-environment.md](../../includes/azure-cli-prepare-your-environment.md)]

> [!IMPORTANT]
> Notification Hubs には、バージョン 2.0.67 以降の Azure CLI が必要です。 [az version](/cli/azure/reference-index#az_version) を実行し、インストールされているバージョンおよび依存ライブラリを検索します。 最新バージョンにアップグレードするには、[az upgrade](/cli/azure/reference-index#az_upgrade) を実行します。

## <a name="create-a-resource-group"></a>リソース グループを作成する

Azure Notification Hubs は、すべての Azure リソースと同様に、リソース グループにデプロイする必要があります。  リソース グループを使用すると、関連する Azure リソースを整理して管理できます。  リソース グループの詳細については、「[Azure Resource Manager とは](../azure-resource-manager/management/overview.md)」を参照してください。

このクイックスタートでは、次の [az group create](/cli/azure/group#az-group-create) コマンドを使用して、**spnhubrg** という名前のリソース グループを **eastus** の場所に作成します。

```azurecli
az group create --name spnhubrg --location eastus
```

## <a name="create-a-notification-hubs-namespace"></a>Notification Hubs の名前空間を作成する

1. 対象の通知ハブ用の名前空間を作成します。

   名前空間には 1 つまたは複数のハブが含まれます。名前はすべての Azure サブスクリプションで一意であり、長さが 6 文字以上である必要があります。 名前の可用性を確認するには、[az notification-hub namespace check-availability](/cli/azure/ext/notification-hub/notification-hub/namespace#ext-notification-hub-az-notification-hub-namespace-check-availability) コマンドを使用します。

   ```azurecli
   az notification-hub namespace check-availability --name spnhubns
   ```

   可用性の要求に対する応答として、Azure CLI により次のコンソール出力が表示されます。

   ```shell
   {
   "id": "/subscriptions/yourSubscriptionID/providers/Microsoft.NotificationHubs/checkNamespaceAvailability",
   "isAvailable": true,
   "location": null,
   "name": "spnhubns",
   "properties": false,
   "sku": null,
   "tags": null,
   "type": "Microsoft.NotificationHubs/namespaces/checkNamespaceAvailability"
   }
   ```

   Azure CLI 応答の 2 行目の `"isAvailable": true` に注目してください。 名前空間に指定した目的の名前が使用できない場合、この行は `false` になります。 名前が使用できることを確認したら、[az notification-hub namespace create](/cli/azure/ext/notification-hub/notification-hub/namespace#ext-notification-hub-az-notification-hub-namespace-create) コマンドを実行して名前空間を作成します。  

   ```azurecli
   az notification-hub namespace create --resource-group spnhubrg --name spnhubns  --location eastus --sku Free
   ```

   `az notification-hub namespace create` コマンドに指定した `--name` が使用できない場合、または [Azure リソースの命名規則と制限](../azure-resource-manager/management/resource-name-rules.md)を満たさない場合、Azure CLI の応答では次のコンソール出力が表示されます。

   ```shell
   #the name is not available
   The specified name is not available. For more information visit https://aka.ms/eventhubsarmexceptions.

   #the name is invalid
   The specified service namespace is invalid.
   ```

   試した最初の名前が成功しない場合は、新しい名前空間に別の名前を選択して、`az notification-hub namespace create` コマンドを再度実行します。

   > [!NOTE]
   > このステップ以降は、このクイックスタートからコピーした各 Azure CLI コマンドの `--namespace` パラメーターの値を置き換える必要があります。

2. 名前空間の一覧を取得します。

   新しい名前空間の詳細を表示するには、[az notification-hub namespace list](/cli/azure/ext/notification-hub/notification-hub/namespace#ext-notification-hub-az-notification-hub-namespace-list) コマンドを使用します。 サブスクリプションのすべての名前空間を表示する場合は、`--resource-group` パラメーターを省略できます。

   ```azurecli
   az notification-hub namespace list --resource-group spnhubrg
   ```

## <a name="create-notification-hubs"></a>通知ハブを作成する

1. 最初の通知ハブを作成します。

   新しい名前空間に 1 つまたは複数の通知ハブを作成できます。 [az notification-hub create](/cli/azure/ext/notification-hub/notification-hub#ext-notification-hub-az-notification-hub-create) コマンドを実行して、通知ハブを作成します。

   ```azurecli
   az notification-hub create --resource-group spnhubrg --namespace-name spnhubns --name spfcmtutorial1nhub --location eastus --sku Free
   ```

2. 2 つ目の通知ハブを作成します。

   単一の名前空間に複数の通知ハブを作成することができます。 同じ名前空間に 2 つ目の通知ハブを作成するには、別のハブ名を使用して `az notification-hub create` コマンドをもう一度実行します。

   ```azurecli
   az notification-hub create --resource-group spnhubrg --namespace-name spnhubns --name mysecondnhub --location eastus --sku Free
   ```

3. 通知ハブの一覧を取得します。

   Azure CLI で実行されたコマンドごとに成功またはエラー メッセージが返されますが、通知ハブの一覧を照会できると安心です。 [az notification-hub list](/cli/azure/ext/notification-hub/notification-hub#ext-notification-hub-az-notification-hub-list) コマンドは、この目的のために設計されています。

   ```azurecli
   az notification-hub list --resource-group spnhubrg --namespace-name spnhubns --output table
   ```

## <a name="work-with-access-policies"></a>アクセス ポリシーを操作する

1. Azure Notification Hubs は、アクセス ポリシーを使用して [Shared Access Signature セキュリティ](./notification-hubs-push-notification-security.md)を使用します。 通知ハブを作成すると、2 つのポリシーが自動的に作成されます。 プッシュ通知を構成するには、これらのポリシーの接続文字列が必要です。 [az notification-hub authorization-rule list](/cli/azure/ext/notification-hub/notification-hub/authorization-rule#ext-notification-hub-az-notification-hub-authorization-rule-list) を実行すると、ポリシー名とそれぞれのリソース グループの一覧が返されます。

   ```azurecli
   az notification-hub authorization-rule list --resource-group spnhubrg --namespace-name spnhubns --notification-hub-name spfcmtutorial1nhub --output table
   ```

   > [!IMPORTANT]
   > アプリケーションで _DefaultFullSharedAccessSignature_ ポリシーを使用しないでください。 このポリシーの使用が意図されているのはバックエンドだけです。 クライアント アプリケーションでは `Listen` アクセス ポリシーのみを使用してください。

2. 意味のある名前を持つ追加の承認規則を作成する場合は、[az notification-hub authorization-rule create](/cli/azure/ext/notification-hub/notification-hub/authorization-rule#ext-notification-hub-az-notification-hub-authorization-rule-create) コマンドを使用して、独自のアクセス ポリシーを作成およびカスタマイズできます。 `--rights` パラメーターは、割り当てたいアクセス許可のスペース区切りの一覧です。

   ```azurecli
   az notification-hub authorization-rule create --resource-group spnhubrg --namespace-name spnhubns --notification-hub-name spfcmtutorial1nhub --name spnhub1key --rights Listen Manage Send
   ```

3. アクセス ポリシーごとに、キーと接続文字列の 2 つのセットがあります。 これらは、後で[通知ハブを構成する](./configure-notification-hub-portal-pns-settings.md)ために必要になります。 Notification Hubs アクセス ポリシーのキーと接続文字列を一覧表示するには、[az notification-hub authorization-rule](/cli/azure/ext/notification-hub/notification-hub/authorization-rule#ext-notification-hub-az-notification-hub-authorization-rule-list-keys) コマンドを使用します。

   ```azurecli
   # query the keys and connection strings for DefaultListenSharedAccessSignature
   az notification-hub authorization-rule list-keys --resource-group spnhubrg --namespace-name spnhubns --notification-hub-name spfcmtutorial1nhub --name DefaultListenSharedAccessSignature --output table
   ```

   ```azurecli
   # query the keys and connection strings for a custom policy
   az notification-hub authorization-rule list-keys --resource-group spnhubrg --namespace-name spnhubns --notification-hub-name spfcmtutorial1nhub --name spnhub1key --output table
   ```

   > [!NOTE]
   > [Notification Hubs 名前空間](/cli/azure/ext/notification-hub/notification-hub/namespace/authorization-rule#ext-notification-hub-az-notification-hub-namespace-authorization-rule-list-keys)と[通知ハブ](/cli/azure/ext/notification-hub/notification-hub/authorization-rule#ext-notification-hub-az-notification-hub-authorization-rule-list-keys)には、別個のアクセス ポリシーがあります。 キーと接続文字列に対してクエリを実行する場合は、正しい Azure CLI 参照を使用していることを確認してください。

## <a name="clean-up-resources"></a>リソースをクリーンアップする

必要がなくなったら、[az group delete](/cli/azure/group) コマンドを使用して、リソース グループとすべての関連リソースを削除します。

```azurecli
az group delete --name spnhubrg
```

## <a name="next-steps"></a>次のステップ

* このクイック スタートでは、通知ハブを作成します。 プラットフォーム通知システム (PNS) 設定を使用してハブを構成する方法については、[通知ハブのプッシュ通知の設定](configure-notification-hub-portal-pns-settings.md)に関するページを参照してください。

* Azure CLI で通知ハブを管理するための広範な機能を検出します。

  [Notification Hubs の完全な参照リスト](/cli/azure/ext/notification-hub/notification-hub)

  [Notification Hubs 名前空間の参照リスト](/cli/azure/ext/notification-hub/notification-hub/namespace)

  [Notification Hubs 承認規則の参照リスト](/cli/azure/ext/notification-hub/notification-hub/authorization-rule)

  [Notification Hubs 資格情報の参照リスト](/cli/azure/ext/notification-hub/notification-hub/credential)