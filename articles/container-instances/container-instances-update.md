---
title: Azure Container Instances のコンテナーを更新する
description: Azure Container Instances のコンテナー グループに含まれる実行中のコンテナーを更新する方法について説明します。
services: container-instances
author: dlepow
manager: gwallace
ms.service: container-instances
ms.topic: article
ms.date: 09/03/2019
ms.author: danlep
ms.openlocfilehash: 3103fe7fbf7dcd587f43b673ef53f32893908ecb
ms.sourcegitcommit: f176e5bb926476ec8f9e2a2829bda48d510fbed7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/04/2019
ms.locfileid: "70307710"
---
# <a name="update-containers-in-azure-container-instances"></a>Azure Container Instances のコンテナーを更新する

コンテナー インスタンスの通常の操作中に、[コンテナー グループ](container-instances-container-groups.md)内の実行中のコンテナーを更新することが必要になる場合があります。 たとえば、イメージのバージョンの更新、DNS 名の変更、環境変数の更新、アプリケーションがクラッシュしたコンテナーの状態の更新を行う場合などです。

> [!NOTE]
> 終了した、または削除されたコンテナー グループを更新することはできません。 停止 (Succeeded または Failed のいずれかの状態) または削除されたコンテナー グループは、新規としてデプロイする必要があります。

## <a name="update-a-container-group"></a>コンテナー グループを更新する

コンテナー グループ内の実行中のコンテナーを更新するには、少なくとも 1 つのプロパティが変更された既存のグループを再デプロイします。 コンテナー グループを更新すると、そのグループに含まれる実行中のコンテナーがすべてインプレースで (通常は基になる同じコンテナー ホスト上で) 再起動されます。

既存のコンテナー グループを再デプロイするには、create コマンドを実行して (または Azure Portal を使用して)、既存のグループの名前を指定します。 作成コマンドを発行して再デプロイをトリガーする場合は、グループの有効なプロパティを少なくとも 1 つ変更し、残りのプロパティは変更しないでおきます (または既定値をそのまま使用します)。 コンテナー グループのすべてのプロパティが再デプロイに有効なわけではありません。 サポート対象外のプロパティの一覧については、「[コンテナーの削除が必要なプロパティ](#properties-that-require-container-delete)」をご覧ください。

次に示す Azure CLI の例では、新しい DNS 名ラベルでコンテナー グループを更新します。 グループの DNS 名ラベルのプロパティが、更新可能なプロパティであるため、コンテナー グループが再デプロイされ、そのコンテナーが再起動されます。

DNS 名ラベル *myapplication-staging* を使用した初期のデプロイ:

```azurecli-interactive
# Create container group
az container create --resource-group myResourceGroup --name mycontainer \
    --image nginx:alpine --dns-name-label myapplication-staging
```

*myapplication* という新しい DNS 名ラベルを使用してコンテナー グループを更新し、残りのプロパティは変更せずにそのままにします。

```azurecli-interactive
# Update DNS name label (restarts container), leave other properties unchanged
az container create --resource-group myResourceGroup --name mycontainer \
    --image nginx:alpine --dns-name-label myapplication
```

## <a name="update-benefits"></a>更新の利点

既存のコンテナー グループを更新する主な利点は、デプロイ時間が短縮されることです。 既存のコンテナー グループを再デプロイすると、以前のデプロイでキャッシュされたコンテナー イメージ レイヤーから対象のグループのレイヤーがプルされます。 新しいデプロイでレジストリに登録されたすべてのイメージ レイヤーをプルするのではなく、変更されたレイヤー (ある場合) だけがプルされます。

Windows Server Core などの大規模なコンテナー イメージに基づくアプリケーションでは、削除と新規のデプロイではなく更新を行うことで、デプロイ速度が大幅に改善されます。

## <a name="limitations"></a>制限事項

コンテナー グループのすべてのプロパティで更新がサポートされているわけではありません。 コンテナー グループの一部のプロパティを変更するには、最初にグループを削除してから再デプロイする必要があります。 詳しくは、「[コンテナーの削除が必要なプロパティ](#properties-that-require-container-delete)」をご覧ください。

コンテナー グループを更新すると、そのコンテナー グループ内のすべてのコンテナーが再起動されます。 複数コンテナー グループに含まれる特定のコンテナーを更新したり、インプレースで再起動したりすることはできません。

通常、コンテナーの IP アドレスが更新の前後で変わることはありませんが、同じアドレスが保持されるという保証もありません。 コンテナー グループが基になる同じホストにデプロイされる限り、コンテナー グループは IP アドレスを保持します。 まれに、Azure Container Instances が同じホストへの再デプロイを試みても、Azure の内部イベントによって別のホストに再デプロイされる場合があります。 この問題を軽減するには、コンテナー インスタンスの DNS 名ラベルを常に使用してください。

終了した、または削除されたコンテナー グループを更新することはできません。 停止 (*Terminated* 状態) または削除されたコンテナー グループは新規としてデプロイされます。

## <a name="properties-that-require-container-delete"></a>コンテナーの削除が必要なプロパティ

前述のとおり、コンテナー グループのすべてのプロパティを更新できるわけではありません。 たとえば、コンテナーのポートまたは再起動ポリシーを変更するには、最初にコンテナー グループを削除してから再び作成する必要があります。

次に示すプロパティでは、再デプロイの前にコンテナー グループを削除する必要があります。

* OS の種類
* CPU
* メモリ
* 再起動ポリシー
* Port

コンテナー グループを削除して再作成する場合、"再デプロイ" ではなく新規作成になります。 すべてのイメージ レイヤーは、以前のデプロイでキャッシュされたレイヤーではなくレジストリからプルされます。 別の基になるホストにコンテナー グループがデプロイされるため、コンテナーの IP アドレスも変わる可能性があります。

## <a name="next-steps"></a>次の手順

この記事では、**コンテナー グループ**についてたびたび言及しました。 Azure Container Instances の各コンテナーはコンテナー グループにデプロイされます。コンテナー グループには複数のコンテナーを格納できます。

[Azure Container Instances のコンテナー グループ](container-instances-container-groups.md)

[複数コンテナー グループのデプロイ](container-instances-multi-container-group.md)

[Azure Container Instances のコンテナーの手動での停止または開始](container-instances-stop-start.md)

<!-- LINKS - External -->

<!-- LINKS - Internal -->
[az-container-create]: /cli/azure/container?view=azure-cli-latest#az-container-create
[azure-cli-install]: /cli/azure/install-azure-cli
