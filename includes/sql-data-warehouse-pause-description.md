
<!--
includes/sql-data-warehouse-include-pause-description.md

Latest Freshness check:  2016-04-22 , barbkess.

As of circa 2016-04-22, the following topics might include this include:
articles/sql-data-warehouse/sql-data-warehouse-manage-scale-out-tasks.md
articles/sql-data-warehouse/sql-data-warehouse-manage-scale-out-tasks-powershell.md
articles/sql-data-warehouse/sql-data-warehouse-manage-scale-out-tasks-rest-api.md

-->
コストを節約するために、オンデマンドでコンピューティング リソースを一時停止および再開できます。 たとえば、夜間と週末にデータベースを使用しない場合、その期間にデータベースを一時停止して、日中に再開することができます。 データベースが一時停止されている間は、DWU に対して課金されません。

データベースを一時停止している場合

* コンピューティング リソースとメモリ リソースは、データ センターで使用可能なリソースのプールに返されます。
* DWU のコストは、一時停止の期間は 0 になります。
* データ ストレージは影響を受けず、データはそのまま残ります。 
* SQL Data Warehouse では、実行中またはキューに格納されたすべての操作を取り消します。



<!--HONumber=Nov16_HO3-->


