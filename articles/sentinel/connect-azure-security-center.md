---
title: Azure Sentinel Preview に Azure Security Center データを接続する | Microsoft Docs
description: Azure Sentinel に Azure Security Center データを接続する方法について説明します。
services: sentinel
documentationcenter: na
author: rkarlin
manager: rkarlin
editor: ''
ms.assetid: d28c2264-2dce-42e1-b096-b5a234ff858a
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 04/07/2019
ms.author: rkarlin
ms.openlocfilehash: 697983000f84f9e4efe7e2c8ef9dbb2f636e0735
ms.sourcegitcommit: 6a42dd4b746f3e6de69f7ad0107cc7ad654e39ae
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/07/2019
ms.locfileid: "67620570"
---
# <a name="connect-data-from-azure-security-center"></a>Azure Security Center からデータを接続する

> [!IMPORTANT]
> 現在、Azure Sentinel はパブリック プレビュー段階にあります。
> このプレビュー バージョンはサービス レベル アグリーメントなしで提供されています。運用環境のワークロードに使用することはお勧めできません。 特定の機能はサポート対象ではなく、機能が制限されることがあります。 詳しくは、[Microsoft Azure プレビューの追加使用条件](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)に関するページをご覧ください。



Azure Sentinel では、[Azure Security Center](../security-center/security-center-intro.md) からアラートを接続して、それらを Azure Sentinel にストリーミングできます。 

## <a name="prerequisites"></a>前提条件

- Azure Security Center からアラートをエクスポートする場合は、そのログをストリーミングするサブスクリプションの共同作成者である必要があります。

- サブスクリプションで実行されている[Azure Security Center Standard レベル](../security-center/security-center-pricing.md)を所有している必要があります。 ない場合は、[サブスクリプションを Standard にアップグレード](https://azure.microsoft.com/pricing/details/security-center/)してください。

- 接続する各サブスクリプションのグローバル管理者またはセキュリティ管理者のアクセス許可を持つユーザーでログインする必要があります。


## <a name="connect-to-azure-security-center"></a>Azure Security Center に接続する

1. Azure Sentinel で **[Data connectors]\(データ コネクタ\)** を選択し、 **[Azure Security Center]** タイルをクリックします。
1. 右側で、そのアラームを Azure Sentinel にストリーミングするサブスクリプションの横にある **[接続]**  をクリックします。 アラートを Azure Sentinel にストリーミングするには、各サブスクリプションが Azure Security Center Standard レベルにアップグレードされていることを確認します。

3. **[接続]** をクリックします。

4. Azure Security Center のアラートで Log Analytics の関連スキーマを使用するには、**SecurityEvent** を検索します。

## <a name="next-steps"></a>次の手順
このドキュメントでは、Azure Security Center を Azure Sentinel に接続する方法について学習しました。 Azure Sentinel の詳細については、以下の記事を参照してください。
- [データと潜在的な脅威を可視化](quickstart-get-visibility.md)する方法についての説明。
- [Azure Sentinel を使用した脅威の検出](tutorial-detect-threats.md)の概要。
