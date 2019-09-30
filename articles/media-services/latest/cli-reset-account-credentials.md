---
title: Azure CLI のサンプル スクリプト - アカウント資格情報のリセット | Microsoft Docs
description: Azure CLI スクリプトを使用して、アカウントの資格情報をリセットし、app.config 設定を取得します。
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.assetid: ''
ms.service: media-services
ms.devlang: azurecli
ms.topic: sample
ms.tgt_pltfrm: multiple
ms.workload: na
ms.date: 08/20/2019
ms.author: juliako
ms.openlocfilehash: 09c93e2d851bea22e9d54dde35398f36335eb896
ms.sourcegitcommit: fbea2708aab06c19524583f7fbdf35e73274f657
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/13/2019
ms.locfileid: "70967585"
---
# <a name="cli-example-reset-the-account-credentials"></a>CLI の例:アカウント資格情報のリセット

この記事の Azure CLI スクリプトは、アカウントの資格情報をリセットし、app.config 設定を取得する方法を示しています。

## <a name="prerequisites"></a>前提条件 

[Media Services アカウントを作成する](create-account-cli-how-to.md)

[!INCLUDE [media-services-cli-instructions.md](../../../includes/media-services-cli-instructions.md)]

## <a name="example-script"></a>サンプル スクリプト

```
# Update the following variables for your own settings:
resourceGroup=amsResourceGroup
amsAccountName=amsmediaaccountname

az ams account sp reset-credentials \
  --account-name $amsAccountName \
  --resource-group $resourceGroup 
 ```

## <a name="next-steps"></a>次の手順

* [az ams](https://docs.microsoft.com/cli/azure/ams?view=azure-cli-latest)
* [資格情報をリセットする](https://docs.microsoft.com/cli/azure/ams/account/sp?view=azure-cli-latest#az-ams-account-sp-reset-credentials)
