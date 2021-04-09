---
title: Service Fabric Mesh アプリケーションを別のリージョンに移動する
description: 現在のテンプレートのコピーを新しい Azure リージョンにデプロイすることによって、Service Fabric Mesh リソースを移動できます。
author: erikadoyle
ms.author: edoyle
ms.topic: how-to
ms.date: 01/14/2020
ms.custom: subject-moving-resources
ms.openlocfilehash: 1b59d482b8b88e37da2d61636ff3f254a46ba5c2
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "99626089"
---
# <a name="move-a-service-fabric-mesh-application-to-another-azure-region"></a>Service Fabric Mesh アプリケーションを別の Azure リージョンに移動する

> [!IMPORTANT]
> Azure Service Fabric Mesh のプレビューは廃止されました。 Service Fabric Mesh API を介した新しいデプロイは許可されなくなります。 既存のデプロイのサポートは、2021 年 4 月 28 日まで継続されます。
> 
> 詳細については、「[Azure Service Fabric Mesh のプレビューの廃止](https://azure.microsoft.com/updates/azure-service-fabric-mesh-preview-retirement/)」を参照してください。

この記事では、Service Fabric Mesh アプリケーションおよびそのリソースを別の Azure リージョンに移動する方法について説明します。 さまざまな理由により、リソースを別のリージョンに移動する場合があります。 たとえば、障害への対応、特定のリージョンでのみ利用可能な機能やサービスの取得、内部ポリシーとガバナンスの要件の達成、容量計画の要求への対応などです。

 Azure リージョン間でリソースを直接移動する機能は、[Service Fabric Mesh ではサポートされていません](../azure-resource-manager/management/region-move-support.md#microsoftservicefabricmesh)。 ただし、現在の Azure Resource Manager テンプレートのコピーを新しいターゲット リージョンにデプロイし、イグレス トラフィックと依存関係を新しく作成された Service Fabric Mesh アプリケーションにリダイレクトすることで、リソースを間接的に移動できます。

## <a name="prerequisites"></a>前提条件

* クライアントと Service Fabric Mesh アプリケーションの間のトラフィックをルーティングするための仲介役として機能するイングレス コントローラー ([Application Gateway](../application-gateway/index.yml) など)
* ターゲット Azure リージョン (`westus`、`eastus`、または `westeurope`) での Service Fabric Mesh (プレビュー) の可用性

## <a name="prepare"></a>準備

1. 最新のデプロイから Azure Resource Manager テンプレートとパラメーターをエクスポートすることで、Service Fabric Mesh アプリケーションの現在の状態の「スナップショット」を取得します。 これを行うには、Azure portal を使用して「[デプロイ後にテンプレートをエクスポートする](../azure-resource-manager/templates/export-template-portal.md#export-template-after-deployment)」の手順に従います。 [Azure CLI](../azure-resource-manager/management/manage-resource-groups-cli.md#export-resource-groups-to-templates)、[Azure PowerShell](../azure-resource-manager/management/manage-resource-groups-powershell.md#export-resource-groups-to-templates)、または [REST API](/rest/api/resources/resourcegroups/exporttemplate) を使用することもできます。

2. 該当する場合は、ターゲット リージョンでの再デプロイのために、[同じリソース グループ内の他のリソースをエクスポート](../azure-resource-manager/templates/export-template-portal.md#export-template-from-a-resource-group)します。

3. エクスポートされたテンプレートをレビュー (および必要に応じて編集) し、既存のプロパティ値がターゲット リージョンで使用するものであることを確認します。 新しい `location` (Azure リージョン) は、再デプロイ時に指定するパラメーターです。

## <a name="move"></a>詳細ビュー

1. ターゲット リージョンに新しいリソース グループを作成するか、既存のリソース グループを使用します。

2. エクスポートしたテンプレートを使用して、Azure portal を使用して「[カスタム テンプレートからリソースをデプロイする](../azure-resource-manager/templates/deploy-portal.md#deploy-resources-from-custom-template)」の手順に従います。 [Azure CLI](../azure-resource-manager/templates/deploy-cli.md)、[Azure PowerShell](../azure-resource-manager/templates/deploy-powershell.md)、または [REST API](../azure-resource-manager/templates/deploy-rest.md) を使用することもできます。

3. [Azure ストレージ アカウント](../storage/common/storage-account-move.md)などの関連リソースの移動に関するガイダンスについては、「[リージョン間の Azure リソースの移動](../azure-resource-manager/management/move-region.md)」に記載されている各サービスのガイダンスを参照してください。

## <a name="verify"></a>確認

1. デプロイが完了したら、アプリケーション エンドポイントをテストして、アプリケーションの機能を確認します。

2. アプリケーションの状態を確認することもできます。そのためには [Azure Service Fabric Mesh CLI](./service-fabric-mesh-quickstart-deploy-container.md#set-up-service-fabric-mesh-cli) を使用して、アプリケーションの状態 ([az mesh app show](/cli/azure/ext/mesh/mesh/app#ext-mesh-az-mesh-app-show)) を確認し、アプリケーション ログと ([az mesh code-package-log](/cli/azure/ext/mesh/mesh/code-package-log)) コマンドをレビューします。

## <a name="commit"></a>Commit

ターゲット リージョンで Service Fabric Mesh アプリケーションの機能が同じであることを確認したら、イングレス コントローラー ([Application Gateway](../application-gateway/redirect-overview.md) など) を構成して、新しいアプリケーションにトラフィックをリダイレクトします。

## <a name="clean-up-source-resources"></a>ソース リソースをクリーンアップする

Service Fabric Mesh アプリケーションの移動を完了するには、[ソース アプリケーションおよび親リソース グループを削除](../azure-resource-manager/management/delete-resource-group.md)します。

## <a name="next-steps"></a>次のステップ

* [リージョン間で Azure リソースを移動する](../azure-resource-manager/management/move-region.md)
* [リージョン間の Azure リソースの移動のサポート](../azure-resource-manager/management/region-move-support.md)
* [リソースを新しいリソース グループまたはサブスクリプションに移動する](../azure-resource-manager/management/move-resource-group-and-subscription.md)
* [リソースの移動操作のサポート](../azure-resource-manager/management/move-support-resources.md
)
