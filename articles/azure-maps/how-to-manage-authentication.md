---
title: 認証を管理する
titleSuffix: Azure Maps
description: Azure Maps 認証について理解を深めます。 どの方法がどのシナリオで最適に機能するかを確認します。 ポータルを使用して認証設定を表示する方法について説明します。
author: anastasia-ms
ms.author: v-stharr
ms.date: 06/12/2020
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: timlt
ms.openlocfilehash: 8fc27ea4de7e81f1279f68d2e4a1a7fde1fcf41f
ms.sourcegitcommit: bfeae16fa5db56c1ec1fe75e0597d8194522b396
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/10/2020
ms.locfileid: "88037339"
---
# <a name="manage-authentication-in-azure-maps"></a>Azure Maps での認証の管理

Azure Maps アカウントを作成した後、Azure Active Directory (Azure AD) 認証および共有キー認証をサポートするためにクライアント ID とキーが作成されます。

## <a name="view-authentication-details"></a>認証の詳細を表示する

Azure Maps アカウントの作成後、主キーと 2 次キーが生成されます。 [共有キー認証を使用して](https://docs.microsoft.com/azure/azure-maps/azure-maps-authentication#shared-key-authentication) Azure Maps を呼び出す場合は、サブスクリプション キーとして主キーを使用することをお勧めします。 2 次キーは、キー変更のローリングなどのシナリオで使用できます。 詳細については、「[Azure Maps による認証](https://aka.ms/amauth)」をご覧ください。

認証の詳細は、Azure portal で確認できます。 Azure portal の自分のアカウントで、 **[設定]** メニューの **[認証]** を選択します。

> [!div class="mx-imgBorder"]
> ![認証の詳細](./media/how-to-manage-authentication/how-to-view-auth.png)

## <a name="discover-category-and-scenario"></a>カテゴリとシナリオを調べる

アプリケーションのニーズに応じて、アプリケーションをセキュリティで保護するための特定の方法があります。 Azure AD では、さまざまな認証フローをサポートするためのカテゴリが定義されています。 アプリケーションがどのカテゴリに適合するかについては、「[アプリケーションのカテゴリ](https://docs.microsoft.com/azure/active-directory/develop/authentication-flows-app-scenarios#application-categories)」を参照してください。

> [!NOTE]
> 共有キー認証を使用する場合でも、カテゴリとシナリオを理解することは、アプリケーションをセキュリティで保護するのに役立ちます。

## <a name="determine-authentication-and-authorization"></a>認証と認可を判断する

次の表に、Azure Maps での一般的な認証と認可のシナリオの概要を示します。 表では、それぞれのシナリオによって提供される保護の種類を比較しています。

> [!IMPORTANT]
> Microsoft では、運用アプリケーション用に Azure Active Directory (Azure AD) およびロールベースのアクセス制御 (RBAC) を実装することをお勧めします。

| シナリオ                                                                                    | 認証 | 承認 | 開発の作業量 | 運用の作業量 |
| ------------------------------------------------------------------------------------------- | -------------- | ------------- | ------------------ | ------------------ |
| [信頼済みデーモンまたは非対話型クライアント アプリケーション](./how-to-secure-daemon-app.md)        | 共有キー     | 該当なし           | Medium             | 高               |
| [信頼済みデーモンまたは非対話型クライアント アプリケーション](./how-to-secure-daemon-app.md)        | Azure AD       | 高          | 低                | Medium             |
| [対話型のシングル サインオンを使用する Web シングル ページ アプリケーション](./how-to-secure-spa-users.md) | Azure AD       | 高          | Medium             | Medium             |
| [非対話型サインオンを使用する Web シングル ページ アプリケーション](./how-to-secure-spa-app.md)      | Azure AD       | 高          | Medium             | Medium             |
| [対話型シングル サインオンを使用する Web アプリケーション](./how-to-secure-webapp-users.md)          | Azure AD       | 高          | 高               | Medium             |
| [IoT デバイスまたは入力の制約付きデバイス](./how-to-secure-device-code.md)                     | Azure AD       | 高          | Medium             | Medium             |

各シナリオの詳細な構成情報については、表のリンクを参照してください。

## <a name="view-role-definitions"></a>ロール定義を表示する

Azure Maps に使用できる Azure ロールを表示するには、 **[アクセス制御 (IAM)]** に移動します。 **[ロール]** を選択してから、「*Azure Maps*」で始まるロールを検索します。 これらの Azure Maps ロールが、アクセス権を付与できるロールです。

> [!div class="mx-imgBorder"]
> ![使用可能なロールの表示](./media/how-to-manage-authentication/how-to-view-avail-roles.png)

## <a name="view-role-assignments"></a>ロールの割り当てを表示する

Azure Maps の RBAC が付与されているユーザーやアプリを表示するには、 **[アクセス制御 (IAM)]** に移動します。 そこで、 **[ロールの割り当て]** を選択してから、「**Azure Maps**」でフィルター処理します。

> [!div class="mx-imgBorder"]
> ![RBAC が付与されているユーザーやアプリの表示](./media/how-to-manage-authentication/how-to-view-amrbac.png)

## <a name="request-tokens-for-azure-maps"></a>Azure Maps のトークンを要求する

Azure AD トークン エンドポイントからトークンを要求します。 Azure AD 要求で、次の詳細を使用します。

| Azure 環境      | Azure AD トークン エンドポイント             | Azure リソース ID              |
| ---------------------- | ----------------------------------- | ------------------------------ |
| Azure パブリック クラウド     | `https://login.microsoftonline.com` | `https://atlas.microsoft.com/` |
| Azure Government クラウド | `https://login.microsoftonline.us`  | `https://atlas.microsoft.com/` |

Azure AD からユーザーやサービス プリンシパルのアクセス トークンを要求する方法の詳細については、[Azure AD の認証シナリオ](https://docs.microsoft.com/azure/active-directory/develop/authentication-scenarios)に関するページを参照し、[シナリオ](./how-to-manage-authentication.md#determine-authentication-and-authorization)の表から特定のシナリオを確認してください。

## <a name="next-steps"></a>次のステップ

詳細については、[Azure AD と Azure Maps Web SDK](https://docs.microsoft.com/azure/azure-maps/how-to-use-map-control) に関する記事をご覧ください。

Azure Maps アカウントにおける API 使用状況メトリックを確認します。
> [!div class="nextstepaction"]
> [使用状況メトリックを表示する](how-to-view-api-usage.md)

Azure AD と Azure Maps を統合する方法を示すサンプルを確認します。

> [!div class="nextstepaction"]
> [Azure AD 認証のサンプル](https://github.com/Azure-Samples/Azure-Maps-AzureAD-Samples)
