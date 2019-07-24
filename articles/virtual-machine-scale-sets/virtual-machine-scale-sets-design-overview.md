---
title: Azure 仮想マシン スケール セットの設計上の考慮事項 | Microsoft Docs
description: Azure 仮想マシン スケール セットの設計上の考慮事項について説明します
keywords: Linux 仮想マシン,仮想マシン スケール セット
services: virtual-machine-scale-sets
documentationcenter: ''
author: mayanknayar
manager: jeconnoc
editor: tysonn
tags: azure-resource-manager
ms.assetid: c27c6a59-a0ab-4117-a01b-42b049464ca1
ms.service: virtual-machine-scale-sets
ms.workload: na
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: article
ms.date: 06/01/2017
ms.author: manayar
ms.openlocfilehash: 67bbad7e73f33d73d4c3f1d4f7e5599d2ef914e3
ms.sourcegitcommit: 295babdcfe86b7a3074fd5b65350c8c11a49f2f1
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/27/2018
ms.locfileid: "53791049"
---
# <a name="design-considerations-for-scale-sets"></a>スケール セットの設計上の考慮事項
この記事では、仮想マシン スケール セットの設計に関する考慮事項について説明します。 仮想マシン スケール セットに関する情報については、「 [仮想マシン スケール セットの概要](virtual-machine-scale-sets-overview.md)」を参照してください。

## <a name="when-to-use-scale-sets-instead-of-virtual-machines"></a>仮想マシンではなくスケール セットを使用するケース
一般に、類似した構成のマシン セットで高可用性インフラストラクチャをデプロイする場合は、スケール セットが便利です。 ただし、スケール セットだけで使用できる機能と、仮想マシンだけで使用できる機能があります。 それぞれのテクノロジの使用場面を適切に判断できるよう、まずはスケール セットだけで使用できる機能のうち、よく使用されるものについて見ていきましょう。

### <a name="scale-set-specific-features"></a>スケール セットだけで使用できる機能

- スケール セットの構成を指定したら、*capacity* プロパティを更新すれば、並列する複数の VM をデプロイすることができます。 このプロセスは、スクリプトを記述したり、並列する多数の VM を調整しながら個別にデプロイしたりするよりも有効です。
- [Azure の自動スケール機能を使用してスケール セットの規模を自動で設定](./virtual-machine-scale-sets-autoscale-overview.md)することができますが、個々の VM に対してはできません。
- [スケール セットの VM を再イメージ化](https://docs.microsoft.com/rest/api/compute/virtualmachinescalesets/reimage)することはできますが、[個々の VM](https://docs.microsoft.com/rest/api/compute/virtualmachines) を再イメージ化することはできません。
- スケール セットの VM を[オーバープロビジョニング](https://docs.microsoft.com/azure/virtual-machine-scale-sets/virtual-machine-scale-sets-design-overview#overprovisioning)して、信頼性高めながらデプロイ時間を短縮することができます。 個々の VM をオーバープロビジョニングするには、このアクションを実行するカスタム コードを記述する必要があります。
- [アップグレード ポリシー](./virtual-machine-scale-sets-upgrade-scale-set.md)を指定して、スケール セット内の VM 全体にアップグレードを簡単にロールアウトすることができます。 個々の VM でこれを行うには、アップデートを自分で調整する必要があります。

### <a name="vm-specific-features"></a>VM だけで使用できる機能

一部の機能は、現在 VM でのみ使用できます。

- 個々の VM からイメージをキャプチャすることはできますが、スケール セット内の VM からはできません。
- 個々の VM はネイティブ ディスクからマネージド ディスクに移行できますが、スケール セット内の VM を移行することはできません。
- 個々の VM の仮想ネットワーク インターフェイスカード (NIC) に IPv6 パブリック IP アドレスを割り当てることはできますが、スケール セット内の VM インスタンスに割り当てることはできません。 個々の VM でもスケール セット内の VM でも、前面のロード バランサーには IPv6 パブリック IP アドレスを割り当てることができます。

## <a name="storage"></a>Storage

### <a name="scale-sets-with-azure-managed-disks"></a>Azure Managed Disksでのスケール セット
スケール セットは、従来の Azure ストレージ アカウントではなく、[Azure Managed Disks](../virtual-machines/windows/managed-disks-overview.md) で作成できます。 Managed Disks には次のような利点があります。
- スケール セットの VM の一連の Azure ストレージ アカウントの事前作成が必要ありません。
- スケール セット内の VM に対して[接続されたデータ ディスク](virtual-machine-scale-sets-attached-disks.md)を定義できます。
- [セット内で最大 1,000 個の VM をサポート](virtual-machine-scale-sets-placement-groups.md)するようにスケール セットを構成できます。 

既存のテンプレートがある場合は、[Managed Disks を使用するようテンプレートを更新](virtual-machine-scale-sets-convert-template-to-md.md)することもできます。

### <a name="user-managed-storage"></a>ユーザー管理ストレージ
Azure Managed Disks で定義されていないスケール セットは、ユーザーが作成したストレージ アカウントに依存して、セット内の VM の OS ディスクを格納します。 IO の最大化を実現するためにストレージ アカウントあたり 20 VM 以下の比率とし、"_オーバープロビジョニング_" (以下を参照) も利用することをお勧めします。 また、ストレージ アカウント名の始めの文字にアルファベットを使用することをお勧めします。 これは、異なる内部システムの負荷を分散するのに役立ちます。 


## <a name="overprovisioning"></a>オーバープロビジョニング
現在、スケール セットは VM を "オーバープロビジョニング" するよう初期設定されています。 オーバープロビジョニングがオンに設定されていると、スケール セットは実際に要求された数よりも多くの VM を起動し、要求された数の VM が正常にプロビジョニングされた後で余分な VM を削除します。 オーバープロビジョニングによって、プロビジョニングの成功率がアップし、デプロイ時間が短縮されます。 必要以上の VM については請求されません。割り当て制限でもカウントされません。

オーバープロビジョニングはプロビジョニングの成功率を上げますが、出現してその後消える余分な VM を処理するように設計されていないアプリケーションにとって、混乱を招く動作を引き起こすことがあります。 オーバープロビジョニングをオフにするには、テンプレートに `"overprovision": "false"` という文字列を与えます。 詳細については、[スケール セット REST API ドキュメント](/rest/api/virtualmachinescalesets/create-or-update-a-set)をご覧ください。

スケール セットがユーザー管理のストレージを使用する場合、オーバープロビジョニングを無効にするとストレージ アカウントあたり 20 を超える VM を持つことができますが、IO パフォーマンス上の理由から、40 以下にすることをお勧めします。 

## <a name="limits"></a>制限
Marketplace イメージ (プラットフォーム イメージとも呼ばれます) 上に構築され、Azure Managed Disks を使用するように構成されたスケール セットでは、最大 1,000 個の VM の容量がサポートされます。 100 を超える VM をサポートするようにスケール セットを構成した場合、すべてのシナリオが同じようには動作しません (負荷分散など)。 詳しくは、「[Working with large virtual machine scale sets](virtual-machine-scale-sets-placement-groups.md)」(大規模な仮想マシン スケール セットの使用) をご覧ください。 

ユーザー管理のストレージ アカウントに構成されているスケール セットは、現在 100 個の VM に制限されています (また、このスケールには 5 つのストレージ アカウントが推奨されます)。

(自分でビルドした) カスタム イメージ上に構築されたスケール セットは、Azure マネージド ディスクで構成した場合に最大 600 個の VM の容量を持つことができます。 スケール セットがユーザー管理のストレージ アカウントで構成されている場合は、1 つのストレージ アカウント内にすべての OS ディスク VHD を作成する必要があります。 その結果、カスタム イメージとユーザー管理のストレージで構築されたスケール セットの VM の推奨される最大数は 20 になります。 オーバープロビジョニングをオフにすると、最大 40 になります。

VM の数が、これらの制限で許可されている数を超える場合は、 [こちらのテンプレート](https://github.com/Azure/azure-quickstart-templates/tree/master/301-custom-images-at-scale)に示すように、複数のスケール セットをデプロイする必要があります。

