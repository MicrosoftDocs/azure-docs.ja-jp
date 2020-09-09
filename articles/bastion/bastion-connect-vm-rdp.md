---
title: Azure Bastion を使用して Windows VM に接続する
description: この記事では、Azure Bastion を使用して、Windows を実行している Azure 仮想マシンに接続する方法について説明します。
services: bastion
author: cherylmc
ms.service: bastion
ms.topic: how-to
ms.date: 02/24/2020
ms.author: cherylmc
ms.openlocfilehash: 79eb09a005f62846fc2f7e3e7b493d5e366edabc
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/02/2020
ms.locfileid: "84744325"
---
# <a name="connect-to-a-windows-virtual-machine-using-azure-bastion"></a>Azure Bastion を使用して Windows 仮想マシンに接続する

Azure Bastion を使用すると、Azure portal で直接、SSL を介して仮想マシンに安全かつシームレスに接続できます。 Azure Bastion を使用する場合、VM にクライアント、エージェント、追加のソフトウェアは不要です。 この記事では、Windows VM に接続する方法について説明します。 Linux VM への接続については、[Azure Bastion を使用した VM への接続 (Linux)](bastion-connect-vm-ssh.md) に関する記事をご覧ください。

Azure Bastion は、プロビジョニングされる仮想ネットワーク内のすべての VM に対して安全な接続を提供します。 Azure Bastion を使用すると、RDP または SSH を使用した安全なアクセスを提供しながら、お使いの仮想マシンが RDP または SSH ポートを外部に公開しないように保護されます。 詳細については、[概要](bastion-overview.md)に関するページを参照してください。

## <a name="before-you-begin"></a>開始する前に

VM が置かれている仮想ネットワークの Azure Bastion ホストが設定されていることを確認します。 Bastion サービスをプロビジョニングし、仮想ネットワークにデプロイしたら、それを使用してこの仮想ネットワーク内の任意の VM に接続できます。 Azure Bastion ホストを設定するには、[「Azure Bastion ホストを作成する」](bastion-create-host-portal.md)を参照してください。

### <a name="required-roles"></a>必要なロール

接続を作成するには、次のロールが必要です。

* 仮想マシンに対する閲覧者ロール
* 仮想マシンのプライベート IP を使用する NIC に対する閲覧者ロール
* Azure Bastion リソースに対する閲覧者ロール

### <a name="ports"></a>Port

Windows VM に接続するには、お使いの Windows VM で次のポートを開いておく必要があります。

* 受信ポート:RDP (3389)

## <a name="connect"></a><a name="rdp"></a>接続

1. [Azure Portal](https://portal.azure.com)を開きます。 接続先の仮想マシンに移動して **[接続]** をクリックし、ドロップダウンから **[Bastion]** をクリックします。

   ![VM への接続](./media/bastion-connect-vm-rdp/connect.png)
1. [Bastion] をクリックすると、RDP、SSH、Bastion の 3 つのタブがあるサイド バーが表示されます。 Bastion が仮想ネットワーク用にプロビジョニングされている場合、[Bastion] タブが既定でアクティブになっています。 Bastion を仮想ネットワーク用にプロビジョニングしていない場合は、リンクをクリックして Bastion を構成できます。 構成手順については、[Bastion の構成](bastion-create-host-portal.md)に関する記事をご覧ください。

   ![[Bastion] タブ](./media/bastion-connect-vm-rdp/bastion.png)
1. [Bastion] タブで、仮想マシンのユーザー名とパスワードを入力し、 **[接続]** をクリックします。 Bastion を使用したこの仮想マシンへの RDP 接続は、ポート 443 と Bastion サービスを使用して (HTML5 を介して) Azure portal で直接開きます。

   ![RDP 接続](./media/bastion-connect-vm-rdp/443rdp.png)
 
## <a name="next-steps"></a>次のステップ

[Bastion に関する FAQ](bastion-faq.md) を読む
