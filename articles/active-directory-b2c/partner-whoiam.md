---
title: whoIam で Azure Active Directory B2C を構成するためのチュートリアル
titleSuffix: Azure AD B2C
description: ユーザー確認のために Azure AD B2C 認証を whoIam と統合する方法について説明します
services: active-directory-b2c
author: gargi-sinha
manager: martinco
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 08/20/2020
ms.author: gasinh
ms.subservice: B2C
ms.openlocfilehash: 6545c5f40004dc50904618a8ea734eb73eeee933
ms.sourcegitcommit: d39f2cd3e0b917b351046112ef1b8dc240a47a4f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/25/2020
ms.locfileid: "88817130"
---
# <a name="tutorial-for-configuring-whoiam-with-azure-active-directory-b2c"></a>Azure Active Directory B2C を使用して WhoIAM を構成するためのチュートリアル

このサンプル チュートリアルでは、お使いの環境でブランド化された ID 管理システム (BRIMS) の [WhoIAM](https://www.whoiam.ai/brims/) を構成し、それを Azure AD B2C と統合する方法についてのガイダンスを提供します。

WhoIAM の BRIMS は、お使いの環境にデプロイされるアプリとサービスのセットです。 ユーザー ベースの音声、SMS、および電子メールの検証を行います。 BRIMS は既存の ID およびアクセス管理ソリューションと連携して機能し、プラットフォームに依存しません。

## <a name="prerequisites"></a>前提条件

開始するには、以下が必要です。

- Azure AD サブスクリプション。 サブスクリプションがない場合は、[無料アカウント](https://azure.microsoft.com/free/)を取得できます。

- お使いの Azure サブスクリプションにリンクされている [Azure AD B2C テナント](https://docs.microsoft.com/azure/active-directory-b2c/tutorial-create-tenant)。

- WhoIAM [試用版アカウント](https://www.whoiam.ai/contact-us/)

## <a name="scenario-description"></a>シナリオの説明

WhoIAM 統合には、次のコンポーネントが含まれています。

- Azure AD B2C テナント – テナントで定義されているカスタム ポリシーに基づいてユーザーの資格情報を検証する承認サーバー。 ID プロバイダーとも呼ばれます。

- クライアントとその構成を管理するための管理ポータル。

- エンドポイントを介してさまざまな機能を公開する API サービス。  

- BRIMS 管理ポータルと API サービスの両方のバックエンドとして使用される Azure Cosmos DB。

次のアーキテクチャの図に、この実装を示します。

![WhoIAM アーキテクチャ図のスクリーンショット](media/partner-whoiam/whoiam-architecture-diagram.png)

|手順 | 説明 |
|:-----| :-----------|
| 1. | ユーザーがログイン ページにアクセスします。 ユーザーは、Azure AD B2C を ID プロバイダーとして使用するアプリへのサインアップまたはサインイン要求を開始します。
| 2. | 認証の一部として、ユーザーは電子メールまたは電話の所有権を確認するか、または音声を生体認証の確認要素として使用するように要求します。  
| 3. | Azure AD B2C は、ユーザーの電子メール アドレス、電話番号、および音声記録を使用して BRIMS API サービスを呼び出します。
| 4. | BRIMS は、完全にカスタマイズ可能な電子メールや SMS テンプレートなどの事前定義された構成を使用して、アプリのルック アンド フィールと一致したそれぞれの言語でユーザーと対話します。
| 5. | ユーザーの本人確認が完了すると、BRIMS は検証の結果を示すトークンを Azure AD B2C に返します。 Azure AD B2C は、アプリへのアクセスをユーザーに許可するか、認証の試行に失敗します。  

## <a name="onboard-with-whoiam"></a>WhoIAM を使用してオンボードする

1. [WhoIAM](https://www.whoiam.ai/contact-us/) に連絡して、BRIMS アカウントを作成します。

2. アカウントが作成されたら、利用可能なオンボードのガイドラインを使用して、次の Azure サービスを構成します。

    - [Azure Key Vault](https://azure.microsoft.com/services/key-vault/) - メール サービスのパスワードなど、パスワードを安全に保管するために使用されます。

    - [Azure App Service](https://azure.microsoft.com/services/app-service/) - BRIMS API と管理ポータル サービスをホストするために使用されます。

    - [Azure Active Directory](https://azure.microsoft.com/services/active-directory/) - 管理ポータルの管理ユーザーを認証するために使用されます。

    - [Azure Cosmos DB](https://azure.microsoft.com/services/cosmos-db/) - 設定を格納および取得するために使用されます。

    - [Application Insights](https://docs.microsoft.com/azure/azure-monitor/app/app-insights-overview#:~:text=Application%20Insights%2C%20a%20feature%20of%20Azure%20Monitor%2C%20is,professionals.%20Use%20it%20to%20monitor%20your%20live%20applications.) (オプション) - API と管理ポータルの両方にログインするために使用されます。

3. BRIMS API と BRIMS 管理ポータルを Azure 環境にデプロイします。

4. Azure AD B2C カスタム ポリシーのサンプルは、BRIMS のオンボード ドキュメントで入手できます。 ドキュメントに従ってアプリを構成し、ユーザーの本人確認に BRIMS プラットフォームを使用します。  

WhoIAM の BRIMS の詳細については、[製品ドキュメント](https://www.whoiam.ai/brims/)を参照してください。

## <a name="test-the-user-flow"></a>ユーザー フローをテストする

1. Azure AD B2C テナントを開き、[ポリシー] の下にある **[Identity Experience Framework]** を選択します。

2. 以前に作成した **SignUpSignIn** を選択します。

3. **[ユーザー フローを実行します]** を選択し、設定を選択します。

   a. **[アプリケーション]** : 登録済みのアプリを選択します (サンプルは JWT)

   b. **[応答 URL]** : **リダイレクト URL** を選択します

   c. **[ユーザー フローを実行します]** を選択します。

4. サインアップ フローを実行し、アカウントを作成します

5. BRIMS サービスは、ユーザー属性が作成された後、フロー中に呼び出されます。 フローが不完全な場合は、ユーザーがディレクトリに保存されていないことを確認してください。

## <a name="next-steps"></a>次のステップ

追加情報については、次の記事を参照してください。

- [Azure AD B2C のカスタム ポリシー](https://docs.microsoft.com/azure/active-directory-b2c/custom-policy-overview)

- [Azure AD B2C のカスタム ポリシーの概要](https://docs.microsoft.com/azure/active-directory-b2c/custom-policy-get-started?tabs=applications)
