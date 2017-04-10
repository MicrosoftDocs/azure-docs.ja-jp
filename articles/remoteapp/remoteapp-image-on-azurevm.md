---
title: "Azure VM に基づく Azure RemoteApp イメージの作成 | Microsoft Docs"
description: "Azure 仮想マシンを使用することで Azure RemoteApp のイメージを作成する方法について説明します。"
services: remoteapp
documentationcenter: 
author: msmbaldwin
manager: mbaldwin
ms.assetid: d41583ef-6cd8-4115-8dcb-b2cd5b3d301a
ms.service: remoteapp
ms.workload: compute
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/23/2016
ms.author: mbaldwin
translationtype: Human Translation
ms.sourcegitcommit: 4f2230ea0cc5b3e258a1a26a39e99433b04ffe18
ms.openlocfilehash: efd70d9843f3d8db0c2396c79eacb230fcdfa467
ms.lasthandoff: 03/25/2017


---
# <a name="create-a-azure-remoteapp-image-based-on-an-azure-virtual-machine"></a>Azure 仮想マシンに基づく Azure RemoteApp イメージの作成
> [!IMPORTANT]
> Azure RemoteApp の提供は終了しました。 詳細については、 [お知らせ](https://go.microsoft.com/fwlink/?linkid=821148) をご覧ください。
> 
> 

Azure 仮想マシンから (コレクションで共有するアプリを保持する) Azure RemoteApp のイメージを作成できます。 また、Azure VM イメージ ギャラリーに追加された、Azure RemoteApp イメージのすべての要件を満たす仮想マシン イメージを使用することもできます。必要に応じて、この VM イメージを独自の VM の開始点として使用することができます。 ライブラリにある "Windows Server Remote Desktop Session Host" イメージを探してみましょう。

Azure VM に基づく独自のイメージを作成するには、イメージの作成と、Azure VM ライブラリから Azure RemoteApp へのイメージのアップロードという 2 つの手順があります。

## <a name="create-a-custom-image-based-on-an-azure-vm"></a>Azure VM に基づくカスタム イメージの作成
次の手順を使用して、Azure VM に基づくイメージを作成します。

1. Azure 仮想マシンを作成します。 Azure 仮想マシン イメージ ギャラリーの "Windows Server Remote Desktop Session Host" イメージまたは "Windows Server Remote Desktop Session Host with Microsoft Office 365 ProPlus" イメージを使用できます。 このイメージは、Azure RemoteApp テンプレート イメージのすべての要件を満たしています。
   
    詳細については、[Windows を実行する VM の作成](../virtual-machines/virtual-machines-windows-hero-tutorial.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)に関するページを参照してください。
2. VM に接続し、RemoteApp を使用して共有するアプリをインストールして構成します。 アプリに必要な追加の Windows の構成も実行してください。
   
    詳細については、「[Windows Server が実行されている仮想マシンにログオンする方法](../virtual-machines/windows/classic/connect-logon.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json)」を参照してください。
3. いずれかの Windows Server リモート デスクトップ セッション ホストのイメージを使用している場合は、VM が RemoteApp の前提条件を満たしていることを確認するための検証スクリプトが含まれています。 スクリプトを実行するには、デスクトップ上にある **ValidateRemoteAppImage** をダブルクリックします。 次の手順に進む前に、スクリプトによって報告されたエラーはすべて修正します。
4. SYSPREP は、イメージを一般化してキャプチャします。 手順については、[テンプレートとして使用する Windows 仮想マシンのキャプチャ方法](../virtual-machines/windows/classic/capture-image.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json)に関するページを参照してください。

## <a name="import-the-image-into-the-azure-remoteapp-image-library"></a>Azure RemoteApp イメージ ライブラリへのイメージのインポート
次の手順を使用して、新しいイメージを Azure RemoteApp にインポートします。

1. **[テンプレート イメージ]** タブで次の操作を実行します。
   
   * 既存のイメージがない場合は、 **[テンプレート イメージのアップロードまたはインポート]**をクリックします。
   * 既に 1 つ以上のイメージがある場合は、 **+** をクリックして新しいイメージを追加します。
2. **[Virtual Machines ライブラリからイメージをインポートします]** を選択し、**[次へ]** をクリックします。
3. 次のページで、一覧からカスタム イメージを選択し、イメージを作成したときに表示された手順に従っていることを確認します。 **[次へ]**をクリックします。
4. 新しい RemoteApp イメージの名前を入力して場所を選択したら、インポート プロセスを開始するチェックマークをクリックします。

> [!NOTE]
> Azure Virtual Machines でサポートされている Azure の任意の場所から、Azure RemoteApp でサポートされている Azure の任意の場所に、イメージをインポートできます。 インポート時間は場所によって異なり、最大 25 分かかります。
> 
> 

これで、ニーズに応じて新しいコレクション ([クラウド](remoteapp-create-cloud-deployment.md) コレクションか[ハイブリッド](remoteapp-create-hybrid-deployment.md) コレクションのいずれか) を作成する準備ができました。


