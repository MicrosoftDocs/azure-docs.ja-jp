---
title: Azure CLI で Media Services アカウントを作成する - Azure | Microsoft Docs
description: Azure Media Services アカウントを作成するには、このクイック スタートの手順に従います。
services: media-services
documentationcenter: ''
author: juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: article
ms.custom: seodec18
ms.date: 05/19/2019
ms.author: juliako
ms.openlocfilehash: f2cb2e2ee6393a59125ee879f2058516eb50d6b1
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/13/2019
ms.locfileid: "65956719"
---
# <a name="create-an-azure-media-services-account"></a>Azure Media Services アカウントの作成

Azure で暗号化、エンコード、分析、管理、およびメディア コンテンツのストリーミングを開始するには、Media Services アカウントを作成する必要があります。 Media Services アカウントは、1 つまたは複数のストレージ アカウントに関連付ける必要があります。

> [!NOTE]
> Media Services アカウントおよび関連するすべてのストレージ アカウントは、同じ Azure サブスクリプションに存在する必要があります。 待機時間やデータ送信コストが増加することを回避するために、ストレージ アカウントを Media Services アカウントと同じ場所で使用することを強くお勧めします。

この記事では、Azure CLI を使用して、新しい Azure Media Services アカウントを作成する手順について説明します。  

## <a name="prerequisites"></a>前提条件

有効な Azure サブスクリプション Azure サブスクリプションをお持ちでない場合は、開始する前に [無料アカウント](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio) を作成してください。

[!INCLUDE [media-services-cli-instructions](../../../includes/media-services-cli-instructions.md)]

## <a name="set-the-azure-subscription"></a>Azure サブスクリプションを設定する

次のコマンドで、Media Services アカウントで使用する Azure サブスクリプション ID を指定します。 [[サブスクリプション]](https://portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade) に移動することで、アクセス権があるサブスクリプションの一覧を表示できます。

```azurecli
az account set --subscription mySubscriptionId
```
 
[!INCLUDE [media-services-cli-create-v3-account-include](../../../includes/media-services-cli-create-v3-account-include.md)]
 
## <a name="next-steps"></a>次の手順

* [v3 API へのアクセス](access-api-cli-how-to.md)
* [ファイルのストリーミング](stream-files-dotnet-quickstart.md)
* [Media Services アカウントにセカンダリ ストレージを接続する](https://docs.microsoft.com/cli/azure/ams/account/storage?view=azure-cli-latest#az-ams-account-storage-add)

## <a name="see-also"></a>関連項目

[Azure CLI](https://docs.microsoft.com/cli/azure/ams?view=azure-cli-latest)

