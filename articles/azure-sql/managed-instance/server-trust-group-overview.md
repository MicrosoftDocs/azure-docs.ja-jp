---
title: サーバー信頼グループ
titleSuffix: Azure SQL Managed Instance
description: サーバー信頼グループについて、および Azure SQL Managed Instance 間の信頼を管理する方法について説明します。
services: sql-database
ms.service: sql-managed-instance
ms.subservice: operations
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: sasapopo
ms.author: sasapopo
ms.reviewer: sstein, bonova
ms.date: 10/08/2020
ms.openlocfilehash: f9d5528746a85668677ab122d98e954bd39cd163
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "92790731"
---
# <a name="use-server-trust-groups-to-set-up-and-manage-trust-between-sql-managed-instances"></a>サーバー信頼グループを使用して SQL マネージド インスタンス間の信頼を設定および管理する
[!INCLUDE[appliesto-sqlmi](../includes/appliesto-sqlmi.md)]

サーバー信頼グループは、Azure SQL Managed Instance 間の信頼を管理するために使用される概念です。 グループを作成すると、そのメンバー間に証明書ベースの信頼が確立されます。 この信頼は、さまざまなクロスインスタンス シナリオに使用できます。 グループからサーバーを削除するか、グループを削除すると、サーバー間の信頼が解除されます。 サーバー信頼グループを作成または削除するには、マネージド インスタンスに対する書き込みアクセス許可が必要です。
[サーバー信頼グループ](/azure/templates/microsoft.sql/allversions)は、Azure portal で **[SQL trust groups]\(SQL 信頼グループ\)** としてラベル付けされた Azure Resource Manager オブジェクトです。

> [!NOTE]
> サーバー信頼グループは、Azure SQL Managed Instance 間の分散トランザクションのパブリック プレビューで導入されています。現在、この記事の後半で説明するいくつかの制限事項があります。

## <a name="server-trust-group-setup"></a>サーバー信頼グループの設定

次のセクションでは、サーバー信頼グループの設定について説明します。

1. [Azure ポータル](https://portal.azure.com/)にアクセスします。

2. 新しく作成したサーバー信頼グループに追加する予定の Azure SQL Managed Instance に移動します。

3. **[セキュリティ]** の設定で、 **[SQL trust groups]\(SQL 信頼グループ\)** タブを選択します。

   :::image type="content" source="./media/server-trust-group-overview/security-sql-trust-groups.png" alt-text="サーバー信頼グループ":::

4. サーバー信頼グループの構成ページで、 **[新しいグループ]** アイコンを選択します。

   :::image type="content" source="./media/server-trust-group-overview/server-trust-group-create-new-group.png" alt-text="新しいグループ":::

5. **[SQL trust groups]\(SQL 信頼グループ\)** の作成ブレードで、 **[グループ名]** を設定します。 これは、グループ メンバーが存在するすべてのリージョンで一意である必要があります。 **[Trust scope]\(信頼スコープ\)** は、サーバー信頼グループで有効になっているクロスインスタンス シナリオの種類を定義します。 プレビューでは、適用可能な唯一の信頼スコープは **[分散トランザクション]** であるため、事前に選択されており、変更することができません。 すべての **[グループ メンバー]** は同じ **[サブスクリプション]** に属している必要がありますが、異なるリソース グループに属することができます。 **[リソース グループ]** と **[SQL Server / instance]\(SQL Server/インスタンス\)** を選択して、グループのメンバーになる Azure SQL Managed Instance を選択します。

   :::image type="content" source="./media/server-trust-group-overview/server-trust-group-create-blade.png" alt-text="サーバー信頼グループの作成ブレード":::

6. すべての必須フィールドにデータが入力されたら、 **[保存]** をクリックします。

## <a name="server-trust-group-maintenance-and-deletion"></a>サーバー信頼グループのメンテナンスと削除

サーバー信頼グループを編集することはできません。 グループから Managed Instance を削除するには、グループを削除して新しいグループを作成する必要があります。

次のセクションでは、サーバー信頼グループの削除プロセスについて説明します。 
1. Azure Portal にアクセスします。
2. 信頼グループに属している Managed Instance に移動します。
3. **[セキュリティ]** の設定で、 **[SQL trust groups]\(SQL 信頼グループ\)** タブを選択します。
4. 削除する信頼グループを選択します。
   :::image type="content" source="./media/server-trust-group-overview/server-trust-group-manage-select.png" alt-text="サーバー信頼グループの選択":::
5. **[グループの削除]** をクリックします。
   :::image type="content" source="./media/server-trust-group-overview/server-trust-group-manage-delete.png" alt-text="サーバー信頼グループの削除":::
6. サーバー信頼グループ名を入力して削除を確認し、 **[削除]** をクリックします。
   :::image type="content" source="./media/server-trust-group-overview/server-trust-group-manage-delete-confirm.png" alt-text="サーバー信頼グループの削除の確認":::

> [!NOTE]
> サーバー信頼グループを削除しても、2 つの Managed Instance 間の信頼がすぐに削除されない場合があります。 信頼の削除は、Managed Instance の[フェールオーバー](/powershell/module/az.sql/Invoke-AzSqlInstanceFailover)を呼び出すことによって適用できます。 この最新の更新プログラムに関する「[既知の問題](../database/doc-changes-updates-release-notes.md?tabs=managed-instance#known-issues)」を確認してください。

## <a name="limitations"></a>制限事項

パブリック プレビューの間、サーバー信頼グループには次の制限事項が適用されます。
 * サーバー信頼グループの名前は、そのメンバーが属しているすべてのリージョンで一意である必要があります。
 * グループには、Azure SQL Managed Instance のみを含めることができ、同じ Azure サブスクリプションに属する必要があります。
 * プレビューでは、グループには厳密に 2 つの Managed Instance を含めることができます。 3 つ以上の Managed Instance 間で分散トランザクションを実行するには、Managed Instance の各ペアに対してサーバー信頼グループを作成する必要があります。
 * 分散トランザクションは、サーバー信頼グループに適用できる唯一のスコープです。
 * サーバー信頼グループは、Azure portal からのみ管理できます。 PowerShell と CLI は後ほどサポートされます。
 * Azure portal でサーバー信頼グループを編集することはできません。 作成または削除のみが可能です。
 * 分散トランザクションの追加の制限は、シナリオに関連している場合があります。 最も注目すべき点は、VNET または VNET ピアリングを介してプライベート エンドポイントで Managed Instance 間を接続する必要があることです。 [Managed Instance に関する現在の分散トランザクションの制限事項](../database/elastic-transactions-overview.md#limitations)を把握しておいてください。

## <a name="next-steps"></a>次のステップ

* Azure SQL Managed Instance での分散トランザクションの詳細については、[分散トランザクション](../database/elastic-transactions-overview.md)に関するページを参照してください。
* 更新プログラムのリリースと既知の問題の状態については、[Managed Instance のリリース ノート](../database/doc-changes-updates-release-notes.md)を参照してください。
* 機能に関するご要望がある場合は、[Managed Instance フォーラム](https://feedback.azure.com/forums/915676-sql-managed-instance)に追加してください。