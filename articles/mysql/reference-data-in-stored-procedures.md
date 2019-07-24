---
title: Azure Database for MySQL データイン レプリケーションのストアド プロシージャ
description: この記事では、データイン レプリケーションに使用されるすべてのストアド プロシージャを紹介します。
author: ajlam
ms.author: andrela
ms.service: mysql
ms.topic: conceptual
ms.date: 08/31/2018
ms.openlocfilehash: a3c88953eea95871529e8ab257f52b694db443a9
ms.sourcegitcommit: 71ee622bdba6e24db4d7ce92107b1ef1a4fa2600
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/17/2018
ms.locfileid: "53544857"
---
# <a name="azure-database-for-mysql-data-in-replication-stored-procedures"></a>Azure Database for MySQL データイン レプリケーションのストアド プロシージャ

データイン レプリケーションでは、オンプレミス、仮想マシン、または他のクラウド プロバイダーによってホストされるデータベース サービスで実行中の MySQL サーバーから Azure Database for MySQL サービスにデータを同期することができます。

次のストアド プロシージャは、マスターとレプリカ間のデータイン レプリケーションの設定または削除に使用されます。

|**ストアド プロシージャ名**|**入力パラメーター**|**出力パラメーター**|**使用上の注意**|
|-----|-----|-----|-----|
|*mysql.az_replication_change_master*|master_host<br/>master_user<br/>master_password<br/>master_host<br/>master_log_file<br/>master_log_file<br/>master_ssl_ca|該当なし|SSL モードを使用してデータを転送するには、CA 証明書のコンテキストを master_ssl_ca パラメーターに渡します。 </br><br>SSL を使用せずにデータを転送するには、空の文字列を master_ssl_ca パラメーターに渡します。|
|*mysql.az_replication _start*|該当なし|該当なし|レプリケーションを開始します。|
|*mysql.az_replication _stop*|該当なし|該当なし|レプリケーションを停止します。|
|*mysql.az_replication _remove_master*|該当なし|該当なし|マスターとレプリカ間のレプリケーション関係を削除します。|
|*mysql.az_replication_skip_counter*|該当なし|該当なし|1 つのレプリケーション エラーをスキップします。|

Azure Database for MySQL 内でマスターとレプリカの間のデータイン レプリケーションを設定するには、[データイン レプリケーションを構成する方法](howto-data-in-replication.md)を参照してください。
