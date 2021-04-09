---
title: Azure Migrate を使用して AWS VM を Azure に移行する
description: この記事では、AWS インスタンスを Azure に移行するためのオプションについて説明したうえで、Azure Migrate をお勧めします。
services: site-recovery
author: rayne-wiselman
manager: carmonm
ms.service: site-recovery
ms.topic: tutorial
ms.date: 07/27/2019
ms.author: raynew
ms.custom: MVC
ms.openlocfilehash: 83d8106b9f206da9095c37b6179bdda8001b439c
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "96009060"
---
# <a name="migrate-amazon-web-services-aws-vms-to-azure"></a>アマゾン ウェブ サービス (AWS) VM を Azure に移行する

この記事では、アマゾン ウェブ サービス (AWS) インスタンスを Azure に移行するオプションについて説明します。

## <a name="migrate-with-azure-migrate"></a>Azure Migrate を使用した移行

[Azure Migrate](../migrate/migrate-services-overview.md) サービスを使用して AWS EC2 インスタンスを Azure に移行することをお勧めします。 Azure Migrate は、サーバーの移行専用に設計されています。 Azure Migrate は、オンプレミスのマシンの検出、評価、および Azure への移行を行うための中心的なハブを提供します。

Azure Migrate を使用して AWS インスタンスを移行する方法については、[こちら](../migrate/tutorial-migrate-aws-virtual-machines.md)を参照してください。 


## <a name="migrate-with-site-recovery"></a>Site Recovery を使用した移行

Site Recovery は、移行でなく、ディザスター リカバリーのみに使用する必要があります。

Azure Site Recovery を既に使用していて、AWS の移行にもそれを引き続き使用したい場合は、[物理マシンのディザスター リカバリー](physical-azure-disaster-recovery.md)を設定したのと同じ手順を実行します。


> [!NOTE]
> ディザスター リカバリーのためにフェールオーバーを実行する場合、最後の手順としてフェールオーバーをコミットします。 AWS インスタンスを移行する場合は、 **[コミット]** オプションは使用しません。 代わりに、 **[移行の完了]** オプションを選択します。 

## <a name="next-steps"></a>次のステップ

> [!div class="nextstepaction"]
> Azure Migrate について[よく寄せられる質問を確認](../migrate/resources-faq.md)する。
