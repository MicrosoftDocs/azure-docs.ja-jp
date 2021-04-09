---
title: リソース グループの管理 - Azure portal
description: Azure portal を使用して、Azure Resource Manager 経由でリソース グループを管理します。 リソース グループを作成、一覧表示、および削除する方法を示します。
author: mumian
ms.topic: conceptual
ms.date: 03/26/2019
ms.author: jgao
ms.openlocfilehash: aabaad54dce52d57aaa4689f591351e290b0f9a3
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "101093702"
---
# <a name="manage-azure-resource-manager-resource-groups-by-using-the-azure-portal"></a>Azure portal を使用して Azure Resource Manager リソース グループを管理する

[Azure portal](https://portal.azure.com) と [Azure Resource Manager](overview.md) を使用して Azure リソース グループを管理する方法について説明します。 Azure リソースの管理については、「[Manage Azure resources by using Azure portal](manage-resources-portal.md)」(Azure portal を使用して Azure リソースを管理する) を参照してください。

リソース グループの管理に関するその他の記事は次のとおりです。

- [Azure CLI を使用した Azure リソース グループの管理](manage-resources-cli.md)に関する記事
- [Azure PowerShell を使用した Azure リソース グループの管理](manage-resources-powershell.md)に関する記事

[!INCLUDE [Handle personal data](../../../includes/gdpr-intro-sentence.md)]

## <a name="what-is-a-resource-group"></a>リソース グループとは

リソース グループは、Azure ソリューションの関連するリソースを保持するコンテナーです。 リソース グループには、ソリューションのすべてのリソースか、グループとして管理したいリソースのみを含めることができます。 組織のニーズに合わせてリソースをリソース グループに割り当てる方法を指定してください。 通常は、同じライフサイクルを共有するリソースを同じリソース グループに追加して、グループとして簡単にデプロイ、更新、および削除できるようにします。

リソース グループには、リソースについてのメタデータが格納されます。 そのため、リソース グループの場所を指定するとき、このメタデータが格納される場所を指定することになります。 コンプライアンス上の理由から、データは特定のリージョンに格納されるようにする必要があります。


## <a name="create-resource-groups"></a>リソース グループを作成する

1. [Azure portal](https://portal.azure.com) にサインインします。
2. **[リソース グループ]** を選択します

    ![add resource group](./media/manage-resource-groups-portal/manage-resource-groups-add-group.png)
3. **[追加]** を選択します。
4. 次の値を入力します。

   - **サブスクリプション**:Azure サブスクリプションを選択します。 
   - **[リソース グループ]** :新しいリソース グループの名前を入力します。 
   - **[リージョン]** : **[米国中部]** など、Azure の場所を選択します。

     ![リソース グループの作成](./media/manage-resource-groups-portal/manage-resource-groups-create-group.png)
5. **[確認と作成]** を選択します
6. **［作成］** を選択します リソース グループの作成には数秒かかります。
7. 上部のメニューにある **[最新の情報に更新]** を選択してリソース グループの一覧を最新の状態に更新し、新しく作成されたリソース グループを選択してこれを開きます。 または、上部にある **[通知]** (ベルのアイコン) をクリックしてから、 **[リソース グループに移動]** を選択して、新しく作成されたリソース グループを開きます

    ![リソース グループに移動](./media/manage-resource-groups-portal/manage-resource-groups-add-group-go-to-resource-group.png)

## <a name="list-resource-groups"></a>リソース グループの一覧を表示する

1. [Azure portal](https://portal.azure.com) にサインインします。
2. リソース グループを表示するには、 **[リソース グループ]** を選択します

    ![リソース グループの参照](./media/manage-resource-groups-portal/manage-resource-groups-list-groups.png)

3. リソース グループについて表示される情報をカスタマイズするには、 **[列の編集]** を選択します。 次のスクリーンショットは、表示に追加できる追加列を示しています。

## <a name="open-resource-groups"></a>リソース グループを開く

1. [Azure portal](https://portal.azure.com) にサインインします。
2. **[リソース グループ]** を選択します。
3. 開くリソース グループを選択します。

## <a name="delete-resource-groups"></a>リソース グループを削除する

1. 削除するリソース グループを開きます。  「[リソース グループを開く](#open-resource-groups)」を参照してください。
2. **[リソース グループの削除]** を選択します。

    ![Azure リソース グループを削除する](./media/manage-resource-groups-portal/delete-group.png)

Azure Resource Manager によってリソースの削除の順序が決定される方法の詳細については、「[Azure Resource Manager によるリソース グループの削除](delete-resource-group.md)」を参照してください。

## <a name="deploy-resources-to-a-resource-group"></a>リソースをリソース グループにデプロイする

Resource Manager テンプレートを作成した後に、Azure portal を使用して Azure リソースをデプロイできます。 テンプレートの作成については、「[クイック スタート:Azure portal を使用した Azure Resource Manager テンプレートの作成とデプロイ](../templates/quickstart-create-templates-use-the-portal.md)」を参照してください。 テンプレートのデプロイについては、「[Resource Manager テンプレートと Azure portal を使用したリソースのデプロイ](../templates/deploy-portal.md)」を参照してください。

## <a name="move-to-another-resource-group-or-subscription"></a>別のリソース グループまたはサブスクリプションに移動する

グループ内のリソースを別のリソース グループに移動できます。 詳細については、「 [新しいリソース グループまたはサブスクリプションへのリソースの移動](move-resource-group-and-subscription.md)」を参照してください。

## <a name="lock-resource-groups"></a>リソース グループをロックする

ロックすることで、組織内の他のユーザーが重要なリソース (Azure サブスクリプション、リソース グループ、リソースなど) を誤って削除または変更することを防ぎます。 

1. ロックするリソース グループを開きます。  「[リソース グループを開く](#open-resource-groups)」を参照してください。
2. 左側のウィンドウで、 **[ロック]** を選択します。
3. ロックをリソース グループに追加するには、 **[追加]** を選択します。
4. **[ロック名]** 、 **[ロックの種類]** 、 **[メモ]** を入力します。 ロックの種類には、 **[読み取り専用]** および **[削除]** が含まれます。

    ![Azure リソース グループをロックする](./media/manage-resource-groups-portal/manage-resource-groups-add-lock.png)

詳細については、「[リソースのロックによる予期せぬ変更の防止](lock-resources.md)」を参照してください。

## <a name="tag-resource-groups"></a>リソース グループにタグを適用する

リソース グループやリソースにタグを適用して、アセットを論理的に整理できます。 詳細については、[タグを使用した Azure リソースの整理](tag-resources.md#portal) に関する記事をご覧ください。

## <a name="export-resource-groups-to-templates"></a>リソース グループをテンプレートにエクスポートする

テンプレートをエクスポートする方法の詳細については、[テンプレートへの単一および複数のリソースのエクスポート - ポータル](../templates/export-template-portal.md)に関するページを参照してください。

## <a name="manage-access-to-resource-groups"></a>リソース グループへのアクセスを管理する

[Azure ロールベースのアクセス制御 (Azure RBAC)](../../role-based-access-control/overview.md) は、Azure のリソースへのアクセスを管理する方法です。 詳細については、[Azure portal を使用して Azure ロールを割り当てる方法](../../role-based-access-control/role-assignments-portal.md)に関するページを参照してください。

## <a name="next-steps"></a>次のステップ

- Azure Resource Manager については、「[Azure Resource Manager の概要](overview.md)」を参照してください。
- Resource Manager テンプレートの構文については、「[Azure Resource Manager テンプレートの構造と構文の詳細](../templates/template-syntax.md)」を参照してください。
- テンプレートを開発する方法については、[ステップバイステップのチュートリアル](../index.yml)のページをご覧ください。
- Azure Resource Manager テンプレートのスキーマを表示するには、[テンプレート リファレンス](/azure/templates/)のページをご覧ください。
