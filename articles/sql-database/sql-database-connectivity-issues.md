---
title: "SQL 接続エラー、一時エラーの修正 | Microsoft Docs"
description: "Azure SQL Database での SQL 接続エラーまたは一時エラーのトラブルシューティング、診断、防止の方法について説明します。 "
keywords: "sql 接続, 接続文字列, 接続の問題, 一時エラー, 接続エラー"
services: sql-database
documentationcenter: 
author: dalechen
manager: cshepard
editor: 
ms.assetid: efb35451-3fed-4264-bf86-72b350f67d50
ms.service: sql-database
ms.custom: troubleshoot
ms.workload: sql-database
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/20/2017
ms.author: daleche
translationtype: Human Translation
ms.sourcegitcommit: a6489fd5ff5e60bc3a1c06485d330556250c21cd
ms.openlocfilehash: eddbdda847d7d0cc12f3e1c6128ce76d15f6f39f
ms.lasthandoff: 03/02/2017


---
# <a name="troubleshoot-diagnose-and-prevent-sql-connection-errors-and-transient-errors-for-sql-database"></a>SQL Database の SQL 接続エラーと一時エラーのトラブルシューティング、診断、防止
この記事では、クライアント アプリケーションが Azure SQL Database とやり取りする際に発生する接続エラーと一時エラーを防止、トラブルシューティング、診断、軽減する方法について説明します。 再試行ロジックの構成方法、接続文字列の作成方法、およびその他の接続設定の調整方法について説明します。

<a id="i-transient-faults" name="i-transient-faults"></a>

## <a name="transient-errors-transient-faults"></a>一時エラー (一過性の障害)
一時エラー (一過性の障害) には、すぐに自動的に解決する根本原因があります。 一時エラーを起こす偶発的原因として、Azure システムが、各種ワークロードの負荷分散を行うために行うハードウェア リソースの瞬間的切り替えがあります。 多くの場合、この再構成イベントのほとんどは 60 秒以内に完了します。 この再構成の進行中、Azure SQL Database への接続の問題が発生する場合があります。 Azure SQL Database に接続するアプリケーションは、これらの一時エラーを想定し、一時エラーをアプリケーション エラーとしてユーザーに示すのではなく、コードで再試行ロジックを実装して処理するように構築する必要があります。

クライアント プログラムで ADO.NET を使用している場合、**SqlException** のスローによって一時エラーが報告されます。 [SQL Database クライアント アプリケーション SQL エラー コード](sql-database-develop-error-messages.md)のトピックの冒頭付近に一時エラーの一覧があります。この表に記載されているエラー番号と **Number** プロパティを比較してください。

<a id="connection-versus-command" name="connection-versus-command"></a>

### <a name="connection-versus-command"></a>接続とコマンド
以下の条件に応じて、SQL 接続を再試行するか、再度確立します。

* **接続試行中に一時エラーが発生した場合**: 数秒待ってから接続を再試行する必要があります。
* **SQL クエリ コマンドの実行中に一時エラーが発生した場合**: そのコマンドをすぐに再試行することは避けてください。 ある程度の時間差を伴って接続が新たに確立されます。 その後でコマンドを再試行してください。

<a id="j-retry-logic-transient-faults" name="j-retry-logic-transient-faults"></a>

### <a name="retry-logic-for-transient-errors"></a>一時エラーの再試行ロジック
一時エラーが多少なりとも生じるクライアント プログラムは、再試行ロジックを組み込むことで堅牢性を高めることができます。

Azure SQL Database との通信にサード パーティのミドルウェアを使用する場合、一時エラーに対する再試行ロジックがそのミドルウェアに備わっているかどうかをベンダーに確認してください。

<a id="principles-for-retry" name="principles-for-retry"></a>

#### <a name="principles-for-retry"></a>再試行の原則
* エラーが一時エラーである場合、再度、接続を開いてみる。
* 一時エラーで失敗した SQL SELECT ステートメントをそのまま再試行することはしない。
  
  * 新しい接続を確立してから、SELECT を再試行してください。
* SQL UPDATE ステートメントが一時エラーで失敗した場合、新たに接続を確立したうえで UPDATE を再試行する。
  
  * データベース トランザクション全体が完了するかトランザクション全体がロールバックされたことを再試行ロジックで確認する必要があります。

#### <a name="other-considerations-for-retry"></a>再試行に関するその他の注意点
* 業務終了時刻に自動的に起動されて翌朝にかけて完了するようなバッチ プログラムは、再試行間隔をある程度長めにしても問題ありません。
* 人間の心理として、待ち時間があまり長いと途中でキャンセルされる可能性があります。ユーザー インターフェイス プログラムはその点を考慮するようにしてください。
  
  * ただし、数秒おきに再試行するようなやり方は避けてください。そのような方法を用いると、大量の要求でシステムが処理しきれなくなる可能性があります。

#### <a name="interval-increase-between-retries"></a>再試行の間隔を長くする
最初に再試行する前に、5 秒間待つことをお勧めします。 5 秒未満で再試行すると、クラウド サービスに過度の負荷がかかるおそれがあります。 再試行するたびに、待ち時間を比例して、最大 60 秒まで長くする必要があります。

ADO.NET を使用するクライアントの *ブロック期間* については、「 [SQL Server の接続プール (ADO.NET)](http://msdn.microsoft.com/library/8xx3tyca.aspx)」を参照してください。

加えて、最大再試行回数を設定し、プログラムが自動的に終了するように配慮する必要があります。

#### <a name="code-samples-with-retry-logic"></a>再試行ロジックを含んだコード サンプル
さまざまなプログラミング言語での再試行ロジックが使われているコード サンプルは次のリンクからアクセスできます。

* [SQL Database と SQL Server の接続ライブラリ](sql-database-libraries.md)

<a id="k-test-retry-logic" name="k-test-retry-logic"></a>

#### <a name="test-your-retry-logic"></a>再試行ロジックのテスト
再試行ロジックをテストするには、プログラムの実行中に修復可能なエラーをシミュレートする (人為的に発生させる) 必要があります。

##### <a name="test-by-disconnecting-from-the-network"></a>ネットワークから切断することによるテスト
再試行ロジックをテストする手段として、プログラムの実行中にクライアント コンピューターをネットワークから切断する方法が挙げられます。 次のエラーが発生します。

* **SqlException.Number** = 11001
* メッセージ: "そのようなホストは不明です"

最初の再試行のときに、プログラムでスペルミスを修正してから接続を試みてください。

具体的には、コンピューターをネットワークから切断した後でプログラムを起動します。 その後プログラムは、実行時パラメーターを通じて次の処理を行います。

1. エラーのリストに対して一時的に 11001 を追加し、一過性と見なします。
2. 通常と同様に初回接続を試みます。
3. エラーが捕捉された後、11001 をリストから削除します。
4. コンピューターをネットワークに接続するようにユーザーに通知するメッセージが表示されます。
   * **Console.ReadLine** メソッドか、[OK] ボタンを含むダイアログのいずれかを使用して、以降の実行を一時停止します。 コンピューターをネットワークに接続した後、ユーザーが Enter キーを押します。
5. 再度接続を試みます。

##### <a name="test-by-misspelling-the-database-name-when-connecting"></a>接続時に間違った綴りのデータベース名を使用することによるテスト
意図的に間違ったユーザー名を使って初回接続を試みます。 次のエラーが発生します。

* **SqlException.Number** = 18456
* メッセージ: "ユーザー WRONG_MyUserName はログインできませんでした"

最初の再試行のときに、プログラムでスペルミスを修正してから接続を試みてください。

具体的には、プログラムで実行時パラメーターを通じ、次の処理を行います。

1. エラーのリストに対して一時的に 18456 を追加し、一過性と見なします。
2. 意図的に 'WRONG_' をユーザー名に追加します。
3. エラーが捕捉された後、18456 をリストから削除します。
4. ユーザー名から 'WRONG_' を削除します。
5. 再度接続を試みます。

<a id="net-sqlconnection-parameters-for-connection-retry" name="net-sqlconnection-parameters-for-connection-retry"></a>

### <a name="net-sqlconnection-parameters-for-connection-retry"></a>接続再試行用の .NET SqlConnection パラメーター
.NET Framework クラスの **System.Data.SqlClient.SqlConnection** を使用してクライアント プログラムから Azure SQL Database に接続する場合は、接続再試行機能を活用できるように .NET 4.6.1 以降 (または .NET Core) を使用してください。 この機能の詳細については [こちら](http://go.microsoft.com/fwlink/?linkid=393996)を参照してください。

<!--
2015-11-30, FwLink 393996 points to dn632678.aspx, which links to a downloadable .docx related to SqlClient and SQL Server 2014.
-->


[接続文字列](http://msdn.microsoft.com/library/System.Data.SqlClient.SqlConnection.connectionstring.aspx) を **SqlConnection** オブジェクト用に作成するときは、次のパラメーター間で値を調整する必要があります。

* ConnectRetryCount &nbsp;&nbsp;*(既定値は 1 です。範囲は 0 ～ 255 です)*
* ConnectRetryInterval &nbsp;&nbsp;*(既定値は 1 秒です。範囲は 1 ～ 60 です)*
* Connection Timeout &nbsp;&nbsp;*(既定値は 15 秒です。範囲は 0 ～ 2147483647 です)*

具体的には、選択した値で次の等式が成り立つ必要があります。

* Connection Timeout = ConnectRetryCount * ConnectionRetryInterval

たとえば、回数が 3、間隔が 10 秒、タイムアウトが 29 秒のみの場合、29 < 3 * 10 となり、接続時に 3 回目と最後の試行には十分な時間が与えらません。

<a id="connection-versus-command" name="connection-versus-command"></a>

### <a name="connection-versus-command"></a>接続とコマンド
**ConnectRetryCount** パラメーターと **ConnectRetryInterval** パラメーターを使用すると、プログラムに制御を返すなど、プログラムへの通知や介入なしに、**SqlConnection** オブジェクトで接続操作を再試行できます。 再試行は次の状況で発生することがあります。

* mySqlConnection.Open メソッドの呼び出し
* mySqlConnection.Execute メソッドの呼び出し

これには注意が必要です。 *クエリ* の実行中に一時エラーが発生した場合、 **SqlConnection** オブジェクトで接続操作が再試行されないため、クエリは再試行されません。 ただし、実行するクエリを送信する前に、 **SqlConnection** ですばやく接続が確認されます。 簡単なチェックで接続の問題が検出された場合、 **SqlConnection** で接続操作が再試行されます。 再試行に成功すると、実行するクエリが送信されます。

#### <a name="should-connectretrycount-be-combined-with-application-retry-logic"></a>ConnectRetryCount をアプリケーションの再試行ロジックと組み合わせて使用する必要があるかどうか
アプリケーションにカスタムの堅牢な再試行ロジックが組み込まれていると仮定します。 このロジックでは、接続操作が 4 回再試行されます。 **ConnectRetryInterval** と **ConnectRetryCount** = 3 を接続文字列に追加すると、再試行回数が 4 * 3 = 12 に増加します。 このように何回も再試行するのは、適切ではない可能性があります。

<a id="a-connection-connection-string" name="a-connection-connection-string"></a>

## <a name="connections-to-azure-sql-database"></a>Azure SQL Database への接続
<a id="c-connection-string" name="c-connection-string"></a>

### <a name="connection-connection-string"></a>接続: 接続文字列
Azure SQL Database に接続するために必要な接続文字列は、Microsoft SQL Server に接続するための文字列とは少し異なります。 データベースの接続文字列は [Azure ポータル](https://portal.azure.com/)からコピーすることができます。

[!INCLUDE [sql-database-include-connection-string-20-portalshots](../../includes/sql-database-include-connection-string-20-portalshots.md)]

<a id="b-connection-ip-address" name="b-connection-ip-address"></a>

### <a name="connection-ip-address"></a>接続: IP アドレス
SQL Database サーバーは、クライアント プログラムのホストとなるコンピューターの IP アドレスからの通信を許可するように構成する必要があります。 この構成は、 [Azure ポータル](https://portal.azure.com/)を介してファイアウォールの設定を編集することによって行います。

IP アドレスの構成を怠った場合、必要な IP アドレスを示した親切なエラー メッセージがプログラムで表示されます。

[!INCLUDE [sql-database-include-ip-address-22-v12portal](../../includes/sql-database-include-ip-address-22-v12portal.md)]

詳細については、「 [ファイアウォール設定の構成方法 (Azure SQL Database)](sql-database-configure-firewall-settings.md)

<a id="c-connection-ports" name="c-connection-ports"></a>

### <a name="connection-ports"></a>接続: ポート
通常、必要な設定は、クライアント プログラムのホストとなるコンピューターのポート 1433 の送信方向を開放するだけです。

たとえば、クライアント プログラムが Windows コンピューターでホストされている場合、そのホストの Windows ファイアウォールでポート 1433 を開放することができます。

1. コントロール パネルを開く
2. &gt; [すべてのコントロール パネル項目]
3. &gt; [Windows ファイアウォール]
4. &gt; [詳細設定]
5. &gt; [送信の規則]
6. &gt; [アクション]
7. &gt; [新しい規則]

Azure 仮想マシン (VM) でクライアント プログラムがホストされている場合、次のように表示されます。<br/>[Ports beyond 1433 for ADO.NET 4.5, and SQL Database V12 (ADO.NET 4.5、SQL Database V12 における 1433 以外のポート)](sql-database-develop-direct-route-ports-adonet-v12.md)

ポートと IP アドレスの構成に関する背景情報については、 [Azure SQL Database のファイアウォール](sql-database-firewall-configure.md)

<a id="d-connection-ado-net-4-5" name="d-connection-ado-net-4-5"></a>

### <a name="connection-adonet-461"></a>接続: ADO.NET 4.6.1
プログラムで **System.Data.SqlClient.SqlConnection** などの ADO.NET クラスを使用して Azure SQL Database に接続する場合は、.NET Framework バージョン 4.6.1 以降を使用することをお勧めします。

ADO.NET 4.6.1:

* Azure SQL Database では、 **SqlConnection.Open** メソッドを使用して接続を開くと信頼性が向上します。 **Open** メソッドには、接続タイムアウト期間内の特定のエラーを対象に、一過性の障害に対応するベスト エフォート再試行メカニズムが組み込まれました。
* 接続プールをサポートします。 加えて、プログラムに割り当てた接続オブジェクトが正常に動作しているかどうかを効率的に検証することが可能です。

接続プールから取得した接続オブジェクトを使用するとき、すぐに使用しないのであれば、プログラムで一時的に接続を閉じるようお勧めします。 接続を再度開いたとしても、新しい接続の作成に伴う処理負荷はわずかです。

ADO.NET 4.0 以前のバージョンを使用している場合、最新の ADO.NET. にアップグレードすることをお勧めします。

* 2015 年 11 月の時点では、 [ADO.NET 4.6.1 をダウンロード](http://blogs.msdn.com/b/dotnet/archive/2015/11/30/net-framework-4-6-1-is-now-available.aspx)できます。

<a id="e-diagnostics-test-utilities-connect" name="e-diagnostics-test-utilities-connect"></a>

## <a name="diagnostics"></a>診断
<a id="d-test-whether-utilities-can-connect" name="d-test-whether-utilities-can-connect"></a>

### <a name="diagnostics-test-whether-utilities-can-connect"></a>診断: ユーティリティから接続できるかどうかをテストする
プログラムから Azure SQL Database に接続できないときの診断方法として&1; つ考えられるのは、ユーティリティ プログラムを使用して接続する方法です。 診断対象のプログラムと同じライブラリを使用して接続するユーティリティがあれば理想的です。

任意の Windows コンピューターで、次のユーティリティを試すことができます。

* SQL Server Management Studio (ssms.exe)。ADO.NET を使用して接続します。
* sqlcmd.exe。[ODBC](http://msdn.microsoft.com/library/jj730308.aspx) を使用して接続します。

接続後、短い SQL SELECT クエリが正しく動作するかどうかをテストしてください。

<a id="f-diagnostics-check-open-ports" name="f-diagnostics-check-open-ports"></a>

### <a name="diagnostics-check-the-open-ports"></a>診断: 開放ポートを確認する
ポートの問題から接続に失敗している可能性があるとします。 ポートの構成に関するレポート作成に対応したユーティリティをご使用のコンピューターから実行してください。

Linux では、次のユーティリティが役に立つ場合があります。

* `netstat -nap`
* `nmap -sS -O 127.0.0.1`
  * (例の値を実際の IP アドレスに変更してください)。

Windows では [PortQry.exe](http://www.microsoft.com/download/details.aspx?id=17148) ユーティリティが利用できます。 以下の例では、Azure SQL Database サーバー上のポートの状況と、ノート PC 上で動作しているポートとを照会しています。

```
[C:\Users\johndoe\]
>> portqry.exe -n johndoesvr9.database.windows.net -p tcp -e 1433

Querying target system called:
 johndoesvr9.database.windows.net

Attempting to resolve name to IP address...
Name resolved to 23.100.117.95

querying...
TCP port 1433 (ms-sql-s service): LISTENING

[C:\Users\johndoe\]
>>
```


<a id="g-diagnostics-log-your-errors" name="g-diagnostics-log-your-errors"></a>

### <a name="diagnostics-log-your-errors"></a>診断: エラーのログを記録する
断続的な問題は、過去数日から数週間にわたる一般的なパターンを検出することによって診断できる場合が多々あります。

診断には、クライアントで発生したエラーのログが役立ちます。 Azure SQL Database が内部的に記録するエラー データとそれらのログ エントリを相互に関連付けることも可能です。

Enterprise Library 6 (EntLib60) には、ログ記録をサポートする .NET マネージ クラスがあります。

* [5 - Logging アプリケーション ブロックの使用に関するページを参照してください。](http://msdn.microsoft.com/library/dn440731.aspx)

<a id="h-diagnostics-examine-logs-errors" name="h-diagnostics-examine-logs-errors"></a>

### <a name="diagnostics-examine-system-logs-for-errors"></a>診断: エラーの発生をシステム ログで調べる
以下に示したのは、エラーや各種情報のログを照会する Transact-SQL SELECT ステートメントの例です。

| ログのクエリ | 説明 |
|:--- |:--- |
| `SELECT e.*`<br/>`FROM sys.event_log AS e`<br/>`WHERE e.database_name = 'myDbName'`<br/>`AND e.event_category = 'connectivity'`<br/>`AND 2 >= DateDiff`<br/>&nbsp;&nbsp;`(hour, e.end_time, GetUtcDate())`<br/>`ORDER BY e.event_category,`<br/>&nbsp;&nbsp;`e.event_type, e.end_time;` |[sys.event_log](http://msdn.microsoft.com/library/dn270018.aspx) ビューには、一時的なエラーや接続障害を引き起こす可能性のある、個々のイベントに関する情報が表示されます。<br/><br/>理想的には、**start_time** や **end_time** の値を、クライアント プログラムに問題が発生した時間の情報に関連付けます。<br/><br/>**ヒント:** 実行するには、**マスター** データベースに接続する必要があります。 |
| `SELECT c.*`<br/>`FROM sys.database_connection_stats AS c`<br/>`WHERE c.database_name = 'myDbName'`<br/>`AND 24 >= DateDiff`<br/>&nbsp;&nbsp;`(hour, c.end_time, GetUtcDate())`<br/>`ORDER BY c.end_time;` |[sys.database_connection_stats](http://msdn.microsoft.com/library/dn269986.aspx) ビューには、イベントの種類ごとに集計されたカウントが表示され、詳しい診断を行うことができます。<br/><br/>**ヒント:** 実行するには、**マスター** データベースに接続する必要があります。 |

<a id="d-search-for-problem-events-in-the-sql-database-log" name="d-search-for-problem-events-in-the-sql-database-log"></a>

### <a name="diagnostics-search-for-problem-events-in-the-sql-database-log"></a>診断: SQL Database のログから問題のイベントを検索する
Azure SQL Database のログで問題のイベントに関するエントリを検索することができます。 **master** データベースで次の Transact-SQL SELECT ステートメントを試してみてください。

```
SELECT
   object_name
  ,CAST(f.event_data as XML).value
      ('(/event/@timestamp)[1]', 'datetime2')                      AS [timestamp]
  ,CAST(f.event_data as XML).value
      ('(/event/data[@name="error"]/value)[1]', 'int')             AS [error]
  ,CAST(f.event_data as XML).value
      ('(/event/data[@name="state"]/value)[1]', 'int')             AS [state]
  ,CAST(f.event_data as XML).value
      ('(/event/data[@name="is_success"]/value)[1]', 'bit')        AS [is_success]
  ,CAST(f.event_data as XML).value
      ('(/event/data[@name="database_name"]/value)[1]', 'sysname') AS [database_name]
FROM
  sys.fn_xe_telemetry_blob_target_read_file('el', null, null, null) AS f
WHERE
  object_name != 'login_event'  -- Login events are numerous.
  and
  '2015-06-21' < CAST(f.event_data as XML).value
        ('(/event/@timestamp)[1]', 'datetime2')
ORDER BY
  [timestamp] DESC
;
```


#### <a name="a-few-returned-rows-from-sysfnxetelemetryblobtargetreadfile"></a>sys.fn_xe_telemetry_blob_target_read_file から返される行の例
次に示したのは、クエリから返される行の例です。 ここに示した行では null 値が表示されていますが、null 以外の場合も多くあります。

```
object_name                   timestamp                    error  state  is_success  database_name

database_xml_deadlock_report  2015-10-16 20:28:01.0090000  NULL   NULL   NULL        AdventureWorks
```


<a id="l-enterprise-library-6" name="l-enterprise-library-6"></a>

## <a name="enterprise-library-6"></a>Enterprise Library 6
Enterprise Library 6 (EntLib60) は、.NET クラスのフレームワークです。クラウド サービス (Azure SQL Database サービスもその一つ) に対する堅牢なクライアントをこのフレームワークを使って実装することができます。 EntLib60 の利便性が発揮される個々の領域の説明については、まず以下のトピックにアクセスしてください。

* [Enterprise Library 6 – 2013 年 4 月](http://msdn.microsoft.com/library/dn169621%28v=pandp.60%29.aspx)

一時エラーを処理するための再試行ロジックは、EntLib60 を利用できる&1; つの領域です。

* [4 - Perseverance, Secret of All Triumphs: Using the Transient Fault Handling Application Block (忍耐力、すべての勝利の秘訣: 一時的エラー処理アプリケーション ブロックの使用)](http://msdn.microsoft.com/library/dn440719%28v=pandp.60%29.aspx)

> [!NOTE]
> EntLib60 のソース コードは、 [ダウンロード サイト](http://go.microsoft.com/fwlink/p/?LinkID=290898)から入手できます。 EntLib に対して機能の更新や保守目的での更新を行う予定はありません。
> 
> 

<a id="entlib60-classes-for-transient-errors-and-retry" name="entlib60-classes-for-transient-errors-and-retry"></a>

### <a name="entlib60-classes-for-transient-errors-and-retry"></a>一時エラーと再試行に関連した EntLib60 のクラス
再試行ロジックで特に利用する機会の多い EntLib60 のクラスは次のとおりです。 いずれのクラス (そのメソッドなども含む) も、**Microsoft.Practices.EnterpriseLibrary.TransientFaultHandling** 名前空間に属しています。

* **Microsoft.Practices.EnterpriseLibrary.TransientFaultHandling**名前空間に属しています。*

* **RetryPolicy** クラス
  
  * **ExecuteAction** メソッド
* **ExponentialBackoff** クラス
* **SqlDatabaseTransientErrorDetectionStrategy** クラス
* **ReliableSqlConnection** クラス
  
  * **ExecuteCommand** メソッド

**Microsoft.Practices.EnterpriseLibrary.TransientFaultHandling.TestSupport**名前空間:

* **AlwaysTransientErrorDetectionStrategy** クラス
* **NeverTransientErrorDetectionStrategy** クラス

EntLib60 に関する情報は以下のリンクから入手できます。

* 無料の [電子ブック: Microsoft Enterprise Library 開発者ガイド、第 2 版](http://www.microsoft.com/download/details.aspx?id=41145)
* ベスト プラクティス: [再試行全般のガイダンス](../best-practices-retry-general.md) には、再試行のロジックが詳しく解説されていてお勧めです。
* NuGet ダウンロード ( [Enterprise Library - Transient Fault Handling アプリケーション ブロック 6.0](http://www.nuget.org/packages/EnterpriseLibrary.TransientFaultHandling/)

<a id="entlib60-the-logging-block" name="entlib60-the-logging-block"></a>

### <a name="entlib60-the-logging-block"></a>EntLib60: Logging ブロック
* Logging ブロックは、きわめて柔軟性に優れた構成可能なソリューションです。
  
  * ログ メッセージをさまざまな場所に作成して保存する。
  * メッセージを分類したりフィルタリングしたりする。
  * デバッグやトレース、監査要件、全般的なログ要件に利用できるコンテキスト情報を収集する。
* Logging ブロックは、ログ出力先が備えるログ機能を抽象化したものです。対象となるログ ストアの場所や種類に関係なく、アプリケーション コードの一貫性を確保することができます。

詳細については、 [5 - Logging アプリケーション ブロックの使用に関するページを参照してください。](https://msdn.microsoft.com/library/dn440731%28v=pandp.60%29.aspx)

<a id="entlib60-istransient-method-source-code" name="entlib60-istransient-method-source-code"></a>

### <a name="entlib60-istransient-method-source-code"></a>EntLib60 IsTransient メソッドのソース コード
以下に示したのは、**SqlDatabaseTransientErrorDetectionStrategy** クラスの **IsTransient** メソッドの C# ソース コードです。 どのようなエラーが一過性で、再試行する価値があるかは、このソース コードを見るとはっきりわかります (2013 年 4 月時点)。

このソース コードのコピーは、読みやすくするために、大部分の **コメント (//)** 行を省略しています。

```
public bool IsTransient(Exception ex)
{
  if (ex != null)
  {
    SqlException sqlException;
    if ((sqlException = ex as SqlException) != null)
    {
      // Enumerate through all errors found in the exception.
      foreach (SqlError err in sqlException.Errors)
      {
        switch (err.Number)
        {
            // SQL Error Code: 40501
            // The service is currently busy. Retry the request after 10 seconds.
            // Code: (reason code to be decoded).
          case ThrottlingCondition.ThrottlingErrorNumber:
            // Decode the reason code from the error message to
            // determine the grounds for throttling.
            var condition = ThrottlingCondition.FromError(err);

            // Attach the decoded values as additional attributes to
            // the original SQL exception.
            sqlException.Data[condition.ThrottlingMode.GetType().Name] =
              condition.ThrottlingMode.ToString();
            sqlException.Data[condition.GetType().Name] = condition;

            return true;

          case 10928:
          case 10929:
          case 10053:
          case 10054:
          case 10060:
          case 40197:
          case 40540:
          case 40613:
          case 40143:
          case 233:
          case 64:
            // DBNETLIB Error Code: 20
            // The instance of SQL Server you attempted to connect to
            // does not support encryption.
          case (int)ProcessNetLibErrorCode.EncryptionNotSupported:
            return true;
        }
      }
    }
    else if (ex is TimeoutException)
    {
      return true;
    }
    else
    {
      EntityException entityException;
      if ((entityException = ex as EntityException) != null)
      {
        return this.IsTransient(entityException.InnerException);
      }
    }
  }

  return false;
}
```


## <a name="next-steps"></a>次のステップ
* Azure SQL Database の他の一般的な接続の問題のトラブルシューティングについては、「 [Azure SQL Database との接続に関する一般的な問題のトラブルシューティング](sql-database-troubleshoot-common-connection-issues.md)」をご覧ください。
* [SQL Server の接続プーリング (ADO.NET)](http://msdn.microsoft.com/library/8xx3tyca.aspx)
* [*Retrying* は Apache 2.0 ライセンスで配布される汎用の再試行ライブラリです。**Python** で作成されています。対象を選ばず、再試行の動作を簡単に追加することができます。](https://pypi.python.org/pypi/retrying)


