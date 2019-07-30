---
title: Azure Bastion を使用して Windows VM に接続する | Microsoft Docs
description: この記事では、Azure Bastion を使用して、Windows を実行している Azure 仮想マシンに接続する方法について説明します。
services: bastion
author: cherylmc
ms.service: bastion
ms.topic: conceptual
ms.date: 06/03/2019
ms.author: cherylmc
ms.openlocfilehash: 376b7042a513dd50647dc8f88bf1de70f65bb21c
ms.sourcegitcommit: f811238c0d732deb1f0892fe7a20a26c993bc4fc
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/29/2019
ms.locfileid: "67478403"
---
# <a name="connect-to-a-windows-virtual-machine-using-azure-bastion-preview"></a>Azure Bastion を使用して Windows 仮想マシンに接続する (プレビュー)

この記事では、Azure Bastion を使用して、Azure 仮想ネットワーク内の Windows VM に安全かつシームレスに RDP 接続する方法について説明します。 VM には Azure portal から直接接続できます。 Azure Bastion を使用する場合、VM にクライアント、エージェント、追加のソフトウェアは不要です。 Azure Bastion の詳細については、[概要](bastion-overview.md)に関する記事をご覧ください。

> [!IMPORTANT]
> このパブリック プレビュー版はサービス レベル アグリーメントなしで提供されています。運用環境のワークロードに使用することは避けてください。 特定の機能はサポート対象ではなく、機能が制限されることがあるか、Azure の場所によっては利用できない場合があります。 詳しくは、「[Microsoft Azure プレビューの追加使用条件](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)」をご覧ください。
>

## <a name="before-you-begin"></a>開始する前に

VM が存在する仮想ネットワークの Azure Bastion ホストが設定されていることを確認します。 詳細については、[Azure Bastion ホストの作成](bastion-create-host-portal.md)に関する記事をご覧ください。 Bastion サービスをプロビジョニングし、仮想ネットワークにデプロイしたら、それを使用してこの仮想ネットワーク内の任意の VM に接続できます。 このプレビューの Bastion では、Windows VM への接続には RDP を使用し、Linux VM への接続には SSH を使用していることを前提としています。 Linux VM への接続については、[VM への接続 (Linux)](bastion-connect-vm-ssh.md) に関する記事をご覧ください。

接続を作成するには、次のロールが必要です。

* 仮想マシンに対する閲覧者ロール
* 仮想マシンのプライベート IP を使用する NIC に対する閲覧者ロール
* Azure Bastion リソースに対する閲覧者ロール

## <a name="rdp"></a>RDP を使用して接続する

1. [このリンク](https://aka.ms/BastionHost)を使用して、Azure Bastion のプレビュー ポータル ページを開きます。 接続先の仮想マシンに移動し、 **[接続]** をクリックします。 RDP 接続を使用する場合、VM は Windows 仮想マシンである必要があります。

    ![VM への接続](./media/bastion-connect-vm-rdp/connect.png)

1. [接続] をクリックすると、RDP、SSH、Bastion の 3 つのタブがあるサイド バーが表示されます。 Bastion が仮想ネットワーク用にプロビジョニングされている場合、[Bastion] タブが既定でアクティブになっています。 Bastion を仮想ネットワーク用にプロビジョニングしていない場合は、リンクをクリックして Bastion を構成できます。 構成手順については、[Bastion の構成](bastion-create-host-portal.md)に関する記事をご覧ください。 **[Bastion]** が表示されない場合は、プレビュー ポータルが開かれていません。 [このプレビュー リンク](https://aka.ms/BastionHost)を使用してポータルを開いてください。

    ![VM への接続](./media/bastion-connect-vm-rdp/bastion.png)

1. [Bastion] タブで、仮想マシンのユーザー名とパスワードを入力し、 **[接続]** をクリックします。 Bastion を使用したこの仮想マシンへの RDP 接続は、ポート 443 と Bastion サービスを使用して (HTML5 を介して) Azure portal で直接開きます。

    ![VM への接続](./media/bastion-connect-vm-rdp/443rdp.png)
 
## <a name="next-steps"></a>次の手順

[Bastion に関する FAQ](bastion-faq.md) を読む
