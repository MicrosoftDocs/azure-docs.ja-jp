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
ms.date: 08/20/2019
ms.author: spelluru
ms.openlocfilehash: a4cb2abec429a790f493f95d3d16b2ff7b3eb445
ms.sourcegitcommit: bb8e9f22db4b6f848c7db0ebdfc10e547779cccc
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/20/2019
ms.locfileid: "69645027"
---
# <a name="set-or-reset-password-for-virtual-machines-in-classroom-labs-instructor"></a>クラスルーム ラボの仮想マシンに対するパスワードを設定またはリセットする (インストラクター)
ラボ所有者 (教師) は、ラボの作成時 (ラボ作成ウィザードで) またはラボの作成後に (ダッシュボードで) VM のパスワードを設定/リセットできます。 

## <a name="set-password-at-the-time-of-lab-creation"></a>ラボの作成時にパスワードを設定する
ラボ所有者 (教師) は、ラボ作成ウィザードの **[資格情報の設定]** ページでラボ内の VM のパスワードを設定できます。

![資格情報の設定](../media/tutorial-setup-classroom-lab/set-credentials.png)

このページの **[Use same password for all virtual machines]\(すべての仮想マシンに同じパスワードを使用する\)** オプションを有効/無効にすることで、教師はラボ内のすべての VM に同じパスワードを使用するか、学生が自分の VM にパスワードを設定できるようにします。 既定でこの設定は、Ubuntu を除くすべての Windows および Linux オペレーティング システム イメージで有効です。 この設定を無効にすると、学生が初めて VM に接続しようとしたときにパスワードを設定するように求められます。 

ラボ所有者は、必要に応じてラボ作成ウィザードの **[Configure template]\(テンプレートの構成\)** ページでこのパスワードをリセットできます。 

![完了後の [Configure template]\(テンプレートの構成\) ページ](../media/tutorial-setup-classroom-lab/configure-template-after-complete.png)

ラボ所有者は、ラボの作成後にダッシュボードでパスワードをリセットすることもできます。 

## <a name="reset-password-on-the-dashboard"></a>ダッシュボードのパスワードのリセット

1. ラボ タイルのオーバーフロー メニュー (縦 3 つのドット) を選択し、 **[Reset password]\(パスワードのリセット\)** を選択します。 

    ![ホーム ページのパスワードのリセット メニュー](../media/how-to-set-virtual-machine-passwords/reset-password-menu-dashboard.png)
1. **[Set password]\(パスワードの設定\)** ダイアログ ボックスでパスワードを入力し、 **[Set password]\(パスワードの設定\)** を選択します。
    
    ![[Set password]\(パスワードの設定\) ダイアログ ボックス](../media/how-to-set-virtual-machine-passwords/set-password.png)

## <a name="next-steps"></a>次の手順
(ラボ所有者として) 構成できる他の学生の使用オプションの詳細については、次の記事を参照してください。[学生の使用の構成](how-to-configure-student-usage.md)

学生が各自の VM のパスワードをリセットする方法については、「[クラスルーム ラボの仮想マシンに対するパスワードを設定またはリセットする (学生)](how-to-set-virtual-machine-passwords-student.md)」を参照してください。