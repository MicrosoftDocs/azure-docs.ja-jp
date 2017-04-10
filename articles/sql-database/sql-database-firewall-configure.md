---
title: "SQL Database ファイアウォール規則の概要 | Microsoft Docs"
description: "アクセスを管理するために、サーバー レベルおよびデータベース レベルのファイアウォール規則を使用して SQL データベース ファイアウォールを構成する方法について説明します。"
keywords: "データベース ファイアウォール"
services: sql-database
documentationcenter: 
author: BYHAM
manager: jhubbard
editor: cgronlun
tags: 
ms.assetid: ac57f84c-35c3-4975-9903-241c8059011e
ms.service: sql-database
ms.custom: authentication and authorization
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: data-management
ms.date: 02/28/2017
ms.author: rickbyh
translationtype: Human Translation
ms.sourcegitcommit: 6654b49658818a3dfd5297dc551a45de0f0968dc
ms.openlocfilehash: 196a627b4c5cf921ddc99a0634f48f807593f8cc
ms.lasthandoff: 03/02/2017


---
# <a name="overview-of-azure-sql-database-firewall-rules"></a>Azure SQL Database ファイアウォール規則の概要 

Microsoft Azure SQL Database は、Azure およびその他のインターネット ベースのアプリケーション用のリレーショナル データベース サービスを提供します。 データを保護するため、ファイアウォールは、どのコンピューターに権限を持たせるかを指定するまで、データベース サーバーへのすべてのアクセスを遮断します。 ファイアウォールは、各要求の送信元 IP アドレスに基づいてデータベースへのアクセス権を付与します。

ファイアウォールを構成するには、受け入れ可能な IP アドレスの範囲を指定するファイアウォール規則を作成します。 サーバーおよびデータベース レベルでは、ファイアウォール規則を作成できます。

* **サーバー レベルのファイアウォール規則:** これらの規則により、クライアントは、Azure SQL サーバー全体、つまり、同じ論理サーバー内のすべてのデータベースにアクセスできるようになります。 これらの規則は、 **マスター** データベースに保存されます。 サーバー レベルのファイアウォール規則を構成するには、ポータルまたは Transact-SQL ステートメントを使用します。 Azure Portal または PowerShell を使用してサーバーレベルのファイアウォール規則を作成するには、サブスクリプション所有者またはサブスクリプション共同作成者である必要があります。 Transact-SQL を使用してサーバーレベルのファイアウォール規則を作成するには、サーバーレベルのプリンシパル ログインまたは Azure Active Directory 管理者として SQL Database インスタンスに接続する必要があります (つまり、サーバーレベルのファイアウォール規則は、最初に Azure レベルのアクセス許可を持つユーザーが作成する必要があります)。
* **データベース レベルのファイアウォール規則:** これらの規則により、クライアントは、Azure SQL Database サーバー内の個々のデータベースにアクセスできるようになります。 これらの規則は、データベースごとに作成され、個々のデータベースに格納されます  (**master** データベースに対して、データベース レベルのファイアウォール規則を作成できます)。これらの規則は、同じ論理サーバー内の特定の (セキュリティで保護された) データベースへのアクセスを制限する場合に便利です。 データベース レベルのファイアウォール規則は、Transact-SQL ステートメントでのみ構成できます。

   > [!NOTE]
   > データベース レベルのファイアウォールの使い方がわかるチュートリアルについては、[SQL の認証と承認に関するページ](sql-database-control-access-sql-authentication-get-started.md)をご覧ください。
   >

**推奨事項:** セキュリティとデータベースの移植性を高めるため、可能な限り、データベース レベルのファイアウォール規則を使用することをお勧めします。 アクセス要件が同じデータベースが多数存在し、それぞれのデータベースの設定に時間を費やしたくない場合は、管理者向けのサーバー レベルのファイアウォール規則を使用します。

> [!Note]
> ビジネス継続性のコンテキストにおける移植可能なデータベースについては、[障害復旧の認証要件](sql-database-geo-replication-security-config.md)に関する記事を参照してください。
>

## <a name="firewall-overview"></a>ファイアウォールの概要
最初は、Azure SQL サーバーへのすべての Transact-SQL アクセスが、ファイアウォールによってブロックされます。 Azure SQL サーバーの使用を開始するには、Azure Portal に移動し、Azure SQL サーバーへのアクセスを有効にする&1; つまたは複数のサーバー レベルのファイアウォール規則を指定する必要があります。 ファイアウォール規則を使用すると、インターネットからのアクセスを許可する IP アドレス範囲、および Azure アプリケーションから Azure SQL サーバーへの接続を試みることができるかどうかを指定できます。

Azure SQL サーバーにある&1; つのデータベースにのみ選択的にアクセス許可を付与するには、必要なデータベース用のデータベース レベルの規則を作成する必要があります。 データベース ファイアウォール規則に対して、サーバー レベルのファイアウォール規則で指定された IP アドレス範囲外の IP アドレスの範囲を指定し、クライアントの IP アドレス範囲が、データベース レベルの規則で指定された範囲内に確実に収まるようにします。

インターネットおよび Azure からの接続の試行は、次の図に示されるように、Azure SQL サーバーまたは SQL Database に到達する前にファイアウォールを通過する必要があります。

   ![Diagram describing firewall configuration.][1]

## <a name="connecting-from-the-internet"></a>インターネットからの接続

コンピューターがインターネットからデータベース サーバーに接続しようとすると、ファイアウォールは、最初に、接続を要求しているデータベースについて、データベースレベルのファイアウォール規則に対する要求の送信元の IP アドレスを確認します。

* 要求の IP アドレスがデータベースレベルのファイアウォール規則で指定されたいずれかの IP アドレス範囲内にある場合は、その規則を含む SQL Database への接続が許可されます。
* 要求の IP アドレスがデータベースレベルのファイアウォール規則で指定されたいずれかの IP アドレス範囲内にない場合は、サーバーレベルのファイアウォール規則が確認されます。 要求の IP アドレスがサーバーレベルのファイアウォール規則で指定されたいずれかの IP アドレス範囲内にある場合は、接続が許可されます。 サーバーレベルのファイアウォール規則は、Azure SQL Server 上のすべての SQL Database に適用されます。  
* 要求の IP アドレスがデータベース レベルのファイアウォール規則またはサーバー レベルのファイアウォール規則で指定された IP アドレス範囲内にない場合、接続要求は失敗します。

> [!NOTE]
> ローカル コンピューターから Azure SQL Database にアクセスするには、ネットワーク上のファイアウォール、およびローカル コンピューターで、TCP ポート 1433 上の送信が許可されていることを確認します。
> 

## <a name="connecting-from-azure"></a>Azure からの接続
Azure のアプリケーションから Azure SQL Server に接続を許可するには、Azure の接続を有効にする必要があります。 Azure からアプリケーションがデータベース サーバーに接続しようとした場合、ファイアウォールは、Azure の接続が許可されていることを確認します。 開始アドレスと終了アドレスが 0.0.0.0 であるファイアウォール設定は、これらの接続が許可されていることを示します。 接続試行が許可されていない場合、要求は、Azure SQL Database サーバーに到達しません。

> [!IMPORTANT]
> このオプションは、ファイアウォールを構成して、他のお客様のサブスクリプションからの接続を含むすべての接続を許可します。 このオプションを選択する場合は、ログインおよびユーザーのアクセス許可が、承認されたユーザーのみにアクセスを制限していることを確認してください。
> 

> [!NOTE]
>  詳細については、[ADO.NET 4.5 と SQL Database における 1433 以外のポート](sql-database-develop-direct-route-ports-adonet-v12.md)に関する記事の **SQL Database の外部と内部**に関するセクションを参照してください。
>  

## <a name="creating-the-first-server-level-firewall-rule"></a>最初のサーバー レベルのファイアウォール規則の作成
最初のサーバー レベルのファイアウォール設定は、[Azure Portal](https://portal.azure.com/) を使用するか、プログラムで REST API または Azure PowerShell を使用して作成できます。 それ以降のサーバーレベルのファイアウォール規則の作成と管理も、これらの方法や Transact-SQL を使用して実行できます。 パフォーマンスを向上させるため、サーバー レベルのファイアウォール規則はデータベース レベルで一時的にキャッシュされます。 キャッシュを更新する方法については、「[DBCC FLUSHAUTHCACHE](https://msdn.microsoft.com/library/mt627793.aspx)」をご覧ください。 サーバー レベルのファイアウォール規則の詳細については、[Azure Portal を使用して Azure SQL Database ファイアウォールを構成する方法](sql-database-configure-firewall-settings.md)に関するページをご覧ください。

## <a name="creating-database-level-firewall-rules"></a>データベース レベルのファイアウォール規則の作成
 最初のサーバー レベルのファイアウォールを構成した後、特定のデータベースへのアクセスを制限することがあります。 サーバー レベルのファイアウォール規則で指定された範囲外にあるデータベース レベルのファイアウォール規則で IP アドレスの範囲を指定した場合、データベース レベルの範囲の IP アドレスを持つクライアントのみがデータベースにアクセスできます。 データベースに対し、最大 128 のデータベース レベルのファイアウォール規則を持つことができます。 マスターおよびユーザーのデータベースに対するデータベース レベルのファイアウォール規則は、Transact-SQL を介して作成、および管理できます。 データベース レベルのファイアウォール規則の構成の詳細については、「[sp_set_database_firewall_rule (Azure SQL データベース)](https://msdn.microsoft.com/library/dn270010.aspx)」をご覧ください。

## <a name="programmatically-managing-firewall-rules"></a>ファイアウォール規則のプログラムによる管理
ファイアウォール規則は、Azure Portal に加え、Transact-SQL、REST API、および Azure PowerShell を使用して、プログラムによって管理することができます。 次の表では、各メソッドで使用できるコマンド セットについて説明します。

### <a name="transact-sql"></a>Transact-SQL
| カタログ ビューまたはストアド プロシージャ | Level | 説明 |
| --- | --- | --- |
| [sys.firewall_rules](https://msdn.microsoft.com/library/dn269980.aspx) |サーバー |現在のサーバー レベルのファイアウォール規則を表示 |
| [sp_set_firewall_rule](https://msdn.microsoft.com/library/dn270017.aspx) |サーバー |サーバー レベルのファイアウォール規則を作成または更新 |
| [sp_delete_firewall_rule](https://msdn.microsoft.com/library/dn270024.aspx) |サーバー |サーバー レベルのファイアウォール規則を削除 |
| [sys.database_firewall_rules](https://msdn.microsoft.com/library/dn269982.aspx) |データベース |現在のデータベース レベルのファイアウォール規則を表示 |
| [sp_set_database_firewall_rule](https://msdn.microsoft.com/library/dn270010.aspx) |データベース |データベース レベルのファイアウォール規則を作成または更新 |
| [sp_delete_database_firewall_rule](https://msdn.microsoft.com/library/dn270030.aspx) |データベース |データベース レベルのファイアウォール規則を削除 |

### <a name="rest-api"></a>REST API
| API | Level | Description |
| --- | --- | --- |
| [ファイアウォール規則の一覧表示](https://msdn.microsoft.com/library/azure/dn505715.aspx) |サーバー |現在のサーバー レベルのファイアウォール規則を表示 |
| [ファイアウォール規則の作成](https://msdn.microsoft.com/library/azure/dn505712.aspx) |サーバー |サーバー レベルのファイアウォール規則を作成または更新 |
| [ファイアウォール規則の設定](https://msdn.microsoft.com/library/azure/dn505707.aspx) |サーバー |既存のサーバー レベルのファイアウォール規則のプロパティを更新 |
| [ファイアウォール規則の削除](https://msdn.microsoft.com/library/azure/dn505706.aspx) |サーバー |サーバー レベルのファイアウォール規則を削除 |

### <a name="azure-powershell"></a>Azure PowerShell
| コマンドレット | Level | Description |
| --- | --- | --- |
| [Get-AzureSqlDatabaseServerFirewallRule](https://msdn.microsoft.com/library/azure/dn546731.aspx) |サーバー |現在のサーバー レベルのファイアウォール規則を返す |
| [New-AzureSqlDatabaseServerFirewallRule](https://msdn.microsoft.com/library/azure/dn546724.aspx) |サーバー |新しいサーバー レベルのファイアウォール規則を作成 |
| [Set-AzureSqlDatabaseServerFirewallRule](https://msdn.microsoft.com/library/azure/dn546739.aspx) |サーバー |既存のサーバー レベルのファイアウォール規則のプロパティを更新 |
| [Remove-AzureSqlDatabaseServerFirewallRule](https://msdn.microsoft.com/library/azure/dn546727.aspx) |サーバー |サーバー レベルのファイアウォール規則を削除 |

> [!NOTE]
> ファイアウォール設定に対する変更が有効になるまでに&5; 分程度かかる可能性があります。
 
 
### <a name="faq-when-should-you-use-a-server-level-firewall-rule-and-when-should-you-use-a-database-level-firewall-rule"></a>FAQ: サーバー レベルのファイアウォール規則とデータベース レベルのファイアウォール規則の使い分けについて   
Q. 個々のデータベースのユーザーを別のデータベースから完全に分離する必要がありますか?   
  分離する必要がある場合は、データベース レベルのファイアウォール規則を使用してアクセスを許可します。 これにより、サーバー レベルのファイアウォール規則の使用を回避できます。サーバー レベルのファイアウォール規則では、すべてのデータベースへのファイアウォール経由のアクセスが許可されるので、セキュリティが低下します。   
 
Q. 指定した IP アドレスのユーザーがすべてのデータベースにアクセスする必要がありますか?   
  サーバー レベルのファイアウォール規則を使用すると、ファイアウォール規則を構成する回数を減らすことができます。   

Q. ファイアウォール規則を構成する個人またはチームが、Azure Portal、PowerShell、または REST API からのみ、アクセスが許可されていますか?   
  サーバー レベルのファイアウォール規則を使用する必要があります。 データベース レベルのファイアウォール規則は、Transact-SQL でのみ構成できます。  

Q. ファイアウォール規則を構成する個人またはチームが、データベース レベルでの高度なアクセス許可を持つことが禁止されていますか?   
  サーバー レベルのファイアウォール規則を使用します。 Transact SQL でデータベース レベルのファイアウォール規則を構成するには、データベース レベルで少なくとも `CONTROL DATABASE` のアクセス許可が必要です。  

Q. ファイアウォール規則を構成または監査する個人またはチームが、多くの (数百の) データベースを一元的に管理していますか?   
  この場合はニーズや環境によって異なります。 サーバー レベルのファイアウォール規則の方が簡単に構成できると思われますが、スクリプトを使用すればデータベース レベルで規則を構成できます。 また、サーバー レベルのファイアウォール規則を使用する場合でも、データベース レベルのファイアウォール規則の監査が必要になることもあります。これは、データベースに対する `CONTROL` のアクセス許可を持つユーザーは、データベース レベルのファイアウォール規則を作成できるためです。   

Q. サーバー レベルのファイアウォール規則とデータベース レベルのファイアウォール規則を混在させることはできますか?   
  はい。 サーバー レベルのファイアウォール規則が必要なユーザー (管理者など) と、 データベース レベルのファイアウォール規則が必要なユーザー (データベース アプリケーションのユーザーなど) がいる可能性があります。   

## <a name="troubleshooting-the-database-firewall"></a>データベース ファイアウォールのトラブルシューティング
Microsoft Azure SQL Database サービスへ期待どおりにアクセスできない場合は、次の点を検討してください。

* **ローカル ファイアウォールの構成:** Azure SQL Database へアクセスする前に、TCP ポート 1433 に対し、コンピューターでファイアウォール例外を作成することが必要な場合があります。 Azure クラウド境界内で接続を行う場合は、追加のポートをいくつか開かなければならない場合があります。 詳細については、「 **ADO.NET 4.5 と SQL Database V12 の 1433 以外のポート** 」の「 [SQL Database V12: 外部と内部](sql-database-develop-direct-route-ports-adonet-v12.md)」を参照してください。
* **ネットワーク アドレス変換 (NAT):** NAT が原因で、Azure SQL Database に接続するコンピューターで使用される IP アドレスが、コンピューターの IP 構成設定で表示される IP アドレスと異なっている場合があります。 Azure に接続するためにコンピューターで使用している IP アドレスを表示するには、ポータルにログインし、データベースをホストしているサーバーの **[構成]** タブに移動します。 **[許可された IP アドレス]** セクションに、**現在のクライアント IP アドレス**が表示されます。 **[追加]** をクリックして **[使用できる IP アドレス]** に追加し、このコンピューターのサーバーへのアクセスを許可します。
* **許可一覧に変更が反映されない:** Azure SQL Database ファイアウォールの構成に対する変更が反映されるまで最大&5; 分間の遅延が発生する場合があります。
* **ログインが許可されない、または正しくないパスワードが使用された**: Azure SQL Database サーバーでは、ログインのアクセス許可がないか、使用したパスワードが正しくない場合、Azure SQL Database サーバーへの接続は拒否されます。 ファイアウォール設定の作成は、クライアントに対し、サーバーへの接続を試行する機会のみを提供します。それぞれのクライアントは、必要なセキュリティ資格情報を提供する必要があります。 ログインを準備する方法の詳細については、「データベースの管理」、「ログイン」、および「Azure SQL Database 内のユーザー」を参照してください。
* **動的 IP アドレス:** 動的 IP アドレス指定によるインターネット接続を使用しており、ファイアウォールの通過に問題が発生している場合は、次の解決策のいずれかをお試しください。
  
  * Azure SQL Database サーバーへアクセスするクライアント コンピューターに割り当てられている IP アドレス範囲について、インターネット サービス プロバイダー (ISP) に問い合わせ、ファイアウォール規則として、IP アドレス範囲を追加してください。
  * 動的 IP アドレスの代わりに、静的 IP アドレスを取得し、ファイアウォール規則として、IP アドレス範囲を追加してください。

## <a name="next-steps"></a>次のステップ
サーバー レベルおよびデータベース レベルのファイアウォール規則の作成については、次の記事をご覧ください。

* [Azure ポータルを使用して Azure SQL Database のサーバー レベルのファイアウォール規則を構成する](sql-database-configure-firewall-settings.md)
* [TSQL を使用して Azure SQL Database ファイアウォールを構成する](sql-database-configure-firewall-settings-tsql.md)
* [PowerShell を使用して Azure SQL Database のサーバー レベルのファイアウォール規則を構成する](sql-database-configure-firewall-settings-powershell.md)
* [REST API を使用して Azure SQL Database ファイアウォールを構成する](sql-database-configure-firewall-settings-rest.md)

データベース作成のチュートリアルについては、「[初めての Azure SQL Database](sql-database-get-started.md)」をご覧ください。
オープン ソースまたはサードパーティ製のアプリケーションから Azure SQL Database に接続する方法の詳細については、 [SQL Database のクライアント クイック スタート コード サンプル](https://msdn.microsoft.com/library/azure/ee336282.aspx)に関する記事をご覧ください。
データベースに移動する方法については、 [データベースへのアクセスとログイン セキュリティの管理](https://msdn.microsoft.com/library/azure/ee336235.aspx)に関する記事をご覧ください。   
ログイン、ユーザー、ファイアウォールを作成するためのエンド ツー エンドのチュートリアルについては、「[SQL Server 認証、アクセス、データベース レベルのファイアウォール規則](sql-database-control-access-sql-authentication-get-started.md)」をご覧ください。

## <a name="additional-resources"></a>その他のリソース
* [データベースの保護](sql-database-security-overview.md)
* [SQL Server Database エンジンと Azure SQL Database のセキュリティ センター](https://msdn.microsoft.com/library/bb510589)

<!--Image references-->
[1]: ./media/sql-database-firewall-configure/sqldb-firewall-1.png

