---
title: Azure Arc 対応 PostgreSQL Hyperscale でサポートされている Postgres のバージョン
description: Azure Arc 対応 PostgreSQL Hyperscale でサポートされている Postgres のバージョン
services: azure-arc
ms.service: azure-arc
ms.subservice: azure-arc-data
author: TheJY
ms.author: jeanyd
ms.reviewer: mikeray
ms.date: 09/22/2020
ms.topic: how-to
ms.openlocfilehash: 0f3f9e7f4566800bdea6871cae1c5bafb3b37b95
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "90931326"
---
# <a name="supported-versions-of-postgres-with-azure-arc-enabled-postgresql-hyperscale"></a>Azure Arc 対応 PostgreSQL Hyperscale でサポートされている Postgres のバージョン

この記事では、Azure Arc 対応 PostgreSQL Hyperscale で使用できる Postgres のバージョンについて説明します。
サポートされているバージョンのリストは、時間の経過と共に進化します。 現在、サポートされているメジャー バージョンは次のとおりです。
- Postgres 12 (既定)
- Postgres 11

[!INCLUDE [azure-arc-data-preview](../../../includes/azure-arc-data-preview.md)]

## <a name="how-to-chose-between-versions"></a>バージョンを選択する方法
アプリケーションが設計されているバージョンと、各バージョンの機能を確認することをお勧めします。 詳細については、公式の Postgres サイトの各バージョンに関するページを参照してください。
- [Postgres 12 (既定)](https://www.postgresql.org/docs/12/index.html)
- [Postgres 11](https://www.postgresql.org/docs/11/index.html)

## <a name="how-to-create-a-particular-version-in-azure-arc-enabled-postgresql-hyperscale"></a>Azure Arc 対応 PostgreSQL Hyperscale で特定のバージョンを作成する方法
作成時に、 _--engine-version_ パラメーターを渡すことによって、作成するバージョンを指定できます。 バージョン情報が指定されていない場合、既定では Postgres バージョン 12 のサーバー グループが作成されます。

## <a name="how-do-be-notified-when-other-versions-are-available"></a>他のバージョンが利用可能になったときに通知を受け取る方法
この記事に戻ってきてください。 必要に応じて更新されます。 また、Kubernetes クラスターの Arc データ コントローラーにあるカスタム リソース定義 (CRD) の種類を一覧表示することもできます。
次のコマンドを実行します。
```console
kubectl get crds
```

次のような出力が返されます。
```console
NAME                                            CREATED AT
datacontrollers.arcdata.microsoft.com           2020-08-31T20:15:16Z
postgresql-11s.arcdata.microsoft.com            2020-08-31T20:15:16Z
postgresql-12s.arcdata.microsoft.com            2020-08-31T20:15:16Z
sqlmanagedinstances.sql.arcdata.microsoft.com   2020-08-31T20:15:16Z
```

この例では、この出力は Postgres に関連する 2 種類の CRD があることを示しています。
- postgresql-12s.arcdata.microsoft.com は Postgres 12 の CRD です
- postgresql-11s.arcdata.microsoft.com は Postgres 11 の CRD です

これらはサーバー グループではなく CRD です。 CRD の存在は、そのバージョンのサーバー グループが作成されているかどうかを示すものではありません。
CRD は、どのような種類のリソースを作成できるかを示すものです。

## <a name="next-steps"></a>次のステップ:
- [Azure Arc 対応 PostgreSQL Hyperscale の作成について確認する](create-postgresql-hyperscale-server-group.md)
- [Arc データ コントローラーに作成された Azure Arc 対応 PostgreSQL Hyperscale サーバー グループの一覧を取得する方法について確認する](list-server-groups-postgres-hyperscale.md)
