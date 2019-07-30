---
title: Azure Lab Services で VM のパスワードを設定する | Microsoft Docs
description: Azure Lab Services のクラスルーム ラボで仮想マシン (VM) のパスワードを設定およびリセットする方法について説明します。
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
ms.date: 06/11/2019
ms.author: spelluru
ms.openlocfilehash: c1564fadef35a20d0d87db8439ae1cc3dc923318
ms.sourcegitcommit: 22c97298aa0e8bd848ff949f2886c8ad538c1473
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/14/2019
ms.locfileid: "67144089"
---
# <a name="set-or-reset-password-for-virtual-machines-in-classroom-labs"></a>クラスルーム ラボで仮想マシンのパスワードを設定またはリセットする
この記事では、クラスルーム ラボで VM にアクセスするためのパスワードを設定およびリセットするさまざまな方法について説明します。 

## <a name="lab-owners-teachers"></a>ラボ所有者 (教師)
ラボ所有者 (教師) は、ラボの作成時 (ラボ作成ウィザードで) またはラボの作成後に (ダッシュボードで) VM のパスワードを設定/リセットできます。 

### <a name="set-password-at-the-time-of-lab-creation"></a>ラボの作成時にパスワードを設定する
ラボ所有者 (教師) は、ラボ作成ウィザードの **[資格情報の設定]** ページでラボ内の VM のパスワードを設定できます。

![資格情報の設定](../media/tutorial-setup-classroom-lab/set-credentials.png)

このページの **[Use same password for all virtual machines]\(すべての仮想マシンに同じパスワードを使用する\)** オプションを有効/無効にすることで、教師はラボ内のすべての VM に同じパスワードを使用するか、学生が自分の VM にパスワードを設定できるようにします。 既定でこの設定は、Ubuntu を除くすべての Windows および Linux オペレーティング システム イメージで有効です。 この設定を無効にすると、学生が初めて VM に接続しようとしたときにパスワードを設定するように求められます。 

ラボ所有者は、必要に応じてラボ作成ウィザードの **[Configure template]\(テンプレートの構成\)** ページでこのパスワードをリセットできます。 

![完了後の [Configure template]\(テンプレートの構成\) ページ](../media/tutorial-setup-classroom-lab/configure-template-after-complete.png)

ラボ所有者は、ラボの作成後にダッシュボードでパスワードをリセットすることもできます。 

### <a name="reset-password-on-the-dashboard"></a>ダッシュボードのパスワードのリセット

1. ラボ タイルのオーバーフロー メニュー (縦 3 つのドット) を選択し、 **[Reset password]\(パスワードのリセット\)** を選択します。 

    ![ホーム ページのパスワードのリセット メニュー](../media/how-to-set-virtual-machine-passwords/reset-password-menu-dashboard.png)
1. **[Set password]\(パスワードの設定\)** ダイアログ ボックスでパスワードを入力し、 **[Set password]\(パスワードの設定\)** を選択します。
    
    ![[Set password]\(パスワードの設定\) ダイアログ ボックス](../media/how-to-set-virtual-machine-passwords/set-password.png)

## <a name="lab-users-students"></a>ラボ ユーザー (学生)
ラボの作成時に、ラボ所有者は **[Use same password for all virtual machines]\(すべての仮想マシンに同じパスワードを使用する\)** を有効または無効にすることができます。 このオプションが有効な場合、学生はパスワードをリセットできません。 ラボ内のすべての VM のパスワードは、教師が設定したパスワードと同じです。 

このオプションを無効にすると、ユーザーが初めて VM に接続するときにパスワードを設定する必要があります。 ユーザー (学生) が **[My virtual machines]\(自分の仮想マシン\)** ページのラボ タイルで **[Connect]\(接続\)** ボタンを選択すると、VM のパスワードを設定する次のダイアログ ボックスが表示されます。 

![学生のパスワードのリセット](../media/how-to-set-virtual-machine-passwords/student-set-password.png)

学生は、ラボ タイルのオーバーフロー メニュー (**縦の 3 つのドット**) をクリックして、 **[Reset password]\(パスワードのリセット\)** を選択してパスワードを設定することもできます。 

## <a name="next-steps"></a>次の手順
(ラボ所有者として) 構成できる他の学生の使用オプションの詳細については、次の記事を参照してください。[学生の使用の構成](how-to-configure-student-usage.md)
