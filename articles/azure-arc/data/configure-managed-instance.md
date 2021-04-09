---
title: Azure Arc 対応 SQL Managed Instance を構成する
description: Azure Arc 対応 SQL Managed Instance を構成する
services: azure-arc
ms.service: azure-arc
ms.subservice: azure-arc-data
author: vin-yu
ms.author: vinsonyu
ms.reviewer: mikeray
ms.date: 09/22/2020
ms.topic: how-to
ms.openlocfilehash: 6e2443014f6788504a11784945078187a5a72de4
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "98985871"
---
# <a name="configure-azure-arc-enabled-sql-managed-instance"></a>Azure Arc 対応 SQL Managed Instance を構成する

この記事では、Azure Arc 対応 SQL Managed Instance を構成する方法について説明します。

[!INCLUDE [azure-arc-data-preview](../../../includes/azure-arc-data-preview.md)]

## <a name="configure-resources"></a>Configure resources

### <a name="configure-using-azure-data-cli-azdata"></a>[!INCLUDE [azure-data-cli-azdata](../../../includes/azure-data-cli-azdata.md)] を使用して構成する

Azure Arc 対応 SQL Managed Instance の構成を [!INCLUDE [azure-data-cli-azdata](../../../includes/azure-data-cli-azdata.md)] を使用して編集できます。 次のコマンドを実行して構成オプションを確認します。 

```
azdata arc sql mi edit --help
```

次の例では、CPU コアとメモリの要求と制限を設定します。

```
azdata arc sql mi edit --cores-limit 4 --cores-request 2 --memory-limit 4Gi --memory-request 2Gi -n <NAME_OF_SQL_MI>
```

SQL Managed Instance に対して行われた変更を確認するには、次のコマンドを使用して、構成 yaml ファイルを表示できます。

```
azdata arc sql mi show -n <NAME_OF_SQL_MI>
```

## <a name="configure-server-options"></a>サーバー オプションの構成

作成後に、Azure Arc 対応 SQL Managed Instance のサーバー構成設定を構成できます。 この記事では、mssql エージェントの有効化/無効化などの設定を構成する方法、およびトラブルシューティングのシナリオで特定のトレース フラグを有効にする方法について説明します。

これらのいずれかの設定を変更するには、次の手順に従います。

1. ターゲット設定を含むカスタム `mssql-custom.conf` ファイルを作成します。 次の例では、SQL エージェントを有効にし、トレース フラグ 1204 を有効にします。

   ```
   [sqlagent]
   enabled=true
   
   [traceflag]
   traceflag0 = 1204
   ```

1. `mssql-custom.conf` ファイルを `master-0` ポッドの `mssql-miaa` コンテナー内の `/var/opt/mssql` にコピーします。 `<namespaceName>` をビッグ データ クラスター名に置き換えます。

   ```bash
   kubectl cp mssql-custom.conf master-0:/var/opt/mssql/mssql-custom.conf -c mssql-server -n <namespaceName>
   ```

1. SQL Server インスタンスを再起動します。  `<namespaceName>` をビッグ データ クラスター名に置き換えます。

   ```bash
   kubectl exec -it master-0  -c mssql-server -n <namespaceName> -- /bin/bash
   supervisorctl restart mssql-server
   exit
   ```


**既知の制限事項**
- 上記の手順では、Kubernetes クラスター管理者のアクセス許可が必要です
- これは、プレビュー全体で変更される可能性があります
