---
title: Azure Virtual Machines の状態と課金
description: VM のさまざまな状態と、ユーザーが課金されるタイミングの概要。
services: virtual-machines
author: mimckitt
ms.service: virtual-machines
ms.subservice: billing
ms.topic: conceptual
ms.date: 03/8/2021
ms.author: mimckitt
ms.reviewer: cynthn
ms.openlocfilehash: c206cba3b23a0bf41ce32481980aa466e869bcde
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "104596299"
---
# <a name="states-and-billing-of-azure-virtual-machines"></a>Azure Virtual Machines の状態と課金

Azure Virtual Machines (VM) はさまざまな状態に変化し、それらは "*プロビジョニング状態*" と "*電源の状態*" に分類することができます。 この記事の目的は、各状態について説明し、ユーザーがインスタンスの使用料金を課金されるタイミングを具体的に明示することです。 

## <a name="get-states-using-instance-view"></a>インスタンス ビューを使用して状態を取得する

インスタンス ビュー API によって、VM の実行状態に関する情報が提供されます。 詳細については、「[Virtual Machines - Instance View](/rest/api/compute/virtualmachines/instanceview)」(仮想マシン - インスタンス ビュー) の API ドキュメントをご覧ください。

Azure リソース エクスプローラーは、VM の実行状態を表示するためのシンプルな UI を備えています ([リソース エクスプローラー](https://resources.azure.com/))。

プロビジョニング状態は、VM のプロパティとインスタンス ビューから確認できます。 電源の状態は、VM のインスタンス ビューから確認できます。

自分のサブスクリプション内のすべての VM の電源状態を取得するには、**statusOnly** パラメーターを *true* に設定して [Virtual Machines の List All API](/rest/api/compute/virtualmachines/listall) を使用します。

> [!NOTE]
> [Virtual Machines の List All API](/rest/api/compute/virtualmachines/listall) を、**statusOnly** パラメーターを true に設定して使用した場合、サブスクリプション内のすべての VM の電源状態を取得できます。 ただし、取得プロセスでの一時的な問題のために、まれに電源状態を取得できない場合があります。 このような場合は、同じ API を使用するか、[Azure Resource Health](../service-health/resource-health-overview.md) または [Azure Resource Graph](..//governance/resource-graph/overview.md) を使用して再試行し、お使いの VM の電源状態を確認することをお勧めします。
 
## <a name="power-states-and-billing"></a>電源の状態と課金

電源の状態は、最後に検出された VM の状態を表します。

:::image type="content" source="./media/virtual-machines-common-states-lifecycle/vm-power-states.png" alt-text="VM が取り得る電源状態を示す図。":::

インスタンスの各状態の説明と、インスタンスの使用料金を課金されるかどうかを、次の表に示します。

| 電源状態 | 説明 | 課金 |  
|---|---|---|
| 開始中| 仮想マシンは起動中です。 |課金されません* | 
| 実行中 | 仮想マシンは完全に起動しました。 これが標準の動作状態です。 | 課金されます | 
| 停止中 | これは、実行中と停止間で遷移している状態です。 | 課金されます| 
|停止済み | Virtual Machine は、ゲスト OS から、または PowerOff API を使用してシャットダウンされました。 この状態では、仮想マシンで、まだ基になるハードウェアがリースされています。 この状態は、*停止 (割り当て済み)* とも呼ばれます。 | 課金されます | 
| 割り当て解除中 | これは、実行中と割り当て解除間で遷移している状態です。 | 課金されません* | 
| 割り当て解除済み | Virtual Machine は、基になるハードウェアに対するリースから解放され、完全に電源が切られました。 この状態は、*停止 (割り当て解除)* とも呼ばれます。 | 課金されません* | 

&#42; Azure の[ディスク](https://azure.microsoft.com/pricing/details/managed-disks)や[ネットワーク](https://azure.microsoft.com/pricing/details/bandwidth/)などの一部のリソースは引き続き課金されます。


## <a name="provisioning-states"></a>プロビジョニング状態

プロビジョニング状態とは、VM 上でユーザーが開始したコントロールプレーン操作の状態のことです。 これらの状態は、VM の電源の状態から独立しています。

:::image type="content" source="./media/virtual-machines-common-states-lifecycle/vm-provisioning-states.png" alt-text="VM が取り得るプロビジョニングの状態を示す図。":::

| プロビジョニングの状態 | 説明 | 電源状態 | 課金 | 
|---|---|---|---|
| 作成 | 仮想マシンを作成しています。 | 開始中 | 課金されません* | 
| 更新 | 既存の仮想マシンのモデルを更新しています。 起動や再起動などのモデル変更ではない一部の変更は、更新の状態に分類されます。 | 実行中 | 課金されます | 
| 削除 | 仮想マシンを削除しています。 | 割り当て解除中 | 課金されません* |
| 割り当て解除 | 仮想マシンは完全に停止され、基になるホストから削除されています。 仮想マシンの割り当て解除は更新と見なされ、更新と似たプロビジョニングの状態が表示されます。 | 割り当て解除中 | 課金されません* | 

&#42; Azure の[ディスク](https://azure.microsoft.com/pricing/details/managed-disks)や[ネットワーク](https://azure.microsoft.com/pricing/details/bandwidth/)などの一部のリソースは引き続き課金されます。

## <a name="os-provisioning-states"></a>OS のプロビジョニング状態
OS のプロビジョニング状態は、OS イメージを使用して作成された仮想マシンにのみ該当します。 特殊化されたイメージでは、これらの状態は表示されません。 

:::image type="content" source="./media/virtual-machines-common-states-lifecycle/os-provisioning-states.png" alt-text="VM が取り得る OS のプロビジョニング状態を示す図。":::

| OS のプロビジョニング状態 | 説明 | 電源状態 | 課金 | 
|---|---|---|---|
| OSProvisioningInProgress | VM は実行中で、ゲスト OS のインストールが進行しています。 | 実行中 | 課金されます | 
| OSProvisioningComplete | これは短期の状態です。 仮想マシンは、この状態からすぐに **成功** に遷移します。 拡張機能がまだインストール中の場合は、完了するまでこの状態は表示され続けます。 | 実行中 | 課金されます | 
| 成功 | ユーザーが開始したアクションが完了しました。 | 実行中 | 課金されます | 
| 失敗 | 操作が失敗したことを表します。 詳細と考えられる解決策は、エラー コードを参照してください。 | 実行中  | 課金されます | 


## <a name="next-steps"></a>次のステップ
- [Azure Cost Management および Billing のドキュメント](../cost-management-billing/index.yml)を参照してください。
- 自分のデプロイ計画には、[Azure の料金計算ツール](https://azure.microsoft.com/pricing/calculator/)を使用してください。
- お使いの VM の監視の詳細については、[Azure で仮想マシンを監視する方法](../azure-monitor/vm/monitor-vm-azure.md)に関するページを参照してください。