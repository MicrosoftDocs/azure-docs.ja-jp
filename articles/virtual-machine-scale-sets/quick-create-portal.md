---
title: クイック スタート - Azure Portal で仮想マシン スケール セットを作成する | Microsoft Docs
description: Azure Portal で仮想マシン スケール セットをすばやく作成する方法を説明します
keywords: 仮想マシン スケール セット
services: virtual-machine-scale-sets
documentationcenter: ''
author: cynthn
manager: jeconnoc
editor: tysonn
tags: azure-resource-manager
ms.assetid: 9c1583f0-bcc7-4b51-9d64-84da76de1fda
ms.service: virtual-machine-scale-sets
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm
ms.devlang: na
ms.topic: quickstart
ms.custom: H1Hack27Feb2017
ms.date: 03/27/2018
ms.author: cynthn
ms.openlocfilehash: a2081bab2aebf0d49f3bde2467dac1fa683452ab
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2019
ms.locfileid: "58008721"
---
# <a name="quickstart-create-a-virtual-machine-scale-set-in-the-azure-portal"></a>クイック スタート:Azure Portal での仮想マシン スケール セットの作成
仮想マシン スケール セットを使用すると、同一の自動スケールの仮想マシンのセットをデプロイおよび管理できます。 スケール セット内の VM の数を手動で拡張したり、CPU などのリソースの使用率、メモリの需要、またはネットワーク トラフィックに基づいて自動的にスケールする規則を定義したりすることができます。 その後、Azure ロード バランサーがトラフィックをスケール セット内の VM インスタンスに分散します。 このクイック スタートでは、Azure Portal で仮想マシン スケール セットを作成します。

Azure サブスクリプションをお持ちでない場合は、開始する前に [無料アカウント](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) を作成してください。


## <a name="log-in-to-azure"></a>Azure にログインする
Azure Portal (https://portal.azure.com) にログインします。


## <a name="create-virtual-machine-scale-set"></a>仮想マシン スケール セットを作成する
Windows Server イメージまたは Linux イメージ (RHEL、CentOS、Ubuntu、SLES など) を含むスケール セットをデプロイできます。

1. Azure Portal の左上隅にある **[リソースの作成]** をクリックします。
2. "*スケール セット*" を検索し、**[仮想マシン スケール セット]** を選択し、**[作成]** を選択します。
3. スケール セットの名前を入力します (*myScaleSet* など)。
4. 目的の OS の種類を選択します (*Windows Server 2016 Datacenter* など)。
5. 目的のリソース グループ名 (*myResourceGroup* など) と場所 ("*米国東部*" など) を入力します。
6. 目的のユーザー名を入力して、任意の認証の種類を選択します。
   - **パスワード**は、12 文字以上で指定する必要があります。また、1 つの小文字、1 つの大文字、1 つの数字、1 つの特殊文字という複雑さの 4 つの要件のうち、3 つを満たしている必要があります。 詳細については、[ユーザー名とパスワードの要件](../virtual-machines/windows/faq.md#what-are-the-username-requirements-when-creating-a-vm)を参照してください。
   - Linux OS ディスク イメージを選択した場合は、代わりに **[SSH public key]\(SSH 公開キー\)** を選択できます。 公開キーのみを指定してください (*~/.ssh/id_rsa.pub* など)。 ポータルから Azure Cloud Shell を使用して、[SSH キー](../virtual-machines/linux/mac-create-ssh-keys.md)を作成および使用することができます。

     ![Azure portal で仮想マシン スケール セットを作成する基本的な詳細](./media/virtual-machine-scale-sets-create-portal/create-scale-set-basic-details.png)
1. **[負荷分散オプションの選択]** で *[ロード バランサー]* などの負荷分散オプションを選択します。 負荷分散オプションのその他の詳細を入力します。 たとえば、*[ロード バランサー]* の場合、**[パブリック IP アドレス名]** と **[ドメイン名ラベル]** を入力する必要があります。
1. **[仮想ネットワークを構成します]** で仮想ネットワークの詳細を入力します。 たとえば、新しい仮想ネットワーク *myVirtualNetwork* と新しいサブネット *default* を作成できます。
1. スケール セットのオプションを確認するために、**[作成]** を選択します。
    ![Azure portal で仮想マシン スケール セットを作成するネットワークの詳細](./media/virtual-machine-scale-sets-create-portal/create-scale-set-networking-details.png)



## <a name="connect-to-a-vm-in-the-scale-set"></a>スケール セットの VM に接続する
ポータルでスケール セットを作成すると、ロード バランサーが作成されます。 ネットワーク アドレス変換 (NAT) 規則は、RDP や SSH などのリモート接続用のスケール セット インスタンスにトラフィックを分散するために使用されます。

スケール セット インスタンスに対するこれらの NAT 規則と接続情報を表示するには、次の操作を行います。

1. 前の手順で作成したリソース グループ (*myResourceGroup* など) を選択します。
2. リソースの一覧から**ロード バランサー** (*myScaleSetLab* など) を選択します。
3. ウィンドウの左側のメニューから **[受信 NAT 規則]** を選択します。

    ![受信 NAT 規則を使用して、仮想マシン スケール セットのインスタンスに接続できます](./media/virtual-machine-scale-sets-create-portal/inbound-nat-rules.png)

これらの NAT 規則を使用して、スケール セット内の各 VM に接続することができます。 それぞれの VM インスタンスの宛先 IP アドレスと TCP ポートの値が表示されます。 たとえば、宛先 IP アドレスが *104.42.1.19* で、TCP ポートが *50001* の場合は、次のように VM インスタンスに接続します。

- Windows スケール セットの場合は、`104.42.1.19:50001` で、RDP を使用して VM インスタンスに接続します。
- Linux スケール セットの場合は、`ssh azureuser@104.42.1.19 -p 50001` で、SSH を使用して VM インスタンスに接続します。

プロンプトが表示されたら、前の手順でスケール セットを作成したときに指定した資格情報を入力します。 スケール セット インスタンスは、通常の VM であり、通常どおりに対話できます。 スケール セット インスタンスにアプリケーションをデプロイして実行する方法の詳細については、「[仮想マシン スケール セットへのアプリケーションのデプロイ](virtual-machine-scale-sets-deploy-app.md)」を参照してください。


## <a name="clean-up-resources"></a>リソースのクリーンアップ
必要がなくなったら、リソース グループ、スケール セット、およびすべての関連リソースを削除します。 そのためには、VM のリソース グループを選択し、**[削除]** をクリックします。


## <a name="next-steps"></a>次の手順
このクイック スタートでは、Azure Portal で基本的なスケール セットを作成しました。 さらに学習するには、Azure 仮想マシン スケール セットを作成および管理する方法についてのチュートリアルに進んでください。

> [!div class="nextstepaction"]
> [Azure 仮想マシン スケール セットの作成と管理](tutorial-create-and-manage-powershell.md)
