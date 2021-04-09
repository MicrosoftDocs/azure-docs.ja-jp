---
title: マルチテナント アプリの例 - Wingtip SaaS
description: Azure SQL Database を使用する、サンプルのマルチ テナント アプリケーション (Wingtip Tickets SaaS サンプル) をインストールして実行するための手順とガイダンスを示します。
services: sql-database
ms.service: sql-database
ms.subservice: scenario
ms.custom: seo-lt-2019, sqldbrb=1
ms.devlang: ''
ms.topic: conceptual
author: stevestein
ms.author: sstein
ms.reviewer: ''
ms.date: 12/18/2018
ms.openlocfilehash: aa9215fa001fb117000eb6a68867ddd46fac9b92
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "92780327"
---
# <a name="general-guidance-for-working-with-wingtip-tickets-sample-saas-apps"></a>Wingtip Tickets サンプル SaaS アプリを使用する場合の一般的なガイダンス
[!INCLUDE[appliesto-sqldb](../includes/appliesto-sqldb.md)]

この記事には、Azure SQL Database を使う Wingtip Tickets サンプル SaaS アプリケーションを実行する場合の一般的なガイダンスが含まれます。

## <a name="download-and-unblock-the-wingtip-tickets-saas-scripts"></a>Wingtip Tickets SaaS スクリプトのダウンロードとブロック解除

実行可能なコンテンツ (スクリプト、dll) は、zip ファイルが外部ソースからダウンロードされ、抽出されると、Windows によってブロックされる場合があります。 zip ファイルからスクリプトを抽出するとき、**以下の手順に従って、.zip ファイルをブロック解除してから抽出します**。 これにより、スクリプトの実行が許可されます。

1. Wingtip Tickets SaaS GitHub リポジトリで、探索するデータベース テナント パターンを参照します。
    - [WingtipTicketsSaaS-StandaloneApp](https://github.com/Microsoft/WingtipTicketsSaaS-StandaloneApp)
    - [WingtipTicketsSaaS-DbPerTenant](https://github.com/Microsoft/WingtipTicketsSaaS-DbPerTenant)
    - [WingtipTicketsSaaS-MultiTenantDb](https://github.com/Microsoft/WingtipTicketsSaaS-MultiTenantDb)
2. **[クローンまたはダウンロード]** をクリックします。
3. **[Download zip]** をクリックして、ファイルを保存します。
4. zip ファイルを右クリックし、 **[プロパティ]** を選びます。 zip ファイルの名前は、リポジトリ名に対応します。 (例: _WingtipTicketsSaaS-DbPerTenant-master.zip_)
5. **[全般]** タブで **[ブロックの解除]** を選択します。
6. **[OK]** をクリックします。
7. ファイルを解凍します。

スクリプトは、 *..\\Learning Modules* フォルダーにあります。


## <a name="working-with-the-wingtip-tickets-powershell-scripts"></a>Wingtip Tickets PowerShell スクリプトを操作する

サンプルを最大限に活用するには、指定されたスクリプトを詳細に把握する必要があります。 ブレークポイントを使ってスクリプトをステップ実行し、さまざまな SaaS パターンがどのように実装されているかを調べます。 指定されたスクリプトおよびモジュールを簡単にステップ実行してしっかりと理解するには、[PowerShell ISE](/powershell/scripting/components/ise/introducing-the-windows-powershell-ise) の使用をお勧めします。

### <a name="update-the-configuration-file-for-your-deployment"></a>デプロイの構成ファイルを更新する

次の手順に従って、デプロイ時に設定したリソース グループとユーザー値で **UserConfig.psm1** ファイルを編集します。

1. *PowerShell ISE* を開き、...\\Learning Modules\\*UserConfig.psm1* をロードします。
2. *ResourceGroupName* と *Name* をデプロイの固有の値で更新します (10 行目および 11 行目のみ)。
3. 変更を保存します。

これらの値を設定しておくと、これらのデプロイ固有の値をスクリプト毎に更新する必要がなくなります。

### <a name="execute-the-scripts-by-pressing-f5"></a>F5 キーを押してスクリプトを実行する

複数のスクリプトでは *$PSScriptRoot* を使用してフォルダー間を移動します。 *$PSScriptRoot* が評価されるのは、**F5** キーを押してスクリプトが実行される場合のみです。    選択項目を強調表示して実行する (**F8** キー) とエラーが発生するため、スクリプトの実行時には **F5** キーを押してください。

### <a name="step-through-the-scripts-to-examine-the-implementation"></a>スクリプトをステップスルーして実装を確認する

スクリプトを理解する最善の方法は、スクリプトをステップ実行して内容を確認することです。 わかりやすいワークフローの概要を提示している、組み込みの **Demo-** スクリプトを確認してください。 **Demo-** スクリプトは、各タスクを完了するために必要なステップを示しているので、ブレークポイントを設定して個々のコールをより詳しく検証し、さまざまな SaaS パターンの実装の詳細を確認してください。

PowerShell スクリプトの調査およびステップ実行に関するヒント

- PowerShell ISE で **Demo-** スクリプトを開きます。
- **F5** キーを使って実行または続行します ( *$PSScriptRoot* は選択したスクリプトの実行時に評価されないため、**F8** キーの使用はお勧めできません)。
- 行をクリックまたは選択して **F9** キーを押し、ブレークポイントを設定します。
- **F10** キーを使用して、関数またはスクリプトの呼び出しをステップ オーバーします。
- **F11** キーを使用して、関数またはスクリプトの呼び出しにステップ インします。
- **Shift + F11** キーを使用して、現在の関数またはスクリプトの呼び出しからステップ アウトします。


## <a name="explore-database-schema-and-execute-sql-queries-using-ssms"></a>SSMS を使用してデータベース スキーマを調べ、SQL クエリを実行する

アプリケーション サーバーとデータベースに接続して参照するには、[SQL Server Management Studio (SSMS)](/sql/ssms/download-sql-server-management-studio-ssms) を使用します。

デプロイには、接続先となるテナントとカタログ サーバーが最初に用意されています。 サーバーの名前は、データベース テナント パターンに依存します (詳細については以下を参照してください)。

   - **スタンドアロン アプリケーション:** テナントごとのサーバー (例: *contosoconcerthall-&lt;ユーザー&gt;* サーバー) および *catalog-sa-&lt;ユーザー&gt;*
   - **テナントごとのデータベース:** *tenants1-dpt-&lt;ユーザー&gt;* および *catalog-dpt-&lt;ユーザー&gt;* サーバー
   - **マルチテナント データベース:** *tenants1-mt-&lt;ユーザー&gt;* および *catalog-mt-&lt;ユーザー&gt;* サーバー

デモ接続を確実に成功させるために、すべてのサーバーに、すべての IP を許可する[ファイアウォール規則](firewall-configure.md)があります。


1. *SSMS* を開き、テナントに接続します。 サーバーの名前は、選択したデータベース テナント パターンに依存します (詳細については以下を参照してください)。
    - **スタンドアロン アプリケーション:** 個々のテナントのサーバー (例: *contosoconcerthall-&lt;ユーザー&gt;.database.windows.net*)
    - **テナントごとのデータベース:** *tenants1-dpt-&lt;ユーザー&gt;.database.windows.net*
    - **マルチテナント データベース:** *tenants1-mt-&lt;ユーザー&gt;.database.windows.net*
2. **[接続]**  >  **[データベース エンジン...]** をクリックします。

   ![カタログ サーバー](./media/saas-tenancy-wingtip-app-guidance-tips/connect.png)

3. デモの資格情報は次のとおりです。ログイン = *developer*、パスワード = *P\@ssword1*

    次の図では、"*テナントごとのデータベース*" パターンのログインを示します。
    ![connection](./media/saas-tenancy-wingtip-app-guidance-tips/tenants1-connect.png)



4. 手順 2 と 3 を繰り返して、カタログ サーバーに接続します (選んだデータベース テナント パターンに基づく具体的なサーバー名は以下を参照)
    - **スタンドアロン アプリケーション:** *catalog-sa-&lt;ユーザー&gt;.database.windows.net*
    - **テナントごとのデータベース:** *catalog-dpt-&lt;ユーザー&gt;.database.windows.net*
    - **マルチテナント データベース:** *catalog-mt-&lt;ユーザー&gt;.database.windows.net*


接続に成功すると、すべてのサーバーが表示されます。 データベースの一覧は、プロビジョニングしたテナントに応じて異なる場合があります。

次の図では、"*テナントごとのデータベース*" パターンのログインを示します。

![オブジェクト エクスプローラー](./media/saas-tenancy-wingtip-app-guidance-tips/object-explorer.png)



## <a name="next-steps"></a>次のステップ
- [Wingtip Tickets SaaS スタンドアロン アプリケーションをデプロイする](./saas-standaloneapp-get-started-deploy.md)
- [Wingtip Tickets SaaS Database Per Tenant アプリケーションのデプロイ](./saas-dbpertenant-get-started-deploy.md)
- [Wingtip Tickets SaaS マルチテナント データベース アプリケーションをデプロイする](./saas-multitenantdb-get-started-deploy.md)