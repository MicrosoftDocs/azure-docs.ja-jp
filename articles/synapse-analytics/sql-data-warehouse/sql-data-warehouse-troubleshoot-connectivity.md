---
title: 接続性のトラブルシューティング
description: 専用 SQL プール (旧称 SQL DW) の接続のトラブルシューティング。
services: synapse-analytics
author: anumjs
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: sql-dw
ms.date: 03/27/2019
ms.author: anjangsh
ms.reviewer: igorstan
ms.custom: seo-lt-2019, azure-synapse, devx-track-csharp
ms.openlocfilehash: 8b23a3634b34277b732d4ba18bef7e71c783ebd5
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "98681188"
---
# <a name="troubleshooting-connectivity-issues-in-dedicated-sql-pool-formerly-sql-dw"></a>専用 SQL プール (旧称 SQL DW) の接続に関する問題のトラブルシューティング

この記事では、専用 SQL プール データベース (旧称 SQL DW) の接続に関連する一般的な問題を解決する方法を挙げます。

## <a name="check-service-availability"></a>サービスが使えることを確認する

このサービスが利用できるかどうかを確認します。 Azure portal で、接続しようとしている専用 SQL プール (旧称 SQL DW) に移動します。 左側 TOC パネルで、 **[問題の診断と解決]** をクリックします。

![[リソース正常性] の選択](./media/sql-data-warehouse-troubleshoot-connectivity/diagnostics-link.png)

専用 SQL プール (旧称 SQL DW) の状態がここに表示されます。 サービスが **[使用可能]** ではない場合、他の手順を試してください。

![サービスは利用可能](./media/sql-data-warehouse-troubleshoot-connectivity/resource-health.png)

[リソース正常性] に専用 SQL プール (旧称 SQL DW) インスタンスが一時停止されているか、スケーリング中であることが表示されている場合、ガイダンスに従ってインスタンスを再開します。

![一時停止またはスケーリング中の専用 SQL プールのインスタンスを示すスクリーンショット。](./media/sql-data-warehouse-troubleshoot-connectivity/resource-health-pausing.png)
Azure Resource Health に関する追加情報がここにあります。

## <a name="check-for-paused-or-scaling-operation"></a>一時停止中の操作やスケーリング操作を確認する

専用 SQL プール (旧称 SQL DW) インスタンスが一時停止しているのか、スケーリング中であるのかをポータルで確認します。

![データ ウェアハウスが一時停止中かどうかを確認する方法を示すスクリーンショット。](./media/sql-data-warehouse-troubleshoot-connectivity/overview-paused.png)

サービスが一時停止されているか、スケーリング中の場合、メンテナンス スケジュール中でないことを確認します。 専用 SQL プール (旧称 SQL DW) のポータルの "*概要*" に、選択されているメンテナンス スケジュールが表示されます。

![[概要] のメンテナンス スケジュール](./media/sql-data-warehouse-troubleshoot-connectivity/overview-maintance-schedule.png)

表示されない場合、このメンテナンスがスケジュールされたイベントではないことを IT 管理者に確認します。 専用 SQL プール (旧称 SQL DW) インスタンスを再開するには、[これらの手順](pause-and-resume-compute-portal.md)に従ってください。

## <a name="check-your-firewall-settings"></a>ファイアウォール設定を確認する

専用 SQL プール (旧称 SQL DW) データベースの通信は、ポート 1433 上で行われます。  企業ネットワーク内から接続しようとしても、ポート 1433 での送信トラフィックがネットワークのファイアウォールで禁止されている場合があります。 その場合、会社の IT 部門がポート 1433 を開かない限り、[論理サーバー](../../azure-sql/database/logical-servers.md)に接続することはできません。 ファイアウォール構成の詳細は[こちら](../../azure-sql/database/firewall-configure.md?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json#create-and-manage-ip-firewall-rules)にあります。

## <a name="check-your-vnetservice-endpoint-settings"></a>VNet/サービス エンドポイント設定を確認する

エラー 40914 や 40615 が表示された場合、エラーの詳細や解決策は[こちら](../../azure-sql/database/vnet-service-endpoint-rule-overview.md?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json#errors-40914-and-40615)で確認できます。

## <a name="check-for-the-latest-drivers"></a>最新のドライバーを確認する

### <a name="software"></a>ソフトウェア

最新のツールを使用して専用 SQL プール (旧称 SQL DW) に接続していることを確認します。

- SSMS
- Azure Data Studio
- SQL Server Data Tools (Visual Studio)

### <a name="drivers"></a>ドライバー

最新版のドライバーを使用していることを確認します。  古いバージョンのドライバーは新しい機能に対応しない可能性があるため、古いバージョンを使用すると予期しない動作が発生することがあります。

- [ODBC](/sql/connect/odbc/download-odbc-driver-for-sql-server?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true)
- [JDBC](/sql/connect/jdbc/download-microsoft-jdbc-driver-for-sql-server?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true)
- [OLE DB](/sql/connect/oledb/download-oledb-driver-for-sql-server?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true)
- [PHP](/sql/connect/php/download-drivers-php-sql-server?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true)

## <a name="check-your-connection-string"></a>接続文字列を確認する

接続文字列が正しく設定されていることを確認します。  以下に例をいくつか示します。  接続文字列に関する追加情報は[こちら](sql-data-warehouse-connection-strings.md)にあります

ADO.NET の接続文字列

```csharp
Server=tcp:{your_server}.database.windows.net,1433;Database={your_database};User ID={your_user_name};Password={your_password_here};Encrypt=True;TrustServerCertificate=False;Connection Timeout=30;
```

ODBC 接続文字列

```csharp
Driver={SQL Server Native Client 11.0};Server=tcp:{your_server}.database.windows.net,1433;Database={your_database};Uid={your_user_name};Pwd={your_password_here};Encrypt=yes;TrustServerCertificate=no;Connection Timeout=30;
```

PHP 接続文字列

```PHP
Server: {your_server}.database.windows.net,1433 \r\nSQL Database: {your_database}\r\nUser Name: {your_user_name}\r\n\r\nPHP Data Objects(PDO) Sample Code:\r\n\r\ntry {\r\n   $conn = new PDO ( \"sqlsrv:server = tcp:{your_server}.database.windows.net,1433; Database = {your_database}\", \"{your_user_name}\", \"{your_password_here}\");\r\n    $conn->setAttribute( PDO::ATTR_ERRMODE, PDO::ERRMODE_EXCEPTION );\r\n}\r\ncatch ( PDOException $e ) {\r\n   print( \"Error connecting to SQL Server.\" );\r\n   die(print_r($e));\r\n}\r\n\rSQL Server Extension Sample Code:\r\n\r\n$connectionInfo = array(\"UID\" => \"{your_user_name}\", \"pwd\" => \"{your_password_here}\", \"Database\" => \"{your_database}\", \"LoginTimeout\" => 30, \"Encrypt\" => 1, \"TrustServerCertificate\" => 0);\r\n$serverName = \"tcp:{your_server}.database.windows.net,1433\";\r\n$conn = sqlsrv_connect($serverName, $connectionInfo);
```

JDBC 接続文字列

```Java
jdbc:sqlserver://yourserver.database.windows.net:1433;database=yourdatabase;user={your_user_name};password={your_password_here};encrypt=true;trustServerCertificate=false;hostNameInCertificate=*.database.windows.net;loginTimeout=30;
```

## <a name="intermittent-connection-issues"></a>接続が途切れる問題

キューに追加された要求の数が多く、サーバーに大きな負荷がかかっていないか確認します。 場合によっては、専用 SQL プール (旧称 SQL DW) を拡張し、リソースを増やす必要があります。

## <a name="common-error-messages"></a>一般的なエラー メッセージ

エラー 40914 と 40615 のエラーの詳細や解決策は[こちら](../../azure-sql/database/vnet-service-endpoint-rule-overview.md?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json#errors-40914-and-40615)で確認できます。

## <a name="still-having-connectivity-issues"></a>接続の問題がまだ解決されませんか。

[サポート チケット](sql-data-warehouse-get-started-create-support-ticket.md)を作成してください。エンジニア チームがお手伝いします。
