---
title: Azure Time Series Insights の API を使用して認証と承認を行う | Microsoft Docs
description: この記事では、Azure Time Series Insights API を呼び出すカスタム アプリケーションの認証と承認を構成する方法を説明します。
ms.service: time-series-insights
services: time-series-insights
author: ashannon7
ms.author: dpalled
manager: cshankar
ms.reviewer: v-mamcge, jasonh, kfile
ms.devlang: csharp
ms.workload: big-data
ms.topic: conceptual
ms.date: 06/29/2019
ms.custom: seodec18
ms.openlocfilehash: 899bcffaf3a54bd541d488f99c35ec6721d751ca
ms.sourcegitcommit: 5bdd50e769a4d50ccb89e135cfd38b788ade594d
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/03/2019
ms.locfileid: "67543998"
---
# <a name="authentication-and-authorization-for-azure-time-series-insights-api"></a>Azure Time Series Insights API の認証と承認

このドキュメントでは、新しい Azure Active Directory ブレードを使用して Azure Active Directory にアプリを登録する方法について説明します。 Azure Active Directory に登録されたアプリを使用すると、ユーザーは、Time Series Insights 環境に関連付けられた Azure Time Series Insight API を使用する認証と承認を受けることができます。

## <a name="service-principal"></a>サービス プリンシパル

以下のセクションでは、アプリケーションを構成して、そのアプリの代わりに Time Series Insights API にアクセスする方法について説明します。 アプリケーションは、Azure Active Directory を介して独自のアプリケーション資格情報を使用し、Time Series Insights 環境で参照データをクエリまたは発行することができます。

## <a name="summary-and-best-practices"></a>概要とベスト プラクティス

Azure Active Directory のアプリ登録フローには、主に 3 つの手順があります。

1. Azure Active Directory に[アプリケーションを登録する](#azure-active-directory-app-registration)。
1. [Time Series Insights 環境へのデータ アクセス](#granting-data-access)をアプリケーションに承認します。
1. [クライアント アプリ](#client-app-initialization)の  **からトークンを取得するには、** [申請 ID] **と**[クライアント シークレット]`https://api.timeseries.azure.com/` を使用します。 トークンは、Time Series Insights API の呼び出しに使用できます。

**手順 3** に従い、アプリケーションとユーザーの資格情報を分離すると、次のことが可能になります。

* 独自のアクセス許可とは異なるアクセス許可を、アプリ ID に割り当てることができます。 通常、こうしたアクセス許可はアプリで必要な操作のみに制限されます。 たとえば、アプリが特定の Time Series Insights 環境でデータの読み取りだけを行うようにすることが可能です。
* **クライアント シークレット**またはセキュリティ証明書を使用して、作成しているユーザーの認証資格情報から、アプリのセキュリティを分離します。 その結果、アプリケーションの資格情報は特定のユーザーの資格情報に依存しません。 ユーザーのロールが変わっても、アプリケーションは必ずしも新しい資格情報や追加の構成を必要としません。 ユーザーが自分のパスワードを変更した場合、アプリケーションへのすべてのアクセスに新しい資格情報やキーは必要ありません。
* (存在する必要がある) 特定のユーザーの資格情報ではなく、**クライアント シークレット**またはセキュリティ証明書を使用して無人スクリプトを実行します。
* Azure Time Series Insights API へのアクセスをセキュリティで保護するには、パスワードではなくセキュリティ証明書を使用します。

> [!IMPORTANT]
> Azure Time Series Insights セキュリティ ポリシーを構成するときは、**懸念事項の分離** (このシナリオについては前述を参照) の原則に従います。

> [!NOTE]
> * この記事では、シングルテナント アプリケーション (1 つの組織内でのみ実行することを目的としたアプリケーション) に焦点を絞って説明します。
> * 通常は、組織内で実行される基幹業務アプリケーションには、シングルテナント アプリケーションが使用されます。

## <a name="detailed-setup"></a>詳細なセットアップ

### <a name="azure-active-directory-app-registration"></a>Azure Active Directory のアプリ登録

[!INCLUDE [Azure Active Directory app registration](../../includes/time-series-insights-aad-registration.md)]

### <a name="granting-data-access"></a>データ アクセスの許可

1. Time Series Insights 環境のための **[データ アクセス ポリシー]** を選択して、 **[追加]** を選択します。

   [![Time Series Insights 環境に新しいデータ アクセス ポリシーを追加する](media/authentication-and-authorization/time-series-insights-data-access-policies-add.png)](media/authentication-and-authorization/time-series-insights-data-access-policies-add.png#lightbox)

1. **[ユーザーの選択]** ダイアログ ボックスで、Azure Active Directory のアプリ登録セクションの **[アプリケーション名]** または **[申請 ID]** のいずれかを貼り付けます。

   [![[ユーザーの選択] ダイアログ ボックスでアプリケーションを検索する](media/authentication-and-authorization/time-series-insights-data-access-policies-select-user.png)](media/authentication-and-authorization/time-series-insights-data-access-policies-select-user.png#lightbox)

1. ロールを選択します。 データのクエリを実行するには **[閲覧者]** を選択し、データのクエリを実行して参照データを変更するには **[共同作成者]** を選択します。 **[OK]** を選択します。

   [![[ユーザー ロールの選択] ダイアログ ボックスで、[閲覧者] または [共同作成者] を選択する](media/authentication-and-authorization/time-series-insights-data-access-policies-select-role.png)](media/authentication-and-authorization/time-series-insights-data-access-policies-select-role.png#lightbox)

1. **[OK]** を選択してポリシーを保存します。

   > [!TIP]
   > Azure Active Directory の Time Series Insights 環境に対する[データ アクセスの許可](./time-series-insights-data-access.md)について参照します。

### <a name="client-app-initialization"></a>クライアント アプリの初期化

1. アプリケーションに代わってトークンを取得するには、Azure Active Directory のアプリ登録セクションの **[申請 ID]** と **[クライアント シークレット]** (アプリケーション キー) を使用します。

    C# では、次のコードでアプリケーションに代わってトークンを取得できます。 サンプルの詳細については、[C# を使用したデータの照会](time-series-insights-query-data-csharp.md)に関する記事をご覧ください。

    ```csharp
    // Enter your Active Directory tenant domain name
    var tenant = "YOUR_AD_TENANT.onmicrosoft.com";
    var authenticationContext = new AuthenticationContext(
        $"https://login.microsoftonline.com/{tenant}",
        TokenCache.DefaultShared);

    AuthenticationResult token = await authenticationContext.AcquireTokenAsync(
        // Set the resource URI to the Azure Time Series Insights API
        resource: "https://api.timeseries.azure.com/",
        clientCredential: new ClientCredential(
            // Application ID of application registered in Azure Active Directory
            clientId: "YOUR_APPLICATION_ID",
            // Application key of the application that's registered in Azure Active Directory
            clientSecret: "YOUR_CLIENT_APPLICATION_KEY"));

    string accessToken = token.AccessToken;
    ```

1. トークンは、アプリケーションが Time Series Insights API を呼び出すときに、`Authorization` ヘッダーで渡すことができます。

## <a name="next-steps"></a>次の手順

- GA Time Series Insights API を呼び出すサンプル コードについては、[C# を使用したデータの照会](./time-series-insights-query-data-csharp.md)に関する記事を参照してください。

- プレビュー Time Series Insights API コード サンプルについては、[C# を使用したプレビュー データの照会](./time-series-insights-update-query-data-csharp.md)に関する記事を参照してください。

- API リファレンスについては、[API リファレンスのクエリ](/rest/api/time-series-insights/ga-query-api)に関するページをご覧ください。

- [サービス プリンシパルを作成する](../active-directory/develop/howto-create-service-principal-portal.md)方法について学習します。