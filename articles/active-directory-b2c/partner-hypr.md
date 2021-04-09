---
title: Azure Active Directory B2C と HYPR を構成するチュートリアル
titleSuffix: Azure AD B2C
description: 真のパスワードレスの強力な顧客認証のために Azure Active Directory B2C と HYPR を構成するチュートリアル
services: active-directory-b2c
author: gargi-sinha
manager: martinco
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 08/27/2020
ms.author: gasinh
ms.subservice: B2C
ms.openlocfilehash: b80b1a4b3f9bcde6cf01b0e0e59425c6783bd5d9
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "94953765"
---
# <a name="tutorial-for-configuring-hypr-with-azure-active-directory-b2c"></a>HYPR と Azure Active Directory B2C を構成するチュートリアル

このサンプル チュートリアルでは、Azure AD B2C と [HYPR](https://get.hypr.com) を構成する方法についてのガイダンスを提供します。 ID プロバイダーとして Azure AD B2C を使用し、HYPR を任意の顧客アプリケーションに統合することで、真のパスワードレス認証をユーザーに提供できます。 HYPR により、パスワードが公開キー暗号化に置き換えられ、不正行為、フィッシング、資格情報の再利用が排除されます。

## <a name="prerequisites"></a>前提条件

作業を開始するには、以下が必要です。

- Azure AD サブスクリプション。 サブスクリプションがない場合は、[無料アカウント](https://azure.microsoft.com/free/)を取得できます。

- [Azure AD B2C テナント](./tutorial-create-tenant.md)。 テナントは Azure サブスクリプションにリンクされています。

- HYPR クラウド テナント。無料の[試用版アカウント](https://get.hypr.com/free-trial)を取得します。

- ユーザーのモバイル デバイス。HYPR Rest API を使用するか、お客様の HYPR テナントの HYPR デバイス マネージャーを使用して登録します。 たとえば、[HYPR Java SDK](https://docs.hypr.com/integratinghypr/docs/hypr-java-web-sdk) を使用してこのタスクを実行できます。

## <a name="scenario-description"></a>シナリオの説明

HYPR 統合には、次のコンポーネントが含まれています。

- Azure AD B2C – ユーザーの資格情報の検証を受け持つ承認サーバー。ID プロバイダーとも呼ばれます。

- Web およびモバイル アプリケーション - HYPR と Azure AD B2C を使用して保護することを選択したモバイルまたは Web アプリケーション。 HYPR は信頼性の高いモバイル SDK を提供します。また、真のパスワードレス認証を実行するために iOS および Android プラットフォームで使用できるモバイル アプリも提供します。

- HYPR モバイル アプリ - 独自のモバイル アプリケーションでモバイル SDK を使用しない場合は、HYPR モバイル アプリを使用してこのサンプルを実行できます。

- HYPR Rest API - HYPR API を使用して、ユーザー デバイスの登録と認証の両方を実行できます。 これらの API については、[こちら](https://apidocs.hypr.com)を参照してください。

次のアーキテクチャの図に、この実装を示します。

![HYPR アーキテクチャ図のスクリーンショット](media/partner-hypr/hypr-architecture-diagram.png)

|手順 | 説明 |
|:-----| :-----------|
| 1. | ユーザーがログイン ページにアクセスします。 ユーザーがサインイン/サインアップを選択し、ユーザー名をページに入力します。
| 2. | アプリケーションが本人確認のためにユーザー属性を Azure AD B2C に送信します。
| 3. | Azure AD B2C がユーザー属性を収集し、属性を HYPR に送信して、HYPR モバイル アプリを通じてユーザーを認証します。
| 4. | Fast Identity Online (FIDO) 認定認証のために、登録されたユーザーのモバイル デバイスに HYPR がプッシュ通知を送信します。 これはユーザーの指紋、生体認証、または分散型 PIN のいずれかです。  
| 5. | ユーザーがプッシュ通知を確認した後、本人確認の結果に基づいてユーザーが顧客アプリケーションへのアクセスを許可または拒否されます。

## <a name="configure-the-azure-ad-b2c-policy"></a>Azure AD B2C ポリシーを構成する

1. ポリシー フォルダーの [Azure AD B2C HYPR ポリシー](https://github.com/HYPR-Corp-Public/Azure-AD-B2C-HYPR-Sample/tree/master/policy)に移動します。

2. この[ドキュメント](./custom-policy-get-started.md?tabs=applications#custom-policy-starter-pack)に従って、[LocalAccounts スターター パック](https://github.com/Azure-Samples/active-directory-b2c-custom-policy-starterpack/tree/master/LocalAccounts)をダウンロードします。

3. Azure AD B2C テナントのポリシーを構成します。

>[!NOTE]
>お使いの特定のテナントに関連付けるため、提供されているポリシーを更新してください。

## <a name="test-the-user-flow"></a>ユーザー フローをテストする

1. Azure AD B2C テナントを開き、[ポリシー] の下にある **[Identity Experience Framework]** を選択します。

2. 以前に作成した **SignUpSignIn** を選択します。

3. **[ユーザー フローを実行します]** を選択し、設定を選択します。

   a. **[アプリケーション]** : 登録済みのアプリを選択します (サンプルは JWT)

   b. **[応答 URL]** : **リダイレクト URL** を選択します

   c. **[ユーザー フローを実行します]** を選択します。

4. サインアップ フローを実行し、アカウントを作成します

5. HYPR は、ユーザー属性が作成された後、フロー中に呼び出されます。 フローが不完全な場合は、ユーザーがディレクトリに保存されていないことを確認してください。

## <a name="next-steps"></a>次のステップ

追加情報については、次の記事を参照してください。

- [Azure AD B2C のカスタム ポリシー](./custom-policy-overview.md)

- [Azure AD B2C のカスタム ポリシーの概要](./custom-policy-get-started.md?tabs=applications)