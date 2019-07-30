---
title: 評価のために Azure Monitor for VMs (プレビュー) を有効にする | Microsoft Docs
description: 1 つの Azure 仮想マシン上または仮想マシン スケール セット上で Azure Monitor for VMs を評価する方法について説明します。
services: azure-monitor
documentationcenter: ''
author: mgoedtel
manager: carmonm
editor: ''
ms.assetid: ''
ms.service: azure-monitor
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 05/09/2019
ms.author: magoedte
ms.openlocfilehash: ec909bcd16f923bbd7036f6a69df2bbb07e561b8
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/13/2019
ms.locfileid: "67122503"
---
# <a name="enable-azure-monitor-for-vms-preview-for-evaluation"></a>評価のために Azure Monitor for VMs (プレビュー) を有効にする

少数の Azure 仮想マシン (VM) 上または 1 つの VM または仮想マシンのスケール セット上で、Azure Monitor for VMs (プレビュー) を評価できます。 監視を有効にする最も簡単で直接的な方法は Azure portal からです。 目標は、VM を監視し、パフォーマンスや可用性の問題を発見することです。 

始める前に、[前提条件](vminsights-enable-overview.md)を読み、サブスクリプションとリソースが要件を満たしていることを確認します。  

## <a name="enable-monitoring-for-a-single-azure-vm"></a>1 つの Azure VM の監視を有効にする
Azure VM の監視を有効にするには:

1. [Azure Portal](https://portal.azure.com) にサインインします。

1. **[仮想マシン]** を選択します。

1. 一覧から VM を選択します。

1. [VM] ページの **[監視]** セクションで、 **[Insights (プレビュー)]** を選択します。

1. **[Insights (プレビュー)]** ページで、 **[今すぐ試す]** を選択します。

    ![VM に対して Azure Monitor for VMs を有効にする](./media/vminsights-enable-single-vm/enable-vminsights-vm-portal-01.png)

1. **[Azure Monitor Insights Onboarding]\(Azure Monitor Insights の配布準備\)** ページで、同じサブスクリプションに既存の Log Analytics ワークスペースがある場合は、ドロップダウン リストでそれを選択します。  

    この一覧では、サブスクリプションで VM がデプロイされている既定のワークスペースと場所が事前に選択されています。 

    >[!NOTE]
    >VM からの監視データを格納するための新しい Log Analytics ワークスペースを作成するには、[Log Analytics ワークスペースの作成](../../azure-monitor/learn/quick-create-workspace.md)に関するページを参照してください。 Log Analytics ワークスペースは、[サポートされているリージョン](vminsights-enable-overview.md#log-analytics)のいずれかに属している必要があります。

監視を有効にした後、VM の正常性メトリックが表示されるまでに 10 分ほどかかることがあります。

![Azure Monitor for VMs の監視デプロイ プロセスを有効にする](./media/vminsights-enable-single-vm/onboard-vminsights-vm-portal-status.png)

## <a name="enable-monitoring-for-a-single-virtual-machine-scale-set"></a>1 つの仮想マシン スケール セットの監視を有効にする

Azure 仮想マシン スケール セットの監視を有効にするには:

1. [Azure Portal](https://portal.azure.com) にサインインします。

2. **[仮想マシン スケール セット]** を選択します。

3. 一覧から仮想マシン スケール セットを選択します。

4. [仮想マシン スケール セット] ページの **[監視]** セクションで、 **[インサイト (プレビュー)]** を選択します。

5. **[インサイト (プレビュー)]** ページで、使用する既存の Log Analytics ワークスペースがある場合は、ドロップダウン リストでそれを選択します。

    このリストでは、サブスクリプションで VM がデプロイされている既定のワークスペースと場所が事前に選択されています。 

    ![仮想マシン スケール セットに対して Azure Monitor for VMs を有効にする](./media/vminsights-enable-single-vm/enable-vminsights-vmss-portal-01.png)

    >[!NOTE]
    >仮想マシン スケール セットからの監視データを格納するための新しい Log Analytics ワークスペースを作成するには、[Log Analytics ワークスペースの作成](../learn/quick-create-workspace.md)に関するページを参照してください。 Log Analytics ワークスペースは、[サポートされているリージョン](vminsights-enable-overview.md#log-analytics)のいずれかに属している必要があります。

監視を有効にした後、スケール セットの監視データが表示されるまでに10 分ほどかかることがあります。

>[!NOTE]
>スケール セットに手動アップグレード モデルを使用している場合は、インスタンスをアップグレードして設定を完了します。 **[インスタンス]** ページの **[設定]** セクションからアップグレードを開始できます。

![Azure Monitor for VMs の監視デプロイ プロセスを有効にする](./media/vminsights-enable-single-vm/onboard-vminsights-vmss-portal-status-01.png)

VM または仮想マシン スケール セットの監視が有効になったので、Azure Monitor for VMs での分析に監視情報を使用できます。 

## <a name="next-steps"></a>次の手順

* 正常性機能の使用方法については、[Azure Monitor VM の正常性の把握](vminsights-health.md)に関する記事を参照してください。 
* 検出されたアプリケーションの依存関係を表示するには、[Azure Monitor for VMs のマップの使用](vminsights-maps.md)に関する記事を参照してください。 
* VM のパフォーマンスでのボトルネックや全体的な使用率を特定するには、[Azure VM のパフォーマンスの表示](vminsights-performance.md)に関する記事を参照してください。