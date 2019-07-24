---
title: インクルード ファイル
description: インクルード ファイル
author: robinsh
ms.service: iot-hub
services: iot-hub
ms.topic: include
ms.date: 03/25/2019
ms.author: robinsh
ms.custom: include file
ms.openlocfilehash: 710bb8cba7fbbe4bc9b9fdc52b0767c96f97fe72
ms.sourcegitcommit: 956749f17569a55bcafba95aef9abcbb345eb929
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2019
ms.locfileid: "58631046"
---
## <a name="create-base-resources"></a>基本のリソースの作成

メッセージ ルーティングを構成するには、IoT ハブ、ストレージ アカウント、および Service Bus キューを事前に作成する必要があります。 これらのリソースは、このチュートリアルのパート 1 の 4 つの記事 (Azure CLI、Azure PowerShell、Azure portal、または Azure Resource Manager テンプレート) のいずれかを使用して作成できます。

すべてのリソースに同じリソース グループと場所を使います。 最後に、リソース グループを削除することによって、すべてのリソースを一度に削除できます。

以降のセクションで、実行する手順について説明します。

1. [リソース グループ](../articles/azure-resource-manager/resource-group-overview.md)を作成します。

2. S1 レベルに IoT ハブを作成します。 コンシューマー グループを IoT ハブに追加します。 コンシューマー グループは、データを取得するときに Azure Stream Analytics によって使われます。

   > [!NOTE]
   > このチュートリアルを完了するには、有料レベルで IoT ハブを使用する必要があります。 無料レベルで設定できるのは 1 つのエンドポイントのみです。このチュートリアルでは複数のエンドポイントが必要です。
   > 

3. Standard_LRS レプリケーションで Standard V1 ストレージ アカウントを作成します。

4. Service Bus の名前空間とキューを作成します。

5. ハブにメッセージを送信するシミュレートされたデバイスのデバイス ID を作成します。 テスト フェーズ用にキーを保存します。 (Resource Manager テンプレートを作成する場合、これはテンプレートをデプロイした後に行います)。