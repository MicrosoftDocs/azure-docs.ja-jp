---
title: Azure でアプリをテストする方法 | Microsoft Docs
description: ラボでファイル共有を作成し、ラボ内のローカル コンピューターと仮想マシンにマウントし、デスクトップまたは web アプリケーションをファイル共有にデプロイしてテストする方法について説明します。
services: devtest-lab,virtual-machines,lab-services
documentationcenter: na
author: spelluru
manager: femila
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/04/2018
ms.author: spelluru
ms.openlocfilehash: 9ca1c4a1ed1841f82bc386cff48315dd15ef91bb
ms.sourcegitcommit: 90c6b63552f6b7f8efac7f5c375e77526841a678
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/23/2019
ms.locfileid: "56730411"
---
# <a name="test-your-app-in-azure"></a>Azure でアプリをテストする 
このアーティクルでは、DevTest Labs を使用して Azure のアプリケーションをテストするための手順を提供します。 最初に、ラボ内のファイル共有を設定して、ローカル開発用コンピューターとラボ内の VM にドライブとしてマウントします。 次に、Visual Studio 2017 を使用して、ラボの VM でアプリを実行できるように、ファイル共有にアプリをデプロイします。  

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="prerequisites"></a>前提条件 
1. サブスクリプションをまだ持っていない場合は[Azure サブスクリプションを作成](https://azure.microsoft.com/free/)し、 [Azure portal](https://portal.azure.com)にサインインします。
2. [このアーティクル](devtest-lab-create-lab.md)の手順に従い、Azure DevTest Labs を使用してラボを作成します。 次回サインインした時に簡単に見つけられるように、ラボをダッシュボードにピン留めします。 Azure DevTest Labs では、無駄を最小限に抑えて原価を管理することで、 Azure 内のリソースを素早く作成することができます。 DevTest Labs の詳細はについては、 [概要](devtest-lab-overview.md)を参照してください。 
3. [ストレージ アカウントを作成する](../storage/common/storage-create-storage-account.md)アーティクルの手順に従い、ラボのリソース グループ内に Azure Storage アカウントを作成します。 **ストレージ アカウントを作成する**ページで、**リソース グループ**用に**Use existing**を選択し、**ラボのリソース グループ**を選択します。 
4. [Azure Files にファイル共有を作成する](../storage/files/storage-how-to-create-file-share.md)アーティクル内の手順に従い、Azure storage 内にファイル共有を作成します。 

## <a name="mount-the-file-share-on-your-local-machine"></a>ローカル コンピューターにファイル共有をマウントする
1. ローカル コンピューター上で、[Windows で Azure ファイル共有を使用する](../storage/files/storage-how-to-use-files-windows.md)アーティクルの[Windows で Azure ファイル共有の資格情報を保持する](../storage/files/storage-how-to-use-files-windows.md#persisting-azure-file-share-credentials-in-windows)セクションのスクリプトを使用します。 
2. 次に、`net use`コマンドを使用して、コンピューターにファイル共有をマウントします。 サンプル コマンドを次に示します:コマンドを実行する前に、ご自分の Azure ストレージ名とファイル共有名を指定します。 

    `net use Z: \\<YOUR AZURE STORAGE NAME>.file.core.windows.net\<YOUR FILE SHARE NAME> /persistent:yes`

## <a name="create-a-vm-in-the-lab"></a>ラボで VM を作成する
1. **ファイル共有**ページで、上部にある階層リンクメニューの**リソース グループ**を選択します。 **リソース グループ** ページが表示されます。 
    
    ![階層リンク メニューからリソース グループを選択する](media/test-app-in-azure/select-resource-group-bread-crump.png)
2. **リソース グループ**ページで、DevTest Labs で作成した**ラボ**を選択します。

    ![ラボを選ぶ](media/test-app-in-azure/select-devtest-lab-in-resource-group.png)
3. ラボの**DevTest Lab**ページで、ツール バーの **+ 追加** を選びます。 

    ![ラボのボタンを追加する](media/test-app-in-azure/add-button-in-lab.png)
4. **ベースの選択**ページで、**smalldisk**を検索して **[smalldisk] Windows Server 2016 Data Center**を選択します。 

    ![Windows server のスモールディスクを選択する](media/test-app-in-azure/choose-small-disk-windows-server.png)
5. **仮想マシン**ページで、**仮想マシン名**、**ユーザー名**、**パスワード**を指定し、**作成**を選択します。    
    
    ![仮想マシン ページの作成](media/test-app-in-azure/create-virtual-machine-page.png)    

## <a name="mount-the-file-share-on-your-vm"></a>VM にファイル共有をマウントする
1. 仮想マシンが正常に作成されたら、一覧から**仮想マシン**を選択します。    

    ![ラボ VM を選ぶ](media/test-app-in-azure/select-lab-vm.png)
2. ツールバーの**接続する**を選択して VM に接続します。 
3. [Azure PowerShell をインストールします](/powershell/azure/install-az-ps)。
4. ファイル共有のマウントに関するセクションの手順に従ってください。 

## <a name="publish-your-app-from-visual-studio"></a>Visual Studio から アプリを発行する
このセクションでは、Visual Studio からクラウド内のテスト VM にアプリを発行します。

1. Visual Studio 2017 を使用して、デスクトップや web アプリケーションを作成します。
2. アプリを作成します。
3. アプリを発行するには、**ソリューション エクスプローラー**でプロジェクトを右クリックし、**発行** を選択します。 
4. **発行ウィザード**内で、ファイル共有にマップされている**ドライブ**を入力します。

    **デスクトップ アプリ:**

    ![デスクトップ アプリ](media/test-app-in-azure/desktop-app.png)

    **Web アプリ:**

    ![Web アプリ](media/test-app-in-azure/web-app.png)

1. **次へ**を選択して発行ワークフローを完了し、**完了**を選択します。 ウィザードの手順が完了したら、Visual Studio はアプリケーションをビルドし、ファイル共有に発行します。 


## <a name="test-the-app-on-your-test-vm-in-the-lab"></a>ラボのテスト VM でアプリをテストする

1. ラボで VM の仮想マシンのページに移動します。 
2. VM が停止状態の場合、ツールバーの**Start**を選択して VM を起動します。 毎回のスタートや停止を回避するために、VM の自動スタートと自動シャットダウンポリシーを設定できます。 
3. **[接続]** を選択します。

    ![仮想マシン ページ](media/test-app-in-azure/virtual-machine-page.png)
4. 仮想マシン内で**ファイル エクスプローラー**を起動させ、**この PC**を選択してファイル共有を検索します。

    ![VM 上の共有を見つける](media/test-app-in-azure/find-share-on-vm.png)

    > [!NOTE]
    > 何らかの理由で、仮想マシン上またはローカル コンピューターでファイル共有を見つけられない場合、`net use`コマンドを実行することで再マウントできます。 `net use`コマンドはAzure Portal内の**ファイル共有**の**接続**ウィザード上にあります。
1. ファイル共有を開き、Visual Studio からデプロイされたアプリが表示されることを確認します。 

    ![共有を VM 上で開く](media/test-app-in-azure/open-file-share.png)

    Azure で作成した テスト VM 内でアプリにアクセスしテストできるようになりました。

## <a name="next-steps"></a>次の手順
ラボで VM を使用する方法については次のアーティクルを参照してください。 

- [VM をラボに追加する](devtest-lab-add-vm.md)
- [ラボ VM を再起動する](devtest-lab-restart-vm.md)
- [ラボ VM のサイズを変更する](devtest-lab-resize-vm.md)
