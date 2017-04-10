---
title: "Advisor の高可用性に関する推奨事項 | Microsoft Docs"
description: "Azure Advisor を使用して、Azure のデプロイの高可用性を向上させます。"
services: advisor
documentationcenter: NA
author: kumudd
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
translationtype: Human Translation
ms.sourcegitcommit: e7fc6cb4ee4752bb7ed90a8f64edcc7c82ec3a88
ms.openlocfilehash: a6ee1d018bf33aa76d089d20f6e71318a3586aa8
ms.lasthandoff: 02/21/2017

---

# <a name="advisor-high-availability-recommendations"></a>Advisor の高可用性に関する推奨事項

Advisor を使用して、ビジネスに不可欠なアプリケーションの継続稼働を保証し、さらに向上させることができます。 Advisor による高可用性に関する推奨事項は、Advisor ダッシュボードの**[高可用性]** タブで取得できます。

![](./media/advisor-high-availability-recommendations/advisor-high-availability-tab.png)


## <a name="virtual-machines-without-an-availability-set"></a>可用性セットに属していない仮想マシン

Advisor は、可用性セットの一部ではない仮想マシンを識別し、それらを可用性セットに移動することを推奨します。 アプリケーションに冗長性をもたらすには、可用性セット内に&2; 台以上の仮想マシンをグループ化することをお勧めします。 このような構成により、計画済みのメンテナンス イベント中でも計画外のメンテナンス イベント中でも、少なくとも&1; 台の仮想マシンが利用可能となり、Azure 仮想マシンの SLA が満たされます。 仮想マシンの可用性セットを作成するか、既存の可用性セットに仮想マシンを追加することを選択できます。

> [!NOTE]
> 可用性セットを作成する場合は、作成後に少なくとも&1; 台以上の仮想マシンを可用性セットに追加する必要があります。 2 台以上の仮想マシンを&1; つの可用性セットにグループ化して、障害の発生時にいずれかのマシンを使用できるようにしておくことをお勧めします。

![](./media/advisor-high-availability-recommendations/advisor-high-availability-create-availability-set.png)

## <a name="availability-sets-with-a-single-virtual-machine"></a>1 台の仮想マシンを含む可用性セット 

Advisor は、1 台の仮想マシンのみを含む可用性セットを識別し、そのセットにさらに仮想マシンを追加することを推奨します。 アプリケーションに冗長性をもたらすには、可用性セット内に&2; つ以上の仮想マシンをグループ化することをお勧めします。 このような構成により、計画済みのメンテナンス イベント中でも計画外のメンテナンス イベント中でも、少なくとも&1; 台の仮想マシンが利用可能となり、Azure 仮想マシンの SLA が満たされます。 仮想マシンを作成するか、既存の仮想マシンを使用してそのマシンを可用性セットに追加することを選択できます。  


![](./media/advisor-high-availability-recommendations/advisor-high-availability-add-vm-to-availability-set.png)

## <a name="virtual-machines-with-standard-disks"></a>Standard ディスクを使用する仮想マシン

Advisor は、Standard ディスクを使用している仮想マシンを識別し、Premium ディスクにアップグレードすることを推奨します。  
Azure Premium Storage は、高負荷の I/O ワークロードを実行する仮想マシン向けに高パフォーマンスで待ち時間の少ないディスク サポートを提供します。 Premium Storage を使用する仮想マシンでは、ソリッド ステート ドライブ (SSD) にデータを格納します。 高い IOPS を必要とする仮想マシンのディスクは Azure Premium Storage に移行して、アプリケーションが最高のパフォーマンスを発揮できるようにすることをお勧めします。 ディスクが高い IOPS を必要としない場合は、Standard Storage で管理することでコストを制限できます。 Standard Storage は、仮想マシンのディスク データを、SSD ではなくハード ディスク ドライブ (HDD) に格納します。 仮想マシンのディスクを Premium ディスクに移行することを選択できます。 Premium ディスクは、ほとんどの仮想マシンの SKU でサポートされます。 ただし、場合によっては、Premium ディスクを使用するには、仮想マシンの SKU もアップグレードする必要があります。   

![](./media/advisor-high-availability-recommendations/advisor-high-availability-upgrade-to-premium-disks.png) 

## <a name="how-to-access-high-availability-recommendations-in-advisor"></a>Advisor の高可用性に関する推奨事項にアクセスする方法

1. [Azure Portal](https://portal.azure.com) にサインインします。
2. 左側のナビゲーション ウィンドウで **[その他のサービス]** をクリックし、サービス メニュー ウィンドウで **[監視と管理]** までスクロールし、**[Azure Advisor]** をクリックします。 これにより、Advisor ダッシュボードが起動します。 
3. Advisor ダッシュボードで、**[高可用性]** タブをクリックし、推奨事項を受け入れるサブスクリプションを選択します。

> [!NOTE]
> Advisor の推奨事項にアクセスするには、最初にサブスクリプションを Advisor に**登録**する必要があります。 **サブスクリプションの所有者**が Advisor ダッシュボードを起動して **[推奨の取得]** ボタンをクリックすると、サブスクリプションが登録されます。 これは **1 回限りの操作**です。 サブスクリプションが登録されると、サブスクリプション、リソース グループ、特定のリソースの**所有者**、**共同作成者**、または**閲覧者**が Advisor の推奨事項にアクセスできます。

## <a name="next-steps"></a>次のステップ

Advisor の推奨事項の詳細については、次のリソースをご覧ください。
-  [Azure Advisor の概要](advisor-overview.md)
-  [Advisor の使用を開始する](advisor-get-started.md)
-  [Advisor のセキュリティに関する推奨事項](advisor-security-recommendations.md)
-  [Advisor のパフォーマンスに関する推奨事項](advisor-performance-recommendations.md)
-  [Advisor のコストに関する推奨事項](advisor-performance-recommendations.md)

