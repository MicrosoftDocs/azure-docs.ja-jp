---
title: Azure Consumption API の概要 | Microsoft Docs
description: Azure Consumption API シリーズを使用して Azure リソースのコストおよび使用状況データにプログラムでアクセスする方法について学習します。
services: billing
documentationcenter: ''
author: bandersmsft
manager: dougeby
editor: ''
tags: billing
ms.assetid: 68825e85-de64-466d-b11a-8baffde836b5
ms.service: billing
ms.devlang: na
ms.topic: reference
ms.tgt_pltfrm: na
ms.workload: billing
ms.date: 6/07/2018
ms.author: banders
ms.openlocfilehash: 2a2922daef5e75af41252119f9a467fae976bcda
ms.sourcegitcommit: a874064e903f845d755abffdb5eac4868b390de7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/24/2019
ms.locfileid: "68443267"
---
# <a name="azure-consumption-api-overview"></a>Azure Consumption API の概要 

Azure Consumption API シリーズを使用すると、Azure リソースのコストおよび使用状況データにプログラムでアクセスできます。 これらの API は、現在、Enterprise 加入契約と Web Direct サブスクリプションのみをサポートしています (いくつかの例外があります)。 API は、他の種類の Azure サブスクリプションをサポートするために継続的に更新されます。

Azure Consumption API シリーズでは、以下にアクセスできます。
- Enterprise および Web Direct のお客様 
    - Usage Details 
    - Marketplace Charges 
    - Reservation Recommendations 
    - Reservation Details 
    - Reservation Summaries 
- Enterprise のお客様のみ 
    - Price Sheet 
    - Budgets 
    - Balances 

## <a name="usage-details-api"></a>Usage Details API

Usage Details API を使用して、すべての Azure ファースト パーティ リソースの料金および使用状況データを取得します。 情報は、現在 1 日あたりリソースあたり測定あたり 1 回発行される使用状況詳細レコード形式で提供されます。 この情報を使用すると、すべてのリソースのコストを合算したり、特定のリソースのコスト/使用状況を調査したりすることができます。 

この API には次の要素が含まれています。

-   **測定レベルの消費データ** - 使用コスト、料金の元となる測定、および料金が関係する Azure リソースを含むデータを表示します。 すべての使用状況詳細レコードは、日単位のバケットにマップされます。
-   **Azure ロールベースのアクセス制御** - [Azure portal](https://portal.azure.com)、[Azure CLI](https://docs.microsoft.com/azure/role-based-access-control/role-assignments-cli)、または [Azure PowerShell コマンドレット](https://docs.microsoft.com/powershell/azure/overview)を使用して、アクセス ポリシーを構成し、サブスクリプションの使用状況データにアクセスできるユーザーやアプリケーションを指定できます。 呼び出し元は、認証に Azure Active Directory トークンを使用する必要があります。 また、呼び出し元が特定の Azure サブスクリプションの使用状況データにアクセスするには、請求閲覧者、閲覧者、所有者、共同作成者のいずれかのロールに呼び出し元を追加します。 
-   **フィルタリング** - 次のフィルターを使用して、API 結果セットをより小さな使用状況詳細レコードのセットにトリミングします。
    - 使用終了/使用開始
    - リソース グループ
    - リソース名
-   **データ集計** - OData を使用して式を適用して、タグまたはフィルター プロパティで使用状況詳細を集計します
-   **別の種類のオファーの使用状況** - 使用状況詳細情報は、現在、Enterprise および Web Direct のお客様が利用できます。

詳細については、[Usage Details API](https://docs.microsoft.com/rest/api/consumption/usagedetails) の技術仕様を参照してください。

## <a name="marketplace-charges-api"></a>Marketplace Charges API

Marketplace Charges API を使用して、Marketplace のすべてのリソース (Azure サード パーティ製ソリューション) に関する料金および使用状況データを取得します。 このデータを使用すると、すべての Marketplace リソースのコストを合算したり、特定のリソースのコスト/使用状況を調査したりすることができます。 

この API には次の要素が含まれています。

-   **測定レベルの消費データ** - マーケットプレースの使用コスト、料金の元となる測定、および料金が関係するリソースを含むデータを表示します。 すべての使用状況詳細レコードは、日単位のバケットにマップされます。
-   **Azure ロールベースのアクセス制御** - [Azure portal](https://portal.azure.com)、[Azure CLI](https://docs.microsoft.com/azure/role-based-access-control/role-assignments-cli)、または [Azure PowerShell コマンドレット](https://docs.microsoft.com/powershell/azure/overview)を使用して、アクセス ポリシーを構成し、サブスクリプションの使用状況データにアクセスできるユーザーやアプリケーションを指定できます。 呼び出し元は、認証に Azure Active Directory トークンを使用する必要があります。 また、呼び出し元が特定の Azure サブスクリプションの使用状況データにアクセスするには、請求閲覧者、閲覧者、所有者、共同作成者のいずれかのロールに呼び出し元を追加します。 
-   **フィルタリング** - 次のフィルターを使用して、API 結果セットをより小さなマーケットプレース レコードのセットにトリミングします。
    - 使用開始/使用終了
    - リソース グループ
    - リソース名
-   **別の種類のオファーの使用状況** - Marketplace 情報は、現在、Enterprise および Web Direct のお客様が利用できます。

詳細については、[Marketplace Charges API](https://docs.microsoft.com/rest/api/consumption/marketplaces) の技術仕様を参照してください。

## <a name="balances-api"></a>Balances API

Enterprise のお客様は、Balances API を使用して、残高、新規購入、Azure Marketplace サービス料金、調整、および超過料金に関する情報の月別サマリーを取得できます。 この情報は、現在の請求期間または過去の任意の期間について取得できます。 企業はこのデータを使用して、手動で計算された集計料金との比較を実行することができます。 この API では、リソースに固有の情報およびコストの集計ビューは提供されません。 

この API には次の要素が含まれています。

-   **Azure ロールベースのアクセス制御** - [Azure portal](https://portal.azure.com)、[Azure CLI](https://docs.microsoft.com/azure/role-based-access-control/role-assignments-cli)、または [Azure PowerShell コマンドレット](https://docs.microsoft.com/powershell/azure/overview)を使用して、アクセス ポリシーを構成し、サブスクリプションの使用状況データにアクセスできるユーザーやアプリケーションを指定できます。 呼び出し元は、認証に Azure Active Directory トークンを使用する必要があります。 また、呼び出し元が特定の Azure サブスクリプションの使用状況データにアクセスするには、請求閲覧者、閲覧者、所有者、共同作成者のいずれかのロールに呼び出し元を追加します。 
-   **Enterprise のお客様のみ** - この API は、EA のお客様のみが使用できます。 
    - この API を呼び出すには、エンタープライズ管理者のアクセス許可が必要です 

詳細については、[Balances API](https://docs.microsoft.com/rest/api/consumption/balances) の技術仕様を参照してください。

## <a name="budgets-api"></a>Budgets API

Enterprise のお客様は、この API を使用して、リソース、リソース グループ、または課金測定のコストまたは使用予算を作成できます。 この情報が決定された後は、ユーザーが定義した予算のしきい値を超えたときに通知するよう警告を構成できます。 

この API には次の要素が含まれています。

-   **Azure ロールベースのアクセス制御** - [Azure portal](https://portal.azure.com)、[Azure CLI](https://docs.microsoft.com/azure/role-based-access-control/role-assignments-cli)、または [Azure PowerShell コマンドレット](https://docs.microsoft.com/powershell/azure/overview)を使用して、アクセス ポリシーを構成し、サブスクリプションの使用状況データにアクセスできるユーザーやアプリケーションを指定できます。 呼び出し元は、認証に Azure Active Directory トークンを使用する必要があります。 また、呼び出し元が特定の Azure サブスクリプションの使用状況データにアクセスするには、請求閲覧者、閲覧者、所有者、共同作成者のいずれかのロールに呼び出し元を追加します。 
-   **Enterprise のお客様のみ** - この API は、EA のお客様のみが使用できます。
-   **構成可能な通知** - 予算に達したときに通知されるユーザーを指定します。
-   **使用状況またはコストベースの予算** - シナリオでの必要性に応じて消費またはコストのいずれかに基づいて予算を作成します。
-   **フィルタリング** - 次の構成可能なフィルターを使用して、予算をより小さなリソースのサブセットにフィルター処理します
    - リソース グループ
    - リソース名
    - 測定
-   **構成可能な予算期間** - 予算をリセットする頻度と予算の有効期間を指定します。

詳細については、[Budgets API](https://docs.microsoft.com/rest/api/consumption/budgets) の技術仕様を参照してください。

## <a name="reservation-recommendations-api"></a>Reservation Recommendations API

この API を使用して、予約 VM インスタンスの購入に関する推奨事項を取得します。 推奨事項は、お客様が予想されるコスト削減と購入額を分析できるように設計されています。 

この API には次の要素が含まれています。

-   **Azure ロールベースのアクセス制御** - [Azure portal](https://portal.azure.com)、[Azure CLI](https://docs.microsoft.com/azure/role-based-access-control/role-assignments-cli)、または [Azure PowerShell コマンドレット](https://docs.microsoft.com/powershell/azure/overview)を使用して、アクセス ポリシーを構成し、サブスクリプションの使用状況データにアクセスできるユーザーやアプリケーションを指定できます。 呼び出し元は、認証に Azure Active Directory トークンを使用する必要があります。 また、呼び出し元が特定の Azure サブスクリプションの使用状況データにアクセスするには、請求閲覧者、閲覧者、所有者、共同作成者のいずれかのロールに呼び出し元を追加します。 
-   **フィルタリング** - 次のフィルターを使用して、推奨の結果を調整します。
    - Scope (スコープ)
    - ルックバック期間
-   **別の種類のオファーの予約情報** - 予約情報は、現在、Enterprise および Web Direct のお客様が利用できます。

詳細については、[Reservation Recommendations API](https://docs.microsoft.com/rest/api/consumption/reservationrecommendations) の技術仕様を参照してください。

## <a name="reservation-details-api"></a>Reservation Details API

Reservation Details API を使用して、予約された消費と実際に使用された量の比較など、以前に購入した VM 予約に関する情報を表示します。 データは、VM 単位の詳細度で表示することができます。 

この API には次の要素が含まれています。

-   **Azure ロールベースのアクセス制御** - [Azure portal](https://portal.azure.com)、[Azure CLI](https://docs.microsoft.com/azure/role-based-access-control/role-assignments-cli)、または [Azure PowerShell コマンドレット](https://docs.microsoft.com/powershell/azure/overview)を使用して、アクセス ポリシーを構成し、サブスクリプションの使用状況データにアクセスできるユーザーやアプリケーションを指定できます。 呼び出し元は、認証に Azure Active Directory トークンを使用する必要があります。 また、呼び出し元が特定の Azure サブスクリプションの使用状況データにアクセスするには、請求閲覧者、閲覧者、所有者、共同作成者のいずれかのロールに呼び出し元を追加します。 
-   **フィルタリング** - 次のフィルターを使用して、API 結果セットをより小さな予約のセットにトリミングします。
    - 期間
-   **別の種類のオファーの予約情報** - 予約情報は、現在、Enterprise および Web Direct のお客様が利用できます。

詳細については、[Reservation Details API](https://docs.microsoft.com/rest/api/consumption/reservationsdetails) の技術仕様を参照してください。

## <a name="reservation-summaries-api"></a>Reservation Summaries API

この API を使用して、予約された消費と実際に使用された合計の量の比較など、以前に購入した VM 予約に関する集計情報を表示します。 

この API には次の要素が含まれています。

-   **Azure ロールベースのアクセス制御** - [Azure portal](https://portal.azure.com)、[Azure CLI](https://docs.microsoft.com/azure/role-based-access-control/role-assignments-cli)、または [Azure PowerShell コマンドレット](https://docs.microsoft.com/powershell/azure/overview)を使用して、アクセス ポリシーを構成し、サブスクリプションの使用状況データにアクセスできるユーザーやアプリケーションを指定できます。 呼び出し元は、認証に Azure Active Directory トークンを使用する必要があります。 また、呼び出し元が特定の Azure サブスクリプションの使用状況データにアクセスするには、請求閲覧者、閲覧者、所有者、共同作成者のいずれかのロールに呼び出し元を追加します。 
-   **フィルタリング** - 次のフィルターを使用して、日単位の粒度を使用する場合の結果を調整します。
    - 使用日
-   **別の種類のオファーの予約情報** - 予約情報は、現在、Enterprise および Web Direct のお客様が利用できます。
-   **日単位または月単位の集計** - 呼び出し元は、予約概要データの粒度として日単位または月単位を指定することができます。

詳細については、[Reservation Summaries API](https://docs.microsoft.com/rest/api/consumption/reservationssummaries) の技術仕様を参照してください。

## <a name="price-sheet-api"></a>Price Sheet API
Enterprise のお客様は、この API を使用して、すべての測定のカスタム価格を取得できます。 企業は、これを使用状況の詳細とマーケットプレースの使用状況情報と共に使用して、使用状況およびマーケットプレース データを使用してコストの計算を実行することができます。 

この API には次の要素が含まれています。

-   **Azure ロールベースのアクセス制御** - [Azure portal](https://portal.azure.com)、[Azure CLI](https://docs.microsoft.com/azure/role-based-access-control/role-assignments-cli)、または [Azure PowerShell コマンドレット](https://docs.microsoft.com/powershell/azure/overview)を使用して、アクセス ポリシーを構成し、サブスクリプションの使用状況データにアクセスできるユーザーやアプリケーションを指定できます。 呼び出し元は、認証に Azure Active Directory トークンを使用する必要があります。 また、呼び出し元が特定の Azure サブスクリプションの使用状況データにアクセスするには、請求閲覧者、閲覧者、所有者、共同作成者のいずれかのロールに呼び出し元を追加します。 
-   **Enterprise のお客様のみ** - この API は、EA のお客様のみが使用できます。 Web Direct のお客様は、RateCard API を使用して価格を取得する必要があります。 

詳細については、[Price Sheet API](https://docs.microsoft.com/rest/api/consumption/pricesheet) の技術仕様を参照してください。

## <a name="scenarios"></a>シナリオ

Consumption API シリーズを使用して可能になるシナリオのいくつかを次に示します。

-   **請求書の調整** - Microsoft からの請求金額が正しいか。  請求書の内容はどうなっているか。自分で料金を計算できるか。
-   **クロス請求** - 請求金額がわかったところで、組織の誰がその料金を支払う必要があるか。
-   **コストの最適化** - 請求金額がわかったところで、 Azure に関する支出からどのように利益を得ることができるか。
-   **コスト管理** - Azure に関する支出と使用状況を時間の経過に沿って確認したい。 どのような傾向があるか。 どのようにすればよりうまくできるか。
-   **Azure の月間の支出** - 当月の今日までの支出はいくらか。 Azure に関する支出と使用状況に何か調整を加える必要があるか。 Azure を最も消費しているのは月内のいつか。
-   **警告の設定** - 予算に基づいてリソースベースの消費または通貨ベースの警告を設定したい。

## <a name="next-steps"></a>次の手順

- Azure Billing API シリーズを使用してプログラムで Azure の使用状況に関する分析情報を取得する方法については、[Azure Billing API シリーズの概要](billing-usage-rate-card-overview.md)に関するページを参照してください。



