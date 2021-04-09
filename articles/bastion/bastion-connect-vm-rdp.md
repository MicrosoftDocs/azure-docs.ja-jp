---
title: Azure Bastion を使用して Windows VM に接続する
description: この記事では、Azure Bastion を使用して、Windows を実行している Azure 仮想マシンに接続する方法について説明します。
services: bastion
author: cherylmc
ms.service: bastion
ms.topic: how-to
ms.date: 10/21/2020
ms.author: cherylmc
ms.openlocfilehash: 708bd1f61da2f3973333f8e68cabdceee0717bee
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "92521538"
---
# <a name="connect-to-a-windows-virtual-machine-using-azure-bastion"></a>Azure Bastion を使用して Windows 仮想マシンに接続する

Azure Bastion を使用すると、Azure portal で直接、SSL を介して仮想マシンに安全かつシームレスに接続できます。 Azure Bastion を使用する場合、VM にクライアント、エージェント、追加のソフトウェアは不要です。 この記事では、Windows VM に接続する方法について説明します。 Linux VM への接続については、[Linux VM への接続](bastion-connect-vm-ssh.md)に関する記事を参照してください。

Azure Bastion は、プロビジョニングされる仮想ネットワーク内のすべての VM に対して安全な接続を提供します。 Azure Bastion を使用すると、RDP または SSH を使用した安全なアクセスを提供しながら、お使いの仮想マシンが RDP または SSH ポートを外部に公開しないように保護されます。 詳細については、「[Azure Bastion とは](bastion-overview.md)」を参照してください。

## <a name="prerequisites"></a>前提条件

開始する前に、次の条件を満たしていることを確認します。

* Bastion ホストが既にインストールされている VNet。

   VM が置かれている仮想ネットワークの Azure Bastion ホストが設定されていることを確認します。 Bastion サービスをプロビジョニングし、仮想ネットワークにデプロイしたら、それを使用してこの仮想ネットワーク内の任意の VM に接続できます。 Azure Bastion ホストを設定するには、「[Bastion ホストの作成](tutorial-create-host-portal.md#createhost)」を参照してください。
* 仮想ネットワーク内の Windows 仮想マシン。
* 次の必須ロール:
  * 仮想マシンに対する閲覧者ロール。
  * 仮想マシンのプライベート IP を使用する NIC に対する閲覧者ロール。
  * Azure Bastion リソースに対する閲覧者ロール。
* ポート: Windows VM に接続するには、お使いの Windows VM で次のポートを開いておく必要があります。
  * 受信ポート:RDP (3389)

## <a name="connect"></a><a name="rdp"></a>接続

[!INCLUDE [Connect to a Windows VM](../../includes/bastion-vm-rdp.md)]
 
## <a name="next-steps"></a>次のステップ

[Azure Bastion に関する FAQ](bastion-faq.md) を読む。