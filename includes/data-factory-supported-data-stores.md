Data Factory のコピー アクティビティは、ソース データ ストアからシンク データ ストアにデータをコピーします。 Data Factory は次のデータ ストアをサポートしています。 また、任意のソースのデータを任意のシンクに書き込むことができます。 データ ストアをクリックすると、そのストアとの間でデータをコピーする方法がわかります。

| カテゴリ | データ ストア | ソースとしてサポート | シンクとしてサポート |
|:--- |:--- |:--- |:--- |
| **Azure** |[Azure BLOB Storage](../articles/data-factory/data-factory-azure-blob-connector.md) |✓  |✓  |
| &nbsp; |[Azure Data Lake Store](../articles/data-factory/data-factory-azure-datalake-connector.md) |✓  |✓  |
| &nbsp; |[Azure DocumentDB](../articles/data-factory/data-factory-azure-documentdb-connector.md) |✓ |✓  |
| &nbsp; |[Azure SQL Database](../articles/data-factory/data-factory-azure-sql-connector.md) |✓  |✓  |
| &nbsp; |[Azure SQL Data Warehouse](../articles/data-factory/data-factory-azure-sql-data-warehouse-connector.md) |✓  |✓  |
| &nbsp; |[Azure Search インデックス](../articles/data-factory/data-factory-azure-search-connector.md) | |✓  |
| &nbsp; |[Azure Table Storage](../articles/data-factory/data-factory-azure-table-connector.md) |✓  |✓  |
| **データベース** |[Amazon Redshift](../articles/data-factory/data-factory-amazon-redshift-connector.md) |✓  | |
| &nbsp; |[DB2](../articles/data-factory/data-factory-onprem-db2-connector.md)* |✓  | |
| &nbsp; |[MySQL](../articles/data-factory/data-factory-onprem-mysql-connector.md)* |✓  | |
| &nbsp; |[Oracle](../articles/data-factory/data-factory-onprem-oracle-connector.md)* |✓  |✓  |
| &nbsp; |[PostgreSQL](../articles/data-factory/data-factory-onprem-postgresql-connector.md)* |✓  | |
| &nbsp; |[SAP Business Warehouse](../articles/data-factory/data-factory-sap-business-warehouse-connector.md)* |✓  | |
| &nbsp; |[SAP HANA](../articles/data-factory/data-factory-sap-hana-connector.md)* |✓  | |
| &nbsp; |[SQL Server](../articles/data-factory/data-factory-sqlserver-connector.md)* |✓  |✓  |
| &nbsp; |[Sybase](../articles/data-factory/data-factory-onprem-sybase-connector.md)* |✓  | |
| &nbsp; |[Teradata](../articles/data-factory/data-factory-onprem-teradata-connector.md)* |✓  | |
| **NoSQL** |[Cassandra](../articles/data-factory/data-factory-onprem-cassandra-connector.md)* |✓  | |
| &nbsp; |[MongoDB](../articles/data-factory/data-factory-on-premises-mongodb-connector.md)* |✓  | |
| **ファイル** |[Amazon S3](../articles/data-factory/data-factory-amazon-simple-storage-service-connector.md) |✓  | |
| &nbsp; |[ファイル システム](../articles/data-factory/data-factory-onprem-file-system-connector.md)* |✓  |✓  |
| &nbsp; |[FTP](../articles/data-factory/data-factory-ftp-connector.md) |✓  | |
| &nbsp; |[HDFS](../articles/data-factory/data-factory-hdfs-connector.md)* |✓  | |
| &nbsp; |[SFTP](../articles/data-factory/data-factory-sftp-connector.md) |✓  | |
| **その他** |[汎用 HTTP](../articles/data-factory/data-factory-http-connector.md) |✓  | |
| &nbsp; |[汎用 OData](../articles/data-factory/data-factory-odata-connector.md) |✓  | |
| &nbsp; |[汎用 ODBC](../articles/data-factory/data-factory-odbc-connector.md)* |✓  | |
| &nbsp; |[Salesforce](../articles/data-factory/data-factory-salesforce-connector.md) |✓  | |
| &nbsp; |[Web テーブル (HTML のテーブル)](../articles/data-factory/data-factory-web-table-connector.md) |✓  | |
| &nbsp; |[GE Historian](../articles/data-factory/data-factory-odbc-connector.md#ge-historian-store)* |✓  | | |

> [!NOTE]
> * が付いたデータ ストアは、オンプレミスと Azure IaaS のどちらでもサポートされます。ただし、オンプレミス/Azure IaaS のコンピューターに [Data Management Gateway](../articles/data-factory/data-factory-data-management-gateway.md) をインストールする必要があります。
>
>
