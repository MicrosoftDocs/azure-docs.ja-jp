---
title: "Advisor の高可用性に関する推奨事項 | Microsoft Docs"
description: "Azure Advisor を使用して、Azure のデプロイの高可用性を向上させます。"
services: advisor
documentationcenter: NA
author: KumudD
manager: carmonm
editor: 
ms.assetid: 
ms.service: advisor
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 11/16/2016
ms.author: kumud
ms.openlocfilehash: 7b63de2453180e562596c211d40cebe85b95bd54
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/11/2017
---
# <a name="advisor-high-availability-recommendations"></a>Advisor の高可用性に関する推奨事項

Azure Advisor を使用して、ビジネスに不可欠なアプリケーションの継続稼働を保証し、さらに向上させることができます。 Advisor による高可用性に関する推奨事項は、Advisor ダッシュボードの**[高可用性]** タブで取得できます。

![Advisor ダッシュボードの [高可用性] ボタン](./media/advisor-high-availability-recommendations/advisor-high-availability-tab.png)


## <a name="ensure-virtual-machine-fault-tolerance"></a>仮想マシンのフォールト トレランスを確保する

Advisor は、可用性セットの一部ではない仮想マシンを識別し、それらを可用性セットに移動することを推奨します。 アプリケーションに冗長性をもたらすには、可用性セット内に 2 つ以上の仮想マシンをグループ化することをお勧めします。 このような構成により、計画済みのメンテナンス イベント中でも計画外のメンテナンス イベント中でも、少なくとも 1 台の仮想マシンが利用可能となり、Azure 仮想マシンの SLA が満たされます。 仮想マシンの可用性セットを作成するか、既存の可用性セットに仮想マシンを追加することを選択できます。

> [!NOTE]
> 可用性セットを作成する場合は、少なくとも 1 台以上の仮想マシンを可用性セットに追加する必要があります。 2 台以上の仮想マシンを 1 つの可用性セットにグループ化して、障害の発生時に少なくとも 1 台のマシンを使用できるようにしておくことをお勧めします。

![Advisor に関する推奨事項: 仮想マシンの冗長性実現のため、可用性セットを使用する](./media/advisor-high-availability-recommendations/advisor-high-availability-create-availability-set.png)

## <a name="ensure-availability-set-fault-tolerance"></a>可用性セットのフォールト トレランスを確保する 

Advisor は、1 台の仮想マシンのみを含む可用性セットを識別し、そのセットにさらに仮想マシンを追加することを推奨します。 アプリケーションに冗長性をもたらすには、可用性セット内に 2 つ以上の仮想マシンをグループ化することをお勧めします。 このような構成により、計画済みのメンテナンス イベント中でも計画外のメンテナンス イベント中でも、少なくとも 1 台の仮想マシンが利用可能となり、Azure 仮想マシンの SLA が満たされます。 仮想マシンを作成するか、既存の仮想マシンを使用してそのマシンを可用性セットに追加することを選択できます。  

![Advisor に関する推奨事項: 1 つ以上の VM をこの可用性セットに追加する](./media/advisor-high-availability-recommendations/advisor-high-availability-add-vm-to-availability-set.png)


## <a name="ensure-application-gateway-fault-tolerance"></a>アプリケーション ゲートウェイのフォールト トレランスを確保する
アプリケーション ゲートウェイを備えるミッション クリティカルなアプリケーションのビジネス継続性を確保するために、Advisor は、フォールト トレランス用に構成されていないアプリケーション ゲートウェイ インスタンスを識別し、実施できる修復アクションを提案します。 Advisor は、中規模または大規模な単一インスタンス アプリケーション ゲートウェイを識別し、少なくとも 1 つ以上のインスタンスを追加することを推奨します。 また、1 つまたは複数の小規模アプリケーション ゲートウェイを識別し、中規模または大規模な SKU に移行することを推奨します。 これらの修復アクションでは、アプリケーション ゲートウェイ インスタンスがこれらのリソースの現在の SLA 要件を満たすように構成されていることを確認します。

![Advisor の推奨事項: 2 つ以上の中規模または大規模なアプリケーション ゲートウェイ インスタンスをデプロイする](./media/advisor-high-availability-recommendations/advisor-high-availability-application-gateway.png)

## <a name="improve-the-performance-and-reliability-of-virtual-machine-disks"></a>仮想マシンのディスクのパフォーマンスと信頼性の向上

Advisor は、Standard ディスクを使用している仮想マシンを識別し、Premium ディスクにアップグレードすることを推奨します。
 
Azure Premium Storage は、高負荷の I/O ワークロードを実行する仮想マシン向けに高パフォーマンスで待ち時間の少ないディスク サポートを提供します。 Premium Storage アカウントを使用する仮想マシンのディスクでは、ソリッド ステート ドライブ (SSD) にデータを格納します。 アプリケーションで最適なパフォーマンスを実現するには、高い IOPS を必要とする仮想マシン ディスクは Premium Storage に移行することをお勧めします。 

ディスクが高い IOPS を必要としない場合は、Standard Storage で管理することでコストを制限できます。 Standard Storage は、仮想マシンのディスク データを、SSD ではなくハード ディスク ドライブ (HDD) に格納します。 仮想マシンのディスクを Premium ディスクに移行することを選択できます。 Premium ディスクは、ほとんどの仮想マシンの SKU でサポートされます。 ただし、場合によっては、Premium ディスクを使用するには、仮想マシンの SKU もアップグレードする必要があります。

![Advisor の推奨事項: Premium ディスクにアップグレードして、仮想マシンのディスクの信頼性を向上させる](./media/advisor-high-availability-recommendations/advisor-high-availability-upgrade-to-premium-disks.png)

## <a name="protect-your-virtual-machine-data-from-accidental-deletion"></a>誤って削除されないように、仮想マシンのデータを保護する
Advisor は、バックアップが有効になっていない仮想マシンを識別子、バックアップを有効にすることを推奨します。 仮想マシンのバックアップを設定することにより、ビジネス クリティカルなデータの可用性が確保され、偶発的な削除または破損からデータを保護します。

![Advisor の推奨事項: ミッション クリティカルなデータを保護するよう仮想マシンのバックアップを構成する](./media/advisor-high-availability-recommendations/advisor-high-availability-virtual-machine-backup.png)

## <a name="access-high-availability-recommendations-in-advisor"></a>Advisor の高可用性に関する推奨事項にアクセスする方法

1. [Azure ポータル](https://portal.azure.com)にサインインします。

2. 左側のウィンドウで、**[その他のサービス]** をクリックします。

3. サービス メニュー ウィンドウの **[Monitoring and Management (監視と管理)]** で、**[Azure Advisor]** をクリックします。  
 Advisor ダッシュボードが表示されます。

4. Advisor ダッシュボードで、**[高可用性]** タブをクリックし、推奨事項を受け入れるサブスクリプションを選択します。

> [!NOTE]
> Advisor の推奨事項にアクセスするには、最初に Advisor に*サブスクリプションを登録する*必要があります。 *サブスクリプションの所有者*が Advisor ダッシュボードを起動して **[推奨事項の取得]** ボタンをクリックすると、サブスクリプションが登録されます。 これは *1 回限りの操作*です。 サブスクリプションが登録されると、サブスクリプション、リソース グループ、または特定のリソースの*所有者*、*共同作成者*、または*閲覧者*として Advisor の推奨事項にアクセスできます。

## <a name="next-steps"></a>次のステップ

Advisor の推奨事項について詳しくは、以下を参照してください。
* [Azure Advisor の概要](advisor-overview.md)
* [Advisor の使用を開始する](advisor-get-started.md)
* [Advisor のコストに関する推奨事項](advisor-performance-recommendations.md)
* [Advisor のパフォーマンスに関する推奨事項](advisor-performance-recommendations.md)
* [Advisor のセキュリティに関する推奨事項](advisor-security-recommendations.md)

