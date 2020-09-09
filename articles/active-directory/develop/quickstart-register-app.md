---
title: クイック スタート:Microsoft ID プラットフォームにアプリを統合する | Azure
description: このクイックスタートでは、アプリケーションを追加し、Microsoft ID プラットフォームに登録する方法について説明します。
services: active-directory
author: rwike77
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: quickstart
ms.workload: identity
ms.date: 03/12/2020
ms.author: ryanwi
ms.custom: aaddev, identityplatformtop40
ms.reviewer: aragra, lenalepa, sureshja
ms.openlocfilehash: 65fff06b4a2d28bbc276920ccbaba90d814d03f3
ms.sourcegitcommit: b8702065338fc1ed81bfed082650b5b58234a702
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/11/2020
ms.locfileid: "88115358"
---
# <a name="quickstart-register-an-application-with-the-microsoft-identity-platform"></a>クイック スタート:Microsoft ID プラットフォームにアプリケーションを登録する

このクイックスタートでは、Azure portal で**アプリの登録 (プレビュー)** エクスペリエンスを使用してアプリを登録します。 

Azure Active Directory テナントに登録することで、Microsoft ID プラットフォームにアプリが登録されます。 エンタープライズ開発者や SaaS (サービスとしてのソフトウェア) プロバイダーは、商用クラウド サービスまたは基幹業務アプリケーションを開発し、それらを Microsoft ID プラットフォームと統合できます。 統合によって、そのようなサービスのサインインと認証がセキュリティで保護されます。

## <a name="prerequisites"></a>前提条件

* アクティブなサブスクリプションが含まれる Azure アカウント。 [無料でアカウントを作成できます](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio)。
* [Azure AD テナント](quickstart-create-new-tenant.md)。

## <a name="register-a-new-application-using-the-azure-portal"></a>Azure portal を使用して新しいアプリケーションを登録します

1. 職場または学校アカウントか、個人の Microsoft アカウントを使用して、[Azure portal](https://portal.azure.com) にサインインします。
1. お使いのアカウントで複数のテナントにアクセスできる場合は、右上隅で自分のアカウントをクリックします。 ポータル セッションを目的の Azure AD テナントに設定します。
1. **Azure Active Directory** を検索して選択します。 **[管理]** の **[アプリの登録]** を選択します。
1. **[新規登録]** を選択します。
1. **[アプリケーションの登録]** に、ユーザーに表示するわかりやすい名前を入力します。
1. 次のようにアプリケーションを使用できる人を指定します。

    | サポートされているアカウントの種類 | 説明 |
    |-------------------------|-------------|
    | **この組織のディレクトリ内のアカウントのみ** | 基幹業務 (LOB) アプリケーションを作成している場合は、このオプションを選択します。 アプリケーションをディレクトリに登録していない場合、このオプションは選択できません。<br><br>このオプションは、Azure AD のみのシングルテナントに対応します。<br><br>このオプションは、ディレクトリの外部にアプリを登録している場合を除き、既定です。 アプリがディレクトリの外部に登録される場合、既定のオプションは Azure AD マルチテナントと個人の Microsoft アカウントです。 |
    | **任意の組織のディレクトリ内のアカウント** | 企業および教育機関のすべてのユーザーを対象とする場合は、このオプションを選択します。<br><br>このオプションは、Azure AD のみのマルチテナントに対応します。<br><br>アプリを Azure AD のみのシングルテナントとして登録した場合は、 **[認証]** ページを使用して、Azure AD マルチテナントに更新したり、シングルテナントに戻したりすることができます。 |
    | **任意の組織のディレクトリ内のアカウントと、個人用の Microsoft アカウント** | 最も広範な顧客のセットを対象とする場合は、このオプションを選択します。<br><br>このオプションは、Azure AD マルチテナントと個人用の Microsoft アカウントに対応します。<br><br>アプリを Azure AD マルチテナントと個人用の Microsoft アカウントとして登録した場合は、この設定を UI で変更することはできません。 代わりに、アプリケーション マニフェスト エディターを使用して、サポートされているアカウントの種類を変更する必要があります。 |

1. **[リダイレクト URI (省略可能)]** で、ビルド中のアプリの種類を選択します。**Web** か**パブリック クライアント (モバイル & デスクトップ)** です。 次にアプリケーションのリダイレクト URI または応答 URL を入力します。

    * Web アプリケーションの場合は、アプリのベース URL を指定します。 ローカル マシンで実行されている Web アプリの URL であれば、たとえば `https://localhost:31544` のようになります。 ユーザーはこの URL を使用して、Web クライアント アプリケーションにサインインすることになります。
    * パブリック クライアント アプリケーションの場合は、トークン応答を返すために Azure AD によって使用される URI を指定します。 アプリケーション固有の値 (たとえば、`myapp://auth`) を入力します。

    Web アプリケーションやネイティブ アプリケーションの例については、「[Microsoft ID プラットフォーム](./index.yml)」のクイックスタートを参照してください。

1. 終了したら、 **[登録]** を選択します。

    ![Azure portal で新しいアプリケーションを登録する画面の画像](./media/quickstart-add-azure-ad-app-preview/new-app-registration.png)

Azure AD によって一意のアプリケーションまたはクライアントがアプリに割り当てられます。 ポータルでアプリケーションの **[概要]** ページが開きます。 アプリケーションに機能を追加するには、ブランド、証明書とシークレット、API のアクセス許可など、その他の構成オプションを選択できます。

![新たに登録されたアプリの概要ページの例](./media/quickstart-add-azure-ad-app-preview/new-app-overview-page-expanded.png)

## <a name="next-steps"></a>次のステップ

* Web API にアクセスするには、以下のクイックスタートを参照してください。[Web API にアクセスするようにクライアント アプリケーションを構成する](quickstart-configure-app-access-web-apis.md)

* アクセス許可の詳細については、「[Microsoft ID プラットフォーム エンドポイントでのアクセス許可と同意](v2-permissions-and-consent.md)」を参照してください。

* Web API を公開するには、「[クイックスタート:Web API を公開するようにアプリケーションを構成する](quickstart-configure-app-expose-web-apis.md)」を参照してください。

* サポートされているアカウントを管理するには、「[クイックスタート:アプリケーションでサポートされているアカウントを変更する](quickstart-modify-supported-accounts.md)」を参照してください。

* アプリをビルドし、機能を追加するには、「[Microsoft ID プラットフォーム](./index.yml)」のクイックスタートを参照してください。

* 登録されたアプリケーションを表す 2 つの Azure AD オブジェクトと、両者間の関係については、[Application objects and service principal objects](app-objects-and-service-principals.md)\(アプリケーション オブジェクトとサービス プリンシパル オブジェクト\) を参照してください。

* アプリを開発するときに使用するブランド化ガイドラインの詳細については、「[アプリケーションのブランド化ガイドライン](howto-add-branding-in-azure-ad-apps.md)」を参照してください。