---
title: PostgreSQL ポートの変更
description: Azure Arc 対応 PostgreSQL Hyperscale サーバー グループがリッスンしているポートを変更します。
services: azure-arc
ms.service: azure-arc
ms.subservice: azure-arc-data
author: TheJY
ms.author: jeanyd
ms.reviewer: mikeray
ms.date: 11/02/2020
ms.topic: how-to
ms.openlocfilehash: 45424408c790e4921be48464818c55fe74313fd3
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "93328656"
---
# <a name="change-the-port-on-which-the-server-group-is-listening"></a>サーバー グループがリッスンしているポートの変更 

ポートの変更は、サーバー グループの標準的な編集操作です。 ポートを変更するには、次のコマンドを実行します。
```console
 azdata arc postgres server edit -n <server group name> --port <desired port number>
```

たとえば、サーバー グループの名前が _postgres01_ である場合に、これがポート _866_ でリッスンするようにしたいとします。 次のコマンドを実行します。
```console
 azdata arc postgres server edit -n postgres01 --port 866
```

## <a name="verify-that-the-port-was-changed"></a>ポートが変更されたことを確認する

ポートが変更されたことを確認するには、次のコマンドを実行して、サーバー グループの構成を表示します。
```console
azdata arc postgres server show -n <server group name>
```

そのコマンドの出力で、サーバー グループの仕様の "service" セクション内にある項目 "port" について表示されているポート番号を確認します。
または、サーバー グループの仕様の "status" セクション内にある項目 "externalEndpoint" で、構成したポート番号の前に IP アドレスがあることを確認できます。

実例として、上記の例を続行する場合は、次のコマンドを実行します。
```console
azdata arc postgres server show -n postgres01
```

ポート 866 が参照されていることが、こちらでわかります。

```console
"service": {
      "port": 866,
      "type": "LoadBalancer"
    },
```
また、こちらも参照してください。

```console
"status": {
    "externalEndpoint": "12.678.345.09:866",
    "logSearchDashboard": "https://12.345.678.90:30777/kibana/app/kibana#/discover?_a=(query:(language:kuery,query:'custom_resource_name:postgres01'))",
    "metricsDashboard": "https://12.345.678.90:30777/grafana/d/postgres-metrics?var-Namespace=arc&var-Name=postgres01",
    "readyPods": "3/3",
    "state": "Ready"
  }
```
## <a name="next-steps"></a>次のステップ
- [サーバー グループに接続する方法](get-connection-endpoints-and-connection-strings-postgres-hyperscale.md)を参照します。
- このドキュメントの、方法、管理、構成、スケーリングに関する各セクションで、サーバー グループの他の側面を構成する方法について参照します。
