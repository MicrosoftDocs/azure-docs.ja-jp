---
title: Azure App Service での断続的な送信接続エラーのトラブルシューティング
description: Azure App Service での断続的な接続エラーと、それに関連したパフォーマンスの問題のトラブルシューティング
author: v-miegge
manager: barbkess
ms.topic: troubleshooting
ms.date: 11/19/2020
ms.author: ramakoni
ms.custom: security-recommendations,fasttrack-edit
ms.openlocfilehash: 2b4719561ad94d54267410d0af28db6ee8d82b00
ms.sourcegitcommit: ba3a4d58a17021a922f763095ddc3cf768b11336
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/23/2021
ms.locfileid: "104799107"
---
# <a name="troubleshooting-intermittent-outbound-connection-errors-in-azure-app-service"></a>Azure App Service での断続的な送信接続エラーのトラブルシューティング

この記事は、[Azure App Service](./overview.md) での断続的な接続エラーと、それに関連したパフォーマンスの問題のトラブルシューティングに役立ちます。 このトピックでは、ソース アドレス ネットワーク変換 (SNAT) ポートの枯渇に関する詳細およびトラブルシューティング方法について説明します。 この記事の内容について不明な点がありましたら、[MSDN の Azure フォーラムと Stack Overflow フォーラム](https://azure.microsoft.com/support/forums/)で Azure エキスパートにお問い合わせください。 または、Azure サポート インシデントを送信してください。 [Azure サポートのサイト](https://azure.microsoft.com/support/options/)に移動して、 **[サポートの要求]** をクリックしてください。

## <a name="symptoms"></a>現象

Azure アプリ サービスでホストされているアプリケーションおよび関数で、次の 1 つ以上の現象が発生する可能性があります。

* サービス プランの全部または一部のインスタンスで応答時間が遅くなる。
* 断続的な 5xx または **Bad Gateway** エラー
* タイムアウト エラー メッセージ
* 外部エンドポイント (SQLDB、Service Fabric、その他のアプリ サービスなど) への接続失敗

## <a name="cause"></a>原因

接続が途切れる問題の主な原因は、新しい送信接続を作成している間に制限に達していることです。 以下のような制限に、到達している可能性があります。

* TCP 接続: 作成できる送信接続の数には制限があります。 送信接続の制限は、使用されるワーカーのサイズに関連付けられています。
* SNAT ポート: [Azure の送信接続](../load-balancer/load-balancer-outbound-connections.md)に関する記事では、SNAT ポートの制限と、送信接続への影響について説明します。 Azure では、送信元ネットワーク アドレス変換 (SNAT) とロード バランサー (顧客に公開されません) を使用して、パブリック IP アドレスと通信します。 Azure アプリ サービスの各インスタンスには、当初、**128** 個の SNAT ポートが事前に割り当てられています。 この SNAT ポートの制限は、同じアドレスとポートの組み合わせへの接続を開くことに影響します。 アドレスとポートのさまざまな組み合わせへの接続をアプリが作成する場合、SNAT ポートを使い果たすことはありません。 SNAT ポートが使い果たされるのは、同じアドレスとポートの組み合わせへの呼び出しを繰り返したときです。 解放されたポートは必要に応じて再利用できます。 Azure ネットワーク ロード バランサーは、4 分間待機した後でないと、閉じられた接続の SNAT ポートを回収しません。

アプリケーションまたは関数で新しい接続を開くペースが速いと、128 ポートの事前割り当てクォータがすぐに枯渇する可能性があります。 その場合、追加の SNAT ポートを動的に割り当てるか、回収した SNAT ポートを再利用することで新しい SNAT ポートが利用可能になるまで、アプリケーションまたは関数はブロックされます。 アプリで SNAT ポートが不足している場合は、送信接続が途切れる問題が発生します。 

## <a name="avoiding-the-problem"></a>問題の回避

SNAT ポートの制限を回避するための解決策は、いくつかあります。 具体的な内容を次に示します。

* 接続プール: 接続をプールすることで、同じアドレスとポートへの呼び出しに対して、新しいネットワーク接続を開かないようにすることができます。
* サービス エンドポイント: サービス エンドポイントで保護されているサービスには SNAT ポートの制限がありません。
* プライベート エンドポイント: プライベート エンドポイントで保護されているサービスには SNAT ポートの制限がありません。
* NAT Gateway: NAT Gateway では、これを介してトラフィックを送信するリソースが使用できる SNAT 送信ポートが 64000 個あります。

SNAT ポートの問題を回避することは、同じホストとポートへの新しい接続を繰り返し作成しないことを意味します。 接続プールは、この問題を解決するための理解しやすい方法の 1 つです。

サービス エンドポイントがサポートされる Azure サービスが宛先であれば、[リージョンの VNet 統合](./web-sites-integrate-with-vnet.md)とサービス エンドポイントまたはプライベート エンドポイントを利用し、SNAT ポート不足問題を回避できます。 リージョンの VNet 統合を使用しているとき、統合サブネットにサービス エンドポイントを配置する場合、アプリでそれらのサービスにトラフィックを送信するとき、送信 SNAT ポート制限が適用されません。 同様に、リージョンの VNet 統合とプライベート エンドポイントを使用する場合、その宛先には、送信 SNAT ポート問題が発生しません。 

送信先が Azure 外部の外部エンドポイントである場合、NAT Gateway を使用すると、64000 個の送信 SNAT ポートが提供されます。 また、他のユーザーと共有しない専用の送信アドレスが提供されます。 

可能な場合は、接続プールを使用するようにコードを改良し、すべての状況を改善します。 必ずしも、この状況を軽減するのに十分な時間をかけてコードを変更できるとは限りません。 時間内にコードを変更できない場合は、他の解決策を利用してください。 問題を解決する最善策として、すべての解決策を可能な限り組み合わせることをお勧めします。 Azure サービスにはサービス エンドポイントとプライベート エンドポイントを、それ以外には NAT Gateway を使用してみてください。 

SNAT ポートの枯渇を避けるための一般的な戦略については、**Azure の送信接続** に関するドキュメントの [問題解決セクション](../load-balancer/load-balancer-outbound-connections.md)で説明しています。 これらの戦略のうち、Azure アプリ サービスでホストされているアプリおよび関数に適用されるものは以下のとおりです。

### <a name="modify-the-application-to-use-connection-pooling"></a>接続プールを使用するようにアプリケーションを変更する

* HTTP 接続のプールについては、[HttpClientFactory による HTTP 接続のプール](/aspnet/core/performance/performance-best-practices#pool-http-connections-with-httpclientfactory)に関するページを確認してください。
* SQL Server 接続プールの詳細については、「[SQL Server の接続プール (ADO.NET)](/dotnet/framework/data/adonet/sql-server-connection-pooling)」を確認してください。
* エンティティ フレームワーク アプリケーションを使用したプールの実装については、「[DbContext プール](/ef/core/what-is-new/ef-core-2.0#dbcontext-pooling)」を確認してください。

次に示すのは、さまざまなソリューション スタックによって接続プールを実装するためのリンク集です。

#### <a name="node"></a>Node

既定では、NodeJS の接続は維持されません。 実装方法の例を含め、接続プール用の一般的なデータベースとパッケージを次に示します。

* [MySQL](https://github.com/mysqljs/mysql#pooling-connections)
* [MongoDB](https://blog.mlab.com/2017/05/mongodb-connection-pooling-for-express-applications/)
* [PostgreSQL](https://node-postgres.com/features/pooling)
* [SQL Server](https://github.com/tediousjs/node-mssql#connection-pools)

HTTP キープアライブ

* [agentkeepalive](https://www.npmjs.com/package/agentkeepalive)
* [Node.js v13.9.0 のドキュメント](https://nodejs.org/api/http.html)

#### <a name="java"></a>Java

実装方法の例を含め、JDBC 接続プールに使用される一般的なライブラリを次に示します。JDBC 接続プール。

* [Tomcat 8](https://tomcat.apache.org/tomcat-8.0-doc/jdbc-pool.html)
* [C3p0](https://github.com/swaldman/c3p0)
* [HikariCP](https://github.com/brettwooldridge/HikariCP)
* [Apache DBCP](https://commons.apache.org/proper/commons-dbcp/)

HTTP 接続プール

* [Apache の接続管理](https://hc.apache.org/httpcomponents-client-5.0.x/)
* [クラス PoolingHttpClientConnectionManager](https://hc.apache.org/httpcomponents-client-5.0.x/)

#### <a name="php"></a>PHP

PHP では接続プールがサポートされていませんが、バックエンド サーバーに対して永続的なデータベース接続を使用してみることができます。
 
* MySQL サーバー

   * 新しいバージョンの [MySQLi 接続](https://www.php.net/manual/mysqli.quickstart.connections.php)
   * 以前のバージョンの PHP の [mysql_pconnect](https://www.php.net/manual/function.mysql-pconnect.php)

* 他のデータ ソース

   * [PHP の接続管理](https://www.php.net/manual/en/pdo.connections.php)

### <a name="modify-the-application-to-reuse-connections"></a>接続を再利用するようにアプリケーションを変更する

*  Azure Functions での接続の管理に関する追加のポインターと例については、「[Azure Functions での接続の管理](../azure-functions/manage-connections.md)」を確認してください。

### <a name="modify-the-application-to-use-less-aggressive-retry-logic"></a>再試行の頻度を抑えるようにアプリケーションのロジックを変更する

* 追加のガイダンスと例については、「[再試行パターン](/azure/architecture/patterns/retry)」を確認してください。

### <a name="use-keepalives-to-reset-the-outbound-idle-timeout"></a>キープアライブを使用して送信アイドル タイムアウトをリセットする

* Node.js アプリのキープアライブを実装する場合は、「[node アプリケーションによる発信呼び出しが多すぎる](./app-service-web-nodejs-best-practices-and-troubleshoot-guide.md#my-node-application-is-making-excessive-outbound-calls)」を確認してください。

### <a name="additional-guidance-specific-to-app-service"></a>App Service 固有のその他のガイダンス:

* [ロード テスト](/azure/devops/test/load-test/app-service-web-app-performance-test)では、現実に即したデータを、安定したフィード速度でシミュレートするようにしてください。 現実的なストレス下でアプリおよび関数をテストすることで、SNAT ポートの枯渇の問題を事前に識別し、解決することができます。
* バックエンド サービスが迅速に応答を返せることを確認します。 Azure SQL データベースのパフォーマンスの問題のトラブルシューティングについては、「[Intelligent Insights を使用した Azure SQL Database のパフォーマンスに関する問題のトラブルシューティング](../azure-sql/database/intelligent-insights-troubleshoot-performance.md#recommended-troubleshooting-flow)」を参照してください。
* App Service プランをより多くのインスタンスにスケールアウトします。 スケーリングの詳細については、[Azure App Service でのアプリのスケーリング](./manage-scale-up.md)に関する記事を参照してください。 App Service プランの各ワーカー インスタンスには、いくつかの SNAT ポートが割り当てられています。 より多くのインスタンスに使用量を分散させた場合、インスタンスあたりの SNAT ポート使用数が、一意のリモート エンドポイントあたり 100 件の送信接続という推奨制限値を下回る可能性があります。
* 1 つの送信 IP アドレスが割り当てられ、接続数と SNAT ポート数の制限がずっと大きい [App Service Environment (ASE)](./environment/using-an-ase.md) への移行を検討してください。 ASE のインスタンスごとの SNAT ポートの数は、[Azure ロード バランサーの事前割り当てテーブル](../load-balancer/load-balancer-outbound-connections.md#snatporttable)に基づいています。つまり、ASE のワーカー インスタンスの数が 1 から 50 である場合、インスタンスごとに事前割り当てされているポートは 1024 であり、ASE のワーカー インスタンスの数が 51 から 100 である場合は、インスタンスごとに事前割り当てされているポートは 512 になります。

制限はワーカーのサイズによって設定されるため、送信 TCP 制限の回避は解決しやすい問題です。 制限については、[TCP 接続でのサンドボックスの VM 間数値制限](https://github.com/projectkudu/kudu/wiki/Azure-Web-App-sandbox#cross-vm-numerical-limits)に関するページで確認できます。

|制限名|説明|小 (A1)|中 (A2)|大 (A3)|分離層 (ASE)|
|---|---|---|---|---|---|
|接続|VM 全体での接続数|1920|3968|8064|16,000|

送信 TCP 制限を回避するために、ワーカーのサイズを大きくするか、水平方向にスケールアウトすることができます。

## <a name="troubleshooting"></a>トラブルシューティング

2 種類の送信接続制限と、アプリの動作について把握することで、トラブルシューティングが容易になります。 アプリが同じストレージ アカウントに対して多くの呼び出しを行うことがわかっている場合、SNAT の制限を疑うことができます。 アプリがインターネット全体のエンドポイントに対して非常に多くの呼び出しを作成する場合、VM の制限に達していることを疑います。

アプリケーションの動作の把握が不十分で原因をすぐに特定できない場合、特定に役立ついくつかのツールと手法を App Service で利用できます。

### <a name="find-snat-port-allocation-information"></a>SNAT ポート割り当て情報の検索

[App Service 診断](./overview-diagnostics.md)を使用して、SNAT ポート割り当て情報を検索したり、App Service サイトの SNAT ポート割り当てメトリックを監視したりできます。 SNAT ポート割り当て情報を検索するには、次の手順に従います。

1. App Service 診断にアクセスするには、[Azure portal](https://portal.azure.com/) 上の App Service Web アプリまたは App Service 環境に移動します。 左側のナビゲーションで、 **[問題の診断と解決]** を選択します。
2. [Availability and Performance] (可用性とパフォーマンス) カテゴリを選択します。
3. カテゴリの下にある使用可能なタイルの一覧から [SNAT Port Exhaustion] (SNAT ポート枯渇) タイルを選択します。 128 未満に維持することをお勧めします。
必要な場合、続けてサポート チケットを開くことができます。このとき、サポート エンジニアがお客様に代わってバックエンドからメトリックを取得します。

SNAT ポート使用数はメトリックとして取得できないため、SNAT ポート使用数に基づいた自動スケールはできず、SNAT ポート割り当てメトリックに基づいて自動スケールを構成することもできません。

### <a name="tcp-connections-and-snat-ports"></a>TCP 接続と SNAT ポート

TCP 接続と SNAT ポートは直接関連していません。 TCP 接続の使用数の検出機能は、App Service サイトの [問題の診断と解決] ブレードに含まれています。 "TCP connections" という語句を検索して見つけます。

* SNAT ポートは外部ネットワーク フローにのみ使用されますが、TCP 接続の合計にはローカル ループバック接続が含まれます。
* プロトコル、IP アドレス、またはポートのいずれかでフローが異なる場合、異なるフローで SNAT ポートを共有できます。 TCP 接続メトリックは、すべての TCP 接続をカウントします。
* TCP 接続の制限は、ワーカー インスタンス レベルで行われます。 Azure ネットワークの送信負荷分散では、SNAT ポート制限に TCP 接続メトリックを使用しません。
* TCP 接続の制限については、[TCP 接続でのサンドボックスの VM 間数値制限](https://github.com/projectkudu/kudu/wiki/Azure-Web-App-sandbox#cross-vm-numerical-limits)に関するページで説明しています。

|制限名|説明|小 (A1)|中 (A2)|大 (A3)|分離層 (ASE)|
|---|---|---|---|---|---|
|接続|VM 全体での接続数|1920|3968|8064|16,000|

### <a name="webjobs-and-database-connections"></a>WebJob とデータベース接続
 
SNAT ポートが枯渇していて、WebJob が SQL Database に接続できない場合に、個別の Web アプリケーション プロセスによって開かれている接続の数を示すメトリックはありません。 問題のある WebJob を見つけるには、いくつかの WebJob を別の App Service プランに移動し、状況が改善するかどうか、またはいずれかのプランで問題が継続するかどうかを確認します。 問題のある WebJob が見つかるまで、このプロセスを繰り返します。

### <a name="using-snat-ports-sooner"></a>SNAT ポートの使用を早める

すべての SNAT ポートは次の条件に従って解放され、動作は仕様であるため、Azure の設定を変更して使用済み SNAT ポートの解放を早めることはできません。
 
* サーバーまたはクライアントのどちらかが FINACK を送信すると、240 秒後に [SNAT ポートは解放されます](../load-balancer/load-balancer-outbound-connections.md)。
* RST が送信されると、SNAT ポートは 15 秒後に解放されます。
* アイドル タイムアウトに達すると、ポートは解放されます。
 
## <a name="additional-information"></a>関連情報

* [App Service を使用した SNAT](https://4lowtherabbit.github.io/blogs/2019/10/SNAT/)
* [Azure App Service でのアプリのパフォーマンス低下に関する問題のトラブルシューティング](./troubleshoot-performance-degradation.md)
