---
title: Microsoft Azure ベースの仮想マシンに接続する | Azure Marketplace
description: Azure に作成された新しい仮想マシンに接続する方法について説明します。
services: Azure, Marketplace, Cloud Partner Portal,
author: v-miclar
ms.service: marketplace
ms.topic: article
ms.date: 10/19/2018
ms.author: pabutler
ms.openlocfilehash: ca002a77fd7252466f604b995af949b54694bc8c
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/13/2019
ms.locfileid: "64938574"
---
# <a name="connect-to-your-azure-based-virtual-machine"></a>Azure ベースの仮想マシンに接続する

この記事では、Azure に作成した仮想マシン (VM) に接続してサインインする方法について説明します。  接続に成功した後は、ホスト サーバーにローカルからログオンしている感覚で VM を操作することができます。 

## <a name="connect-to-a-windows-based-vm"></a>Windows ベースの VM に接続する

Azure にホストされている Windows ベースの VM には、リモート デスクトップ クライアントを使用して接続することになります。  ほとんどのバージョンの Windows は、リモート デスクトップ プロトコル (RDP) をネイティブでサポートしています。  その他のマシンのクライアントについて詳しくは、「[リモート デスクトップ クライアント](https://docs.microsoft.com/windows-server/remote/remote-desktop-services/clients/remote-desktop-clients)」を参照してください。  

次の記事では、組み込みの Windows RDP サポートを使用して VM に接続する方法について詳しく説明しています。[Windows が実行されている Azure 仮想マシンに接続してログオンする方法](../../../virtual-machines/windows/connect-logon.md)。  

>[!TIP]
> このプロセス中にセキュリティ警告が表示されることがあります (.rdp ファイルの発行元が不明、ユーザーの資格情報を確認できないなど)。  これらの警告は無視しても問題ありません。


## <a name="connect-to-a-linux-based-vm"></a>Linux ベースの VM に接続する

Linux ベースの VM に接続するには、Secure Shell プロトコル (SSH) クライアントが必要です。  ここでは、無料の [PuTTY](https://www.ssh.com/ssh/putty/) SHH ターミナルを使用します。

1. [Azure portal](https://ms.portal.azure.com) の **[仮想マシン]** ブレードで、接続先の VM を選択します。  
2. まだ VM が稼働していない場合は、VM を**起動**します。
3. VM の名前をクリックしてその **[概要]** ページを開きます。
4. VM のパブリック IP アドレスと DNS 名をメモします。  これらの値が設定されていない場合は、[ネットワーク インターフェイスを作成](https://docs.microsoft.com/azure/virtual-network/virtual-network-network-interface#create-a-network-interface)する必要があります。

   ![VM の [概要] の設定](./media/publishvm_019.png)
 
5. PuTTY アプリケーションを開きます。  
6. [PuTTY Configuration]\(PuTTY 構成\) ダイアログで、VM の IP アドレスまたは DNS 名を入力します。 

   ![PuTTY ターミナルの設定](./media/publishvm_020.png)
 
7. **[Open]\(開く\)** をクリックして PuTTY ターミナルを開きます。  
8. プロンプトが表示されたら、Linux VM のアカウント名とパスワードを入力します。 

   接続に問題がある場合は、SSH クライアントのドキュメント、たとえば「[Chapter 10:Common error messages (第 10 章: 一般的なエラー メッセージ)](https://www.ssh.com/ssh/putty/putty-manuals/0.68/Chapter10.html#errors)」を参照してください。

プロビジョニング済みの Linux VM にデスクトップを追加する方法など、詳細については、「[リモート デスクトップをインストールして Azure の Linux VM に接続するように構成する](../../../virtual-machines/linux/use-remote-desktop.md)」を参照してください。


## <a name="stop-unused-vms"></a>使用していない VM を停止する
Azure では、実行中 "*またはアイドル状態*" の VM のホスティングに関して料金が発生します。  したがって、使用していないときには VM を停止するようお勧めします。  たとえば、テストやバックアップ用の VM、不使用になった VM はシャットダウンの候補となります。 VM をシャットダウンするには、次の手順を実行します。

1. **[仮想マシン]** ブレードで、停止する VM を選択します。 
2. ページの上部付近にあるツール バーの **[停止]** ボタンをクリックします。

   ![VM の停止](./media/publishvm_018.png)

ほどなくして Azure の "*割り当て解除*" と呼ばれるプロセスで VM が停止されます。このプロセスにより、VM 上のオペレーティング システムがシャットダウンされるだけでなく、VM にプロビジョニングされていたハードウェア リソースとネットワーク リソースも解放されます。

停止した VM を後で再度有効にしたければ、その VM を選択して、 **[Start]\(スタート\)** ボタンをクリックしてください。


## <a name="next-steps"></a>次の手順

リモート接続に成功したら、[VM を構成](./cpp-configure-vm.md)する作業に取り組んでみましょう。
