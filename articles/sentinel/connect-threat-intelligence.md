---
title: Azure Sentinel に脅威インテリジェンス データを接続する | Microsoft Docs
description: 脅威インテリジェンス データを Azure Sentinel に接続する方法について説明します。
documentationcenter: na
author: rkarlin
manager: rkarlin
editor: ''
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/24/2019
ms.author: rkarlin
ms.openlocfilehash: 44b3830465bf2b5aa06612aa868b086b120f1ece
ms.sourcegitcommit: 0576bcb894031eb9e7ddb919e241e2e3c42f291d
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/15/2019
ms.locfileid: "72372273"
---
# <a name="connect-data-from-threat-intelligence-providers"></a>脅威インテリジェンス プロバイダーからデータを接続する

> [!IMPORTANT]
> Azure Sentinel の脅威インテリジェンス プラットフォーム データ コネクタは、現在パブリック プレビューです。
> この機能はサービス レベル アグリーメントなしで提供されています。運用環境のワークロードに使用することはお勧めできません。 特定の機能はサポート対象ではなく、機能が制限されることがあります。 詳しくは、[Microsoft Azure プレビューの追加使用条件](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)に関するページをご覧ください。

Azure Sentinel を使用すると、組織で使用されている脅威インジケーターをインポートでき、これにより、セキュリティ アナリストをいっそう適切に既知の脅威を検出して優先順位を付けることができるようになります。 Azure Sentinel の複数の機能が利用可能になるか、強化されます。

- **分析**には、一連のスケジュールされた規則テンプレートが含まれており、これを有効にすると、脅威インジケーターからのログ イベントの一致に基づいてアラートとインシデントを生成できます。

- **ブック**では、Azure Sentinel にインポートされた脅威インジケーターと、脅威インジケーターに一致する分析ルールから生成されたアラートに関する、概要情報が提供されます。

- **ハンティング**  クエリを使用すると、セキュリティの調査担当は、一般的な捜索シナリオのコンテキスト内で脅威インジケーターを使用できるようになります。

- **ノートブック**では、異常を調査し、悪意のある動作を捜索するときに、脅威インジケーターを使用できます。

次のセクションの一覧で示す統合された脅威インテリジェンス プラットフォーム (TIP) 製品のいずれかを使用して、または [Microsoft Graph Security tiIndicators API](https://aka.ms/graphsecuritytiindicators) との直接統合を使用することによって、脅威インジケーターを Azure Sentinel にストリーミングできます。

## <a name="integrated-threat-intelligence-platform-products"></a>統合された脅威インテリジェンス プラットフォーム製品

- [MISP Open Source Threat Intelligence Platform](https://www.misp-project.org/)
    
    脅威インジケーターを Microsoft Graph Security API に移行するための MISP インスタンスをクライアントに提供するサンプルスクリプトについては、「[MISP から Microsoft Graph Security へのスクリプト](https://github.com/microsoftgraph/security-api-solutions/tree/master/Samples/MISP)」を参照してください。

- [Palo Alto Networks MineMeld](https://www.paloaltonetworks.com/products/secure-the-network/subscriptions/minemeld)
    
    ガイド付きの手順については、「[MineMeld を使用して IOC を Microsoft Graph Security API に送信する](https://live.paloaltonetworks.com/t5/MineMeld-Articles/Sending-IOCs-to-the-Microsoft-Graph-Security-API-using-MineMeld/ta-p/258540)」を参照してください。

- [ThreatConnect Platform](https://threatconnect.com/solution/)


## <a name="prerequisites"></a>前提条件  

- Microsoft Graph Security tiIndicators API との直接統合を使用する TIP 製品またはカスタム アプリケーションにアクセス許可を付与するための、グローバル管理者またはセキュリティ管理者の Azure AD ロール。

- 脅威インジケーターを格納するための、Azure Sentinel ワークスペースに対する読み取りおよび書き込みアクセス許可。

## <a name="connect-azure-sentinel-to-your-threat-intelligence-provider"></a>Azure Sentinel を脅威インテリジェンス プロバイダーに接続する

1. Azure Active Directory に[アプリケーションを登録](/graph/auth-v2-service#1-register-your-app)して、アプリケーション ID、アプリケーションシークレット、Azure Active Directory テナント ID を取得します。 Microsoft Graph Security tiIndicators API との直接統合を使用する統合された TIP 製品またはアプリケーションを構成するときに、これらの値が必要になります。

2. 登録されたアプリケーションに対する [API アクセス許可を構成](/graph/auth-v2-service#2-configure-permissions-for-microsoft-graph)します。登録したアプリケーションに、Microsoft Graph アプリケーションのアクセス許可 **ThreatIndicators.ReadWrite.OwnedBy** を追加します。

3. 組織に登録されているアプリケーションに対して管理者の同意を許可するよう、Azure Active Directory テナント管理者に依頼します。 Azure ポータルで次の手順を実行します。 **[Azure Active Directory]**  >  **[アプリの登録]**  >  **\<_アプリ名_>**  >  **[API アクセス許可の表示]**  >  **[\<_テナント名_> に管理者の同意を与えます]** 。

4. 以下を指定することにより、Azure Sentinel にインジケーターを送信するように、Microsoft Graph Security tiIndicators API との直接統合を使用する TIP 製品またはアプリを構成します。
    
    a. 登録されているアプリケーションの ID、シークレット、およびテナント ID の値。
    
    b. ターゲット製品として、Azure Sentinel を指定します。
    
    c. アクションとして、アラートを指定します。

5. Azure portal で、 **[Azure Sentinel]**  >  **[Data connectors]\(データ コネクタ\)** に移動し、 **[Threat Intelligence Platforms (Preview)]\(脅威インテリジェンス プラットフォーム (プレビュー)\)** コネクタを選択します。

6. **[Open connector page]\(コネクタ ページを開く\)** を選択し、 **[接続]** を選択します。

7. Azure Sentinel にインポートされた脅威インジケーターを表示するには、 **[Azure Sentinel - ログ]**  >  **[SecurityInsights]** に移動し、 **[ThreatIntelligenceIndicator]** を展開します。

## <a name="next-steps"></a>次の手順

このドキュメントでは、Azure Sentinel に脅威インテリジェンス プロバイダーを接続する方法を説明しました。 Azure Sentinel の詳細については、次の記事をご覧ください。

- [データと潜在的な脅威を可視化](quickstart-get-visibility.md)する方法についての説明。
- [Azure Sentinel を使用した脅威の検出](tutorial-detect-threats.md)の概要。
