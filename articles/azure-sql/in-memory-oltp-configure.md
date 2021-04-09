---
title: インメモリ OLTP による SQL のトランザクション パフォーマンスの向上
description: インメモリ OLTP を導入し、Azure SQL Database と Azure SQL Managed Instance の既存データベースのトランザクション パフォーマンスを向上させます。
services: sql-database
ms.service: sql-database
ms.custom: sqldbrb=2
ms.subservice: development
ms.topic: how-to
author: stevestein
ms.author: sstein
ms.reviewer: MightyPen
ms.date: 11/07/2018
ms.openlocfilehash: e17e98e784b7453c87814c5cce5c03568f66b1cb
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "91619748"
---
# <a name="use-in-memory-oltp-to-improve-your-application-performance-in-azure-sql-database-and-azure-sql-managed-instance"></a>インメモリ OLTP を使用して Azure SQL Database と Azure SQL Managed Instance のアプリケーション パフォーマンスを向上させる
[!INCLUDE[appliesto-sqldb-sqlmi](includes/appliesto-sqldb-sqlmi.md)]

[インメモリ OLTP](in-memory-oltp-overview.md) は、[Premium および Business Critical レベル](database/service-tiers-vcore.md)のデータベースで、価格レベルを上げることなくトランザクション処理、データ インジェスト、一時的なデータ シナリオのパフォーマンスを向上させるために使用できます。

> [!NOTE]
> [Quorum 社が Azure SQL Database を使用して DTU を 70% 下げながら、主要なデータベースのワークロードを 2 倍にした](https://customers.microsoft.com/story/quorum-doubles-key-databases-workload-while-lowering-dtu-with-sql-database)方法を学習します

既存のデータベースでインメモリ OLTP を採用するには、以下の手順に従います。

## <a name="step-1-ensure-you-are-using-a-premium-and-business-critical-tier-database"></a>手順 1:Premium および Business Critical レベルのデータベースを使用していることを確認する

インメモリ OLTP は、Premium および Business Critical レベルのデータベースでのみサポートされています。 返された結果が (0 ではなく) 1 である場合は、インメモリがサポートされています。

```sql
SELECT DatabasePropertyEx(Db_Name(), 'IsXTPSupported');
```

*XTP* は、*Extreme Transaction Processing* (大量トランザクション処理) の略です。

## <a name="step-2-identify-objects-to-migrate-to-in-memory-oltp"></a>手順 2:インメモリ OLTP に移行するオブジェクトを特定する

SSMS には、アクティブなワークロードがあるデータベースに対して実行できる **トランザクション パフォーマンス分析の概要** レポートが用意されています。 このレポートを使用して、インメモリ OLTP への移行の候補となるテーブルとストアド プロシージャを特定できます。

SSMS でこのレポートを生成するには、

* **オブジェクト エクスプローラー** でデータベース ノードを右クリックし、
* **[レポート]**  >  **[標準レポート]**  >  **[トランザクション パフォーマンス分析の概要]** の順にクリックします。

詳細については、「 [テーブルまたはストアド プロシージャをインメモリ OLTP に移植する必要があるかどうかの確認](/sql/relational-databases/in-memory-oltp/determining-if-a-table-or-stored-procedure-should-be-ported-to-in-memory-oltp)」を参照してください。

## <a name="step-3-create-a-comparable-test-database"></a>手順 3:比較用のテスト データベースを作成する

メモリ最適化テーブルに変換するとメリットが得られるテーブルがデータベース内にあるとレポートに記載されていたとします。 このような場合は、まずテストを実施して、指摘された点について確認することをお勧めします。

そのためには、運用データベースのテスト コピーが必要です。 テスト データベースは、運用データベースと同じレベルのサービス層に配置する必要があります。

テストを容易にするために、テスト データベースを次のように調整します。

1. SSMS を使用して、テスト データベースに接続します。
2. クエリで WITH (SNAPSHOT) オプションを使用しなくても済むように、次の T-SQL ステートメントに示すようにデータベース オプションを設定します。

   ```sql
   ALTER DATABASE CURRENT
    SET
        MEMORY_OPTIMIZED_ELEVATE_TO_SNAPSHOT = ON;
   ```

## <a name="step-4-migrate-tables"></a>手順 4:テーブルを移行する

テストするテーブルのメモリ最適化コピーを作成し、データを入力する必要があります。 次のいずれかを使用して作成できます。

* SSMS の便利なメモリ最適化ウィザード
* 手動による T-SQL の実行

### <a name="memory-optimization-wizard-in-ssms"></a>SSMS のメモリ最適化ウィザード

この移行オプションを使用する手順は、以下のとおりです。

1. SSMS を使用して、テスト データベースに接続します。
2. **オブジェクト エクスプローラー** で、テーブルを右クリックし、 **[メモリ最適化アドバイザー]** をクリックします。

   **テーブル メモリ オプティマイザー アドバイザー** ウィザードが表示されます。
3. ウィザードで、 **[移行の検証]** (または **[次へ]** ) をクリックし、メモリ最適化テーブルでサポートされていない機能がテーブルに含まれているかどうかを確認します。 詳細については、次を参照してください。

   * 「 *メモリ最適化アドバイザー* 」の [メモリ最適化のチェック リスト](/sql/relational-databases/in-memory-oltp/memory-optimization-advisor)
   * [インメモリ OLTP でサポートされていない Transact-SQL の構造](/sql/relational-databases/in-memory-oltp/transact-sql-constructs-not-supported-by-in-memory-oltp)
   * [インメモリ OLTP への移行](/sql/relational-databases/in-memory-oltp/plan-your-adoption-of-in-memory-oltp-features-in-sql-server)
4. サポートされていない機能がテーブルになければ、アドバイザーで実際のスキーマとデータの移行を実行できます。

### <a name="manual-t-sql"></a>手動による T-SQL の実行

この移行オプションを使用する手順は、以下のとおりです。

1. SSMS (または同様のユーティリティ) を使用して、テスト データベースに接続します。
2. 使用するテーブルとそのインデックスに対応した完全な T-SQL スクリプトを取得します。

   * SSMS で、テーブル ノードを右クリックします。
   * **[テーブルをスクリプト化]**  >  **[CREATE To (作成先)]**  >  **[新しいクエリ ウィンドウ]** の順にクリックします。
3. スクリプト ウィンドウで、WITH (MEMORY_OPTIMIZED = ON) を CREATE TABLE ステートメントに追加します。
4. CLUSTERED インデックスがある場合は、NONCLUSTERED に変更します。
5. SP_RENAME を使用して、既存のテーブルの名前を変更します。
6. 編集した CREATE TABLE スクリプトを実行して、テーブルのメモリ最適化コピーを新規作成します。
7. INSERT...SELECT * INTO を使用して、このメモリ最適化テーブルにデータをコピーします。

```sql
INSERT INTO <new_memory_optimized_table>
        SELECT * FROM <old_disk_based_table>;
```

## <a name="step-5-optional-migrate-stored-procedures"></a>手順 5 (省略可能):ストアド プロシージャを移行する

インメモリ機能では、ストアド プロシージャに変更を加えて、パフォーマンスを向上することもできます。

### <a name="considerations-with-natively-compiled-stored-procedures"></a>ネイティブ コンパイル ストアド プロシージャに関する考慮事項

ネイティブ コンパイル ストアド プロシージャでは、T-SQL WITH 句に次のオプションが必要です。

* NATIVE_COMPILATION
* SCHEMABINDING: これを指定されたテーブルでは、ストアド プロシージャにより、そのストアド プロシージャを削除しない限り、ストアド プロシージャに影響を与えるような変更を列の定義に加えることができません。

ネイティブ モジュールでは、トランザクション管理用に大きな [ATOMIC ブロック](/sql/relational-databases/in-memory-oltp/atomic-blocks-in-native-procedures) を 1 つ使用する必要があります。 明示的 BEGIN TRANSACTION、または ROLLBACK TRANSACTION の役割はありません。 コードでビジネス規則違反が検出されると、 [THROW](/sql/t-sql/language-elements/throw-transact-sql) ステートメントを含むアトミック ブロックが停止する可能性があります。

### <a name="typical-create-procedure-for-natively-compiled"></a>ネイティブ コンパイル向けの一般的な CREATE PROCEDURE

ネイティブ コンパイル ストアド プロシージャを作成する T-SQL は、一般的に、次のテンプレートのようになります。

```sql
CREATE PROCEDURE schemaname.procedurename
    @param1 type1, …
    WITH NATIVE_COMPILATION, SCHEMABINDING
    AS
        BEGIN ATOMIC WITH
            (TRANSACTION ISOLATION LEVEL = SNAPSHOT,
            LANGUAGE = N'your_language__see_sys.languages'
            )
        …
        END;
```

* TRANSACTION_ISOLATION_LEVEL は、SNAPSHOT がネイティブ コンパイル ストアド プロシージャで最も一般的な値です。 ただし、その他の値のサブセットもサポートされています。
  
  * REPEATABLE READ
  * SERIALIZABLE
* LANGUAGE の値は、sys.languages ビューに存在している必要があります。

### <a name="how-to-migrate-a-stored-procedure"></a>ストアド プロシージャを移行する方法

移行の手順は次のとおりです。

1. 通常の解釈されたストアド プロシージャを作成する CREATE PROCEDURE スクリプトを取得します。
2. 前述のテンプレートと一致するようにヘッダーを書き直します。
3. ネイティブ コンパイル ストアド プロシージャでサポートされていない機能がこのストアド プロシージャの T-SQL コードで使用されているかどうかを確認します。 必要な場合は、回避策を実装します。

   詳細については、「 [ネイティブ コンパイル ストアド プロシージャの移行に関する問題](/sql/relational-databases/in-memory-oltp/a-guide-to-query-processing-for-memory-optimized-tables)」を参照してください。
4. SP_RENAME を使用して、元のストアド プロシージャの名前を変更します。 または、削除します。
5. 編集した CREATE PROCEDURE T-SQL スクリプトを実行します。

## <a name="step-6-run-your-workload-in-test"></a>手順 6:ワークロードをテスト実行する

運用データベースで実行するワークロードと同様のワークロードをテスト データベースで実行します。 これにより、テーブルとストアド プロシージャにインメモリ機能を使用したことでパフォーマンスがどれほど向上したかがわかります。

ワークロードの主な属性は次のとおりです。

* コンカレント接続数
* 読み取り/書き込みの比率

テスト ワークロードを調整して実行するには、インメモリに関する[こちら](in-memory-oltp-overview.md)の記事で説明されている、便利な `ostress.exe` ツールの使用を検討してください。

ネットワーク待ち時間を最小限に抑えるために、データベースが存在するのと同じ Azure リージョンでテストを実行してください。

## <a name="step-7-post-implementation-monitoring"></a>手順 7:実装後の監視

運用環境でインメモリ機能の実装によるパフォーマンスへの影響を監視することを検討してください。

* [インメモリ ストレージを監視する](in-memory-oltp-monitor-space.md)。
* [動的管理ビューを使用した監視](database/monitoring-with-dmvs.md)

## <a name="related-links"></a>関連リンク

* [インメモリ OLTP (インメモリ最適化)](/sql/relational-databases/in-memory-oltp/in-memory-oltp-in-memory-optimization)
* [ネイティブ コンパイル ストアド プロシージャの概要](/sql/relational-databases/in-memory-oltp/a-guide-to-query-processing-for-memory-optimized-tables)
* [メモリ最適化アドバイザー](/sql/relational-databases/in-memory-oltp/memory-optimization-advisor)
