---
title: "Azure DevTest Labs でのラボの基本ポリシーの管理 | Microsoft Docs"
description: "DevTest Labs のラボの基本ポリシー (設定) をいくつか設定する方法について説明します"
services: devtest-lab,virtual-machines
documentationcenter: na
author: tomarcher
manager: douge
editor: 
ms.assetid: 
ms.service: devtest-lab
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/16/2017
ms.author: tarcher
translationtype: Human Translation
ms.sourcegitcommit: afe143848fae473d08dd33a3df4ab4ed92b731fa
ms.openlocfilehash: e5b622be66c3e009c67a00dd380efd87b013c920
ms.lasthandoff: 03/17/2017


---

# <a name="manage-basic-policies-for-a-lab-in-azure-devtest-labs"></a>Azure DevTest Labs でのラボの基本ポリシーの管理

Azure DevTest Labs では、各ラボのポリシー (設定) を管理することで、ラボのコストを制御し、無駄を最小限に抑えることができます。 この記事では、ポリシーを使用するにあたり、最も重要な 2 つのポリシーを設定する方法について説明します。1 つは 1 人のユーザーによって作成または要求できる仮想マシン (VM) の数を制限するポリシー、もう 1 つは自動シャットダウンを構成するポリシーです。 すべてのラボのポリシーを設定する方法については、[Azure DevTest Labs でのラボのポリシーの定義](devtest-lab-set-lab-policy.md)に関する記事をご覧ください。  

## <a name="accessing-a-labs-policies-in-azure-devtest-labs"></a>Azure DevTest Labs でのラボのポリシーへのアクセス
次の手順は、Azure DevTest Labs でラボのポリシーを設定する方法を示します。

ラボのポリシーを表示 (および変更) するには、次の手順に従います。

1. [Azure ポータル](http://go.microsoft.com/fwlink/p/?LinkID=525040)にサインインします。

1. **[その他のサービス]** を選択し、一覧の **[DevTest Labs]** を選択します。

1. ラボの一覧で目的のラボを選択します。   

1. **[Configuration and policies (構成とポリシー)]** を選択します。

    ![ポリシー設定ブレード](./media/devtest-lab-set-lab-policy/policies-menu.png)

1. **[Configuration and policies (構成とポリシー)]** ブレードには、指定できる設定のメニューが含まれています。 この記事では、**ユーザーごとの VM 数**と**自動シャットダウン**の設定についてのみ説明します。 他の設定については、[Azure DevTest Labs でラボのすべてのポリシーを管理する方法](./devtest-lab-set-lab-policy.md)に関するページをご覧ください。 
   
## <a name="set-virtual-machines-per-user"></a>ユーザーごとの仮想マシンの設定
**[ユーザーごとの VM 数]** のポリシーでは、各ユーザーが作成できる VM の最大数を指定できます。 ユーザーの上限に達している場合、ユーザーが VM を作成または要求しようとすると、VM を作成/要求できないことを示すエラー メッセージが表示されます。 

1. ラボの **[Configuration and policies (構成とポリシー)]** メニューの **[Virtual machines per user (ユーザーごとの VM 数)]** を選択します。
   
    ![ユーザーごとの VM 数](./media/devtest-lab-set-lab-policy/max-vms-per-user.png)

1. **[はい]** を選択して、ユーザーごとの VM 数を制限します。 ユーザーごとの VM 数を制限しない場合は、**[いいえ]** を選択します。 **[はい]** を選択した場合は、ユーザーが作成または要求できる VM の最大数を示す数値を入力します。 

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

## <a name="next-steps"></a>次のステップ

- [Azure DevTest Labs でのラボのポリシーの定義](devtest-lab-set-lab-policy.md) - 他のラボのポリシーを変更する方法について説明します 

