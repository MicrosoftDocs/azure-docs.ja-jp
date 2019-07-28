---
title: Linux VM デプロイのトラブルシューティング | Microsoft Docs
description: Azure で新しい Linux 仮想マシンを作成するときに発生する Resource Manager デプロイメントの問題のトラブルシューティング
services: virtual-machines-linux, azure-resource-manager
documentationcenter: ''
author: jiangchen79
manager: gwallace
editor: ''
tags: top-support-issue, azure-resource-manager
ms.assetid: 906a9c89-6866-496b-b4a4-f07fb39f990c
ms.service: virtual-machines-linux
ms.workload: na
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: troubleshooting
ms.date: 09/09/2016
ms.author: cjiang
ms.openlocfilehash: dd6241f75311ea956b204b5181ae17db0ef3faa1
ms.sourcegitcommit: c105ccb7cfae6ee87f50f099a1c035623a2e239b
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/09/2019
ms.locfileid: "67710486"
---
# <a name="troubleshoot-resource-manager-deployment-issues-with-creating-a-new-linux-virtual-machine-in-azure"></a>Azure での新しい Linux 仮想マシンの作成に関する Resource Manager デプロイメントの問題のトラブルシューティング
[!INCLUDE [virtual-machines-troubleshoot-deployment-new-vm-opening](../../../includes/virtual-machines-troubleshoot-deployment-new-vm-opening-include.md)]

[!INCLUDE [support-disclaimer](../../../includes/support-disclaimer.md)]

## <a name="top-issues"></a>代表的な問題
[!INCLUDE [support-disclaimer](../../../includes/virtual-machines-linux-troubleshoot-deploy-vm-top.md)]

VM デプロイに関するその他の問題や質問については、「[Troubleshoot deploying Linux virtual machine issues in Azure](troubleshoot-deploy-vm-linux.md) (Azure での Linux 仮想マシンのデプロイに関する問題のトラブルシューティング)」を参照してください。

## <a name="collect-activity-logs"></a>アクティビティ ログを収集する
トラブルシューティングを開始するには、アクティビティ ログを収集して問題に関連するエラーを特定します。 このプロセスの詳細については、次のリンクをご覧ください。

[デプロイ操作の表示](../../azure-resource-manager/resource-manager-deployment-operations.md)

[アクティビティ ログを表示して Azure リソースを管理する](../../resource-group-audit.md)

[!INCLUDE [virtual-machines-troubleshoot-deployment-new-vm-issue1](../../../includes/virtual-machines-troubleshoot-deployment-new-vm-issue1-include.md)]

[!INCLUDE [virtual-machines-linux-troubleshoot-deployment-new-vm-table](../../../includes/virtual-machines-linux-troubleshoot-deployment-new-vm-table.md)]

**Y:** OS が一般化された Linux であり、一般化された設定でアップロード/キャプチャされた場合、エラーは発生しません。 同様に、OS が特殊化された Linux であり、特殊化された設定でアップロード/キャプチャされた場合、エラーは発生しません。

**アップロード エラー:**

**N<sup>1</sup>:** OS が一般化された Linux であり、特殊化された Linux としてアップロードされた場合、プロビジョニングの段階で VM がスタックされるため、プロビジョニング タイムアウト エラーが発生します。

**N<sup>2</sup>:** OS が特殊化された Linux であり、一般化された Linux としてアップロードされた場合、新しい VM は元のコンピューター名、ユーザー名、パスワードを使用して実行されるため、プロビジョニング エラー (プロビジョニング失敗) が発生します。

**解決策:**

これらのエラーを解決するには、OS と同じ設定 (一般化/特殊化) を使用して、オンプレミスで使用可能な元の VHD をアップロードします。 一般化された OS としてアップロードするには、まずプロビジョニング解除を必ず実行してください。

**キャプチャ エラー:**

**N<sup>3</sup>:** OS が一般化された Linux であり、特殊化された Linux としてキャプチャされた場合、一般化された Linux としてマークされた元の VM を使用できないため、プロビジョニング タイムアウト エラーが発生します。

**N<sup>4</sup>:** OS が特殊化された Linux であり、一般化された Linux としてキャプチャされた場合、新しい VM は元のコンピューター名、ユーザー名、パスワードを使用して実行されるため、プロビジョニング エラー (プロビジョニング失敗) が発生します。 また、元の VM は特殊化としてマークされているので使用できません。

**解決策:**

これらのエラーの両方を解決するには、ポータルから現在のイメージを削除し、OS と同じ設定 (一般化/特殊化) で[現在の VHD からイメージをキャプチャし直します](../linux/capture-image.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)。

## <a name="issue-custom-gallery-marketplace-image-allocation-failure"></a>問題: カスタム/ ギャラリー/ マーケットプレース イメージ - 割り当てエラー
このエラーは、新しい VM 要求が、要求されている VM サイズをサポートできないか、要求に対応するための使用可能な空き領域がないクラスターに固定されている場合に発生します。

**原因 1:** クラスターが要求された VM サイズをサポートできない。

**解決策 1:**

* VM サイズを小さくして要求を再試行します。
* 要求した VM のサイズを変更できない場合は、次の手順を実行します。
  * 可用性セットのすべての VM を停止します。
    **[リソース グループ]**  > *対象のリソース グループ* >  **[リソース]**  > *対象の可用性セット* >  **[Virtual Machines]**  > *対象の仮想マシン* >  **[停止]** の順にクリックします。
  * すべての VM が停止したら、目的のサイズで新しい VM を作成します。
  * 新しい VM を起動してから、停止している各 VM を選択し、 **[起動]** をクリックします。

**原因 2:** クラスターに空きリソースがない。

**解決策 2:**

* 後で要求を再試行します。
* 新しい VM を別の可用性セットに含めることができる場合
  * 新しい VM を (同じリージョンの) 別の可用性セットに作成します。
  * 新しい VM を同じ仮想ネットワークに追加します。

## <a name="next-steps"></a>次の手順
Azure で停止していた Linux VM の再起動または既存の Linux VM のサイズ変更に問題が発生する場合は、「 [Azure での既存の Linux 仮想マシンの再起動またはサイズ変更に関する Resource Manager デプロイメントの問題のトラブルシューティング](../linux/restart-resize-error-troubleshooting.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)」を参照してください。

