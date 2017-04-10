---
title: "Azure 仮想マシン スケール セットの規模を設計する | Microsoft Docs"
description: "Azure 仮想マシン スケール セットの規模を設計する方法について説明します"
keywords: "Linux 仮想マシン,仮想マシン スケール セット"
services: virtual-machine-scale-sets
documentationcenter: 
author: gatneil
manager: madhana
editor: tysonn
tags: azure-resource-manager
ms.assetid: c27c6a59-a0ab-4117-a01b-42b049464ca1
ms.service: virtual-machine-scale-sets
ms.workload: na
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: article
ms.date: 02/13/2017
ms.author: negat
translationtype: Human Translation
ms.sourcegitcommit: e869b06935736fae72bd3b5407ebab7c3830098d
ms.openlocfilehash: de3687a1bf36bf49db400a5660ac631f20b629d0
ms.lasthandoff: 02/14/2017


---
# <a name="designing-vm-scale-sets-for-scale"></a>VM スケール セットの規模を設計する
このトピックでは、仮想マシン スケール セットの設計に関する考慮事項について説明します。 仮想マシン スケール セットに関する情報については、「 [仮想マシン スケール セットの概要](virtual-machine-scale-sets-overview.md)」を参照してください。

## <a name="storage"></a>Storage

### <a name="scale-sets-with-azure-managed-disks"></a>Azure Managed Disksでのスケール セット
スケール セットを今すぐ [Azure Managed Disks](../storage/storage-managed-disks-overview.md) で作成できます。 Managed Disks には次のような利点があります。
- スケール セットの VM の一連の Azure ストレージ アカウントの事前作成が必要ありません。
- スケール セット内の VM に対して[接続されたデータ ディスク](virtual-machine-scale-sets-attached-disks.md)を定義できます。
- [セット内で最大 1,000 個の VM をサポート](virtual-machine-scale-sets-placement-groups.md)するようにスケール セットを構成できます。 

スケール セットは、Azure コンピューティング API のバージョン "2016-04-30-preview" 以降の Managed Disks で作成できます。 スケール セット テンプレートを Managed Disks に変換する方法については、「[Convert a scale set template to a managed disk scale set template](virtual-machine-scale-sets-convert-template-to-md.md)」(スケール セット テンプレートを管理ディスク スケール セット テンプレートに変換する) をご覧ください。

### <a name="user-managed-storage"></a>ユーザー管理ストレージ
Azure Managed Disks で定義されていないスケール セットは、ユーザーが作成したストレージ アカウントに依存して、セット内の VM の OS ディスクを格納します。 IO の最大化を実現するためにストレージ アカウントあたり 20 VM 以下の比率とし、"_オーバープロビジョニング_" (以下を参照) も利用することをお勧めします。 また、ストレージ アカウント名の始めの文字にアルファベットを使用することをお勧めします。 これは、異なる内部システムの負荷を分散するのに役立ちます。 

>[!NOTE]
>VM Scale Sets API バージョン `2016-04-30-preview` では、オペレーティング システム ディスクと追加データ ディスクでの Azure Managed Disks の使用がサポートされます。 詳しくは、[Managed Disks の概要](../storage/storage-managed-disks-overview.md)と[接続されたデータ ディスクの使用](virtual-machine-scale-sets-attached-disks.md)に関するページをご覧ください。 

## <a name="overprovisioning"></a>オーバープロビジョニング
"2016-03-30" API バージョンより、VM Scale Sets は VM を "オーバープロビジョニング" するように初期設定されています。 オーバープロビジョニングがオンに設定されていると、スケール セットは実際に要求された数よりも多くの VM を起動し、要求された数の VM が正常にプロビジョニングされた後で余分な VM を削除します。 オーバープロビジョニングによって、プロビジョニングの成功率がアップし、デプロイ時間が短縮されます。 必要以上の VM については請求されません。割り当て制限でもカウントされません。

オーバープロビジョニングはプロビジョニングの成功率を上げますが、出現してその後消える余分な VM を処理するように設計されていないアプリケーションにとって、混乱を招く動作を引き起こすことがあります。 オーバープロビジョニングをオフにするには、テンプレートに "overprovision": "false" という文字列を与えます。 詳細については、 [VM スケール セット REST API ドキュメント](https://msdn.microsoft.com/library/azure/mt589035.aspx)をご覧ください。

スケール セットがユーザー管理のストレージを使用する場合、オーバープロビジョニングを無効にするとストレージ アカウントあたり 20 を超える VM を持つことができますが、IO パフォーマンス上の理由から、40 以下にすることをお勧めします。 

## <a name="limits"></a>制限
Marketplace イメージ (プラットフォーム イメージとも呼ばれます) 上に構築され、Azure Managed Disks を使用するように構成されたスケール セットでは、最大 1,000 個の VM の容量がサポートされます。 100 を超える VM をサポートするようにスケール セットを構成した場合、すべてのシナリオが同じようには動作しません (負荷分散など)。 詳しくは、「[Working with large virtual machine scale sets](virtual-machine-scale-sets-placement-groups.md)」(大規模な仮想マシン スケール セットの使用) をご覧ください。 

ユーザー管理のストレージ アカウントに構成されているスケール セットは、現在 100 個の VM に制限されています (また、このスケールには 5 つのストレージ アカウントが推奨されます)。

(自分でビルドした) カスタム イメージ上に構築されたスケール セットは、Azure Managed Disks で構成した場合に最大 100 個の VM の容量を持つことができます。 スケール セットがユーザー管理のストレージ アカウントで構成されている場合は、1 つのストレージ アカウント内にすべての OS ディスク VHD を作成する必要があります。 その結果、カスタム イメージとユーザー管理のストレージで構築されたスケール セットの VM の推奨される最大数は 20 になります。 オーバープロビジョニングをオフにすると、最大 40 になります。

VM の数が、これらの制限で許可されている数を超える場合は、 [こちらのテンプレート](https://github.com/Azure/azure-quickstart-templates/tree/master/301-custom-images-at-scale)に示すように、複数のスケール セットをデプロイする必要があります。


