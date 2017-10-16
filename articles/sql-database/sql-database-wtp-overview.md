---
title: "Azure SQL Database マルチテナント アプリの例 - Wingtip SaaS | Microsoft Docs"
description: "Azure SQL Database を使用するマルチテナント アプリケーションのサンプルを使って、Wingtip SaaS の例について説明します"
keywords: "SQL データベース チュートリアル"
services: sql-database
author: stevestein
manager: craigg
ms.service: sql-database
ms.custom: scale out apps
ms.workload: data-management
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/09/2017
ms.author: sstein
ms.openlocfilehash: 68a9d97a881f3a7628a08b66091c3feb4c4dbbfe
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/11/2017
---
# <a name="introduction-to-a-sql-database-multi-tenant-saas-app-example"></a>SQL Database のマルチ テナント SaaS アプリの例の概要

*Wingtip SaaS* アプリケーションは、SQL Database の独自のメリットを示すためのサンプルのマルチテナント アプリです。 このアプリは、SaaS アプリケーション パターンとしてテナント単位のデータベースを使用して、複数のテナントにサービスを提供します。 アプリの目的は、複数の SaaS の設計と管理のパターンを含む各種の SaaS シナリオを実現している Azure SQL Database の機能を紹介することにあります。 このアプリをすぐに使い始めるために、Wingtip SaaS アプリを 5 分以内でデプロイできます。

アプリケーションのソース コードと管理スクリプトは、[WingtipSaaS](https://github.com/Microsoft/WingtipSaaS) GitHub リポジトリから入手できます。 スクリプトを実行するには、[Learning Modules](#download-and-unblock-the-wingtip-saas-scripts) フォルダーをローカル コンピューターにダウンロードします。

## <a name="sql-database-wingtip-saas-tutorials"></a>SQL Database Wingtip SaaS のチュートリアル

アプリをデプロイしたら、初期デプロイを前提とする以下のチュートリアルを参照してください。 これらのチュートリアルでは、SQL Database、SQL Data Warehouse、およびその他の Azure サービスに組み込まれた機能を活用した一般的な SaaS パターンを確認できます。 チュートリアルには、PowerShell スクリプトとその詳細な説明が含まれています。これらを利用すると、SaaS 管理パターンの理解、およびお使いのアプリケーションでの同じ SaaS 管理パターンの実装を大幅に簡略化できます。


| チュートリアル | Description |
|:--|:--|
|[Wingtip SaaS アプリケーションをデプロイして調査する](sql-database-saas-tutorial.md)| **ここから開始** Wingtip SaaS アプリケーションをお使いの Azure サブスクリプションにデプロイして調査する |
|[テナントのプロビジョニングおよびカタログ登録を行う](sql-database-saas-tutorial-provision-and-catalog.md)| アプリケーションからカタログ データベースを使用するテナントに接続する方法、カタログがテナントをデータにマッピングする方法について説明します。 |
|[パフォーマンスを監視および管理する](sql-database-saas-tutorial-performance-monitoring.md)| SQL Database の監視機能の使用方法と、パフォーマンスのしきい値が超過した場合のアラートの設定方法について説明します。 |
|[Log Analytics (OMS) を使って監視する](sql-database-saas-tutorial-log-analytics.md) | [Log Analytics](../log-analytics/log-analytics-overview.md) を使って、複数のプールにある大量のリソースを監視する方法を説明します。 |
|[シングル テナントを復元する](sql-database-saas-tutorial-restore-single-tenant.md)| テナント データベースを前のポイント イン タイムに復元する方法を説明します。 既存のテナント データベースをオンラインにしたまま、並列のデータベースに復元する手順も含まれています。 |
|[テナント スキーマを管理する](sql-database-saas-tutorial-schema-management.md)| すべての Wingtip SaaS テナントにあるスキーマの更新および参照データの更新を行う方法について説明します。 |
|[アドホック分析を実行する](sql-database-saas-tutorial-adhoc-analytics.md) | アドホック分析データベースを作成し、すべてのテナントに対してリアルタイムに配布されたクエリを実行します。  |
|[テナント分析を実行する](sql-database-saas-tutorial-tenant-analytics.md) | 実行中のオフライン分析クエリに対応する分析データベースまたはデータ ウェアハウスに、テナント データを抽出します。 |



## <a name="application-architecture"></a>アプリケーションのアーキテクチャ

Wingtip SaaS アプリでは、テナント単位のデータベース モデルを使用します。また、SQL エラスティック プールを使用して効率を最大化します。 テナントをプロビジョニングしてデータにマッピングするために、カタログ データベースが使用されます。 主要な Wingtip SaaS アプリケーションは、3 つのサンプル テナントと 1 つのカタログ データベースを備えたプールを使用します。 多数の Wingtip SaaS チュートリアルを完了すると、分析データベース、複数データベース スキーマ管理などを導入することで、結果的に初期デプロイへのアドオンになります。


![Wingtip SaaS のアーキテクチャ](media/sql-database-wtp-overview/app-architecture.png)


チュートルアルを実施してアプリを操作する際は、SaaS パターンに注目することが重要です。これは、SaaS パターンがデータ層に関連付けられているためです。 つまり、データ層に注目し、アプリ自体を過剰に分析しないでください。 これらの SaaS パターンの実装を理解することが、特定のビジネス要件に必要な変更を考慮しながら、アプリケーションにこれらのパターンを実装するための鍵となります。

## <a name="download-and-unblock-the-wingtip-saas-scripts"></a>Wingtip SaaS スクリプトのダウンロードとブロック解除

実行可能なコンテンツ (スクリプト、dll) は、zip ファイルが外部ソースからダウンロードされ、抽出されると、Windows によってブロックされる場合があります。 zip ファイルからスクリプトを抽出するとき、***以下の手順に従って、.zip ファイルをブロック解除してから抽出します***。 これにより、スクリプトの実行が許可されます。

1. [Wingtip SaaS GitHub のリポジトリ](https://github.com/Microsoft/WingtipSaaS)を参照します。
1. **[複製またはダウンロード]** をクリックします。
1. **[Download ZIP]** をクリックし、ファイルを保存します。
1. **WingtipSaaS master.zip** ファイルを右クリックし、**[プロパティ]** を選択します。
1. **[全般]** タブで **[ブロックの解除]** を選択します。
1. **[OK]**をクリックします。
1. ファイルを解凍します。

スクリプトは、*..\\WingtipSaaS-master\\Learning Modules* フォルダーにあります。


## <a name="working-with-the-wingtip-saas-powershell-scripts"></a>Wingtip SaaS PowerShell スクリプトを操作する

サンプルを最大限に活用するには、指定されたスクリプトを詳細に把握する必要があります。 ブレークポイントを使用してスクリプトをステップ実行し、さまざまな SaaS パターンがどのように実装されているか詳細に調べます。 指定されたスクリプトおよびモジュールを簡単にステップ実行してしっかりと理解するには、[PowerShell ISE](https://msdn.microsoft.com/powershell/scripting/core-powershell/ise/introducing-the-windows-powershell-ise) の使用をお勧めします。

### <a name="update-the-configuration-file-for-your-deployment"></a>デプロイの構成ファイルを更新する

次の手順に従って、デプロイ時に設定したリソース グループとユーザー値で **UserConfig.psm1** ファイルを編集します。

1. *PowerShell ISE* を開き、...\\Learning Modules\\*UserConfig.psm1* をロードします。 
1. *ResourceGroupName*と*Name*をデプロイの固有の値で更新します (10 行目および 11 行目のみ)。
1. 変更を保存します。

これらの値を設定しておくと、これらのデプロイ固有の値をスクリプト毎に更新する必要がなくなります。

### <a name="execute-scripts-by-pressing-f5"></a>F5 キーを押してスクリプトを実行する

複数のスクリプトでは *$PSScriptRoot* を使用してフォルダー間を移動します。*$PSScriptRoot* が評価されるのは、**F5** キーを押してスクリプトが実行される場合のみです。  選択項目を強調表示して実行する (**F8** キー) とエラーが発生するため、スクリプトの実行時には **F5** キーを押してください。

### <a name="step-through-the-scripts-to-examine-the-implementation"></a>スクリプトをステップスルーして実装を確認する

スクリプトを理解する最善の方法は、スクリプトをステップ実行して内容を確認することです。 わかりやすいワークフローの概要を提示している、組み込みの **Demo-** スクリプトを確認してください。 **Demo-** スクリプトは、各タスクを完了するために必要なステップを示しているので、ブレークポイントを設定して個々のコールをより詳しく検証し、さまざまな SaaS パターンの実装の詳細を確認してください。

PowerShell スクリプトの調査およびステップ実行に関するヒント

* PowerShell ISE で **Demo-** スクリプトを開きます。
* **F5** キーを使って実行または続行します (*$PSScriptRoot* は選択したスクリプトの実行時に評価されないため、**F8** キーの使用はお勧めできません)。
* 行をクリックまたは選択して **F9** キーを押し、ブレークポイントを設定します。
* **F10** キーを使用して、関数またはスクリプトの呼び出しをステップ オーバーします。
* **F11** キーを使用して、関数またはスクリプトの呼び出しにステップ インします。
* **Shift + F11** キーを使用して、現在の関数またはスクリプトの呼び出しからステップ アウトします。


## <a name="explore-database-schema-and-execute-sql-queries-using-ssms"></a>SSMS を使用してデータベース スキーマを調べ、SQL クエリを実行する

アプリケーション サーバーとデータベースに接続して参照するには、[SQL Server Management Studio (SSMS)](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms) を使用します。

デプロイには、接続先となる 2 つの SQL Database サーバー (*tenants1-&lt;ユーザー&gt;* サーバーと *catalog-&lt;ユーザー&gt;* サーバー) が最初に用意されています。 デモ接続を確実に成功させるために、どちらのサーバーにも、すべての IP を許可する[ファイアウォール規則](sql-database-firewall-configure.md)があります。


1. *SSMS* を開き、*tenants1-&lt;ユーザー&gt;.database.windows.net* サーバーに接続します。
1. **[接続]** > **[データベース エンジン...]** をクリックします。

   ![カタログ サーバー](media/sql-database-wtp-overview/connect.png)

1. デモの資格情報は、ログイン = *developer*、パスワード = *P@ssword1* です。

   ![connection](media\sql-database-wtp-overview\tenants1-connect.png)

1. 手順 2 と 3 を繰り返し、*catalog-&lt;ユーザー&gt;.database.windows.net* サーバーに接続します。

接続に成功すると、両方のサーバーが表示されます。 データベースの一覧は、プロビジョニングしたテナントに応じて異なる場合があります。

![オブジェクト エクスプローラー](media/sql-database-wtp-overview/object-explorer.png)



## <a name="next-steps"></a>次のステップ

[Wingtip SaaS アプリケーションのデプロイ](sql-database-saas-tutorial.md)