---
title: "Azure クイック スタート - ポータルで Windows VM を作成する | Microsoft Docs"
description: "Azure クイック スタート - ポータルで Windows VM を作成する"
services: virtual-machines-windows
documentationcenter: virtual-machines
author: neilpeterson
manager: timlt
editor: tysonn
tags: azure-resource-manager
ms.assetid: 
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: hero-article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.date: 03/21/2017
ms.author: nepeters
translationtype: Human Translation
ms.sourcegitcommit: 424d8654a047a28ef6e32b73952cf98d28547f4f
ms.openlocfilehash: a13ac5ab425ccbbe53d77cb9f5a8ebf02d009370
ms.lasthandoff: 03/22/2017

---

# <a name="create-a-windows-virtual-machine-with-the-azure-portal"></a>Azure Portal で Windows 仮想マシンを作成する

Azure 仮想マシンは、Azure Portal で作成できます。 この方法では、ブラウザーベースのユーザー インターフェイスで仮想マシンとそれに関連するすべてのリソースを作成して構成できます。 このクイック スタートでは、Azure Portal を使用して仮想マシンを作成する方法について説明しています。 

Azure サブスクリプションをお持ちでない場合は、開始する前に [無料アカウント](https://azure.microsoft.com/free/) を作成してください。

## <a name="log-in-to-azure"></a>Azure へのログイン 

Azure Portal (http://portal.azure.com) にログインします。

## <a name="create-virtual-machine"></a>仮想マシンの作成

2. Azure Portal の左上隅にある **[新規]** ボタンをクリックします。

3. **[新規]** ブレードの **[Compute]** を選択し、**[Compute]** ブレードの **[Windows Server 2016 Datacenter]** を選択して、**[作成]** ボタンをクリックします。

4. 仮想マシンの **[基本]** フォームに入力します。 ここに入力したユーザー名とパスワードが、仮想マシンへのログインに使用されます。 **[リソース グループ]** では、新しいグループを作成します。 リソース グループとは、Azure リソースの作成と一括管理に使用する論理コンテナーです。 完了したら、**[OK]** をクリックします。

    ![ポータルのブレードで VM に関する基本情報を入力する](./media/virtual-machine-quick-start/create-windows-vm-portal-basic-blade.png)  

5. VM のサイズを選択し、**[選択]** をクリックします。 

6. 設定ブレードの **[管理ディスクを使用]** で **[はい]** を選択し、他の設定は既定のままにして、**[OK]** をクリックします。

7. 概要ページで **[OK]** をクリックして、仮想マシンのデプロイを開始します。

8. デプロイの状態を監視するには、仮想マシンをクリックします。 VM は、Azure Portal ダッシュボードに表示されます。または、左側のメニューで **[Virtual Machines]** を選択すると表示されます。 VM が作成されると、状態は **[デプロイ中]** から **[実行中]** に変わります。

## <a name="connect-to-virtual-machine"></a>仮想マシンへの接続

デプロイが完了したら、仮想マシンとのリモート デスクトップ接続を作成します。

1. 仮想マシンのブレードで、**[接続]** ボタンをクリックします。 リモート デスクトップ プロトコル ファイル (.rdp ファイル) が作成されてダウンロードされます。

    ![ポータル 9](./media/virtual-machine-quick-start/portal-quick-start-9.png) 

2. VM に接続するには、ダウンロードした RDP ファイルを開きます。 メッセージが表示されたら、**[接続]** をクリックします。 Mac では、この[リモート デスクトップ クライアント](https://itunes.apple.com/us/app/microsoft-remote-desktop/id715768417?mt=12)のような RDP クライアントを Mac App Store から入手する必要があります。

3. 仮想マシンの作成時に指定したユーザー名とパスワードを入力し、**[OK]** をクリックします。

4. ログイン処理中に証明書の警告が表示される場合があります。 **[はい]** または **[続行]** をクリックして接続処理を続行します。

## <a name="delete-virtual-machine"></a>仮想マシンの削除

必要がなくなったら、リソース グループ、仮想マシン、すべての関連リソースを削除します。 そのためには、仮想マシン ブレードでリソース グループを選択し、**[削除]** をクリックします。

## <a name="next-steps"></a>次のステップ

[ロールのインストールとファイアウォールの構成のチュートリアル](./virtual-machines-windows-hero-role.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)

[VM デプロイ CLI サンプルを探索する](./virtual-machines-windows-cli-samples.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)
