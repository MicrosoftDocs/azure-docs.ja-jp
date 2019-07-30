---
title: Azure Cost Management での AWS のコストと使用状況の管理
description: この記事では、Cost Management でのコスト分析と予算を使用して、AWS のコストと使用状況を管理する方法を理解できます。
services: cost-management
keywords: ''
author: bandersmsft
ms.author: banders
ms.date: 04/26/2019
ms.topic: conceptual
ms.service: cost-management
manager: ormaoz
ms.custom: ''
ms.openlocfilehash: 57e66d449b194662bfc03f7e130cf49c02a15793
ms.sourcegitcommit: a52d48238d00161be5d1ed5d04132db4de43e076
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/20/2019
ms.locfileid: "67275709"
---
# <a name="manage-aws-costs-and-usage-in-azure"></a>Azure での AWS のコストと使用状況の管理

Azure Cost Management のための AWS のコストおよび使用レポートを設定して構成したら、AWS のコストと使用状況を開始する準備ができたことになります。 この記事では、Cost Management でのコスト分析と予算を使用して、AWS のコストと使用状況を管理する方法を理解できます。

統合をまだ構成していない場合は、「[AWS の使用状況レポート統合の設定と構成](aws-integration-set-up-configure.md)」を参照してください。

_開始する前に_:コスト分析に慣れていない場合は、「[コスト分析を使用したコストの調査と分析](quick-acm-cost-analysis.md)」クイックスタートを参照してください。 Azure での予算に慣れていない場合は、「[Azure の予算の作成と管理](tutorial-acm-create-budgets.md)」チュートリアルを参照してください。

## <a name="view-aws-costs-in-cost-analysis"></a>コスト分析での AWS のコストの表示

AWS のコストは、次のスコープのコストの分析で使用できます。

- 管理グループ下の AWS のリンクされたアカウント
- AWS のリンクされたアカウントのコスト
- AWS 統合アカウントのコスト

次のセクションでは、スコープを使用してそれぞれのコストと使用状況のデータを表示する方法について説明します。

### <a name="view-aws-linked-accounts-under-a-management-group"></a>管理グループ下の AWS のリンクされたアカウントの表示

さまざまなサブスクリプションおよびリンクされたアカウントから集計されたコストを確認する唯一の方法は、管理グループのスコープを使用してコストを表示することです。 管理グループを使用して、クラウド間のビューを提供します。

コスト分析で、スコープ ピッカーを開き、AWS のリンクされたアカウントを保持する管理グループを選択します。 Azure portal での画像の例を示します。

![[スコープの選択] ビューの例](./media/aws-integration-manage/select-scope01.png)



プロバイダー (Azure と AWS) でグループ化された、コスト分析での管理グループのコストを示す例を次に示します。

![コスト分析での四半期の Azure と AWS のコストを示す例](./media/aws-integration-manage/cost-analysis-aws-azure.png)

### <a name="view-aws-linked-account-costs"></a>AWS のリンクされたアカウントのコストの表示

AWS のリンクされたアカウントのコストを表示するには、スコープ ピッカーを開き、AWS のリンクされたアカウントを選択します。 リンクされたアカウントは、AWS のコネクタでの定義に従って管理グループに関連付けられていることに注意してください。

AWS のリンクされたアカウントのスコープを選択する例を次に示します。

![[スコープの選択] ビューの例](./media/aws-integration-manage/select-scope02.png)



### <a name="view-aws-consolidated-account-costs"></a>AWS 統合アカウントのコストの表示

AWS 統合アカウントのコストを表示するには、スコープ ピッカーを開き、AWS 統合アカウントを選択します。 AWS 統合アカウントのスコープを選択する例を示します。

![[スコープの選択] ビューの例](./media/aws-integration-manage/select-scope03.png)



このスコープは、AWS 統合アカウントに関連付けられているすべての AWS のリンクされたアカウントの集計ビューを提供します。 サービス名別にグループ化された AWS 統合アカウントのコストの例を次に示します。

![コスト分析で AWS 統合コストを示す例](./media/aws-integration-manage/cost-analysis-aws-consolidated.png)

### <a name="dimensions-available-for-filtering-and-grouping"></a>フィルター処理やグループ化に使用できるディメンション

次の表では、コスト分析でのグループ化やフィルター処理に使用できるディメンションについて説明します。

| Dimension | Amazon CUR ヘッダー | スコープ | 説明 |
| --- | --- | --- | --- |
| 可用性ゾーン | lineitem/AvailabilityZone | All |   |
| Location | product/Region | All |   |
| 測定 |   | All |   |
| Meter category | lineItem/ProductCode | All |   |
| Meter subcategory | lineitem/UsageType | All |   |
| Operation | lineItem/Operation | All |   |
| Resource | lineItem/ResourceId | All |   |
| リソースの種類 | product/instanceType | All | product/instanceType が null の場合は、UsageType が使用されます。 |
| ResourceGuid | 該当なし | All | Azure メーターの GUID。 |
| サービス名 | product/ProductName | All | product/ProductName が null の場合は、lineItem/ProductCode が使用されます。 |
| サービス階層 |   |   |   |
| サブスクリプション ID | lineItem/UsageAccountId | 統合アカウントおよび管理グループ |   |
| サブスクリプション名 | 該当なし | 統合アカウントおよび管理グループ | アカウント名は、AWS 組織 API を使用して収集されます。 |
| タグ | resourceTags/\* | All | _user:_ プレフィックスは、クラウドの間のタグを許可するユーザー定義タグから削除されます。 _aws:_ プレフィックスはそのまま残されます。 |
| 請求先アカウント ID | bill/PayerAccountId | 管理グループ |   |
| 請求先アカウント名 | 該当なし | 管理グループ | アカウント名は、AWS 組織 API を使用して収集されます。 |
| プロバイダー | 該当なし | 管理グループ | AWS または Azure のいずれか。 |

## <a name="set-budgets-on-aws-scopes"></a>AWS のスコープで予算を設定

予算を使用して、組織において積極的にコストを管理し、説明責任を推進します。 予算は、AWS 統合アカウントとAWS のリンクされたアカウントのスコープに対して設定されます。 Cost Management で示される、AWS 統合アカウントの予算の例を次に示します。

![AWS 統合アカウントの予算を示す例](./media/aws-integration-manage/budgets-aws-consolidated-account01.png)

## <a name="aws-data-collection-process"></a>AWS データ コレクション処理

AWS コネクタの設定後、データ収集と探索のプロセスを開始します。 すべての使用状況データを収集するには、いくらかの時間がかかる場合があります。 期間は以下に応じて異なります。

- AWS の S3 バケット内にある CUR ファイルを処理するために必要な時間。
- AWS 統合アカウントとAWS のリンクされたアカウントのスコープを作成するために必要な時間。
- S3 バケットのコストと使用状況レポートのファイルに AWS が書き込む時間と頻度

## <a name="aws-integration-pricing"></a>AWS の統合の価格

AWS の各コネクタには、90 日間の無料試用期間があります。 パブリック プレビューの期間中は無料です。

定額は、AWS の月額の 1% です。 各月の課金は、前の月の請求コストに基づいています。

AWS API にアクセスすると、追加のコストが発生する場合があります。

## <a name="aws-integration-limitations"></a>AWS 統合の制限事項

- Cost Management では、複数の種類の通貨が含まれるコスト レポートはサポートしていません。 複数の通貨を持つスコープを選択した場合には、エラー メッセージが表示されます。
- クラウド コネクタは、AWS GovCloud (US)、AWS Gov、または AWS China はサポートしていません。
- Cost Management は、AWS _使用状況コスト_のみを示します。 税、サポート、返金、RI、クレジット、その他の料金タイプはまだサポートされていません。

## <a name="troubleshooting-aws-integration"></a>AWS 統合のトラブルシューティング

一般的な問題を解決するには、次のトラブルシューティング情報を使用します。

### <a name="no-permission-to-aws-linked-accounts"></a>AWS のリンクされたアカウントへのアクセス許可がない

**エラー コード:** _Unauthorized_

AWS のリンクされたアカウントのコストへのアクセス許可を取得するには、次の 2 つの方法があります。

- AWS のリンクされたアカウントのある管理グループへのアクセスを取得する。
- AWS のリンクされたアカウントへのアクセス権を誰かに付与してもらう。

既定では、AWS コネクタの作成者は、コネクタが作成したすべてのオブジェクトの所有者です。 AWS 統合アカウントと AWS のリンクされたアカウントが含まれます。

コネクタの設定を確認するには、少なくとも共同作成者ロールが必要であり、閲覧者がコネクタの設定を確認することはできません。

### <a name="collection-failed-with-assumerole"></a>AssumeRole での収集の失敗

**エラー コード:** _FailedToAssumeRole_

このエラーは、Cost Management が AWS AssumeRole API を呼び出すことができないことを意味します。 この問題は、ロールの定義の問題が原因で発生する可能性があります。 次の条件が該当することを確認します。

- 外部 ID は、ロールの定義とコネクタの定義のものと同じです。
- ロールの種類は、**ユーザーまたはサード パーティに属する別の AWS アカウントに設定されます。**
- **[MFA を要求]** 選択項目はクリアされます。
- AWS ロールの信頼される AWS アカウントは、_432263259397_ です。

### <a name="collection-failed-with-access-denied"></a>アクセス拒否で収集が失敗

- **エラー コード:** _AccessDeniedReportDefinitions_ 
- **エラー コード:** _AccessDeniedReportDefinitions_ 
- **エラー コード:** _AccessDeniedDownloadReport_ 

このエラー メッセージは、Cost Management で Amazon S3 バケットに格納されている CUR ファイルにアクセスできないことを意味します。 ロールに関連付けられている AWS JSON ポリシーが、「[AWS でのロールとポリシーの作成](aws-integration-set-up-configure.md#create-a-role-and-policy-in-aws)」セクションの下部に示されている例と似たものであることを確認してください。

### <a name="collection-failed-since-we-did-not-find-the-cost-and-usage-report"></a>コストと使用状況レポートが見つからないための収集の失敗

**エラー コード:** _FailedToFindReport_

このエラーは、Cost Management がコネクタで定義されているコストと使用状況レポートを見つけられないことを意味します。 それが削除されておらず、ロールに関連付けられている AWS JSON ポリシーが、「[AWS でのロールとポリシーの作成](aws-integration-set-up-configure.md#create-a-role-and-policy-in-aws)」セクションの下部に示されている例と似たものであることを確認してください。

### <a name="unable-to-create-or-verify-connector-due-to-cost-and-usage-report-definitions-mismatch"></a>コストと使用状況レポートの定義が一致しないため、コネクタを作成または確認できない

**エラー コード:** _ReportIsNotValid_

このエラーは、AWS のコストと使用状況レポートの定義に関連しています。このレポートには固有の設定が必要です。[AWS でのコストと使用状況レポートの作成](aws-integration-set-up-configure.md#create-a-cost-and-usage-report-in-aws)に記載されている要件を参照してください。

## <a name="next-steps"></a>次の手順

- Azure 環境を管理グループでまだ構成していない場合は、「[管理グループの初期セットアップ](../governance/management-groups/index.md#initial-setup-of-management-groups)」を参照してください。
