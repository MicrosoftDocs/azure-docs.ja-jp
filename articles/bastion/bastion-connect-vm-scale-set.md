---
title: Azure Bastion を使用して Windows 仮想マシン スケール セットに接続する | Microsoft Docs
description: この記事では、Azure Bastion を使用して Azure 仮想マシン スケール セットに接続する方法について説明します。
services: bastion
author: charwen
ms.service: bastion
ms.topic: how-to
ms.date: 02/03/2020
ms.author: charwen
ms.openlocfilehash: e3dc7ce36e773b5a615b1abf4f50406fcb07826b
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/02/2020
ms.locfileid: "84744308"
---
# <a name="connect-to-a-virtual-machine-scale-set-using-azure-bastion"></a>Azure Bastion を使用して仮想マシン スケール セットに接続する

この記事では、Azure Bastion を使用して、Azure 仮想ネットワーク内の Windows 仮想マシン スケール セットのインスタンスに、安全かつシームレスに RDP 接続する方法について説明します。 仮想マシン スケール セットのインスタンスには、Azure portal から直接接続できます。 Azure Bastion を使用する場合、VM にクライアント、エージェント、追加のソフトウェアは不要です。 Azure Bastion の詳細については、[概要](bastion-overview.md)に関する記事をご覧ください。

## <a name="before-you-begin"></a>開始する前に

仮想マシン スケール セットが存在する仮想ネットワークに対して Azure Bastion ホストが設定されていることを確認します。 詳細については、[Azure Bastion ホストの作成](bastion-create-host-portal.md)に関する記事をご覧ください。 Bastion サービスをプロビジョニングし、仮想ネットワークにデプロイしたら、それを使用してこの仮想ネットワーク内の任意の仮想マシン スケール セット インスタンスに接続できます。 Bastion では、Windows 仮想マシン スケール セットへの接続には RDP を使用し、Linux 仮想マシン スケール セットへの接続には SSH を使用していることを前提としています。 Linux VM への接続については、[VM への接続 (Linux)](bastion-connect-vm-ssh.md) に関する記事をご覧ください。

## <a name="connect-using-rdp"></a><a name="rdp"></a>RDP を使用して接続する

1. [Azure Portal](https://portal.azure.com)を開きます。 接続先の仮想マシン スケール セットに移動します。

   ![移動](./media/bastion-connect-vm-scale-set/1.png)
2. 接続先の仮想マシン スケール セットのインスタンスに移動し、 **[接続]** を選択します。 RDP 接続を使用するときは、仮想マシン スケール セットは Windows 仮想マシン スケール セットである必要があります。

   ![仮想マシン スケール セット](./media/bastion-connect-vm-scale-set/2.png)
3. **[接続]** を選択すると、RDP、SSH、Bastion の 3 つのタブがあるサイド バーが表示されます。 サイド バーの **[Bastion]** タブを選択します。 Bastion を仮想ネットワーク用にプロビジョニングしていない場合は、リンクを選択して Bastion を構成できます。 構成手順については、[Bastion の構成](bastion-create-host-portal.md)に関する記事をご覧ください。

   ![[Bastion] タブ](./media/bastion-connect-vm-scale-set/3.png)
4. [Bastion] タブで、仮想マシン スケール セットのユーザー名とパスワードを入力し、 **[接続]** を選択します。

   ![接続する](./media/bastion-connect-vm-scale-set/4.png)
5. Bastion を使用したこの仮想マシンへの RDP 接続は、ポート 443 と Bastion サービスを使用して (HTML5 を介して) Azure portal で直接開きます。

## <a name="next-steps"></a>次のステップ

[Azure Bastion に関する FAQ](bastion-faq.md) を読む。