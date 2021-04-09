---
title: Azure Sentinel のユーザーとエンティティの行動分析 (UEBA) を使用して高度な脅威を特定する | Microsoft Docs
description: エンティティ (ユーザー、ホスト名、IP アドレス) の行動ベースラインを作成し、それを使用して異常な行動を検出し、ゼロディの高度な永続的脅威 (APT) を特定します。
services: sentinel
documentationcenter: na
author: yelevin
manager: rkarlin
editor: ''
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 02/10/2021
ms.author: yelevin
ms.openlocfilehash: bf7a17d96d31fd4214d5465a5739acc9ce9a9d53
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "102455503"
---
# <a name="identify-advanced-threats-with-user-and-entity-behavior-analytics-ueba-in-azure-sentinel"></a>Azure Sentinel のユーザーとエンティティの行動分析 (UEBA) を使用して高度な脅威を特定する

> [!IMPORTANT]
>
> - UEBA およびエンティティ ページ機能は、Azure Sentinel の "**_すべて_**" の地域とリージョンで **一般提供** になりました。

## <a name="what-is-user-and-entity-behavior-analytics-ueba"></a>ユーザーとエンティティの行動分析 (UEBA) の概要

### <a name="the-concept"></a>概念

組織内の脅威とその潜在的な影響を特定することは (それが侵害されたエンティティ、または悪意のある内部関係者かどうかには関係なく) 常に、時間のかかる、労働集約的なプロセスでした。 アラートの選別、ドットの接続、積極的なハンティングがすべて一緒になって膨大な量の時間と労力が費やされますが、最小限の結果しか得られず、高度な脅威の可能性は簡単に検出を回避します。 特に、対象を絞った、ゼロディの高度な永続的脅威などの捕らえにくい脅威は、組織にとって最も危険なものになることがあるため、それらの検出がますます重要になっています。

Azure Sentinel の UEBA 機能は、アナリストのワークロードから面倒な作業を、またその取り組みから不確実性を排除し、忠実度が高い、すぐに使用可能なインテリジェンスを提供するため、アナリストは調査と修復に集中することができます。

Azure Sentinel は、接続されているすべてのデータ ソースからログとアラートを収集すると、それらを分析して、時間とピア グループの期間全体にわたる組織のエンティティ (ユーザー、ホスト、IP アドレス、アプリケーションなど) のベースライン行動プロファイルを構築します。 Azure Sentinel はその後、さまざまな手法や機械学習機能を使用して異常なアクティビティを特定できるため、資産が侵害されているかどうかを判定するために役立ちます。 それだけではなく、特定の資産の相対的な機密性を見つけたり、資産のピア グループを識別したり、特定の侵害された資産の潜在的な影響 (その "影響範囲") を評価したりすることもできます。 これらの情報を利用して、調査やインシデント処理に効果的に優先順位を付けることができます。 

### <a name="architecture-overview"></a>アーキテクチャの概要

:::image type="content" source="media/identify-threats-with-entity-behavior-analytics/entity-behavior-analytics-architecture.png" alt-text="エンティティの行動分析のアーキテクチャ":::

### <a name="security-driven-analytics"></a>セキュリティ主導の分析

UEBA ソリューションに対する Gartner のパラダイムから着想を得て、Azure Sentinel は、次の 3 つの参照フレームに基づいた "アウトサイドイン" アプローチを提供します。

- **ユース ケース:** さまざまなエンティティを被害者、加害者、またはピボット ポイントとしてキル チェーン内に配置する、戦術、テクニック、サブテクニックの MITRE ATT&CK フレームワークに対応したセキュリティ調査に基づいて、関連する攻撃ベクトルとシナリオに優先順位を付けることにより、Azure Sentinel は特に、各データ ソースが提供できる最も重要なログに焦点を置いています。

- **データ ソース:** まず何よりも Azure データ ソースをサポートしていますが、Azure Sentinel は、脅威シナリオに一致するデータを提供するためにサード パーティのデータ ソースを慎重に選択します。

- **分析:** さまざまな機械学習 (ML) アルゴリズムを使用して、Azure Sentinel は異常なアクティビティを特定し、コンテキスト エンリッチメントの形式で明確かつ簡潔に証拠を提供します。そのいくつかの例を次に示します。

    :::image type="content" source="media/identify-threats-with-entity-behavior-analytics/behavior-analytics-top-down.png" alt-text="行動分析のアウトサイドイン アプローチ":::

Azure Sentinel は、セキュリティ アナリストが、ユーザーのベースライン プロファイルのコンテキストで、かつそれとの比較で異常なアクティビティを明確に理解するために役立つアーティファクトを提供します。 ユーザー (またはホスト、またはアドレス) によって実行されたアクションは、次のようにコンテキストから評価されます。ここで、"true" の結果は特定された異常を示します。
- 地理的な場所、デバイス、環境にまたがって。
- 時間と頻度の期間にまたがって (ユーザー独自の履歴と比較して)。
- ピアの行動と比較して。
- 組織の行動と比較して。

    :::image type="content" source="media/identify-threats-with-entity-behavior-analytics/context.png" alt-text="エンティティのコンテキスト":::


### <a name="scoring"></a>ポイントの計算

各アクティビティは、"調査の優先順位のスコア" でスコア付けされます。これにより、ユーザーとそのピアの行動学習に基づいて、特定のユーザーが特定のアクティビティを実行する確率が決定されます。 最も異常であると識別されたアクティビティが最高のスコアを受け取ります (スケールは 0 ～ 10)。

この動作の例については、[Microsoft Cloud App Security](https://techcommunity.microsoft.com/t5/microsoft-security-and/prioritize-user-investigations-in-cloud-app-security/ba-p/700136) で行動分析がどのように使用されているかを参照してください。

## <a name="entity-pages"></a>エンティティ ページ

[Azure Sentinel のエンティティ](entities-in-azure-sentinel.md)に関する詳細と、[サポートされているエンティティと識別子](entities-reference.md)の完全一覧をご覧ください。

検索、アラート、または調査で何らかのエンティティ (現在はユーザーとホストに制限されています) を検出した場合は、そのエンティティを選択して **エンティティ ページ** に移動できます。これは、そのエンティティに関する役立つ情報が豊富に含まれているデータシートです。 このページで見つけることができる情報の種類には、そのエンティティについての基本的な事実、このエンティティに関連した注目すべきイベントのタイムライン、そのエンティティの行動に関する分析情報が含まれます。
 
エンティティ ページは、次の 3 つの部分で構成されます。
- 左側のパネルには、Azure Active Directory、Azure Monitor、Azure Security Center、Microsoft Defender などのデータ ソースから収集された、そのエンティティの識別情報が含まれています。

- 中央のパネルには、そのエンティティに関連した注目すべきイベント (アラート、ブックマーク、アクティビティなど) のグラフとテキストの両方のタイムラインが表示されます。 アクティビティは、Log Analytics からの注目すべきイベントの集計です。 これらのアクティビティを検出するクエリは、Microsoft のセキュリティ調査チームによって開発されています。

- 右側のパネルには、エンティティに関する行動分析情報が表示されます。 これらの分析情報は、異常やセキュリティの脅威をすばやく特定するために役立ちます。 この分析情報は Microsoft のセキュリティ調査チームによって開発され、異常検出モデルに基づいています。

### <a name="the-timeline"></a>タイムライン

:::image type="content" source="./media/identify-threats-with-entity-behavior-analytics/entity-pages-timeline.png" alt-text="エンティティ ページのタイムライン":::

タイムラインは、エンティティ ページが Azure Sentinel の行動分析に寄与している内容の主要な部分です。 ここには、エンティティ関連のイベントに関する項目が表示されるため、特定の期間内のエンティティのアクティビティを理解するのに役立ちます。

事前に設定されたいくつかのオプション ( *[過去 24 時間]* など) の中から **[時間範囲]** を選択するか、またはそれをカスタム定義の期間に設定できます。 さらに、タイムライン内の情報を特定の種類のイベントまたはアラートに制限するフィルターを設定できます。

タイムラインには、次の種類の項目が含まれています。

- アラート - そのエンティティが **[マップされたエンティティ]** として定義されているすべてのアラート。 組織で[分析ルールを使用したカスタム アラート](./tutorial-detect-threats-custom.md)が作成されている場合は、ルールのエンティティ マッピングが正しく実行されていることを確認する必要がある点に注意してください。

- ブックマーク - ページにその特定のエンティティが表示されているすべてのブックマーク。

- アクティビティ - そのエンティティに関連した注目すべきイベントの集計。 
 
### <a name="entity-insights"></a>エンティティ分析情報
 
エンティティ分析情報は、アナリストがより効率的かつ効果的に調査できるようにするために Microsoft のセキュリティ研究者によって定義されたクエリです。 この分析情報はエンティティ ページの一部として表示され、ホストとユーザーに関する重要なセキュリティ情報を表形式データとグラフの両方の形式で提供します。 ここに情報が表示されるため、Log Analytics に迂回する必要はありません。 この分析情報には、サインイン、グループの追加、異常なイベントなどに関連したデータや、異常な行動を検出するための高度な ML アルゴリズムが含まれています。 

この分析情報は、次のデータ ソースに基づいています。
- Syslog (Linux)
- SecurityEvent (Windows)
- AuditLogs (Azure AD)
- SigninLogs (Azure AD)
- OfficeActivity (Office 365)
- BehaviorAnalytics (Azure Sentinel UEBA)
- Heartbeat (Azure Monitor Agent)
- CommonSecurityLog (Azure Sentinel)

### <a name="how-to-use-entity-pages"></a>エンティティ ページを使用する方法

エンティティ ページは、複数の使用シナリオの一部になるように設計されており、インシデント管理、調査グラフ、ブックマークから、または Azure Sentinel のメイン メニューの **[エンティティの行動分析]** の下のエンティティ検索ページから直接アクセスできます。

:::image type="content" source="./media/identify-threats-with-entity-behavior-analytics/entity-pages-use-cases.png" alt-text="エンティティ ページのユース ケース":::

## <a name="data-schema"></a>データ スキーマ

### <a name="behavior-analytics-table"></a>行動分析テーブル

| フィールド                     | 説明                                                         |
|---------------------------|---------------------------------------------------------------------|
| TenantId                  | テナントの一意の ID 番号                                      |
| SourceRecordId            | EBA イベントの一意の ID 番号                                   |
| TimeGenerated             | アクティビティの発生のタイムスタンプ                              |
| TimeProcessed             | EBA エンジンによるアクティビティの処理のタイムスタンプ            |
| ActivityType              | アクティビティの高レベルのカテゴリ                                 |
| ActionType                | アクティビティの標準化名                                     |
| UserName                  | アクティビティを開始したユーザーのユーザー名                    |
| UserPrincipalName         | アクティビティを開始したユーザーの完全なユーザー名               |
| EventSource               | 元のイベントを提供したデータ ソース                        |
| SourceIPAddress           | アクティビティが開始された元の IP アドレス                        |
| SourceIPLocation          | アクティビティが開始された元の国 (IP アドレスからエンリッチ処理済み) |
| SourceDevice              | アクティビティを開始したデバイスのホスト名                  |
| DestinationIPAddress      | アクティビティのターゲットの IP アドレス                            |
| DestinationIPLocation     | アクティビティのターゲットの国 (IP アドレスからエンリッチ処理済み)     |
| DestinationDevice         | ターゲット デバイスの名前                                           |
| **UsersInsights**         | 関連するユーザーのコンテキスト エンリッチメント                            |
| **DevicesInsights**       | 関連するデバイスのコンテキスト エンリッチメント                          |
| **ActivityInsights**      | プロファイリングに基づくアクティビティのコンテキスト分析              |
| **InvestigationPriority** | 0 ～ 10 の異常スコア (0=無害、10=きわめて異常)         |
|

**UsersInsights**、**DevicesInsights**、**ActivityInsights** で参照されるすべてのコンテキスト エンリッチメントについては、[UEBA エンリッチメントのリファレンス ドキュメント](ueba-enrichments.md)で確認できます。

### <a name="querying-behavior-analytics-data"></a>行動分析データへのクエリ実行

[KQL](/azure/data-explorer/kusto/query/) を使用すると、行動分析テーブルにクエリを実行できます。

たとえば、Azure リソースへのサインインに失敗したユーザーのすべてのケースを見つけたい場合 (それがそのユーザーの特定の国からの最初の接続の試みであり、そのユーザーのピアに対してもその国からの接続はまれである場合) は、次のクエリを使用できます。

```Kusto
BehaviorAnalytics
| where ActivityType == "FailedLogOn"
| where FirstTimeUserConnectedFromCountry == True
| where CountryUncommonlyConnectedFromAmongPeers == True
```

### <a name="user-peers-metadata---table-and-notebook"></a>ユーザー ピア メタデータ - テーブルとノートブック

ユーザー ピア メタデータは、脅威の検出、インシデントの調査、潜在的な脅威のハンティングにおける重要なコンテキストを提供します。 セキュリティ アナリストは、あるユーザーのピアの通常のアクティビティを観察して、そのユーザーのアクティビティがピアのアクティビティと比較して異常であるかどうかを判定できます。

Azure Sentinel は、ユーザーの Azure AD セキュリティ グループ メンバーシップ、メーリング リストなどに基づいて、そのユーザーのピアを計算してランク付けし、1 ～ 20 にランク付けされたピアを **UserPeerAnalytics** テーブルに格納します。 次のスクリーンショットは UserPeerAnalytics テーブルのスキーマを示し、ユーザー Kendall Collins の上位 8 つのランク付けされたピアを表示しています。 Azure Sentinel は、*用語の出現頻度/逆文書頻度* (TF-IDF) アルゴリズムを使用して、ランク付けを計算するための重みを正規化します。グループが小さいほど、重みが大きくなります。 

:::image type="content" source="./media/identify-threats-with-entity-behavior-analytics/user-peers-metadata.png" alt-text="ユーザー ピア メタデータ テーブルのスクリーンショット":::

Azure Sentinel GitHub リポジトリで提供されている [Jupyter ノートブック](https://github.com/Azure/Azure-Sentinel-Notebooks/tree/master/BehaviorAnalytics/UserSecurityMetadata)を使用して、ユーザー ピア メタデータを視覚化できます。 このノートブックを使用する方法の詳細な手順については、[ガイド付き分析 - ユーザー セキュリティ メタデータ](https://github.com/Azure/Azure-Sentinel-Notebooks/blob/master/BehaviorAnalytics/UserSecurityMetadata/Guided%20Analysis%20-%20User%20Security%20Metadata.ipynb)のノートブックを参照してください。

### <a name="permission-analytics---table-and-notebook"></a>アクセス許可分析 - テーブルとノートブック

アクセス許可分析は、攻撃者による組織資産の侵害の潜在的な影響を特定するために役立ちます。 この影響は、資産の "影響範囲" とも呼ばれます。 セキュリティ アナリストは、この情報を使用して、調査やインシデント処理に優先順位を付けることができます。

Azure Sentinel は、ユーザーが直接、またはグループやサービス プリンシパル経由でアクセスできる Azure サブスクリプションを評価することによって、Azure リソースへの特定のユーザーによって保持されている直接および推移的なアクセス権を特定します。 この情報や、そのユーザーの Azure AD セキュリティ グループ メンバーシップの完全な一覧は、その後 **UserAccessAnalytics** テーブルに格納されます。 次のスクリーンショットは、ユーザー Alex Johnson に関する UserAccessAnalytics テーブル内のサンプル行を示しています。 **[ソース エンティティ]** はユーザーまたはサービス プリンシパル アカウントであり、 **[ターゲット エンティティ]** はソース エンティティがアクセスできるリソースです。 **[アクセス レベル]** と **[アクセスの種類]** の値は、ターゲット エンティティのアクセス制御モデルによって異なります。 Alex が Azure サブスクリプション *Contoso Hotels Tenant* に対する共同作成者のアクセス権を持っていることがわかります。 このサブスクリプションのアクセス制御モデルは Azure RBAC です。   

:::image type="content" source="./media/identify-threats-with-entity-behavior-analytics/user-access-analytics.png" alt-text="ユーザー アクセス分析テーブルのスクリーンショット":::

Azure Sentinel GitHub リポジトリの [Jupyter ノートブック](https://github.com/Azure/Azure-Sentinel-Notebooks/tree/master/BehaviorAnalytics/UserSecurityMetadata) (前に説明したものと同じノートブック) を使用して、アクセス許可分析データを視覚化できます。 このノートブックを使用する方法の詳細な手順については、[ガイド付き分析 - ユーザー セキュリティ メタデータ](https://github.com/Azure/Azure-Sentinel-Notebooks/blob/master/BehaviorAnalytics/UserSecurityMetadata/Guided%20Analysis%20-%20User%20Security%20Metadata.ipynb)のノートブックを参照してください。

### <a name="hunting-queries-and-exploration-queries"></a>ハンティング クエリと探索クエリ

Azure Sentinel には、BehaviorAnalytics テーブルに基づいた、一連のハンティング クエリ、探索クエリ、ブックが標準で用意されています。 これらのツールは、異常な行動を示す、特定のユース ケースに焦点を絞ったエンリッチ処理されたデータを提供します。 

Azure Sentinel でのハンティングと調査グラフの詳細については、[ここ](./hunting.md) を参照してください。

## <a name="next-steps"></a>次のステップ
このドキュメントでは、Azure Sentinel のエンティティの行動分析機能について学習しました。 実装や、取得した分析情報を使用する方法に関する実用的なガイダンスについては、次の記事を参照してください。

- Azure Sentinel の[エンティティの行動分析を有効にする](./enable-entity-behavior-analytics.md)。
- [セキュリティの脅威を検出する](./hunting.md)。
