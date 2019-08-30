---
title: Azure Lab Services の Linux 向けリモート デスクトップを有効にする | Microsoft Docs
description: Azure Lab Services のラボで Linux 仮想マシン向けリモート デスクトップを有効にする方法について説明します。
services: lab-services
documentationcenter: na
author: spelluru
manager: ''
editor: ''
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/20/2019
ms.author: spelluru
ms.openlocfilehash: 3d08105e78274300eb7ee0a8c0ad146a737d0ffa
ms.sourcegitcommit: bb8e9f22db4b6f848c7db0ebdfc10e547779cccc
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/20/2019
ms.locfileid: "69644948"
---
# <a name="enable-remote-desktop-for-linux-virtual-machines-in-a-lab-in-azure-lab-services"></a>Azure Lab Services のラボの Linux 仮想マシンでリモート デスクトップを有効にする
この記事では、次のタスクの手順について説明します。

- Linux VM のリモート デスクトップを有効にする
- 教師がリモート デスクトップ接続 (RDP) 経由でテンプレート VM に接続する

## <a name="enable-remote-desktop-for-linux-vm"></a>Linux VM のリモート デスクトップを有効にする
教師はラボの作成時に **Linux** イメージの**リモート デスクトップ接続**を有効にすることができます。 テンプレートに対して Linux イメージが選択されていると、 **[Enable Remote Desktop Connection]\(リモート デスクトップ接続を有効にする\)** オプションが表示されます。 このオプションが有効になっていると、教師が RDP (リモート デスクトップ) を介してテンプレート VM と学生用 VM に接続することができます。 

![Linux イメージのリモート デスクトップ接続を有効にする](../media/how-to-enable-remote-desktop-linux/enable-rdp-option.png)

**[Enabling Remote Desktop Connection]\(リモート デスクトップ接続の有効化\)** メッセージ ボックスで、 **[Continue with Remote Desktop]\(リモート デスクトップで続行\)** を選択します。 

![Linux イメージのリモート デスクトップ接続を有効にする](../media/how-to-enable-remote-desktop-linux/enabling-remote-desktop-connection-dialog.png)

> [!IMPORTANT] 
> **リモート デスクトップ接続**を有効にすると、Linux マシンの **RDP** ポートのみが開きます。 教師は最初に SSH を使用して Linux マシンに接続し、RDP と GUI のパッケージをインストールします。これで、後で RDP を使用して Linux マシンに接続できるようになります。 次に、学生が学生の Linux VM にリモート デスクトップ接続できるように、イメージを**発行**します。 

## <a name="supported-operating-systems"></a>サポートされているオペレーティング システム
現時点では、次のオペレーティング システムのリモート デスクトップ接続がサポートされています。

- openSUSE Leap 42.3
- CentOS-based 7.5
- Debian 9 "Stretch"
- Ubuntu Server 16.04 LTS

## <a name="teachers-connecting-to-the-template-vm-using-rdp"></a>教師が RDP を使用してテンプレート VM に接続する
教師は最初に SSH を使用してテンプレート VM に接続し、これに RDP と GUI のパッケージをインストールする必要があります。 これで、教師は次の手順で RDP を使用して Linux VM に接続できるようになります。 

ラボの作成時に、テンプレート VM に接続するための **[リモート デスクトップ]** オプションが表示されます。 

![作成時に RDP 経由でテンプレートに接続する](../media/how-to-enable-remote-desktop-linux/connect-at-creation.png)

ラボが作成され、テンプレート VM が開始すると、ラボのホーム ページに **[リモート デスクトップ]** オプションが表示されます。 テンプレート VM がまだ開始されていない場合は、開始します。 

![ラボの作成後に RDP 経由でテンプレートに接続する](../media/how-to-enable-remote-desktop-linux/rdp-after-lab-creation.png) 

SSH または RDP を使用した VM への接続の詳細については、[SSH または RDP を使用して接続する](#connect-using-ssh-or-rdp) を参照してください。 

## <a name="teachers-connecting-to-a-student-vm-using-rdp"></a>教師が RDP を使用して学生用 VM に接続する
教師/教授は、 **[仮想マシン]** 表示に切り替えて **[接続]** アイコンを選択すると学生用 VM に接続できます。 その前に、テンプレート イメージを、これにインストールされている RDP と GUI のパッケージを使用して**発行**する必要があります。 

![教師が学生用 VM に接続する](../media/how-to-enable-remote-desktop-linux/teacher-connect-to-student-vm.png)

SSH または RDP を使用した VM への接続の詳細については、[SSH または RDP を使用して接続する](#connect-using-ssh-or-rdp) を参照してください。 

## <a name="connect-using-ssh-or-rdp"></a>SSH または RDP を使用して接続する
**[SSH]** オプションを選択した場合は、次の **[仮想マシンに接続する]** ダイアログ ボックスが表示されます。  

![SSH 接続文字列](../media/how-to-enable-remote-desktop-linux/ssh-connection-string.png)

テキスト ボックスの横にある **[コピー]** ボタンを選択して、クリップボードにコピーします。 SSH 接続文字列を保存します。 仮想マシンに接続するには、SSH ターミナル ([Putty](https://www.putty.org/) など) からこの接続文字列を使用します。

**[RDP]** オプションを選択した場合は、RDP ファイルがお使いのコンピューターにダウンロードされます。 それを保存してから開いて、マシンに接続します。 

## <a name="next-steps"></a>次の手順
リモート デスクトップ接続機能をインストラクターが有効にした後で、学生が RDP と SSH 経由でその VM に接続することはできません。 詳細については、[クラスルーム ラボの Linux VM でリモート デスクトップを使用する方法](how-to-use-remote-desktop-linux-student.md)に関するページを参照してください。 