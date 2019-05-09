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
ms.date: 02/07/2019
ms.author: spelluru
ms.openlocfilehash: bc5c12d4bb92edaafcc9808da8c48106a6e0cbd5
ms.sourcegitcommit: 031e4165a1767c00bb5365ce9b2a189c8b69d4c0
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/13/2019
ms.locfileid: "59548043"
---
# <a name="how-to-access-a-classroom-lab-in-azure-lab-services"></a>Azure Lab Services でクラスルーム ラボにアクセスする方法
この記事では、クラスルーム ラボにアクセスし、ラボの VM に接続して、VM を停止する方法について説明します。 

## <a name="register-to-a-lab"></a>ラボに登録する
1. 教師から受け取った**登録 URL** に移動します。 
2. 学校アカウントを使ってサービスにサインインし、登録を完了します。 
3. 登録した後、アクセスできるラボの仮想マシンが表示されることを確認します。 
2. 仮想マシンの準備が完了するのを待って VM を**起動**します。 このプロセスには、ある程度時間がかかります。  

    ![VM を起動する](../media/tutorial-connect-vm-in-classroom-lab/start-vm.png)


## <a name="view-all-the-classroom-labs"></a>すべてのクラスルーム ラボを表示する
ラボへの登録後は、次の手順に従ってクラスルーム ラボを表示できます。 

1. [https://labs.azure.com](https://labs.azure.com) に移動します。 Internet Explorer 11 はまだサポートされていないことに注意してください。 
2. ラボへの登録に使用したユーザー アカウントを使用してサービスにサインインします。 
3. 自分にアクセス権があるすべてのラボが表示されることを確認します。 

    ![すべてのラボを表示する](../media/how-to-use-classroom-lab/all-labs.png)

## <a name="connect-to-the-virtual-machine-in-a-classroom-lab"></a>クラスルーム ラボの仮想マシンに接続する

1. VM をまだ起動していない場合は起動し、タイルで **[開始]** を選択します。 
2. アクセスするラボの仮想マシンを表すタイルの **[接続]** を選びます。 
3. 次のいずれかの手順を実行します。 
   1. **Windows** 仮想マシンの場合は、**RDP** ファイルをハード ディスクに保存します。 仮想マシンに接続するための RDP ファイルを開きます。 マシンにログインするために教師から提供された**ユーザー名**と**パスワード**を使います。 
   3. **Linux** 仮想マシンの場合は、**[仮想マシンに接続する]** ダイアログ ボックス上で SSH 接続文字列をコピーして保存します。 仮想マシンに接続するには、SSH ターミナル ([Putty](https://www.putty.org/) など) からこの接続文字列を使用します。

## <a name="stop-the-virtual-machine-in-a-classroom-lab"></a>クラスルーム ラボの仮想マシンを停止する

VM を停止するには、タイルの **[停止]** を選択します。 VM が停止すると、タイルの **[開始]** ボタンが有効になります。 

## <a name="next-steps"></a>次の手順
次の記事を参照してください。

- [管理者としてラボ アカウントを作成および管理する](how-to-manage-lab-accounts.md)
- [ラボ所有者としてラボを作成および管理する](how-to-manage-classroom-labs.md)
- [ラボ所有者としてテンプレートを設定および発行する](how-to-create-manage-template.md)
- [ラボ所有者としてラボの使用を構成および制御する](how-to-configure-student-usage.md)
 