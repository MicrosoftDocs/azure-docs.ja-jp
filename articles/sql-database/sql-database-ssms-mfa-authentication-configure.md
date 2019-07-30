---
title: Multi-Factor Authentication の構成 - Azure SQL | Microsoft Docs
description: SQL Database と SQL Data Warehouse の SSMS で Multi-Factored Authentication を使用する方法について説明します。
services: sql-database
ms.service: sql-database
ms.subservice: security
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: GithubMirek
ms.author: mireks
ms.reviewer: vanto
manager: craigg
ms.date: 09/25/2018
ms.openlocfilehash: b12508fbd0aec3bc6c697c920cd8ff94e3ac3396
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/13/2019
ms.locfileid: "67119169"
---
# <a name="configure-multi-factor-authentication-for-sql-server-management-studio-and-azure-ad"></a>SQL Server Management Studio と Azure AD 用に多要素認証を構成する

このトピックでは、Azure Active Directory Multi-Factor Authentication (MFA) と SQL Server Management Studio を使用する方法について説明します。 Azure AD MFA は、SSMS または SqlPackage.exe を Azure の [SQL Database](sql-database-technical-overview.md) と [SQL Data Warehouse](../sql-data-warehouse/sql-data-warehouse-overview-what-is.md) に接続するときに使用できます。 Azure SQL Database の多要素認証の概要については、「[SQL Database と SQL Data Warehouse でのユニバーサル認証 (MFA 対応の SSMS サポート)](sql-database-ssms-mfa-authentication.md)」を参照してください。

> [!NOTE]
> このトピックは Azure SQL サーバーのほか、その Azure SQL サーバーに作成される SQL Database と SQL Data Warehouse の両方に当てはまります。 わかりやすいように、SQL Database という言葉で SQL Database と SQL Data Warehouse の両方を言い表します。

## <a name="configuration-steps"></a>構成の手順

1. **Azure Active Directory の構成** - 詳細については、「[Azure AD ディレクトリの管理](https://msdn.microsoft.com/library/azure/hh967611.aspx)」、「[オンプレミスのディレクトリと Azure Active Directory の統合](../active-directory/hybrid/whatis-hybrid-identity.md)」、[独自のドメイン名を Azure AD に追加する方法に関するブログ](https://azure.microsoft.com/blog/20../../windows-azure-now-supports-federation-with-windows-server-active-directory/)、[Microsoft Azure が Windows Server Active Directory とのフェデレーションをサポートするようになったことに関するブログ](https://azure.microsoft.com/blog/20../../windows-azure-now-supports-federation-with-windows-server-active-directory/)、および[Windows PowerShell を使用した Azure AD の管理に関する記事](https://msdn.microsoft.com/library/azure/jj151815.aspx)を参照してください。
2. **MFA の構成** - 詳細な手順については、「[Azure Multi-Factor Authentication とは](../active-directory/authentication/multi-factor-authentication.md)」、「[Azure SQL Database と Data Warehouse での条件付きアクセス (MFA)](sql-database-conditional-access.md)」を参照してください (完全な条件付きアクセスの場合は Premium Azure Active Directory (Azure AD) が必要です。 標準の Azure AD では、制限付きの MFA を使用できます)。
3. **SQL Database または SQL Data Warehouse での Azure AD 認証の構成** - 手順については、「[Azure Active Directory 認証を使用して SQL Database または SQL Data Warehouse に接続する](sql-database-aad-authentication.md)」をご覧ください。
4. **SSMS のダウンロード** - クライアント コンピューターで、「[SQL Server Management Studio (SSMS) のダウンロード](https://msdn.microsoft.com/library/mt238290.aspx)」から SSMS の最新版をダウンロードします。 このトピックのすべての機能を使用するには、2017 年 7 月以降のバージョン 17.2 を使用してください。  

## <a name="connecting-by-using-universal-authentication-with-ssms"></a>SSMS でユニバーサル認証を使って接続する

最新版の SSMS を使用して SQL Database または SQL Data Warehouse に接続する手順は、次のとおりです。

1. ユニバーサル認証を使用して接続するには、 **[サーバーに接続]** ダイアログ ボックスで、 **[Active Directory - MFA サポートで汎用]** を選択します ( **[Active Directory のユニバーサル認証]** と表示される場合、お使いのバージョンは最新の SSMS ではありません)。  
   ![1mfa-universal-connect][1]  
2. **[ユーザー名]** ボックスに、`user_name@domain.com` という形式で Azure Active Directory の資格情報を入力します。  
   ![1mfa-universal-connect-user](./media/sql-database-ssms-mfa-auth/1mfa-universal-connect-user.png)   
3. ゲスト ユーザーとして接続している場合は、 **[オプション]** をクリックし、 **[接続プロパティ]** ダイアログ ボックスで **[AD ドメイン名またはテナント ID]** ボックスに入力します。 詳細については、「[SQL Database と SQL Data Warehouse でのユニバーサル認証 (MFA 対応の SSMS サポート)](sql-database-ssms-mfa-authentication.md)」を参照してください。
   ![mfa-tenant-ssms](./media/sql-database-ssms-mfa-auth/mfa-tenant-ssms.png)   
4. SQL Database と SQL Data Warehouse では通常どおり、 **[オプション]** をクリックし、 **[オプション]** ダイアログ ボックスでデータベースを指定します。 接続ユーザーがゲスト ユーザーの場合 (つまり joe@outlook.com)、このチェックボックスをオンにして、オプションの一部として現在の AD ドメイン名またはテナント ID を追加する必要があります。 「[SQL Database と SQL Data Warehouse でのユニバーサル認証 (MFA 対応の SSMS サポート)](sql-database-ssms-mfa-authentication.md)」を参照してください。 次いで **[接続]** をクリックします。  
5. **[アカウントにサインイン]** ダイアログ ボックスが開いたら、Azure Active Directory ID のアカウントとパスワードを入力します。 ユーザーが Azure AD とフェデレーションされているドメインに属している場合、パスワードは不要です。  
   ![2mfa-sign-in][2]  

   > [!NOTE]
   > MFA を必要としないアカウントでのユニバーサル認証の場合は、この時点で接続が完了します。 MFA を必要とするユーザーの場合は、次の手順に進みます。
   >  
   
6. MFA 設定用の 2 つのダイアログ ボックスが表示される場合があります。 この 1 度限りの操作は MFA 管理者の設定に応じて異なり、省略することも可能です。 MFA が有効なドメインでは、この手順が事前に定義されている場合もあります (たとえば、ユーザーはスマート カードと PIN を使用するように求められます)。  
   ![3mfa-setup][3]  
7. 2 つ目に表示される可能性のある 1 度限りのダイアログ ボックスでは、認証方式の詳細を選択できます。 使用可能なオプションは、管理者によって構成されます。  
   ![4mfa-verify-1][4]  
8. Azure Active Directory から確認用の情報が送信されます。 確認コードを受け取ったら、 **[確認コードを入力する]** ボックスにコードを入力し、 **[サインイン]** をクリックします。  
   ![5mfa-verify-2][5]  

確認が完了すると、有効な資格情報とファイアウォール アクセスが推定され、SSMS の接続が通常どおり行われます。

## <a name="next-steps"></a>次の手順

- Azure SQL Database の多要素認証の概要については、「[SQL Database と SQL Data Warehouse でのユニバーサル認証 (MFA 対応の SSMS サポート)](sql-database-ssms-mfa-authentication.md)」を参照してください。  
- 自分のデータベースへのアクセス権を他のユーザーに付与する:[SQL Database の認証と承認:アクセス権の付与](sql-database-manage-logins.md)  
- 他のユーザーがファイアウォール経由で接続できるようにする:[Azure Portal を使用して Azure SQL Database のサーバー レベルのファイアウォール規則を作成する](sql-database-configure-firewall-settings.md)  
- **Active Directory - MFA で汎用**認証を使うとき、ADAL トレースは [SSMS 17.3](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms) 以降で利用できます。 ADAL トレースは既定ではオフであり、オンにするには、 **[ツール]** の **[オプション]** メニューで、 **[Azure サービス]** 、 **[Azure クラウド]** 、 **[ADAL 出力ウィンドウのトレース レベル]** の順に選んで、 **[表示]** メニューの **[出力]** を有効にします。 出力ウィンドウで **[Azure Active Directory option]\(Azure Active Directory オプション\)** を選ぶと、トレースが使用可能になります。   


[1]: ./media/sql-database-ssms-mfa-auth/1mfa-universal-connect.png
[2]: ./media/sql-database-ssms-mfa-auth/2mfa-sign-in.png
[3]: ./media/sql-database-ssms-mfa-auth/3mfa-setup.png
[4]: ./media/sql-database-ssms-mfa-auth/4mfa-verify-1.png
[5]: ./media/sql-database-ssms-mfa-auth/5mfa-verify-2.png

