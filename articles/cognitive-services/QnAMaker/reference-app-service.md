---
title: サービス構成 - QnA Maker
description: リソースを構成する方法と場所について理解します。
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: reference
ms.date: 11/9/2020
ms.openlocfilehash: da46084c8c2616284c31ef155927e8dbcbd19e0e
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/30/2021
ms.locfileid: "102209361"
---
# <a name="service-configuration"></a>サービス構成

QnA Maker の各バージョンには、異なる Azure リソース (サービス) のセットが使用されます。 この記事では、これらのサービスでサポートされているカスタマイズについて説明します。 

## <a name="app-service"></a>App Service

# <a name="qna-maker-ga-stable-release"></a>[QnA Maker GA (安定版リリース)](#tab/v1)

QnA Maker は、App Service を使用して、[generateAnswer API](/rest/api/cognitiveservices/qnamaker4.0/runtime/generateanswer) が使用するクエリ ランタイムを提供します。

これらの設定は、App Service 用に Azure portal に用意されています。 この設定は、 **[設定]** 、 **[構成]** を選択して使用できます。

個々に設定する場合は [アプリケーションの設定] の一覧から、いくつかの設定を変更する場合は **[高度な編集]** から選択して変更することができます。

|リソース|設定|
|--|--|
|AzureSearchAdminKey|Cognitive Search - QnA ペア ストレージおよび 1 位のランカーで使用されます|
|AzureSearchName|Cognitive Search - QnA ペア ストレージおよび 1 位のランカーで使用されます|
|DefaultAnswer|一致が見つからない場合の回答のテキスト|
|UserAppInsightsAppId|チャット ログとテレメトリ|
|UserAppInsightsKey|チャット ログとテレメトリ|
|UserAppInsightsName|チャット ログとテレメトリ|
|QNAMAKER_EXTENSION_VERSION|常に _latest_ に設定します。 この設定により、App Service の QnAMaker サイト拡張機能が初期化されます。|

変更を完了したら、Azure portal の **[概要]** ページからサービスを **再起動** する必要があります。

# <a name="qna-maker-managed-preview-release"></a>[QnA Maker マネージド (プレビュー リリース)](#tab/v2)

App Service のカスタマイズは、QnA Maker マネージド (プレビュー) には適用されません。

---

## <a name="qna-maker-service"></a>QnA Maker Service

QnA Maker サービスには、次のユーザーが 1 つの QnA Maker サービスとそのすべてのナレッジベースで共同作業するための構成があります。

お使いのサービスに[コラボレーターを追加する方法](./reference-role-based-access-control.md)を確認してください。

## <a name="change-azure-cognitive-search"></a>Azure Cognitive Search を変更する

QnA Maker サービスにリンクされている [Cognitive Search サービスを変更する方法](./how-to/configure-QnA-Maker-resources.md#configure-qna-maker-to-use-different-cognitive-search-resource)を参照してください。

## <a name="change-default-answer"></a>既定の回答の変更

[既定の回答のテキストを変更する方法](How-To/change-default-answer.md)を参照してください。 

## <a name="telemetry"></a>製品利用統計情報

# <a name="qna-maker-ga-stable-release"></a>[QnA Maker GA (安定版リリース)](#tab/v1)

Application Insights は、QnA Maker GA でテレメトリを監視するために使用されます。 QnA Maker に固有の構成設定はありません。

# <a name="qna-maker-managed-preview-release"></a>[QnA Maker マネージド (プレビュー リリース)](#tab/v2)

[QnA Maker マネージド (プレビュー) サービスにテレメトリを追加する方法](How-To/get-analytics-knowledge-base.md)を参照してください。 

---

## <a name="app-service-plan"></a>App Service プラン

# <a name="qnamaker-ga-stable-release"></a>[QnAMaker GA (安定版リリース)](#tab/v1)

App Service プランには、QnA Maker に固有の構成設定はありません。

# <a name="qnamaker-managed-preview-release"></a>[QnAMaker マネージド (プレビュー リリース)](#tab/v2)

App Service プランは、QnA Maker マネージド (プレビュー) では使用されません。

---

## <a name="next-steps"></a>次のステップ

ナレッジ ベースにインポートするドキュメントおよび URL の[形式](reference-document-format-guidelines.md)を確認してください。
