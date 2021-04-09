---
title: ファイアウォール規則 - Hyperscale (Citus) - Azure Database for PostgreSQL
description: この記事では、Azure Database for PostgreSQL - Hyperscale (Citus) のファイアウォール規則について説明します。
author: jonels-msft
ms.author: jonels
ms.service: postgresql
ms.subservice: hyperscale-citus
ms.topic: conceptual
ms.date: 9/12/2019
ms.openlocfilehash: 559c5eca6fa8a6eceb37ade003d4f1983c0a1a1b
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "90902097"
---
# <a name="firewall-rules-in-azure-database-for-postgresql---hyperscale-citus"></a>Azure Database for PostgreSQL - Hyperscale (Citus) のファイアウォール規則
Azure Database for PostgreSQL サーバーのファイアウォールは、どのコンピューターに権限を持たせるかを指定するまで、Hyperscale (Citus) コーディネーター ノードへのすべてのアクセスを遮断します。 ファイアウォールは、各要求の送信元 IP アドレスに基づいてサーバーへのアクセス権を付与します。
ファイアウォールを構成するには、受け入れ可能な IP アドレスの範囲を指定するファイアウォール規則を作成します。 ファイアウォール規則はサーバー レベルで作成できます。

**ファイアウォール規則**: これらの規則により、クライアントは、Hyperscale (Citus) コーディネーター ノード、つまり、同じ論理サーバー内のすべてのデータベースにアクセスできるようになります。 サーバー レベルのファイアウォール規則を構成するには、Azure portal を使用します。 サーバー レベルのファイアウォール規則を作成するには、サブスクリプション所有者またはサブスクリプション共同作成者である必要があります。

## <a name="firewall-overview"></a>ファイアウォールの概要
既定では、コーディネーター ノードへのすべてのデータベース アクセスが、ファイアウォールによってブロックされます。 他のコンピューターからサーバーの使用を開始するには、サーバー レベルのファイアウォール規則を 1 つ以上指定して、サーバーへのアクセスを有効にする必要があります。 ファイアウォール規則を使用して、インターネットからのアクセスを許可する IP アドレス範囲を指定します。 Azure Portal Web サイト自体へのアクセスは、ファイアウォール規則の影響は受けません。
インターネットおよび Azure からの接続の試行は、次の図に示されるように、PostgreSQL データベースに到達する前にファイアウォールを通過する必要があります。

:::image type="content" source="media/concepts-hyperscale-firewall-rules/1-firewall-concept.png" alt-text="ファイアウォールのしくみを示すサンプル フロー":::

## <a name="connecting-from-the-internet-and-from-azure"></a>インターネットおよび Azure からの接続

Hyperscale (Citus) サーバー グループ ファイアウォールは、グループのコーディネーター ノードに接続できるユーザーを制御します。 ファイアウォールは、構成可能な規則の一覧を調べてアクセスを決定します。 各規則は、許可される IP アドレスまたはアドレスの範囲です。

ファイアウォールによって接続がブロックされると、アプリケーション エラーが発生する可能性があります。 たとえば、PostgreSQL JDBC ドライバーを使用すると、次のようなエラーが発生します。

> java.util.concurrent.ExecutionException: java.lang.RuntimeException: org.postgresql.util.PSQLException: FATAL: no pg\_hba.conf entry for host "123.45.67.890", user "citus", database "citus", SSL

ルールの定義方法については、[ファイアウォール規則の作成および管理](howto-hyperscale-manage-firewall-using-portal.md)に関する記事をご覧ください。

## <a name="troubleshooting-the-database-server-firewall"></a>データベース サーバー ファイアウォールのトラブルシューティング
Microsoft Azure Database for PostgreSQL - Hyperscale (Citus) サービスに期待どおりにアクセスできない場合は、次の点を検討してください。

* **許可一覧に変更が反映されない:** Hyperscale (Citus) ファイアウォールの構成に対する変更が反映されるまで最大 5 分間の遅延が発生する場合があります。

* **ユーザーが認可されない、または正しくないパスワードが使用された**: サーバーでは、ユーザーにアクセス許可がないか、使用したパスワードが正しくない場合、サーバーへの接続は拒否されます。 ファイアウォール設定の作成は、サーバーへの接続を試行する機会をクライアントに提供するだけです。そのため、各クライアントは、必要なセキュリティ資格情報を提供する必要があります。

たとえば、JDBC クライアントを使用すると、次のエラーが表示されることがあります。
> java.util.concurrent.ExecutionException: java.lang.RuntimeException: org.postgresql.util.PSQLException:FATAL: password authentication failed for user "yourusername"

* **動的 IP アドレス:** 動的 IP アドレス指定によるインターネット接続を使用しており、ファイアウォールの通過に問題が発生している場合は、次の解決策のいずれかをお試しください。

* Hyperscale (Citus) コーディネーター ノードにアクセスするクライアント コンピューターに割り当てられている IP アドレス範囲について、インターネット サービス プロバイダー (ISP) に問い合わせ、ファイアウォール規則として その IP アドレス範囲を追加してください。

* 代わりに、クライアント コンピューター用に静的 IP アドレスを取得し、ファイアウォール規則としてその静的 IP アドレス範囲を追加してください。

## <a name="next-steps"></a>次のステップ
サーバー レベルおよびデータベース レベルのファイアウォール規則の作成については、次の記事をご覧ください。
* [Azure Portal を使用した Azure Database for PostgreSQL ファイアウォール規則の作成と管理](howto-hyperscale-manage-firewall-using-portal.md)
