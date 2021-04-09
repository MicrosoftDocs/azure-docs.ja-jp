---
title: Azure サービスの中断
description: Azure サービスの中断が Azure 仮想マシンに影響を与える場合の対処方法について説明します。
author: cynthn
ms.service: virtual-machines
ms.topic: conceptual
ms.date: 05/31/2017
ms.author: cynthn
ms.reviewer: ''
ms.openlocfilehash: 5c6036a65fa449986f5c085202fefa752e3a414a
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "98701035"
---
# <a name="what-if-an-azure-service-disruption-impacts-azure-vms"></a>Azure サービスの中断が Azure VM に影響を与える場合の対処方法

Microsoft では、必要なときにサービスがいつでも使用できるように取り組んでいますが、 やむを得ない事情により、計画されていないサービス中断が発生することがあります。

Microsoft は、稼働時間と接続に関するコミットメントとして、サービスのサービス レベル アグリーメント (SLA) を提供しています。 個々の Azure サービスの SLA については、 [Azure サービス レベル アグリーメント](https://azure.microsoft.com/support/legal/sla/)に関するページを参照してください。

Azure には、可用性の高いアプリケーションをサポートするさまざまなプラットフォーム機能が既に組み込まれています。 これらのサービスの詳細については、 [Azure アプリケーションのディザスター リカバリーと高可用性](/azure/architecture/framework/resiliency/backup-and-recovery)に関する記事をご覧ください。

この記事では、大きな自然災害や広範囲にわたるサービス中断により、リージョン全体で障害が発生した場合の真のディザスター リカバリーのシナリオについて説明します。 こうした状況はほとんど発生しませんが、リージョン全体で中断が発生する可能性に対して準備する必要があります。 リージョン全体でサービス中断が発生した場合、データのローカル冗長コピーは、一時的に使用できなくなります。 geo レプリケーションを有効にしてある場合は、Azure Storage の BLOB とテーブルのコピーがさらに 3 つ、別のリージョンに格納されます。 地域的な停電や災害が発生し、プライマリ リージョンを復旧できない場合は、すべての DNS エントリが、geo レプリケートされたリージョンに再マッピングされます。

こうした状況はほとんど発生しませんが、Azure 仮想マシンのアプリケーションがデプロイされているリージョン全体でサービス中断が発生した場合は、Azure 仮想マシンに関する次のガイダンスに従って対応してください。

## <a name="option-1-initiate-a-failover-by-using-azure-site-recovery"></a>オプション 1: Azure Site Recovery を使用してフェールオーバーを開始する
VM に Azure Site Recovery を構成して、1 回のクリックによってわずか数分でアプリケーションを復旧できるようにします。 任意の Azure リージョンにレプリケートでき、ペア リージョンに限定されません。 [仮想マシンのレプリケート](../site-recovery/azure-to-azure-quickstart.md)から始めます。 [復旧計画を作成](../site-recovery/site-recovery-create-recovery-plans.md)して、アプリケーションのフェールオーバー プロセス全体を自動化できます。 運用アプリケーションや実行中のレプリケーションに影響を与えることなく、事前に[フェールオーバーをテスト](../site-recovery/site-recovery-test-failover-to-azure.md)できます。 プライマリ リージョンでの中断が発生した場合は、[フェールオーバーを開始](../site-recovery/site-recovery-failover.md)して、アプリケーションをターゲット リージョンに移すだけです。


## <a name="option-2-wait-for-recovery"></a>オプション 2: 復旧を待つ
この場合、ユーザーによる操作は必要ありません。 サービスを利用できるようにするために鋭意取り組んでいることをご理解ください。 現在のサービスの状態は、 [Azure サービス正常性ダッシュボード](https://azure.microsoft.com/status/)で確認できます。

中断前に Azure Site Recovery、読み取りアクセス geo 冗長ストレージ、または geo 冗長ストレージを設定しなかった場合は、このオプションの使用をお勧めします。 VM 仮想ハード ドライブ (VHD) が格納されているストレージ アカウントに対して geo 冗長ストレージまたは読み取りアクセス geo 冗長ストレージを設定した場合は、基本イメージ VHD の回復を当てにして、そこから新しい VM のプロビジョニングを試みることができます。 データの同期が保証されないので、このオプションはお勧めできません。 そのため、このオプションの動作は保証されません。


> [!NOTE]
> ユーザーはこのプロセスを制御できないこと、およびリージョン全体のサービス中断の場合にのみ行われることに注意してください。 そのため、最高レベルの可用性を実現するには、アプリケーション固有の他のバックアップ戦略にも依存する必要があります。 詳細については、 [ディザスター リカバリーのためのデータ戦略](/azure/architecture/reliability/disaster-recovery#disaster-recovery-plan)に関するセクションをご覧ください。
>
>

## <a name="next-steps"></a>次のステップ

- Azure Site Recovery を使用して [Azure 仮想マシンで実行されているアプリケーションの保護](../site-recovery/azure-to-azure-quickstart.md)を開始する

- ディザスター リカバリーと高可用性戦略を実装する方法の詳細については、[Azure アプリケーションのディザスター リカバリーと高可用性](/azure/architecture/framework/resiliency/backup-and-recovery)に関するページを参照してください。

- クラウド プラットフォームの機能の詳細な技術について理解を深めるには、「 [Azure の回復性技術ガイダンス](../data-lake-store/data-lake-store-disaster-recovery-guidance.md)」を参照してください。


- 不明な点がある場合、または Microsoft による代理操作をご希望の場合は、 [カスタマー サポート](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade)までご連絡ください。