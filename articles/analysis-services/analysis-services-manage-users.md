---
title: "Azure Analysis Services でのユーザーの管理 | Microsoft Docs"
description: "Azure の Analysis Services サーバーでユーザーを管理する方法について説明します。"
services: analysis-services
documentationcenter: 
author: minewiskan
manager: erikre
editor: 
tags: 
ms.assetid: 
ms.service: analysis-services
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: na
ms.date: 02/27/2016
ms.author: owend
translationtype: Human Translation
ms.sourcegitcommit: 194910a3e4cb655b39a64d2540994d90d34a68e4
ms.openlocfilehash: 039ed6f4be9f3e0f6b92e5a9f11e12392912df9d
ms.lasthandoff: 02/16/2017


---
# <a name="manage-users-in-azure-analysis-services"></a>Azure Analysis Services でのユーザーの管理
Azure Analysis Services のユーザーには、サーバー管理者とデータベース ユーザーの&2; 種類があります。 

## <a name="server-administrators"></a>サーバー管理者
Azure Portal のサーバー用コントロール ブレードで **[Analysis Services 管理者]** を使用するか、SSMS のサーバー プロパティを使用して、サーバー管理者を管理できます。 Analysis Services 管理者は、データベースの追加と削除やユーザーの管理などの一般的なデータベース管理タスクに対する権限を持つデータベース サーバー管理者です。 既定では、Azure Portal でサーバーを作成したユーザーは、自動的に Analysis Services 管理者として追加されます。

![Azure Portal のサーバー管理者](./media/analysis-services-manage-users/aas-manage-users-admins.png)

また、次のことを知っておく必要があります。

* Windows Live ID は、Azure の Analysis Services に対してサポートされる ID の種類ではありません。  
* Analysis Services 管理者は、有効な Azure Active Directory ユーザーである必要があります。
* Azure Resource Manager テンプレートを使って Azure Analysis Services サーバーを作る場合、Analysis Services 管理者は管理者として追加する必要のあるユーザーの JSON 配列を取得します。

Analysis Services 管理者は、Azure サブスクリプションのリソースを管理できる Azure リソース管理者とは別のユーザーでもかまいません。 これにより、Analysis Services での既存の XMLA および TMSL 管理動作との互換性が維持され、Azure リソース管理と Analysis Services データベース管理の責任を分離できます。 Azure Analysis Services リソースのすべてのロールとアクセスの種類を表示するには、コントロール ブレードの [アクセス制御 (IAM)] を使います。

### <a name="to-add-administrators-using-azure-portal"></a>Azure Portal を使用して管理者を追加するには
1. サーバーのコントロール ブレードで **[Analysis Services 管理者]** をクリックします。
2. **[\<サーバー名> - Analysis Services 管理者]** ブレードで、**[追加]** をクリックします。
3. **[Add Server Administrator (サーバー管理者の追加)]** ブレードで、追加するユーザー アカウントを選択します。

## <a name="database-users"></a>データベース ユーザー
データベース ユーザーは、データベース ロールに追加する必要があります。 ロールにより、同じアクセス許可を持つユーザーとグループがデータベースに対して定義されます。 既定では、表形式のモデル データベースには、読み取りアクセス許可が付与された既定のユーザー ロールが割り当てられています。 詳細については、[表形式モデルのロール](https://msdn.microsoft.com/library/hh213165.aspx)に関するページをご覧ください。

Azure Analysis Services のモデル データベースのユーザーは、"*Azure Active Directory に属している必要があります*"。 また、ユーザー名は、組織の電子メール アドレスまたは UPN で指定する必要があります。 これは、Windows ドメイン ユーザー名でユーザーをサポートするオンプレミスの表形式モデル データベースとは異なります。 

データベース ロールを作成したり、ユーザーとグループをロールに追加したり、行レベルをセキュリティを構成したりするには、SQL Server Data Tools (SSDT) または SQL Server Management Studio (SSMS) を使用します。 [Analysis Services PowerShell コマンドレット](https://msdn.microsoft.com/library/hh758425.aspx)や [Tabular Model Scripting Language](https://msdn.microsoft.com/library/mt614797.aspx) (TMSL) を使って、ユーザーを追加または削除することもできます。

**TMSL のサンプル スクリプト**

このサンプルでは、ユーザーとグループが、SalesBI データベースのユーザー ロールに追加されます。

```
{
  "createOrReplace": {
    "object": {
      "database": "SalesBI",
      "role": "Users"
    },
    "role": {
      "name": "Users",
      "description": "All allowed users to query the model",
      "modelPermission": "read",
      "members": [
        {
          "memberName": "user1@contoso.com",
          "identityProvider": "AzureAD"
        },
        {
          "memberName": "group1@contoso.com",
          "identityProvider": "AzureAD"
        }
      ]
    }
  }
}
```

## <a name="role-based-access-control-rbac"></a>ロール ベースのアクセス制御 (RBAC)

サブスクリプション管理者は、コントロール ブレードの **[アクセス制御 (IAM)]** を使用してロールを構成できます。 これは、上記の説明でサーバー レベルまたはデータベース レベルで構成されたサーバー管理者またはデータベース ユーザーとは異なります。 

![Azure Portal の [アクセス制御 (IAM)]](./media/analysis-services-manage-users/aas-manage-users-rbac.png)

ロールは、Portal で完了可能なタスクまたは Azure Resource Manager テンプレートを使用して完了可能なタスクを実行する必要のあるユーザーまたはアカウントに適用します。 詳しくは、「[ロールベースのアクセス制御](../active-directory/role-based-access-control-what-is.md)」をご覧ください。

## <a name="next-steps"></a>次のステップ
サーバーに表形式モデルをまだデプロイしていない場合は、ここで行うことをお勧めします。 詳しくは、「[Azure Analysis Services にデプロイする](analysis-services-deploy.md)」をご覧ください。

モデルをサーバーにデプロイしてある場合は、クライアントまたはブラウザーを使って接続できる状態になっています。 詳しくは、「[Get data from Azure Analysis Services server](analysis-services-connect.md)」(Azure Analysis Services サーバーからデータを取得する) をご覧ください。


