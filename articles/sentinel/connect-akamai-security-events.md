---
title: Azure Sentinel に Akamai セキュリティ イベント コレクターを接続する | Microsoft Docs
description: Akamai セキュリティ イベント コネクタを使用して、Akamai ソリューションのセキュリティ ログを Azure Sentinel にプルする方法について説明します。 Akamai のデータをブックに表示し、アラートを作成し、調査を改善します。
services: sentinel
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
ms.date: 02/03/2021
ms.author: yelevin
ms.openlocfilehash: 8aa5a52a06713b4f00b43205a57148049a8ef8da
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "101711962"
---
# <a name="connect-your-akamai-security-events-collector-to-azure-sentinel"></a>Azure Sentinel に Akamai セキュリティ イベント コレクターを接続する

> [!IMPORTANT]
> Akamai セキュリティ イベント コネクタは、現在 **プレビュー** 段階です。 ベータ版、プレビュー版、または一般提供としてまだリリースされていない Azure の機能に適用されるその他の法律条項については、「[Microsoft Azure プレビューの追加使用条件](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)」を参照してください。

この記事では、Azure Sentinel に Akamai セキュリティ イベント コレクターを接続する方法について説明します。 Akamai セキュリティ イベント データ コネクタを使用すると、Akamai ログを Azure Sentinel に簡単に接続できます。これにより、そのデータをブックで表示したり、カスタム アラートの作成のためにクエリを実行したり、調査の改善のために取り込んだりできます。 Akamai セキュリティ イベント コレクターと Azure Sentinel の統合には、CEF 形式の Syslog、Linux ベースのログ フォワーダー、Log Analytics エージェントを使用します。 また、Kusto 関数に基づくカスタムビルドのログ パーサーも使用されます。

> [!NOTE]
> データは、Azure Sentinel を実行しているワークスペースの地理的な場所に格納されます。

## <a name="prerequisites"></a>前提条件

- Azure Sentinel ワークスペースに対する読み取りおよび書き込みアクセス許可が必要です。

- ワークスペースの共有キーに対する読み取りアクセス許可が必要です。 ワークスペース キーの詳細については、[こちら](../azure-monitor/agents/log-analytics-agent.md#workspace-id-and-key)を参照してください。

## <a name="send-akamai-security-events-logs-to-azure-sentinel"></a>Akamai セキュリティ イベントのログを Azure Sentinel に送信する

ログを Azure Sentinel に送るには、Linux ベースのログ転送サーバー (rsyslog または syslog-ng を実行) に Syslog メッセージを CEF 形式で送信するように Akamai セキュリティ イベント コレクターを構成します。 このサーバーには Log Analytics エージェントをインストールします。このエージェントで、ログをお使いの Azure Sentinel ワークスペースに転送します。

1. Azure Sentinel のナビゲーション メニューで、 **[データ コネクタ]** を選択します。

1. **[データ コネクタ]** ギャラリーから、 **[Akamai Security Events (Preview)]\(Akamai セキュリティ イベント (プレビュー)\)** を選択し、 **[コネクタ ページを開く]** を選択します。

1. **[手順]** タブの **[構成]** の手順に従います。

    1. **[1. Linux Syslog agent configuration]\(Linux Syslog エージェントの構成\)** - ログ フォワーダーをまだ実行していない場合、または別のものが必要な場合は、この手順を実行します。 サイズ情報、詳細な手順、詳しい説明については、Azure Sentinel ドキュメントの「[手順 1: ログ フォワーダーをデプロイする](connect-cef-agent.md)」を参照してください。

    1. **[2. Common Event Format (CEF) ログを Syslog エージェントに転送する]** - Akamai の指示に従い、[SIEM 統合を構成](https://developer.akamai.com/tools/integrations/siem)し、[CEF コネクタを設定](https://developer.akamai.com/tools/integrations/siem/siem-cef-connector)します。 このコネクタは、SIEM OPEN API を使用して、Akamai ソリューションからセキュリティ イベントをほぼリアルタイムで受信し、それらを JSON から CEF 形式に変換します。
    
        この構成には、次の要素を含める必要があります。
    
        - ログの送信先 – お使いのログ転送サーバーのホスト名、IP アドレス、またはこれらの両方
        - プロトコルとポート – TCP 514 (これ以外が推奨されている場合は、お使いのログ転送サーバーの syslog デーモンで同様の変更を行ってください)
        - ログの形式 – CEF
        - ログの種類 – すべて使用可能

    1. **3. Validate connection\(接続を検証する\)** - コネクタ ページのコマンドをコピーし、ログ フォワーダーでそれを実行することで、データの取り込みを検証します。 詳細な手順および説明については、Azure Sentinel のドキュメントの「[手順 3: 接続を検証する](connect-cef-verify.md)」を参照してください。

        ログが Log Analytics に表示され始めるまで、最大 20 分かかることがあります。

## <a name="find-your-data"></a>データの検索

接続が正常に確立されると、 *[CommonSecurityLog]* テーブルにある **[Azure Sentinel]** セクションの **[ログ]** にデータが表示されます。

このデータ コネクタは、Kusto 関数に基づくパーサーに依存して正常に動作します。 次の手順に従って、クエリおよびブックで使用する **AkamaiSIEMEvent** Kusto 関数を設定します。

1. Azure Sentinel のナビゲーション メニューから **[ログ]** を選択します。

1. 次のクエリをコピーして、クエリ ウィンドウに貼り付けます。
    ```kusto
    CommonSecurityLog 
    | where DeviceVendor == 'Akamai'
    | where DeviceProduct == 'akamai_siem'
    | extend EventVendor = 'Akamai'
    | extend EventProduct = 'akamai_siem'
    | extend EventProductVersion = '1.0'
    | extend EventId = DeviceEventClassID
    | extend EventCategory = Activity
    | extend EventSeverity = LogSeverity
    | extend DvcAction = DeviceAction
    | extend NetworkApplicationProtocol = ApplicationProtocol
    | extend Ipv6Src = DeviceCustomIPv6Address2
    | extend RuleName = DeviceCustomString1
    | extend RuleMessages = DeviceCustomString2
    | extend RuleData = DeviceCustomString3
    | extend RuleSelectors = DeviceCustomString4
    | extend ClientReputation = DeviceCustomString5
    | extend ApiId = DeviceCustomString6
    | extend RequestId = DevicePayloadId
    | extend DstDvcHostname = DestinationHostName
    | extend DstPortNumber = DestinationPort
    | extend ConfigId = FlexString1
    | extend PolicyId = FlexString2
    | extend NetworkBytes = SentBytes
    | extend UrlOriginal = RequestURL
    | extend HttpRequestMethod = RequestMethod
    | extend SrcIpAddr = SourceIP
    | extend EventStartTime = datetime(1970-01-01) + tolong(extract(@'.*start=(.*?);', 1, AdditionalExtensions)) * 1s 
    | extend SlowPostAction = extract(@'.*AkamaiSiemSlowPostAction=(.*?);', 1, AdditionalExtensions)
    | extend SlowPostRate = extract(@'.*AkamaiSiemSlowPostRate=(.*?);', 1, AdditionalExtensions)
    | extend RuleVersions = extract(@'.*AkamaiSiemRuleVersions=,?(.*?);', 1, AdditionalExtensions)
    | extend RuleTags = extract(@'.*AkamaiSiemRuleTags=(.*?);', 1, AdditionalExtensions)
    | extend ApiKey = extract(@'.*AkamaiSiemApiKey=(.*?);', 1, AdditionalExtensions)
    | extend Tls = extract(@'.*AkamaiSiemTLSVersion=(.*?);', 1, AdditionalExtensions)
    | extend RequestHeaders = extract(@'.*AkamaiSiemRequestHeaders=;?(.*?);', 1, AdditionalExtensions)
    | extend ResponseHeaders = extract(@'.*AkamaiSiemResponseHeaders=(.*?);', 1, AdditionalExtensions)
    | extend HttpStatusCode = extract(@'.*AkamaiSiemResponseStatus=(.*?);', 1, AdditionalExtensions)
    | extend GeoContinent = extract(@'.*AkamaiSiemContinent=(.*?);', 1, AdditionalExtensions)
    | extend SrcGeoCountry = extract(@'.*AkamaiSiemCountry=(.*?);', 1, AdditionalExtensions)
    | extend SrcGeoCity = extract(@'.*AkamaiSiemCity=(.*?);', 1, AdditionalExtensions)
    | extend SrcGeoRegion = extract(@'.*AkamaiSiemRegion=(.*?);', 1, AdditionalExtensions)
    | extend GeoAsn = extract(@'.*AkamaiSiemASN=(\d+)', 1, AdditionalExtensions)
    | extend Custom = extract(@'.*AkamaiSiemCusomData=(.*?)', 1, AdditionalExtensions)
    | project TimeGenerated
            , EventVendor
            , EventProduct
            , EventProductVersion
            , EventStartTime
            , EventId
            , EventCategory
            , EventSeverity
            , DvcAction
            , NetworkApplicationProtocol
            , Ipv6Src
            , RuleName
            , RuleMessages
            , RuleData
            , RuleSelectors
            , ClientReputation
            , ApiId
            , RequestId
            , DstDvcHostname
            , DstPortNumber
            , ConfigId
            , PolicyId
            , NetworkBytes
            , UrlOriginal
            , HttpRequestMethod
            , SrcIpAddr
            , SlowPostAction
            , SlowPostRate
            , RuleVersions
            , RuleTags
            , ApiKey
            , Tls
            , RequestHeaders
            , ResponseHeaders
            , HttpStatusCode
            , GeoContinent
            , SrcGeoCountry
            , SrcGeoCity
            , SrcGeoRegion
            , GeoAsn
            , Custom
    ```

1. **[保存]** ドロップダウンをクリックし、 **[保存]** をクリックします。 **[保存]** パネルで、次の手順に従います。

    1. **[名前]** に、「**AkamaiSIEMEvent**」と入力します。

    1. **[名前を付けて保存]** で、 **[関数]** を選択します。

    1. **[関数のエイリアス]** に、「**AkamaiSIEMEvent**」と入力します。

    1. **[カテゴリ]** に、「**関数**」と入力します。

    1. **[保存]** をクリックします。

    関数アプリのアクティブ化には、通常、10 分から 15 分かかります。

これで、クエリ ウィンドウの一番上の行に `AkamaiSIEMEvent` と入力すると、Akamai データのクエリを実行する準備が整います。

その他のクエリの例については、コネクタ ページの **[Next steps]\(次の手順\)** タブを参照してください。

## <a name="next-steps"></a>次の手順

このドキュメントでは、Akamai セキュリティ イベントを Azure Sentinel に接続する方法について説明しました。 Azure Sentinel の詳細については、次の記事をご覧ください。

- [データと潜在的な脅威を可視化](quickstart-get-visibility.md)する方法についての説明。
- [Azure Sentinel を使用した脅威の検出](tutorial-detect-threats-built-in.md)の概要。
- [ブックを使用](tutorial-monitor-your-data.md)してデータを監視する。