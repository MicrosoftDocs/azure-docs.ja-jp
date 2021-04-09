---
title: Azure Cloud Services (クラシック) に影響を与える Azure サービスの中断に対処する
description: Azure Cloud Services に影響を与える Azure サービスの中断が発生した場合の対処方法について説明します。
ms.topic: article
ms.service: cloud-services
ms.date: 10/14/2020
ms.author: tagore
author: tanmaygore
ms.reviewer: mimckitt
ms.custom: ''
ms.openlocfilehash: cdd6c9da5a1895d4aadd73133734cd4c8204ecf1
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "98742167"
---
# <a name="what-to-do-in-the-event-of-an-azure-service-disruption-that-impacts-azure-cloud-services-classic"></a>Azure Cloud Services (クラシック) に影響を与える Azure サービスの中断が発生した場合の対処方法

> [!IMPORTANT]
> [Azure Cloud Services (延長サポート)](../cloud-services-extended-support/overview.md) は、Azure Cloud Services 製品向けの新しい Azure Resource Manager ベースのデプロイ モデルです。 この変更により、Azure Service Manager ベースのデプロイ モデルで実行されている Azure Cloud Services は Cloud Services (クラシック) という名前に変更されました。そして、すべての新しいデプロイでは [Cloud Services (延長サポート)](../cloud-services-extended-support/overview.md) を使用する必要があります。

Microsoft では、必要なときにサービスがいつでも使用できるように取り組んでいますが、 やむを得ない事情により、計画されていないサービス中断が発生することがあります。

Microsoft は、稼働時間と接続に関するコミットメントとして、サービスのサービス レベル アグリーメント (SLA) を提供しています。 個々の Azure サービスの SLA については、 [Azure サービス レベル アグリーメント](https://azure.microsoft.com/support/legal/sla/)に関するページを参照してください。

Azure には、可用性の高いアプリケーションをサポートするさまざまなプラットフォーム機能が既に組み込まれています。 これらのサービスの詳細については、 [Azure アプリケーションのディザスター リカバリーと高可用性](/azure/architecture/framework/resiliency/backup-and-recovery)に関する記事をご覧ください。

この記事では、大きな自然災害や広範囲にわたるサービス中断により、リージョン全体で障害が発生した場合の真のディザスター リカバリーのシナリオについて説明します。 こうした状況はほとんど発生しませんが、リージョン全体で中断が発生する可能性に対して準備する必要があります。 リージョン全体でサービス中断が発生した場合、データのローカル冗長コピーは、一時的に使用できなくなります。 geo レプリケーションを有効にしてある場合は、Azure Storage の BLOB とテーブルのコピーがさらに 3 つ、別のリージョンに格納されます。 地域的な停電や災害が発生し、プライマリ リージョンを復旧できない場合は、すべての DNS エントリが、geo レプリケートされたリージョンに再マッピングされます。

> [!NOTE]
> ユーザーはこのプロセスを制御できないこと、およびデータセンター全体のサービス中断の場合にのみ行われることに注意してください。 そのため、最高レベルの可用性を実現するには、アプリケーション固有の他のバックアップ戦略にも依存する必要があります。 詳細については、「[Microsoft Azure 上に構築されたアプリケーションの障害復旧と高可用性](/azure/architecture/framework/resiliency/backup-and-recovery)」を参照してください。 独自のフェールオーバーを使用できるようにする場合は、[読み取りアクセス geo 冗長ストレージ (RA-GRS)](../storage/common/storage-redundancy.md) の使用を検討してください。この場合は、他のリージョンに、データの読み取り専用のコピーが作成されます。
>
>


## <a name="option-1-use-a-backup-deployment-through-azure-traffic-manager"></a>オプション 1: Azure Traffic Manager を介してバックアップ デプロイを使用する
最も堅牢なディザスター リカバリー ソリューションでは、アプリケーションの複数のデプロイを異なるリージョンで維持し、[Azure Traffic Manager](../traffic-manager/traffic-manager-overview.md) を使用して、それらの間のトラフィックを転送します。 Azure Traffic Manager は複数の[ルーティング メソッド](../traffic-manager/traffic-manager-routing-methods.md)を備えているため、プライマリ/バックアップ モデルを使用してデプロイを管理するか、それらの間のトラフィックを分割するかどうかを選択できます。

![Balancing Azure Cloud Services across regions with Azure Traffic Manager](./media/cloud-services-disaster-recovery-guidance/using-azure-traffic-manager.png)

リージョンの損失に迅速に対応するには、Traffic Manager の[エンドポイント監視](../traffic-manager/traffic-manager-monitoring.md)を構成することが重要です。

## <a name="option-2-deploy-your-application-to-a-new-region"></a>オプション 2: 新しいリージョンにアプリケーションをデプロイする
前のオプションの説明に従って、複数のアクティブなデプロイを維持するには、追加の継続的なコストが必要です。 復旧時間目標 (RTO) に十分な柔軟性があり、元のコードまたはコンパイル済みの Cloud Services パッケージがある場合は、アプリケーションの新しいインスタンスを別のリージョンに作成して、新しいデプロイを指すように DNS レコードを更新することができます。

クラウド サービス アプリケーションを作成してデプロイする方法の詳細については、「[クラウド サービスを作成してデプロイする方法](cloud-services-how-to-create-deploy-portal.md)」を参照してください。

アプリケーションのデータ ソースによっては、アプリケーションのデータ ソースの復旧手順を確認することが必要になる場合があります。

* Azure Storage データ ソースの場合は、[Azure Storage の冗長性](../storage/common/storage-redundancy.md)に関するセクションを参照して、アプリケーション用に選択した冗長モデルに応じて使用できるオプションについて確認してください。
* SQL Database ソースの場合は、「[概要: SQL Database を使用したクラウド ビジネス継続性とデータベース ディザスター リカバリー](../azure-sql/database/business-continuity-high-availability-disaster-recover-hadr-overview.md)」を参照して、アプリケーション用に選択したレプリケーション モデルに応じて使用できるオプションについて確認してください。


## <a name="option-3-wait-for-recovery"></a>オプション 3: 復旧を待つ
この場合、ユーザーによる操作は不要ですが、リージョンが復元されるまでサービスは利用できません。 現在のサービスの状態は、[Azure サービスの正常性ダッシュボード](https://azure.microsoft.com/status/)で確認できます。

## <a name="next-steps"></a>次のステップ
ディザスター リカバリーと高可用性戦略を実装する方法の詳細については、[Azure アプリケーションのディザスター リカバリーと高可用性](/azure/architecture/framework/resiliency/backup-and-recovery)に関するページを参照してください。

クラウド プラットフォームの機能の詳細な技術について理解を深めるには、「 [Azure の回復性技術ガイダンス](/azure/architecture/checklist/resiliency-per-service)」を参照してください。