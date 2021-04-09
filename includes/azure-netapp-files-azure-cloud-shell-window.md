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
ms.openlocfilehash: e78be737ef8d191cddc2da0f738250ca04cae9a1
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "91672357"
---
1. Azure NetApp Files に対して承認されているサブスクリプションを指定します。
    
    ```azurecli-interactive
    az account set --subscription <subscriptionId>
    ```

1. Azure リソース プロバイダーを登録します。 
    
    ```azurecli-interactive
    az provider register --namespace Microsoft.NetApp --wait  
    ```