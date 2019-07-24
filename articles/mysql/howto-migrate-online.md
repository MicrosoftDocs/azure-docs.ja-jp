---
title: 最小限のダウンタイムでの Azure Database for MySQL への移行
description: この記事では、Azure Database Migration Service を使用して、MySQL データベースを Azure Database for MySQL に最小限のダウンタイムで移行する方法について説明します。
author: ajlam
ms.author: andrela
ms.service: mysql
ms.topic: conceptual
ms.date: 12/07/2018
ms.openlocfilehash: 49e2662f215d845d416e46246b03e4408fae118b
ms.sourcegitcommit: 71ee622bdba6e24db4d7ce92107b1ef1a4fa2600
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/17/2018
ms.locfileid: "53543463"
---
# <a name="minimal-downtime-migration-to-azure-database-for-mysql"></a>最小限のダウンタイムでの Azure Database for MySQL への移行
[Azure Database Migration Service](https://aka.ms/get-dms) (DMS) に新たに導入された**継続的同期機能**を使用すると、最小限のダウンタイムで MySQL を Azure Database for MySQL に移行できます。 この機能で、アプリケーションによって発生するダウンタイムの長さが短くなります。

## <a name="overview"></a>概要
Azure DMS は、オンプレミスから Azure Database for MySQL への初期読み込みを実行してから、アプリケーションは実行中のままで、新しいトランザクションを Azure に継続的に同期します。 データがターゲットの Azure 側に追いついた後、アプリケーションを短時間停止し (最小限のダウンタイム)、データの最後のバッチがターゲットに追いつくまで (アプリケーションを停止してから、アプリケーションが事実上新しいトラフィックを受け取ることができなくなるまで) 待ってから、Azure を指すように接続文字列を更新します。 完了すると、アプリケーションは Azure 上で動作します。

![Azure Database Migration Service での継続的同期](./media/howto-migrate-online/ContinuousSync.png)

## <a name="next-steps"></a>次の手順
- [MySQL/PostgreSQL アプリを Azure マネージド サービスに簡単に移行する方法](https://medius.studios.ms/Embed/Video/THR2201?sid=THR2201)のビデオをご覧ください。MySQL アプリを Azure Database for MySQL に移行する方法を示すデモが含まれています。
- 「[DMS を使用して MySQL をオンラインの Azure Database for MySQL に移行する](https://docs.microsoft.com/azure/dms/tutorial-mysql-azure-mysql-online)」のチュートリアルを参照してください。
