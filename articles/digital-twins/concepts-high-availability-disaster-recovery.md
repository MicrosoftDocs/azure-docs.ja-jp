---
title: 高可用性とディザスター リカバリー
titleSuffix: Azure Digital Twins
description: ディザスター リカバリー機能を持つ高可用性 Azure IoT ソリューションの構築を支援する Azure および Azure Digital Twins の機能について説明します。
author: baanders
ms.author: baanders
ms.date: 10/14/2020
ms.topic: conceptual
ms.service: digital-twins
ms.openlocfilehash: 3336a086fbe8f4291f752836a610cd80b773ec2d
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "98790818"
---
# <a name="azure-digital-twins-high-availability-and-disaster-recovery"></a>Azure Digital Twins の高可用性とディザスター リカバリー

回復性がある IoT ソリューションで考慮すべき主な領域は、事業継続とディザスター リカバリーです。 **高可用性 (HA)** と **ディザスター リカバリー (DR)** を設計すると、お客様のソリューションに適したアップタイム目標を定義し、達成するのに役立ちます。

この記事では、特に Azure Digital Twins サービスによって提供されている HA および DR の機能について説明します。

Azure Digital Twins では、次の機能オプションがサポートされています。
* *リージョン内 HA* -サービスのアップタイムを実現するために組み込まれている冗長性
* *リージョン間 DR* -予期しないデータ センターの障害が発生した場合の Azure geo ペア リージョンへのフェールオーバー

HA/DR の設計に関する一般的な Azure ガイダンスについては、[*ベスト プラクティス*](#best-practices)に関するセクションも参照してください。

## <a name="intra-region-ha"></a>リージョン内 HA
 
Azure Digital Twins では、サービス内で冗長性を実装することで、リージョン内 HA が実現されます。 これは、アップタイムの[サービス SLA](https://azure.microsoft.com/support/legal/sla/digital-twins)に反映されます。 **これらの HA 機能を利用するために、Azure Digital Twins ソリューションの開発者が追加の作業を行う必要はありません。** Azure Digital Twins ではかなり高いアップタイムが保証されていますが、分散コンピューティング プラットフォームの場合と同様に一時的な障害が発生する可能性があります。 一時的な障害に対処するには、クラウド アプリケーションと対話するコンポーネントに適切な再試行ポリシーを組み込む必要があります。

## <a name="cross-region-dr"></a>リージョン間 DR

リージョン内の停電やその他のイベントにより、データセンターに長時間の停止が発生する場合があります。 このようなイベントはまれであり、障害時に、前述のリージョン内 HA 機能があっても役立つとは限りません。 Azure Digital Twins では、Microsoft が開始するフェールオーバーを使用して、これに対処します。

**Microsoft が開始するフェールオーバー** は、Microsoft によって実行されます。まれではありますが、影響を受けるリージョンのすべての Azure Digital Twins インスタンスは、対応する geo ペア リージョンにフェールオーバーされます。 このプロセスは既定のオプションであり (ユーザーがオプトアウトする方法はありません)、ユーザー操作は必要はありません。 Microsoft は、このオプションを実行するタイミングを決定する権利を留保します。 このメカニズムでは、ユーザーのインスタンスがフェールオーバーされる前にユーザーの同意を必要としません。

>[!NOTE]
> 一部の Azure サービスには、**顧客が開始するフェールオーバー** と呼ばれる追加のオプションも用意されています。たとえば、DR ドリルを実行するために、顧客が自分のインスタンスのフェールオーバーのみを開始できます。 現時点で Azure Digital Twins では、このメカニズムが **サポートされていません**。 

## <a name="monitor-service-health"></a>サービス正常性の監視

Azure Digital Twins インスタンスがフェールオーバーされ回復されると、[Azure Service Health](../service-health/service-health-overview.md) ツールを使用してプロセスを監視できます。 Service Health は、さまざまなリージョンやサブスクリプションにわたる Azure サービスの正常性を追跡し、サービスに影響する類いの障害やダウンタイムに関するお知らせを共有します。

フェールオーバー イベント中に、Service Health は、いつサービスが停止し、いつバックアップされたかを示すことができます。

Service Health イベントを表示するには...
1. Azure portal で [Service Health](https://portal.azure.com/?feature.customportal=false#blade/Microsoft_Azure_Health/AzureHealthBrowseBlade/serviceIssues) に移動します (このリンクを使用しても、ポータルの検索バーを使用して検索してもかまいません)。
1. 左側のメニューを使用して、 *[正常性の履歴]* ページに切り替えます。
1. **Azure Digital Twins** で始まる *問題の名前* を探し、それを選択します。

    :::image type="content" source="media/concepts-high-availability-disaster-recovery/navigate.png" alt-text="[正常性の履歴] ページを示す Azure portal のスクリーンショット。過去数日間のいくつかの問題の一覧があり、&quot;Azure Digital Twins - 西ヨーロッパ - 軽減済み&quot; という問題が強調表示されています。" lightbox="media/concepts-high-availability-disaster-recovery/navigate.png":::

1. 停止に関する概要情報については、 *[概要]* タブを確認してください。

    :::image type="content" source="media/concepts-high-availability-disaster-recovery/summary.png" alt-text="[正常性の履歴] ページでは [概要] タブが強調表示されています。このタブには、影響を受けたリソース、そのリージョン、そのサブスクリプションなどの一般的な情報が表示されます。" lightbox="media/concepts-high-availability-disaster-recovery/summary.png":::
1. 時間の経過に伴う問題の詳細と更新については、 *[問題の更新]* タブを確認してください。

    :::image type="content" source="media/concepts-high-availability-disaster-recovery/issue-updates.png" alt-text="[正常性の履歴] ページでは [問題の更新] タブが強調表示されています。このタブには、1 日前からの現在の状態を示すいくつかのエントリが表示されます。" lightbox="media/concepts-high-availability-disaster-recovery/issue-updates.png":::


このツールに表示される情報は、1 つの Azure Digital インスタンスに特有のものではありません。 Service Health を使用して、特定のリージョンまたはサブスクリプションでの Azure Digital Twins サービスの状況を把握した後、[リソース正常性ツール](troubleshoot-resource-health.md)を使用して特定のインスタンスにドリルダウンし、それらが影響を受けているかどうかを確認することにより、監視を一歩進めることができます。

## <a name="best-practices"></a>ベスト プラクティス

HA/DR のベスト プラクティスについては、このトピックの次の Azure ガイダンスを参照してください。 
* [*Azure ビジネス継続性テクニカル ガイダンス*](/azure/architecture/framework/resiliency/overview)に関する記事では、ビジネス継続性とディザスター リカバリーについて考慮する際に役立つ一般的なフレームワークについて説明しています。 
* 「[*Disaster recovery and high availability for Azure applications*](/azure/architecture/framework/resiliency/backup-and-recovery)」(Azure アプリケーションのディザスター リカバリーと高可用性) では、高可用性 (HA) とディザスター リカバリー (DR) を実現するための Azure アプリケーションの戦略に関するアーキテクチャのガイダンスを確認できます。

## <a name="next-steps"></a>次の手順 

Azure Digital Twins ソリューションの概要については、次を参照してください。
 
* [*Azure Digital Twins とは*](overview.md)
* [*クイック スタート:サンプル シナリオを精査する*](quickstart-adt-explorer.md)