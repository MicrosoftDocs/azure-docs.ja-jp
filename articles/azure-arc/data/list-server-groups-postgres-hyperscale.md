---
title: Azure Arc データ コントローラーに作成されている Azure Arc 対応 PostgreSQL Hyperscale サーバー グループの一覧を表示する
description: Azure Arc データ コントローラーに作成されている Azure Arc 対応 PostgreSQL Hyperscale サーバー グループの一覧を表示する
services: azure-arc
ms.service: azure-arc
ms.subservice: azure-arc-data
author: TheJY
ms.author: jeanyd
ms.reviewer: mikeray
ms.date: 09/22/2020
ms.topic: how-to
ms.openlocfilehash: 7577ca4b8a1d7db7ea99aadfef4fd2a445b66425
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "90932606"
---
# <a name="list-the-azure-arc-enabled-postgresql-hyperscale-server-groups-created-in-an-azure-arc-data-controller"></a>Azure Arc データ コントローラーに作成されている Azure Arc 対応 PostgreSQL Hyperscale サーバー グループの一覧を表示する

この記事では、Arc データ コントローラーに作成されているサーバー グループの一覧を取得する方法について説明します。

この一覧を取得するには、Arc データ コントローラーに接続した後、次のいずれかの方法を使用します。

[!INCLUDE [azure-arc-data-preview](../../../includes/azure-arc-data-preview.md)]

## <a name="from-cli-with-azdata"></a>CLI から azdata を使用する
コマンドの一般的な形式は次のとおりです。
```console
azdata arc postgres server list
```

次のような出力が返されます。
```console
Name        State    Workers
----------  -------  ---------
postgres01  Ready    2
postgres02  Ready    2
```
このコマンドで使用できるパラメーターの詳細については、次を実行してください。
```console
azdata arc postgres server list --help
```

## <a name="from-cli-with-kubectl"></a>CLI から kubectl を使用する
次のコマンドのいずれかを実行します。

**Postgres のバージョンに関係なくサーバー グループの一覧を表示するには、以下を実行します。**
```console
kubectl get postgresqls
```
次のような出力が返されます。
```console
NAME                                             STATE   READY-PODS   EXTERNAL-ENDPOINT   AGE
postgresql-12.arcdata.microsoft.com/postgres01   Ready   3/3          10.0.0.4:30499      51s
postgresql-12.arcdata.microsoft.com/postgres02   Ready   3/3          10.0.0.4:31066      6d
```

**Postgres の特定のバージョンのサーバー グループの一覧を表示するには、以下を実行します。**
```console
kubectl get postgresql-12
```

Postgres のバージョン 11 を実行しているサーバー グループの一覧を表示するには、_postgresql-12_ を _postgresql-11_ に置き換えます。

## <a name="next-steps"></a>次のステップ:

* [接続エンド ポイントを取得し、サーバー グループに接続するための接続文字列を作成する方法についての記事を参照する](get-connection-endpoints-and-connection-strings-postgres-hyperscale.md)
* [Azure Arc 対応 PostgreSQL ハイパースケール サーバー グループの構成の表示に関する記事を参照する](show-configuration-postgresql-hyperscale-server-group.md)
