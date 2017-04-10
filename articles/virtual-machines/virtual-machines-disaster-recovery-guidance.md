---
title: "Azure VM の障害復旧のシナリオ | Microsoft Docs"
description: "Azure サービスの中断が Azure 仮想マシンに影響を与える場合の対処方法について説明します。"
services: virtual-machines
documentationcenter: 
author: kmouss
manager: timlt
editor: 
ms.assetid: 65272148-ff06-4bce-91f1-851d706d4d40
ms.service: virtual-machines
ms.workload: virtual-machines
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/16/2016
ms.author: kmouss;aglick
ms.custom: H1Hack27Feb2017
translationtype: Human Translation
ms.sourcegitcommit: 0d8472cb3b0d891d2b184621d62830d1ccd5e2e7
ms.openlocfilehash: b1fb88277f6da86c023cd63af0d7c2c5681f8be0
ms.lasthandoff: 03/21/2017


---
# <a name="what-to-do-in-the-event-that-an-azure-service-disruption-impacts-azure-vms"></a>Azure サービスの中断が Azure VM に影響を与える場合の対処方法
Microsoft では、必要なときにサービスがいつでも使用できるように取り組んでいますが、 やむを得ない事情により、計画されていないサービス中断が発生することがあります。

Microsoft は、稼働時間と接続に関するコミットメントとして、サービスのサービス レベル アグリーメント (SLA) を提供しています。 個々の Azure サービスの SLA については、 [Azure サービス レベル アグリーメント](https://azure.microsoft.com/support/legal/sla/)に関するページを参照してください。

Azure には、可用性の高いアプリケーションをサポートするさまざまなプラットフォーム機能が既に組み込まれています。 これらのサービスの詳細については、 [Azure アプリケーションの障害復旧と高可用性](../resiliency/resiliency-disaster-recovery-high-availability-azure-applications.md)に関する記事をご覧ください。

この記事では、大きな自然災害や広範囲にわたるサービス中断により、リージョン全体で障害が発生した場合の真の障害復旧シナリオについて説明します。 こうした状況はほとんど発生しませんが、リージョン全体で中断が発生する可能性に対して準備する必要があります。 リージョン全体でサービス中断が発生した場合、データのローカル冗長コピーは、一時的に使用できなくなります。 geo レプリケーションを有効にしてある場合は、Azure Storage の BLOB とテーブルのコピーがさらに&3; つ、別のリージョンに格納されます。 地域的な停電や災害が発生し、プライマリ リージョンを復旧できない場合は、すべての DNS エントリが、geo レプリケートされたリージョンに再マッピングされます。

> [!NOTE]
> ユーザーはこのプロセスを制御できないこと、およびリージョン全体のサービス中断の場合にのみ行われることに注意してください。 そのため、最高レベルの可用性を実現するには、アプリケーション固有の他のバックアップ戦略にも依存する必要があります。 詳細については、 [障害復旧のためのデータ戦略](https://docs.microsoft.com/azure/architecture/resiliency/disaster-recovery-azure-applications#data-strategies-for-disaster-recovery)に関するセクションをご覧ください。
>
>

こうした状況はほとんど発生しませんが、Azure 仮想マシンのアプリケーションがデプロイされているリージョン全体でサービス中断が発生した場合は、Azure 仮想マシンに関する次のガイダンスに従って対応してください。

## <a name="option-1-wait-for-recovery"></a>オプション 1: 復旧を待つ
この場合、ユーザーによる操作は必要ありません。 サービスを利用できるようにするために鋭意取り組んでいることをご理解ください。 現在のサービスの状態は、 [Azure サービス正常性ダッシュボード](https://azure.microsoft.com/status/)で確認できます。

> [!NOTE]
> 中断前に Azure Site Recovery、仮想マシンのバックアップ、読み取りアクセス geo 冗長ストレージ、または geo 冗長ストレージを設定しなかった場合は、このオプションを使用することをお勧めします。 VM 仮想ハード ドライブ (VHD) が格納されているストレージ アカウントに対して geo 冗長ストレージまたは読み取りアクセス geo 冗長ストレージを設定した場合は、基本イメージ VHD の回復を当てにして、そこから新しい VM のプロビジョニングを試みることができます。 このオプションはお勧めできません。Azure VM バックアップまたは Azure Site Recovery が使用されていない限り、データの同期が保証されていないためです。 そのため、このオプションの動作は保証されません。
>
>

仮想マシンにすぐにアクセスする必要があるお客様は、次の&2; つのオプションを使用できます。  

> [!NOTE]
> 次のオプションは両方とも、一部のデータが失われる可能性があることに注意してください。     
>
>

## <a name="option-2-restore-a-vm-from-a-backup"></a>オプション 2: バックアップから VM を復元する
VM バックアップが構成されているお客様の場合は、そのバックアップと回復ポイントから VM を復元できます。

Azure バックアップから新しい VM を復元するには、「 [Azure での仮想マシンの復元](../backup/backup-azure-restore-vms.md)」をご覧ください。

Azure 仮想マシンのバックアップ インフラストラクチャの計画については、「 [Azure における VM バックアップ インフラストラクチャの計画を立てる](../backup/backup-azure-vms-introduction.md)」をご覧ください。

## <a name="option-3-initiate-a-failover-by-using-azure-site-recovery"></a>オプション 3: Azure Site Recovery を使用してフェールオーバーを開始する
影響を受ける Azure 仮想マシンで動作するように Azure Site Recovery を構成した場合は、そのレプリカから VM を復元できます。 このレプリカは、Azure またはオンプレミスのいずれかに配置できます。 この場合は、既存のレプリカから新しい VM を作成できます。 Azure Site Recovery レプリカから VM を復元するには、「 [Azure Site Recovery を使用した Azure リージョン間での Azure IaaS 仮想マシンの移行](../site-recovery/site-recovery-migrate-azure-to-azure.md)」をご覧ください。

> [!NOTE]
> Azure の仮想マシンのオペレーティング システムとデータ ディスクはセカンダリ VHD にレプリケートされますが、レプリケートの対象が geo 冗長ストレージまたは読み取りアクセス geo 冗長ストレージ アカウント内にある場合は、各 VHD のレプリケートは個別に実行されます。 このレベルのレプリケーションでは、レプリケートされた VHD 間の一貫性が保証されません。 こうしたデータ ディスクを使用しているアプリケーション/データベースが相互に依存している場合は、すべての VHD が&1; つのスナップショットとしてレプリケートされるとは限りません。 また、geo 冗長ストレージまたは読み取りアクセス geo 冗長ストレージの VHD レプリカを使用した場合、VM を起動できるアプリケーション整合性スナップショットが作成されるとは限りません。
>
>

## <a name="next-steps"></a>次のステップ
障害復旧と高可用性戦略を実装する方法の詳細については、 [Azure アプリケーションの障害復旧と高可用性](../resiliency/resiliency-disaster-recovery-high-availability-azure-applications.md)に関するページを参照してください。

クラウド プラットフォームの機能の詳細な技術について理解を深めるには、「 [Azure の回復性技術ガイダンス](../resiliency/resiliency-technical-guidance.md)」を参照してください。

仮想マシンをバックアップする方法を「 [Azure 仮想マシンのバックアップ](../backup/backup-azure-vms.md)」で参照します。

Azure Site Recovery を使用して、VMWare、HYPER-V 仮想マシン上で実行されている物理的 (および仮想的) な Windows コンピューターと Linux コンピューターの保護を調整し自動化する方法を [Azure Site Recovery](https://azure.microsoft.com/documentation/learning-paths/site-recovery/)で参照します。

不明な点がある場合、または Microsoft による代理操作をご希望の場合は、 [カスタマー サポート](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade)までご連絡ください。

