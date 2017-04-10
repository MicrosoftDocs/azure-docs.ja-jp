データベース トランザクション ユニット (DTU) は、データベースのトランザクションという実際の測定に基づいたデータベースの相対的な能力を表す SQL Database の測定単位です。 ここでは、完全搭載条件で、オンライン トランザクション処理 (OLTP) 要求に一般的な一連の操作を実行し、1 秒間に完了可能なトランザクション数を測定しました (詳細については、「 [ベンチマークの概要](../articles/sql-database/sql-database-benchmark-overview.md)」を参照してください)。 

たとえば、DTU が 1750 である Premium P11 データベースは、DTU が 5 である Basic データベースと比べ、DTU 換算で 350 倍の計算能力を持ちます。 

![SQL Database の概要: 階層とレベル別の 1 つのデータベースの DTU](./media/sql-database-understanding-dtus/single_db_dtus.png)

> [!NOTE]
> 既存の SQL Server データベースを移行する場合は、サードパーティ製のツールである [Azure SQL Database DTU Calculator](http://dtucalculator.azurewebsites.net/)を使用して、データベースに必要な Azure SQL Database のパフォーマンス レベルとサービス レベルを見積もることができます。
> 
> 

### <a name="dtu-vs-edtu"></a>DTU と eDTU
単一のデータベースの DTU は、エラスティック データベースの eDTU に直接変換されます。 たとえば、Basic エラスティック データベース プール内のデータベースが最大 5 eDTU を提供するとします。 これは、Basic 単一データベースと同等のパフォーマンスです。 違いは、エラスティック データベースは必要になるまでプールの eDTU を使用しない点です。 

![SQL Database の概要: 階層別のエラスティック プール](./media/sql-database-understanding-dtus/sqldb_elastic_pools.png)

単純な例を次に示します。 1000 DTU の Basic エラスティック データベース プールがあるとします。プール内には 800 個のデータベースがあります。 同時に使用されているデータベースが 800 個中 200 個のデータベースのみであれば (5 DTU X 200 = 1000)、プールの上限を超えることはなく、データベースのパフォーマンスは低下しません。 この例は、わかりやすくするために単純化しています。 実際には、もう少し複雑な計算が行われます。 ポータルでは自動計算され、データベースの使用履歴に基づいて推奨が表示されます。 推奨事項のしくみや自分で計算する方法については、[エラスティック データベース プールの価格とパフォーマンスの考慮事項](../articles/sql-database/sql-database-elastic-pool-guidance.md)に関するページを参照してください。 



<!--HONumber=Jan17_HO3-->


