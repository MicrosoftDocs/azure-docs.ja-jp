---
title: "Azure DevTest Labs でのラボのポリシーの管理 | Microsoft Docs"
description: "VM サイズ、ユーザーごとの VM の最大数、シャットダウンの自動化など、ラボのポリシーを定義する方法について説明します。"
services: devtest-lab,virtual-machines
documentationcenter: na
author: tomarcher
manager: douge
editor: 
ms.assetid: 7756aa64-49ca-45a0-9f90-0fd101c7be85
ms.service: devtest-lab
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/13/2017
ms.author: tarcher
ms.custom: H1Hack27Feb2017
translationtype: Human Translation
ms.sourcegitcommit: a087df444c5c88ee1dbcf8eb18abf883549a9024
ms.openlocfilehash: 799bacdac6ff49cb6fa38cd208318673629d4035
ms.lasthandoff: 03/15/2017


---
# <a name="manage-all-policies-for-a-lab-in-azure-devtest-labs"></a>Azure DevTest Labs でラボのすべてのポリシーを管理
> [!VIDEO https://channel9.msdn.com/Blogs/Azure/How-to-set-VM-policies-in-a-DevTest-Lab/player]
> 
> 

Azure DevTest Labs では、各ラボのポリシー (設定) を管理することで、ラボのコストを制御し、無駄を最小限に抑えることができます。 この記事では、各ポリシーを設定する方法を順を追って詳しく説明します。  

## <a name="set-allowed-virtual-machine-sizes"></a>許可される仮想マシン サイズの設定
許可される VM サイズを設定するポリシーでは、ラボで使用可能な VM サイズを指定できるので、ラボの無駄を最小限に抑えるのに役立ちます。 このポリシーを有効にすると、この一覧の VM サイズだけを使用して VM を作成できます。

1. ラボの **[Configuration and policies (構成とポリシー)]** ブレードで、**[許可される仮想マシンのサイズ]** を選択します。
   
    ![[許可される仮想マシンのサイズ]](./media/devtest-lab-set-lab-policy/allowed-vm-sizes.png)

1. このポリシーを有効にするには **[オン]** を選択し、無効にするには **[オフ]** を選択します。

1. このポリシーを有効にする場合は、ラボで作成できる&1; つ以上の VM サイズを選択します。

1. [ **保存**] を選択します。

## <a name="set-virtual-machines-per-user"></a>ユーザーごとの仮想マシンの設定
**[ユーザーごとの VM 数]** のポリシーでは、各ユーザーが作成できる VM の最大数を指定できます。 ユーザーの上限に達している場合、ユーザーが VM を作成または要求しようとすると、VM を作成/要求できないことを示すエラー メッセージが表示されます。 

1. ラボの **[Configuration and policies (構成とポリシー)]** メニューの **[Virtual machines per user (ユーザーごとの VM 数)]** を選択します。
   
    ![ユーザーごとの VM 数](./media/devtest-lab-set-lab-policy/max-vms-per-user.png)

1. **[はい]** を選択して、ユーザーごとの VM 数を制限します。 ユーザーごとの VM 数を制限しない場合は、**[いいえ]** を選択します。 **[はい]** を選択した場合は、ユーザーが作成または要求できる VM の最大数を示す数値を入力します。 

1. **[はい]** を選択して、SSD (ソリッド ステート ディスク) を使用できる VM 数を制限します。 SSD を使用できる VM 数を制限しない場合は、**[いいえ]** を選択します。 **[はい]** を選択した場合は、SSD を使用して作成できる VM の最大数を示す数値を入力します。 

1. [ **保存**] を選択します。

## <a name="set-virtual-machines-per-lab"></a>ラボごとの仮想マシンの設定
**ラボごとの VM 数** のポリシーでは、現在のラボで作成できる VM の最大数を指定することができます。 ラボの上限に達している場合、ユーザーが VM を作成しようとすると、VM を作成できないことを示すエラー メッセージが表示されます。 

1. ラボの **[Configuration and policies (構成とポリシー)]** メニューの **[Virtual machines per lab (ラボごとの VM 数)]** を選択します。
   
    ![ラボごとの VM 数](./media/devtest-lab-set-lab-policy/max-vms-per-lab.png)

1. **[はい]** を選択して、ラボごとの VM 数を制限します。 ラボごとの VM 数を制限しない場合は、**[いいえ]** を選択します。 **[はい]** を選択した場合は、ユーザーが作成または要求できる VM の最大数を示す数値を入力します。 

1. **[はい]** を選択して、SSD (ソリッド ステート ディスク) を使用できる VM 数を制限します。 SSD を使用できる VM 数を制限しない場合は、**[いいえ]** を選択します。 **[はい]** を選択した場合は、SSD を使用して作成できる VM の最大数を示す数値を入力します。 

1. [ **保存**] を選択します。

## <a name="set-auto-shutdown"></a>自動シャットダウンの設定
自動シャットダウン ポリシーでは、このラボの VM をシャットダウンする時刻を指定できるので、ラボの無駄を最小限に抑えるのに役立ちます。

1. ラボの **[Configuration and policies (構成とポリシー)]** ブレードで **[自動シャットダウン]** を選択します。
   
    ![自動シャットダウン](./media/devtest-lab-set-lab-policy/auto-shutdown.png)

1. このポリシーを有効にするには **[オン]** を選択し、無効にするには **[オフ]** を選択します。

1. このポリシーを有効にした場合は、現在のラボのすべての VM をシャットダウンする時刻 (およびタイム ゾーン) を指定します。

1. 指定した自動シャットダウン時刻の 15 分前に通知を送信するオプションに対して **[はい]** または **[いいえ]** を指定します。 **[はい]** を指定した場合は、通知を受信する webhook の URL エンドポイントを入力します。 webhook の詳細については、「[webhook または API Azure Function を作成する](../azure-functions/functions-create-a-web-hook-or-api-function.md)」を参照してください。 

1. [ **保存**] を選択します。

    このポリシーを有効にすると、既定では現在のラボのすべての VM にこのポリシーが適用されます。 この設定を特定の VM から削除するには、その VM のブレードを開いて **[自動シャットダウン]** 設定を変更します。 

## <a name="set-auto-start"></a>自動開始の設定
自動開始のポリシーでは、現在のラボにある VM をいつ開始するか指定することができます。  

1. ラボの **[Configuration and policies (構成とポリシー)]** ブレードで **[自動開始]** を選択します。
   
    ![自動開始](./media/devtest-lab-set-lab-policy/auto-start.png)

2. このポリシーを有効にするには **[オン]** を選択し、無効にするには **[オフ]** を選択します。

3. このポリシーを有効にした場合は、ポリシーを開始する時刻、タイム ゾーン、その時刻を適用する曜日を指定します。 

4. [ **保存**] を選択します。

    このポリシーを有効にしても、現在のラボの VM に自動的に適用されることはありません。 この設定を特定の VM に適用するには、その VM のブレードを開いて **[自動開始]** 設定を変更します。 

[!INCLUDE [devtest-lab-try-it-out](../../includes/devtest-lab-try-it-out.md)]

## <a name="next-steps"></a>次のステップ
ラボに対して各種の VM ポリシー設定を定義および適用した後は、次に示すいくつかの操作を試してみてください。

* [共有 IP アドレスについて](devtest-lab-shared-ip.md) - DevTest Labs で共有 IP アドレスを使用して、ラボの VM への接続に必要なパブリック IP アドレスの数を最小限に抑える方法について説明します。
* [コスト管理を構成する](devtest-lab-configure-cost-management.md) - **月間推定コスト傾向**グラフを使用して  
  現在のカレンダー月の現時点までの推定コストと月末の予測コストを表示する方法を示します。
* [カスタム イメージを作成する](devtest-lab-create-template.md) - VM を作成する場合は、ベースとしてカスタム イメージまたは Marketplace イメージを指定します。 この記事では、VHD ファイルからカスタム イメージを作成する方法について説明します。
* [Marketplace イメージを構成する](devtest-lab-configure-marketplace-images.md) - Azure DevTest Labs では、Azure Marketplace イメージを基にした VM を作成することができます。 この記事では、ラボで VM を作成する際に使用できるようにする Azure Marketplace イメージ (該当するものがある場合) を指定する方法について説明します。
* [ラボで VM を作成する](devtest-lab-add-vm-with-artifacts.md) - 基本イメージ (カスタムまたは Marketplace) から VM を作成する方法と、VM でアーティファクトを操作する方法について説明します。


