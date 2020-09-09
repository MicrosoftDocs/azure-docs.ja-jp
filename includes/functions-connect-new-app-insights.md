---
title: インクルード ファイル
description: インクルード ファイル
services: functions
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 06/10/2020
ms.author: glenga
ms.custom: include file
ms.openlocfilehash: 87af50c5b5e5b69fd175ac4a570c4b6f659b97e6
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/02/2020
ms.locfileid: "84731024"
---
Azure Functions を使用すると、[Azure portal] から関数アプリに Application Insights 統合を簡単に追加できます。

1. [Azure portal][Azure portal] で、"**関数アプリ**" を検索して選択し、対象の関数アプリを選択します。 

1. ウィンドウの上部にある **[Application Insights が構成されていません]** バナーを選択します。 このバナーが表示されない場合は、アプリで既に Application Insights が有効になっている可能性があります。

    :::image type="content" source="media/functions-connect-new-app-insights/enable-application-insights.png" alt-text="ポータルから Application Insights を有効にする":::

1. **[リソースの変更]** を展開し、下の表に指定されている設定を使用して、Application Insights リソースを作成します。  

    | 設定      | 推奨値  | 説明                                        |
    | ------------ |  ------- | -------------------------------------------------- |
    | **新しいリソース名** | 一意のアプリ名 | 関数アプリと同じ名前を使用するのが最も簡単です。この名前は、サブスクリプション内で一意である必要があります。 | 
    | **場所** | 西ヨーロッパ | 可能であれば、お使いの関数アプリと同じ[リージョン](https://azure.microsoft.com/regions/)、または近隣のリージョンを使用してください。 |

    :::image type="content" source="media/functions-connect-new-app-insights/ai-general.png" alt-text="Application Insights リソースを作成します":::。

1. **[適用]** を選択します。 

   Application Insights リソースは、関数アプリと同じリソース グループおよびサブスクリプションに作成されます。 リソースが作成されたら、Application Insights ウィンドウを閉じます。

1. 対象の関数アプリで、 **[設定]** の下の **[構成]** を選択し、 **[アプリケーション設定]** を選択します。 `APPINSIGHTS_INSTRUMENTATIONKEY` という設定が表示された場合は、Azure で実行されている関数アプリに対して Application Insights 統合が有効になっています。

[Azure Portal]: https://portal.azure.com
