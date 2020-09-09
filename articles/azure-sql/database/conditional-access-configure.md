---
title: 条件付きアクセス
description: Azure SQL Database、Azure SQL Managed Instance、および Azure Synapse Analytics 用に条件付きアクセスを構成する方法を説明します。
titleSuffix: Azure SQL Database & SQL Managed Instance & Azure Synapse Analytics
services: sql-database
ms.service: sql-db-mi
ms.subservice: security
ms.topic: conceptual
author: GithubMirek
ms.author: mireks
ms.reviewer: vanto
ms.custom: sqldbrb=1
ms.date: 04/28/2020
tag: azure-synpase
ms.openlocfilehash: 67fef7b192caf064cd7c3b4b6af8923b153ee5b1
ms.sourcegitcommit: 93462ccb4dd178ec81115f50455fbad2fa1d79ce
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/06/2020
ms.locfileid: "85985348"
---
# <a name="conditional-access-with-azure-sql-database-and-azure-synapse-analytics"></a>Azure SQL Database と Azure Synapse Analytics での条件付きアクセス

[!INCLUDE[appliesto-sqldb-sqlmi-asa](../includes/appliesto-sqldb-sqlmi-asa.md)]

[Azure SQL Database](sql-database-paas-overview.md)、[Azure SQL Managed Instance](../managed-instance/sql-managed-instance-paas-overview.md)、および [Azure Synapse Analytics](../../synapse-analytics/sql-data-warehouse/sql-data-warehouse-overview-what-is.md) では、Microsoft の条件付きアクセスがサポートされています。

次の手順では、条件付きアクセス (CA) ポリシーを適用するように Azure SQL Database、SQL Managed Instance、または Azure Synapse を構成する方法を示します。  

## <a name="prerequisites"></a>前提条件

- Azure Active Directory (Azure AD) 認証をサポートするように Azure SQL Database、Azure SQL Managed Instance、または Azure Synapse の Azure SQL プールを構成する必要があります。 具体的な手順については、「[SQL Database または Azure Synapse で Azure Active Directory 認証を構成して管理する](authentication-aad-configure.md)」を参照してください。  
- 多要素認証が有効になっている場合は、最新の SQL Server Management Studio (SSMS) などのサポートされているツールに接続する必要があります。 詳細については、「[SQL Server Management Studio 用に Azure SQL Database の多要素認証を構成する](authentication-mfa-ssms-configure.md)」を参照してください。  

## <a name="configure-conditional-access"></a>条件付きアクセスを構成する

> [!NOTE]
> 次の例では Azure SQL Database を使用しますが、条件付きアクセスを構成する適切な製品を選択する必要があります。

1. Azure portal にサインインし、 **[Azure Active Directory]** を選択してから **[条件付きアクセス]** を選択します。 詳細については、「[Azure Active Directory の条件付きアクセスに関するテクニカル リファレンス](https://docs.microsoft.com/azure/active-directory/active-directory-conditional-access-technical-reference)」を参照してください。  
   ![[条件付きアクセス] ブレード](./media/conditional-access-configure/conditional-access-blade.png)

2. **[Conditional Access-Policies]\(条件付きアクセス ポリシー\)** ブレードで、 **[新しいポリシー]** をクリックし、名前を指定してから **[Configure rules]\(規則の構成\)** をクリックします。  
3. **[割り当て]** で、 **[ユーザーとグループ]** を選択し、 **[ユーザーとグループの選択]** をオンにしてから条件付きアクセス対象のユーザーまたはグループを選択します。 **[選択]** をクリックし、 **[完了]** をクリックして選択を確定します。  
   ![[ユーザーとグループの選択]](./media/conditional-access-configure/select-users-and-groups.png)  

4. **[クラウド アプリ]** を選択し、 **[アプリを選択]** をクリックします。 条件付きアクセスに使用可能なすべてのアプリが表示されます。 **[Azure SQL Database]** を選択し、下部にある **[選択]** をクリックしてから **[完了]** をクリックします。  
   ![SQL Database を選択する](./media/conditional-access-configure/select-sql-database.png)  
   3 番目のスクリーンショットに示されているように **[Azure SQL Database]** が表示されない場合は、以下の手順を実行します。
   - Azure AD 管理者アカウントで SSMS を使用して Azure SQL Database のデータベースに接続します。  
   - `CREATE USER [user@yourtenant.com] FROM EXTERNAL PROVIDER` を実行します。  
   - Azure AD にサインインし、Azure AD インスタンスのアプリケーションに Azure SQL Database、SQL Managed Instance、または Azure Synapse がリストされていることを確認します。  

5. **[アクセス制御]** を選択し、 **[許可]** を選択してから適用するポリシーを確認します。 この例では、 **[多要素認証が必要です]** を選択します。  
   ![アクセス権の付与を選択する](./media/conditional-access-configure/grant-access.png)  

## <a name="summary"></a>まとめ

これで、Azure AD Premium を使用して選択されたアプリケーション (Azure SQL Database) で、選択された条件付きアクセス ポリシー **[多要素認証が必要です]** が適用されるようになりました。

Azure SQL Database と Database and Azure Synapse での多要素認証に関するご質問は、<MFAforSQLDB@microsoft.com> にお問い合わせください。  

## <a name="next-steps"></a>次のステップ  

チュートリアルについては、[SQL Database のデータベースのセキュリティ保護](secure-database-tutorial.md)に関するページを参照してください。
