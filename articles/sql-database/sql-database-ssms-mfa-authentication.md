---
title: Multi-Factor Authentication - Azure SQL | Microsoft Docs
description: "SQL Database と SQL Data Warehouse の SSMS で Multi-Factored Authentication を使用します。"
services: sql-database
documentationcenter: 
author: BYHAM
manager: jhubbard
editor: 
tags: 
ms.assetid: fbd6e644-0520-439c-8304-2e4fb6d6eb91
ms.service: sql-database
ms.custom: authentication and authorization
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: data-management
ms.date: 01/23/2017
ms.author: rickbyh
translationtype: Human Translation
ms.sourcegitcommit: 6e68ff56eaab3de8166711c872bf1b5707dbdf56
ms.openlocfilehash: ce77a059a05f83d74e80c71a87a22de6755b2dbb
ms.lasthandoff: 02/16/2017


---
# <a name="ssms-support-for-azure-ad-mfa-with-sql-database-and-sql-data-warehouse"></a>SQL Database と SQL Data Warehouse での Azure AD MFA のための SSMS のサポート
Azure SQL Database と Azure SQL Data Warehouse では、" *Active Directory ユニバーサル認証*" を使用して SQL Server Management Studio (SSMS) からの接続をサポートするようになりました。 Active Directory ユニバーサル認証とは、 *Azure Multi-Factor Authentication* (MFA) をサポートする対話型ワークフローです。 Azure MFA では、シンプルなサインイン プロセスを好むユーザーのニーズに応えながら、データやアプリケーションへのアクセスを効果的に保護することができます。 電話、テキスト メッセージ、スマート カードと PIN、モバイル アプリ通知など、簡単な各種確認オプションによって強力な認証が実現するため、ユーザーは自分に最も合った方法を選択できます。 

* Multi-Factor Authentication の説明については、 [Multi-Factor Authentication](../multi-factor-authentication/multi-factor-authentication.md)に関する記事を参照してください。
* 構成手順については、「[SQL Server Management Studio 用に Azure SQL Database の多要素認証を構成する](sql-database-ssms-mfa-authentication-configure.md)」をご覧ください。

## <a name="multi-factor-options"></a>多要素認証のオプション

現在、SSMS では以下がサポートされています。

* ポップアップ ダイアログ ボックスでの検証が可能な、Azure AD との対話型 MFA。
* 多くの異なるアプリケーション (ADO.NET、JDBC、ODBC など) で使用可能な、非対話型 Active Directory パスワードおよび Active Directory 統合認証方式。 これら&2; つの方式では、ポップアップ ダイアログ ボックスは表示されません。

ユーザー アカウントが MFA 用に構成されている場合、対話型の認証ワークフローでは、ポップアップ ダイアログ ボックスやスマート カードの使用など、追加のユーザー操作が必要になります。ユーザー アカウントが MFA 用に構成されている場合、ユーザーは接続する Azure ユニバーサル認証を選択する必要があります。 ユーザー アカウントが MFA を必要としない場合は、引き続き他の&2; つの Azure Active Directory 認証オプションを使用できます。

## <a name="universal-authentication-limitations-for-sql-database-and-sql-data-warehouse"></a>SQL Database と SQL Data Warehouse でのユニバーサル認証の制限事項
* SSMS は、Active Directory ユニバーサル認証を介する MFA で現在有効になっている唯一のツールです。
* ユニバーサル認証を使用して SSMS のインスタンスにログインできるのは、1 つの Azure Active Directory アカウントのみです。 別の Azure AD アカウントとしてログインするには、SSMS の別のインスタンスを使用する必要があります (この制限は Active Directory ユニバーサル認証に限定されます。 Active Directory パスワード認証、Active Directory 統合認証、または SQL Server 認証の使用時は別のサーバーにログインできます)。
* SSMS では、オブジェクト エクスプローラー、クエリ エディター、クエリ ストアの視覚化で Active Directory ユニバーサル認証がサポートされます。
* DacFx とスキーマ デザイナーでは、ユニバーサル認証はサポートされません。
* MSA アカウントは Active Directory ユニバーサル認証でサポートされていません。
* 他の Azure Active Directory から現在の Active Directory にインポートされたユーザーの場合、SSMS での Active Directory ユニバーサル認証はサポートされていません。 アカウントの検証にテナント ID が必要になるが、それを実行するメカニズムがないため、これらのユーザーはサポートされていません。
* サポートされているバージョンの SSMS を使用する必要があることを除き、Active Directory ユニバーサル認証に関する追加のソフトウェア要件はありません。



## <a name="next-steps"></a>次のステップ

* 構成手順については、「[SQL Server Management Studio 用に Azure SQL Database の多要素認証を構成する](sql-database-ssms-mfa-authentication-configure.md)」をご覧ください。
* 他のユーザーへのデータベースへのアクセス権の付与: [SQL Database の認証と承認: アクセス権の付与](sql-database-manage-logins.md)  
他のユーザーがファイアウォール経由で接続する手順は、「[Azure Portal を使用して Azure SQL Database のサーバー レベルのファイアウォール規則を作成する](sql-database-configure-firewall-settings.md)」をご覧ください。



