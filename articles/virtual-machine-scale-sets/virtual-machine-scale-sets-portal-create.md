---
title: "Azure ポータルを使用した仮想マシン スケール セットの作成 | Microsoft Docs"
description: "Azure Portal を使用してスケール セットをデプロイします。"
keywords: "仮想マシン スケール セット"
services: virtual-machine-scale-sets
documentationcenter: 
author: gatneil
manager: madhana
editor: tysonn
tags: azure-resource-manager
ms.assetid: 9c1583f0-bcc7-4b51-9d64-84da76de1fda
ms.service: virtual-machine-scale-sets
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm
ms.devlang: na
ms.topic: article
ms.date: 09/15/2016
ms.author: negat
ms.custom: H1Hack27Feb2017
translationtype: Human Translation
ms.sourcegitcommit: 06e7418111762a0388ce1704ba5abaa9c3a10349
ms.openlocfilehash: e0853070b5a3f9d44baebecac625a5ad6b9ef755
ms.lasthandoff: 03/01/2017


---
# <a name="how-to-create-a-virtual-machine-scale-set-with-the-azure-portal"></a>Azure Portal を使用して Virtual Machine Scale Set を作成する方法
このチュートリアルでは、Azure Portal を使用して仮想マシン スケール セットをわずか数分で簡単に作成する方法を示します。 Azure サブスクリプションをお持ちでない場合は、開始する前に [無料アカウント](https://azure.microsoft.com/free/) を作成してください。

## <a name="choose-the-vm-image-from-the-marketplace"></a>Marketplace から VM イメージを選択する
Portal では、CentOS、CoreOS、Debian、Open Suse、Red Hat Enterprise Linux、SUSE Linux Enterprise Server、Ubuntu Server、または Windows Server イメージを使用してスケール セットを簡単にデプロイすることができます。

まず、Web ブラウザーで、 [Azure Portal](https://portal.azure.com) に移動します。 [`New`] をクリックし、"`scale set`" を検索して、[`Virtual machine scale set`] エントリを選択します。

![ScaleSetPortalOverview](./media/virtual-machine-scale-sets-portal-create/ScaleSetPortalOverview.PNG)

## <a name="create-the-scale-set"></a>スケール セットを作成する
既定の設定を使って、スケール セットをすぐに作成できます。

* [`Basics`] ブレードで、スケール セットの名前を入力します。 この名前は、スケール セットの前に設置されるロード バランサーの FQDN のベースになります。そのため、Azure 全体で一意の名前にする必要があります。
* 任意の OS の種類を選択し、目的のユーザー名を入力して、任意の認証の種類を選択します。 パスワードを選択する場合、パスワードは 12 文字以上にする必要があります。また、1 つの小文字、1 つの大文字、1 つの数字、1 つの特殊文字という複雑さの 4 要件のうち、3 つを満たしている必要があります。 [ユーザー名とパスワードの要件](../virtual-machines/virtual-machines-windows-faq.md#what-are-the-username-requirements-when-creating-a-vm)に関するページで詳細を確認してください。 [`SSH public key`] を選択した場合、秘密キーではなく、公開キーのみを貼り付けます。

![ScaleSetPortalBasics](./media/virtual-machine-scale-sets-portal-create/ScaleSetPortalBasics.PNG)

* スケール セットを&1; つの配置グループに制限するか、または複数の配置グループにまたがるかを選びます。 複数の配置グループにまたがるスケール セットを許可すると、制限はありますが、スケール セットの容量は 100 VM を超える (最大 1,000) ことができます。 詳しくは、[こちらのドキュメント](./virtual-machine-scale-sets-placement-groups.md)をご覧ください。
* 目的のリソース グループ名と場所を入力し、[`OK`] をクリックします。
* [ `Virtual machine scale set service settings` ] ブレードで、目的のドメイン名のラベル (スケール セットの前に設置されたロード バランサーの FQDN のベース) を入力します。 このラベルは、Azure 全体で一意の名前にする必要があります。
* 目的のオペレーティング システム ディスク イメージ、インスタンス数、マシンのサイズを選択します。
* 目的のディスクの種類を、管理または非管理対象から選びます。 詳しくは、[こちらのドキュメント](./virtual-machine-scale-sets-managed-disks.md)をご覧ください。 複数の配置グループにまたがるスケール セットを指定した場合は、スケール セットが複数の配置グループにまたがるには管理ディスクが必要であるため、このオプションは変更できません。
* 自動スケールを有効または無効にします。有効にした場合は、次のように構成します。

![ScaleSetPortalService](./media/virtual-machine-scale-sets-portal-create/ScaleSetPortalService.PNG)

* 検証が終了したら、[`Summary`] ブレードで [`OK`] をクリックしてスケール セットのデプロイを開始します。


## <a name="connect-to-a-vm-in-the-scale-set"></a>スケール セットの VM に接続する
スケール セットを&1; つの配置グループに制限することを選んだ場合は、スケール セットに簡単に接続できるように構成された NAT 規則と共に、スケール セットがデプロイされます (この規則がない場合は、スケール セットの仮想マシンに接続するには、スケール セットと同じ仮想ネットワークにジャンプボックス作成する必要があります)。 これらの NAT 規則を表示するには、スケール セットのロード バランサーの [`Inbound NAT Rules`] タブに移動します。

![ScaleSetPortalNatRules](./media/virtual-machine-scale-sets-portal-create/ScaleSetPortalNatRules.PNG)

これらの NAT 規則を使用して、スケール セット内の各 VM に接続することができます。 たとえば、Windows スケール セットで、受信ポート 50000 の NAT 規則がある場合、 `<load-balancer-ip-address>:50000`の RDP を経由してマシンに接続することができます。 Linux スケール セットの場合、 `ssh -p 50000 <username>@<load-balancer-ip-address>`コマンドを使用して接続することができます。

## <a name="next-steps"></a>次のステップ
CLI を使用してスケール セットをデプロイする方法については、 [このドキュメント](virtual-machine-scale-sets-cli-quick-create.md)をご覧ください。

PowerShell を使用してスケール セットをデプロイする方法については、 [このドキュメント](virtual-machine-scale-sets-windows-create.md)をご覧ください。

Visual Studio を使用してスケール セットをデプロイする方法については、 [このドキュメント](virtual-machine-scale-sets-vs-create.md)をご覧ください。

全般的なドキュメントについては、 [スケール セットの概要に関するページ](virtual-machine-scale-sets-overview.md)をご覧ください。

全般的な情報については、 [スケール セットのメイン ランディング ページ](https://azure.microsoft.com/services/virtual-machine-scale-sets/)をご覧ください。


