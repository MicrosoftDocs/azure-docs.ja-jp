---
title: 一時エラーへの対応 - Azure SQL Database | Microsoft Docs
description: Azure SQL Database での SQL 接続エラーまたは一時エラーのトラブルシューティング、診断、防止の方法について説明します。
keywords: sql 接続, 接続文字列, 接続の問題, 一時エラー, 接続エラー
services: sql-database
ms.service: sql-database
ms.subservice: development
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: dalechen
ms.author: ninarn
ms.reviewer: carlrab
manager: craigg
ms.date: 06/14/2019
ms.openlocfilehash: adbe8dfd41725c11516f820656b0476ed1aa8881
ms.sourcegitcommit: 22c97298aa0e8bd848ff949f2886c8ad538c1473
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/14/2019
ms.locfileid: "67144048"
---
# <a name="working-with-sql-database-connection-issues-and-transient-errors"></a>SQL Database の接続に関する問題と一時的なエラーに対応する

この記事では、クライアント アプリケーションが Azure SQL Database とやり取りする際に発生する接続エラーと一時エラーを防止、トラブルシューティング、診断、軽減する方法について説明します。 再試行ロジックの構成方法、接続文字列の作成方法、およびその他の接続設定の調整方法について説明します。

<a id="i-transient-faults" name="i-transient-faults"></a>

## <a name="transient-errors-transient-faults"></a>一時エラー (一過性の障害)

一時エラーは、一過性の障害とも呼ばれ、基になる原因はすぐ自動的に解決されます。 一時エラーを起こす偶発的原因として、Azure システムが、各種ワークロードの負荷分散を行うために行うハードウェア リソースの瞬間的切り替えがあります。 この再構成イベントのほとんどは 60 秒以内に完了します。 この再構成の進行中、SQL Database への接続の問題が発生する場合があります。 SQL Database に接続するアプリケーションは、これらの一時エラーを想定して構築する必要があります。 これに対処するには、アプリケーション エラーとしてユーザーに表示するのではなく、コードに再試行ロジックを実装します。

クライアント プログラムで ADO.NET を使用している場合、**SqlException** のスローによって一時エラーが報告されます。 [SQL Database クライアント アプリケーションの SQL エラー コード](sql-database-develop-error-messages.md)に関するの記事の冒頭付近に一時エラーの一覧があります。この表のエラー番号と **Number** プロパティを比較してください。

<a id="connection-versus-command" name="connection-versus-command"></a>

### <a name="connection-vs-command"></a>接続とコマンド

以下の条件に応じて、SQL 接続を再試行するか、再度確立します。

- **接続試行中に一時エラーが発生する**

数秒間の時間を置いて、接続を再試行します。

- **SQL クエリ コマンド中に一時エラーが発生する**

コマンドをすぐに再試行しないでください。 少し時間を置いて、新たに接続を確立します。 その後、コマンドを再試行します。

<a id="j-retry-logic-transient-faults" name="j-retry-logic-transient-faults"></a>

## <a name="retry-logic-for-transient-errors"></a>一時エラーの再試行ロジック

一時エラーが多少なりとも生じるクライアント プログラムは、再試行ロジックを組み込むことで堅牢性を高めることができます。 ご使用のプログラムがサード パーティのミドルウェアを介して SQL Database と通信する場合、一時エラーに対応した再試行ロジックがそのミドルウェアに備わっているかどうかをベンダーに確認してください。

<a id="principles-for-retry" name="principles-for-retry"></a>

### <a name="principles-for-retry"></a>再試行の原則

- 一時的なエラーである場合は、再試行して接続を開いてください。
- 一時エラーで失敗した SQL `SELECT` ステートメントは、そのまま再試行しないでください。 新しい接続を確立してから、`SELECT` を再試行してください。
- SQL `UPDATE` ステートメントが一時エラーで失敗した場合、新たに接続を確立したうえで UPDATE を再試行してください。 データベース トランザクション全体が完了したのか、トランザクション全体がロールバックされたのかを再試行ロジックで確認する必要があります。

### <a name="other-considerations-for-retry"></a>再試行に関するその他の注意点

- 業務終了時刻に自動的に起動され翌朝までに完了するバッチ プログラムは、再試行間隔をある程度長めにしても問題ありません。
- 人間の心理として、待ち時間があまり長いと途中でキャンセルされる可能性があります。ユーザー インターフェイス プログラムはその点を考慮するようにしてください。 数秒おきに再試行するようなやり方は避けてください。そのような方法を用いると、大量の要求でシステムが処理しきれなくなる可能性があります。

### <a name="interval-increase-between-retries"></a>再試行の間隔を長くする

最初に再試行する前に、5 秒間待つことをお勧めします。 5 秒未満で再試行すると、クラウド サービスに過度の負荷がかかるおそれがあります。 再試行するたびに、待ち時間を大幅に長くし、最大 60 秒待つ必要があります。

ADO.NET を使用するクライアントのブロック期間については、「[SQL Server の接続プール (ADO.NET)](https://msdn.microsoft.com/library/8xx3tyca.aspx)」を参照してください。

加えて、最大再試行回数を設定し、プログラムが自動的に終了するように配慮する必要があります。

### <a name="code-samples-with-retry-logic"></a>再試行ロジックを含んだコード サンプル

再試行ロジックを含むコード サンプルについては次の記事をご覧ください。

- [ADO.NET を使用して SQL に弾性的に接続する][step-4-connect-resiliently-to-sql-with-ado-net-a78n]
- [PHP を使用して SQL に弾性的に接続する][step-4-connect-resiliently-to-sql-with-php-p42h]

<a id="k-test-retry-logic" name="k-test-retry-logic"></a>

### <a name="test-your-retry-logic"></a>再試行ロジックのテスト

再試行ロジックをテストするには、プログラムの実行中に修復可能なエラーをシミュレートする (人為的に発生させる) 必要があります。

#### <a name="test-by-disconnecting-from-the-network"></a>ネットワークから切断することによるテスト

再試行ロジックをテストする手段として、プログラムの実行中にクライアント コンピューターをネットワークから切断する方法が挙げられます。 エラーは次のとおりです。

- **SqlException.Number** = 11001
- メッセージ:"そのようなホストは不明です"

最初の再試行の一環として、クライアント コンピューターをネットワークに再接続してから接続を試行することができます。

このテストを実践するには、コンピューターをネットワークから切断した後でプログラムを起動します。 その後プログラムは、ランタイム パラメーターを通じて次の処理を行います。

- エラーのリストに対して一時的に 11001 を追加し、一過性と見なします。
- 通常と同様に初回接続を試みます。
- エラーが捕捉された後、11001 をリストから削除します。
- コンピューターをネットワークに接続するようにユーザーに通知するメッセージが表示されます。
- **Console.ReadLine** メソッドか、[OK] ボタンを含むダイアログのいずれかを使用して、以降の実行を一時停止します。 コンピューターがネットワークに接続された後に、ユーザーが Enter キーを押します。
- 再度接続を試みます。

#### <a name="test-by-misspelling-the-database-name-when-connecting"></a>接続時に間違った綴りのデータベース名を使用することによるテスト

意図的に間違ったユーザー名を使って初回接続を試みます。 エラーは次のとおりです。

- **SqlException.Number** = 18456
- メッセージ:"ユーザー WRONG_MyUserName はログインできませんでした"

最初の再試行のときに、プログラムでスペルミスを修正してから接続を試みてください。

このテストを実践するには、プログラムでランタイムパラメーターを通じて次の処理を行います。

- エラーのリストに対して一時的に 18456 を追加し、一過性と見なします。
- 意図的に 'WRONG_' をユーザー名に追加します。
- エラーが捕捉された後、18456 をリストから削除します。
- ユーザー名から 'WRONG_' を削除します。
- 再度接続を試みます。

<a id="net-sqlconnection-parameters-for-connection-retry" name="net-sqlconnection-parameters-for-connection-retry"></a>

## <a name="net-sqlconnection-parameters-for-connection-retry"></a>接続再試行用の .NET SqlConnection パラメーター

.NET Framework クラスの **System.Data.SqlClient.SqlConnection** を使用してクライアント プログラムから SQL Database に接続する場合は、接続再試行機能を活用できるように .NET 4.6.1 以降 (または .NET Core) を使用してください。 機能の詳細については、[この Web ページ](https://docs.microsoft.com/dotnet/api/system.data.sqlclient.sqlconnection)を参照してください。

<!--
2015-11-30, FwLink 393996 points to dn632678.aspx, which links to a downloadable .docx related to SqlClient and SQL Server 2014.
-->

[接続文字列](https://msdn.microsoft.com/library/System.Data.SqlClient.SqlConnection.connectionstring.aspx) を **SqlConnection** オブジェクト用に作成するときは、次のパラメーター間で値を調整します。

- **ConnectRetryCount**:&nbsp;&nbsp;既定値は 1 です。 範囲は 0 ～ 255 です。
- **ConnectRetryInterval**:&nbsp;&nbsp;既定値は 10 秒です。 範囲は 1 ～ 60 です。
- **Connection Timeout**:&nbsp;&nbsp;既定値は 15 秒です。 範囲は 0 ～ 2147483647 です。

具体的には、選択した値で次の等式が成り立つ必要があります。Connection Timeout = ConnectRetryCount * ConnectionRetryInterval

たとえば、回数が 3、間隔が 10 秒、タイムアウトが 29 秒のみの場合、29 < 3 * 10 となり、3 回目の最後の接続の試行には十分な時間が与えらません。

<a id="connection-versus-command" name="connection-versus-command"></a>

## <a name="connection-vs-command"></a>接続とコマンド

**ConnectRetryCount** パラメーターと **ConnectRetryInterval** パラメーターを使用すると、プログラムに制御を返すなど、プログラムへの通知や介入なしに、**SqlConnection** オブジェクトで接続操作を再試行できます。 再試行は次の状況で発生することがあります。

- mySqlConnection.Open メソッドの呼び出し
- mySqlConnection.Execute メソッドの呼び出し

これには注意が必要です。 "*クエリ*" の実行中に一時エラーが発生した場合、**SqlConnection** オブジェクトで接続操作が再試行されません。 間違いなくクエリは再試行されません。 ただし、実行するクエリを送信する前に、 **SqlConnection** ですばやく接続が確認されます。 簡単なチェックで接続の問題が検出された場合、 **SqlConnection** で接続操作が再試行されます。 再試行に成功すると、実行するクエリが送信されます。

### <a name="should-connectretrycount-be-combined-with-application-retry-logic"></a>ConnectRetryCount をアプリケーションの再試行ロジックと組み合わせて使用する必要があるかどうか

アプリケーションにカスタムの堅牢な再試行ロジックが組み込まれていると仮定します。 このロジックでは、接続操作が 4 回再試行されます。 **ConnectRetryInterval** と **ConnectRetryCount** = 3 を接続文字列に追加すると、再試行回数が 4 * 3 = 12 に増加します。 このように何回も再試行するのは、適切ではない可能性があります。

<a id="a-connection-connection-string" name="a-connection-connection-string"></a>

## <a name="connections-to-sql-database"></a>SQL Database への接続

<a id="c-connection-string" name="c-connection-string"></a>

### <a name="connection-connection-string"></a>接続:接続文字列

SQL Database に接続するために必要な接続文字列は、SQL Server への接続に使用される文字列とは若干異なります。 データベースの接続文字列は [Azure ポータル](https://portal.azure.com/)からコピーすることができます。

[!INCLUDE [sql-database-include-connection-string-20-portalshots](../../includes/sql-database-include-connection-string-20-portalshots.md)]

<a id="b-connection-ip-address" name="b-connection-ip-address"></a>

### <a name="connection-ip-address"></a>接続:IP アドレス

SQL Database サーバーは、クライアント プログラムのホストとなるコンピューターの IP アドレスからの通信を許可するように構成する必要があります。 この構成を設定するには、[Azure Portal](https://portal.azure.com/) を通じてファイアウォール設定を編集します。

IP アドレスの構成を怠った場合、必要な IP アドレスを示した役立つエラー メッセージが表示され、プログラムが失敗します。

[!INCLUDE [sql-database-include-ip-address-22-portal](../../includes/sql-database-include-ip-address-22-v12portal.md)]

詳細については、[SQL Database のファイアウォール設定の構成](sql-database-configure-firewall-settings.md)に関するページを参照してください。
<a id="c-connection-ports" name="c-connection-ports"></a>

### <a name="connection-ports"></a>接続:Port

通常、必要な設定は、クライアント プログラムのホストとなるコンピューターのポート 1433 の送信方向を開放するだけです。

たとえば、クライアント プログラムが Windows コンピューターでホストされている場合、そのホストの Windows ファイアウォールを使用してポート 1433 を開放することができます。

1. [コントロール パネル] を開きます。
2. **[すべてのコントロール パネル項目]**  >  **[Windows ファイアウォール]**  >  **[詳細設定]**  >  **[送信の規則]**  >  **[アクション]**  >  **[新しい規則]** の順に選択します。

クライアント プログラムが Azure の仮想マシン (VM) でホストされている場合は、[ADO.NET 4.5 用の 1433 以外のポートと SQL Database](sql-database-develop-direct-route-ports-adonet-v12.md) に関するページを参照してください。

ポートと IP アドレスの構成に関する背景情報については、[Azure SQL Database のファイアウォール](sql-database-firewall-configure.md)に関するページを参照してください。

<a id="d-connection-ado-net-4-5" name="d-connection-ado-net-4-5"></a>

### <a name="connection-adonet-462-or-later"></a>接続:ADO.NET 4.6.2 以降

プログラムで **System.Data.SqlClient.SqlConnection** などの ADO.NET クラスを使用して SQL Database に接続する場合は、.NET Framework バージョン 4.6.2 以降を使用することをお勧めします。

#### <a name="starting-with-adonet-462"></a>ADO.NET 4.6.2 で開始する場合

- Azure SQL データベースに対して接続を開く試みが直ちに再試行されます。そのため、クラウド対応アプリのパフォーマンスが向上します。

#### <a name="starting-with-adonet-461"></a>ADO.NET 4.6.1 で開始する場合

- SQL Database では、**SqlConnection.Open** メソッドを使用して接続を開くと信頼性が向上します。 **Open** メソッドには、接続タイムアウト期間内の特定のエラーを対象に、一過性の障害に対応するベスト エフォート再試行メカニズムが組み込まれました。
- 接続プールがサポートされているため、プログラムに割り当てた接続オブジェクトが正常に動作しているかどうかを効率的に検証することが可能です。

接続プールから取得した接続オブジェクトを使用するとき、すぐに使用しないのであれば、プログラムで一時的に接続を閉じることをお勧めします。 接続を再度開く処理負荷はわずかですが、新しい接続を作成する負荷は大きくなります。

ADO.NET 4.0 以前のバージョンを使用する場合、最新の ADO.NET. にアップグレードすることをお勧めします。 2018 年 8 月の時点で、[ADO.NET 4.6.2 のダウンロード](https://blogs.msdn.microsoft.com/dotnet/20../../announcing-the-net-framework-4-7-2/)が可能になりました。

<a id="e-diagnostics-test-utilities-connect" name="e-diagnostics-test-utilities-connect"></a>

## <a name="diagnostics"></a>診断

<a id="d-test-whether-utilities-can-connect" name="d-test-whether-utilities-can-connect"></a>

### <a name="diagnostics-test-whether-utilities-can-connect"></a>診断:ユーティリティから接続できるかどうかをテストする

プログラムから SQL Database に接続できないときの診断方法として 1 つ考えられるのは、ユーティリティ プログラムを使用して接続する方法です。 プログラムで使用しているのと同じライブラリを使用して接続するユーティリティがあれば理想的です。

任意の Windows コンピューターで、次のユーティリティを試すことができます。

- SQL Server Management Studio (ssms.exe)。ADO.NET を使用して接続します。
- `sqlcmd.exe`。[ODBC](https://msdn.microsoft.com/library/jj730308.aspx) を使用して接続します。

プログラムが接続された後に、短い SQL SELECT クエリが正しく動作するかどうかをテストしてください。

<a id="f-diagnostics-check-open-ports" name="f-diagnostics-check-open-ports"></a>

### <a name="diagnostics-check-the-open-ports"></a>診断:開放ポートを確認する

ポートの問題が原因で接続に失敗している可能性がある場合は、ポートの構成に関するレポート作成に対応したユーティリティをご使用のコンピューターで実行してください。

Linux では、次のユーティリティが役に立つ場合があります。

- `netstat -nap`
- `nmap -sS -O 127.0.0.1`:例の値を実際の IP アドレスに変更してください。

Windows では [PortQry.exe](https://www.microsoft.com/download/details.aspx?id=17148) ユーティリティが利用できます。 以下は、SQL Database サーバーのポートの状況の照会をノート PC 上で実行する例を示しています。

```cmd
[C:\Users\johndoe\]
>> portqry.exe -n johndoesvr9.database.windows.net -p tcp -e 1433

Querying target system called: johndoesvr9.database.windows.net

Attempting to resolve name to IP address...
Name resolved to 23.100.117.95

querying...
TCP port 1433 (ms-sql-s service): LISTENING

[C:\Users\johndoe\]
>>
```

<a id="g-diagnostics-log-your-errors" name="g-diagnostics-log-your-errors"></a>

### <a name="diagnostics-log-your-errors"></a>診断:エラーのログを記録する

断続的な問題は、過去数日から数週間にわたる一般的なパターンを検出することによって診断できる場合が多々あります。

診断には、クライアントで発生したエラーのログが役立ちます。 SQL Database が内部的に記録するエラー データとそれらのログ エントリを相互に関連付けることも可能です。

Enterprise Library 6 (EntLib60) には、ログ記録をサポートする .NET マネージド クラスがあります。 詳細については、「[5 - As easy as falling off a log: Use the Logging Application Block (5 - きわめて簡単: Logging アプリケーション ブロックの使用)](https://msdn.microsoft.com/library/dn440731.aspx)」を参照してください。

<a id="h-diagnostics-examine-logs-errors" name="h-diagnostics-examine-logs-errors"></a>

### <a name="diagnostics-examine-system-logs-for-errors"></a>診断:エラーの発生をシステム ログで調べる

以下に示したのは、エラー ログや各種情報を照会する Transact-SQL SELECT ステートメントの例です。

| ログのクエリ | 説明 |
|:--- |:--- |
| `SELECT e.*`<br/>`FROM sys.event_log AS e`<br/>`WHERE e.database_name = 'myDbName'`<br/>`AND e.event_category = 'connectivity'`<br/>`AND 2 >= DateDiff`<br/>&nbsp;&nbsp;`(hour, e.end_time, GetUtcDate())`<br/>`ORDER BY e.event_category,`<br/>&nbsp;&nbsp;`e.event_type, e.end_time;` |[sys.event_log](https://msdn.microsoft.com/library/dn270018.aspx) ビューには、一時エラーや接続障害を引き起こす可能性のあるものを含む、個々のイベントに関する情報が表示されます。<br/><br/>理想的には、**start_time** や **end_time** の値を、クライアント プログラムに問題が発生した時間の情報に関連付けます。<br/><br/>このクエリを実行するには、"*マスター*" データベースに接続する必要があります。 |
| `SELECT c.*`<br/>`FROM sys.database_connection_stats AS c`<br/>`WHERE c.database_name = 'myDbName'`<br/>`AND 24 >= DateDiff`<br/>&nbsp;&nbsp;`(hour, c.end_time, GetUtcDate())`<br/>`ORDER BY c.end_time;` |[sys.database_connection_stats](https://msdn.microsoft.com/library/dn269986.aspx) ビューには、イベントの種類ごとに集計されたカウントが表示され、詳しい診断を行うことができます。<br/><br/>このクエリを実行するには、"*マスター*" データベースに接続する必要があります。 |

<a id="d-search-for-problem-events-in-the-sql-database-log" name="d-search-for-problem-events-in-the-sql-database-log"></a>

### <a name="diagnostics-search-for-problem-events-in-the-sql-database-log"></a>診断:SQL Database のログから問題のイベントを検索する

SQL Database のログで問題のイベントに関するエントリを検索することができます。 *master* データベースで次の Transact-SQL SELECT ステートメントを試してみてください。

```sql
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

次の例は、どのような行が返されるかを示しています。 ここに示した行では null 値が表示されていますが、null 以外の場合も多くあります。

```
object_name                   timestamp                    error  state  is_success  database_name

database_xml_deadlock_report  2015-10-16 20:28:01.0090000  NULL   NULL   NULL        AdventureWorks
```

<a id="l-enterprise-library-6" name="l-enterprise-library-6"></a>

## <a name="enterprise-library-6"></a>Enterprise Library 6

Enterprise Library 6 (EntLib60) は、.NET クラスのフレームワークです。クラウド サービス (SQL Database サービスもその 1 つ) に対する堅牢なクライアントをこのフレームワークを使って実装することができます。 EntLib60 の利便性が発揮される個々の領域の説明については、「[Enterprise Library 6 - April 2013 (Enterprise Library 6 – 2013 年 4 月)](https://msdn.microsoft.com/library/dn169621%28v=pandp.60%29.aspx)」を参照してください。

一時エラーを処理するための再試行ロジックは、EntLib60 を利用できる 1 つの領域です。 詳細については、「[4 - Perseverance, Secret of All Triumphs:Use the Transient Fault Handling Application Block](https://msdn.microsoft.com/library/dn440719%28v=pandp.60%29.aspx)」をご覧ください。

> [!NOTE]
> EntLib60 のソース コードは、[ダウンロード センター](https://go.microsoft.com/fwlink/p/?LinkID=290898)から入手できます。 EntLib に対して機能の更新や保守目的での更新を行う予定はありません。

<a id="entlib60-classes-for-transient-errors-and-retry" name="entlib60-classes-for-transient-errors-and-retry"></a>

### <a name="entlib60-classes-for-transient-errors-and-retry"></a>一時エラーと再試行に関連した EntLib60 のクラス

再試行ロジックで特に利用する機会の多い EntLib60 のクラスは次のとおりです。 これらのクラスはすべて、**Microsoft.Practices.EnterpriseLibrary.TransientFaultHandling** 名前空間に属しています。

名前空間 **Microsoft.Practices.EnterpriseLibrary.TransientFaultHandling**:

- **RetryPolicy** クラス
  - **ExecuteAction** メソッド
- **ExponentialBackoff** クラス
- **SqlDatabaseTransientErrorDetectionStrategy** クラス
- **ReliableSqlConnection** クラス
  - **ExecuteCommand** メソッド

**Microsoft.Practices.EnterpriseLibrary.TransientFaultHandling.TestSupport**名前空間:

- **AlwaysTransientErrorDetectionStrategy** クラス
- **NeverTransientErrorDetectionStrategy** クラス

EntLib60 に関する情報は以下のリンクから入手できます。

- 無料の電子ブック ダウンロード:[Microsoft Enterprise Library 開発者ガイド、第 2 版](https://www.microsoft.com/download/details.aspx?id=41145)。
- ベスト プラクティス:[再試行全般のガイダンス](../best-practices-retry-general.md) には、再試行のロジックが詳しく解説されていてお勧めです。
- NuGet ダウンロード:[Enterprise Library - Transient Fault Handling Application Block 6.0](https://www.nuget.org/packages/EnterpriseLibrary.TransientFaultHandling/)

<a id="entlib60-the-logging-block" name="entlib60-the-logging-block"></a>

### <a name="entlib60-the-logging-block"></a>EntLib60:Logging ブロック

- Logging ブロックは、きわめて柔軟性に優れた構成可能なソリューションであり、次の目的で使用できます。
  - ログ メッセージをさまざまな場所に作成して保存する。
  - メッセージを分類したりフィルタリングしたりする。
  - デバッグやトレース、監査要件、全般的なログ要件に利用できるコンテキスト情報を収集する。
- Logging ブロックは、ログ出力先が備えるログ機能を抽象化したものです。対象となるログ ストアの場所や種類に関係なく、アプリケーション コードの一貫性を確保することができます。

詳細については、「[5 - As easy as falling off a log: Use the Logging Application Block (5 - きわめて簡単: Logging アプリケーション ブロックの使用)](https://msdn.microsoft.com/library/dn440731%28v=pandp.60%29.aspx)」を参照してください。

<a id="entlib60-istransient-method-source-code" name="entlib60-istransient-method-source-code"></a>

### <a name="entlib60-istransient-method-source-code"></a>EntLib60 IsTransient メソッドのソース コード

以下に示したのは、**SqlDatabaseTransientErrorDetectionStrategy** クラスの **IsTransient** メソッドの C# ソース コードです。 どのようなエラーが一過性で、再試行する価値があるかは、このソース コードを見るとはっきりわかります (2013 年 4 月時点)。

```csharp
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

## <a name="next-steps"></a>次の手順

- SQL Database の他の一般的な接続の問題のトラブルシューティングの詳細については、「[Azure SQL Database との接続に関する一般的な問題のトラブルシューティング](sql-database-troubleshoot-common-connection-issues.md)」をご覧ください。
- [SQL Database と SQL Server の接続ライブラリ](sql-database-libraries.md)
- [SQL Server の接続プール (ADO.NET)](https://docs.microsoft.com/dotnet/framework/data/adonet/sql-server-connection-pooling)
- [*Retrying* は Apache 2.0 ライセンスで配布される汎用の再試行ライブラリです。Python で作成されています。](https://pypi.python.org/pypi/retrying)対象を選ばず、再試行の動作を簡単に追加することができます。

<!-- Link references. -->

[step-4-connect-resiliently-to-sql-with-ado-net-a78n]: https://docs.microsoft.com/sql/connect/ado-net/step-4-connect-resiliently-to-sql-with-ado-net

[step-4-connect-resiliently-to-sql-with-php-p42h]: https://docs.microsoft.com/sql/connect/php/step-4-connect-resiliently-to-sql-with-php
