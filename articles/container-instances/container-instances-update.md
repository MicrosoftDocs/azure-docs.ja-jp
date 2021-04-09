---
title: コンテナー グループの更新
description: Azure Container Instances のコンテナー グループに含まれる実行中のコンテナーを更新する方法について説明します。
ms.topic: article
ms.date: 04/17/2020
ms.openlocfilehash: fb31eeda83532c408a303e879439006bcd7d4e45
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "102200650"
---
# <a name="update-containers-in-azure-container-instances"></a>Azure Container Instances のコンテナーを更新する

コンテナー インスタンスの通常の操作中に、[コンテナー グループ](./container-instances-container-groups.md)内の実行中のコンテナーを更新することが必要になる場合があります。 たとえば、イメージのバージョン、DNS 名、または環境変数などのプロパティを更新したり、アプリケーションがクラッシュしたコンテナー内のプロパティを更新したりする場合があります。

コンテナー グループ内の実行中のコンテナーを更新するには、少なくとも 1 つのプロパティが変更された既存のグループを再デプロイします。 コンテナー グループを更新すると、そのグループに含まれる実行中のコンテナーがすべてインプレースで (通常は基になる同じコンテナー ホスト上で) 再起動されます。

> [!NOTE]
> 終了した、または削除されたコンテナー グループを更新することはできません。 停止 (Succeeded または Failed のいずれかの状態) または削除されたコンテナー グループは、新規としてデプロイする必要があります。 他の[制限事項](#limitations)を参照してください。

## <a name="update-a-container-group"></a>コンテナー グループを更新する

既存のコンテナー グループを更新するには、次のようにします。

* create コマンドを発行 (または Azure portal を使用) し、既存のグループの名前を指定します。 
* 再デプロイするときに、更新をサポートするグループのプロパティを少なくとも 1 つ変更または追加します。 特定のプロパティでは、[更新はサポートされていません](#properties-that-require-container-delete)。
* 以前に指定した値を使用して、他のプロパティを設定します。 プロパティの値を設定しなかった場合は、既定値に戻ります。

> [!TIP]
> [YAML ファイル](./container-instances-container-groups.md#deployment)は、コンテナー グループのデプロイ構成を維持するのに役立ち、更新されたグループをデプロイするための開始点を提供します。 別の方法を使用してグループを作成した場合は、[az container export][az-container-export] を使用して、構成を YAML にエクスポートすることができます。 

### <a name="example"></a>例

次に示す Azure CLI の例では、新しい DNS 名ラベルでコンテナー グループを更新します。 グループの DNS 名ラベルのプロパティが、更新可能なプロパティであるため、コンテナー グループが再デプロイされ、そのコンテナーが再起動されます。

DNS 名ラベル *myapplication-staging* を使用した初期のデプロイ:

```azurecli-interactive
# Create container group
az container create --resource-group myResourceGroup --name mycontainer \
    --image nginx:alpine --dns-name-label myapplication-staging
```

*myapplication* という新しい DNS 名ラベルを使用してコンテナー グループを更新し、残りのプロパティは、以前使用した値を使って設定します。

```azurecli-interactive
# Update DNS name label (restarts container), leave other properties unchanged
az container create --resource-group myResourceGroup --name mycontainer \
    --image nginx:alpine --dns-name-label myapplication
```

## <a name="update-benefits"></a>更新の利点

既存のコンテナー グループを更新する主な利点は、デプロイ時間が短縮されることです。 既存のコンテナー グループを再デプロイすると、以前のデプロイでキャッシュされたコンテナー イメージ レイヤーから対象のグループのレイヤーがプルされます。 新しいデプロイでレジストリに登録されたすべてのイメージ レイヤーをプルするのではなく、変更されたレイヤー (ある場合) だけがプルされます。

Windows Server Core などの大規模なコンテナー イメージに基づくアプリケーションでは、削除と新規のデプロイではなく更新を行うことで、デプロイ速度が大幅に改善されます。

## <a name="limitations"></a>制限事項

* コンテナー グループのすべてのプロパティで更新がサポートされているわけではありません。 コンテナー グループの一部のプロパティを変更するには、最初にグループを削除してから再デプロイする必要があります。 「[コンテナーの削除が必要なプロパティ](#properties-that-require-container-delete)」を参照してください。
* コンテナー グループを更新すると、そのコンテナー グループ内のすべてのコンテナーが再起動されます。 複数コンテナー グループに含まれる特定のコンテナーを更新したり、インプレースで再起動したりすることはできません。
* 通常、コンテナー グループの IP アドレスは、更新されても保持されますが、同じままであることは保証されません。 コンテナー グループが基になる同じホストにデプロイされる限り、コンテナー グループは IP アドレスを保持します。 まれに、一部の Azure 内部イベントが原因で、別のホストに再デプロイされる場合があります。 この問題を軽減するには、コンテナー インスタンスに対して DNS 名ラベルを使用することをお勧めします。
* 終了した、または削除されたコンテナー グループを更新することはできません。 コンテナー グループが停止 ("*終了*" 状態) または削除されると、グループは新規としてデプロイされます。

## <a name="properties-that-require-container-delete"></a>コンテナーの削除が必要なプロパティ

コンテナー グループのすべてのプロパティを更新できるわけではありません。 たとえば、コンテナーの再起動ポリシーを変更するには、まずコンテナー グループを削除し、それから再び作成する必要があります。

次のプロパティを変更した場合は、再デプロイの前にコンテナー グループの削除が必要になります。

* OS の種類
* CPU、メモリ、または GPU リソース
* 再起動ポリシー
* ネットワーク プロファイル

コンテナー グループを削除して再作成する場合、"再デプロイ" ではなく新規作成になります。 すべてのイメージ レイヤーは、以前のデプロイでキャッシュされたレイヤーではなくレジストリからプルされます。 別の基になるホストにコンテナー グループがデプロイされるため、コンテナーの IP アドレスも変わる可能性があります。

## <a name="next-steps"></a>次のステップ

この記事では、**コンテナー グループ** についてたびたび言及しました。 Azure Container Instances の各コンテナーはコンテナー グループにデプロイされます。コンテナー グループには複数のコンテナーを格納できます。

[Azure Container Instances のコンテナー グループ](./container-instances-container-groups.md)

[複数コンテナー グループのデプロイ](container-instances-multi-container-group.md)

[Azure Container Instances のコンテナーの手動での停止または開始](container-instances-stop-start.md)

<!-- LINKS - External -->

<!-- LINKS - Internal -->
[az-container-create]: /cli/azure/container#az-container-create
[azure-cli-install]: /cli/azure/install-azure-cli
[az-container-export]: /cli/azure/container#az-container-export
