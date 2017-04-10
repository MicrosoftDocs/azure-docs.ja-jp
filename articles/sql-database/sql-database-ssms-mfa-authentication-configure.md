---
title: "Multi-Factor Authentication の構成 - Azure SQL | Microsoft Docs"
description: "SQL Database と SQL Data Warehouse の SSMS で Multi-Factored Authentication を使用します。"
services: sql-database
documentationcenter: 
author: BYHAM
manager: jhubbard
editor: 
tags: 
ms.assetid: 
ms.service: sql-database
ms.custom: authentication and authorization
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: data-management
ms.date: 01/23/2017
ms.author: rickbyh
translationtype: Human Translation
ms.sourcegitcommit: b134999d407195aaf44babb3e4862b96cc1dc1ed
ms.openlocfilehash: b36f0cf8cbf0dfb310d6dd534906ee5391ce4cd5
ms.lasthandoff: 03/02/2017


---
# <a name="configure-azure-sql-database-multi-factor-authentication-for-sql-server-management-studio"></a>SQL Server Management Studio 用に Azure SQL Database の多要素認証を構成する

このトピックでは、SQL Server Management Studio 用に Azure SQL Database の多要素認証を構成する方法について説明します。 

Azure SQL Database の多要素認証の概要については、[SQL Server Management Studio に対する Azure SQL Database の多要素認証の概要に関する記事](sql-database-ssms-mfa-authentication.md)をご覧ください。

## <a name="configuration-steps"></a>構成の手順

1. **Azure Active Directory の構成** - 詳しくは、「[オンプレミス ID と Azure Active Directory の統合](../active-directory/active-directory-aadconnect.md)」、[Azure AD への独自のドメイン名の追加に関する記事](https://azure.microsoft.com/blog/2012/11/28/windows-azure-now-supports-federation-with-windows-server-active-directory/)、「[Microsoft Azure now supports federation with Windows Server Active Directory](https://azure.microsoft.com/blog/2012/11/28/windows-azure-now-supports-federation-with-windows-server-active-directory/)」(Microsoft Azure による Windows Server Active Directory とのフェデレーションのサポートの実現)、「[Azure AD ディレクトリの管理](https://msdn.microsoft.com/library/azure/hh967611.aspx)」、[Windows PowerShell による Azure AD の管理に関する記事](https://msdn.microsoft.com/library/azure/jj151815.aspx)をご覧ください。
2. **MFA の構成** - 手順については、「[Azure Multi-Factor Authentication の構成](../multi-factor-authentication/multi-factor-authentication-whats-next.md)」をご覧ください。 
3. **SQL Database または SQL Data Warehouse での Azure AD 認証の構成** - 手順については、「[Azure Active Directory 認証を使用して SQL Database または SQL Data Warehouse に接続する](sql-database-aad-authentication.md)」をご覧ください。
4. **SSMS のダウンロード** - クライアント コンピューターで、「[SQL Server Management Studio (SSMS) のダウンロード](https://msdn.microsoft.com/library/mt238290.aspx)」から SSMS の最新版 (少なくとも 2016 年 8 月) をダウンロードします。

## <a name="connecting-by-using-universal-authentication-with-ssms"></a>SSMS でユニバーサル認証を使って接続する

最新版の SSMS を使用して SQL Database または SQL Data Warehouse に接続する手順は、次のとおりです。

1. ユニバーサル認証を使用して接続するには、**[サーバーに接続]** ダイアログ ボックスで、**[Active Directory Universal Authentication (Active Directory ユニバーサル認証)]** を選択します。

   ![1mfa-universal-connect][1]
2. SQL Database と SQL Data Warehouse では通常どおり、**[オプション]** をクリックし、**[オプション]** ダイアログ ボックスでデータベースを指定します。 次いで **[接続]**をクリックします。
3. **[アカウントにサインイン]** ダイアログ ボックスが開いたら、Azure Active Directory ID のアカウントとパスワードを入力します。

   ![2mfa-sign-in][2]
   
   > [!NOTE]
   > MFA を必要としないアカウントでのユニバーサル認証の場合は、この時点で接続が完了します。 MFA を必要とするユーザーの場合は、次の手順に進みます。
   > 
   > 
4. MFA 設定用の&2; つのダイアログ ボックスが表示される場合があります。 この&1; 度限りの操作は MFA 管理者の設定に応じて異なり、省略することも可能です。 MFA が有効なドメインでは、この手順が事前に定義されている場合もあります (たとえば、ユーザーはスマート カードと PIN を使用するように求められます)。  

   ![3mfa-setup][3]
5. 2 つ目に表示される可能性のある&1; 度限りのダイアログ ボックスでは、認証方式の詳細を選択できます。 使用可能なオプションは、管理者によって構成されます。

   ![4mfa-verify-1][4]
6. Azure Active Directory から確認用の情報が送信されます。 確認コードを受け取ったら、**[確認コードを入力する]** ボックスにコードを入力し、**[サインイン]** をクリックします。

   ![5mfa-verify-2][5]

確認が完了すると、有効な資格情報とファイアウォール アクセスが推定され、SSMS の接続が通常どおり行われます。

## <a name="next-steps"></a>次のステップ

* Azure SQL Database の多要素認証の概要については、[SQL Server Management Studio に対する Azure SQL Database の多要素認証の概要に関する記事](sql-database-ssms-mfa-authentication.md)をご覧ください。
* 他のユーザーへのデータベースへのアクセス権の付与: [SQL Database の認証と承認: アクセス権の付与](sql-database-manage-logins.md)  
他のユーザーがファイアウォール経由で接続する手順は、「[Azure Portal を使用して Azure SQL Database のサーバー レベルのファイアウォール規則を作成する](sql-database-configure-firewall-settings.md)」をご覧ください。

[1]: ./media/sql-database-ssms-mfa-auth/1mfa-universal-connect.png
[2]: ./media/sql-database-ssms-mfa-auth/2mfa-sign-in.png
[3]: ./media/sql-database-ssms-mfa-auth/3mfa-setup.png
[4]: ./media/sql-database-ssms-mfa-auth/4mfa-verify-1.png
[5]: ./media/sql-database-ssms-mfa-auth/5mfa-verify-2.png


