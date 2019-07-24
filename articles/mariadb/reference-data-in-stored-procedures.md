---
title: Azure Database for MariaDB データイン レプリケーションのストアド プロシージャ
description: この記事では、データイン レプリケーションに使用されるすべてのストアド プロシージャを紹介します。
author: ajlam
ms.author: andrela
ms.service: mariadb
ms.topic: reference
ms.date: 09/24/2018
ms.openlocfilehash: 75dc10ba3d95fd12ea99e10d321237560ee28171
ms.sourcegitcommit: 71ee622bdba6e24db4d7ce92107b1ef1a4fa2600
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/17/2018
ms.locfileid: "53535354"
---
# <a name="azure-database-for-mariadb-data-in-replication-stored-procedures"></a>Azure Database for MariaDB データイン レプリケーションのストアド プロシージャ

データイン レプリケーションでは、オンプレミス、仮想マシン、または他のクラウド プロバイダーによってホストされるデータベース サービスで実行中の MariaDB サーバーから Azure Database for MariaDB サービスにデータを同期することができます。

次のストアド プロシージャは、マスターとレプリカ間のデータイン レプリケーションの設定または削除に使用されます。

|**ストアド プロシージャ名**|**入力パラメーター**|**出力パラメーター**|**使用上の注意**|
|-----|-----|-----|-----|
|*mysql.az_replication_change_master*|master_host<br/>master_user<br/>master_password<br/>master_host<br/>master_log_file<br/>master_log_file<br/>master_ssl_ca|該当なし|SSL モードを使用してデータを転送するには、CA 証明書のコンテキストを master_ssl_ca パラメーターに渡します。 </br><br>SSL を使用せずにデータを転送するには、空の文字列を master_ssl_ca パラメーターに渡します。|
|*mysql.az_replication _start*|該当なし|該当なし|レプリケーションを開始します。|
|*mysql.az_replication _stop*|該当なし|該当なし|レプリケーションを停止します。|
|*mysql.az_replication _remove_master*|該当なし|該当なし|マスターとレプリカ間のレプリケーション関係を削除します。|
|*mysql.az_replication_skip_counter*|該当なし|該当なし|1 つのレプリケーション エラーをスキップします。|

Azure Database for MariaDB 内でマスターとレプリカの間のデータイン レプリケーションを設定するには、[データイン レプリケーションを構成する方法](howto-data-in-replication.md)に関するページを参照してください。
