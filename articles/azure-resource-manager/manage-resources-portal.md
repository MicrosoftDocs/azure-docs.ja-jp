---
title: Azure portal を使用した Azure リソースの管理 | Microsoft Docs
description: Azure portal と Azure Resource Manager を使用してリソースを管理します。
services: azure-resource-manager,azure-portal
documentationcenter: ''
author: mumian
ms.service: azure-resource-manager
ms.topic: conceptual
ms.date: 02/11/2019
ms.author: jgao
ms.openlocfilehash: a4ff94a98514041290518e72e1b13c76722b9d4f
ms.sourcegitcommit: b7a44709a0f82974578126f25abee27399f0887f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/18/2019
ms.locfileid: "67205596"
---
# <a name="manage-azure-resources-by-using-the-azure-portal"></a>Azure portal を使用した Azure リソース の管理

[Azure portal](https://portal.azure.com) と [Azure Resource Manager](resource-group-overview.md) を使用して Azure リソースを管理する方法について説明します。 リソース グループの管理については、[Azure portal を使用した Azure リソース グループの管理](./manage-resource-groups-portal.md)に関するページを参照してください。

リソースの管理に関するその他の記事は次のとおりです。

- [Manage Azure resources by using Azure CLI (Azure CLI を使用して Azure リソースを管理する)](./manage-resources-cli.md)
- [Manage Azure resources by using Azure PowerShell (Azure PowerShell を使用して Azure リソースを管理する)](./manage-resources-powershell.md)

[!INCLUDE [Handle personal data](../../includes/gdpr-intro-sentence.md)]

## <a name="deploy-resources-to-a-resource-group"></a>リソースをリソース グループにデプロイする

Resource Manager テンプレートを作成した後に、Azure portal を使用して Azure リソースをデプロイできます。 テンプレートの作成については、「[クイック スタート:Azure portal を使用した Azure Resource Manager テンプレートの作成とデプロイ](./resource-manager-quickstart-create-templates-use-the-portal.md)」を参照してください。 テンプレートのデプロイについては、「[Resource Manager テンプレートと Azure portal を使用したリソースのデプロイ](resource-group-template-deploy-portal.md)」を参照してください。

## <a name="open-resources"></a>リソースを開く

Azure のリソースは、Azure のサービスとリソース グループで構成されます。 次の手順で、**mystorage0207** という名前のストレージ アカウントを開く方法を示します。 仮想マシンは、**mystorage0207rg** という名前のリソース グループ内に存在します。

サービスの種類によってリソースを開くには、次の手順を実行します。

1. [Azure Portal](https://portal.azure.com) にサインインします。
2. 左側のウィンドウで、Azure のサービスを選択します。 この場合は、 **[ストレージ アカウント]** です。  該当するサービスが表示されない場合は、 **[すべてのサービス]** を選択し、サービスの種類を選択します。

    ![ポータルで Azure リソースを開く](./media/manage-resources-portal/manage-azure-resources-portal-open-service.png)

3. 開くリソースを選択します。

    ![ポータルで Azure リソースを開く](./media/manage-resources-portal/manage-azure-resources-portal-open-resource.png)

    ストレージ アカウントは次のとおりです。

    ![ポータルで Azure リソースを開く](./media/manage-resources-portal/manage-azure-resources-portal-open-resource-storage.png)

リソース グループによってリソースを開くには、次の手順を実行します。

1. [Azure Portal](https://portal.azure.com) にサインインします。
2. 左側のウィンドウで、 **[リソース グループ]** を選択し、グループ内のリソースを一覧表示します。
3. 開くリソースを選択します。 

## <a name="manage-resources"></a>リソースの管理

ポータルでリソースを表示すると、その特定のリソースを管理するためのオプションが表示されます。

![Azure のリソースを管理する](./media/manage-resources-portal/manage-azure-resources-portal-manage-resource.png)

このスクリーンショットには、Azure 仮想マシンの管理オプションが表示されています。 仮想マシンの起動、再起動、停止などの操作を実行できます。

## <a name="delete-resources"></a>リソースを削除する

1. ポータルでリソースを開きます。 手順については、「[リソースを開く](#open-resources)」を参照してください。
2. **[削除]** を選択します。 次のスクリーンショットには、仮想マシンの管理オプションが表示されています。

    ![Azure リソースを削除する](./media/manage-resources-portal/manage-azure-resources-portal-delete-resource.png)
3. 削除を確認するためにリソースの名前を入力し、 **[削除]** をクリックします。

Azure Resource Manager によってリソースの削除の順序が決定される方法の詳細については、「[Azure Resource Manager によるリソース グループの削除](./resource-group-delete.md)」をご覧ください。

## <a name="move-resources"></a>リソースの移動

1. ポータルでリソースを開きます。 手順については、「[リソースを開く](#open-resources)」を参照してください。
2. **[移動]** を選択します。 次のスクリーンショットには、ストレージ アカウントの管理オプションが表示されています。

    ![Azure リソースを移動する](./media/manage-resources-portal/manage-azure-resources-portal-move-resource.png)
3. ニーズに応じて、 **[別のリソース グループに移動する]** または **[別のサブスクリプションに移動する]** を選択します。

詳細については、「 [新しいリソース グループまたはサブスクリプションへのリソースの移動](resource-group-move-resources.md)」を参照してください。

## <a name="lock-resources"></a>リソースのロック

ロックを適用することで、組織の他のユーザーが重要なリソース (Azure サブスクリプション、リソース グループ、リソースなど) を誤って削除または変更するのを防ぐことができます。 

1. ポータルでリソースを開きます。 手順については、「[リソースを開く](#open-resources)」を参照してください。
2. **[ロック]** を選択します。 次のスクリーンショットには、ストレージ アカウントの管理オプションが表示されています。

    ![Azure リソースをロックする](./media/manage-resources-portal/manage-azure-resources-portal-lock-resource.png)
3. **[追加]** を選択し、ロックのプロパティを指定します。

詳細については、[「Azure Resource Manager によるリソースのロック」](resource-group-lock-resources.md)を参照してください。

## <a name="tag-resources"></a>リソースへのタグ付け

タグ付けすると、リソース グループとリソースを論理的に整理できます。 

1. ポータルでリソースを開きます。 手順については、「[リソースを開く](#open-resources)」を参照してください。
2. **[タグ]** を選択します。 次のスクリーンショットには、ストレージ アカウントの管理オプションが表示されています。

    ![Azure リソースにタグを付ける](./media/manage-resources-portal/manage-azure-resources-portal-tag-resource.png)
3. タグのプロパティを指定し、 **[保存]** を選択します。

詳細については、[タグを使用した Azure リソースの整理](./resource-group-using-tags.md#portal)に関する記事をご覧ください。

## <a name="monitor-resources"></a>リソースの監視

リソースを開くと、そのリソースの種類を監視するための既定のグラフと表がポータルに表示されます。 次のスクリーンショットには、仮想マシンのグラフが表示されています。

![Azure リソースを監視する](./media/manage-resources-portal/manage-azure-resources-portal-monitor-resource.png)

グラフをダッシュボードにピン留めするには、グラフの右上隅のピン アイコンを選択します。 ダッシュボードの操作の詳細については、「 [Azure Portal でのダッシュボードの作成と共有](../azure-portal/azure-portal-dashboards.md)」を参照してください。

## <a name="manage-access-to-resources"></a>リソースへのアクセスの管理

[ロールベースのアクセス制御 (RBAC)](../role-based-access-control/overview.md) は、Azure に存在するリソースに対するアクセス権を管理するための手法です。 詳細については、「[RBAC と Azure portal を使用してアクセスを管理する](../role-based-access-control/role-assignments-portal.md)」を参照してください。

## <a name="next-steps"></a>次の手順

- Azure Resource Manager については、「[Azure Resource Manager の概要](./resource-group-overview.md)」を参照してください。
- Resource Manager テンプレートの構文については、「[Azure Resource Manager テンプレートの構造と構文の詳細](./resource-group-authoring-templates.md)」を参照してください。
- テンプレートを開発する方法については、[ステップバイステップのチュートリアル](/azure/azure-resource-manager/)のページをご覧ください。
- Azure Resource Manager テンプレートのスキーマを表示するには、[テンプレート リファレンス](/azure/templates/)のページをご覧ください。