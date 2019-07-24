---
title: Azure Lab Services でラボの使用状況を追跡する | Microsoft Docs
description: このチュートリアルでは、ラボの使用状況をその作成者/所有者として追跡します。
services: lab-services
documentationcenter: na
author: spelluru
manager: ''
editor: ''
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.custom: mvc
ms.date: 01/17/2019
ms.author: spelluru
ms.openlocfilehash: e2831191905da1b9e0ad55131be9eaa7aa13950e
ms.sourcegitcommit: e51e940e1a0d4f6c3439ebe6674a7d0e92cdc152
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/08/2019
ms.locfileid: "55894362"
---
# <a name="tutorial-track-usage-of-a-lab-in-azure-lab-service"></a>チュートリアル:Azure Lab Services でラボの使用状況を追跡する
このチュートリアルでは、ラボの作成者/所有者がラボの使用状況を追跡する方法について説明します。

このチュートリアルでは、次のアクションを実行します。

> [!div class="checklist"]
> * ラボに登録されているユーザーを確認する
> * ラボの VM の使用状況を確認する
> * 学生の VM を管理する 


## <a name="view-users-registered-with-the-lab"></a>ラボに登録されているユーザーを確認する

1. [Azure Lab Services Web サイト](https://labs.azure.com)に移動します。 
2. **[サインイン]** を選択して、資格情報を入力します。 Azure Lab Services では、組織アカウントと Microsoft アカウントがサポートされています。
3. **[My labs]\(自分のラボ\)** ページで、使用状況を追跡するラボを選択します。 
4. 左側のメニューまたは **[ユーザー]** タイルの **[ユーザー]** を選択します。 ラボに登録されている学生が表示されます。 まだラボに登録していない新しい学生がいる場合は、**[登録リンク]** を選択し、リンクをコピーして送信してください。 

    ![登録されているユーザー](../media/tutorial-track-usage/registered-users.png)

## <a name="view-the-usage-of-vms-in-the-lab"></a>ラボの VM の使用状況を確認する 

1. 左側のメニューから **[仮想マシン]** を選択します。 
2. VMの状態と VM が実行されている時間数が表示されていることを確認します。 最後の列に表示される使用時間には、ラボの所有者が学生の VM に関して費やした時間はカウントされません。 

    ![VM の使用状況](../media/tutorial-track-usage/vm-usage.png)

## <a name="manage-student-vms"></a>学生の VM を管理する 
仮想マシン一覧の行にマウス カーソルを合わせると、次のタスクを実行するためのコントロールが表示されます (前セクションの画像を参照)。 

- VM への接続
- VM の起動
- VM の停止
- VM の削除


![VM のコントロール](../media/tutorial-track-usage/vm-controls.png)

ツール バー ボタンを使用して、VM を起動、停止、または削除することもできます。 



## <a name="next-steps"></a>次の手順
クラスルーム ラボの詳細については、[攻略ガイド](how-to-manage-lab-accounts.md)の各記事を参照してください。
