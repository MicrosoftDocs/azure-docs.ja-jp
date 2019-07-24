---
title: Azure NetApp Files にアクセスするための NetApp アカウントを作成する | Microsoft Docs
description: 容量プールを設定してボリュームを作成できるよう Azure NetApp Files にアクセスして NetApp アカウントを作成する方法について説明します。
services: azure-netapp-files
documentationcenter: ''
author: b-juche
manager: ''
editor: ''
ms.assetid: ''
ms.service: azure-netapp-files
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 03/28/2018
ms.author: b-juche
ms.openlocfilehash: bb43a75b6a221c15c8724302797d04c22e04c8d2
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2019
ms.locfileid: "58113641"
---
# <a name="create-a-netapp-account"></a>NetApp アカウントを作成する
NetApp アカウントを作成することによって、容量プールを設定し、その後、ボリュームを作成することができます。 新しい NetApp アカウントの作成は、[Azure NetApp Files] ブレードを使用して行います。

## <a name="before-you-begin"></a>開始する前に
NetApp Resource Provider とパブリック プレビュー機能を使用するには、サブスクリプションが登録済みであることが必要です。

[Azure NetApp Files に登録する](azure-netapp-files-register.md)

## <a name="steps"></a>手順 

1. Azure ポータルにサインインします。 
2. 次のいずれかの方法で [Azure NetApp Files] ブレードにアクセスします。  
   * Azure portal の検索ボックスで「**Azure NetApp Files**」を検索します。  
   * ナビゲーションの **[すべてのサービス]** をクリックし、フィルターで Azure NetApp Files を特定します。  

   [Azure NetApp Files] ブレードは、その横に表示される星のアイコンをクリックすることで、"お気に入り" に登録することができます。 

3. **[+ 追加]** をクリックして新しい NetApp アカウントを作成します。  
   [New NetApp account]\(新しい NetApp アカウント\) ウィンドウが表示されます。  

4. NetApp アカウントについて次の情報を入力します。 
   * **アカウント名**  
     サブスクリプションの一意の名前を指定します。
   * **サブスクリプション**  
     既存のサブスクリプションからいずれかのサブスクリプションを選択します。
   * **リソース グループ**   
     既存のリソース グループを使用するか、新しいリソース グループを作成します。
   * **場所**  
     アカウントとその子リソースを置くリージョンを選択します。  

     ![新しい NetApp アカウント](../media/azure-netapp-files/azure-netapp-files-new-netapp-account.png)


5. **Create** をクリックしてください。     
   作成した NetApp アカウントが [Azure NetApp Files] ブレードに表示されます。 

## <a name="next-steps"></a>次の手順  

[容量プールを設定する](azure-netapp-files-set-up-capacity-pool.md)

