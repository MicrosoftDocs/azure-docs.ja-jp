---
title: "Azure Database for PostgreSQL のサーバー ログ | Microsoft Docs"
description: "Azure Database for PostgreSQL でクエリ ログとエラーログを生成します。"
services: postgresql
author: SaloniSonpal
ms.author: salonis
manager: jhubbard
editor: jasonwhowell
ms.service: postgresql
ms.topic: article
ms.date: 05/10/2017
ms.translationtype: Human Translation
ms.sourcegitcommit: 71fea4a41b2e3a60f2f610609a14372e678b7ec4
ms.openlocfilehash: c384171c806e929a425e4299c81f0fb2321dc91b
ms.contentlocale: ja-jp
ms.lasthandoff: 05/10/2017

---
# <a name="server-logs-in-azure-database-for-postgresql"></a>Azure Database for PostgreSQL のサーバー ログ 
Azure Database for PostgreSQL ではクエリ ログとエラー ログが生成されます。 ただし、トランザクション ログへのアクセスはサポートされていません。 クエリ ログとエラー ログは、構成エラーや十分に最適化されていないパフォーマンスの特定、トラブルシューティング、および修復に使用できます。 詳細については、「[Error Reporting and Logging (エラー レポートとログ記録)](https://www.postgresql.org/docs/9.6/static/runtime-config-logging.html)」を参照してください。

## <a name="access-server-logs"></a>サーバー ログへのアクセス
Azure PostgreSQL サーバーのエラー ログは、Azure Portal、[Azure CLI](howto-configure-server-logs-using-cli.md)、および Azure REST API を使用して表示およびダウンロードできます。

## <a name="log-retention"></a>ログのリテンション期間
システム ログのリテンション期間を設定するには、サーバーに関連付けられている **log\_retention\_period** パラメーターを使用します。 このパラメーターは日単位で (確認が必要)、 既定値は 3 日間、 最大値は 7 日間です。 サーバーには、保持されているログ ファイルを格納できるストレージが十分に確保されている必要があります。
ログ ファイルのローテーションは、1 時間ごとか 100 MB ごとのどちらか早い方のタイミングで行われます。

## <a name="configure-logging-for-azure-postgresql-server"></a>Azure Database for PostgreSQL サーバーのログ記録の構成
サーバーに対してクエリとエラーのログ記録を有効にできます。 エラー ログには、自動バキューム、接続、およびチェックポイント情報を含めることができます。

PostgreSQL DB インスタンスのクエリのログ記録を有効にするには、log\_statement と log\_min\_duration\_statement の 2 つのサーバー パラメーターを設定します。

**log\_statement** パラメーターは、どの SQL ステートメントをログに記録するかを制御します。 このパラメーターを ***all*** に設定して、すべてのステートメントを記録することをお勧めします。既定値は none です(確認が必要)。

**log\_min\_duration\_statement** パラメーターでは、記録するステートメントの制限をミリ秒単位で設定します。 このパラメーター設定よりも長く実行されているすべての SQL ステートメントが記録されます。 既定ではこのパラメーターは無効になっており、マイナス 1 (-1) に設定されています。 このパラメーターを有効にすると、アプリケーションで最適化されていないクエリを追跡する際に役に立ちます。

**log\_min\_messages** を使用すると、サーバー ログに書き込まれるメッセージ レベルを制御できます。 既定値は "警告" です  (確認が必要)。

こうした設定の詳細については、「[Error Reporting and Logging (エラー レポートとログ記録)](https://www.postgresql.org/docs/9.6/static/runtime-config-logging.html)」を参照してください。 特に Azure Database for PostgreSQL サーバー パラメーターの構成については、「[Azure Database for PostgreSQL のサーバー ログ](concepts-server-logs.md)」を参照してください。

## <a name="next-steps"></a>次のステップ
- Azure CLI コマンド ライン インターフェイスを使用してログにアクセスするには、「[Configure and access server logs using Azure CLI (Azure CLI を使用したサーバー ログの構成とログへのアクセス)](howto-configure-server-logs-using-cli.md)」を参照してください
- サーバー パラメーターの詳細については、「[Customize server configuration parameters using Azure CLI (Azure CLI を使用したサーバー構成パラメーターのカスタマイズ)](howto-configure-server-parameters-using-cli.md)」を参照してください。
