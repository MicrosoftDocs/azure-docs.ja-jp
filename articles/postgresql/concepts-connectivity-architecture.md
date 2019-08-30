---
title: Azure Database for PostgreSQL の接続アーキテクチャ
description: Azure Database for PostgreSQL サーバーの接続アーキテクチャについて説明します。
author: kummanish
ms.author: manishku
ms.service: postgresql
ms.topic: conceptual
ms.date: 05/23/2019
ms.openlocfilehash: 92844f0fe3a851802836015a1340983eb4633ed2
ms.sourcegitcommit: d3dced0ff3ba8e78d003060d9dafb56763184d69
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/22/2019
ms.locfileid: "69900546"
---
# <a name="connectivity-architecture-in-azure-database-for-postgresql"></a>Azure Database for PostgreSQL の接続アーキテクチャ
この記事では、Azure Database for PostgreSQL 接続アーキテクチャと、Azure 内外の両方のクライアントからトラフィックがどのように Azure Database for PostgreSQL データベース インスタンスに転送されるかについて説明します。

## <a name="connectivity-architecture"></a>接続のアーキテクチャ
Azure Database for PostgreSQL への接続は、受信接続を Microsoft のクラスター内にあるお客様のサーバーの物理的な場所にルーティングする役割を果たすゲートウェイを介して確立されます。 次の図に、そのトラフィック フローを示します。

![接続アーキテクチャの概要](./media/concepts-connectivity-architecture/connectivity-architecture-overview-proxy.png)

クライアントでは、データベースに接続するときに、ゲートウェイに接続する接続文字列を取得します。 このゲートウェイは、ポート 5432 をリッスンするパブリック IP アドレスを持っています。 データベース クラスター内では、トラフィックは適切な Azure Database for PostgreSQL に転送されます。 そのため、企業ネットワークなどからサーバーに接続するには、クライアント側のファイアウォールを開いて送信トラフィックがゲートウェイに到達できるようにする必要があります。 リージョンごとに Microsoft のゲートウェイで使用されている IP アドレスの完全な一覧を次に示します。

## <a name="azure-database-for-postgresql-gateway-ip-addresses"></a>Azure Database for PostgreSQL ゲートウェイの IP アドレス
次の表は、すべてのデータ リージョンの Azure Database for PostgreSQL ゲートウェイのプライマリ IP とセカンダリ IP を一覧にまとめたものです。 プライマリ IP アドレスはゲートウェイの現行 IP アドレスであり、セカンダリ IP アドレスはプライマリでエラーが発生した場合のフェールオーバー IP アドレスになります。 前述のように、お客様が両方の IP アドレスへの送信を許可する必要があります。 セカンダリ IP アドレスは、接続を受け入れるために Azure Database for PostgreSQL によりアクティブにされるまで、いかなるサービスでもリッスンしません。

| **リージョン名** | **プライマリ IP アドレス** | **セカンダリ IP アドレス** |
|:----------------|:-------------|:------------------------|
| オーストラリア東部 | 13.75.149.87 | 40.79.161.1 |
| オーストラリア東南部 | 191.239.192.109 | 13.73.109.251 |
| ブラジル南部 | 104.41.11.5 | |
| カナダ中部 | 40.85.224.249 | |
| カナダ東部 | 40.86.226.166 | |
| 米国中部 | 23.99.160.139 | 13.67.215.62 |
| 中国東部 1 | 139.219.130.35 | |
| 中国東部 2 | 40.73.82.1 | |
| 中国北部 1 | 139.219.15.17 | |
| 中国北部 2 | 40.73.50.0 | |
| 東アジア | 191.234.2.139 | 52.175.33.150 |
| 米国東部 1 | 191.238.6.43 | 40.121.158.30 |
| 米国東部 2 | 191.239.224.107 | 40.79.84.180 * |
| フランス中部 | 40.79.137.0 | 40.79.129.1 |
| ドイツ中部 | 51.4.144.100 | |
| インド中部 | 104.211.96.159 | |
| インド南部 | 104.211.224.146 | |
| インド西部 | 104.211.160.80 | |
| 東日本 | 191.237.240.43 | 13.78.61.196 |
| 西日本 | 191.238.68.11 | 104.214.148.156 |
| 韓国中部 | 52.231.32.42 | |
| 韓国南部 | 52.231.200.86 |  |
| 米国中北部 | 23.98.55.75 | 23.96.178.199 |
| 北ヨーロッパ | 191.235.193.75 | 40.113.93.91 |
| 米国中南部 | 23.98.162.75 | 13.66.62.124 |
| 東南アジア | 23.100.117.95 | 104.43.15.0 |
| 英国南部 | 51.140.184.11 | |
| 英国西部 | 51.141.8.11| |
| 西ヨーロッパ | 191.237.232.75 | 40.68.37.158 |
| 米国西部 1 | 23.99.34.75 | 104.42.238.205 |
| 米国西部 2 | 13.66.226.202 | |
||||

> [!NOTE]
> *米国東部 2* には、`52.167.104.0` の第 3 IP アドレスもあります。

## <a name="next-steps"></a>次の手順

* [Azure Portal を使用した Azure Database for PostgreSQL ファイアウォール規則の作成と管理](./howto-manage-firewall-using-portal.md)
* [Azure CLI を使用した Azure Database for PostgreSQL ファイアウォール規則の作成と管理](./howto-manage-firewall-using-cli.md)
