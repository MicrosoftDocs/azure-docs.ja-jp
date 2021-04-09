---
title: 複数のワークスペースおよびテナントに Azure Sentinel を拡張する | Microsoft Docs
description: Azure Sentinel を使用して、複数のワークスペースやテナントのデータを対象にクエリと分析を行う方法について説明します。
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
ms.date: 09/11/2020
ms.author: yelevin
ms.openlocfilehash: 49b267d36fb6c365cf2125912c0d27fe7d669474
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "100585286"
---
# <a name="extend-azure-sentinel-across-workspaces-and-tenants"></a>ワークスペースおよびテナント全体での Azure Sentinel の拡張

## <a name="the-need-to-use-multiple-azure-sentinel-workspaces"></a>複数の Azure Sentinel ワークスペースを使用する必要がある場合

Azure Sentinel は、Log Analytics ワークスペースの上に構築されています。 Azure Sentinel をオンボードする最初のステップは、その目的に使用する Log Analytics ワークスペースを選択することです。

1 つのワークスペースを使用するのでも、Azure Sentinel のエクスペリエンスを最大限に利用できます。 しかし、複数のワークスペースを使用することが必要になる場合があります。 次の表では、このような状況をいくつか示します。可能であれば、1 つのワークスペースで要件を満たすことができる方法を示します。

| 要件 | 説明 | ワークスペースの数を減らす方法 |
|-------------|-------------|--------------------------------|
| 主権と規制のコンプライアンス | ワークスペースは、特定のリージョンに結び付けられます。 規制の要件を満たすためにデータを異なる [Azure の地域](https://azure.microsoft.com/global-infrastructure/geographies/)に保持する必要がある場合は、別のワークスペースに分割する必要があります。 |  |
| データ所有権 | データ所有権の境界 (たとえば、子会社や関連会社など) は、個別のワークスペースを使用するとより適切に線引きできます。 |  |
| 複数の Azure テナント | Azure Sentinel では、それ自体の Azure Active Directory (Azure AD) テナントの境界内にある Microsoft と Azure の SaaS リソースからのデータ収集だけがサポートされています。 そのため、Azure AD テナントごとに個別のワークスペースが必要です。 |  |
| 詳細なデータ アクセスの制御 | 組織によっては、組織の内部または外部の異なるグループに、Azure Sentinel によって収集されたデータの一部へのアクセスを許可することが必要になる場合があります。 次に例を示します。<br><ul><li>リソース所有者のリソースに関連するデータへのアクセス</li><li>地域または子会社の SOC による、組織のそれぞれの部分に関連するデータへのアクセス</li></ul> | [リソース Azure RBAC](https://techcommunity.microsoft.com/t5/azure-sentinel/controlling-access-to-azure-sentinel-data-resource-rbac/ba-p/1301463) または[テーブル レベル Azure RBAC](https://techcommunity.microsoft.com/t5/azure-sentinel/table-level-rbac-in-azure-sentinel/ba-p/965043) を使用します |
| 詳細な保有期間の設定 | 従来、複数のワークスペースは、異なるデータの種類に対して異なる保有期間を設定するための唯一の方法でした。 テーブル レベルの保有期間の設定の導入により、多くの場合、これは不要になりました。 | [テーブル レベルの保有期間の設定](https://techcommunity.microsoft.com/t5/azure-sentinel/new-per-data-type-retention-is-now-available-for-azure-sentinel/ba-p/917316)を使用するか、または[データの削除](../azure-monitor/logs/personal-data-mgmt.md#how-to-export-and-delete-private-data)を自動化します |
| 課金を分割する | 個別のサブスクリプションにワークスペースを配置することにより、異なるパーティに課金できます。 | 使用状況レポートとクロス請求 |
| レガシ アーキテクチャ | 複数のワークスペースの使用は、現在ではもう有効ではない制限やベスト プラクティスが考慮されていた以前の設計から生じている可能性があります。 また、設計に関する恣意的な選択であり、変更することで Azure Sentinel にいっそう適切に対応できる可能性もあります。<br><br>たとえば、次のようになります。<br><ul><li>Azure Security Center をデプロイするときの、サブスクリプションごとの既定のワークスペースの使用</li><li>解決策が比較的新しいものである、詳細なアクセス制御または保有期間の設定に対するニーズ</li></ul> | ワークスペースを再設計します |

### <a name="managed-security-service-provider-mssp"></a>マネージド セキュリティ サービス プロバイダー (MSSP)

複数のワークスペースが必要になる具体的なユースケースは、MSSP Azure Sentinel サービスです。 この場合、上記の要件のすべてではなくても多くが当てはまり、複数のワークスペースとテナントがベスト プラクティスになります。 MSSP では [Azure Lighthouse](../lighthouse/overview.md) を使用して、Azure Sentinel のクロスワークスペース機能をテナント間に拡張できます。

## <a name="azure-sentinel-multiple-workspace-architecture"></a>Azure Sentinel の複数ワークスペース アーキテクチャ

上記の要件で示されているように、複数の Azure Sentinel ワークスペース (場合によっては Azure Active Directory (Azure AD) の複数のテナントにまたがる) を、1 つの SOC で一元的に監視および管理することが必要になる場合があります。

- MSSP Azure Sentinel サービス。

- それぞれに独自のローカル SOC がある複数の子会社に対応するグローバル SOC。

- 組織内の複数の Azure AD テナントを監視する SOC。

この要件に対応するため、Azure Sentinel には、一元的な監視、構成、管理を可能にする複数ワークスペース機能が用意されており、次の図に示すように、SOC によってカバーされるすべてのものに対する単一のペインが提供されています。

:::image type="content" source="media/extend-sentinel-across-workspaces-tenants/cross-workspace-architecture.png" alt-text="クロス ワークスペース アーキテクチャ":::

このモデルでは、すべてのデータが 1 つのワークスペースにコピーされる完全な集中型モデルよりも大きなメリットが提供されます。

- グローバル SOC とローカル SOC、または顧客の MSSP に対する柔軟なロールの割り当て。

- データの所有権、データのプライバシー、規制のコンプライアンスに関する課題の減少。

- 最小限のネットワーク待機時間と料金。

- 新しい子会社または顧客の簡単なオンボードとオフボード。

以下のセクションでは、このモデルの使用方法と、特に次の方法について説明します。

- 複数のワークスペース (複数のテナントにわたる可能性がある) を一元的に監視し、SOC に 1 つのペインを提供します。

- 自動化を使用して、複数のワークスペース (複数のテナントにわたる可能性がある) を一元的に構成および管理します。

## <a name="cross-workspace-monitoring"></a>クロスワークスペース監視

### <a name="manage-incidents-on-multiple-workspaces"></a>複数のワークスペースのインシデントを管理する

Azure Sentinel では[複数ワークスペースのインシデント ビュー](./multiple-workspace-view.md)がサポートされており、複数のワークスペースでのインシデントの一元的な監視と管理が容易になります。 一元的なインシデント ビューを使用すると、インシデントを直接管理したり、元のワークスペースのコンテキストでインシデントの詳細に透過的にドリルダウンしたりできます。

### <a name="cross-workspace-querying"></a>クロスワークスペース クエリの実行

Azure Sentinel では、[1 つのクエリでの複数のワークスペース](../azure-monitor/logs/cross-workspace-query.md)の照会がサポートされており、1 つのクエリで複数のワークスペースのデータを検索して関連付けることができます。 

- 異なるワークスペースのテーブルを参照するには、[workspace() 式](../azure-monitor/logs/workspace-expression.md)を使用します。 
- 複数のワークスペースの複数のテーブルに対してクエリを適用するには、workspace() 式と共に [union 演算子](/azure/data-explorer/kusto/query/unionoperator?pivots=azuremonitor)を使用します。

保存されている[関数](../azure-monitor/logs/functions.md)を使用すると、クロスワークスペース クエリを簡単に行うことができます。 たとえば、ワークスペースへの参照が長い場合、式 `workspace("customer-A's-hard-to-remember-workspace-name").SecurityEvent` を `SecurityEventCustomerA` という名前の関数として保存することができます。 その後、`SecurityEventCustomerA | where ...` のようにクエリを記述できます。

関数を使用すると、よく使用される和集合を簡略化することもできます。 たとえば、次の式を `unionSecurityEvent` という名前の関数として保存できます。

`union workspace(“hard-to-remember-workspace-name-1”).SecurityEvent, workspace(“hard-to-remember-workspace-name-2”).SecurityEvent`

その後、`unionSecurityEvent | where ...` で始めることにより、両方のワークスペースに対するクエリを作成できます。

#### <a name="cross-workspace-analytics-rules"></a>クロスワークスペース分析ルール<a name="scheduled-alerts"></a>
<!-- Bookmark added for backward compatibility with old heading -->
クロスワークスペース クエリをスケジュールされた分析ルールに含めることができるようになりました。ただし、次の制限事項があります。

- 1 つのクエリに含めることができるワークスペースは、最大 20 個です。
- クエリで参照されているすべてのワークスペースに、Azure Sentinel をデプロイする必要があります。

> [!NOTE] 
> 同じクエリ内で複数のワークスペースに対してクエリを実行すると、パフォーマンスに影響する可能性があるので、ロジックでこの機能が必要なときにのみ推奨されます。

#### <a name="cross-workspace-workbooks"></a>クロスワークスペース ブック<a name="using-cross-workspace-workbooks"></a>
<!-- Bookmark added for backward compatibility with old heading -->
[ブック](./overview.md#workbooks)では、 Azure Sentinel にダッシュボードとアプリが提供されます。 複数のワークスペースを使用する場合、ワークスペース間の監視とアクションが提供されます。

ブックでは、3 つの方法のいずれかでクロスワークスペース クエリを提供できます。各方法は、異なるレベルのエンド ユーザーの専門知識に対応します。

| Method  | 説明 | 使用すべきとき |
|---------|-------------|--------------------|
| クロスワークスペース クエリを作成する | ブックの作成者は、ブック内でクロスワークスペース クエリ (前述) を作成できます。 | このオプションを使用すると、ブックの作成者は、ワークスペースの構造からユーザーを完全にシールドできます。 |
| ワークスペース セレクターをブックに追加する | ブックの作成者は、ブックの一部としてワークスペース セレクターを実装できます。詳細については[こちら](https://techcommunity.microsoft.com/t5/azure-sentinel/making-your-azure-sentinel-workbooks-multi-tenant-or-multi/ba-p/1402357)を参照してください。 | このオプションを使用すると、ユーザーは、使いやすいドロップダウン ボックスにより、ブックで示されるワークスペースを制御できます。 |
| ブックを対話形式で編集する | 既存のブックを変更する高度なユーザーは、その中のクエリを編集し、エディターのワークスペース セレクターを使用して対象のワークスペースを選択できます。 | このオプションを使用すると、パワー ユーザーは既存のブックを簡単に変更して、複数のワークスペースで作業できます。 |
|

#### <a name="cross-workspace-hunting"></a>クロスワークスペースの検出

Azure Sentinel には事前に読み込まれたクエリ例が用意されており、初めて使用するときや、テーブルとクエリ言語に不慣れなときに役立ちます。 これらの組み込み検出クエリは、Microsoft セキュリティ研究員が新しいクエリを追加し、既存のクエリを微調整しながら継続的に開発しているもので、新しい検出を探し、セキュリティ ツールによって検出されていない侵入の兆候を特定するためのエントリ ポイントが提供されます。  

クロスワークスペース検出機能により、脅威探索で、上記のような union 演算子と workspace () 式を使用して、新しい検索クエリを作成したり、既存のものを適合させたりして、複数のワークスペースを対象とすることができます。

## <a name="cross-workspace-management-using-automation"></a>自動化を使用するクロスワークスペース管理

複数の Azure Sentinel ワークスペースを構成して管理するには、Azure Sentinel 管理 API の使用を自動化する必要があります。 アラート ルール、ハンティング クエリ、ブック、プレイブックなど、Azure Sentinel リソースのデプロイを自動化する方法の詳細については、「[Azure Sentinel の拡張: API、統合、管理の自動化](https://techcommunity.microsoft.com/t5/azure-sentinel/extending-azure-sentinel-apis-integration-and-management/ba-p/1116885)」を参照してください。

また、Azure Sentinel をコードとして管理するための統合されたコミュニティ提供の方法、およびプライベート GitHub リポジトリからのリソースのデプロイと構成については、[コードとしての Azure Sentinel のデプロイと管理](https://techcommunity.microsoft.com/t5/azure-sentinel/deploying-and-managing-azure-sentinel-as-code/ba-p/1131928)に関するページと、[Azure Lighthouse と Azure Sentinel の DevOps 機能の結合](https://techcommunity.microsoft.com/t5/azure-sentinel/combining-azure-lighthouse-with-sentinel-s-devops-capabilities/ba-p/1210966)に関するページも参照してください。 

## <a name="managing-workspaces-across-tenants-using-azure-lighthouse"></a>Azure Lighthouse を使用したテナント間でのワークスペースの管理

前述のように、多くのシナリオでは、異なる Azure Sentinel ワークスペースを異なる Azure AD テナントに配置できます。 [Azure Lighthouse](../lighthouse/overview.md) を使用すると、テナントの境界を越えてすべてのクロスワークスペース アクティビティを拡張することができ、管理するテナントのユーザーは、すべてのテナントで Azure Sentinel ワークスペースを使用できるようになります。 Azure Lighthouse を[オンボード](../lighthouse/how-to/onboard-customer.md)した後、Azure portal で[ディレクトリとサブスクリプション セレクター](./multiple-tenants-service-providers.md#how-to-access-azure-sentinel-in-managed-tenants)を使用して、管理するワークスペースが含まれるすべてのサブスクリプションを選択します。これにより、ポータルの異なるワークスペース セレクターですべてのサブスクリプションを使用できるようになります。

Azure Lighthouse を使用するときは、Azure Sentinel ロールごとにグループを作成し、各テナントからそれらのグループにアクセス許可を委任することをお勧めします。

## <a name="next-steps"></a>次のステップ
このドキュメントでは、Azure Sentinel の機能を複数のワークスペースとテナントに拡張する方法について説明しました。 Azure Sentinel のクロスワークスペース アーキテクチャの実装に関する実用的なガイダンスについては、次の記事を参照してください。

- Azure Lighthouse を使用して、Azure Sentinel で[複数のテナントを操作する](./multiple-tenants-service-providers.md)方法を確認します。
- シームレスに[複数のワークスペースのインシデントを表示および管理する](./multiple-workspace-view.md)方法を確認します。
