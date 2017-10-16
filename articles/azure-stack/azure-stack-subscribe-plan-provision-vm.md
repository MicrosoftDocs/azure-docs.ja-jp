---
title: "オファーをサブスクラブする | Microsoft Docs"
description: "ユーザーとしてオファーをサブスクライブする方法について説明します。"
services: azure-stack
documentationcenter: 
author: ErikjeMS
manager: byronr
editor: 
ms.assetid: 7f3f8683-ef09-4838-92ed-41f2fddbbbed
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 7/03/2017
ms.author: erikje
ms.openlocfilehash: f70815b5e89753a4b0083ffbe10d9920062d1ff0
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/11/2017
---
# <a name="subscribe-to-an-offer"></a>プランへのサブスクライブ

*適用先: Azure Stack 統合システムと Azure Stack 開発キット*

[オファーの作成](azure-stack-create-offer.md)が終わったら、ユーザーがサブスクリプションを作成できるかどうかをテストします。

1. ユーザーとして Azure Stack のユーザー ポータル (https://portal.local.azurestack.external) に[サインイン](azure-stack-connect-azure-stack.md)し、**[サブスクリプションの取得]** をクリックします。

   ![](media/azure-stack-subscribe-plan-provision-vm/image01.png)
2. **[表示名]** フィールドにサブスクリプションの名前を入力し、**[オファー]** をクリックします。**[Choose an offer]\(オファーの選択\)** ブレードでいずれかのオファーをクリックし、**[作成]** をクリックします。

   ![](media/azure-stack-subscribe-plan-provision-vm/image02.png)
3. 作成したサブスクリプションを表示するには、**[その他のサービス]**、**[サブスクリプション]** の順にクリックし、作成した新しいサブスクリプションをクリックします。  

オファーをサブスクライブしたら、ポータルを更新して、どのサービスが新しいサブスクリプションの一部であるかを確認します。

## <a name="subscribe-to-an-add-on-plan"></a>アドオン プランをサブスクライブする
オファーにアドオン プランがある場合、ユーザーは、それらをいつでも自分のサブスクリプションに追加できます。  

1. ユーザーポータルで、**[その他のサービス]** > **[サブスクリプション]** を選択します。

2. サブスクリプションをクリックし、**[プランの追加]** ボタンをクリックし、アドオン プランを選択します。



## <a name="next-steps"></a>次のステップ
[仮想マシンのプロビジョニング](azure-stack-provision-vm.md)
