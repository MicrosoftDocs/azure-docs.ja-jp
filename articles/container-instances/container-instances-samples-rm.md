---
title: Azure Resource Manager テンプレート サンプル
description: さまざまな構成で Azure Container Instances をデプロイする Azure Resource Manager テンプレートのサンプルを見つける
ms.topic: article
ms.date: 03/07/2019
ms.openlocfilehash: 6c487087b39244178643fd81364150ceb3ac4a63
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "86169648"
---
# <a name="azure-resource-manager-templates-for-azure-container-instances"></a>Azure Container Instances のための Azure Resource Manager テンプレート

次のサンプル テンプレートは、さまざまな構成のコンテナー インスタンスをデプロイします。

デプロイオプションについては、[デプロイ](#deployment)セクションを参照してください。 独自のテンプレートを作成する場合は、Azure Container Instances の [Azure Resource Manager テンプレート リファレンス][ref]にテンプレートの形式と使用可能なプロパティが詳しく説明されています。

## <a name="sample-templates"></a>サンプル テンプレート

| Template | 説明 |
|-|-|
| **アプリケーション** ||
| [WordPress][app-wp] | コンテナー グループに WordPress Web サイトと、MySQL データベースを作成します。 WordPress サイトのコンテンツと MySQL データベースは、Azure Files 共有で永続化されます。 WordPress へのパブリック ネットワーク アクセスを公開するアプリケーション ゲートウェイも作成します。 |
| [SQL Server と IIS を使用する MS NAV][app-nav] | 全機能を備えた自己完結型 Dynamics NAV / Dynamics 365 Business Central 環境に単一の Windows コンテナーをデプロイします。 |
| **Volumes** ||
| [emptyDir][vol-emptydir] | emptyDir ボリュームを共有する 2 つの Linux コンテナーをデプロイします。 |
| [gitRepo][vol-gitrepo] | GitHub リポジトリのクローンを作成し、ボリュームとしてマウントする Linux コンテナーをデプロイします。 |
| [secret][vol-secret] | シークレット ボリュームとしてマウントされている PFX 証明書を使用して Linux コンテナーを配置します。 |
| **ネットワーク** ||
| [UDP 公開されたコンテナー][net-udp] | UDP ポートを公開する、Windows または Linux のコンテナーをデプロイします。 |
| [パブリック IP を使用する Linux コンテナー][net-publicip] | パブリック IP を使用してアクセスできる 1 つの Linux コンテナーをデプロイします。 |
| [仮想ネットワークでのコンテナー グループのデプロイ][net-vnet] | 新しい仮想ネットワーク、サブネット、ネットワーク プロファイル、およびコンテナー グループをデプロイします。 |
| **Azure リソース** ||
| [Azure Storage アカウントおよび Azure Files 共有を作成する][az-files] | コンテナー インスタンスで Azure CLI を使用して、ストレージ アカウントと Azure Files 共有します。

## <a name="deployment"></a>デプロイ

Resource Manager テンプレートを使用してリソースを配置するには、いくつかのオプションがあります。

[Azure CLI][deploy-cli]

[Azure PowerShell][deploy-powershell]

[Azure Portal][deploy-portal]

[REST API][deploy-rest]

<!-- LINKS - External -->
[app-nav]: https://github.com/Azure/azure-quickstart-templates/tree/master/101-aci-dynamicsnav
[app-wp]: https://github.com/Azure/azure-quickstart-templates/tree/master/201-aci-wordpress
[az-files]: https://github.com/Azure/azure-quickstart-templates/tree/master/101-aci-storage-file-share
[net-publicip]: https://github.com/Azure/azure-quickstart-templates/tree/master/101-aci-linuxcontainer-public-ip
[net-udp]: https://github.com/Azure/azure-quickstart-templates/tree/master/201-aci-udp
[net-vnet]: https://github.com/Azure/azure-quickstart-templates/tree/master/101-aci-vnet
[repo]: https://github.com/Azure/azure-quickstart-templates
[vol-emptydir]: https://github.com/Azure/azure-quickstart-templates/tree/master/201-aci-linuxcontainer-volume-emptydir
[vol-gitrepo]: https://github.com/Azure/azure-quickstart-templates/tree/master/201-aci-linuxcontainer-volume-gitrepo
[vol-secret]: https://github.com/Azure/azure-quickstart-templates/tree/master/201-aci-linuxcontainer-volume-secret

<!-- LINKS - Internal -->
[deploy-cli]: ../azure-resource-manager/templates/deploy-cli.md
[deploy-portal]: ../azure-resource-manager/templates/deploy-portal.md
[deploy-powershell]: ../azure-resource-manager/templates/deploy-powershell.md
[deploy-rest]: ../azure-resource-manager/templates/deploy-rest.md
[ref]: /azure/templates/microsoft.containerinstance/containergroups
