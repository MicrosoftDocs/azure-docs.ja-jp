---
title: "VM の再起動またはサイズ変更に関する問題 | Microsoft Docs"
description: "Azure での既存の Linux 仮想マシンの再起動またはサイズ変更に関するクラシック デプロイメントの問題のトラブルシューティング"
services: virtual-machines-linux
documentationcenter: 
author: Deland-Han
manager: felixwu
editor: 
tags: top-support-issue
ms.assetid: 73f2672c-602e-4766-8948-2b180115d299
ms.service: virtual-machines-linux
ms.topic: support-article
ms.tgt_pltfrm: vm-linux
ms.workload: required
ms.date: 01/10/2017
ms.devlang: na
ms.author: delhan
translationtype: Human Translation
ms.sourcegitcommit: 356de369ec5409e8e6e51a286a20af70a9420193
ms.openlocfilehash: 466db1525d3f1a9bdde86643089493133f32bad1
ms.lasthandoff: 03/27/2017


---
# <a name="troubleshoot-classic-deployment-issues-with-restarting-or-resizing-an-existing-linux-virtual-machine-in-azure"></a>Azure での既存の Linux 仮想マシンの再起動またはサイズ変更に関するクラシック デプロイメントの問題のトラブルシューティング
> [!div class="op_single_selector"]
> * [クラシック](restart-resize-error-troubleshooting.md)
> * [Resource Manager](../../virtual-machines-linux-restart-resize-error-troubleshooting.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
> 
> 

停止している Azure 仮想マシン (VM) を起動しようとしたとき、または既存の Azure VM のサイズを変更しようとしたときに発生する一般的なエラーは割り当てエラーです。 このエラーは、クラスターまたはリージョンに使用可能なリソースがないか、要求された VM サイズをサポートできない場合に発生します。

> [!IMPORTANT] 
> Azure には、リソースの作成と操作に関して、 [Resource Manager とクラシック](../../../resource-manager-deployment-model.md)の 2 種類のデプロイメント モデルがあります。 この記事では、クラシック デプロイ モデルの使用方法について説明します。 最新のデプロイでは、リソース マネージャー モデルを使用することをお勧めします。 Resource Manager バージョンについては、[こちら](../../virtual-machines-linux-restart-resize-error-troubleshooting.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)をご覧ください。

[!INCLUDE [support-disclaimer](../../../../includes/support-disclaimer.md)]

## <a name="collect-audit-logs"></a>監査ログの収集
トラブルシューティングを開始するには、監査ログを収集して問題に関連するエラーを特定します。

Azure ポータルで、**[参照]** > **[仮想マシン]** > *対象の Linux 仮想マシン* > **[設定]** > **[監査ログ]** をクリックします。

## <a name="issue-error-when-starting-a-stopped-vm"></a>問題: 停止している VM の起動時のエラー
停止している VM を起動しようとしたときに、割り当てエラーが発生します。

### <a name="cause"></a>原因
停止している VM の起動要求は、クラウド サービスをホストしている元のクラスターで行う必要がありますが、 クラスターに要求の処理に使用できる空き領域がありません。

### <a name="resolution"></a>解決策
* 新しいクラウド サービスを作成し、アフィニティ グループではなく、リージョンまたはリージョン ベースの仮想ネットワークに関連付けます。
* 停止している VM を削除します。
* ディスクを使用して、新しいクラウド サービスに VM を再作成します。
* 再作成された VM を起動します。

新しいクラウド サービスを作成しようとしたときにエラーが発生した場合は、後で再試行するか、クラウド サービスのリージョンを変更します。

> [!IMPORTANT]
> 新しいクラウド サービスには、新しい名前と VIP が割り当てられるので、既存のクラウド サービスでこの情報を使用しているすべての依存関係について、この情報を変更する必要があります。
> 
> 

## <a name="issue-error-when-resizing-an-existing-vm"></a>問題: 既存の VM のサイズ変更時のエラー
既存の VM のサイズを変更しようとしたときに割り当てエラーが発生します。

### <a name="cause"></a>原因
VM のサイズ変更要求は、クラウド サービスをホストしている元のクラスターで行う必要がありますが、 クラスターが要求された VM サイズをサポートしていません。

### <a name="resolution"></a>解決策
要求する VM サイズを小さくし、サイズ変更要求を再試行します。

* **[すべて参照]** > **[仮想マシン (クラシック)]** > *お使いの仮想マシン* > **[設定]** > **[サイズ]** の順にクリックします。 詳細な手順については、「 [Resize the virtual machine (仮想マシンのサイズの変更)](https://msdn.microsoft.com/library/dn168976.aspx)」を参照してください。

VM サイズを小さくできない場合は、次の手順に従います。

* アフィニティ グループにリンクしたり、アフィニティ グループにリンクされた仮想ネットワークに関連付けたりせずに、新しいクラウド サービスを作成します。
* 作成したクラウド サービスにサイズの大きい新しい VM を作成します。

同じクラウド サービス内のすべての VM を統合できます。 既存のクラウド サービスがリージョン ベースの仮想ネットワークに関連付けられている場合は、新しいクラウド サービスを既存の仮想ネットワークに接続できます。

既存のクラウド サービスがリージョン ベースの仮想ネットワークに関連付けられていない場合は、既存のクラウド サービス内の VM を削除し、ディスクから新しいクラウド サービスに VM を再作成する必要があります。 ただし、新しいクラウド サービスには新しい名前と VIP が割り当てられるので、既存のクラウド サービスでこれらの情報を現在使用しているすべての依存関係について、情報を更新する必要があります。

## <a name="next-steps"></a>次のステップ
Azure で新しい Linux VM を作成するときに問題が発生する場合は、[Azure での新しい Linux 仮想マシンの作成に関するデプロイメントの問題のトラブルシューティング](../../virtual-machines-linux-troubleshoot-deployment-new-vm.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)に関するページを参照してください。


