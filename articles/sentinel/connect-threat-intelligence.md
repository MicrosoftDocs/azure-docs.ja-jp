---
title: Azure Sentinel に脅威インテリジェンス データを接続する | Microsoft Docs
description: 脅威インテリジェンス データを Azure Sentinel に接続する方法について説明します。
documentationcenter: na
author: yelevin
manager: rkarlin
editor: ''
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/22/2019
ms.author: yelevin
ms.openlocfilehash: 205cc6eea5d1ac3be2d0e266621067dc8e20d2f9
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "96121746"
---
# <a name="connect-data-from-threat-intelligence-providers"></a>脅威インテリジェンス プロバイダーからデータを接続する

> [!IMPORTANT]
> Azure Sentinel の脅威インテリジェンス データ コネクタは、現在パブリック プレビュー中です。
> この機能はサービス レベル アグリーメントなしで提供されています。運用環境のワークロードに使用することはお勧めできません。 特定の機能はサポート対象ではなく、機能が制限されることがあります。 詳しくは、[Microsoft Azure プレビューの追加使用条件](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)に関するページをご覧ください。

Azure Sentinel を使用すると、組織で使用されている脅威インジケーターをインポートでき、これにより、既知の脅威を検出して優先順位を付けるというセキュリティ アナリストの能力が高まります。 Azure Sentinel の複数の機能が利用可能になるか、強化されます。

- **分析** には、一連のスケジュールされた規則テンプレートが含まれており、これを有効にすると、脅威インジケーターからのログ イベントの一致に基づいてアラートとインシデントを生成できます。

- **ブック** では、Azure Sentinel にインポートされた脅威インジケーターと脅威インジケーターと一致する分析ルールから生成されたアラートに関する概要情報が提供されます。

- **ハンティング**  クエリを使用すると、セキュリティの調査担当は、一般的な捜索シナリオのコンテキスト内で脅威インジケーターを使用できるようになります。

- **ノートブック** では、異常を調査し、悪意のある動作を捜索するときに、脅威インジケーターを使用できます。

次のセクションの一覧に示されている統合された脅威インテリジェンス プラットフォーム (TIP) 製品を使用するか、TAXII サーバーに接続するか、[Microsoft Graph Security Indicators API](/graph/api/resources/tiindicator) との直接統合を使用することで、脅威インジケーターを Azure Sentinel にストリーミングできます。

## <a name="integrated-threat-intelligence-platform-products"></a>統合された脅威インテリジェンス プラットフォーム製品

- [MISP Open Source Threat Intelligence Platform](https://www.misp-project.org/)
    
    脅威インジケーターを Microsoft Graph Security API に移行するための MISP インスタンスをクライアントに提供するサンプルスクリプトについては、「[MISP から Microsoft Graph Security へのスクリプト](https://github.com/microsoftgraph/security-api-solutions/tree/master/Samples/MISP)」を参照してください。

- [Anomali ThreatStream](https://www.anomali.com/products/threatstream)

    ThreatStream Integrator and Extensions、および ThreatStream インテリジェンスを Microsoft Graph Security API に接続するための手順をダウンロードするには、[ThreatStream のダウンロード](https://ui.threatstream.com/downloads)のページを参照してください。

- [Palo Alto Networks MineMeld](https://www.paloaltonetworks.com/products/secure-the-network/subscriptions/minemeld)
    
    ガイド付きの手順については、「[MineMeld を使用して IOC を Microsoft Graph Security API に送信する](https://live.paloaltonetworks.com/t5/MineMeld-Articles/Sending-IOCs-to-the-Microsoft-Graph-Security-API-using-MineMeld/ta-p/258540)」を参照してください。

- [ThreatConnect Platform](https://threatconnect.com/solution/)

    詳細については、[ThreatConnect の Integration](https://threatconnect.com/integrations/) ページに移動し、Microsoft Graph Security API を探してください。

- [EclecticIQ Platform](https://www.eclecticiq.com/solutions)

- [ThreatQ Threat Intelligence Platform](https://www.threatq.com/)

    詳細およびガイド付きの手順については、[ThreatQ 統合用の Microsoft Sentinel コネクタ](https://appsource.microsoft.com/product/web-apps/threatquotientinc1595345895602.microsoft-sentinel-connector-threatq?src=health&tab=Overview)に関する記事を参照してください。

## <a name="connect-azure-sentinel-to-your-threat-intelligence-platform"></a>Azure Sentinel を脅威インテリジェンス プラットフォームに接続する

### <a name="prerequisites"></a>前提条件  

- Microsoft Graph Security tiIndicators API との直接統合を使用する TIP 製品またはカスタム アプリケーションにアクセス許可を付与するための、グローバル管理者またはセキュリティ管理者の Azure AD ロール。

- 脅威インジケーターを格納するための、Azure Sentinel ワークスペースに対する読み取りおよび書き込みアクセス許可。

### <a name="instructions"></a>Instructions

1. Azure Active Directory に[アプリケーションを登録](/graph/auth-v2-service#1-register-your-app)して、アプリケーション ID、アプリケーションシークレット、Azure Active Directory テナント ID を取得します。 Microsoft Graph Security tiIndicators API との直接統合を使用する統合された TIP 製品またはアプリケーションを構成するときに、これらの値が必要になります。

2. 登録されたアプリケーションに対する [API アクセス許可を構成](/graph/auth-v2-service#2-configure-permissions-for-microsoft-graph)します。登録したアプリケーションに、Microsoft Graph アプリケーションのアクセス許可 **ThreatIndicators.ReadWrite.OwnedBy** を追加します。

3. 組織に登録されているアプリケーションに対して管理者の同意を許可するよう、Azure Active Directory テナント管理者に依頼します。 Azure ポータルで次の手順を実行します。 **[Azure Active Directory]**  >  **[アプリの登録]**  >  **\<_app name_>**  >  **[API アクセス許可の表示]**  >  **[\<_tenant name_> に管理者の同意を与えます]** 。

4. 以下を指定することにより、Azure Sentinel にインジケーターを送信するように、Microsoft Graph Security tiIndicators API との直接統合を使用する TIP 製品またはアプリを構成します。
    
    a. 登録されているアプリケーションの ID、シークレット、およびテナント ID の値。
    
    b. ターゲット製品として、Azure Sentinel を指定します。
    
    c. アクションとして、アラートを指定します。

5. Azure portal で、 **[Azure Sentinel]**  >  **[Data connectors]\(データ コネクタ\)** に移動し、 **[Threat Intelligence Platforms (Preview)]\(脅威インテリジェンス プラットフォーム (プレビュー)\)** コネクタを選択します。

6. **[Open connector page]\(コネクタ ページを開く\)** を選択し、 **[接続]** を選択します。

7. Azure Sentinel にインポートされた脅威インジケーターを表示するには、 **[Azure Sentinel - ログ]**  >  **[SecurityInsights]** に移動し、 **[ThreatIntelligenceIndicator]** を展開します。

## <a name="connect-azure-sentinel-to-taxii-servers"></a>Azure Sentinel を TAXII サーバーに接続する

### <a name="prerequisites"></a>前提条件

- 脅威インジケーターを格納するための、Azure Sentinel ワークスペースに対する読み取りおよび書き込みアクセス許可。

- TAXII 2.0 サーバー URI とコレクション ID。

### <a name="instructions"></a>Instructions

1. Azure portal で、 **[Azure Sentinel]**  >  **[データ コネクタ]** に移動し、 **[脅威インテリジェンス - TAXII (プレビュー)]** コネクタを選択します。

2. **[Open connector page]\(コネクタ ページを開く\)** を選択します。

3. 必要な情報とオプションの情報をテキスト ボックスに指定します。

4. **[追加]** を選択して、TAXII 2.0 サーバーへの接続を有効にします。

5. 追加の TAXII 2.0 サーバーがある場合:手順 3 と 手順 4 を繰り返します。

6. Azure Sentinel にインポートされた脅威インジケーターを表示するには、 **[Azure Sentinel - ログ]**  >  **[SecurityInsights]** に移動し、 **[ThreatIntelligenceIndicator]** を展開します。

## <a name="next-steps"></a>次のステップ

このドキュメントでは、Azure Sentinel に脅威インテリジェンス プロバイダーを接続する方法を説明しました。 Azure Sentinel の詳細については、次の記事をご覧ください。

- [データと潜在的な脅威を可視化](quickstart-get-visibility.md)する方法についての説明。
- [Azure Sentinel を使用した脅威の検出](./tutorial-detect-threats-built-in.md)の概要。