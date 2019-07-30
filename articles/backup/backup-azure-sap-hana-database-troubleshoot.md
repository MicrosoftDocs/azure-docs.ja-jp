---
title: Azure Backup を使用した SAP HANA データベースのバックアップ中のエラーをトラブルシューティングする | Microsoft Docs
description: Azure Backup を使用して SAP HANA データベースをバックアップするときに発生する可能性のある一般的なエラーをトラブルシューティングする方法について説明します。
author: pvrk
manager: vijayts
ms.service: backup
ms.topic: conceptual
ms.date: 07/22/2019
ms.author: pullabhk
ms.openlocfilehash: 221b669c141681749709d6a5a406c78499f21032
ms.sourcegitcommit: c72ddb56b5657b2adeb3c4608c3d4c56e3421f2c
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/24/2019
ms.locfileid: "68465469"
---
# <a name="troubleshoot-backup-of-sap-hana-databases-on-azure"></a>Azure での SAP HANA データベースのバックアップをトラブルシューティングする

この記事では、Azure 仮想マシン上で SAP HANA データベースをバックアップするためのトラブルシューティング情報を提供します。

## <a name="prerequisites"></a>前提条件

[前提条件](backup-azure-sap-hana-database.md#prerequisites)の一部として、HANA がインストールされている仮想マシン上で事前登録スクリプトが実行されていることを確認してください。

### <a name="setting-up-permissions"></a>アクセス許可の設定

事前登録スクリプトは、次のことを実行します。

1. HANA システム内に AZUREWLBACKUPHANAUSER を作成し、次の必要なロールとアクセス許可を追加します。
    - DATABASE ADMIN: 復元中に新しい DB を作成します。
    - CATALOG READ: バックアップ カタログを読み取ります。
    - SAP_INTERNAL_HANA_SUPPORT いくつかのプライベート テーブルにアクセスします。
2. すべての操作 (データベース クエリ、復元操作、バックアップの構成および実行) を処理するためのキーを HANA プラグインの Hdbuserstore に追加します。
   
   キーの作成を確認するには、SIDADM 資格情報を使用して HANA コンピューター上で HDBSQL コマンドを実行します。

    ``` hdbsql
    hdbuserstore list
    ```
    
    このコマンド出力には、AZUREWLBACKUPHANAUSER として示されるユーザーと共に {SID}{DBNAME} キーが表示されます。

> [!NOTE]
> **/usr/sap/{SID}/home/.hdb/** の下に固有の一連の SSFS ファイルがあることを確認してください。 このパスにはフォルダーが 1 つしか存在しません。

### <a name="setting-up-backint-parameters"></a>BackInt パラメーターの設定

バックアップのためのデータベースが選択されると、Azure Backup サービスは DATABASE レベルで次の backInt パラメーターを構成します。

- [catalog_backup_using_backint:true]
- [enable_accumulated_catalog_backup:false]
- [parallel_data_backup_backint_channels:1]
- [log_backup_timeout_s:900)]
- [backint_response_timeout:7200]

> [!NOTE]
> これらのパラメーターが HOST レベルに存在*しない*ことを確認してください。 ホスト レベルのパラメーターによってこれらのパラメーターがオーバーライドされるため、予期しない動作が発生することがあります。

## <a name="common-user-errors"></a>一般的なユーザー エラー

### <a name="usererrorinopeninghanaodbcconnection"></a>UserErrorInOpeningHanaOdbcConnection

data| エラー メッセージ | 考えられる原因 | 推奨される操作 |
|---|---|---|
| HANA システムに接続できませんでした。 システムが稼働していることを確認してください。| HANA データベースがダウンしているため、Azure Backup サービスは HANA に接続できません。 または、HANA は実行されているが、Azure Backup サービスの接続が許可されていません。 | HANA データベースまたはサービスがダウンしているかどうかを確認してください。 HANA データベースまたはサービスが稼働している場合は、[すべてのアクセス許可が設定されている](#setting-up-permissions)かどうかを確認してください。 キーがない場合は、事前登録スクリプトを再実行して新しいキーを作成します。 |

### <a name="usererrorinvalidbackintconfiguration"></a>UserErrorInvalidBackintConfiguration

| エラー メッセージ | 考えられる原因 | 推奨される操作 |
|---|---|---|
| 無効な Backint 構成が検出されました。 保護を停止し、データベースを再構成してください。| backInt パラメーターが Azure Backup に対して正しく指定されていません。 | [パラメーターが設定されている](#setting-up-backint-parameters)かどうかを確認してください。 backInt ベースのパラメーターが HOST に存在する場合は、それらを削除します。 パラメーターが HOST レベルに存在しないが、データベース レベルで手動で変更されている場合は、それらを先に説明した適切な値に戻します。 または、Azure portal から **[保護を停止してバックアップ データを保持する]** を実行してから、 **[バックアップの再開]** を選択します。|
