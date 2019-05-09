---
title: Azure portal を使用して Azure Resource Manager グループを管理する | Microsoft Docs
description: Azure portal を使用して Azure Resource Manager グループを管理します。
services: azure-resource-manager,azure-portal
documentationcenter: ''
author: mumian
ms.service: azure-resource-manager
ms.workload: multiple
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 03/26/2019
ms.author: jgao
ms.openlocfilehash: cb1eb5ac27c53f4c0d48fe3644febc62f848486d
ms.sourcegitcommit: 0dd053b447e171bc99f3bad89a75ca12cd748e9c
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/26/2019
ms.locfileid: "58484697"
---
# <a name="manage-azure-resource-manager-resource-groups-by-using-the-azure-portal"></a>Azure portal を使用して Azure Resource Manager リソース グループを管理する

[Azure portal](https://portal.azure.com) と [Azure Resource Manager](resource-group-overview.md) を使用して Azure リソース グループを管理する方法について説明します。 Azure リソースの管理については、「[Manage Azure resources by using Azure portal](./manage-resources-portal.md)」(Azure portal を使用して Azure リソースを管理する) を参照してください。

リソース グループの管理に関するその他の記事は次のとおりです。

- [Azure CLI を使用した Azure リソース グループの管理](./manage-resources-cli.md)に関する記事
- [Azure PowerShell を使用した Azure リソース グループの管理](./manage-resources-powershell.md)に関する記事

[!INCLUDE [Handle personal data](../../includes/gdpr-intro-sentence.md)]

## <a name="what-is-a-resource-group"></a>リソース グループとは

リソース グループは、Azure ソリューションの関連するリソースを保持するコンテナーです。 リソース グループには、ソリューションのすべてのリソースか、グループとして管理したいリソースのみを含めることができます。 組織のニーズに合わせてリソースをリソース グループに割り当てる方法を指定してください。 通常は、同じライフサイクルを共有するリソースを同じリソース グループに追加して、グループとして簡単にデプロイ、更新、および削除できるようにします。

リソース グループには、リソースについてのメタデータが格納されます。 そのため、リソース グループの場所を指定するとき、このメタデータが格納される場所を指定することになります。 コンプライアンス上の理由から、データは特定のリージョンに格納されるようにする必要があります。

リソース グループには、リソースについてのメタデータが格納されます。 リソース グループの場所を指定するとき、このメタデータが格納される場所を指定することになります。

## <a name="create-resource-groups"></a>リソース グループを作成する

1. [Azure Portal](https://portal.azure.com) にサインインします。
2. **[リソース グループ]** を選択します

    ![add resource group](./media/manage-resource-groups-portal/manage-resource-groups-add-group.png)
3. **[追加]** を選択します。
4. 次の値を入力します。

   - **サブスクリプション**:Azure サブスクリプションを選択します。 
   - **[リソース グループ]**:新しいリソース グループの名前を入力します。 
   - **[リージョン]**:**[米国中部]** など、Azure の場所を選択します。

     ![リソース グループの作成 ](./media/manage-resource-groups-portal/manage-resource-groups-create-group.png)
5. **[確認と作成]** を選択します
6. **作成**を選択します。 リソース グループの作成には数秒かかります。
7. 上部のメニューにある **[最新の情報に更新]** を選択してリソース グループの一覧を最新の状態に更新し、新しく作成されたリソース グループを選択してこれを開きます。 または、上部にある **[通知]** (ベルのアイコン) をクリックしてから、**[リソース グループに移動]** を選択して、新しく作成されたリソース グループを開きます

    ![リソース グループに移動](./media/manage-resource-groups-portal/manage-resource-groups-add-group-go-to-resource-group.png)

## <a name="list-resource-groups"></a>リソース グループの一覧を表示する

1. [Azure Portal](https://portal.azure.com) にサインインします。
2. リソース グループを表示するには、**[リソース グループ]** を選択します

    ![リソース グループの参照](./media/manage-resource-groups-portal/manage-resource-groups-list-groups.png)

3. リソース グループについて表示される情報をカスタマイズするには、**[列の編集]** を選択します。 次のスクリーンショットは、表示に追加できる追加列を示しています。

## <a name="open-resource-groups"></a>リソース グループを開く

1. [Azure Portal](https://portal.azure.com) にサインインします。
2. **[リソース グループ]** を選択します。
3. 開くリソース グループを選択します。

## <a name="delete-resource-groups"></a>リソース グループを削除する

1. 削除するリソース グループを開きます。  「[リソース グループを開く](#open-resource-groups)」を参照してください。
2. **[リソース グループの削除]** を選択します。

    ![Azure リソース グループを削除する](./media/manage-resource-groups-portal/delete-group.png)

Azure Resource Manager によってリソースの削除の順序が決定される方法の詳細については、「[Azure Resource Manager によるリソース グループの削除](./resource-group-delete.md)」を参照してください。

## <a name="deploy-resources-to-a-resource-group"></a>リソースをリソース グループにデプロイする

Resource Manager テンプレートを作成した後に、Azure portal を使用して Azure リソースをデプロイできます。 テンプレートの作成については、「[クイック スタート:Azure portal を使用した Azure Resource Manager テンプレートの作成とデプロイ](./resource-manager-quickstart-create-templates-use-the-portal.md)」を参照してください。 ポータルを使用したテンプレートのデプロイについては、「[Resource Manager テンプレートと Azure portal を使用したリソースのデプロイ](resource-group-template-deploy-portal.md)」を参照してください。

## <a name="move-to-another-resource-group-or-subscription"></a>別のリソース グループまたはサブスクリプションに移動する

グループ内のリソースを別のリソース グループに移動できます。 詳細については、「 [新しいリソース グループまたはサブスクリプションへのリソースの移動](resource-group-move-resources.md)」を参照してください。

## <a name="lock-resource-groups"></a>リソース グループをロックする

ロックを適用することで、組織の他のユーザーが重要なリソース (Azure サブスクリプション、リソース グループ、リソースなど) を誤って削除または変更するのを防ぐことができます。 

1. 削除するリソース グループを開きます。  「[リソース グループを開く](#open-resource-groups)」を参照してください。
2. 左側のウィンドウで、**[ロック]** を選択します。
3. ロックをリソース グループに追加するには、**[追加]** を選択します。
4. **[ロック名]**、**[ロックの種類]**、**[メモ]** を入力します。 ロックの種類には、**[読み取り専用]** および **[削除]** が含まれます。

    ![Azure リソース グループをロックする](./media/manage-resource-groups-portal/manage-resource-groups-add-lock.png)

詳細については、「[リソースのロックによる予期せぬ変更の防止](./resource-group-lock-resources.md)」を参照してください。

## <a name="tag-resource-groups"></a>リソース グループにタグを適用する

リソース グループやリソースにタグを適用して、アセットを論理的に整理できます。 詳細については、[タグを使用した Azure リソースの整理](./resource-group-using-tags.md#portal) に関する記事をご覧ください。

## <a name="export-resource-groups-to-templates"></a>リソース グループをテンプレートにエクスポートする

リソース グループを正常に設定したら、リソース グループの Resource Manager テンプレートを表示できます。 テンプレートのエクスポートには、2 つの利点があります。

- テンプレートに完全なインフラストラクチャが含まれているため、ソリューションの将来のデプロイを自動化します。
- ソリューションを表す JavaScript Object Notation (JSON) を調べることで、テンプレートの構文を確認できます。

テンプレートをエクスポートするには、次の 2 とおりの方法があります。

- デプロイに使用した実際のテンプレートをエクスポートできます。 エクスポートしたテンプレートには、元のテンプレートで定義されたのと同じパラメーターと変数がすべて含まれます。 この方法は、ポータル経由でリソースをデプロイ済みで、そのリソースの作成に使ったテンプレートを確認したい場合に便利です。 このテンプレートはすぐに使用できます。 
- リソース グループの現在の状態を表す生成済みのテンプレートをエクスポートできます。 エクスポートしたテンプレートは、デプロイに使用したテンプレートに基づいていません。 代わりに、リソース グループの "スナップショット" または "バックアップ" であるテンプレートが作成されます。 エクスポートしたテンプレートにはハードコーディングされた多くの値が含まれ、おそらく、通常定義するのと同程度のパラメーターは含まれません。 このオプションは、同じリソース グループにリソースを再デプロイする場合に使います。 別のリソース グループにこのテンプレートを使うには、大幅な変更が必要になる場合があります。

### <a name="export-templates-from-deployment-history"></a>デプロイ履歴からテンプレートをエクスポートする

この方法により、特定のデプロイ用のテンプレートがエクスポートされます。 ポータルを変更したり、複数のデプロイでリソースを追加または削除したりした場合は、「[リソース グループからテンプレートをエクスポートする](#export-templates-from-resource-groups)」を参照してください。

1. エクスポートするリソース グループを開きます。  「[リソース グループを開く](#open-resource-groups)」を参照してください。
2. 左側のウィンドウで、**[デプロイ]** を選択するか、**[デプロイ]** の下にあるリンクを選択します。  次のスクリーンショットでは、4 つの異なるデプロイ名の 4 つの別個のデプロイがあるため、**[4 Succeeded]** (4 成功しました) が示されています。 **[1 Succeeded]** (1 成功しました) が表示される場合があります。

    ![Azure のリソース グループのエクスポート テンプレート](./media/manage-resource-groups-portal/manage-resource-groups-export-templates-deployment-history.png)

3. 一覧からいずれかのデプロイを選択します。
4. 左側のウィンドウで、**[テンプレート]** を選択します。 Resource Manager によって、次の 6 つのファイルが取得されます。

   - **Template** - ソリューションのインフラストラクチャを定義するテンプレート。 ポータルでストレージ アカウントを作成したときに、Resource Manager はテンプレートを使用してそれをデプロイし、今後参照できるようにテンプレートを保存しました。
   - **Parameters** - デプロイ中に値を渡すために使用できるパラメーター ファイル。 初回デプロイ時に指定した値が含まれています。 これらの値はどれも、テンプレートを再デプロイするときに変更できます。
   - **CLI** - テンプレートをデプロイするために使用できる Azure CLI スクリプト ファイル。
   - **PowerShell** - テンプレートをデプロイするために使用できる Azure PowerShell スクリプト ファイル。
   - **.NET** - テンプレートをデプロイするために使用できる .NET クラス。
   - **Ruby** - テンプレートをデプロイするために使用できる Ruby クラス。

     テンプレートは、ポータルに既定で表示されます。

5. **[ダウンロード]** を選択し、ローカル コンピューターにテンプレートをエクスポートします。

    ![Azure のリソース グループのエクスポート テンプレート](./media/manage-resource-groups-portal/manage-resource-groups-export-templates-deployment-history-download.png)

<a name="export-templates-from-resource-groups"></a>
### <a name="export-templates-from-resource-groups"></a>リソース グループからテンプレートをエクスポートする

ポータルからリソースを変更したり、複数のデプロイでリソースを追加または削除したりした場合、デプロイ履歴からテンプレートを取得しても、リソース グループの現在の状態が反映されていません。 このセクションでは、リソース グループの現在の状態を反映したテンプレートをエクスポートする方法について説明します。 リソース グループのスナップショットとして意図されており、同じリソース グループへの再デプロイに使うことができます。 エクスポートされたテンプレートを他のソリューションに使うには、大幅な変更を行う必要があります。

1. エクスポートするリソース グループを開きます。  「[リソース グループを開く](#open-resource-groups)」を参照してください。
2. 左側のウィンドウで、**[テンプレートのエクスポート]** を選択します。 Resource Manager によって、次の 6 つのファイルが取得されます。

   - **Template** - ソリューションのインフラストラクチャを定義するテンプレート。 ポータルでストレージ アカウントを作成したときに、Resource Manager はテンプレートを使用してそれをデプロイし、今後参照できるようにテンプレートを保存しました。
   - **Parameters** - デプロイ中に値を渡すために使用できるパラメーター ファイル。 初回デプロイ時に指定した値が含まれています。 これらの値はどれも、テンプレートを再デプロイするときに変更できます。
   - **CLI** - テンプレートをデプロイするために使用できる Azure CLI スクリプト ファイル。
   - **PowerShell** - テンプレートをデプロイするために使用できる Azure PowerShell スクリプト ファイル。
   - **.NET** - テンプレートをデプロイするために使用できる .NET クラス。
   - **Ruby** - テンプレートをデプロイするために使用できる Ruby クラス。

     テンプレートは、ポータルに既定で表示されます。
3. **[ダウンロード]** を選択し、ローカル コンピューターにテンプレートをエクスポートします。

エクスポートされたテンプレートの中には、使用する前にいくらかの編集が必要なものがあります。 テンプレートを開発する方法については、「[ステップバイステップのチュートリアル](/azure/azure-resource-manager/)」を参照してください。

### <a name="export-template-before-deploying"></a>デプロイする前にテンプレートをエクスポートする

ポータルを使用して、リソースを定義することができます。  リソースをデプロイする前に、テンプレートを表示してエクスポートできます。 手順については、「[クイック スタート:Azure portal を使用した Azure Resource Manager テンプレートの作成とデプロイ](./resource-manager-quickstart-create-templates-use-the-portal.md)」を参照してください。

### <a name="fix-export-issues"></a>エクスポートの問題の修正

テンプレート関数のエクスポートは、すべてのリソースの種類でサポートされているわけではありません。 エクスポートの問題は、リソース グループからエクスポートする場合にのみ発生します。デプロイ履歴からのエクスポートでは発生しません。 最後のデプロイがリソース グループの現在の状態を正確に表しているようであれば、リソース グループからではなく、デプロイ履歴からテンプレートをエクスポートすることをお勧めします。 単一のテンプレートで定義されていない変更をリソース グループに加えた場合にのみ、リソース グループからエクスポートしてください。

エクスポートの問題を回避するには、欠けているリソースを対象のテンプレートに手動で追加します。 エラー メッセージには、エクスポートできないリソースの種類が含まれています。 そのリソースの種類は、[テンプレート リファレンス](/azure/templates/)で探してください。 たとえば、仮想ネットワーク ゲートウェイを手動で追加するには、[Microsoft.Network/virtualNetworkGateways テンプレート リファレンス](/azure/templates/microsoft.network/virtualnetworkgateways)を参照してください。 テンプレート リファレンスには、テンプレートにリソースを追加するための JSON が記載されています。

リソースに対応する JSON 形式を取得したら、次にリソースの値を取得する必要があります。 リソースの値は、リソースの種類に対応する REST API の GET 操作を使用して確認できます。 たとえば、仮想ネットワーク ゲートウェイの値を取得するには、「[Virtual Network Gateways - Get](/rest/api/network-gateway/virtualnetworkgateways/get)」(仮想ネットワーク ゲートウェイ - Get) を参照します。

## <a name="manage-access-to-resource-groups"></a>リソース グループへのアクセスを管理する

[ロールベースのアクセス制御 (RBAC)](../role-based-access-control/overview.md) は、Azure に存在するリソースに対するアクセス権を管理するための手法です。 詳細については、「[RBAC と Azure portal を使用してアクセスを管理する](../role-based-access-control/role-assignments-portal.md)」を参照してください。

## <a name="next-steps"></a>次の手順

- Azure Resource Manager については、「[Azure Resource Manager の概要](./resource-group-overview.md)」を参照してください。
- Resource Manager テンプレートの構文については、「[Azure Resource Manager テンプレートの構造と構文の詳細](./resource-group-authoring-templates.md)」を参照してください。
- テンプレートを開発する方法については、[ステップバイステップのチュートリアル](/azure/azure-resource-manager/)のページをご覧ください。
- Azure Resource Manager テンプレートのスキーマを表示するには、[テンプレート リファレンス](/azure/templates/)のページをご覧ください。