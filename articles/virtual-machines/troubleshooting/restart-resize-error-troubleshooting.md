---
title: Azure での VM の再起動またはサイズ変更に関する問題 | Microsoft Docs
description: Azure での既存の仮想マシンの再起動またはサイズ変更に関する Resource Manager デプロイメントの問題のトラブルシューティング
services: virtual-machines
documentationcenter: ''
author: Deland-Han
manager: felixwu
editor: ''
tags: top-support-issue
ms.assetid: 0756b52d-4f5a-4503-ae45-c00a6a2edcdf
ms.service: virtual-machines
ms.topic: troubleshooting
ms.date: 06/15/2018
ms.author: delhan
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: f510a111a6c8846b300c09f368a3a2a05b2bb7ad
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/13/2019
ms.locfileid: "64719902"
---
# <a name="troubleshoot-deployment-issues-with-restarting-or-resizing-an-existing-windows-vm-in-azure"></a>Azure での既存の Windows VM の再起動またはサイズ変更に関するデプロイメントの問題のトラブルシューティング
停止している Azure 仮想マシン (VM) を起動しようとしたとき、または既存の Azure VM のサイズを変更しようとしたときに発生する一般的なエラーは割り当てエラーです。 このエラーは、クラスターまたはリージョンに使用可能なリソースがないか、要求された VM サイズをサポートできない場合に発生します。

[!INCLUDE [support-disclaimer](../../../includes/support-disclaimer.md)]

## <a name="collect-activity-logs"></a>アクティビティ ログを収集する
トラブルシューティングを開始するには、アクティビティ ログを収集して問題に関連するエラーを特定します。 このプロセスの詳細については、次のリンクをご覧ください。

[デプロイ操作の表示](../../azure-resource-manager/resource-manager-deployment-operations.md)

[アクティビティ ログを表示して Azure リソースを管理する](../../resource-group-audit.md)

## <a name="issue-error-when-starting-a-stopped-vm"></a>問題: 停止している VM の起動時のエラー
停止している VM を起動しようとしたときに、割り当てエラーが発生します。

### <a name="cause"></a>原因
停止している VM の起動要求は、クラウド サービスをホストしている元のクラスターで行う必要がありますが、 クラスターに要求の処理に使用できる空き領域がありません。

### <a name="resolution"></a>解決策
* 可用性セットのすべての VM を停止し、各 VM を再起動します。
  
  1. **[リソース グループ]**  > *対象のリソース グループ* >  **[リソース]**  > *対象の可用性セット* >  **[Virtual Machines]**  > *対象の仮想マシン* >  **[停止]** の順にクリックします。
  2. すべての VM が停止したら、停止している各 VM を選択し、[起動] をクリックします。
* 後で再起動要求を再試行します。

## <a name="issue-error-when-resizing-an-existing-vm"></a>問題: 既存の VM のサイズ変更時のエラー
既存の VM のサイズを変更しようとしたときに割り当てエラーが発生します。

### <a name="cause"></a>原因
VM のサイズ変更要求は、クラウド サービスをホストしている元のクラスターで行う必要がありますが、 クラスターが要求された VM サイズをサポートしていません。

### <a name="resolution"></a>解決策
* VM サイズを小さくして要求を再試行します。
* 要求した VM のサイズを変更できない場合は、次の手順を実行します。
  
  1. 可用性セットのすべての VM を停止します。
     
     * **[リソース グループ]**  > *対象のリソース グループ* >  **[リソース]**  > *対象の可用性セット* >  **[Virtual Machines]**  > *対象の仮想マシン* >  **[停止]** の順にクリックします。
  2. すべての VM が停止したら、目的の VM のサイズを大きなサイズに変更します。
  3. サイズ変更した VM を選択して **[起動]** をクリックした後、停止している各 VM を起動します。

## <a name="next-steps"></a>次の手順
Azure で新しい Windows VM を作成するときに問題が発生する場合は、[Azure での新しい Windows 仮想マシンの作成に関するデプロイメントの問題のトラブルシューティング](../windows/troubleshoot-deployment-new-vm.md)に関する記事を参照してください。

