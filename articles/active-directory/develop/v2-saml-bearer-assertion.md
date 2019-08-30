---
title: Microsoft ID プラットフォームと SAML ベアラー アサーション フロー | Azure
description: SAML ベアラー アサーション フローを使用して、ユーザーに資格情報の入力を求めずに Microsoft Graph からデータをフェッチする方法について説明します。
services: active-directory
documentationcenter: ''
author: umeshbarapatre
manager: CelesteDG
editor: ''
ms.assetid: ''
ms.service: active-directory
ms.subservice: develop
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 08/05/2019
ms.author: ryanwi
ms.reviewer: hirsin
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: 13b316568ba555de764c1aaa4ddf0e72d25cf24f
ms.sourcegitcommit: 5b76581fa8b5eaebcb06d7604a40672e7b557348
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/13/2019
ms.locfileid: "68990647"
---
# <a name="microsoft-identity-platform-and-oauth-20-saml-bearer-assertion-flow"></a>Microsoft ID プラットフォームと OAuth 2.0 SAML ベアラー アサーション フロー
OAuth 2.0 SAML ベアラー アサーション フローでは、クライアントが既存の信頼関係を使用する必要があるときに、SAML アサーションを使用して OAuth アクセス トークンを要求することができます。 SAML アサーションに適用される署名は、承認されたアプリの認証を提供します。 SAML アサーションは、ID プロバイダーによって発行され、サービス プロバイダーによって使用される XML セキュリティ トークンです。 サービス プロバイダーは、セキュリティ関連の目的でアサーションの対象を識別するために、そのコンテンツに依存します。

SAML アサーションは、OAuth トークン エンドポイントにポストされます。  エンドポイントはアサーションを処理し、アプリの事前の承認に基づいてアクセス トークンを発行します。 クライアントでは、更新トークンを持つ必要も保存する必要もありません。また、トークン エンドポイントに渡す必要があるクライアント シークレットも不要です。

SAML ベアラー アサーション フローは、ユーザーに資格情報の入力を求めることなく Microsoft Graph API (委任されたアクセス許可のみをサポート) からデータをフェッチする場合に便利です。 このシナリオでは、バックグラウンド プロセスで推奨されるクライアント資格情報の付与は機能しません。

ブラウザーベースの対話型サインインを実行して SAML アサーションを取得してから、OAuth で保護された API (Microsoft Graph など) にアクセスを追加するアプリケーションの場合、API のアクセス トークンを取得する OAuth 要求を行うことができます。 ユーザーを認証するためにブラウザーが Azure AD にリダイレクトされると、ブラウザーは SAML サインインからセッションを取得し、ユーザーは資格情報を入力する必要はありません。

OAuth SAML ベアラー アサーション フローは、Azure Active Directory との間で認証連携された Active Directory フェデレーション サービス (ADFS) などの ID プロバイダーを使用して認証されるユーザーに対してもサポートされます。  ADFS から取得した SAML アサーションを OAuth フローで使用して、ユーザーを認証できます。

![OAuth フロー](./media/v2-saml-bearer-assertion/1.png)

## <a name="call-graph-using-saml-bearer-assertion"></a>SAML ベアラー アサーションを使用して Graph を呼び出す
ここで、実際にプログラムで SAML アサーションを取得する方法について説明します。 この方法は、ADFS を使用してテストされます。 ただし、これが機能するのは、プログラムで SAML アサーションが返されるのをサポートしている ID プロバイダーを使用した場合になります。 基本的なプロセスとしては、SAML アサーションを取得し、アクセス トークンを取得して、Microsoft Graph にアクセスします。

### <a name="prerequisites"></a>前提条件

承認サーバー/環境 (Microsoft 365) と ID プロバイダー、または SAML 2.0 ベアラー アサーションの発行者 (ADFS) との間で信頼関係を確立します。 シングル サインオン用および ID プロバイダーとして ADFS を構成するには、[こちらの記事](https://blogs.technet.microsoft.com/canitpro/2015/09/11/step-by-step-setting-up-ad-fs-and-enabling-single-sign-on-to-office-365/)をご覧ください。

次の手順に従って、[ポータル](https://ms.portal.azure.com/#blade/Microsoft_AAD_RegisteredApps/ApplicationsListBlade)でアプリケーションを登録します。
1. [ポータルの [アプリの登録] ブレード](https://ms.portal.azure.com/#blade/Microsoft_AAD_RegisteredApps/ApplicationsListBlade)にサインインします (Graph API には v2.0 エンドポイントを使用しているため、このポータルでアプリケーションを登録する必要があることに注意してください。 それ以外の場合は、Azure Active Directory で登録を使用することもできます)。 
1. **[新規登録]** を選択します。
1. **[アプリケーションの登録]** ページが表示されたら、以下のアプリケーションの登録情報を入力します。 
    1. **[名前]** - アプリのユーザーに表示されるわかりやすいアプリケーション名を入力します。
    1. **[サポートされているアカウントの種類]** - アプリケーションでサポートするアカウントを選択します。
    1. **[リダイレクト URI (省略可能)]** - 構築するアプリの種類として [Web クライアント] または [パブリック クライアント (モバイルとデスクトップ)] を選択してから、アプリケーションのリダイレクト URI (または応答 URL) を入力します。
    1. 終了したら、 **[登録]** を選択します。
1. アプリケーション (クライアント) ID をメモします。
1. 左側のウィンドウで、 **[証明書とシークレット]** を選択します。 **[クライアント シークレット]** セクションで、 **[新しいクライアント シークレット]** をクリックします。 新しいクライアント シークレットをコピーしてください。このブレードから移動すると取得できなくなります。
1. 左側のウィンドウで、 **[API のアクセス許可]** 、 **[アクセス許可の追加]** の順に選択します。 Outlook Graph API を使用する予定のため、 **[Microsoft Graph]** を選択し、 **[委任されたアクセス許可]** を選択してから、 **[Tasks.read]** を選択します。 

サンプル要求をテストするために必要なツールである [Postman](https://www.getpostman.com/) をインストールします。  後で、要求をコードに変換できます。

### <a name="get-the-saml-assertion-from-adfs"></a>ADFS から SAML アサーションを取得する
SOAP エンベロープを使用して SAML アサーションをフェッチするために、ADFS エンドポイントへの POST 要求を作成します。

![SAML アサーションを取得する](./media/v2-saml-bearer-assertion/2.png)

ヘッダー値:

![ヘッダー値](./media/v2-saml-bearer-assertion/3.png)

ADFS 要求本文:

![ADFS 要求本文](./media/v2-saml-bearer-assertion/4.png)

この要求が正常にポストされると、ADFS から SAML アサーションを受け取ります。 **SAML:Assertion** タグ データのみが必要です。今後の要求で使用するには、base64 エンコードに変換してください。

### <a name="get-the-oauth2-token-using-the-saml-assertion"></a>SAML アサーションを使用して OAuth2 トークンを取得する 
この手順では、ADFS アサーション応答を使用して OAuth2 トークンをフェッチします。

1. 以下に示すように、ヘッダー値を使用して POST 要求を作成します。

    ![POST 要求](./media/v2-saml-bearer-assertion/5.png)
1. 要求の本文で、**client_id**、**client_secret**、および **assertion** (前の手順で取得した base64 エンコードの SAML アサーション) を置き換えます。

    ![要求本文](./media/v2-saml-bearer-assertion/6.png)
1. 要求が成功すると、Azure Active Directory からアクセス トークンを受け取ります。

### <a name="get-the-data-with-the-oauth-token"></a>Oauth トークンを使用してデータを取得する

アクセス トークンを受け取ったら、Graph API (この例では Outlook タスク) を呼び出します。 

1. 前の手順でフェッチしたアクセス トークンを使用して GET 要求を作成します。

    ![GET 要求](./media/v2-saml-bearer-assertion/7.png)

1. 要求が成功すると、JSON 応答を受け取ります。

## <a name="next-steps"></a>次の手順

さまざまな[認証フローとアプリケーションのシナリオ](authentication-flows-app-scenarios.md)について学習します。