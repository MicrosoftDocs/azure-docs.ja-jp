---
title: Azure 仮想マシンを作成した後で WinRM を構成する | Microsoft Docs
description: Azure でホストされる仮想マシンを作成した後で Windows リモート管理 (WinRM) を構成する方法について説明します。
services: Azure, Marketplace, Cloud Partner Portal,
documentationcenter: ''
author: v-miclar
manager: Patrick.Butler
editor: ''
ms.assetid: ''
ms.service: marketplace
ms.workload: ''
ms.tgt_pltfrm: ''
ms.devlang: ''
ms.topic: conceptual
ms.date: 11/27/2018
ms.author: pbutlerm
ms.openlocfilehash: 6350d5bde737c46cf14a9aef75a7ec57260a6afa
ms.sourcegitcommit: 5b869779fb99d51c1c288bc7122429a3d22a0363
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/10/2018
ms.locfileid: "53196190"
---
# <a name="configure-winrm-after-virtual-machine-creation"></a>仮想マシンの作成後に WinRM を構成する

この記事では、Azure でホストされる既存の仮想マシン (VM) で HTTPS 経由の WinRM を有効にするよう構成する方法について説明します。  この構成は Windows ベースの VM にのみ適用され、次の 2 段階のプロセスが必要です。

1. HTTPS プロトコルを使用する WinRM へのポート トラフィックを有効にします。  VM に対するこの設定は Azure portal で構成します。
2. 提供された PowerShell スクリプトを実行して、WinRM を有効にするように VM を構成します。


## <a name="enabling-port-traffic"></a>ポートのトラフィックを有効にする

HTTPS プロトコル経由の WinRM では、ポート 5896 が使用されます。このポートは、Azure Marketplace で提供される事前構成済みの Windows VM では、既定では有効になっていません。 このプロトコルを有効にするには、[Azure portal](https://portal.azure.com) で次の手順を使用してネットワーク セキュリティ グループ (NSG) に新しい規則を追加します。  NSG について詳しくは、「[セキュリティ グループ](https://docs.microsoft.com/azure/virtual-network/security-overview)」をご覧ください。

1.  **[Virtual Machines] >**   <*VM 名*>  **> [Settings/Networking]\(設定/ネットワーク\)** ブレードに移動します。
2.  NSG の名前 (この例では **testvm11002**) をクリックして、そのプロパティを表示します。

    ![ネットワーク セキュリティ グループのプロパティ](./media/nsg-properties.png)
 
3. **[設定]** で **[受信セキュリティ規則]** を選択してこのブレードを表示します。
4. **[+ 追加]** をクリックして、TCP ポート 5986 用に `WinRM_HTTPS` という名前の新しいルールを作成します。

    ![受信ネットワーク セキュリティ規則を追加する](./media/nsg-new-rule.png)

5. 値の指定が終わったら、**[OK]** をクリックします。  受信セキュリティ規則の一覧に、次の新しいエントリが含まれるはずです。

    ![受信ネットワーク セキュリティ規則の一覧](./media/nsg-new-inbound-listing.png)


## <a name="configure-vm-to-enable-winrm"></a>VM を構成して WinRM を有効にする 

Windows VM で Windows リモート管理機能を有効にして構成するには、次の手順を使用します。   

1. Azure でホストされている VM にリモート デスクトップ接続を確立します。  詳しくは、「[Windows が実行されている Azure 仮想マシンに接続してログオンする方法](https://docs.microsoft.com/azure/virtual-machines/windows/connect-logon)」をご覧ください。  以降の手順は、お使いの VM で実行します。
2. 次のファイルをダウンロードして、VM 上のフォルダーに保存します。
    - [ConfigureWinRM.ps1](https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/201-vm-winrm-windows/ConfigureWinRM.ps1)
    - [makecert.exe](https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/201-vm-winrm-windows/makecert.exe)
    - [winrmconf.cmd](https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/201-vm-winrm-windows/winrmconf.cmd)
3. 昇格された特権 (**[管理者として実行]**) で、**PowerShell コンソール**を開きます。 
4. 必須のパラメーター (VM の完全修飾ドメイン名 (FQDN)) を指定して、次のコマンドを実行します。 <br/>
   `ConfigureWinRM.ps1 <vm-domain-name>`

    ![PowerShell 構成スクリプト 1](./media/powershell-file1.png)

    このスクリプトを使用するには、他の 2 つのファイルが同じフォルダーに存在する必要があります。


## <a name="next-steps"></a>次の手順

WinRM の構成が済むと、[その構成要素である VHD から VM をデプロイする](./cpp-deploy-vm-vhd.md)準備が整いました。
