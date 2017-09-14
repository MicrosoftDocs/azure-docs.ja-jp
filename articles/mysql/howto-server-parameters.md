---
title: "Azure Database for MySQL のサーバー パラメーターの構成方法 | Microsoft Docs"
description: "この記事では、Azure ポータルを使用して Azure Database for MySQL サーバーで使用できるサーバー パラメータを構成する方法について説明します。"
services: mysql
author: v-chenyh
ms.author: v-chenyh
manager: jhubbard
editor: jasonwhowell
ms.service: mysql-database
ms.topic: article
ms.date: 06/19/2017
ms.translationtype: Human Translation
ms.sourcegitcommit: 1500c02fa1e6876b47e3896c40c7f3356f8f1eed
ms.openlocfilehash: 718bbf359253849fbc989c563ffd6d1099f92348
ms.contentlocale: ja-jp
ms.lasthandoff: 06/30/2017

---

# <a name="how-to-configure-server-parameters-in-azure-database-for-mysql-using-the-azure-portal"></a>Azure ポータルを使用して Azure Database for MySQL サーバーのサーバー パラメータを構成する方法

Azure Database for MySQL では、いくつかのサーバー パラメーターの構成をサポートします。 この記事では、Azure ポータルを使用してこれらのパラメーターを構成する方法について説明し、サポートされるパラメーター、既定値、および有効値の範囲を一覧で示します。 すべてのサーバー パラメーターを調整できるわけではありません。 ここに示されているもののみがサポートされます。

## <a name="navigate-to-server-parameters-blade-on-azure-portal"></a>Azure ポータルで [サーバー パラメーター] ブレードに移動する

Azure ポータルにログインし、Azure Database for MySQL サーバー名をクリックします。 **[設定]** セクションの **[サーバー パラメーター]** をクリックして、Azure Database for MySQL の [サーバー パラメーター] ブレードを開きます。

![Azure ポータルの [サーバー パラメーター] ブレード](./media/howto-server-parameters/auzre-portal-server-parameters.png)

## <a name="list-of-configurable-server-parameters"></a>構成可能なサーバー パラメーターの一覧

次の表は、現在サポートされているサーバーのパラメーターを示しています。 アプリケーションの要件に従って、これらのパラメーターを構成できます。

> [!div class="mx-tableFixed"]
|パラメーター名|既定値|範囲|説明|
|---|---|---|---|
|binlog_group_commit_sync_delay|1,000|0、11 ～ 1000000|バイナリ ログのコミットで、バイナリ ログ ファイルをディスクに同期する前に待機する時間 (マイクロ秒単位) を制御します。|
|binlog_group_commit_sync_no_delay_count|0|0 ～ 1000000|binlog-group-commit-sync-delay の指定に従って現在の遅延を中止する前に待機するトランザクションの最大数。|
|character_set_server|LATIN1|BIG5、UTF8MB4 など。|charset_name を既定のサーバーの文字セットとして使用します。|
|div_precision_increment|4|0 ～ 30|除算演算の結果のスケールを増やすために使用する桁数。|
|group_concat_max_len|1024|4 ～ 16777216|GROUP_CONCAT() で許可される結果の最大長 (バイト単位)。|
|innodb_adaptive_hash_index|ON|ON、OFF|innodb 適応型ハッシュインデックスを有効にするか無効にするか。|
|innodb_lock_wait_timeout|50|1 ～ 3600|InnoDB トランザクションが行ロックを行うために待機する時間 (秒単位)。この時間が経過すると待機は断念されます。|
|interactive_timeout|1800|10 ～ 1800|対話型接続でサーバーがアクティビティを行うために待機する秒数。この秒数が経過すると、接続が閉じます。|
|log_bin_trust_function_creators|OFF|ON、OFF|この変数は、バイナリ ログが有効になっているときに適用されます。 安全でないイベントのバイナリ ログへの書き込みを引き起こすストアド関数を作成しないという点で、ストアド関数の作成者を信頼できるかどうかを制御します。|
|log_queries_not_using_indexes|OFF|ON、OFF|すべての行を取得することが予想されるクエリを、低速クエリ ログに記録します。|
|log_slow_admin_statements|OFF|ON、OFF|低速クエリ ログに書き込まれる、ステートメント内の低速管理ステートメントを含みます。|
|log_throttle_queries_not_using_indexes|0|0 ～ 4294967295|低速クエリ ログに書き込むことができるクエリの 1 分当たりのクエリの数を制限します。|
|long_query_time|10|1 ～ 1E+100|クエリがこの秒数よりも長くかかる場合、サーバーは、Slow_queries 状態変数を増分します。|
|max_allowed_packet|536870912|1024 ～ 1073741824|1 つのパケットまたは生成/中間文字列の最大サイズ。またはmysql_stmt_send_long_data() C API 関数によって送信されるパラメーター。|
|min_examined_row_limit|0|0 ～ 18446744073709551615|構成された行数を超えるクエリを、低速クエリ ログに記録します。 |
|server_id|3293747068|1000 ～ 4294967295|レプリケーションでマスターとスレーブに一意の ID を与えるために使用されるサーバー ID。|
|slave_net_timeout|60|30 ～ 3600|スレーブがマスターからデータを取得するために待機する秒数。この秒数を経過すると、スレーブは接続が切断されているとみなして読み取りを中止し、再接続を試行します。|
|slow_query_log|OFF|ON、OFF|低速クエリ ログを有効または無効にします。|
|sql_mode|0 が選択されています|ALLOW_INVALID_DATES、IGNORE_SPACE など。|現在のサーバー SQL モード。|
|time_zone|SYSTEM|例: -8:00、+05:30|サーバーのタイム ゾーン。|
|wait_timeout|120|60 ～ 240|非対話型接続でサーバーがアクティビティを行うために待機する秒数。この秒数が経過すると、接続が閉じます。|

## <a name="next-steps"></a>次のステップ
- [Azure Database for MySQL の接続ライブラリ](concepts-connection-libraries.md)

