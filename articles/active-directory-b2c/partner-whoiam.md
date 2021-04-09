---
title: WhoIAM で Azure Active Directory B2C を構成するためのチュートリアル
titleSuffix: Azure AD B2C
description: このチュートリアルでは、ユーザー確認のために Azure AD B2C 認証を WhoIAM と統合する方法について説明します。
services: active-directory-b2c
author: gargi-sinha
manager: martinco
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 08/20/2020
ms.author: gasinh
ms.subservice: B2C
ms.openlocfilehash: d1b2c7513562e951e1098cf327780387ddf6a495
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "94953544"
---
# <a name="tutorial-for-configuring-whoiam-with-azure-active-directory-b2c"></a>Azure Active Directory B2C を使用して WhoIAM を構成するためのチュートリアル

このサンプル チュートリアルでは、お使いの環境でブランド化された ID 管理システム (BRIMS) の [WhoIAM](https://www.whoiam.ai/brims/) を構成し、それを Active Directory B2C (Azure AD B2C) と統合する方法についてのガイダンスを提供します。

BRIMS は、お使いの環境にデプロイされるアプリとサービスのセットです。 ユーザー ベースの音声、SMS、および電子メールの検証を行います。 BRIMS は既存の ID およびアクセス管理ソリューションと連携して機能し、プラットフォームに依存しません。

## <a name="prerequisites"></a>前提条件

作業を開始するには、以下が必要です。

- Azure AD サブスクリプション。 サブスクリプションがない場合は、[無料アカウント](https://azure.microsoft.com/free/)を取得できます。

- お使いの Azure サブスクリプションにリンクされている [Azure AD B2C テナント](./tutorial-create-tenant.md)。

- WhoIAM [試用版アカウント](https://www.whoiam.ai/contact-us/)。

## <a name="scenario-description"></a>シナリオの説明

WhoIAM 統合には、次のコンポーネントが含まれています。

- Azure AD B2C テナント。 これは承認サーバーで、そこで定義されたカスタム ポリシーに基づいてユーザーの資格情報を検証します。 ID プロバイダーとも呼ばれます。

- クライアントとその構成を管理するための管理ポータル。

- エンドポイントを介してさまざまな機能を公開する API サービス。  

- BRIMS 管理ポータルと API サービスの両方のバックエンドとして機能する Azure Cosmos DB。

次のアーキテクチャの図に、この実装を示します。

![WhoIAM と統合された Azure AD B2C のアーキテクチャの図。](media/partner-whoiam/whoiam-architecture-diagram.png)

|手順 | 説明 |
|:-----| :-----------|
| 1. | ユーザーは、Azure AD B2C を ID プロバイダーとして使用するアプリへのサインアップまたはサインイン要求を開始するためのページにアクセスします。
| 2. | 認証の一部として、ユーザーは電子メールまたは電話の所有権を確認するか、または音声を生体認証の確認要素として使用するように要求します。  
| 3. | Azure AD B2C は、BRIMS API サービスを呼び出し、ユーザーの電子メール アドレス、電話番号、音声記録を渡します。
| 4. | BRIMS は、完全にカスタマイズ可能な電子メールや SMS テンプレートなどの事前定義された構成を使用して、アプリのスタイルと一致した方法で、それぞれの言語でユーザーと対話します。
| 5. | ユーザーの本人確認が完了すると、BRIMS は検証の結果を示すトークンを Azure AD B2C に返します。 Azure AD B2C は、アプリへのアクセスをユーザーに許可するか、認証の試行に失敗します。  

## <a name="sign-up-with-whoiam"></a>WhoIAM を使用してサインアップする

1. [WhoIAM](https://www.whoiam.ai/contact-us/) に連絡して、BRIMS アカウントを作成します。

2. 利用可能なサインアップのガイドラインを使用して、次の Azure サービスを構成します。

    - [Azure Key Vault](https://azure.microsoft.com/services/key-vault/):メール サービスのパスワードなど、パスワードを安全に保管するために使用されます。

    - [Azure App Service](https://azure.microsoft.com/services/app-service/):BRIMS API と管理ポータル サービスをホストするために使用されます。

    - [Azure Active Directory](https://azure.microsoft.com/services/active-directory/): 管理ポータルの管理ユーザーを認証するために使用されます。

    - [Azure Cosmos DB](https://azure.microsoft.com/services/cosmos-db/):設定を格納および取得するために使用されます。

    - [Application Insights](../azure-monitor/app/app-insights-overview.md) (省略可能):API と管理ポータルの両方にログインするために使用されます。

3. BRIMS API と BRIMS 管理ポータルを Azure 環境にデプロイします。

4. Azure AD B2C カスタム ポリシーのサンプルは、BRIMS のサインアップ ドキュメントで入手できます。 ドキュメントに従ってアプリを構成し、ユーザーの本人確認に BRIMS プラットフォームを使用します。  

WhoIAM の BRIMS の詳細については、[製品ドキュメント](https://www.whoiam.ai/brims/)を参照してください。

## <a name="test-the-user-flow"></a>ユーザー フローをテストする

1. Azure AD B2C テナントを開きます。 **[ポリシー]** で **[Identity Experience Framework]** を選択します。

2. 以前に作成した **SignUpSignIn** を選択します。

3. **[ユーザー フローを実行します]** を選択して、次を行います。

   a. **[アプリケーション]** の場合は、登録済みのアプリを選択します (サンプルは JWT)。

   b. **[応答 URL]** の場合は、**リダイレクト URL** を選択します。

   c. **[ユーザー フローを実行します]** を選択します。

4. サインアップ フローを実行し、アカウントを作成します。

5. BRIMS サービスは、ユーザー属性が作成された後、フロー中に呼び出されます。 フローが不完全な場合は、ユーザーがディレクトリに保存されていないことを確認してください。

## <a name="next-steps"></a>次のステップ

追加情報については、次の記事を参照してください。

- [Azure AD B2C のカスタム ポリシー](./custom-policy-overview.md)

- [Azure AD B2C のカスタム ポリシーの概要](./custom-policy-get-started.md?tabs=applications)