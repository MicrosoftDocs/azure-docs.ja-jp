---
title: クイック スタート - CLI を使用して Azure API Management インスタンスを作成する
description: Azure CLI を使用して新しい Azure API Management サービス インスタンスを作成します。
author: dlepow
ms.service: api-management
ms.topic: quickstart
ms.custom: ''
ms.date: 09/10/2020
ms.author: apimpm
ms.openlocfilehash: 72d1faac02a21f23d46eb992af1d501bca89e71f
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "101688078"
---
# <a name="quickstart-create-a-new-azure-api-management-service-instance-by-using-the-azure-cli"></a>クイック スタート: Azure CLI を使用して新しい Azure API Management サービス インスタンスを作成する

API Management (APIM) が組織にもたらす利点は、外部、パートナー、社内の開発者に API を公開することによって、社内のデータやサービスの可能性を広げられることです。 API Management は、開発者の取り組み、ビジネス インサイト、分析、セキュリティ、保護を通じて API プログラムの価値を高め、企業にコア コンピテンシーをもたらします。 APIM を使用すると、任意の場所でホストされている既存のバックエンド サービスの最新の API ゲートウェイを作成し、管理できます。 詳細については、[概要](api-management-key-concepts.md)に関するページを参照してください。

このクイックスタートでは、Azure CLI の [az apim](/cli/azure/apim) コマンドを使用して新しい API Management インスタンスを作成する手順を説明します。

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [azure-cli-prepare-your-environment.md](../../includes/azure-cli-prepare-your-environment.md)]

- この記事では、Azure CLI のバージョン 2.11.1 以降が必要です。 Azure Cloud Shell を使用している場合は、最新バージョンが既にインストールされています。

## <a name="create-a-resource-group"></a>リソース グループを作成する

Azure API Management インスタンスは、すべての Azure リソースと同様に、リソース グループにデプロイする必要があります。 リソース グループを使用すると、関連する Azure リソースを整理して管理できます。

まず、次の [az group create](/cli/azure/group#az-group-create) コマンドを使用して、*myResourceGroup* という名前のリソース グループを米国中部リージョンに作成します。

```azurecli-interactive
az group create --name myResourceGroup --location centralus
```

## <a name="create-a-new-service"></a>新しいサービスの作成

リソース グループを作成したので、API Management サービス インスタンスを作成できます。 [az apim create](/cli/azure/apim#az-apim-create) コマンドを使用して作成し、サービス名と公開元の詳細を指定します。 サービス名は Azure 内で一意である必要があります。 

次の例では、*myapim* をサービス名として使用します。 名前を一意の値に更新します。 また、API 公開元の組織の名前と、通知を受信するための電子メール アドレスを更新します。 

```azurecli-interactive
az apim create --name myapim --resource-group myResourceGroup \
  --publisher-name Contoso --publisher-email admin@contoso.com \
  --no-wait
```

既定では、このコマンドは、Azure API Management を評価するための経済的なオプションである Developer レベルにインスタンスを作成します。 このレベルは運用目的では使用できません。 API Management レベルのスケーリングの詳細については、[アップグレードとスケーリング](upgrade-and-scale.md)に関するをページをご覧ください。 

> [!TIP]
> このレベルに API Management サービスを作成してアクティブにするには、30 から 40 分かかる場合があります。 前のコマンドでは `--no-wait` オプションを使用して、サービスの作成中にコマンドが直ちに制御を返すようにしています。

[az apim show](/cli/azure/apim#az-apim-show) コマンドを実行して、デプロイの状態を確認します。

```azurecli-interactive
az apim show --name myapim --resource-group myResourceGroup --output table
```

最初は、出力は次のようになり、`Activating` の状態が表示されます。

```console
NAME         RESOURCE GROUP    LOCATION    GATEWAY ADDR    PUBLIC IP    PRIVATE IP    STATUS      TIER       UNITS
-----------  ----------------  ----------  --------------  -----------  ------------  ----------  ---------  -------
myapim       myResourceGroup   Central US                                             Activating  Developer  1
```

アクティブ化が完了すると、状態は `Online` になり、サービス インスタンスにゲートウェイ アドレスとパブリック IP アドレスが含まれます。 現時点では、これらのアドレスはコンテンツを公開しません。 次に例を示します。

```console
NAME         RESOURCE GROUP    LOCATION    GATEWAY ADDR                       PUBLIC IP     PRIVATE IP    STATUS    TIER       UNITS
-----------  ----------------  ----------  ---------------------------------  ------------  ------------  --------  ---------  -------
myapim       myResourceGroup   Central US  https://myapim.azure-api.net       203.0.113.1                 Online    Developer  1
```

API Management サービス インスタンスがオンラインになったら、使用する準備ができています。 [最初の API をインポートして発行する](import-and-publish.md)チュートリアルを開始します。

## <a name="clean-up-resources"></a>リソースをクリーンアップする

必要がなくなったら、[az group delete](/cli/azure/group#az-group-delete) コマンドを使用して、リソース グループおよび API Management サービス インスタンスを削除できます。

```azurecli-interactive
az group delete --name myResourceGroup
```

## <a name="next-steps"></a>次のステップ

> [!div class="nextstepaction"]
> [最初の API をインポートして発行する](import-and-publish.md)
