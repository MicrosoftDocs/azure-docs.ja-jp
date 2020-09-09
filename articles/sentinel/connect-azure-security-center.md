---
title: Azure Security Center データを Azure Sentinel に接続する
description: Azure Security Center (ASC) Standard レベルからアラートを接続し、それらを Azure Sentinel にストリーミングする方法について説明します。
author: yelevin
manager: rkarlin
ms.assetid: d28c2264-2dce-42e1-b096-b5a234ff858a
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.topic: conceptual
ms.date: 09/23/2019
ms.author: yelevin
ms.openlocfilehash: 2fc7744600a9652ad43fd0aae8d886dc94acd58f
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/02/2020
ms.locfileid: "85559154"
---
# <a name="connect-data-from-azure-security-center-asc"></a>Azure Security Center (ASC) からデータを接続する

Azure Sentinel では、[Azure Security Center](../security-center/security-center-intro.md) からアラートを接続して、それらを Azure Sentinel にストリーミングできます。 

## <a name="prerequisites"></a>前提条件

- Azure Security Center からアラートをエクスポートするには、ストリーミングするログのサブスクリプションで Security Reader ロールが必要になります。

- サブスクリプションで実行されている[Azure Security Center Standard レベル](../security-center/security-center-pricing.md)を所有している必要があります。 ない場合は、[サブスクリプションを Standard にアップグレード](https://azure.microsoft.com/pricing/details/security-center/)してください。

## <a name="connect-to-azure-security-center"></a>Azure Security Center に接続する

1. Azure Sentinel で、ナビゲーション メニューから **[Data connectors]\(データ コネクタ\)** を選択します。

1. データ コネクタ ギャラリーで、 **[Azure Security Center]** を選択し、 **[Open connector page]\(コネクタ ページを開く\)** ボタンをクリックします。

1. **[構成]** で、アラームを Azure Sentinel にストリーミングするサブスクリプションの横にある **[接続]** をクリックします。 [接続] ボタンは、必要なアクセス許可と ASC Standard レベルのサブスクリプションがある場合にのみ使用できます。

1. Azure Security Center のアラートによって Azure Sentinel で自動的にインシデントが生成されるようにするかどうかを選択できます。 **[Create incidents]\(インシデントの作成\)** で **[有効化]** を選択して、アラートからインシデントを自動的に作成する既定の分析ルールを有効にします。 次に、 **[Active rules]\(アクティブなルール\)** タブの **[分析]** でこのルールを編集します。

1. Azure Security Center のアラートで Log Analytics の関連スキーマを使用するには、**SecurityAlert** を検索します。

## <a name="next-steps"></a>次のステップ
このドキュメントでは、Azure Security Center を Azure Sentinel に接続する方法について学習しました。 Azure Sentinel の詳細については、次の記事をご覧ください。
- [データと潜在的な脅威を可視化](quickstart-get-visibility.md)する方法についての説明。
- [Azure Sentinel を使用した脅威の検出](tutorial-detect-threats-built-in.md)の概要。
