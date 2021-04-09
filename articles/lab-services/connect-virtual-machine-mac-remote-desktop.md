---
title: Mac から Azure Lab Services VM に接続する方法 | Microsoft Docs
description: Azure Lab Services で Mac から仮想マシンに接続する方法について説明します。
ms.topic: article
ms.date: 06/26/2020
ms.openlocfilehash: 674bea13093e185ae991148c2cf96599a273d0bc
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "85444626"
---
# <a name="connect-to-a-vm-using-remote-desktop-protocol-on-a-mac"></a>Mac でリモート デスクトップ プロトコル (RDP) を使用して VM に接続する
このセクションでは、Mac から RDP を使用してクラスルーム ラボ VM に接続する方法を学生向けに説明します。

## <a name="install-microsoft-remote-desktop-on-a-mac"></a>Microsoft リモート デスクトップを MAC にインストールする
1. MAC で App Store を開き、**Microsoft リモート デスクトップ** を検索します。

    ![Microsoft リモート デスクトップ](./media/how-to-use-classroom-lab/install-ms-remote-desktop.png)
1. 最新バージョンの Microsoft リモート デスクトップをインストールします。 

## <a name="access-the-vm-from-your-mac-using-rdp"></a>MAC から RDP を使用して VM にアクセスする
1. **Microsoft リモート デスクトップ** がインストールされているコンピューターで、ダウンロードした **RDP** ファイルを開きます。 VM への接続が開始されます。 

    ![VM への接続](./media/how-to-use-classroom-lab/connect-linux-vm.png)
1. 次の警告が表示された場合は、 **[Continue]\(続行\)** を選択します。 

    ![証明書の警告](./media/how-to-use-classroom-lab/certificate-error.png)
1. VM が表示されます。 

    > [!NOTE]
    > 次に示したのは、CentOS Linux VM の例です。 

    ![VM](./media/how-to-use-classroom-lab/vm-ui.png)


## <a name="next-steps"></a>次のステップ
RDP を使用して Linux VM に接続する方法については、[Linux 仮想マシン用リモート デスクトップの使用](how-to-use-remote-desktop-linux-student.md)に関するページをご覧ください


