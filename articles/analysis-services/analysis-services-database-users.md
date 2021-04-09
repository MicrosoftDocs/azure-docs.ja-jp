---
title: Azure Analysis Services でのデータベース ロールとユーザーの管理 | Microsoft Docs
description: Azure の Analysis Services サーバーでデータベース ロールとユーザーを管理する方法について説明します。
author: minewiskan
ms.service: azure-analysis-services
ms.topic: conceptual
ms.date: 04/15/2020
ms.author: owend
ms.reviewer: minewiskan
ms.custom: references_regions
ms.openlocfilehash: 31910e92ba4d5cbb1f133eaff6880fafb809b772
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "99054095"
---
# <a name="manage-database-roles-and-users"></a>データベース ロールとユーザーの管理

model データベース レベルでは、すべてのユーザーがロールに属する必要があります。 ロールは、model データベースの特定の権限を持つユーザーを定義します。 ロールに追加されたユーザーまたはセキュリティ グループは、サーバーと同じサブスクリプションに Azure AD テナントのアカウントが必要です。 

ロールを定義する方法は、使用しているツールによって異なりますが、効果は同じです。

ロールのアクセス許可には次のものが含まれます。
*  **Administrator (管理者)** - ユーザーはデータベースの完全なアクセス許可があります。 管理者のアクセス許可を持つデータベース ロールは、サーバー管理者とは異なります。
*  **Process (プロセス)** - ユーザーは、データベースに接続し、データベースでのプロセス操作を実行し、model データベースのデータを分析できます。
*  **Read (読み取り)** - ユーザーは、クライアント アプリケーションを使用して model データベースに接続し、このデータを分析できます。

表形式のモデル プロジェクトを作成する場合、Analysis Services プロジェクトを使って Visual Studio 内でロール マネージャーを使用して、ロールを作成し、これらのロールにユーザーまたはグループを追加します。 サーバーにデプロイするとき、SQL Server Management Studio (SSMS)、[Analysis Services PowerShell コマンドレット](/analysis-services/powershell/analysis-services-powershell-reference)、または [Tabular Model Scripting Language](/analysis-services/tmsl/tabular-model-scripting-language-tmsl-reference) (TMSL) を使用して、ロールおよびユーザーのメンバーを追加または削除します。

**セキュリティ グループ** を追加するときは、`obj:groupid@tenantid` を使用します。

**サービス プリンシパル** を追加するときは、`app:appid@tenantid` を使用します。

## <a name="to-add-or-manage-roles-and-users-in-visual-studio"></a>Visual Studio でロールおよびユーザーを追加または管理する方法  
  
1.  **Tabular Model Explorer** で **[Roles]\(ロール\)** を右クリックします｡  
  
2.  **[ロール マネージャー]** で、 **[新規]** をクリックします。  
  
3.  ロールの名前を入力します。  
  
     既定では、既定のロールの名前は、新しいロールごとに増分の番号が付けられます。 Finance Managers(ファイナンス マネージャー) や Human Resources Specialists(人事担当) など、メンバーの種類を明確に識別する名前を入力するをお勧めします。  
  
4.  次のアクセス許可のいずれかを選択します。  
  
    |権限|説明|  
    |----------------|-----------------|  
    |**なし**|メンバーは、モデル スキーマの読み取りまたは変更はできず、データのクエリもできません。|  
    |**読み取り**|メンバーは、(行フィルターに基づいて) データをクエリできますが、モデル スキーマを変更することはできません。|  
    |**読み取りと処理**|メンバーは、(行レベル フィルターに基づいて) データをクエリし、プロセス操作および Process All (すべてプロセス) 操作を実行できますが、モデル スキーマを変更することはできません。|  
    |**処理**|メンバーは、処理およびすべて処理の各操作を実行できます。 モデル スキーマの読み取りまたは変更はできず、データのクエリもできません。|  
    |**管理者**|メンバーは、モデル スキーマの変更とすべてのデータのクエリを実行できます。|   
  
5.  作成しているロールに「Read (読み取り)」または「Read and Process (読み取りとプロセス)」のアクセス許可がある場合、DAX 数式を使用して行フィルターを追加できます。 **[Row Filters(行フィルター)]** タブをクリックし、テーブルを選択してから、 **[DAX Filter(DAX フィルター)]** フィールドをクリックし、DAX 数式を入力します。
  
6.  **[Members(メンバー)]**  >  **[Add External(外部を追加)]** の順にクリックします。  
  
8.  **[Add External Member(外部メンバーの追加)]** で、Azure AD のテナントにユーザーまたはグループを電子メール アドレスごとに入力します。 [OK] をクリックし、ロール マネージャーを終了した後、ロールおよびロール メンバーが Tabular Model Explorer に表示されます。 
 
     ![Tabular Model Explorer のロールとユーザー](./media/analysis-services-database-users/aas-roles-tmexplorer.png)

9. Azure Analysis Services サーバーにデプロイします。


## <a name="to-add-or-manage-roles-and-users-in-ssms"></a>SSMS でロールおよびユーザーを追加または管理する方法

デプロイ済みの model データベースにロールとユーザーを追加するには、サーバー管理者としてサーバーに接続しているか、または管理者アクセス許可があるデータベース ロールでサーバーに既に接続している必要があります。

1. Object Exporer で、 **[Roles(ロール)]**  >  **[New Role(新規ロール)]** の順に右クリックします。

2. **[Create Role(ロールの作成)]** で、ロールの名前と説明を入力します。

3. アクセス許可を選択します。

   |権限|説明|  
   |----------------|-----------------|  
   |**フル コントロール (管理者)**|メンバーは、モデル スキーマの変更とプロセス、およびすべてのデータのクエリを実行できます。| 
   |**データベースの処理**|メンバーは、処理およびすべて処理の各操作を実行できます。 モデル スキーマを変更することはできませんし、データをクエリすることもできません。|  
   |**読み取り**|メンバーは、(行フィルターに基づいて) データをクエリできますが、モデル スキーマを変更することはできません。|  
  
4. **[Membership(メンバーシップ)]** をクリックし、Azure AD のテナントにユーザーまたはグループを電子メール アドレスごとに入力します。

     ![ユーザーの追加](./media/analysis-services-database-users/aas-roles-adduser-ssms.png)

5. 作成しているロールに「Read (読み取り)」のアクセス許可がある場合、DAX 数式を使用して行フィルターを追加できます。 **[Row Filters(行フィルター)]** をクリックし、テーブルを選択してから、 **[DAX Filter(DAX フィルター)]** フィールドに DAX 数式を入力します。 

## <a name="to-add-roles-and-users-by-using-a-tmsl-script"></a>TMSL スクリプトを使用して、ロールとユーザーを追加する方法

SSMS の XMLA ウィンドウで、または PowerShell を使用して、TMSL スクリプトを実行できます。 [CreateOrReplace](/analysis-services/tmsl/createorreplace-command-tmsl) コマンドおよび [Roles(ロール)](/analysis-services/tmsl/roles-object-tmsl) オブジェクトを使用します。

**TMSL のサンプル スクリプト**

このサンプルでは、B2B 外部ユーザーとグループは、SalesBI データベースに対する Read(読み取り) アクセス許可を持つ Analyst (アナリスト) ロールに追加されます。 外部ユーザーとグループの両方が Azure AD の同じテナントに存在する必要があります。

```
{
  "createOrReplace": {
    "object": {
      "database": "SalesBI",
      "role": "Analyst"
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
          "memberName": "group1@adventureworks.com",
          "identityProvider": "AzureAD"
        }
      ]
    }
  }
}
```

## <a name="to-add-roles-and-users-by-using-powershell"></a>PowerShell を使用してロールとユーザーを追加する方法

[SqlServer](/analysis-services/powershell/analysis-services-powershell-reference) モジュールには、タスク固有のデータベース管理コマンドレットと、Tabular Model Scripting Language (TMSL) クエリまたはスクリプトを受け入れる汎用 Invoke-ASCmd コマンドレットが用意されています。 次のコマンドレットは、データベース ロールとユーザーを管理するために使用されます。
  
|コマンドレット|説明|
|------------|-----------------| 
|[Add-RoleMember](/powershell/module/sqlserver/Add-RoleMember)|データベース ロールにメンバーを追加します。| 
|[Remove-RoleMember](/powershell/module/sqlserver/remove-rolemember)|データベース ロールからメンバーを削除します。|   
|[Invoke-ASCmd](/powershell/module/sqlserver/invoke-ascmd)|TMSL スクリプトを実行します。|

## <a name="row-filters"></a>行フィルター  

行フィルターは、特定のロールのメンバーがクエリできるテーブル内の行を定義します。 行フィルターは、DAX 式を使用してモデル内の各テーブルに対して定義されます。  
  
行フィルターは、Read (読み取り) アクセス許可および Read and Process (読み取りとプロセス) アクセス許可を持つロールに対してのみ定義できます。 既定では、行フィルターが特定のテーブルに対して定義されていない場合、別のテーブルからクロス フィルターが適用されていない限り、メンバーはテーブル内のすべての行をクエリできます。
  
 行フィルターは、その特定のロールのメンバーがクエリできる行を定義するために、TRUE または FALSE の値に評価される必要がある DAX 数式が必要です。 DAX 数式に含まれていない行はクエリできません。 たとえば、行フィルター式 *=Customers [Country] = "USA"* がある Customers テーブルでは、Sales (セールス) ロールのメンバーは米国のお客様のみを表示できます。  
  
行フィルターは、指定した行と関連する行に適用されます。 1 つのテーブルに複数のリレーションシップがある場合、フィルターによりセキュリティがアクティブなリレーションシップに適用されます。 行フィルターは、関連するテーブルに対して定義されたその他の行フィルターと交差します。次に例を示します。  
  
|テーブル|DAX 式|  
|-----------|--------------------|  
|リージョン|=Region[Country]="USA"|  
|ProductCategory|=ProductCategory[Name]="Bicycles"|  
|トランザクション|=Transactions[Year]=2016|  
  
 実際の効果は、メンバーがクエリできるのは、お客様が米国に住んでいて、製品カテゴリが自転車で、2016 年のデータの行です。 ユーザーは、米国外のトランザクション、自転車以外のトランザクション、2016 年以外のトランザクションをクエリできません。ただし、これらのアクセス許可が付与されている別のロールのメンバーはこの制約を受けません。
  
 フィルター *=FALSE()* を使用して、テーブル全体のすべての行へのアクセスを拒否できます。

## <a name="next-steps"></a>次のステップ

  [サーバー管理者の管理](analysis-services-server-admins.md)   
  [PowerShell で Azure Analysis Services を管理する](analysis-services-powershell.md)  
  [Tabular Model Scripting Language (TMSL) のリファレンス](/analysis-services/tmsl/tabular-model-scripting-language-tmsl-reference)
