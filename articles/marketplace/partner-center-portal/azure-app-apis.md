---
title: Microsoft コマーシャル マーケットプレースで Azure アプリをオンボードするためのパートナー センター申請 API
description: Microsoft パートナー センターのコマーシャル マーケットプレースで Azure アプリ用のパートナー センター申請 API を使用するための前提条件について説明します。
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 12/10/2019
ms.author: mingshen
author: mingshen-ms
ms.openlocfilehash: 9863ed24da9e427f885a4794bda7e103b0c1cc8e
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "96455449"
---
# <a name="partner-center-submission-api-to-onboard-azure-apps-in-partner-center"></a>パートナー センターで Azure アプリをオンボードするためのパートナー センター申請 API

*パートナー センター申請 API* を使用して、プログラムにより Azure プランをクエリし、申請を作成し、公開します。  この API は、お使いのアカウントで多くのプランを管理していて、これらのプランの申請プロセスを自動化および最適化したい場合に役立ちます。

## <a name="api-prerequisites"></a>API 前提条件

Azure 製品向けのパートナー センター API を使用するには、以下のいくつかのプログラム資産が必要です。 

- Azure Active Directory アプリケーション。
- Azure Active Directory (Azure AD) アクセス トークン。

### <a name="step-1-complete-prerequisites-for-using-the-partner-center-submission-api"></a>手順 1:パートナー センター申請 API を使用するための前提条件を満たす

パートナー センター申請 API を呼び出すコードを書き始める前に、次の前提条件が満たされていることを確認してください。

- 自分 (または自分の組織) に Azure AD ディレクトリがあり、自分がそのディレクトリに対する[グローバル管理者](../../active-directory/roles/permissions-reference.md)のアクセス許可を持っている必要があります。 Microsoft 365 または Microsoft の他のビジネス サービスをすでに使用している場合、Azure AD ディレクトリをすでに所有しています。 そうでなければ、[パートナー センターで新しい Azure AD を無料で作成](/windows/uwp/publish/associate-azure-ad-with-partner-center#create-a-brand-new-azure-ad-to-associate-with-your-partner-center-account)できます。

- [Azure AD アプリケーションをパートナー センター アカウントと関連付け](/windows/uwp/monetize/create-and-manage-submissions-using-windows-store-services#associate-an-azure-ad-application-with-your-windows-partner-center-account)、テナント ID、クライアント ID、キーを取得する必要があります。 Azure AD アクセス トークンを取得するにはこれらの値が必要です。Microsoft Store 申請 API への呼び出しにこれらを使用します。

#### <a name="how-to-associate-an-azure-ad-application-with-your-partner-center-account"></a>Azure AD アプリケーションをパートナー センター アカウントと関連付ける方法

Microsoft Store 申請 API を使用するには、Azure AD アプリケーションをパートナー センター アカウントと関連付け、アプリケーションのテナント ID とクライアント ID を取得し、キーを生成する必要があります。 Azure AD アプリケーションは、パートナー センター申請 API の呼び出し元であるアプリまたはサービスを表します。 API に渡す Azure AD アクセス トークンを取得するには、テナント ID、クライアント ID、キーが必要です。

>[!Note]
>このタスクは 1 度だけ実行する必要があります。 テナント ID、クライアント ID、キーがあれば、新しい Azure AD アクセス トークンを作成する必要がある度にそれらを再利用できます。

1. パートナー センターで、[組織のパートナー センター アカウントを組織の Azure AD ディレクトリに関連付けます](/windows/uwp/publish/associate-azure-ad-with-partner-center)。
1. 次に、パートナー センターの **[アカウント設定]** セクションの **[ユーザー]** ページから、パートナー センター アカウントの申請にアクセスするために使用するアプリまたはサービスを表す [Azure AD アプリケーションを追加](/windows/uwp/publish/add-users-groups-and-azure-ad-applications#add-azure-ad-applications-to-your-partner-center-account)します。 このアプリケーションに **マネージャー** ロールを確実に割り当てます。 アプリケーションがまだ Azure AD ディレクトリに存在しない場合、[パートナー センターで新しい Azure AD アプリケーションを作成](/windows/uwp/publish/add-users-groups-and-azure-ad-applications#create-a-new-azure-ad-application-account-in-your-organizations-directory-and-add-it-to-your-partner-center-account)できます。
1. **[ユーザー]** ページに戻り、Azure AD アプリケーションの名前をクリックしてアプリケーション設定に移動し、**テナント ID** と **クライアント ID** の値を書き留めます。
1. **[新しいキーを追加]** をクリックします。 次の画面で、**キー** の値を書き留めます。 このページを離れると、この情報にアクセスすることはできなくなります。 詳細については、「[Azure AD アプリケーションのキーを管理する](/windows/uwp/publish/add-users-groups-and-azure-ad-applications#manage-keys)」を参照してください。

### <a name="step-2-obtain-an-azure-ad-access-token"></a>手順 2:Azure AD アクセス トークンを取得する

パートナー センター申請 API のメソッドを呼び出す前に、API の各メソッドの **Authorization** ヘッダーに渡す Azure AD アクセス トークンを取得する必要があります。 アクセス トークンを取得したら、期限が切れる 60 分が経過する前に使用します。 トークンの有効期限が切れたら、トークンを更新して、将来の API 呼び出しで使用できるようにします。

アクセス トークンを取得するには、「[クライアント資格情報を使用したサービス間の呼び出し](../../active-directory/azuread-dev/v1-oauth2-client-creds-grant-flow.md)」の指示に従って、`HTTP POST` を `https://login.microsoftonline.com/<tenant_id>/oauth2/token` エンドポイントに送信します。 要求のサンプルを次に示します。

JSONCopy
```Json
POST https://login.microsoftonline.com/<tenant_id>/oauth2/token HTTP/1.1
Host: login.microsoftonline.com
Content-Type: application/x-www-form-urlencoded; charset=utf-8

grant_type=client_credentials
&client_id=<your_client_id>
&client_secret=<your_client_secret>
&resource= https://api.partner.microsoft.com
```

`POST URI` の *tenant_id* 値と *client_id* および *client_secret* パラメーターには、前のセクションでパートナー センターから取得した、アプリケーションのテナント ID、クライアント ID、キーを指定します。 *resource* パラメーターには、`https://api.partner.microsoft.com` を指定する必要があります。

### <a name="step-3-use-the-microsoft-store-submission-api"></a>手順 3:Microsoft Store 申請 API を使用する

Azure AD アクセス トークンを取得したら、パートナー センター申請 API でメソッドを呼び出すことができます。 申請を作成または更新するには、通常はパートナー センター申請 API 内で複数のメソッドを特定の順序で呼び出します。 それぞれのシナリオと各メソッドの構文については、Ingestion API Swagger を参照してください。

https://apidocs.microsoft.com/services/partneringestion/

## <a name="next-steps"></a>次のステップ

* [Azure VM 技術資産を作成する](../create-azure-container-technical-assets.md)方法について学習します
* [Azure Container オファー](../create-azure-container-offer.md)の作成方法について学習します