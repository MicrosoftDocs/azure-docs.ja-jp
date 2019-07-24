---
title: Azure Time Series Insights の API によって認証と承認を行う方法 | Microsoft Docs
description: この記事では、Azure Time Series Insights API を呼び出すカスタム アプリケーションの認証と承認を構成する方法を説明します。
ms.service: time-series-insights
services: time-series-insights
author: ashannon7
ms.author: anshan
manager: cshankar
ms.reviewer: v-mamcge, jasonh, kfile, anshan
ms.devlang: csharp
ms.workload: big-data
ms.topic: conceptual
ms.date: 11/27/2017
ms.custom: seodec18
ms.openlocfilehash: b9f3561155038e91bc278f5d7ddc995adb53ad78
ms.sourcegitcommit: d4f728095cf52b109b3117be9059809c12b69e32
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 01/10/2019
ms.locfileid: "54197999"
---
# <a name="authentication-and-authorization-for-azure-time-series-insights-api"></a>Azure Time Series Insights API の認証と承認

この記事では、Azure Time Series Insights API を呼び出すカスタム アプリケーションで使用される認証と承認を構成する方法を説明します。

## <a name="service-principal"></a>サービス プリンシパル

このセクションでは、アプリケーションを構成して、そのアプリケーションのために Time Series Insights API にアクセスする方法について説明します。 このアプリケーションでは、ユーザー資格情報ではなくアプリケーションの資格情報を使って、Time Series Insights 環境にあるデータにクエリを実行したり参照データを公開したりできます。

Time Series Insights へのアクセスが必要なアプリケーションがある場合、Azure Active Directory アプリケーションを設定し、Time Series Insights 環境でデータ アクセス ポリシーを割り当てる必要があります。 この方法は、お客様自身の資格情報でアプリを実行するよりも推奨されます。

* お客様自身のアクセス許可とは異なるアクセス許可を、アプリ ID に割り当てることができます。 通常、こうしたアクセス許可はアプリで必要な操作のみに制限されます。 たとえば、アプリが特定の Time Series Insights 環境でデータの読み取りだけを行うようにすることが可能です。
* お客様の責任が変わっても、アプリの資格情報を変更する必要はありません。
* 無人インストール用スクリプトを実行するときに、証明書またはアプリケーション キーを使用して認証を自動化できます。

この記事では、Azure Portal でそれらの手順を実行する方法を示します。 ここでは、シングル テナント アプリケーション (1 つの組織内でのみ実行することを目的としたアプリケーション) に焦点を絞って説明します。 一般に、組織内で実行される基幹業務アプリケーションには、シングル テナント アプリケーションが使用されます。

セットアップ フローは、次の 3 つの主要な手順で構成されます。

1. Azure Active Directory にアプリケーションを作成する。
2. このアプリケーションを承認して Time Series Insights 環境にアクセスする。
3. アプリケーション ID とキーを使って、`"https://api.timeseries.azure.com/"` の対象ユーザーまたはリソースへのトークンを取得する。 トークンは、Time Series Insights API の呼び出しに使用できます。

詳細な手順を以下に示します。

1. Azure Portal で、**[Azure Active Directory]** > **[アプリの登録]** > **[新しいアプリケーションの登録]** の順に選びます。

   ![Azure Active Directory での新しいアプリケーションの登録](media/authentication-and-authorization/active-directory-new-application-registration.png)  

2. アプリケーションに名前を付けて、種類として **[Web アプリ/API]** を選択し、**[サインオン URL]** に任意の有効な URI を選択して、**[作成]** をクリックします。

   ![Azure Active Directory にアプリケーションを作成する](media/authentication-and-authorization/active-directory-create-web-api-application.png)

3. 新しく作成されたアプリケーションを選択し、アプリケーション ID を普段使用しているテキスト エディターにコピーします。

   ![アプリケーション ID をコピーする](media/authentication-and-authorization/active-directory-copy-application-id.png)

4. **[キー]** を選択してキー名を入力し、有効期限を選択して、**[保存]** をクリックします。

   ![アプリケーション キーを選択する](media/authentication-and-authorization/active-directory-application-keys.png)

   ![キー名と有効期限を入力し、[保存] をクリックする](media/authentication-and-authorization/active-directory-application-keys-save.png)

5. 普段使用しているテキスト エディターにキーをコピーします。

   ![アプリケーション キーをコピーする](media/authentication-and-authorization/active-directory-copy-application-key.png)

6. Time Series Insights 環境のための **[データ アクセス ポリシー]** を選択して、**[追加]** をクリックします。

   ![Time Series Insights 環境に新しいデータ アクセス ポリシーを追加する](media/authentication-and-authorization/time-series-insights-data-access-policies-add.png)

7. **[ユーザーの選択]** ダイアログ ボックスに、アプリケーション名 (手順 2 より) またはアプリケーション ID (手順 3 より) を貼り付けます。

   ![[ユーザーの選択] ダイアログ ボックスでアプリケーションを検索する](media/authentication-and-authorization/time-series-insights-data-access-policies-select-user.png)

8. 役割 (データを照会する場合は **[閲覧者]**、データを照会して参照データを変更する場合は **[共同作成者]**) を選択して、**[OK]** をクリックします。

   ![[役割の選択] ダイアログ ボックスで、[閲覧者] または [共同作成者] を選択する](media/authentication-and-authorization/time-series-insights-data-access-policies-select-role.png)

9. **[OK]** をクリックしてポリシーを保存します。

10. アプリケーション ID (手順 3 より) とアプリケーション キー (手順 5 より) を使用して、アプリケーションのためのトークンを取得します。 トークンは、アプリケーションが Time Series Insights API を呼び出すときに、`Authorization` ヘッダーで渡すことができます。

    C# を使用している場合は、次のコードを使って、アプリケーションのためのトークンを取得できます。 サンプルの詳細については、[C# を使用したデータの照会](time-series-insights-query-data-csharp.md)に関する記事をご覧ください。

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
            clientId: "1bc3af48-7e2f-4845-880a-c7649a6470b8", 
            // Application key of the application that's registered in Azure Active Directory
            clientSecret: "aBcdEffs4XYxoAXzLB1n3R2meNCYdGpIGBc2YC5D6L2="));

    string accessToken = token.AccessToken;
    ```

お使いのアプリケーションのアプリケーション ID とキーを使用し、Azure Time Series Insight で認証します。 

## <a name="next-steps"></a>次の手順
- Time Series Insights API を呼び出すサンプル コードについては、[C# を使用したデータの照会](time-series-insights-query-data-csharp.md)に関する記事をご覧ください。
- API リファレンスについては、[API リファレンスのクエリ](/rest/api/time-series-insights/ga-query-api)に関するページをご覧ください。

> [!div class="nextstepaction"]
> [サービス プリンシパルの作成](../active-directory/develop/howto-create-service-principal-portal.md)
