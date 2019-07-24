---
title: ラボ内の VM を Azure DevTest Labs で再デプロイする | Microsoft Docs
description: Azure DevTest Labs で仮想マシンを再デプロイする (1 つの Azure ノードから別のノードに移動する) 方法を説明します。
services: devtest-lab,virtual-machines,lab-services
documentationcenter: na
author: spelluru
manager: femila
editor: ''
ms.assetid: 8460f09e-482f-48ba-a57a-c95fe8afa001
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/11/2018
ms.author: spelluru
ms.openlocfilehash: 4500fcfa6fbfb346a6e5c7fd045ba0046a901b91
ms.sourcegitcommit: 7e772d8802f1bc9b5eb20860ae2df96d31908a32
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/06/2019
ms.locfileid: "57454257"
---
# <a name="redeploy-a-vm-in-a-lab-in-azure-devtest-labs"></a>ラボ内の VM を Azure DevTest Labs で再デプロイする
ラボの仮想マシン (VM) に対してリモート デスクトップ接続経由で接続できない場合は、VM を再デプロイし、接続を再試行します。 VM を再デプロイするときに、DevTest Labs は、それが実行されているノードから Azure インフラストラクチャ内の新しいノードに VM を移動します。 次に、すべての構成オプションと関連するリソースを保持したまま VM が起動されます。 この機能によって、ラボ内の Windows ベースの VM へのリモート デスクトップ接続やアプリケーション アクセスのトラブルシューティングに費やす時間を節約できます。 

## <a name="steps-to-redeploy-a-vm-in-a-lab"></a>ラボで VM を再デプロイする手順 
Azure DevTest Labs でラボ内の VM を再デプロイするには、次の手順を実行します。 

1. [Azure Portal](https://portal.azure.com) にサインインします。
2. **[すべてのサービス]** を選択し、一覧の **[DevTest Labs]** を選択します。
3. ラボの一覧で、再デプロイする VM が含まれているラボを選択します。  
4. 左側のパネルで、**[仮想マシン]** を選択します。 
5. VM の一覧で、VM を選択します。
6. VM の [仮想マシン] ページの左メニューにある **[操作]** の下の **[再デプロイ]** を選択します。

    ![Redeploy](media/devtest-lab-redeploy-vm/redeploy.png)
7. このページの情報を読んで、**[再デプロイ]** ボタンをクリックします。 9. **[通知]** ウィンドウで再デプロイ操作の状態を確認します。

    ![状態を再デプロイする](media/devtest-lab-redeploy-vm/redeploy-status.png)

## <a name="next-steps"></a>次の手順
Azure DevTest Labs の [VM のサイズを変更する](devtest-lab-resize-vm.md)方法について説明します。


