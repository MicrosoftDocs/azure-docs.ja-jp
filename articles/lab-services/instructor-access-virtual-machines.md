---
title: Azure Lab Services 内の VM にアクセスする教師
description: この記事では、教師が教師ビューから学生の VM にアクセスする方法について説明します。 たとえば、補助教員があるクラスの教師になり、他のクラスで学生になることがあります。
ms.topic: article
ms.date: 06/26/2020
ms.openlocfilehash: ad4f9cfd11b372e5c6da5c17eaeba82b0cd8a91f
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "85445102"
---
# <a name="access-virtual-machines-as-a-student-from-the-educator-view"></a>教師ビューから学生として仮想マシンにアクセスする
この記事では、教師が学生として参加するクラスの VM にアクセスする方法について説明します。 

この機能は、たとえば、次のシナリオで役立ちます。 補助教員が、あるクラスでは教師であるが、他のクラスでは学生である。 その補助教員が、自分が所有するラボを表示する教師ビューから学生 VM を表示してアクセスしたい。 

## <a name="access-vms-from-educator-view"></a>教師ビューから VM にアクセスする

1. [Azure Lab Services Web サイト](https://labs.azure.com)にサインインします。 自分が所有しているラボが表示されます。 これらのラボは、自分で作成したラボ、または管理者によって所有者として割り当てられたラボです。 詳細については、[既存のラボに所有者を追加する方法](how-to-add-user-lab-owner.md)に関するページを参照してください。
2. 学生として参加しているクラスの VM にアクセスするには、右上隅にあるコンピューター アイコンを選択します。 学生としてアクセスできる VM が表示されることを確認します。 次の例のユーザーは、Python ラボでは補助教員ですが、Java ラボでは学生です。 そのため、このユーザーは、ドロップダウン リストの Java ラボから VM を表示できます。 このユーザーはその VM を起動して接続できます。 
    
    ![学生の VM にアクセスする](./media/instructors-access-virtual-machines/access-student-virtual-machines.png)

## <a name="next-steps"></a>次のステップ
次の記事をご覧ください。

- [VM に接続する](how-to-use-classroom-lab.md#connect-to-the-vm)
- [MAC から RDP を使用して VM に接続する](connect-virtual-machine-mac-remote-desktop.md)
- [Chromebook から RDP を使用して VM に接続する](connect-virtual-machine-chromebook-remote-desktop.md)
- [Linux 仮想マシン用リモート デスクトップを使用する](how-to-use-remote-desktop-linux-student.md)
