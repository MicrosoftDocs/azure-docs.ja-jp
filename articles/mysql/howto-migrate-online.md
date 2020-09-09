---
title: 最小限のダウンタイムでの移行 - Azure Database for MySQL
description: この記事では、Azure Database Migration Service を使用して、MySQL データベースを Azure Database for MySQL に最小限のダウンタイムで移行する方法について説明します。
author: ajlam
ms.author: andrela
ms.service: mysql
ms.topic: how-to
ms.date: 3/18/2020
ms.openlocfilehash: 587e50393102d1d7791f5ddac904d525f1af36a3
ms.sourcegitcommit: d7008edadc9993df960817ad4c5521efa69ffa9f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/08/2020
ms.locfileid: "86118258"
---
# <a name="minimal-downtime-migration-to-azure-database-for-mysql"></a>最小限のダウンタイムでの Azure Database for MySQL への移行
[Azure Database Migration Service](https://aka.ms/get-dms) (DMS) に新たに導入された**継続的同期機能**を使用すると、最小限のダウンタイムで MySQL を Azure Database for MySQL に移行できます。 この機能で、アプリケーションによって発生するダウンタイムの長さが短くなります。

## <a name="overview"></a>概要
Azure DMS は、オンプレミスから Azure Database for MySQL への初期読み込みを実行してから、アプリケーションは実行中のままで、新しいトランザクションを Azure に継続的に同期します。 データがターゲットの Azure 側に追いついた後、アプリケーションを短時間停止し (最小限のダウンタイム)、データの最後のバッチがターゲットに追いつくまで (アプリケーションを停止してから、アプリケーションが事実上新しいトラフィックを受け取ることができなくなるまで) 待ってから、Azure を指すように接続文字列を更新します。 完了すると、アプリケーションは Azure 上で動作します。

![Azure Database Migration Service での継続的同期](./media/howto-migrate-online/ContinuousSync.png)

## <a name="next-steps"></a>次のステップ
- [MySQL/PostgreSQL アプリを Azure マネージド サービスに簡単に移行する方法](https://medius.studios.ms/Embed/Video/THR2201?sid=THR2201)のビデオをご覧ください。MySQL アプリを Azure Database for MySQL に移行する方法を示すデモが含まれています。
- 「[DMS を使用して MySQL をオンラインの Azure Database for MySQL に移行する](https://docs.microsoft.com/azure/dms/tutorial-mysql-azure-mysql-online)」のチュートリアルを参照してください。
