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
ms.date: 03/28/2019
ms.author: spelluru
ms.openlocfilehash: 6985bd0bbae858ad258e723ef4d6d6d687b2c86e
ms.sourcegitcommit: bf509e05e4b1dc5553b4483dfcc2221055fa80f2
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/22/2019
ms.locfileid: "60005351"
---
# <a name="enable-and-use-remote-desktop-for-linux-virtual-machines-in-a-lab-in-azure-lab-services"></a>Azure Lab Services のラボで Linux 仮想マシン向けリモート デスクトップを有効にして使用する
この記事では、次のタスクの手順について説明します。

- Linux VM のリモート デスクトップを有効にする
- 教師がリモート デスクトップ接続 (RDP) 経由でテンプレート VM に接続する
- 学生が RDP 経由で学生用 VM に接続する

## <a name="enable-remote-desktop-for-linux-vm"></a>Linux VM のリモート デスクトップを有効にする
教師はラボの作成時に **Linux** イメージの**リモート デスクトップ接続**を有効にすることができます。 テンプレートに対して Linux イメージが選択されていると、**[Enable Remote Desktop Connection]\(リモート デスクトップ接続を有効にする\)** オプションが表示されます。 このオプションが有効になっていると、教師が RDP (リモート デスクトップ) を介してテンプレート VM と学生用 VM に接続することができます。 

![Linux イメージのリモート デスクトップ接続を有効にする](../media/how-to-enable-remote-desktop-linux/enable-rdp-option.png)

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

**[RDP]** オプションを選択すると、RDP ファイルがダウンロードされます。 これを開き、Linux マシンに接続します。 

## <a name="teachers-connecting-to-a-student-vm-using-rdp"></a>教師が RDP を使用して学生用 VM に接続する
ラボ所有者 (教師/教授) は、**[仮想マシン]** 表示に切り替えて **[接続]** アイコンを選択すると学生用 VM に接続できます。 その前に、テンプレート イメージを、これにインストールされている RDP と GUI のパッケージを使用して**発行**する必要があります。 

![教師が学生用 VM に接続する](../media/how-to-enable-remote-desktop-linux/teacher-connect-to-student-vm.png)

## <a name="students-connecting-to-the-student-vm"></a>学生が学生用 VM に接続する
ラボ所有者 (教師/教授) がマシンにインストールされた RDP と GUI のパッケージを使用してテンプレート VM を**発行**すると、学生が学生の Linux VM にリモート デスクトップ接続できるようになります。 手順は次のようになります。 

1. 学生がラボのポータルに直接 (`http://labs.azure.com`)、または登録リンク (`http://labs.azure.com/register/<registrationCode>`) を使用してサインインすると、学生がアクセスできる各ラボのタイルが表示されます。 
2. タイル上で、VM が停止している場合は **[開始]** を選択します。 
3. **[接続]** を選択します。 このアクションにより、マシン上に RDP ファイルがダウンロードされます。 これを保存して開き、RDP を介して Linux マシンに接続します。 

    ![学生用 VM - RDP のダウンロード](../media/how-to-enable-remote-desktop-linux/student-rdp-download.png)

    引き続き SSH を使用して Linux VM に接続できます。 SSH のオプションを表示するには、**[... (省略記号)]** を選択します。 
    
    ![学生用 VM - SSH](../media/how-to-enable-remote-desktop-linux/student-ssh.png)

    **[仮想マシンに接続する]** ダイアログ ボックス上で SSH 接続文字列をコピーして保存します。 仮想マシンに接続するには、SSH ターミナル ([Putty](https://www.putty.org/) など) からこの接続文字列を使用します。 

## <a name="next-steps"></a>次の手順
次の記事を参照してください。

- [管理者としてラボ アカウントを作成および管理する](how-to-manage-lab-accounts.md)
- [ラボ所有者としてラボを作成および管理する](how-to-manage-classroom-labs.md)
- [ラボ所有者としてテンプレートを設定および発行する](how-to-create-manage-template.md)
- [ラボ ユーザーとしてクラスルーム ラボにアクセスする](how-to-use-classroom-lab.md)

