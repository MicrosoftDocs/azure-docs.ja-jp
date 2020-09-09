---
title: Azure Bastion を使用して Linux VM に接続する
description: この記事では、Azure Bastion を使用して Linux 仮想マシンに接続する方法について説明します。
services: bastion
author: cherylmc
ms.service: bastion
ms.topic: how-to
ms.date: 02/24/2020
ms.author: cherylmc
ms.openlocfilehash: 98f1f3d02d8433dd88fed844a587a961ea9f8c82
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/02/2020
ms.locfileid: "84744291"
---
# <a name="connect-using-ssh-to-a-linux-virtual-machine-using-azure-bastion"></a>Azure Bastion を使用して Linux 仮想マシンに SSH 接続する

この記事では、Azure 仮想ネットワーク内の Linux VM に安全かつシームレスに SSH 接続する方法について説明します。 VM には Azure portal から直接接続できます。 Azure Bastion を使用する場合、VM にクライアント、エージェント、追加のソフトウェアは不要です。 Azure Bastion の詳細については、[概要](bastion-overview.md)に関する記事をご覧ください。

Azure Bastion を使用して、Linux 仮想マシンに SSH 接続できます。 認証には、ユーザー名/パスワードと SSH キーの両方を使用できます。 SSH キーを使用して VM に接続するには、次のいずれかを使用します。

* 手動で入力した秘密キー
* 秘密キーの情報を含むファイル

SSH 秘密キーは、`"-----BEGIN RSA PRIVATE KEY-----"` で始まり、`"-----END RSA PRIVATE KEY-----"` で終わる形式である必要があります。

## <a name="before-you-begin"></a>開始する前に

VM が存在する仮想ネットワークの Azure Bastion ホストが設定されていることを確認します。 詳細については、[Azure Bastion ホストの作成](bastion-create-host-portal.md)に関する記事をご覧ください。 Bastion サービスをプロビジョニングし、仮想ネットワークにデプロイしたら、それを使用してこの仮想ネットワーク内の任意の VM に接続できます。 

Bastion を使用して接続する場合、Windows VM への接続には RDP を使用し、Linux VM への接続には SSH を使用していることを前提としています。 Windows VM への接続については、[VM への接続 (Windows)](bastion-connect-vm-rdp.md) に関する記事をご覧ください。

### <a name="required-roles"></a>必要なロール

接続を作成するには、次のロールが必要です。

* 仮想マシンに対する閲覧者ロール
* 仮想マシンのプライベート IP を使用する NIC に対する閲覧者ロール
* Azure Bastion リソースに対する閲覧者ロール

### <a name="ports"></a>Port

SSH 経由で Linux VM に接続するには、お使いの VM で次のポートを開いておく必要があります。

* 受信ポート:SSH (22)

## <a name="connect-using-username-and-password"></a><a name="username"></a>接続: ユーザー名とパスワードを使用する

1. [Azure Portal](https://portal.azure.com)を開きます。 接続先の仮想マシンに移動し、 **[接続]** をクリックし、ドロップダウンから **[Bastion]** を選択します。

   ![接続する](./media/bastion-connect-vm-ssh/connect.png)
1. [Bastion] をクリックすると、RDP、SSH、Bastion の 3 つのタブがあるサイド バーが表示されます。 Bastion が仮想ネットワーク用にプロビジョニングされている場合、[Bastion] タブが既定でアクティブになっています。 Bastion を仮想ネットワーク用にプロビジョニングしていない場合は、[Bastion の構成](bastion-create-host-portal.md)に関する記事をご覧ください。

   ![Bastion](./media/bastion-connect-vm-ssh/bastion.png)
1. 仮想マシンに SSH 接続するためのユーザー名とパスワードを入力します。
1. キーを入力したら、 **[接続]** をクリックします。

## <a name="connect-manually-enter-a-private-key"></a><a name="privatekey"></a>接続: 秘密キーを手動で入力する

1. [Azure Portal](https://portal.azure.com)を開きます。 接続先の仮想マシンに移動し、 **[接続]** をクリックし、ドロップダウンから **[Bastion]** を選択します。

   ![接続する](./media/bastion-connect-vm-ssh/connect.png)
1. [Bastion] をクリックすると、RDP、SSH、Bastion の 3 つのタブがあるサイド バーが表示されます。 Bastion が仮想ネットワーク用にプロビジョニングされている場合、[Bastion] タブが既定でアクティブになっています。 Bastion を仮想ネットワーク用にプロビジョニングしていない場合は、[Bastion の構成](bastion-create-host-portal.md)に関する記事をご覧ください。

   ![Bastion](./media/bastion-connect-vm-ssh/bastion.png)
1. ユーザー名を入力し、 **[SSH 秘密キー]** を選択します。
1. **[SSH 秘密キー]** のテキスト領域に秘密キーを入力します (または直接貼り付けます)。
1. キーを入力したら、 **[接続]** をクリックします。

## <a name="connect-using-a-private-key-file"></a><a name="ssh"></a>接続: 秘密キー ファイルを使用する

1. [Azure Portal](https://portal.azure.com)を開きます。 接続先の仮想マシンに移動し、 **[接続]** をクリックし、ドロップダウンから **[Bastion]** を選択します。

   ![接続する](./media/bastion-connect-vm-ssh/connect.png)
1. [Bastion] をクリックすると、RDP、SSH、Bastion の 3 つのタブがあるサイド バーが表示されます。 Bastion が仮想ネットワーク用にプロビジョニングされている場合、[Bastion] タブが既定でアクティブになっています。 Bastion を仮想ネットワーク用にプロビジョニングしていない場合は、[Bastion の構成](bastion-create-host-portal.md)に関する記事をご覧ください。

   ![Bastion](./media/bastion-connect-vm-ssh/bastion.png)
1. ユーザー名を入力し、 **[SSH Private Key from Local File]\(ローカル ファイルの SSH 秘密キー\)** を選択します。
1. **[参照]** (ローカル ファイルのフォルダー アイコン) をクリックします。
1. ファイルを参照し、 **[開く]** をクリックします。
1. **[接続]** をクリックして VM に接続します。 [接続] をクリックすると、この仮想マシンへの SSH 接続が Azure portal で直接開きます。 この接続は、仮想マシンのプライベート IP を使用して、Bastion サービスのポート 443 で HTML5 を介して行われます。

## <a name="next-steps"></a>次のステップ

[Bastion に関する FAQ](bastion-faq.md) を読む
