---
title: 管理ストアド プロシージャ - Azure Database for MySQL
description: データイン レプリケーションの構成、タイムゾーンの設定、クエリの終了を行う場合に役立つ Azure Database for MySQL のストアド プロシージャについて説明します。
author: savjani
ms.author: pariks
ms.service: mysql
ms.topic: conceptual
ms.date: 3/18/2020
ms.openlocfilehash: 7a1aa061bb8c8be3a676e0e5bb690b2a9749b6c8
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "94536134"
---
# <a name="azure-database-for-mysql-management-stored-procedures"></a>Azure Database for MySQL の管理ストアド プロシージャ

ストアド プロシージャは、MySQL サーバーの管理に役立つ Azure Database for MySQL サーバーで使用できます。 これには、サーバーの接続管理、クエリ、データイン レプリケーションの設定が含まれます。  

## <a name="data-in-replication-stored-procedures"></a>データイン レプリケーションのストアド プロシージャ

データイン レプリケーションでは、オンプレミス、仮想マシン、または他のクラウド プロバイダーによってホストされるデータベース サービスで実行中の MySQL サーバーから Azure Database for MySQL サービスにデータを同期することができます。

次のストアド プロシージャは、ソースとレプリカの間のデータイン レプリケーションを設定または削除するために使用されます。

|**ストアド プロシージャ名**|**入力パラメーター**|**出力パラメーター**|**使用上の注意**|
|-----|-----|-----|-----|
|*mysql.az_replication_change_master*|master_host<br/>master_user<br/>master_password<br/>master_host<br/>master_log_file<br/>master_log_file<br/>master_ssl_ca|該当なし|SSL モードを使用してデータを転送するには、CA 証明書のコンテキストを master_ssl_ca パラメーターに渡します。 </br><br>SSL を使用せずにデータを転送するには、空の文字列を master_ssl_ca パラメーターに渡します。|
|*mysql.az_replication _start*|該当なし|該当なし|レプリケーションを開始します。|
|*mysql.az_replication _stop*|該当なし|該当なし|レプリケーションを停止します。|
|*mysql.az_replication _remove_master*|該当なし|該当なし|ソースとレプリカの間のレプリケーション関係を削除します。|
|*mysql.az_replication_skip_counter*|該当なし|該当なし|1 つのレプリケーション エラーをスキップします。|

Azure Database for MySQL 内でソースとレプリカの間のデータイン レプリケーションを設定するには、[データイン レプリケーションを構成する方法](howto-data-in-replication.md)を参照してください。

## <a name="other-stored-procedures"></a>その他のストアド プロシージャ

Azure Database for MySQL では、以下のストアド プロシージャがサーバー管理に使用できます。

|**ストアド プロシージャ名**|**入力パラメーター**|**出力パラメーター**|**使用上の注意**|
|-----|-----|-----|-----|
|*mysql.az_kill*|processlist_id|該当なし|[`KILL CONNECTION`](https://dev.mysql.com/doc/refman/8.0/en/kill.html) コマンドと同等です。 接続で実行されているステートメントを終了した後、指定された processlist_id に関連する接続を終了します。|
|*mysql.az_kill_query*|processlist_id|該当なし|[`KILL QUERY`](https://dev.mysql.com/doc/refman/8.0/en/kill.html) コマンドと同等です。 接続で現在実行されているステートメントを終了します。 接続自体をアクティブのままにします。|
|*mysql.az_load_timezone*|該当なし|該当なし|タイム ゾーン テーブルを読み込み、パラメーター `time_zone` を名前付きの値に設定できるようにします (例 "US/Pacific")。|

## <a name="next-steps"></a>次のステップ
- [データイン レプリケーション](howto-data-in-replication.md) を設定する方法について確認する
- [タイム ゾーン テーブル](howto-server-parameters.md#working-with-the-time-zone-parameter) を使用する方法について確認する
