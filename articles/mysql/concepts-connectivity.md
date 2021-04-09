---
title: 一時的な接続エラー - Azure Database for MySQL
description: 一時的な接続エラーを処理して Azure Database for MySQL に効率的に接続する方法について説明します。
keywords: MySQL 接続,接続文字列,接続の問題,一時的なエラー,接続エラー,効率的に接続する
author: savjani
ms.author: pariks
ms.service: mysql
ms.topic: conceptual
ms.date: 3/18/2020
ms.openlocfilehash: 89673c14c38947dc5aeb91cacde1eb2755e84138
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "94542611"
---
# <a name="handle-transient-errors-and-connect-efficiently-to-azure-database-for-mysql"></a>一時的なエラーを処理して Azure Database for MySQL に効率的に接続する

この記事では、一時的なエラーを処理して Azure Database for MySQL に効率的に接続する方法について説明します。

## <a name="transient-errors"></a>一時的なエラー

一時的なエラーとは自然に解決するエラーのことで、一過性の障害とも呼ばれます。 これらのエラーは、データベース サーバーへの接続が切断される症状として現れるのが最も典型的です。 また、サーバーへの新しい接続を開くことができないこともあります。 たとえばハードウェアやネットワークの障害が起こると、一時的なエラーが発生することがあります。 新しいバージョンの PaaS サービスがロールアウトされていることが原因であることもあります。こうした事象の大半は、システムにより、60 秒かからず自動的に緩和されます。 クラウドのアプリケーションを設計および開発するベスト プラクティスでは、一時的なエラーを想定します。 どのコンポーネントでも常に起こりうると想定し、そうした状況に対処するための適切なロジックを設けるのです。

## <a name="handling-transient-errors"></a>一時的エラーの処理

一時的なエラーは、再試行ロジックを使用して処理する必要があります。 考慮に入れるべき状況は以下のとおりです。

* 接続を開こうとするときにエラーが発生する
* アイドル状態の接続がサーバー側で切断される コマンドを発行しようとしても実行できない
* 現在コマンドを実行しているアクティブな接続が切断される。

1 つ目と 2 つ目は、かなり対処しやすいケースです。 もう一度接続を開いてみてください。 成功するようなら、一時的なエラーがシステム側で既に緩和されています。 再び Azure Database for MySQL を使用することができます。 接続を再試行する前に待ち時間を設けることをお勧めします。 初回再試行に失敗した場合は、いったん手を引きます。 そうすることで、システムは利用可能なすべてのリソースを使用してエラー状態を克服することができます。 推奨されるパターンを次に示します。

* 初回再試行の前には 5 秒間待機します。
* 以降再試行するたびに、60 秒を上限として、待ち時間を指数関数的に増やします。
* 操作が失敗したとアプリケーションで見なされる最大再試行回数を設定します。

アクティブなトランザクションが進行している接続でエラーが発生した場合、回復に向けた正しい対処への難易度が上がります。 次の 2 つのケースがあります。トランザクションが本来読み取り専用である場合は、接続を再度開いてトランザクションを再試行するのが安全です。 ただし、トランザクションがデータベースにも書き込んでいた場合は、そのトランザクションがロールバックされたかどうか、または一時的なエラーが発生する前に成功したかどうかを判定する必要があります。 その場合は、データベース サーバーからコミットの確認を受信していないだけである可能性があります。

その判断を行う 1 つの方法として、すべての再試行に使用される一意の ID をクライアントで生成することが考えられます。 この一意の ID をトランザクションの一環としてサーバーに渡し、一意制約のある列に格納します。 こうすることで、トランザクションを安全に再試行することができます。 以前のトランザクションがロールバックされ、クライアントによって生成された一意の ID がシステム内にまだ存在しない場合、これは成功します。 一意の ID が既に格納されている場合は、前回のトランザクションが正常に完了していることになるので、再試行は失敗し、キーが重複している旨の違反が表示されます。

ご使用のプログラムがサード パーティのミドルウェアを介して Azure Database for MySQL と通信する場合、一時エラーに対応した再試行ロジックがそのミドルウェアに備わっているかどうかをベンダーに確認してください。

再試行ロジックは必ずテストしてください。 たとえば、Azure Database for MySQL サーバーのコンピューティング リソースをスケールアップまたはスケールダウンしている間にコードを実行してみてください。 この操作中に発生した短時間のダウンタイムには、アプリケーションで問題なく対処する必要があります。

## <a name="connect-efficiently-to-azure-database-for-mysql"></a>Azure Database for MySQL に効率的に接続する

データベース接続は制限されたリソースであるため、Azure Database for MySQL にアクセスするための接続プールを効率的に使用すると、パフォーマンスが最適化されます。 次のセクションでは、接続プールまたは永続的な接続を使用して Azure Database for MySQL により効率的にアクセスする方法について説明します。

## <a name="access-databases-by-using-connection-pooling-recommended"></a>接続プールを使用してデータベースにアクセスする (推奨)

データベース接続の管理が、全体としてアプリケーションのパフォーマンスに大きな影響を与える場合があります。 アプリケーションのパフォーマンスを最適化するには、接続が確立される回数や、主要なコード パスで接続を確立するための時間を削減することを目標にする必要があります。 データベース接続プールまたは永続的な接続を使用して Azure Database for MySQL に接続することを強くお勧めします。 データベース接続プールは、データベース接続の作成、管理、および割り当てに対応します。 プログラムはデータベース接続を要求するときに、新しい接続の作成ではなく、既存のアイドル状態のデータベース接続の割り当てを優先します。 プログラムがデータベース接続の使用を完了すると、その接続は単純に閉じられるのではなく、それ以降の使用に備えて回復されます。

わかりやすくするために、この記事では、例として JAVA を使用する [1 つのサンプル コード](./sample-scripts-java-connection-pooling.md)を示しています。 詳細については、[Apache の一般的な DBCP](https://commons.apache.org/proper/commons-dbcp/) に関するページを参照してください。

> [!NOTE]
> サーバーは、リソースを解放するために、一定の期間アイドル状態にあった接続を閉じるためのタイムアウト メカニズムを構成します。 永続的な接続を使用している場合は、それらの接続の効率を確認するための検証システムを設定するようにしてください。 詳細については、[永続的な接続の効率を確認するためのクライアント側での検証システムの構成](concepts-connectivity.md#configure-verification-mechanisms-in-clients-to-confirm-the-effectiveness-of-persistent-connections)に関するセクションを参照してください。

## <a name="access-databases-by-using-persistent-connections-recommended"></a>永続的な接続を使用してデータベースにアクセスする (推奨)

永続的な接続の概念は、接続プールの概念に似ています。 短い接続を永続的な接続に置き換えるにはコードの少しの変更だけで済みますが、多くの標準的なアプリケーション シナリオでのパフォーマンス向上の点から見ると大きな影響を与えます。

## <a name="access-databases-by-using-wait-and-retry-mechanism-with-short-connections"></a>短い接続での待機および再試行メカニズムを使用してデータベースにアクセスする

リソース制限がある場合は、データベース プーリングまたは永続的な接続を使用してデータベースにアクセスすることを強くお勧めします。 アプリケーションで短い接続を使用しており、コンカレント接続数の上限に近づくと接続エラーが発生する場合は、待機および再試行メカニズムを試みることができます。 最初の試みの後に (待機時間の短い) 適切な待機時間を設定できます。 それ以降、複数回イベントを試みることができます。

## <a name="configure-verification-mechanisms-in-clients-to-confirm-the-effectiveness-of-persistent-connections"></a>永続的な接続の効率を確認するためにクライアントで検証メカニズムを構成する

サーバーは、リソースを解放するために、一定の期間アイドル状態にあった接続を閉じるためのタイムアウト メカニズムを構成します。 クライアントが再度データベースにアクセスした場合は、クライアントとサーバーの間の新しい接続要求を作成することと同等になります。 使用中の接続の効率を確認するには、クライアントで検証メカニズムを構成します。 次の例に示すように、Tomcat の JDBC 接続プールを使用してこの検証メカニズムを構成できます。

TestOnBorrow パラメーターを設定することによって、新しい要求が発生したとき、接続プールによってすべての使用可能なアイドル接続の効率が自動的に検証されます。 このような接続が効率的である場合は、それが直接返されます。それ以外の場合は、接続プールによって接続が取り消されます。 その後、接続プールは新しい効率的な接続を作成し、それを返します。 このプロセスにより、データベースが効率的にアクセスされることが保証されます。 

特定の設定については、[JDBC 接続プールの公式の導入ドキュメント](https://tomcat.apache.org/tomcat-7.0-doc/jdbc-pool.html#Common_Attributes)を参照してください。 主に設定する必要があるのは、TestOnBorrow (true に設定)、ValidationQuery (SELECT 1 に設定)、および ValidationQueryTimeout (1 に設定) の 3 つのパラメーターです。 特定のサンプル コードを次に示します。

```java
public class SimpleTestOnBorrowExample {
      public static void main(String[] args) throws Exception {
          PoolProperties p = new PoolProperties();
          p.setUrl("jdbc:mysql://localhost:3306/mysql");
          p.setDriverClassName("com.mysql.jdbc.Driver");
          p.setUsername("root");
          p.setPassword("password");
            // The indication of whether objects will be validated by the idle object evictor (if any). 
            // If an object fails to validate, it will be dropped from the pool. 
            // NOTE - for a true value to have any effect, the validationQuery or validatorClassName parameter must be set to a non-null string. 
          p.setTestOnBorrow(true); 

            // The SQL query that will be used to validate connections from this pool before returning them to the caller.
            // If specified, this query does not have to return any data, it just can't throw a SQLException.
          p.setValidationQuery("SELECT 1");

            // The timeout in seconds before a connection validation queries fail. 
            // This works by calling java.sql.Statement.setQueryTimeout(seconds) on the statement that executes the validationQuery. 
            // The pool itself doesn't timeout the query, it is still up to the JDBC driver to enforce query timeouts. 
            // A value less than or equal to zero will disable this feature.
          p.setValidationQueryTimeout(1);
            // set other useful pool properties.
          DataSource datasource = new DataSource();
          datasource.setPoolProperties(p);

          Connection con = null;
          try {
            con = datasource.getConnection();
            // execute your query here
          } finally {
            if (con!=null) try {con.close();}catch (Exception ignore) {}
          }
      }
  }
```

## <a name="next-steps"></a>次のステップ

* [Azure Database for MySQL への接続に関する問題のトラブルシューティング](howto-troubleshoot-common-connection-issues.md)
