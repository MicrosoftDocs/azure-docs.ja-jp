---
title: "Azure Cloud Services に影響を与える Azure サービスの中断が発生した場合の対処方法 | Microsoft Docs"
description: "Azure Cloud Services に影響を与える Azure サービスの中断が発生した場合の対処方法について説明します。"
services: cloud-services
documentationcenter: 
author: kmouss
manager: drewm
editor: 
ms.assetid: e52634ab-003d-4f1e-85fa-794f6cd12ce4
ms.service: cloud-services
ms.workload: cloud-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/16/2016
ms.author: kmouss;aglick
translationtype: Human Translation
ms.sourcegitcommit: e7d3c82e235d691c4ab329be3b168dcccc19774f
ms.openlocfilehash: 5db1864f5fb04b30a7b8ce59932e826fcef792f0


---
# <a name="what-to-do-in-the-event-of-an-azure-service-disruption-that-impacts-azure-cloud-services"></a>Azure Cloud Services に影響を与える Azure サービスの中断が発生した場合の対処方法
Microsoft では、必要なときにサービスがいつでも使用できるように取り組んでいますが、 やむを得ない事情により、計画されていないサービス中断が発生することがあります。

Microsoft は、稼働時間と接続に関するコミットメントとして、サービスのサービス レベル アグリーメント (SLA) を提供しています。 個々の Azure サービスの SLA については、 [Azure サービス レベル アグリーメント](https://azure.microsoft.com/support/legal/sla/)に関するページを参照してください。

Azure には、可用性の高いアプリケーションをサポートするさまざまなプラットフォーム機能が既に組み込まれています。 これらのサービスの詳細については、 [Azure アプリケーションの障害復旧と高可用性](../resiliency/resiliency-disaster-recovery-high-availability-azure-applications.md)に関する記事をご覧ください。

この記事では、大きな自然災害や広範囲にわたるサービス中断により、リージョン全体で障害が発生した場合の真の障害復旧シナリオについて説明します。 こうした状況はほとんど発生しませんが、リージョン全体で中断が発生する可能性に対して準備する必要があります。 リージョン全体でサービス中断が発生した場合、データのローカル冗長コピーは、一時的に使用できなくなります。 geo レプリケーションを有効にしてある場合は、Azure Storage の BLOB とテーブルのコピーがさらに 3 つ、別のリージョンに格納されます。 地域的な停電や災害が発生し、プライマリ リージョンを復旧できない場合は、すべての DNS エントリが、geo レプリケートされたリージョンに再マッピングされます。

> [!NOTE]
> ユーザーはこのプロセスを制御できないこと、およびデータセンター全体のサービス中断の場合にのみ行われることに注意してください。 そのため、最高レベルの可用性を実現するには、アプリケーション固有の他のバックアップ戦略にも依存する必要があります。 詳細については、「[Microsoft Azure 上に構築されたアプリケーションの障害復旧と高可用性](../resiliency/resiliency-disaster-recovery-high-availability-azure-applications.md)」を参照してください。 独自のフェールオーバーを使用できるようにする場合は、[読み取りアクセス geo 冗長ストレージ (RA-GRS)](../storage/storage-redundancy.md#read-access-geo-redundant-storage) の使用を検討してください。この場合は、他のリージョンに、データの読み取り専用のコピーが作成されます。
>
>

こうした状況はほとんど発生しませんが、Azure Virtual Machines (VM) のアプリケーションがデプロイされているリージョン全体でサービス中断が発生した場合は、Azure VM に関する次のガイダンスに従って対応してください。

## <a name="option-1-wait-for-recovery"></a>オプション 1: 復旧を待つ
この場合、ユーザーによる操作は必要ありません。 Azure チームがサービスを利用できるようにするために鋭意取り組んでいることをご理解ください。 現在のサービスの状態は、 [Azure サービスの正常性ダッシュボード](https://azure.microsoft.com/status/)で確認できます。

> [!NOTE]
> お客様が Azure Site Recovery を設定していない場合や別のリージョンにセカンダリ デプロイを持っている場合は、これが最適なオプションです。
>
>

デプロイしたクラウド サービスにすぐにアクセスする必要があるお客様の場合は、以下のオプションを利用できます。

> [!NOTE]
> これらのオプションを使用すると、一部のデータが失われる可能性があることに注意してください。     
>
>

## <a name="option-2-re-deploy-your-cloud-service-configuration-to-a-new-region"></a>オプション 2: 新しいリージョンにクラウド サービス構成を再デプロイする
元のコードがある場合は、アプリケーション、関連付けられている構成、および関連付けられているリソースを、新しいリージョンの新しいクラウド サービスに簡単に再デプロイすることができます。  

クラウド サービス アプリケーションを作成してデプロイする方法の詳細については、「[クラウド サービスを作成してデプロイする方法](cloud-services-how-to-create-deploy-portal.md)」を参照してください。

アプリケーションのデータ ソースによっては、アプリケーションのデータ ソースの復旧手順を確認することが必要になる場合があります。

* Azure Storage データ ソースの場合は、 [Azure Storage のレプリケーション](../storage/storage-redundancy.md#read-access-geo-redundant-storage) に関するセクションを参照して、アプリケーション用に選択したレプリケーション モデルに応じて使用できるオプションについて確認してください。
* SQL Database ソースの場合は、「 [概要: SQL Database を使用したクラウド ビジネス継続性とデータベース障害復旧](../sql-database/sql-database-business-continuity.md) 」を参照して、アプリケーション用に選択したレプリケーション モデルに応じて使用できるオプションについて確認してください。

## <a name="option-3-use-a-backup-deployment-through-azure-traffic-manager"></a>オプション 3: Azure Traffic Manager を介してバックアップ デプロイを使用する
このオプションは、リージョンの障害復旧を考慮に入れて既にアプリケーション ソリューションを設計していることを前提としています。 Cloud Services アプリケーションのセカンダリ デプロイを既に別のリージョンで実行していて、Traffic Manager チャネルを介して接続されている場合に、このオプションを使用できます。 この場合、セカンダリ デプロイの正常性を確認し、 正常であれば、Azure Traffic Manager を介して、そこにトラフィックをリダイレクトできます。 そうすることで、Azure Traffic Manager のトラフィック ルーティング方法とフェールオーバー順序構成の利点を活用できます。 詳細については、「[Traffic Manager について](../traffic-manager/traffic-manager-overview.md)」を参照してください。

![Balancing Azure Cloud Services across regions with Azure Traffic Manager](./media/cloud-services-disaster-recovery-guidance/using-azure-traffic-manager.png)

## <a name="next-steps"></a>次のステップ
障害復旧と高可用性戦略を実装する方法の詳細については、 [Azure アプリケーションの障害復旧と高可用性](../resiliency/resiliency-disaster-recovery-high-availability-azure-applications.md)に関するページを参照してください。

クラウド プラットフォームの機能の詳細な技術について理解を深めるには、「 [Azure の回復性技術ガイダンス](../resiliency/resiliency-technical-guidance.md)」を参照してください。

不明な点がある場合、または Microsoft による代理操作をご希望の場合は、 [カスタマー サポート](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade)までご連絡ください。



<!--HONumber=Nov16_HO3-->


