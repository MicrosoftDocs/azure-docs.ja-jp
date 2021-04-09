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
ms.topic: how-to
ms.date: 03/28/2018
ms.author: b-juche
ms.openlocfilehash: e5e0c43aa8c4fbf0db89065e0fab93ee174a4ede
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "96006595"
---
# <a name="create-a-netapp-account"></a>NetApp アカウントを作成する
NetApp アカウントを作成することによって、容量プールを設定し、その後、ボリュームを作成することができます。 新しい NetApp アカウントの作成は、[Azure NetApp Files] ブレードを使用して行います。

## <a name="before-you-begin"></a>開始する前に
サービスへのアクセス許可が付与されたことを確認できる Azure NetApp Files チームからの電子メールを、必ず受信します。 [サービスにアクセスするための順番待ち要求の送信](azure-netapp-files-register.md#waitlist)に関するページを参照してください。

また、NetApp Resource Provider を使用する場合、サブスクリプションが登録済みになっている必要があります。 「[NetApp リソース プロバイダーの登録](azure-netapp-files-register.md#resource-provider)」をご覧ください。

## <a name="steps"></a>手順 

1. Azure portal にサインインします。 
2. 次のいずれかの方法で [Azure NetApp Files] ブレードにアクセスします。  
   * Azure portal の検索ボックスで「**Azure NetApp Files**」を検索します。  
   * ナビゲーションの **[すべてのサービス]** をクリックし、フィルターで Azure NetApp Files を特定します。  

   [Azure NetApp Files] ブレードは、その横に表示される星のアイコンをクリックすることで、"お気に入り" に登録することができます。 

3. **[+ 追加]** をクリックして新しい NetApp アカウントを作成します。  
   [New NetApp account]\(新しい NetApp アカウント\) ウィンドウが表示されます。  

4. NetApp アカウントについて次の情報を入力します。 
   * **アカウント名**  
     サブスクリプションの一意の名前を指定します。
   * **[サブスクリプション]**  
     既存のサブスクリプションからいずれかのサブスクリプションを選択します。
   * **リソース グループ**   
     既存のリソース グループを使用するか、新しいリソース グループを作成します。
   * **場所**  
     アカウントとその子リソースを置くリージョンを選択します。  

     ![新しい NetApp アカウント](../media/azure-netapp-files/azure-netapp-files-new-netapp-account.png)


5. **Create** をクリックしてください。     
   作成した NetApp アカウントが [Azure NetApp Files] ブレードに表示されます。 

> [!NOTE] 
> Azure NetApp Files サービスへのアクセスが付与されていない場合、最初の NetApp アカウントの作成を試行したときに次のエラーを受信します。  
>
> `{"code":"DeploymentFailed","message":"At least one resource deployment operation failed. Please list deployment operations for details. Please see https://aka.ms/arm-debug for usage details.","details":[{"code":"NotFound","message":"{\r\n \"error\": {\r\n \"code\": \"InvalidResourceType\",\r\n \"message\": \"The resource type could not be found in the namespace 'Microsoft.NetApp' for api version '2017-08-15'.\"\r\n }\r\n}"}]}`

## <a name="next-steps"></a>次のステップ  

[容量プールを設定する](azure-netapp-files-set-up-capacity-pool.md)

