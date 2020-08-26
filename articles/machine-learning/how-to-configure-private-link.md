---
title: プライベート エンドポイントを構成する (プレビュー)
titleSuffix: Azure Machine Learning
description: Azure Private Link を使用して、仮想ネットワークから Azure Machine Learning ワークスペースに安全にアクセスします。
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.custom: how-to
ms.author: aashishb
author: aashishb
ms.reviewer: larryfr
ms.date: 07/28/2020
ms.openlocfilehash: bdb7ba30d9fa2d0bd1eff9368d6e30e516b53895
ms.sourcegitcommit: 9ce0350a74a3d32f4a9459b414616ca1401b415a
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/13/2020
ms.locfileid: "88192733"
---
# <a name="configure-azure-private-link-for-an-azure-machine-learning-workspace-preview"></a>Azure Machine Learning ワークスペース用に Azure Private Link を構成する (プレビュー)

このドキュメントでは、Azure Machine Learning ワークスペースで Azure Private Link を使用する方法について説明します。 

> [!IMPORTANT]
> Azure Machine Learning ワークスペースでの Azure Private Link の使用は、現在パブリック プレビュー段階です。 この機能は**米国東部**と**米国西部 2** リージョン でのみ利用できます。 このプレビュー版はサービス レベル アグリーメントなしで提供されています。運用環境のワークロードに使用することはお勧めできません。 特定の機能はサポート対象ではなく、機能が制限されることがあります。 詳しくは、[Microsoft Azure プレビューの追加使用条件](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)に関するページをご覧ください。

Azure Private Link では、プライベート エンドポイントを使用してワークスペースに接続できます。 プライベート エンドポイントは、仮想ネットワーク内にある一組のプライベート IP アドレスです。 これにより、ワークスペースへのアクセスが、プライベート IP アドレスでのみ行われるように制限できます。 Private Link を使用すると、データ窃盗のリスクを軽減できます。 プライベート エンドポイントの詳細については、[Azure Private Link](/azure/private-link/private-link-overview) に関する記事を参照してください。

> [!IMPORTANT]
> Azure Private Link は、ワークスペースの削除やコンピューティング リソースの管理などの Azure コントロール プレーン (管理操作) には影響しません。 たとえば、コンピューティング先の作成、更新、削除などです。 これらの操作は、通常どおりパブリック インターネット経由で実行されます。
>
> Azure Machine Learning コンピューティング インスタンス プレビューは、Private Link が有効になっているワークスペースではサポートされていません。
>
> Mozilla Firefox を使用している場合、ワークスペースのプライベート エンドポイントにアクセスしようとしたときに問題が発生することがあります。 この問題は、Mozilla の DNS over HTTPS に関連している可能性があります。 回避策として、Microsoft Edge または Google Chrome を使用することをお勧めします。

## <a name="create-a-workspace-that-uses-a-private-endpoint"></a>プライベート エンドポイントを使用するワークスペースを作成する

> [!IMPORTANT]
> 現時点では、新しい Azure Machine Learning ワークスペースを作成する場合にのみ、プライベート エンドポイントの有効化がサポートされています。

[https://github.com/Azure/azure-quickstart-templates/tree/master/201-machine-learning-advanced](https://github.com/Azure/azure-quickstart-templates/tree/master/201-machine-learning-advanced) のテンプレートを使用し、プライベート エンドポイントでワークスペースを作成できます。

プライベート エンドポイントを含め、このテンプレートの使用に関する詳細については、「[Azure Resource Manager テンプレートを使用して Azure Machine Learning のワークスペースを作成します。](how-to-create-workspace-template.md)」を参照してください。

## <a name="using-a-workspace-over-a-private-endpoint"></a>プライベート エンドポイント経由でのワークスペースの使用

ワークスペースへの通信は仮想ネットワークからのみ許可されているため、ワークスペースを使用する開発環境はすべて仮想ネットワークのメンバーである必要があります。 たとえば、仮想ネットワーク内の仮想マシンを使用します。

> [!IMPORTANT]
> 接続の一時的な中断を回避するために、Private Link を有効にした後で、ワークスペースに接続しているコンピューターの DNS キャッシュをフラッシュすることをお勧めします。 

Azure Virtual Machines について詳しくは、「[Virtual Machines のドキュメント](/azure/virtual-machines/)」を参照してください。


## <a name="using-azure-storage"></a>Azure Storage を使用する

ワークスペースによって使用される Azure Storage アカウントをセキュリティで保護するには、それを仮想ネットワーク内に配置します。

仮想ネットワークにストレージ アカウントを配置する方法の詳細については、「[ワークスペース用のストレージ アカウントを使用する](how-to-enable-virtual-network.md#use-a-storage-account-for-your-workspace)」を参照してください。

> [!WARNING]
> Azure Machine Learning では、Private Link が有効になっている Azure Storage アカウントの使用はサポートされていません。

## <a name="using-azure-key-vault"></a>Azure Key Vault の使用

ワークスペースで使用されている Azure Key Vault をセキュリティで保護するには、それを仮想ネットワーク内に配置するか、それに対して Private Link を有効にすることができます。

仮想ネットワークにキー コンテナーを配置する方法については、「[ワークスペース内でキー コンテナー インスタンスを使用する](how-to-enable-virtual-network.md#key-vault-instance)」を参照してください。

キー コンテナーの Private Link を有効にする方法については、「[Key Vault を Azure Private Link と統合する](/azure/key-vault/private-link-service)」を参照してください。

## <a name="using-azure-kubernetes-services"></a>Azure Kubernetes Service の使用

ワークスペースによって使用される Azure Kubernetes Service をセキュリティで保護するには、それを仮想ネットワーク内に配置します。 詳細については、[ワークスペースで Azure Kubernetes Service を使用する](how-to-enable-virtual-network.md#aksvnet)方法に関するページを参照してください。

Azure Machine Learning で、Private Link が有効な Azure Kubernetes サービスの使用がサポートされるようになりました。
プライベート AKS クラスターを作成するには、[こちら](https://docs.microsoft.com/azure/aks/private-clusters)のドキュメントに従ってください

## <a name="azure-container-registry"></a>Azure Container Registry

仮想ネットワーク内の Azure Container Registry のセキュリティ保護については、「[Azure Container Registry を使用する](how-to-enable-virtual-network.md#azure-container-registry)」を参照してください。

> [!IMPORTANT]
> お使いの Azure Machine Learning ワークスペースに対して Private Link を使用しているときに、ワークスペースの Azure Container Registry を仮想ネットワークに配置する場合は、次の Azure Resource Manager テンプレートも適用する必要があります。 このテンプレートによって、お使いのワークスペースで Private Link を使用して ACR と通信できるようになります。

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
      "keyVaultArmId": {
      "type": "string"
      },
      "workspaceName": {
      "type": "string"
      },
      "containerRegistryArmId": {
      "type": "string"
      },
      "applicationInsightsArmId": {
      "type": "string"
      },
      "storageAccountArmId": {
      "type": "string"
      },
      "location": {
      "type": "string"
      }
  },
  "resources": [
      {
      "type": "Microsoft.MachineLearningServices/workspaces",
      "apiVersion": "2019-11-01",
      "name": "[parameters('workspaceName')]",
      "location": "[parameters('location')]",
      "identity": {
          "type": "SystemAssigned"
      },
      "sku": {
          "tier": "enterprise",
          "name": "enterprise"
      },
      "properties": {
          "sharedPrivateLinkResources":
  [{"Name":"Acr","Properties":{"PrivateLinkResourceId":"[concat(parameters('containerRegistryArmId'), '/privateLinkResources/registry')]","GroupId":"registry","RequestMessage":"Approve","Status":"Pending"}}],
          "keyVault": "[parameters('keyVaultArmId')]",
          "containerRegistry": "[parameters('containerRegistryArmId')]",
          "applicationInsights": "[parameters('applicationInsightsArmId')]",
          "storageAccount": "[parameters('storageAccountArmId')]"
      }
      }
  ]
}
```

## <a name="next-steps"></a>次のステップ

Azure Machine Learning ワークスペースのセキュリティ保護の詳細については、[エンタープライズ セキュリティ](concept-enterprise-security.md)に関する記事を参照してください。