---
title: メディア占有ユニット (MRU) をスケーリングする - CLI
description: このトピックでは、CLI を使用して、Azure Media Services でのメディア処理をスケーリングする方法について説明します。
services: media-services
documentationcenter: ''
author: IngridAtMicrosoft
manager: femila
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.date: 09/30/2020
ms.author: inhenkel
ms.openlocfilehash: b1c98bfa6b2cf45a59b70126001442ed80659668
ms.sourcegitcommit: 4e70fd4028ff44a676f698229cb6a3d555439014
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 01/28/2021
ms.locfileid: "98955887"
---
# <a name="how-to-scale-media-reserved-units"></a>メディア占有ユニットをスケーリングする方法

[!INCLUDE [media services api v3 logo](./includes/v3-hr.md)]

この記事では、エンコードを高速化するためにメディア占有ユニット (MRU) をスケーリングする方法について説明します。

## <a name="prerequisites"></a>前提条件

[Media Services アカウントを作成する](./create-account-howto.md)

[メディア占有ユニット](concept-media-reserved-units.md)について理解する。

[!INCLUDE [media-services-cli-instructions](../../../includes/media-services-cli-instructions.md)]

## <a name="scale-media-reserved-units-with-cli"></a>CLI を使用してメディア占有ユニットをスケーリングする

`mru` コマンドを実行します。

次の [az ams account mru](/cli/azure/ams/account/mru?view=azure-cli-latest) コマンドでは、"amsaccount" アカウントのメディア占有ユニットを、**count** パラメーターと **type** パラメーターを使用して設定しています。

```azurecli
az ams account mru set -n amsaccount -g amsResourceGroup --count 10 --type S3
```

## <a name="billing"></a>課金

アカウントにメディア占有ユニットがプロビジョニングされた時間 (分) に基づいて課金されます。 課金は、アカウントで実行中のジョブがあるかどうかとは無関係に発生します。 詳しくは、[Media Services の価格詳細](https://azure.microsoft.com/pricing/details/media-services/)ページの FAQ のセクションをご覧ください。   

## <a name="next-step"></a>次のステップ

[ビデオを分析する](analyze-videos-tutorial-with-api.md)

## <a name="see-also"></a>関連項目

* [クォータと制限](limits-quotas-constraints.md)
