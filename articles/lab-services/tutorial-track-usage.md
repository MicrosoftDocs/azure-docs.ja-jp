---
title: Azure Lab Services でラボの使用状況を追跡する | Microsoft Docs
description: このチュートリアルでは、ラボの使用状況をその作成者/所有者として追跡します。
ms.topic: tutorial
ms.date: 06/26/2020
ms.openlocfilehash: 8a3ca9e011eb89b3db8b202bab11d14f10d74e7e
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "96434619"
---
# <a name="tutorial-track-usage-of-a-lab-in-azure-lab-service"></a>チュートリアル: Azure Lab Services でラボの使用状況を追跡する
このチュートリアルでは、ラボの作成者/所有者がラボの使用状況を追跡する方法について説明します。

このチュートリアルでは、次のアクションを実行します。

> [!div class="checklist"]
> * ラボに登録されているユーザーを確認する
> * ラボの VM の使用状況を確認する
> * 学生の VM を管理する 


## <a name="view-registered-users"></a>登録されているユーザーを表示する

1. [Azure Lab Services Web サイト](https://labs.azure.com)に移動します。 
2. **[サインイン]** を選択して、資格情報を入力します。 Azure Lab Services では、組織アカウントと Microsoft アカウントがサポートされています。
3. **[My labs]\(自分のラボ\)** ページで、使用状況を追跡するラボを選択します。 
4. 左側のメニューまたは **[ユーザー]** タイルの **[ユーザー]** を選択します。 ラボに登録されている学生が表示されます。  

    ![登録されているユーザー](./media/tutorial-track-usage/registered-users.png)

    ラボのユーザーの追加と管理の詳細については、「[ラボ ユーザーを追加および管理する](how-to-configure-student-usage.md)」を参照してください。

## <a name="view-the-usage-of-vms"></a>VM の使用状況を表示する

1. 左側のメニューから **[仮想マシン]** を選択します。 
2. VMの状態と VM が実行されている時間数が表示されていることを確認します。 最後の列に表示される使用時間には、ラボの所有者が学生の VM に関して費やした時間はカウントされません。 

    ![VM の使用状況](./media/tutorial-track-usage/vm-usage.png)

## <a name="manage-student-vms"></a>学生の VM を管理する 
このページでは、 **[状態]** 列またはツール バーのコントロールを使用して、学生の VM を起動、停止、またはリセットできます。

![VM アクション](./media/tutorial-track-usage/vm-controls.png)

ラボの仮想マシン プールの管理の詳細については、「[仮想マシン プールを設定および管理する](how-to-set-virtual-machine-passwords.md)」を参照してください。

> [!NOTE]
> 教師が学生用 VM をオンにしても、学生のクォータは影響を受けません。 ユーザーのクォータによって指定されるのは、スケジュールされたクラス時間外にユーザーが使用できるラボ時間数です。 クォータの詳細については、「[ユーザーのクォータを設定する](how-to-configure-student-usage.md?#set-quotas-for-users)」を参照してください。

## <a name="next-steps"></a>次のステップ
ラボの詳細については、[攻略ガイド](how-to-manage-lab-accounts.md)の各記事を参照してください。
