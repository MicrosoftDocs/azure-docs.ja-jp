---
title: "Azure RBAC のカスタム ロールの作成 | Microsoft Docs"
description: "Azure サブスクリプションのきめ細かい ID 管理を実現するために Azure のロールベースのアクセス制御でカスタム ロールを定義する方法について説明します。"
services: active-directory
documentationcenter: 
author: kgremban
manager: femila
editor: 
ms.assetid: e4206ea9-52c3-47ee-af29-f6eef7566fa5
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 02/21/2017
ms.author: kgremban
ms.custom: H1Hack27Feb2017
translationtype: Human Translation
ms.sourcegitcommit: 53fa0876ce1e3e2b2ac47316f37c5a0de2591d41
ms.openlocfilehash: c8189af2ebd9cabfa3e20e6b3520ba347d6147ff
ms.lasthandoff: 02/28/2017

---
# <a name="create-custom-roles-for-azure-role-based-access-control"></a>Azure のロールベースのアクセス制御のためのカスタム ロールを作成する
組み込みのロールの中にアクセス権に関する特定の要件を満たすものがない場合は、Azure のロールベースのアクセス制御 (RBAC) でカスタム ロールを作成できます。 カスタム ロールは、[Azure PowerShell](role-based-access-control-manage-access-powershell.md)、[Azure コマンドライン インターフェイス](role-based-access-control-manage-access-azure-cli.md) (CLI)、および [REST API](role-based-access-control-manage-access-rest.md) で作成することができます。 組み込みのロールと同様、カスタム ロールは、ユーザー、グループ、アプリケーションに対して、サブスクリプション、リソース グループ、リソースのスコープで割り当てることができます。 カスタム ロールは Azure AD テナントで保存され、そのテナントをサブスクリプションの Azure AD ディレクトリとして使用するすべてのサブスクリプションで共有することができます。

以下は、仮想マシンの監視と再起動を行うためのカスタム ロールの例です。

```
{
  "Name": "Virtual Machine Operator",
  "Id": "cadb4a5a-4e7a-47be-84db-05cad13b6769",
  "IsCustom": true,
  "Description": "Can monitor and restart virtual machines.",
  "Actions": [
    "Microsoft.Storage/*/read",
    "Microsoft.Network/*/read",
    "Microsoft.Compute/*/read",
    "Microsoft.Compute/virtualMachines/start/action",
    "Microsoft.Compute/virtualMachines/restart/action",
    "Microsoft.Authorization/*/read",
    "Microsoft.Resources/subscriptions/resourceGroups/read",
    "Microsoft.Insights/alertRules/*",
    "Microsoft.Insights/diagnosticSettings/*",
    "Microsoft.Support/*"
  ],
  "NotActions": [

  ],
  "AssignableScopes": [
    "/subscriptions/c276fc76-9cd4-44c9-99a7-4fd71546436e",
    "/subscriptions/e91d47c4-76f3-4271-a796-21b4ecfe3624",
    "/subscriptions/34370e90-ac4a-4bf9-821f-85eeedeae1a2"
  ]
}
```
## <a name="actions"></a>アクション
カスタム ロールを通じてアクセス権を付与する Azure の操作は、ロールの **Actions** プロパティで指定します。 このプロパティに文字列で指定された一連の操作によって、Azure リソース プロバイダーのセキュリティ保護可能な操作が識別されます。 操作文字列は、`Microsoft.<ProviderName>/<ChildResourceType>/<action>` の形式に従います。 操作文字列にワイルドカード (\*) を指定すると、その文字列と一致するすべての操作にアクセス権が与えられます。 次に例を示します。

* `*/read` は、すべての Azure リソース プロバイダーの全リソース タイプを対象に読み取り操作のアクセス権を付与します。
* `Microsoft.Compute/*` は、Microsoft.Compute リソース プロバイダーのすべてのリソース タイプに対するすべての操作のアクセス権を付与します。
* `Microsoft.Network/*/read` は、Azure の Microsoft.Network リソース プロバイダーの全リソース タイプを対象に読み取り操作のアクセス権を付与します。
* `Microsoft.Compute/virtualMachines/*` は、Virtual Machines とその子リソース タイプを対象にすべての操作のアクセス権を付与します。
* `Microsoft.Web/sites/restart/Action` は、Web サイトを再起動するためのアクセス権を付与します。

Azure リソース プロバイダーの操作を一覧表示するには、`Get-AzureRmProviderOperation` (PowerShell の場合) または `azure provider operations show` (Azure CLI の場合) を使用します。 これらのコマンドを使って、操作文字列が有効であるかどうかを確認したり、操作文字列のワイルドカードを展開した結果を表示したりすることもできます。

```
Get-AzureRMProviderOperation Microsoft.Compute/virtualMachines/*/action | FT Operation, OperationName

Get-AzureRMProviderOperation Microsoft.Network/*
```

![PowerShell のスクリーンショット - Get-AzureRMProviderOperation](./media/role-based-access-control-configure/1-get-azurermprovideroperation-1.png)

```
azure provider operations show "Microsoft.Compute/virtualMachines/*/action" --js on | jq '.[] | .operation'

azure provider operations show "Microsoft.Network/*"
```

![Azure CLI screenshot - azure provider operations show "Microsoft.Compute/virtualMachines/\*/action" ](./media/role-based-access-control-configure/1-azure-provider-operations-show.png)

## <a name="notactions"></a>NotActions
制限対象の操作を除外する方が、許可する操作セットを容易に定義できる場合は、 **NotActions** プロパティを使用します。 カスタム ロールによって実際に付与されるアクセス権は、**Actions** の操作から **NotActions** の操作を差し引くことで算出されます。

> [!NOTE]
> **NotActions**で特定の操作を除外したロールをユーザーに割り当てたうえで、同じユーザーにその操作へのアクセス権を付与する別のロールを割り当てた場合、ユーザーはその操作の実行が許可されます。 **NotActions** は拒否ルールとは異なり、特定の操作を除外する必要があるときに、許可の対象となる一連の操作を指定しやすくすることを目的としたものに過ぎません。
>
>

## <a name="assignablescopes"></a>AssignableScopes
カスタム ロールの **AssignableScopes** プロパティでは、割り当てにカスタム ロールを利用できるスコープ (サブスクリプション、リソース グループ、リソースのいずれか) を指定します。 カスタム ロールを必要とするサブスクリプションやリソース グループのみに割り当てを限定し、それ以外のサブスクリプションやリソース グループについては元のユーザー エクスペリエンスを保ち、不要な混乱を避けることができます。

有効な AssignableScopes の例を次に示します。

* "/subscriptions/c276fc76-9cd4-44c9-99a7-4fd71546436e"、"/subscriptions/e91d47c4-76f3-4271-a796-21b4ecfe3624" - 対象となるロールの割り当てを&2; つのサブスクリプションに許可します。
* "/subscriptions/c276fc76-9cd4-44c9-99a7-4fd71546436e" - 対象となるロールの割り当てを&1; つのサブスクリプションに許可します。
* "/subscriptions/c276fc76-9cd4-44c9-99a7-4fd71546436e/resourceGroups/Network" - 対象となるロールの割り当てを Network リソース グループのみに許可します。

> [!NOTE]
> 少なくとも&1; つのサブスクリプション、リソース グループ、またはリソース ID を使用する必要があります。
>
>

## <a name="custom-roles-access-control"></a>カスタム ロールのアクセス制御
カスタム ロールの **AssignableScopes** プロパティは、そのロールをだれが表示、変更、削除できるかという点も制御することができます。

* カスタム ロールを作成できるユーザー:
    サブスクリプション、リソース グループ、リソースの所有者 (およびユーザー アクセス管理者) は、それぞれのスコープで使用するカスタム ロールを作成することができます。
    ロールを作成するユーザーは、ロールのすべての **AssignableScopes** に対して `Microsoft.Authorization/roleDefinition/write` 操作を実行できる必要があります。
* カスタム ロールに変更を加えることができるユーザー:
    サブスクリプション、リソース グループ、リソースの所有者 (およびユーザー アクセス管理者) は、それぞれのスコープでカスタム ロールに変更を加えることができます。 ユーザーは、カスタム ロールのすべての **AssignableScopes** に対して `Microsoft.Authorization/roleDefinition/write` 操作を実行できる必要があります。
* カスタム ロールを表示できるユーザー: 
    Azure RBAC の組み込みロールについてはいずれも、割り当て可能なロールの表示対象として許可されています。 特定のスコープで `Microsoft.Authorization/roleDefinition/read` 操作を実行できるユーザーが、そのスコープで割り当て可能な RBAC ロールを表示できます。

## <a name="see-also"></a>関連項目
* [ロールベースのアクセス制御](role-based-access-control-configure.md): Azure ポータルでの RBAC の基本について説明します。
* 次の要素を使用したアクセス管理方法の詳細
  * [PowerShell](role-based-access-control-manage-access-powershell.md)
  * [Azure CLI](role-based-access-control-manage-access-azure-cli.md)
  * [REST API](role-based-access-control-manage-access-rest.md)
* [組み込みのロール](role-based-access-built-in-roles.md): RBAC の標準ロールの詳細について説明します。

