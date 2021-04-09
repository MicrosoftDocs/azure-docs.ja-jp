---
title: Azure への移行について Azure Migrate と Site Recovery を比較する
description: Site Recovery ではなく、Azure Migrate を移行に使用する利点の概要を示します。
services: site-recovery
author: rayne-wiselman
manager: carmonm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 08/06/2020
ms.author: raynew
ms.openlocfilehash: 358efaa1493aa08fb76c9bb83e0e4289950e0969
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "87844322"
---
# <a name="migrating-to-azure"></a>Azure への移行

移行の場合、VM とサーバーを Azure に移行するには、Azure Site Recovery サービスではなく、Azure Migrate サービスを使用することをお勧めします。 Azure Migrate の詳細については、[こちら](../migrate/migrate-services-overview.md)をご覧ください。


## <a name="why-use-azure-migrate"></a>Azure Migrate を使用する理由

Azure Migrate を使った移行には、多くの利点があります。
 
 
- Azure Migrate は、検出、評価、および Azure への移行を行うための中央ハブとなります。
- Azure Migrate を使用すると、Azure Migrate ツール、その他の Azure サービス、サードパーティ製のツールによって相互運用性と将来の拡張性が確保されます。
- Azure Migrate: Server Migration ツールは、サーバーを Azure に移行するための専用ツールです。 これは移行のために最適化されています。 移行とは直接関係ない概念やシナリオを理解する必要はありません。 
- VM のレプリケーションが開始された時点から 180 日間の移行に関して、ツールの使用料金は発生しません。 これにより、移行を完了する時間を確保できます。 料金は、レプリケーションで使用されるストレージとネットワークのリソース、およびテスト移行時に使用されるコンピューティング料金に対してのみ発生します。
- Azure Migrate では、Site Recovery でサポートされているすべての移行シナリオがサポートされます。 さらに、VMware VM の場合、Azure Migrate にはエージェントレスの移行オプションが用意されています。
- 新しい移行機能は、Azure Migrate: Server Migration ツールでのみ優先的に使用できます。 これらの機能は、Site Recovery を対象としていません。

## <a name="when-to-use-site-recovery"></a>Site Recovery を使用する場合

Site Recovery は、次の場合に使用します。

- Azure に対してオンプレミス マシンのディザスター リカバリーを行う場合
- Azure リージョン間で Azure VM のディザスター リカバリーを行う場合

オンプレミスのサーバーを Azure に移行する場合は Azure Migrate を使用することをお勧めしますが、既に Site Recovery を使用して移行を開始している場合は、引き続き Site Recovery を使用して移行を完了できます。  

## <a name="next-steps"></a>次のステップ

> Azure Migrate について[よく寄せられる質問を確認](../migrate/resources-faq.md)する。
