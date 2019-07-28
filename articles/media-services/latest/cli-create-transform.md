---
title: Azure CLI のサンプル スクリプト - 変換の作成 | Microsoft Docs
description: Azure CLI スクリプトを使用して変換を作成します。
services: media-services
documentationcenter: ''
author: juliako
manager: femila
editor: ''
ms.assetid: ''
ms.service: media-services
ms.devlang: azurecli
ms.topic: sample
ms.tgt_pltfrm: multiple
ms.workload: na
ms.date: 05/01/2019
ms.author: juliako
ms.openlocfilehash: 86f455f25bb41833774bb7ad7d81688aad812b5a
ms.sourcegitcommit: 2ce4f275bc45ef1fb061932634ac0cf04183f181
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 05/07/2019
ms.locfileid: "65236820"
---
# <a name="cli-example-create-a-transform"></a>CLI の例:Transform を作成します。

この記事の Azure CLI スクリプトは、変換の作成方法を示しています。 変換では、ビデオまたはオーディオ ファイルを処理するためのタスクの単純なワークフローを記述します (多くの場合、"レシピ" と呼ばれます)。 必要な名前と "レシピ" を持つ変換が既に存在するかどうかを必ず確認する必要があります。 その場合は再利用する必要があります。

## <a name="prerequisites"></a>前提条件 

[Media Services アカウントを作成する](create-account-cli-how-to.md)

[!INCLUDE [media-services-cli-instructions.md](../../../includes/media-services-cli-instructions.md)]

## <a name="example-script"></a>サンプル スクリプト

[!code-azurecli-interactive[main](../../../cli_scripts/media-services/create-transform/Create-Transform.sh "Create a transform")]

## <a name="next-steps"></a>次の手順

[Media Services の概要](media-services-overview.md)
