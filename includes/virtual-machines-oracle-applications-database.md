---
author: dlepow
ms.service: virtual-machines-linux
ms.topic: include
ms.date: 06/01/2020
ms.author: danlep
ms.openlocfilehash: b9e275cbdaf264df3d0a40f032693fd93553e520
ms.sourcegitcommit: 69156ae3c1e22cc570dda7f7234145c8226cc162
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/03/2020
ms.locfileid: "84317425"
---
### <a name="database-tier"></a>データベース層

データベース層には、アプリケーションのデータベース インスタンスが存在します。 データベースには、Oracle DB、Oracle RAC、Oracle Exadata Database システムのいずれかを選択できます。 

Oracle DB を選択した場合、Azure Marketplace で提供されている Oracle DB イメージを使用してデータベース インスタンスを Azure にデプロイできます。 または、Azure と OCI 間の相互接続を使用し、OCI 上に PaaS モデルで Oracle DB をデプロイすることもできます。

Oracle RAC の場合、PaaS モデルで OCI を使用できます。 2 ノードの RAC システムを使用することをお勧めします。 IaaS モデルでは Azure CloudSimple に Oracle RAC をデプロイできますが、Oracle でサポートされている構成ではありません。 「[許可を受けたクラウド環境で適格な Oracle プログラム](http://www.oracle.com/us/corporate/pricing/authorized-cloud-environments-3493562.pdf)」を参照してください。

最後に、Exadata システムの場合は、OCI 相互接続を使用して、Exadata システムを OCI にデプロイします。 前出のアーキテクチャ図は、2 つのサブネットにまたがって OCI にデプロイされた Exadata システムを示しています。

運用のシナリオでは、2 つの可用性ゾーン (Azure にデプロイする場合) または 2 つの可用性ドメイン (OCI の場合) にまたがってデータベースの複数インスタンスをデプロイします。 プライマリ データベースとスタンバイ データベースの同期には、Oracle Active Data Guard を使用します。

データベース層は、中間層からの要求のみを受け取ります。 中間層からの要求と、管理を目的とした要塞サーバーからの要求のみを、それぞれポート 1521 とポート 22 で許可するネットワーク セキュリティ グループ (OCI にデータベースをデプロイする場合はセキュリティ リスト) を設定するようお勧めします。

OCI にデプロイされたデータベースについては、FastConnect 回線に接続された動的ルーティング ゲートウェイ (DRG) を使用して、別個の仮想クラウド ネットワークを設定する必要があります。