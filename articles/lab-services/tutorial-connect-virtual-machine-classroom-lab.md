---
title: Azure Lab Services でクラスルーム ラボにアクセスする | Microsoft Docs
description: このチュートリアルでは、教師によって設定されたクラスルーム ラボ内の仮想マシンにアクセスします。
ms.topic: tutorial
ms.date: 06/26/2020
ms.openlocfilehash: dacfa34c0d3ab637ef513342bc5ce5fe81038e11
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "85443470"
---
# <a name="tutorial-access-a-classroom-lab-in-azure-lab-services"></a>チュートリアル:Azure Lab Services でクラスルーム ラボにアクセスする
このチュートリアルでは、学生がクラスルーム ラボ内の仮想マシン (VM) にアクセスします。 

このチュートリアルでは、次のアクションを実行します。

> [!div class="checklist"]
> * ラボに登録する
> * VM を起動する
> * VM に接続します

## <a name="register-to-the-lab"></a>ラボに登録する

1. 教師から受け取った **登録 URL** に移動します。 登録完了後は、登録 URL を使用する必要はありません。 代わりに [https://labs.azure.com](https://labs.azure.com) という URL を使用します。 Internet Explorer 11 はまだサポートされていません。 

    ![ラボに登録する](./media/tutorial-connect-vm-in-classroom-lab/register-lab.png)
1. 学校アカウントを使ってサービスにサインインし、登録を完了します。 

    > [!NOTE]
    > Azure Lab Services を使用するためには Microsoft アカウントが必要です。 Yahoo アカウントや Google アカウントなど、Microsoft 以外のアカウントを使用してポータルへのサインインを試みる場合は、Microsoft 以外のアカウントにリンクする Microsoft アカウントの作成手順に従ってください。 そのうえで、登録プロセスの実施手順に従います。 
1. 登録した後、アクセスできるラボの仮想マシンが表示されることを確認します。 

    ![アクセス可能な VM](./media/tutorial-connect-vm-in-classroom-lab/accessible-vms.png)
1. 仮想マシンの準備が完了するまで待ちます。 VM タイルで次のフィールドを確認します。
    1. タイルの上部に、**ラボの名前** が表示されます。
    1. その右側には、VM の **オペレーティング システム (OS)** を表すアイコンが表示されます。 この例では Windows OS です。 
    1. タイルの進行状況バーは、ユーザーに割り当てられた[ クォータ時間](how-to-configure-student-usage.md#set-quotas-for-users)数に対して費やされた時間数を示しています。 この時間は、ラボに対してスケジュールされている時間のほかに、追加で割り当てられた時間です。 
    1. タイルの一番下には、VM の起動と停止、VM への接続に使用するアイコンとボタンが表示されます。 
    1. それらのボタンの右側には、VM の状態が表示されます。 VM の状態が **停止済み** と表示されていることを確認します。 

        ![VM が停止状態](./media/tutorial-connect-vm-in-classroom-lab/vm-in-stopped-state.png)

## <a name="start-the-vm"></a>VM を起動する
1. 次の画像に示すように、先頭のボタンを選択して、VM を **起動** します。 このプロセスには、ある程度時間がかかります。  

    ![VM を起動する](./media/tutorial-connect-vm-in-classroom-lab/start-vm.png)
4. VM の状態が **実行中** に設定されていることを確認します。 

    ![VM が実行中の状態](./media/tutorial-connect-vm-in-classroom-lab/vm-running.png)

    先頭のボタンが **停止** 操作を表すアイコンに変化していることがわかります。 このボタンを選択すると、VM を停止することができます。 

## <a name="connect-to-the-vm"></a>VM に接続します

1. ラボの VM に **接続** するには、次の画像のように 2 つ目のボタンを選択します。 

    ![VM への接続](./media/tutorial-connect-vm-in-classroom-lab/connect-vm.png)
2. 次のいずれかの手順を実行します。 
    1. **Windows** 仮想マシンの場合は、**RDP** ファイルをハード ディスクに保存します。 仮想マシンに接続するための RDP ファイルを開きます。 教師から提供された **ユーザー名** と **パスワード** を使用してマシンにサインインします。 
    3. **Linux** 仮想マシンの場合は、**SSH** または **RDP** を使用して接続できます (有効な場合)。 詳細については、[Linux マシンのリモート デスクトップ接続の有効化](how-to-enable-remote-desktop-linux.md)に関するページを参照してください。 

## <a name="next-steps"></a>次のステップ
このチュートリアルでは、教師から入手した登録リンクを使って、クラスルーム ラボにアクセスしました。

ラボの所有者として、ラボに登録されているユーザーを確認し、VM の使用状況を追跡します。 次のチュートリアルに進み、ラボの使用を追跡する方法を確認してください。

> [!div class="nextstepaction"]
> [ラボの使用状況を追跡する](tutorial-track-usage.md) 
