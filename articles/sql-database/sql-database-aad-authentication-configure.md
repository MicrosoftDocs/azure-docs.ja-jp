---
title: "Azure Active Directory 認証を構成する - SQL | Microsoft Docs"
description: "Azure Active Directory 認証を使って SQL Database と SQL Data Warehouse に接続する方法について説明します。"
services: sql-database
documentationcenter: 
author: BYHAM
manager: jhubbard
editor: 
tags: 
ms.assetid: 7e2508a1-347e-4f15-b060-d46602c5ce7e
ms.service: sql-database
ms.custom: authentication and authorization
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: data-management
ms.date: 01/23/2017
ms.author: rickbyh
translationtype: Human Translation
ms.sourcegitcommit: 2c13daf84727a500a2ea6a3dc1d4968c9824e223
ms.openlocfilehash: d7eef61b28d63b794235d82fdbbabb13b4cd3372
ms.lasthandoff: 02/16/2017


---
# <a name="configure-and-manage-azure-active-directory-authentication-with-sql-database-or-sql-data-warehouse"></a>SQL Database または SQL Data Warehouse で Azure Active Directory 認証を構成して管理する

この記事では、Azure AD を作成して設定した後、Azure SQL Database と SQL Data Warehouse で Azure AD を使用する方法を示します。 概要については、「[Azure Active Directory 認証](sql-database-aad-authentication.md)」を参照してください。

## <a name="create-and-populate-an-azure-ad"></a>Azure AD を作成して設定する
Azure AD を作成し、ユーザーとグループを設定します。 Azure AD は初期ドメインの Azure AD の管理対象ドメインにすることができます。 また、Azure AD とフェデレーションされるオンプレミスの Active Directory Domain Services にすることもできます。

詳細については、「[オンプレミス ID と Azure Active Directory の統合](../active-directory/active-directory-aadconnect.md)」、[Azure AD への独自のドメイン名の追加](../active-directory/active-directory-add-domain.md)に関するページ、「[Microsoft Azure now supports federation with Windows Server Active Directory](https://azure.microsoft.com/blog/2012/11/28/windows-azure-now-supports-federation-with-windows-server-active-directory/)」(Microsoft Azure が Windows Server Active Directory とのフェデレーションに対応)、「[Azure AD ディレクトリの管理](https://msdn.microsoft.com/library/azure/hh967611.aspx)」、[Windows PowerShell を使用した Azure AD の管理](https://msdn.microsoft.com/library/azure/jj151815.aspx)に関するページ、および「[Hybrid Identity Required Ports and Protocols](../active-directory/active-directory-aadconnect-ports.md)」(ハイブリッド ID の必須ポートとプロトコル) を参照してください。

## <a name="optional-associate-or-change-the-active-directory-that-is-currently-associated-with-your-azure-subscription"></a>省略可能: Active Directory を関連付けるか、現在 Azure サブスクリプションに関連付けられている Active Directory を変更する
データベースを組織の Azure AD ディレクトリに関連付けるには、そのディレクトリを、データベースをホストする Azure サブスクリプションの信頼できるディレクトリにします。 詳細については、 [Azure サブスクリプションを Azure AD に関連付ける方法](https://msdn.microsoft.com/library/azure/dn629581.aspx)を参照してください。

**追加情報:** すべての Azure サブスクリプションには、Azure AD インスタンスとの信頼関係があります。 つまり、ディレクトリを信頼してユーザー、サービス、デバイスを認証します。 複数のサブスクリプションが同じディレクトリを信頼できますが、1 つのサブスクリプションは&1; つのディレクトリだけを信頼します。 サブスクリプションが信頼しているディレクトリは、[https://manage.windowsazure.com/](https://manage.windowsazure.com/) の **[設定]** タブで確認できます。 このサブスクリプションとディレクトリの間の信頼関係は、サブスクリプションと Azure 内の他のすべてのリソース (Web サイト、データベースなど) の間の関係と異なります。後者は、サブスクリプションの子リソースにより近いものです。 サブスクリプションの有効期限が切れた場合、サブスクリプションに関連付けられたこれらの他のリソースへのアクセスも停止します。 一方、ディレクトリは Azure 内に残っており、別のサブスクリプションをそのディレクトリと関連付けて、ディレクトリ ユーザーの管理を継続できます。 リソースの詳細については、[Azure でのリソース アクセスの説明](https://msdn.microsoft.com/library/azure/dn584083.aspx)に関するページを参照してください。

次の手順は、特定のサブスクリプションの関連付けられたディレクトリを変更する方法を示しています。
1. Azure サブスクリプションの管理者を使用して [Azure クラシック ポータル](https://manage.windowsazure.com/) に接続します。
2. 左側のバナーで **[設定]**を選択します。
3. [設定] 画面にサブスクリプションが表示されます。 目的のサブスクリプションが表示されていない場合は、上部にある **[サブスクリプション]** をクリックし、**[ディレクトリでフィルター]** ボックスの一覧でサブスクリプションが含まれるディレクトリを選択して、**[適用]** をクリックします。
   
    ![サブスクリプションを選択する][4]
4. **[設定]** 領域でサブスクリプションをクリックし、ページの下部にある **[ディレクトリの編集]** をクリックします。
   
    ![ad-settings-portal][5]
5. **[ディレクトリの編集]** ボックスで、SQL Server または SQL Data Warehouse に関連付けられている Azure Active Directory を選択し、矢印をクリックして次に進みます。
   
    ![edit-directory-select][6]
6. **[ディレクトリ マッピングの確認]** ダイアログ ボックスで、"**共同管理者は全員削除されます。**" と表示されていることを確認します。
   
    ![edit-directory-confirm][7]
7. チェック マークをクリックしてポータルを再度読み込みます。

   > [!NOTE]
   > ディレクトリを変更すると、共同管理者、Azure AD のユーザーとグループ、ディレクトリでサポートされているリソース ユーザーすべてへのアクセスが削除され、それらはこのサブスクリプションまたはそのリソースにアクセスできなくなります。 サービス管理者のみ、新しいディレクトリに基づくプリンシパルのアクセスを構成できます。 この変更は、すべてのリソースに反映されるまで相当な時間がかかる場合があります。 ディレクトリを変更すると、SQL Database および SQL Data Warehouse の Azure AD 管理者も変更されるため、既存のすべての Azure AD ユーザーはデータベースにアクセスできなくなります。 Azure AD の管理者を再設定し (後で説明)、新しい Azure AD ユーザーを作成する必要があります。
   >  

## <a name="create-an-azure-ad-administrator-for-azure-sql-server"></a>Azure SQL Server の Azure AD 管理者を作成する
(SQL Database または SQL Data Warehouse をホストする) 各 Azure SQL Server は、Azure SQL Server 全体の管理者である&1; つのサーバー管理者アカウントから開始します。 Azure AD アカウントであるもう&1; つの SQL Server 管理者を作成する必要があります。 このプリンシパルは、master データベースの包含データベース ユーザーとして作成されます。 管理者の場合、サーバー管理者アカウントは、すべてのユーザー データベースの **db_owner** ロールのメンバーであり、**dbo** ユーザーとして各ユーザー データベースに入ります。 サーバー管理者アカウントについて詳しくは、「[Azure SQL Database におけるデータベースとログインの管理](sql-database-manage-logins.md)」をご覧ください。

geo レプリケーションで Azure Active Directory を使用する場合は、プライマリ サーバーとセカンダリ サーバーの両方で Azure Active Directory 管理者を構成する必要があります。 サーバーに Azure Active Directory 管理者がいない場合、Azure Active Directory へのログイン時に、"接続できません" サーバー エラーが発生します。

> [!NOTE]
> Azure AD アカウント (Azure SQL Server の管理者アカウントを含みます) に基づいていないユーザーは、Azure AD に基づくユーザーを作成できません。これは、Azure AD で提示されるデータベース ユーザーを検証するアクセス許可がないためです。
> 

## <a name="provision-an-azure-active-directory-administrator-for-your-azure-sql-server"></a>Azure SQL Server の Azure Active Directory 管理者をプロビジョニングします

次の&2; つの手順は、Azure Portal と PowerShell を使用して、Azure SQL Server の Azure Active Directory 管理者をプロビジョニングする方法を示しています。

### <a name="azure-portal"></a>Azure Portal
1. [Azure Portal](https://portal.azure.com/) の右上隅にあるユーザー アイコンをクリックすると、Active Directory 候補の一覧がドロップダウンで表示されます。 既定の Azure AD として適切な Active Directory を選択します。 この手順では、Active Directory とサブスクリプションの関連付けを Azure SQL Server とリンクすることで、Azure AD と SQL Server の両方に同じサブスクリプションが使用されるようにします  (Azure SQL Server は、Azure SQL Database または Azure SQL Data Warehouse をホストしている可能性があります)。
   
    ![choose-ad][8]
2. 左側のバナーで **[SQL Server]** を選択し、使用する **SQL Server** を選択した後、**[SQL Server]** ブレードの上部にある **[設定]** をクリックします。
   
    ![ad settings][9]
3. **[設定]** ブレードで **[Active Directory 管理者]** をクリックします。
4. **[Active Directory 管理者]** ブレードで、**[Active Directory 管理者]** をクリックし、上部にある **[管理者の設定]** をクリックします。
5. **[管理者の追加]** ブレードで、ユーザーを検索し、管理者にするユーザーまたはグループを選択してから **[選択]** をクリックします。 [Active Directory 管理者] ブレードには、Active Directory のメンバーとグループがすべて表示されます。 淡色表示されているユーザーまたはグループは、Azure AD 管理者としてサポートされていないため選択できません (サポートされている管理者の一覧については、上記の「**Azure AD の機能と制限事項**」を参照してください)。ロール ベースのアクセス制御 (RBAC) はポータルにのみ適用され、SQL Server には反映されません。
6. **[Active Directory 管理者]** ブレードの上部にある **[保存]** をクリックします。

    ![choose admin][10]
   
    管理者を変更する処理には数分かかる場合があります。 処理が完了すると、 **[Active Directory 管理者]** ボックスに新しい管理者が表示されます。

   > [!NOTE]
   > Azure AD 管理者をセットアップする場合、新しい管理者名 (ユーザーまたはグループ) が SQL Server 認証ユーザーとして仮想マスター データベースに既に存在していてはいけません。 存在する場合、Azure AD 管理者のセットアップは失敗します。その作成をロールバックされ、そのような管理者 (名前) が既に存在していることが示されます。 そのような SQL Server 認証ユーザーは Azure AD に属していないため、Azure AD 認証を使用してサーバーに接続しようとしても失敗します。
   > 


後で管理者を削除するには、**[Active Directory 管理者]** ブレードの上部にある **[管理者の削除]** をクリックし、**[保存]** をクリックします。

### <a name="powershell"></a>PowerShell
PowerShell コマンドレットを実行するには、Azure PowerShell をインストールし、実行している必要があります。 詳細については、「 [Azure PowerShell のインストールと構成の方法](/powershell/azureps-cmdlets-docs)」をご覧ください。

Azure AD 管理者をプロビジョニングするには、次のような Azure PowerShell コマンドを実行する必要があります。

* Add-AzureRmAccount
* Select-AzureRmSubscription

Azure AD 管理者のプロビジョニングと管理に使用するコマンドレットは、次のとおりです。

| コマンドレット名 | Description |
| --- | --- |
| [Set-AzureRMSqlServerActiveDirectoryAdministrator](https://msdn.microsoft.com/library/azure/mt603544.aspx) |Azure SQL Server または Azure SQL Data Warehouse の Azure Active Directory 管理者をプロビジョニングします  (現在のサブスクリプションから実行する必要があります)。 |
| [Remove-AzureRMSqlServerActiveDirectoryAdministrator](https://msdn.microsoft.com/library/azure/mt619340.aspx) |Azure SQL Server または Azure SQL Data Warehouse の Azure Active Directory 管理者を削除します。 |
| [Get-AzureRMSqlServerActiveDirectoryAdministrator](https://msdn.microsoft.com/library/azure/mt603737.aspx) |現在 Azure SQL Server または Azure SQL Data Warehouse 用に構成されている Azure Active Directory 管理者に関する情報を返します。 |

これらの各コマンドの詳細を確認するには、 ``get-help Set-AzureRmSqlServerActiveDirectoryAdministrator``のように PowerShell コマンドの get-help を使用します。

次のスクリプトでは、**Group-23** という名前のリソース グループ内にあるサーバー **demo_server** に対して、**DBA_Group** という名前の Azure AD 管理者グループ (オブジェクト ID `40b79501-b343-44ed-9ce7-da4c8cc7353f`) をプロビジョニングします。

```
Set-AzureRmSqlServerActiveDirectoryAdministrator -ResourceGroupName "Group-23"
-ServerName "demo_server" -DisplayName "DBA_Group"
```

**DisplayName** 入力パラメーターには、Azure AD の表示名またはユーザー プリンシパル名を使用できます。 たとえば、``DisplayName="John Smith"`` や ``DisplayName="johns@contoso.com"``す。 Azure AD グループの場合は、Azure AD の表示名のみがサポートされています。

> [!NOTE]
> Azure PowerShell コマンド ```Set-AzureRmSqlServerActiveDirectoryAdministrator``` では、サポートされていないユーザーに対する Azure AD 管理者のプロビジョニングは禁止されていません。 サポートされていないユーザーのプロビジョニングは可能ですが、このようなユーザーはデータベースに接続できません 
> 

次の例では、オプションとして **ObjectID**を使用します。

```
Set-AzureRmSqlServerActiveDirectoryAdministrator -ResourceGroupName "Group-23"
-ServerName "demo_server" -DisplayName "DBA_Group" -ObjectId "40b79501-b343-44ed-9ce7-da4c8cc7353f"
```

> [!NOTE]
> **DisplayName** が一意でない場合は、Azure AD の **ObjectID** が必要です。 **ObjectID** と **DisplayName** の値を取得するには、Azure クラシック ポータルの [Active Directory] セクションを使用して、ユーザーまたはグループのプロパティを確認します。
> 

次の例では、Azure SQL Server の現在の Azure AD 管理者に関する情報が返されます。

```
Get-AzureRmSqlServerActiveDirectoryAdministrator -ResourceGroupName "Group-23" -ServerName "demo_server" | Format-List
```

次の例では、Azure AD 管理者が削除されます。

```
Remove-AzureRmSqlServerActiveDirectoryAdministrator -ResourceGroupName "Group-23" -ServerName "demo_server"
```

Azure Active Directory 管理者は、REST API を使用してプロビジョニングすることもできます。 詳細については、「 [Service Management REST API リファレンスと Azure SQL Database の操作](https://msdn.microsoft.com/library/azure/dn505719.aspx)

## <a name="configure-your-client-computers"></a>クライアント コンピューターを構成する
Azure AD の ID を使用して Azure SQL Database または Azure SQL Data Warehouse に接続するアプリケーションまたはユーザーが存在するすべてのクライアント コンピューターには、次のソフトウェアをインストールする必要があります。

* [https://msdn.microsoft.com/library/5a4x27ek.aspx](https://msdn.microsoft.com/library/5a4x27ek.aspx)から入手できる .NET framework 4.6 以降。
* SQL Server 用の Azure Active Directory 認証ライブラリ (**ADALSQL.DLL**)。ダウンロード センターの「[Microsoft SQL Server 用の Microsoft Active Directory 認証ライブラリ](http://www.microsoft.com/download/details.aspx?id=48742)」から複数の言語版 (x86 と amd64 の両方) を入手できます。

これらの要件は、次の操作を行うことで満たすことができます。

* [SQL Server 2016 Management Studio](https://msdn.microsoft.com/library/mt238290.aspx) または [SQL Server Data Tools for Visual Studio 2015](https://msdn.microsoft.com/library/mt204009.aspx) をインストールすると、.NET Framework 4.6 の要件が満たされます。
* SSMS の場合、x86 バージョンの **ADALSQL.DLL**がインストールされます。
* SSDT の場合、amd64 バージョンの **ADALSQL.DLL**がインストールされます。
* 「 [Visual Studio のダウンロード](https://www.visualstudio.com/downloads/download-visual-studio-vs) 」にある最新の Visual Studio をインストールすると、.NET Framework 4.6 の要件は満たされますが、必要な amd64 バージョンの **ADALSQL.DLL**はインストールされません。

## <a name="create-contained-database-users-in-your-database-mapped-to-azure-ad-identities"></a>Azure AD の ID にマップされている包含データベース ユーザーをデータベースに作成する

Azure Active Directory 認証では、データベース ユーザーを包含データベース ユーザーとして作成することが必要です。 Azure AD の ID に基づく包含データベース ユーザーは、master データベースにログインを持たないデータベース ユーザーで、そのデータベースに関連付けられている Azure AD ディレクトリの ID にマップされています。 Azure AD の ID には、個々のユーザー アカウントにもグループ アカウントにもなります。 包含データベース ユーザーの詳細については、「 [包含データベース ユーザー - データベースの可搬性を確保する](https://msdn.microsoft.com/library/ff929188.aspx)」を参照してください。

> [!NOTE]
> ポータルを使用して、データベース ユーザー (管理者を除く) を作成することはできません。 RBAC ロールは、SQL Server、SQL Database、または SQL Data Warehouse には反映されません。 Azure RBAC ロールは Azure リソースの管理に使用され、データベースのアクセス許可には適用されません。 たとえば、 **SQL Server 共同作成者** ロールでは、SQL Database または SQL Data Warehouse に接続するためのアクセス権は付与されません。 Transact-SQL ステートメントを使用して、アクセス許可をデータベースで直接付与する必要があります。
>

Azure AD ベースの包含データベース ユーザー (データベースを所有するサーバー管理者以外) を作成するには、少なくとも **ALTER ANY USER** アクセス許可を持つユーザーとして、Azure AD の ID でデータベースに接続します。 その後、次の Transact-SQL 構文を使用します。

```
CREATE USER <Azure_AD_principal_name> FROM EXTERNAL PROVIDER;
```

*Azure_AD_principal_name* には、Azure AD ユーザーのユーザー プリンシパル名または Azure AD のグループの表示名を指定できます。

**例:** Azure AD のフェデレーション ドメインまたは管理対象ドメインのユーザーを表す包含データベース ユーザーを作成するには、次のようにします。
```
CREATE USER [bob@contoso.com] FROM EXTERNAL PROVIDER;
CREATE USER [alice@fabrikam.onmicrosoft.com] FROM EXTERNAL PROVIDER;
```

Azure AD またはフェデレーション ドメインのグループを表す包含データベース ユーザーを作成するには、セキュリティ グループの表示名を指定します。
```
CREATE USER [ICU Nurses] FROM EXTERNAL PROVIDER;
```

Azure AD トークンを使用して接続するアプリケーションを表す包含データベース ユーザーを作成するには、次のようにします。

```
CREATE USER [appName] FROM EXTERNAL PROVIDER;
```

>  [!TIP]
>  使用している Azure サブスクリプションに関連付けられている Azure Active Directory とは別の Azure Active Directory から、ユーザーを直接作成することはできません。 ただし、別の Active Directory のメンバーのうち、関連付けられている Active Directory にインポート済みのユーザー (外部ユーザーと呼ばれます) は、テナント Active Directory の Active Directory グループに追加できます。 外部の Active Directory のユーザーは、この AD グループの包含データベース ユーザーを作成することで SQL Database にアクセスできるようになります。   

Azure Active Directory の ID に基づく包含データベース ユーザーの作成の詳細については、「 [CREATE USER (Transact-SQL)](http://msdn.microsoft.com/library/ms173463.aspx)」を参照してください。

> [!NOTE]
> Azure SQL Server の Azure Active Directory 管理者を削除すると、Azure AD 認証ユーザーはサーバーに接続できなくなります。 必要に応じて、SQL Database 管理者は不要な Azure AD ユーザーを手動で削除できます。   

>  [!NOTE]
>  "**接続がタイムアウトしました**" と表示された場合は、接続文字列の `TransparentNetworkIPResolution` パラメーターを false に設定することが必要になる場合があります。 詳しくは、「[Connection timeout issue with .NET Framework 4.6.1 - TransparentNetworkIPResolution](https://blogs.msdn.microsoft.com/dataaccesstechnologies/2016/05/07/connection-timeout-issue-with-net-framework-4-6-1-transparentnetworkipresolution/)」(.NET Framework 4.6.1 での接続タイムアウトの問題 - TransparentNetworkIPResolution) をご覧ください。   

   
データベース ユーザーを作成すると、そのユーザーには **CONNECT** アクセス許可が付与され、**PUBLIC** ロールのメンバーとしてそのデータベースに接続できるようになります。 最初にこのユーザーが利用できるアクセス許可は、 **PUBLIC** ロールに付与されているアクセス許可か、またはそのユーザーが属している Windows グループに付与されているアクセス許可のみです。 Azure AD ベースの包含データベース ユーザーをプロビジョニングすると、他の種類のユーザーにアクセス許可を付与する場合と同様に、そのユーザーに追加のアクセス許可を付与できます。 通常は、データベース ロールにアクセス許可を付与し、そのロールにユーザーを追加します。 詳細については、 [データベース エンジンのアクセス許可の基本知識](http://social.technet.microsoft.com/wiki/contents/articles/4433.database-engine-permission-basics.aspx)に関するページを参照してください。 特殊な SQL Database ロールの詳細については、「 [Azure SQL Database におけるデータベースとログインの管理](sql-database-manage-logins.md)」を参照してください。
管理対象ドメインにインポートされるフェデレーション ドメイン ユーザーは、管理対象ドメインの ID を使用する必要があります。

> [!NOTE]
> データベースのメタデータでは、Azure AD ユーザーはタイプ E (EXTERNAL_USER) 、グループはタイプ X (EXTERNAL_GROUPS) でマークされます。 詳細については、「[sys.database_principals](https://msdn.microsoft.com/library/ms187328.aspx)」を参照してください。 
>

## <a name="connect-to-the-user-database-or-data-warehouse-by-using-sql-server-management-studio-or-sql-server-data-tools"></a>SQL Server Management Studio または SQL Server Data Tools を使用してユーザーのデータベースまたはデータ ウェアハウスに接続する
Azure AD 管理者が正しく設定されていることを確認するには、Azure AD の管理者アカウントを使用して **master** データベースに接続します。
Azure AD ベースの包含データベース ユーザー (データベースを所有しているサーバー管理者以外) をプロビジョニングするには、そのデータベースへのアクセス権を持つ Azure AD の ID を使用してデータベースに接続します。

> [!IMPORTANT]
> Azure Active Directory 認証は、[SQL Server 2016 Management Studio](https://msdn.microsoft.com/library/mt238290.aspx) および Visual Studio 2015 の [SQL Server Data Tools](https://msdn.microsoft.com/library/mt204009.aspx) でサポートされています。 SSMS の 2016 年 8 月のリリースには、Active Directory Universal 認証のサポートも含まれます。これにより、管理者は、電話、テキスト メッセージ、スマート カードと暗証番号 (PIN)、またはモバイル アプリ通知を使用する Multi-Factor Authentication を要求できます。
 
## <a name="using-an-azure-ad-identity-to-connect-using-sql-server-management-studio-or-sql-server-database-tools"></a>SQL Server Management Studio または SQL Server Database Tools での Azure AD の ID を使用した接続

次の手順は、SQL Server Management Studio または SQL Server Database Tools で Azure AD の ID を使用して SQL Database に接続する方法を示しています。

### <a name="active-directory-integrated-authentication"></a>Active Directory 統合認証

フェデレーション ドメインから Azure Active Directory の資格情報を使用して Windows にログオンしている場合は、この方法を使用します。

1. Management Studio または Data Tools を起動し、**[サーバーへの接続]** (または **[データベース エンジンへの接続]**) ダイアログ ボックスの **[認証]** ボックスで、**[Active Directory 統合認証]** を選択します。 接続用の既存の資格情報が表示されるため、パスワードは不要であるか、入力できません。   

    ![AD 統合認証を選択する][11]
2. **[オプション]** ボタンをクリックし、**[接続プロパティ]** ページの **[データベースへの接続]** ボックスに、接続先となるユーザー データベースの名前を入力します。   

    ![データベース名を選択する][13]

## <a name="active-directory-password-authentication"></a>Active Directory パスワード認証

Azure AD の管理対象ドメインを使用して Azure AD のプリンシパル名で接続する場合は、この方法を使用します。 また、リモートで作業する場合など、ドメインにアクセスできないフェデレーション アカウントにもこの方法を使用できます。

この方法を使用するのは、Azure とフェデレーションされていないドメインの資格情報を使用して Windows にログオンしている場合や、初期ドメインまたはクライアント ドメインに基づく Azure AD で Azure AD 認証を使用する場合です。

1. Management Studio または Data Tools を起動し、**[サーバーへの接続]** (または **[データベース エンジンへの接続]**) ダイアログ ボックスの **[認証]** ボックスで、**[Active Directory パスワード認証]** を選択します。
2. **[ユーザー名]** ボックスに、Azure Active Directory のユーザー名を **username@domain.com** の形式で入力します。 これは、Azure Active Directory のアカウントか、Azure Active Directory とフェデレーションするドメインのアカウントである必要があります。
3. **[パスワード]** ボックスに、Azure Active Directory アカウントまたはフェデレーション ドメイン アカウントのユーザー パスワードを入力します。

    ![AD パスワード認証を選択する][12]
4. **[オプション]** ボタンをクリックし、**[接続プロパティ]** ページの **[データベースへの接続]** ボックスに、接続先となるユーザー データベースの名前を入力します。 (前のオプションの図を参照してください)。

## <a name="using-an-azure-ad-identity-to-connect-from-a-client-application"></a>クライアント アプリケーションからの Azure AD の ID を使用した接続

次の手順は、クライアント アプリケーションから Azure AD の ID を使用して SQL データベースに接続する方法を示しています。

###  <a name="active-directory-integrated-authentication"></a>Active Directory 統合認証

統合 Windows 認証を使用するには、ドメインの Active Directory が Azure Active Directory とフェデレーションする必要があります。 データベースに接続するクライアント アプリケーション (またはサービス) は、ユーザーのドメイン資格情報を使用してドメインに参加しているコンピューターで実行されている必要があります。

統合認証と Azure AD の ID を使用してデータベースに接続するには、データベース接続文字列内の Authentication キーワードを "Active Directory Integrated" に設定する必要があります。 次の C# のコード サンプルでは、ADO .NET を使用します。

```
string ConnectionString =
@"Data Source=n9lxnyuzhv.database.windows.net; Authentication=Active Directory Integrated; Initial Catalog=testdb;";
SqlConnection conn = new SqlConnection(ConnectionString);
conn.Open();
```

接続文字列キーワード ``Integrated Security=True`` は、Azure SQL Database への接続ではサポートされていません。 ODBC 接続を行うには、スペースを削除して Authentication を 'ActiveDirectoryIntegrated' に設定する必要があります。

### <a name="active-directory-password-authentication"></a>Active Directory パスワード認証

統合認証と Azure AD の ID を使用してデータベースに接続するには、Authentication キーワードを "Active Directory Password" に設定する必要があります。 接続文字列にユーザー ID (UID) とパスワード (PWD) のキーワードと値を含める必要があります。 次の C# のコード サンプルでは、ADO .NET を使用します。

```
string ConnectionString =
@"Data Source=n9lxnyuzhv.database.windows.net; Authentication=Active Directory Password; Initial Catalog=testdb;  UID=bob@contoso.onmicrosoft.com; PWD=MyPassWord!";
SqlConnection conn = new SqlConnection(ConnectionString);
conn.Open();
```

[GitHub の Azure AD 認証のデモ](https://github.com/Microsoft/sql-server-samples/tree/master/samples/features/security/azure-active-directory-auth)で入手できるデモ コード サンプルを使用して、Azure AD の認証方法の詳細を確認してください。

## <a name="azure-ad-token"></a>Azure AD トークン
この認証方法を使用すると、中間層サービスは Azure Active Directory (AAD) からトークンを取得して、Azure SQL Database または Azure SQL Data Warehouse に接続できます。 この方法では、証明書ベースの認証などの高度なシナリオが可能になります。 Azure AD トークンの認証を使用するには、4 つの基本的な手順を完了する必要があります。

1. Azure Active Directory にアプリケーションを登録し、コードのクライアント ID を取得します。 
2. アプリケーションを表すデータベース ユーザーを作成します (上の手順&6;. で完了しています)。
3. アプリケーションを実行するクライアント コンピューターで証明書を作成します。
4. アプリケーションのキーとして、証明書を追加します。

サンプルの接続文字列を次に示します。

```
string ConnectionString =@"Data Source=n9lxnyuzhv.database.windows.net; Initial Catalog=testdb;"
SqlConnection conn = new SqlConnection(ConnectionString);
connection.AccessToken = "Your JWT token"
conn.Open();
```

詳細については、「 [SQL Server Security Blog (SQL Server のセキュリティに関するブログ)](https://blogs.msdn.microsoft.com/sqlsecurity/2016/02/09/token-based-authentication-support-for-azure-sql-db-using-azure-ad-auth/)」をご覧ください。

### <a name="sqlcmd"></a>sqlcmd

次のステートメントは、sqlcmd のバージョン 13.1 を使用して接続しています。これは、[ダウンロード センター](http://go.microsoft.com/fwlink/?LinkID=825643)から入手できます。

```
sqlcmd -S Target_DB_or_DW.testsrv.database.windows.net  -G  
sqlcmd -S Target_DB_or_DW.testsrv.database.windows.net -U bob@contoso.com -P MyAADPassword -G -l 30
```

## <a name="next-steps"></a>次のステップ
- SQL Database でのアクセスおよび制御の概要については、[SQL Database のアクセスと制御](sql-database-control-access.md)に関するページを参照してください。
- SQL Database のログイン、ユーザー、データベース ロールの概要については、[ログイン、ユーザー、およびデータベース ロール](sql-database-manage-logins.md)に関するページを参照してください。
- データベース プリンシパルの詳細については、「[プリンシパル](https://msdn.microsoft.com/library/ms181127.aspx)」を参照してください。
- データベース ロールの詳細については、[データベース ロール](https://msdn.microsoft.com/library/ms189121.aspx)に関するページを参照してください。
- SQL Database のファイアウォール規則の詳細については、[SQL Database のファイアウォール規則](sql-database-firewall-configure.md)に関するページを参照してください。
- SQL Server 認証の使用に関するチュートリアルについては、[SQL の認証と許可](sql-database-control-access-sql-authentication-get-started.md)に関するページを参照してください。
- Azure Active Directory 認証の使用に関するチュートリアルについては、[Azure AD の認証と許可](sql-database-control-access-aad-authentication-get-started.md)に関するページを参照してください。

<!--Image references-->

[1]: ./media/sql-database-aad-authentication/1aad-auth-diagram.png
[2]: ./media/sql-database-aad-authentication/2subscription-relationship.png
[3]: ./media/sql-database-aad-authentication/3admin-structure.png
[4]: ./media/sql-database-aad-authentication/4select-subscription.png
[5]: ./media/sql-database-aad-authentication/5ad-settings-portal.png
[6]: ./media/sql-database-aad-authentication/6edit-directory-select.png
[7]: ./media/sql-database-aad-authentication/7edit-directory-confirm.png
[8]: ./media/sql-database-aad-authentication/8choose-ad.png
[9]: ./media/sql-database-aad-authentication/9ad-settings.png
[10]: ./media/sql-database-aad-authentication/10choose-admin.png
[11]: ./media/sql-database-aad-authentication/11connect-using-int-auth.png
[12]: ./media/sql-database-aad-authentication/12connect-using-pw-auth.png
[13]: ./media/sql-database-aad-authentication/13connect-to-db.png


