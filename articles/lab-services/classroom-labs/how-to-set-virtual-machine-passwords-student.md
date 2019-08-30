---
title: Azure Lab Services でクラスルーム ラボの VM に対するパスワードをリセットする | Microsoft Docs
description: Azure Lab Services のクラスルーム ラボの仮想マシン (VM) に対するパスワードをリセットする方法について説明します。
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
ms.openlocfilehash: 26b0f710590496875521e0dd8577a35841fbd3dd
ms.sourcegitcommit: bb8e9f22db4b6f848c7db0ebdfc10e547779cccc
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/20/2019
ms.locfileid: "69656822"
---
# <a name="set-or-reset-password-for-virtual-machines-in-classroom-labs-students"></a>クラスルーム ラボの仮想マシンに対するパスワードを設定またはリセットする (学生)
この記事では、学生が各自の VM のパスワードを設定またはリセットする方法について説明します。 

## <a name="enable-resetting-of-passwords"></a>パスワードのリセットを有効にする
ラボの作成時に、ラボ所有者は **[Use same password for all virtual machines]\(すべての仮想マシンに同じパスワードを使用する\)** を有効または無効にすることができます。 このオプションが有効にされている場合、学生はパスワードをリセットできません。 ラボ内のすべての VM のパスワードは、インストラクターが設定したパスワードと同じです。 

このオプションを無効にすると、ユーザーが初めて VM に接続するときにパスワードを設定する必要があります。 また、この記事の最後のセクションで説明しているように、学生は後からいつでもパスワードをリセットすることができます。 

## <a name="reset-password-for-the-first-time"></a>パスワードを初めてリセットする
**[Use same password for all virtual machines]\(すべての仮想マシンに同じパスワードを使用する\)** オプションが無効にされている場合、ユーザー (学生) が **[My virtual machines]\(自分の仮想マシン\)** ページのラボ タイルで **[Connect]\(接続\)** ボタンを選択すると、VM のパスワードを設定する次のダイアログ ボックスが表示されます。 

![学生のパスワードのリセット](../media/how-to-set-virtual-machine-passwords/student-set-password.png)

## <a name="reset-password-later"></a>パスワードを後からリセットする
学生は、ラボ タイルのオーバーフロー メニュー (**縦の 3 つのドット**) をクリックして、 **[Reset password]\(パスワードのリセット\)** を選択してパスワードを設定することもできます。 

![パスワードを後からリセットする](../media/how-to-set-virtual-machine-passwords/student-set-password-2.png)


## <a name="next-steps"></a>次の手順
ラボ所有者として構成できる他の学生の使用オプションの詳細については、次の記事を参照してください。[学生の使用の構成](how-to-configure-student-usage.md)
