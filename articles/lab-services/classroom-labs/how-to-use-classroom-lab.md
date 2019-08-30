---
title: Azure Lab Services でクラスルーム ラボにアクセスする方法 | Microsoft Docs
description: このチュートリアルでは、教師によって設定されたクラスルーム ラボ内の仮想マシンにアクセスします。
services: devtest-lab, lab-services, virtual-machines
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
ms.date: 08/19/2019
ms.author: spelluru
ms.openlocfilehash: 8987a22d4546fe887d1dda9adff8f46afe667618
ms.sourcegitcommit: bb8e9f22db4b6f848c7db0ebdfc10e547779cccc
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/20/2019
ms.locfileid: "69644985"
---
# <a name="how-to-access-a-classroom-lab-in-azure-lab-services"></a>Azure Lab Services でクラスルーム ラボにアクセスする方法
この記事では、クラスルーム ラボを登録したり、自分がアクセスできるすべてのラボを確認したり、ラボの VM を開始または停止したり、VM に接続したりする方法について説明します。 

## <a name="register-to-the-lab"></a>ラボに登録する

1. 教師から受け取った**登録 URL** に移動します。 登録完了後は、登録 URL を使用する必要はありません。 代わりに [https://labs.azure.com](https://labs.azure.com) という URL を使用します。 Internet Explorer 11 はまだサポートされていません。 
1. 学校アカウントを使ってサービスにサインインし、登録を完了します。 
2. 登録した後、アクセスできるラボの仮想マシンが表示されることを確認します。 
3. 仮想マシンの準備が完了するまで待ちます。 VM タイルで次のフィールドに注目してください。
    1. タイルの上部に、**ラボの名前**が表示されます。
    1. その右側には、VM の**オペレーティング システム (OS)** を表すアイコンが表示されます。 この例では Windows OS です。 
    1. タイルの一番下には、VM の起動と停止、VM への接続に使用するアイコンとボタンが表示されます。 
    1. それらのボタンの右側には、VM の状態が表示されます。 VM の状態が**停止**として表示されていることを確認してください。

        ![VM が停止状態](../media/tutorial-connect-vm-in-classroom-lab/vm-in-stopped-state.png)

## <a name="start-or-stop-the-vm"></a>VM を起動または停止する
1. 次の画像に示したように、先頭のボタンを選択して、VM を**起動**します。 このプロセスには、ある程度時間がかかります。  

    ![VM を起動する](../media/tutorial-connect-vm-in-classroom-lab/start-vm.png)
4. VM の状態が**実行中**になっていることを確認します。 

    ![VM が実行中の状態](../media/tutorial-connect-vm-in-classroom-lab/vm-running.png)

    先頭のボタンが**停止**操作を表すアイコンに変化していることがわかります。 このボタンを選択することで、VM を停止することができます。 

## <a name="connect-to-the-vm"></a>VM に接続します

1. ラボの VM に**接続**するには、次の画像に示した 2 つ目のボタンを選択します。 

    ![VM への接続](../media/tutorial-connect-vm-in-classroom-lab/connect-vm.png)
2. 次のいずれかの手順を実行します。 
    1. **Windows** 仮想マシンの場合は、**RDP** ファイルをハード ディスクに保存します。 仮想マシンに接続するための RDP ファイルを開きます。 教師から提供された**ユーザー名**と**パスワード**を使用してマシンにサインインします。 
    3. **Linux** 仮想マシンの場合は、**SSH** または **RDP** を使用して接続できます (有効な場合)。 詳細については、[Linux マシンのリモート デスクトップ接続の有効化](how-to-enable-remote-desktop-linux.md)に関するページを参照してください。 

## <a name="progress-bar"></a>進行状況バー 
タイルの進行状況バーは、ユーザーに割り当てられた[クォータ時間](how-to-configure-student-usage.md#set-quotas-for-users)数に対して費やされた時間数を示しています。 この時間は、ラボに対してスケジュールされている時間のほかに、追加で割り当てられる時間です。 進行状況バーの色とその下に表示されるテキストは、次のようにシナリオによって異なります。

- クラスが進行中 (クラスのスケジュール内) である場合、進行状況バーは灰色表示され、クォータ時間は使用されていないことがわかります。 

    ![進行状況バーが灰色表示](../media/tutorial-connect-vm-in-classroom-lab/progress-bar-class-in-progress.png)
- クォータが割り当てられていない (ゼロ時間である) 場合は、進行状況バーの代わりに、"**Available during classes only (クラス中のみ使用可能)** " というテキストが表示されます。 
    
    ![クォータが設定されていないときの状態](../media/tutorial-connect-vm-in-classroom-lab/available-during-class.png)
- **クォータを使い果たす**と、進行状況バーが**赤色**になります。 

    ![進行状況バーが赤色](../media/tutorial-connect-vm-in-classroom-lab/progress-bar-red-color.png)
- ラボに対してスケジュールされた時間の範囲外で、かつクォータ時間の一部が使用済みである場合には、進行状況バーが**青色**になります。 

    ![進行状況バーが青色](../media/tutorial-connect-vm-in-classroom-lab/progress-bar-blue-color.png)


## <a name="view-all-the-classroom-labs"></a>すべてのクラスルーム ラボを表示する
ラボへの登録後は、次の手順に従ってクラスルーム ラボを表示できます。 

1. [https://labs.azure.com](https://labs.azure.com) に移動します。 Internet Explorer 11 はまだサポートされていません。 
2. ラボへの登録に使用したユーザー アカウントを使用してサービスにサインインします。 
3. 自分にアクセス権があるすべてのラボが表示されることを確認します。 

    ![すべてのラボを表示する](../media/how-to-use-classroom-lab/all-labs.png)


## <a name="next-steps"></a>次の手順
次の記事を参照してください。

- [管理者としてラボ アカウントを作成および管理する](how-to-manage-lab-accounts.md)
- [ラボ所有者としてラボを作成および管理する](how-to-manage-classroom-labs.md)
- [ラボ所有者としてテンプレートを設定および発行する](how-to-create-manage-template.md)
- [ラボ所有者としてラボの使用を構成および制御する](how-to-configure-student-usage.md)
 