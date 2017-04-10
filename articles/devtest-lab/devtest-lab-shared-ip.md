---
title: "Azure DevTest Labs の共有 IP アドレスについて | Microsoft Docs"
description: "Azure DevTest Labs で共有 IP アドレスを使用して、ラボの VM へのアクセスに必要なパブリック IP アドレスを最小限に抑える方法について説明します。"
services: devtest-lab
documentationcenter: na
author: camsoper
manager: douge
editor: 
ms.assetid: 
ms.service: devtest-lab
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/16/2017
ms.author: casoper
translationtype: Human Translation
ms.sourcegitcommit: afe143848fae473d08dd33a3df4ab4ed92b731fa
ms.openlocfilehash: 905357b9e2262b86cde31874287cc0b89eef4815
ms.lasthandoff: 03/17/2017

---

# <a name="understand-shared-ip-addresses-in-azure-devtest-labs"></a>Azure DevTest Labs の共有 IP アドレスについて

Azure DevTest Labs では共有 IP アドレスを使用して、ラボの個別の VM へのアクセスに必要なパブリック IP アドレス数を最小限に抑えます。  この記事では、共有 IP アドレスの動作と、関連する構成オプションについて説明します。

## <a name="shared-ip-setting"></a>共有 IP 設定

新しいラボは、仮想ネットワークのサブネットに作成されます。  既定では、このサブネットは、**[Enable shared public IP (共有パブリック IP を有効にする)]** が "*はい*" に設定されている状態で作成されます。  この構成では、サブネット全体に対して 1 つのパブリック IP アドレスが作成されます。  仮想ネットワークとサブネットの構成について詳しくは、「[Azure DevTest Labs で仮想ネットワークを構成する](devtest-lab-configure-vnet.md)」をご覧ください。

![新しいラボのサブネット](media/devtest-lab-shared-ip/lab-subnet.png)

このラボで作成された VM は、既定では共有 IP を使用するように設定されます。  VM の作成中、この設定は、**[詳細設定]** ブレードの **[IP アドレス構成]** で確認できます。

![新しい VM](media/devtest-lab-shared-ip/new-vm.png)

共有 IP が有効になっている VM がサブネットに追加されると必ず、パブリック IP アドレスで TCP ポートが割り当てられ、VM の RDP ポートに転送されます。  

## <a name="using-the-shared-ip"></a>共有 IP の使用

RDP クライアントの VM のリモート デスクトップに接続するには、IP アドレスまたは完全修飾ドメイン名、コロン、ポート番号の順に指定します。  たとえば、次の図では、VM に接続するための RDP アドレスは `doclab-lab13998814308000.centralus.cloudapp.azure.com:51686` になります。  また、Azure Portal で **[接続]** ボタンを選択して、構成済み RDP ファイルをダウンロードすることもできます。

![VM の例](media/devtest-lab-shared-ip/vm-info.png)

## <a name="next-steps"></a>次のステップ

* [Azure DevTest Labs でラボのポリシーを定義する](devtest-lab-set-lab-policy.md)
* [Azure DevTest Labs で仮想ネットワークを構成する](devtest-lab-configure-vnet.md)






