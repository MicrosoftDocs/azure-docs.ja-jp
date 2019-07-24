---
title: Azure NetApp Files に登録する | Microsoft Docs
description: Azure NetApp Files サービスに加入するためのリクエストを送信する方法について説明します。
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
ms.date: 01/04/2018
ms.author: b-juche
ms.openlocfilehash: 86c016a5dbcc0d78378e59bc6b3606ddf2c54f64
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2019
ms.locfileid: "58122337"
---
# <a name="register-for-azure-netapp-files"></a>Azure NetApp Files に登録する
Azure NetApp Files を使用する前には、Azure NetApp Files サービスに加入するためのリクエストを送信する必要があります。  加入後、サービスを使用するための登録を行います。

## <a name="request-to-enroll-in-the-service"></a>サービスに加入するためのリクエスト
パブリック プレビュー プログラムに参加していて、Microsoft.NetApp リソース プロバイダーにアクセスするためのホワイトリストに登録されている必要があります。 パブリック プレビュー プログラムへの参加の詳細については、[Azure NetApp Files パブリック プレビューのサインアップ ページ](https://aka.ms/nfspublicpreview)をご覧ください。 


## <a name="register-the-netapp-resource-provider"></a>NetApp リソース プロバイダーの登録

サービスを使用するには、Azure NetApp Files 用に Azure リソース プロバイダーを登録する必要があります。 

1. Azure portal で、右上隅にある Azure Cloud Shell アイコンをクリックします。

      ![Azure Cloud Shell アイコン](../media/azure-netapp-files/azure-netapp-files-azure-cloud-shell.png)

2. お客様の Azure アカウントにサブスクリプションが複数ある場合は、Azure NetApp Files のホワイトリストに登録されているものを選択します。
    
        az account set --subscription <subscriptionId>

3. Azure Cloud Shell コンソールで次のコマンドを入力して、お客様のサブスクリプションがホワイトリストに登録されていることを確認します。
    
        az feature list | grep NetApp

   コマンドの出力は次のようになります。
   
       "id": "/subscriptions/<SubID>/providers/Microsoft.Features/providers/Microsoft.NetApp/features/publicPreviewADC",  
       "name": "Microsoft.NetApp/publicPreviewADC" 
       
   `<SubID>` がお客様のサブスクリプション ID です。

4. Azure Cloud Shell コンソールで次のコマンドを入力して、Azure リソース プロバイダーを登録します。 
    
        az provider register --namespace Microsoft.NetApp --wait

   `--wait` パラメーターで、登録が完了するまで待機するようコンソールに指示します。 登録プロセスは、完了するまでに時間がかかることがあります。

5. Azure Cloud Shell コンソールで次のコマンドを入力して、Azure リソース プロバイダーが登録されたことを確認します。 
    
        az provider show --namespace Microsoft.NetApp

   コマンドの出力は次のようになります。
   
        {
        "id": "/subscriptions/<SubID>/providers/Microsoft.NetApp",
        "namespace": "Microsoft.NetApp", 
        "registrationState": "Registered", 
        "resourceTypes": […. 

   `<SubID>` がお客様のサブスクリプション ID です。  `state` パラメーターの値は `Registered` を示しています。

6. Azure portal で、**[サブスクリプション]** ブレードをクリックします。
7. [サブスクリプション] ブレードで、お客様のサブスクリプション ID をクリックします。 
8. サブスクリプションの設定で、**[リソース プロバイダー]** をクリックして、Microsoft.NetApp プロバイダーの状態が Registered であることを確認します。 

      ![登録済みの Microsoft.NetApp](../media/azure-netapp-files/azure-netapp-files-registered-resource-providers.png)


## <a name="next-steps"></a>次の手順  

[NetApp アカウントを作成する](azure-netapp-files-create-netapp-account.md)