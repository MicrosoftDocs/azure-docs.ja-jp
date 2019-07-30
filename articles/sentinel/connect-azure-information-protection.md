---
title: Azure Sentinel Preview に Azure Information Protection データを接続する | Microsoft Docs
description: Azure Sentinel で Azure Information Protection データを接続する方法について説明します。
services: sentinel
documentationcenter: na
author: rkarlin
manager: rkarlin
editor: ''
ms.assetid: bfa2eca4-abdc-49ce-b11a-0ee229770cdd
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 04/07/2019
ms.author: rkarlin
ms.openlocfilehash: 3edfdca16141726ea591c8473528fc23ee076c53
ms.sourcegitcommit: 6a42dd4b746f3e6de69f7ad0107cc7ad654e39ae
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/07/2019
ms.locfileid: "67620604"
---
# <a name="connect-data-from-azure-information-protection"></a>Azure Information Protection からデータを接続する

> [!IMPORTANT]
> 現在、Azure Sentinel はパブリック プレビュー段階にあります。
> このプレビュー バージョンはサービス レベル アグリーメントなしで提供されています。運用環境のワークロードに使用することはお勧めできません。 特定の機能はサポート対象ではなく、機能が制限されることがあります。 詳しくは、[Microsoft Azure プレビューの追加使用条件](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)に関するページをご覧ください。

シングル クリックで、[Azure Information Protection](https://docs.microsoft.com/azure/information-protection/reports-aip) から Azure Sentinel にログをストリーミングできます。 Azure Information Protection を使用すると、クラウドとオンプレミス インフラストラクチャのどちらに保存されているかに関係なく、データを保護できます。また、社外で共有する電子メール、ドキュメント、機密データを管理し、セキュリティで保護することもできます。 簡単な分類から組み込みのラベルやアクセス許可まで、Azure Information Protection を使用して常にデータ保護を強化します。 Azure Information Protection を Azure Sentinel に接続すると、Azure Information Protection のすべてのアラートを Azure Sentinel にストリーミングできます。


## <a name="prerequisites"></a>前提条件

- グローバル管理者、セキュリティ管理者、または Information Protection のアクセス許可を持つユーザー


## <a name="connect-to-azure-information-protection"></a>Azure Information Protection に接続する

Azure Information Protection が既にある場合は、[ネットワークで有効](https://docs.microsoft.com/azure/information-protection/activate-service)になっていることを確認します。
Azure Information Protection がデプロイされ、データを取得している場合は、アラート データを Azure Sentinel に簡単にストリーミングできます。


1. Azure Sentinel で **[Data connectors]\(データ コネクタ\)** を選択し、 **[Azure Information Protection]** タイルをクリックします。

2. [Azure Information Protection ポータル](https://portal.azure.com/?ScannerConfiguration=true&EndpointDiscovery=true#blade/Microsoft_Azure_InformationProtection/DataClassGroupEditBlade/quickstartBlade)に移動します。 

3. **[接続]** で、[[分析の構成]](https://portal.azure.com/#blade/Microsoft_Azure_InformationProtection/DataClassGroupEditBlade/analyticsOnboardBlade) をクリックして、Azure Information Protection から Azure Sentinel へのログのストリーミングを設定します。

4. Azure Sentinel をデプロイしたワークスペースを選択します。 

5. Click **OK**.

6. Log Analytics で Azure Information Protection のアラートに関連するスキーマを使用するために、**InformationProtectionLogs_CL** を検索します。




## <a name="next-steps"></a>次の手順
このドキュメントでは、Azure Information Protection を Azure Sentinel に接続する方法について説明しました。 Azure Sentinel の詳細については、次の記事をご覧ください。
- [データと潜在的な脅威を可視化](quickstart-get-visibility.md)する方法についての説明。
- [Azure Sentinel を使用した脅威の検出](tutorial-detect-threats.md)の概要。
