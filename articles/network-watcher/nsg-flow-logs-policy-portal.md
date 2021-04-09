---
title: クイックスタート-Azure Policy を使用した NSG フロー ログのデプロイと管理
titleSuffix: Azure Network Watcher
description: この記事では、組み込みのポリシーを使用して、NSG フロー ログのデプロイを管理する方法について説明します。
services: network-watcher
documentationcenter: na
author: damendo
ms.service: network-watcher
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 01/07/2021
ms.author: damendo
ms.openlocfilehash: 8da1130809c1802f4db963f4b4b000a848e9abaa
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "98011103"
---
# <a name="quickstart-deploy-and-manage-nsg-flow-logs-using-azure-policy"></a>クイック スタート: Azure Policy を使用した NSG フロー ログのデプロイと管理 

## <a name="overview"></a>概要
Azure Policy は、組織の標準を適用し、コンプライアンスを大規模に評価するのに役立ちます。 Azure Policy の一般的なユースケースには、リソースの整合性、規制コンプライアンス、セキュリティ、コスト、管理のガバナンスの実装が含まれています。 この記事では、NSG フロー ログ用に用意されている 2 つの組み込みポリシーを使用して、フロー ログの設定を管理します。 最初のポリシーでは、フロー ログが有効になっていない NSG にフラグを設定します。 2 番目のポリシーは、フロー ログが有効になっていない NSG のフロー ログを自動的にデプロイします。 

Azure ポリシーを初めて作成する場合は、次を参照してください。 
- [Azure Policy の概要](../governance/policy/overview.md) 
- [ポリシーを作成するためのチュートリアル](../governance/policy/assign-policy-portal.md#create-a-policy-assignment)


## <a name="locate-the-policies"></a>ポリシーを見つける
1. Azure ポータル ([portal.azure.com](https://portal.azure.com)) に移動します。 

![ポリシーのホームページ](./media/network-watcher-builtin-policy/1_policy-search.png)上部の検索バーでポリシーを検索して Azure Policy ページに移動する

2. 左側ペインの **[Assignments (割り当て)]** タブに移動します。

![［割り当て］タブ](./media/network-watcher-builtin-policy/2_assignments-tab.png)

3. **[Assign Policy (ポリシーの割り当て)]** ボタンをクリックします。 

![[Assign Policy (ポリシーの割り当て)] ボタン](./media/network-watcher-builtin-policy/3_assign-policy-button.png)

4. [Policy Definitions (ポリシーの定義)] の下にある 3 つのドット メニューをクリックして、使用可能なポリシーを確認します。

5. 型フィルターを使用して、[Built-in (組み込み)] を選択します。 次に、「フロー ログ」を検索します。

フロー ログの2つの組み込みポリシー ![Policy List (ポリシーの一覧)](./media/network-watcher-builtin-policy/4_filter-for-flow-log-policies.png)が表示されます。

6. 割り当てるポリシーを選択します。

- *「フロー ログは、すべてのネットワーク セキュリティ グループに対して構成する必要があります」* は、準拠していない NSG にフラグを設定する監査ポリシーです。つまり、フロー ログが有効になっていない NSG です。
- *「ターゲット ネットワーク セキュリティ グループを使用してフロー ログ リソースをデプロイする」* というのは、展開アクションのあるポリシーであり、フロー ログのないすべての NSG のフロー ログを有効にします。

各ポリシーには、次のような個別の手順があります。  

## <a name="audit-policy"></a>監査ポリシー 

### <a name="how-the-policy-works"></a>ポリシーのしくみ

ポリシーは、"Microsoft. Network/networkSecurityGroups" という種類の既存の ARM オブジェクトをすべてチェックします。これは、特定のスコープ内のすべての NSG を確認し、NSG の Flow Logs プロパティを使用してリンクされたフロー ログの存在を確認します。 プロパティが存在しない場合は、NSG にフラグが設定されます。

ポリシーの完全な定義を確認するには [[Definition (定義)] ](https://ms.portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyMenuBlade/Definitions)タブにアクセスし、「Flow log (フローログ)」 を検索してポリシーを検索します。

### <a name="assignment"></a>割り当て

1. ポリシーの詳細を入力します

- スコープ:サブスクリプションを選択するのが一般的です。管理グループまたはユーザーに関係するリソースグループを選択することもできます。  
- ポリシー定義:「Locate the policies (ポリシーの検索)」セクションで示されているように選択する必要があります。
- AssignmentName:わかりやすい名前を使用します。 

2. [確認および作成] をクリックして、割り当て内容を確認します。

追加のパラメーターは必要ありません。 監査ポリシーを割り当てるときは、[Remediation (修復)] タブで詳細を入力する必要はありません。  

![監査ポリシーの確認](./media/network-watcher-builtin-policy/5_1_audit-policy-review.png)

### <a name="results"></a>結果

結果を確認するには、[Compliance (コンプライアンス)] タブを開き、割り当ての名前を検索します。
ポリシーを実行すると、次のスクリーンショットに似た内容が表示されます。 ポリシーが実行されていない場合は、しばらく待ってください。 

![監査ポリシーの結果](./media/network-watcher-builtin-policy/7_1_audit-policy-results.png)

## <a name="deploy-if-not-exists-policy"></a>Deploy-If-not-exists ポリシー 

### <a name="policy-structure"></a>ポリシー構造

ポリシーは、"Microsoft. Network/networkSecurityGroups" という種類の既存の ARM オブジェクトをすべてチェックします。これは、特定のスコープ内のすべての NSG を確認し、NSG の Flow Logs プロパティを使用してリンクされたフロー ログの存在を確認します。 プロパティが存在しない場合は、ポリシーによってフロー ログが展開されます。 

ポリシーの完全な定義を確認するには [[Definition (定義)] ](https://ms.portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyMenuBlade/Definitions)タブにアクセスし、「Flow log (フローログ)」 を検索してポリシーを検索します。 

### <a name="assignment"></a>割り当て

1. ポリシーの詳細を入力します

- スコープ:サブスクリプションを選択するのが一般的です。管理グループまたはユーザーに関係するリソースグループを選択することもできます。  
- ポリシー定義:「Locate the policies (ポリシーの検索)」セクションで示されているように選択する必要があります。
- AssignmentName:わかりやすい名前を使用します。 

2. ポリシーのパラメーターを追加します。 

Azure Network Watcher は地域サービスです。 これらのパラメーターを使用すると、フロー ログを展開するポリシーアクションを実行できます。 
- NSG リージョン:ポリシーの対象となる Azure リージョン
- Storage ID:ストレージ アカウントのフル リソース ID。 注:ストレージ アカウントは、NSG と同じリージョンに存在する必要があります。 
- Network Watchers RG:ご利用の Network Watcher リソースを含むリソース グループの名前。 名前を変更していない場合は、既定の "NetworkWatcherRG" を入力できます。
- Network Watcher 名:リージョンの Network Watcher サービスの名前。 形式:NetworkWatcher_RegionName。 例:NetworkWatcher_centralus. 完全なリストをご覧ください。

![DINE ポリシー パラメーター](./media/network-watcher-builtin-policy/5_2_1_dine-policy-details-alt.png)

3. 修復の詳細の追加

- ポリシーが既存のリソースに影響を与えるようにする場合は、[Create Remediation task (修復タスクの作成)] のチェックボックスをオンにします。 
- [Create a Managed Identity (マネージド ID の作成)] が既にオンになっている必要があります。
- マネージド ID に対して前回と同じ場所を選択しました。 
- このポリシーを使用するには、共同作成者または所有者のアクセス許可が必要です。 これらのアクセス許可がある場合、エラーは表示されません。

![DINE ポリシーの修復](./media/network-watcher-builtin-policy/5_2_2_dine-remediation.png) 

4. [Review + Create (レビュー + 作成)] をクリックして割り当てを確認すると、次のスクリーンショットのような内容が表示されます。

![DINE ポリシーの確認](./media/network-watcher-builtin-policy/5_2_3_dine-review.png) 


### <a name="results"></a>結果

結果を確認するには、[Compliance (コンプライアンス)] タブを開き、割り当ての名前を検索します。
ポリシーができると、次のようなスクリーンショットが表示されます。 ポリシーが実行されていない場合は、しばらく待ってください。

![DINE ポリシーの結果](./media/network-watcher-builtin-policy/7_2_dine-policy-results.png)  


## <a name="next-steps"></a>次の手順 

-   この [チュートリアル](./quickstart-configure-network-security-group-flow-logs-from-arm-template.md)では、ARM テンプレートを使用してフローログとトラフィック解析を展開することでさらに詳しく します。
-   [Network Watcher ](./index.yml)の詳細