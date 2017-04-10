---
title: "Azure VM の監視を有効または無効にする"
description: "Azure VM の監視を有効または無効にする方法を説明します"
services: virtual-machines-linux
documentationcenter: virtual-machines
author: kmouss
manager: timlt
editor: 
ms.assetid: 6ce366d2-bd4c-4fef-a8f5-a3ae2374abcc
ms.service: virtual-machines-linux
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 02/08/2016
ms.author: kmouss
translationtype: Human Translation
ms.sourcegitcommit: 356de369ec5409e8e6e51a286a20af70a9420193
ms.openlocfilehash: 1f15a77304ccf4fdef995d216817363fbf3f6e73
ms.lasthandoff: 03/27/2017


---
# <a name="enable-or-disable-azure-vm-monitoring"></a>Azure VM の監視を有効または無効にする
このセクションでは、Azure で動作する仮想マシンの監視を有効または無効にする方法について説明します。 既定により、 [Azure ポータル](https://portal.azure.com) からデプロイされた Azure 仮想マシンでは監視が有効になり、監視グラフが 1 分間隔で提供されます。 ポータルまたは Mac、Linux、Windows (Azure CLI) の Azure コマンドライン インターフェイスを使用して、監視を有効または無効にできます。 

## <a name="enable--disable-monitoring-through-the-azure-portal"></a>Azure ポータルで監視を有効/無効にする
Azure VM の監視を有効にできます。インスタンスに関するデータが 1 分間隔で提供されます  (ストレージの変更が適用されます)。 VM の詳細な診断データを、ポータル グラフまたは API で利用できます。 Azure ポータルでは監視が既定で有効になりますが、以下で説明するようにオフにすることができます。 VM が実行中でも停止状態のときでも、監視を有効にできます。

* Azure Portal (**[https://portal.azure.com](https://portal.azure.com)**) を開きます。
* 左側のナビゲーションで、[仮想マシン] をクリックします。
* 仮想マシンのリストで、実行中または停止中のインスタンスを選択します。 [仮想マシン] ブレードが開きます。
* [すべての設定] をクリックします。
* [診断] をクリックします。
* ステータスをオンまたはオフに変更します。 このブレードでは、仮想マシンに対して有効にする監視の詳細レベルを選択することもできます。

[Azure.Note] 新しい仮想マシンを作成すると、診断は既定でオンになります。

![Azure ポータルで監視を有効または無効にします。][1]

## <a name="enable--disable-monitoring-with-azure-cli"></a>Azure CLI で監視を有効/無効にする
Azure VM の監視を有効にするには:

* 次のような内容で、PrivateConfig.json といった名前のファイルを作成します。
        { "ストレージ アカウント名":"データを受信するストレージ アカウント", "ストレージ アカウント キー":"アカウントのキー" }
* 次の Azure CLI コマンドを実行します。
  
        azure vm extension set myvm LinuxDiagnostic Microsoft.OSTCExtensions 2.0 --private-config-path PrivateConfig.json

[Azure.Note] 使用可能な場合は、バージョン 2.0 からそれより後のバージョンに変更できます。 

監視メトリックの構成の詳細とサンプルについては、「[Linux の診断拡張機能を使用した Linux VM のパフォーマンスと診断データの監視](linux/classic/diagnostic-extension.md?toc=%2fazure%2fvirtual-machines%2flinux%2fclassic%2ftoc.json)」を参照してください。

<!--Image references-->
[1]: ./media/virtual-machines-linux-vm-monitoring/portal-enable-disable.png



