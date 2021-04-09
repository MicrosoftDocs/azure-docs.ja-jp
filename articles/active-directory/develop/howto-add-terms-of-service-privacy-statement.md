---
title: アプリのサービス利用規約とプライバシーに関する声明 | Azure
description: Azure AD を使用するために登録されているアプリに対してサービス利用規約とプライバシーに関する声明を設定する方法について説明します。
services: active-directory
author: rwike77
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: how-to
ms.workload: identity
ms.date: 05/22/2019
ms.author: ryanwi
ms.reviewer: lenalepa, sureshja
ms.custom: aaddev
ms.openlocfilehash: 311dfd976610c392909a0ec3d91fecaa4d733539
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "100104197"
---
# <a name="how-to-configure-terms-of-service-and-privacy-statement-for-an-app"></a>方法:アプリのサービス利用規約とプライバシーに関する声明を構成する

Azure Active Directory (Azure AD) アカウントおよび Microsoft アカウントと統合されているアプリをビルドして管理する開発者は、アプリのサービス利用規約とプライバシーに関する声明へのリンクを含める必要があります。 サービス利用規約とプライバシーに関する声明は、ユーザーの同意エクスペリエンスからユーザーに提示されます。 これは、ユーザーがアプリを信頼できることを知るのに役立ちます。 サービス利用規約とプライバシーに関する声明は、ユーザー向けマルチテナント アプリに特に重要です。アプリは複数のディレクトリによって使用され、すべての Microsoft アカウントで利用できます。

お客様は自分のアプリのサービス利用規約とプライバシーに関する声明ドキュメントを作成し、これらのドキュメントへの URL を提供する責任があります。 これらのリンクを提供できないマルチテナント アプリの場合、アプリに対するユーザーの同意エクスペリエンスで、ユーザーがアプリに同意することを防ぐためのアラートが表示されます。

> [!NOTE]
> * シングルテナント アプリでは、アラートは表示されません。
> * この 2 つのリンクの一方または両方が存在しない場合は、アプリにアラートが表示されます。

## <a name="user-consent-experience"></a>ユーザーの同意エクスペリエンス

次の例では、サービス利用規約とプライバシーに関する声明を設定し、これらのリンクを設定していないときに、ユーザーの同意エクスペリエンスが表示されます。

![提供されたプライバシーに関する声明とサービス利用規約を含むスクリーンショット、および含まないスクリーンショット](./media/howto-add-terms-of-service-privacy-statement/user-consent-exp-privacy-statement-terms-service.png)

## <a name="formatting-links-to-the-terms-of-service-and-privacy-statement-documents"></a>サービス利用規約とプライバシーに関する声明のドキュメントへのリンクを書式設定する

自分のアプリのサービス利用規約とプライバシーに関する声明のドキュメントへのリンクを追加する前に、URL が以下のガイドラインに従っていることを確認します。

| ガイドライン     | 説明                           |
|---------------|---------------------------------------|
| Format        | 有効な URL                             |
| 有効なスキーマ | HTTP および HTTPS<br/>HTTPS を推奨 |
| 最大長    | 2048 文字                       |

例: `https://myapp.com/terms-of-service`、`https://myapp.com/privacy-statement`

## <a name="adding-links-to-the-terms-of-service-and-privacy-statement"></a>サービス利用規約とプライバシーに関する声明へのリンクを追加する

サービス利用規約とプライバシーに関する声明の準備ができたら、次のメソッドのいずれかを使用して、自分のアプリにこれらのドキュメントへのリンクを追加できます。

* [Azure portal を使用する](#azure-portal)
* [アプリ オブジェクト JSON を使用する](#app-object-json)
* [Microsoft Graph API を使用する](#msgraph-rest-api)

### <a name="using-the-azure-portal"></a><a name="azure-portal"></a>Azure portal を使用する
Azure portal で次の手順に従います。

1. <a href="https://portal.azure.com/" target="_blank">Azure portal</a> にサインインし、(B2C ではなく) 適切な Azure AD テナントを選択します。
2. **[アプリの登録]** セクションに移動して、自分のアプリを選択します。
3. **[管理]** 下にある **[ブランド]** を選択します。
4. **[サービス利用規約 URL]** と **[プライバシーに関する声明 URL]** フィールドを入力します。
5. **[保存]** を選択します。

    ![サービス利用規約 URL とプライバシーに関する声明 URL を含むアプリのプロパティ](./media/howto-add-terms-of-service-privacy-statement/azure-portal-terms-service-privacy-statement-urls.png)

### <a name="using-the-app-object-json"></a><a name="app-object-json"></a>アプリ オブジェクト JSON を使用する

アプリ オブジェクト JSON を直接変更する場合、Azure portal またはアプリケーション登録ポータルでマニフェスト エディターを使用して、自分のアプリのサービス利用規約とプライバシーに関する声明へのリンクを含めることができます。

1. **[アプリの登録]** セクションに移動し、自分のアプリを選択します。
2. **[マニフェスト]** ペインを開きます。
3. Ctrl + F キーを押し、"informationalUrls" を検索します。 情報を入力します。
4. 変更を保存します。

```json
    "informationalUrls": { 
        "termsOfService": "<your_terms_of_service_url>", 
        "privacy": "<your_privacy_statement_url>" 
    }
```

### <a name="using-the-microsoft-graph-api"></a><a name="msgraph-rest-api"></a>Microsoft Graph API を使用する

プログラムを使用してすべてのアプリを更新するには、Microsoft Graph API を使用してすべてのアプリを更新し、サービス利用規約とプライバシーに関する声明のドキュメントへのリンクを含めることができます。

```
PATCH https://graph.microsoft.com/v1.0/applications/{application id}
{ 
    "appId": "{your application id}", 
    "info": { 
        "termsOfServiceUrl": "<your_terms_of_service_url>", 
        "supportUrl": null, 
        "privacyStatementUrl": "<your_privacy_statement_url>", 
        "marketingUrl": null, 
        "logoUrl": null 
    }
}
```

> [!NOTE]
> * 次のフィールド (`supportUrl`、`marketingUrl`、`logoUrl`) に割り当てた既存の値を上書きしないように注意してください。
> * Microsoft Graph API は、Azure AD アカウントを使用してサインインした場合にのみ機能します。 個人用 Microsoft アカウントはサポートされていません。
