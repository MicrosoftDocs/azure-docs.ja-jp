---
title: "Azure SQL Database へのアクセスの許可 | Microsoft Docs"
description: "Microsoft Azure SQL Database へのアクセスの許可について説明します。"
services: sql-database
documentationcenter: 
author: BYHAM
manager: jhubbard
editor: 
tags: 
ms.assetid: 8e71b04c-bc38-4153-8f83-f2b14faa31d9
ms.service: sql-database
ms.custom: overview
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: data-management
ms.date: 02/06/2017
ms.author: rickbyh
translationtype: Human Translation
ms.sourcegitcommit: ae230c012a17eb73c8993a32197c844c6abaa2a4
ms.openlocfilehash: 8b9b9b70cc7b6a9b89a52523f713b43b74713042
ms.lasthandoff: 02/17/2017

---
# <a name="azure-sql-database-access-control"></a>Azure SQL Database のアクセス制御
SQL Database では、セキュリティを提供するために、IP アドレスで接続を制限するファイアウォール規則、ユーザーに ID の指定を要求する認証メカニズム、およびユーザーを特定の操作とデータに限定する承認メカニズムによって、アクセスを制御します。 

> [!IMPORTANT]
> SQL Database のセキュリティ機能の概要については、[SQL のセキュリティの概要](sql-database-security-overview.md)に関するページを参照してください。 SQL Server 認証の使用に関するチュートリアルについては、[SQL の認証と許可](sql-database-control-access-sql-authentication-get-started.md)に関するページを参照してください。 Azure Active Directory 認証の使用に関するチュートリアルについては、[Azure AD の認証と許可](sql-database-control-access-aad-authentication-get-started.md)に関するページを参照してください。

## <a name="firewall-and-firewall-rules"></a>ファイアウォールとファイアウォール規則
Microsoft Azure SQL Database は、Azure およびその他のインターネット ベースのアプリケーション用のリレーショナル データベース サービスを提供します。 データを保護するため、ファイアウォールは、どのコンピューターに権限を持たせるかを指定するまで、データベース サーバーへのすべてのアクセスを遮断します。 ファイアウォールは、各要求の送信元 IP アドレスに基づいてデータベースへのアクセス権を付与します。 詳細については、「[Azure SQL Database ファイアウォール規則の概要](sql-database-firewall-configure.md)」を参照してください。

Azure SQL Database サービスは TCP ポート 1433 経由でのみ利用できます。 コンピューターから SQL Database にアクセスするには、クライアント コンピューターのファイアウォールで、TCP ポート 1433 での TCP 通信の発信を許可する必要があります。 他のアプリケーションで必要ない場合は、TCP ポート 1433 での着信接続をブロックします。 

接続処理の一部として、Azure 仮想マシンからの接続は worker ロールごとに異なる別の IP アドレスとポートにリダイレクトされます。 ポート番号の範囲は、11000 ～ 11999 です。 TCP ポートの詳細については、「 [ADO.NET 4.5、SQL Database V12 における 1433 以外のポート](sql-database-develop-direct-route-ports-adonet-v12.md)」をご覧ください。

## <a name="authentication"></a>認証

SQL Database は、2 種類の認証をサポートしています。

* **SQL 認証**。ユーザー名とパスワードを使用します。 データベースの論理サーバーを作成したときに、ユーザー名とパスワードによる "サーバー管理" ログインを指定したとします。 これらの資格情報を使用すると、データベース所有者、つまり "dbo" として、そのサーバーにある任意のデータベースを認証できます。 
* **Azure Active Directory 認証**。Azure Active Directory で管理されている ID を使用します。管理および統合されたドメインの場合にサポートされます。 [可能であれば](https://msdn.microsoft.com/library/ms144284.aspx)、Active Directory 認証 (統合セキュリティ) を使用します。 Azure Active Directory 認証を使用する場合は、"Azure AD 管理者" という、Azure AD ユーザーとグループを管理できるサーバー管理者を別に作成する必要があります。 この管理者は、通常のサーバー管理者が実行できるすべての操作も実行できます。 Azure AD 管理者を作成して Azure Active Directory 認証を有効にする方法のチュートリアルについては、「 [Azure Active Directory の認証を使用して SQL Database に接続する](sql-database-aad-authentication.md) 」を参照してください。

データベース エンジンは、30 分以上アイドル状態が続くと接続を閉じます。 接続する前に、もう一度ログインする必要があります。 SQL Database への継続したアクティブな接続では、少なくとも 10 時間ごとに再認証が必要になります (データベース エンジンによって実行されます)。 データベース エンジンは最初に送信されたパスワードを使用して再認証を試みるため、ユーザー入力は必要ありません。 パフォーマンス上の理由により、パスワードが SQL Database でリセットされると、接続プールが原因で接続がリセットされた場合でも、接続は再認証されません。 これは、オンプレミスの SQL Server とは異なる動作です。 接続が最初に承認されて以来パスワードが変更されている場合は、その接続を終了し、新しいパスワードを使用して新しい接続を作成する必要があります。 `KILL DATABASE CONNECTION` アクセス許可を持つユーザーは、[KILL](https://msdn.microsoft.com/library/ms173730.aspx) コマンドを使用して SQL Database への接続を明示的に終了できます。

ユーザー アカウントを master データベースに作成し、サーバーのすべてのデータベースでアクセス許可を付与することができます。または、ユーザー アカウントをデータベース自体に作成することができます (包含ユーザーと呼ばれます)。 ログインの作成と管理については、[ログインの管理](sql-database-manage-logins.md)に関するページを参照してください。 移植性とスケーラビリティを高める場合は、包含データベース ユーザーを使用してスケーラビリティを高めます。 包含ユーザーの詳細については、「[包含データベース ユーザー - データベースの可搬性を確保する](https://msdn.microsoft.com/library/ff929188.aspx)」、「[CREATE USER (Transact-SQL)](https://technet.microsoft.com/library/ms173463.aspx)」、「[包含データベース](https://technet.microsoft.com/library/ff929071.aspx)」を参照してください。

ベスト プラクティスとしては、アプリケーションで専用アカウントを使用して認証することをお勧めします。この方法により、アプリケーションに付与されるアクセス許可を制限し、アプリケーション コードが SQL インジェクション攻撃に対して脆弱な場合に、悪意のあるアクティビティのリスクを軽減できます。 [包含データベース ユーザー](https://msdn.microsoft.com/library/ff929188)を作成する方法をお勧めします。この方法により、アプリがデータベースから直接認証を受けることができます。 

## <a name="authorization"></a>承認

承認とは、Azure SQL Database でユーザーが許可される操作を示すものであり、ユーザー アカウントのデータベース [ロールのメンバーシップ](https://msdn.microsoft.com/library/ms189121)と[オブジェクト レベルのアクセス許可](https://msdn.microsoft.com/library/ms191291.aspx)によって制御されます。 ベスト プラクティスとして、必要最低限の特権をユーザーに付与することをお勧めします。 接続しているサーバー管理者のアカウントは db_owner のメンバーであり、データベース内ですべての操作を実行する権限を持ちます。 スキーマのアップグレードやその他の管理操作をデプロイするために、このアカウントを保存します。 アクセス許可が限定された "ApplicationUser" アカウントを使用して、アプリケーションで必要な最小限の特権により、アプリケーションをデータベースに接続します。 詳細については、[ログインの管理](sql-database-manage-logins.md)に関するページを参照してください。

通常、`master` データベースへのアクセスが必要なのは管理者のみです。 各ユーザー データベースに定期的にアクセスするには、各データベースで作成された包含データベース ユーザー (管理者以外) を使用する必要があります。 包含データベース ユーザーを使用する場合は、`master` データベースにログインを作成する必要はありません。 詳細については、「 [包含データベース ユーザー - データベースの可搬性を確保する](https://msdn.microsoft.com/library/ff929188.aspx)」を参照してください。

アクセス許可を制限したり昇格させたりするために使用できる次の機能について理解を深める必要があります。   
* [権限借用](https://msdn.microsoft.com/library/vstudio/bb669087)と[モジュール署名](https://msdn.microsoft.com/library/bb669102)を使用すると、安全にアクセス許可を一時的に昇格できます。
* [Row-Level Security](https://msdn.microsoft.com/library/dn765131) を使用すると、ユーザーがアクセスできる行を制限できます。
* [データのマスキング](sql-database-dynamic-data-masking-get-started.md) を使用すると、機密データの公開を制限できます。
* [ストアド プロシージャ](https://msdn.microsoft.com/library/ms190782) を使用すると、データベースで実行できるアクションを制限できます。

## <a name="next-steps"></a>次のステップ

- SQL Database のセキュリティ機能の概要については、[SQL のセキュリティの概要](sql-database-security-overview.md)に関するページを参照してください。
- ファイアウォール規則の詳細については、[ファイアウォール規則](sql-database-firewall-configure.md)に関するページを参照してください。
- ユーザーとログインの詳細については、[ログインの管理](sql-database-manage-logins.md)に関するページを参照してください。 
- プロアクティブな監視の詳細については、[データベース監査](sql-database-auditing.md)と[SQL Database の脅威の検出](sql-database-threat-detection.md)に関するページを参照してください。
- SQL Server 認証の使用に関するチュートリアルについては、[SQL の認証と許可](sql-database-control-access-sql-authentication-get-started.md)に関するページを参照してください。
- Azure Active Directory 認証の使用に関するチュートリアルについては、[Azure AD の認証と許可](sql-database-control-access-aad-authentication-get-started.md)に関するページを参照してください。

