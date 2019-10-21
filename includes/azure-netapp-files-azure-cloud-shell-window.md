---
title: インクルード ファイル
description: インクルード ファイル
services: azure-netapp-files
author: b-juche
ms.service: azure-netapp-files
ms.topic: include
ms.date: 09/10/2019
ms.author: b-juche
ms.custom: include file
ms.openlocfilehash: 3a63fd96b09910b0cd7ee8c3ab9947b034173c8f
ms.sourcegitcommit: 15e3bfbde9d0d7ad00b5d186867ec933c60cebe6
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/03/2019
ms.locfileid: "71836301"
---
1. Azure NetApp Files に対してホワイトリスト登録されているサブスクリプションを指定します。
    
    ```azurecli-interactive
    az account set --subscription <subscriptionId>
    ```

1. Azure リソース プロバイダーを登録します。 
    
    ```azurecli-interactive
    az provider register --namespace Microsoft.NetApp --wait  
    ```