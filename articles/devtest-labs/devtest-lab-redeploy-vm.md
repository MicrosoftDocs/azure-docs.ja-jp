---
title: ラボ内の VM を Azure DevTest Labs で再デプロイする | Microsoft Docs
description: Azure DevTest Labs で仮想マシンを再デプロイする (1 つの Azure ノードから別のノードに移動する) 方法を説明します。
ms.topic: article
ms.date: 06/26/2020
ms.openlocfilehash: a38b112165b893d877733b967c21bb62b20ca2f6
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "90530320"
---
# <a name="redeploy-a-vm-in-a-lab-in-azure-devtest-labs"></a>ラボ内の VM を Azure DevTest Labs で再デプロイする
ラボの仮想マシン (VM) に対してリモート デスクトップ接続経由で接続できない場合は、VM を再デプロイし、接続を再試行します。 VM を再デプロイするときに、DevTest Labs は、それが実行されているノードから Azure インフラストラクチャ内の新しいノードに VM を移動します。 次に、すべての構成オプションと関連するリソースを保持したまま VM が起動されます。 この機能によって、ラボ内の Windows ベースの VM へのリモート デスクトップ接続やアプリケーション アクセスのトラブルシューティングに費やす時間を節約できます。 

## <a name="steps-to-redeploy-a-vm-in-a-lab"></a>ラボで VM を再デプロイする手順 
Azure DevTest Labs でラボ内の VM を再デプロイするには、次の手順を実行します。 

1. [Azure portal](https://portal.azure.com) にサインインする
2. **[すべてのサービス]** を選択し、一覧の **[DevTest Labs]** を選択します。
3. ラボの一覧で、再デプロイする VM が含まれているラボを選択します。  
4. 左側のパネルで、 **[仮想マシン]** を選択します。 
5. VM の一覧で、VM を選択します。
6. VM の [仮想マシン] ページの左メニューにある **[操作]** の下の **[再デプロイ]** を選択します。

    ![[再デプロイ] が選択された [仮想マシン] ページが表示されている画面キャプチャ。](media/devtest-lab-redeploy-vm/redeploy.png)
7. このページの情報を読んで、 **[再デプロイ]** ボタンをクリックします。 9. **[通知]** ウィンドウで再デプロイ操作の状態を確認します。

    ![状態を再デプロイする](media/devtest-lab-redeploy-vm/redeploy-status.png)

## <a name="next-steps"></a>次のステップ
Azure DevTest Labs の [VM のサイズを変更する](devtest-lab-resize-vm.md)方法について説明します。


