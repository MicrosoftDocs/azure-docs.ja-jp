---
title: 最小限のダウンタイムでの Azure Database for PostgreSQL - Single Server への移行
description: この記事では、Azure Database Migration Service を使用して、PostgreSQL データベースを Azure Database for PostgreSQL - Single Server に最小限のダウンタイムで移行する方法について説明します。
author: sr-msft
ms.author: srranga
ms.service: postgresql
ms.topic: how-to
ms.date: 5/6/2019
ms.openlocfilehash: 27da5f1b731b2cdb0604f91f7f9e78b19ee2908b
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "92489797"
---
# <a name="minimal-downtime-migration-to-azure-database-for-postgresql---single-server"></a>最小限のダウンタイムでの Azure Database for PostgreSQL - Single Server への移行
[!INCLUDE[applies-to-postgres-single-flexible-server](includes/applies-to-postgres-single-flexible-server-hyperscale.md)]

[Azure Database Migration Service](https://aka.ms/get-dms) (DMS) に新たに導入された **継続的同期機能** を使用すると、最小限のダウンタイムで PostgreSQL を Azure Database for PostgreSQL に移行できます。 この機能で、アプリケーションによって発生するダウンタイムの長さが短くなります。

## <a name="overview"></a>概要
Azure DMS では、オンプレミスから Azure Database for PostgreSQL への初期読み込みを実行してから、アプリケーションは実行中のままで、新しいトランザクションを Azure に継続的に同期します。 データがターゲットの Azure 側に追いついた後、アプリケーションを短時間停止し (最小限のダウンタイム)、データの最後のバッチがターゲットに追いつくまで (アプリケーションを停止してから、アプリケーションが事実上新しいトラフィックを受け取ることができなくなるまで) 待ってから、Azure を指すように接続文字列を更新します。 完了すると、アプリケーションは Azure 上で動作します。

:::image type="content" source="./media/howto-migrate-online/ContinuousSync.png" alt-text="Azure Database Migration Service での継続的同期":::

## <a name="next-steps"></a>次のステップ
- [Microsoft Azure でのアプリケーションの最新化](https://medius.studios.ms/Embed/Video/BRK2102?sid=BRK2102)のビデオをご覧ください。PostgreSQL アプリを Azure Database for PostgreSQL に移行する方法のデモが含まれています。
- 「[DMS を使用して PostgreSQL をオンラインで Azure Database for PostgreSQL に移行する](../dms/tutorial-postgresql-azure-postgresql-online.md)」のチュートリアルを参照してください。