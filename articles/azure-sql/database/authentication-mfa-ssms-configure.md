---
title: Multi-Factor Authentication の構成
titleSuffix: Azure SQL Database & SQL Managed Instance & Azure Synapse Analytics
description: Azure SQL Database、Azure SQL Managed Instance、Azure Synapse Analytics の SSMS で多要素認証を使用する方法について説明します。
services: sql-database
ms.service: sql-db-mi
ms.subservice: security
ms.custom: has-adal-ref, sqldbrb=3
ms.devlang: ''
ms.topic: how-to
author: GithubMirek
ms.author: mireks
ms.reviewer: vanto
ms.date: 08/27/2019
ms.openlocfilehash: 4f90299daed46d06dad9ab37103e3b8f53763ed4
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "96454377"
---
# <a name="configure-multi-factor-authentication-for-sql-server-management-studio-and-azure-ad"></a>SQL Server Management Studio と Azure AD 用に多要素認証を構成する
[!INCLUDE[appliesto-sqldb-sqlmi-asa](../includes/appliesto-sqldb-sqlmi-asa.md)]

この記事では、Azure Active Directory (Azure AD) の多要素認証 (MFA) と SQL Server Management Studio (SSMS) を使用する方法について説明します。 Azure AD MFA は、SSMS または SqlPackage.exe を [Azure SQL Database](sql-database-paas-overview.md)、[Azure SQL Managed Instance](../managed-instance/sql-managed-instance-paas-overview.md)、および [Azure Synapse Analytics](../../synapse-analytics/sql-data-warehouse/sql-data-warehouse-overview-what-is.md) に接続するときに使用できます。 多要素認証の概要については、[SQL Database、SOL Managed Instance、Azure Synapse でのユニバーサル認証 (MFA 対応の SSMS サポート)](../database/authentication-mfa-ssms-overview.md) に関するページを参照してください。

> [!IMPORTANT]
> Azure SQL Database、Azure SQL Managed Instance、Azure Synapse のデータベースは、この記事の残りの部分ではまとめてデータベースと呼ばれています。また、サーバーは、Azure SQL Database と Azure Synapse のデータベースをホストする[サーバー](logical-servers.md)を指しています。

## <a name="configuration-steps"></a>構成の手順

1. **Azure Active Directory の構成** - 詳細については、「[Azure AD ディレクトリの管理](/previous-versions/azure/azure-services/hh967611(v=azure.100))」、「[オンプレミスのディレクトリと Azure Active Directory の統合](../../active-directory/hybrid/whatis-hybrid-identity.md)」、[独自のドメイン名を Azure AD に追加する方法に関するブログ](https://azure.microsoft.com/blog/20../../windows-azure-now-supports-federation-with-windows-server-active-directory/)、[Microsoft Azure が Windows Server Active Directory とのフェデレーションをサポートするようになったことに関するブログ](https://azure.microsoft.com/blog/20../../windows-azure-now-supports-federation-with-windows-server-active-directory/)、および [Windows PowerShell を使用した Azure AD の管理に関する記事](/previous-versions/azure/jj151815(v=azure.100))を参照してください。
2. **MFA の構成** - 詳細な手順については、「[Azure AD Multi-Factor Authentication とは](../../active-directory/authentication/concept-mfa-howitworks.md)」、「[Azure SQL Database と Data Warehouse での条件付きアクセス (MFA)](conditional-access-configure.md)」を参照してください (完全な条件付きアクセスの場合は Premium Azure Active Directory が必要です。 標準の Azure AD では、制限付きの MFA を使用できます)。
3. **Azure AD Authentication の構成** - 手順については、[Azure AD Authentication を使用して SQL Database、SQL Managed Instance、または Azure Synapse に接続する](authentication-aad-overview.md)方法に関するページをご覧ください。
4. **SSMS のダウンロード** - クライアント コンピューターで、「[SQL Server Management Studio (SSMS) のダウンロード](/sql/ssms/download-sql-server-management-studio-ssms)」から SSMS の最新版をダウンロードします。

## <a name="connecting-by-using-universal-authentication-with-ssms"></a>SSMS でユニバーサル認証を使って接続する

次の手順は、最新の SSMS を使用して接続する方法を示しています。

[!INCLUDE[ssms-connect-azure-ad](../includes/ssms-connect-azure-ad.md)]

1. ユニバーサル認証を使用して接続するには、SQL Server Management Studio (SSMS) の **[サーバーに接続]** ダイアログ ボックスで、 **[Active Directory - MFA サポートで汎用]** を選択します ( **[Active Directory のユニバーサル認証]** と表示される場合、お使いのバージョンは最新の SSMS ではありません)。

   ![SSMS の [サーバーに接続] ダイアログの [接続プロパティ] タブのスクリーンショット。[データベースへの接続] ドロップダウンで [MyDatabase] が選択されています。](./media/authentication-mfa-ssms-configure/mfa-no-tenant-ssms.png)  
2. **[ユーザー名]** ボックスに、`user_name@domain.com` という形式で Azure Active Directory の資格情報を入力します。

   ![[サーバーに接続] ダイアログでのサーバーの種類、サーバー名、認証、ユーザー名の設定のスクリーンショット。](./media/authentication-mfa-ssms-configure/1mfa-universal-connect-user.png)
3. ゲスト ユーザーとして接続する場合は、ゲスト ユーザーの [AD ドメイン名またはテナント ID] フィールドを入力する必要はありません。SSMS 18.x 以降では自動的に認識されます。 詳細については、[SQL Database、SQL Managed Instance、Azure Synapse でのユニバーサル認証 (MFA 対応の SSMS サポート)](../database/authentication-mfa-ssms-overview.md) に関するページを参照してください。

   ![SSMS の [サーバーに接続] ダイアログの [接続プロパティ] タブのスクリーンショット。[データベースへの接続] ドロップダウンで [MyDatabase] が選択されています。](./media/authentication-mfa-ssms-configure/mfa-no-tenant-ssms.png)

   ただし、SSMS 17.x 以前を使用し、ゲスト ユーザーとして接続している場合は、 **[オプション]** をクリックし、 **[接続プロパティ]** ダイアログ ボックスで **[AD ドメイン名またはテナント ID]** ボックスに入力する必要があります。

   ![SSMS の [サーバーに接続] ダイアログの [接続プロパティ] タブのスクリーンショット。オプション [AD ドメイン名またはテナント ID] プロパティが入力されています。](./media/authentication-mfa-ssms-configure/mfa-tenant-ssms.png)

4. **[オプション]** を選択し、 **[オプション]** ダイアログ ボックスでデータベースを指定します。 接続ユーザーがゲスト ユーザーの場合 (つまり joe@outlook.com)、このチェックボックスをオンにして、オプションの一部として現在の AD ドメイン名またはテナント ID を追加する必要があります。 「[SQL Database と Azure Synapse Analytics を使用したユニバーサル認証 (SSMS での MFA のサポート)](../database/authentication-mfa-ssms-overview.md)」をご覧ください。 次いで **[Connect]** をクリックします。  
5. **[アカウントにサインイン]** ダイアログ ボックスが開いたら、Azure Active Directory ID のアカウントとパスワードを入力します。 ユーザーが Azure AD とフェデレーションされているドメインに属している場合、パスワードは不要です。

   ![Azure SQL Database と Data Warehouse の [アカウントにサインイン] ダイアログのスクリーンショット。 アカウントとパスワードが入力されています。](./media/authentication-mfa-ssms-configure/2mfa-sign-in.png)  

   > [!NOTE]
   > MFA を必要としないアカウントでのユニバーサル認証の場合は、この時点で接続が完了します。 MFA を必要とするユーザーの場合は、次の手順に進みます。
   >  

6. MFA 設定用の 2 つのダイアログ ボックスが表示される場合があります。 この 1 度限りの操作は MFA 管理者の設定に応じて異なり、省略することも可能です。 MFA が有効なドメインでは、この手順が事前に定義されている場合もあります (たとえば、ユーザーはスマート カードと PIN を使用するように求められます)。

   ![追加のセキュリティ検証の設定を求めるプロンプトが表示されている、Azure SQL Database と Data Warehouse の [アカウントにサインイン] ダイアログのスクリーンショット。](./media/authentication-mfa-ssms-configure/3mfa-setup.png)
  
7. 2 つ目に表示される可能性のある 1 度限りのダイアログ ボックスでは、認証方式の詳細を選択できます。 使用可能なオプションは、管理者によって構成されます。

   ![認証方法を選択して構成するためのオプションが含まれる [追加のセキュリティ確認] ダイアログのスクリーンショット。](./media/authentication-mfa-ssms-configure/4mfa-verify-1.png)  
8. Azure Active Directory から確認用の情報が送信されます。 確認コードを受け取ったら、 **[確認コードを入力する]** ボックスにコードを入力し、 **[サインイン]** をクリックします。

   ![確認コードの入力を求めるプロンプトが表示されている、Azure SQL Database と Data Warehouse の [アカウントにサインイン] ダイアログのスクリーンショット。](./media/authentication-mfa-ssms-configure/5mfa-verify-2.png)  

確認が完了すると、有効な資格情報とファイアウォール アクセスが推定され、SSMS の接続が通常どおり行われます。

## <a name="next-steps"></a>次のステップ

- 多要素認証の概要については、[SQL Database、SOL Managed Instance、Azure Synapse でのユニバーサル認証 (MFA 対応の SSMS サポート)](../database/authentication-mfa-ssms-overview.md) に関するページを参照してください。  
- 自分のデータベースへのアクセス権を他のユーザーに付与する:[SQL Database の認証と承認:アクセス権の付与](logins-create-manage.md)  
- 他のユーザーがファイアウォール経由で接続できるようにする:[Azure portal でサーバー レベルのファイアウォール規則を構成する](./firewall-configure.md)  
- **Active Directory - MFA で汎用** 認証を使うとき、ADAL トレースは [SSMS 17.3](/sql/ssms/download-sql-server-management-studio-ssms) 以降で利用できます。 ADAL トレースは既定ではオフであり、オンにするには、 **[ツール]** の **[オプション]** メニューで、 **[Azure サービス]** 、 **[Azure クラウド]** 、 **[ADAL 出力ウィンドウのトレース レベル]** の順に選んで、 **[表示]** メニューの **[出力]** を有効にします。 出力ウィンドウで **[Azure Active Directory option]\(Azure Active Directory オプション\)** を選ぶと、トレースが使用可能になります。