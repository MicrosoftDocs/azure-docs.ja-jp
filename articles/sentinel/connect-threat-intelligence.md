---
title: Azure Sentinel Preview に脅威インテリジェンス データを接続する | Microsoft Docs
description: 脅威インテリジェンス データを Azure Sentinel に接続する方法について説明します。
documentationcenter: na
author: rkarlin
manager: barbkess
editor: ''
ms.assetid: 56412543-5664-44c1-b026-2dbaf78a9a50
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 04/07/2019
ms.author: rkarlin
ms.openlocfilehash: a33b00e4e553824f1f719be2c8518487241f0a11
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/18/2019
ms.locfileid: "59786186"
---
# <a name="connect-data-from-threat-intelligence-providers"></a>脅威インテリジェンス プロバイダーからデータを接続する 

> [!IMPORTANT]
> 現在、Azure Sentinel はパブリック プレビュー段階にあります。
> このプレビュー バージョンはサービス レベル アグリーメントなしで提供されています。運用環境のワークロードに使用することはお勧めできません。 特定の機能はサポート対象ではなく、機能が制限されることがあります。 詳しくは、[Microsoft Azure プレビューの追加使用条件](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)に関するページをご覧ください。

Azure Sentinel にデータをストリーミングしたら、組織全体で使用されている脅威インテリジェンス フィードでデータを強化することができます。 

アラートやルールを実際の脅威インテリジェンスと照合できるように、Azure Sentinel は[脅威インテリジェンス プロバイダー](https://aka.ms/graphsecuritytips)と統合できます。たとえば、特定の IP アドレスからアラートを受け取った場合、脅威インテリジェンス プロバイダーの統合により、その IP アドレスが悪意のあるものであることが最近判明したかどうかを通知できるようになります。 

シングル クリックで、脅威インテリジェンス プロバイダーから Azure Sentinel にログをストリーミングできます。 この接続により、さまざまな種類の監視可能なもの (IP アドレス、ドメイン、URL、ファイル ハッシュなど) を含むインジケーターを組み込んで、Azure Sentinel でカスタム アラート ルールを検索および作成できます。  
> [!NOTE]
> [Microsoft Graph Security tiIndicator](https://aka.ms/graphsecuritytiindicators) のエンティティと統合するか、[Microsoft Graph Security 統合脅威インテリジェンス プラットフォーム](https://aka.ms/graphsecuritytips)を使用して、カスタマイズした脅威インジケーターを、警告ルール、ダッシュボード、捜索シナリオで使用するために Azure Sentinel に入力することができます。

## <a name="prerequisites"></a>前提条件  

- グローバル管理者またはセキュリティ管理者のアクセス許可を持つユーザー 

- Microsoft インテリジェント セキュリティ グラフと統合された脅威インテリジェンス アプリケーション 

## <a name="connect-to-threat-intelligence"></a>脅威インテリジェンスに接続する 

1. 脅威インテリジェンス プロバイダーを既に使用している場合は、TIP アプリケーションを参照し、インジケーターを Microsoft に送信するためのアクセス許可を付与して、サービスを Azure Sentinel として指定する必要があります。  

2. Azure Sentinel で **[Data connectors]\(データ コネクタ\)** を選択し、**[脅威インテリジェンス]** タイルをクリックします。

3. **[接続]** をクリックします。 

4. Log Analytics で脅威インテリジェンス フィードに関連するスキーマを使用するために、**ThreatIntelligenceIndicator** を検索します。 

 
## <a name="next-steps"></a>次の手順

このドキュメントでは、Azure Sentinel に脅威インテリジェンス プロバイダーを接続する方法を説明しました。 Azure Sentinel の詳細については、次の記事をご覧ください。

- Azure Sentinel を使用するには、Microsoft Azure のサブスクリプションが必要です。 サブスクリプションがない場合は、 [無料試用版](https://azure.microsoft.com/free/)にサインアップできます。
- [データを Azure Sentinel にオンボード](quickstart-onboard.md)し、[データや潜在的な脅威を視覚化する](quickstart-get-visibility.md)方法を確認します。
