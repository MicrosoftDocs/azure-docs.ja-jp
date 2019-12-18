---
title: Microsoft ID プラットフォームにアプリを登録する | Azure
description: Microsoft ID プラットフォームにアプリケーションを追加および登録する方法について説明します。
services: active-directory
author: rwike77
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: quickstart
ms.workload: identity
ms.date: 05/09/2019
ms.author: ryanwi
ms.custom: aaddev, identityplatformtop40
ms.reviewer: aragra, lenalepa, sureshja
ms.collection: M365-identity-device-management
ms.openlocfilehash: 58a68b89ce38664c0d0ec87b509fe5f872cfd78c
ms.sourcegitcommit: a5ebf5026d9967c4c4f92432698cb1f8651c03bb
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/08/2019
ms.locfileid: "74921417"
---
# <a name="quickstart-register-an-application-with-the-microsoft-identity-platform"></a>クイック スタート:Microsoft ID プラットフォームにアプリケーションを登録する

エンタープライズ開発者や SaaS (サービスとしてのソフトウェア) プロバイダーは、商用クラウド サービスまたは基幹業務アプリケーションを開発し、それらを Microsoft ID プラットフォームと統合することによって、自社のサービスにセキュリティで保護されたサインインと認可を実現することができます。

このクイック スタートでは、Azure portal で**アプリの登録**エクスペリエンスを使用してアプリケーションを追加および登録し、Microsoft ID プラットフォームと統合できるようにする方法を説明します。 新しいアプリ登録エクスペリエンスに追加された機能と強化された機能について詳しくは、[こちらのブログ記事](https://developer.microsoft.com/graph/blogs/new-app-registration/)を参照してください。

## <a name="register-a-new-application-using-the-azure-portal"></a>Azure portal を使用して新しいアプリケーションを登録します

1. 職場または学校アカウントか、個人の Microsoft アカウントを使用して、[Azure portal](https://portal.azure.com) にサインインします。
1. ご利用のアカウントで複数のテナントにアクセスできる場合は、右上隅でアカウントを選択し、ポータルのセッションを目的の Azure AD テナントに設定します。
1. **Azure Active Directory** を検索して選択します。 **[Active Directory]** ページで、 **[アプリの登録]** を選択し、 **[新しい登録]** を選択します。
1. **[アプリケーションの登録]** ページが表示されたら、以下のアプリケーションの登録情報を入力します。

   - **[名前]** - アプリのユーザーに表示されるわかりやすいアプリケーション名を入力します。
   - **[サポートされているアカウントの種類]** - アプリケーションでサポートするアカウントを選択します。

       | サポートされているアカウントの種類 | 説明 |
       |-------------------------|-------------|
       | **この組織のディレクトリ内のアカウントのみ** | 基幹業務 (LOB) アプリケーションを作成している場合は、このオプションを選択します。 アプリケーションをディレクトリに登録していない場合、このオプションは選択できません。<br><br>このオプションは、Azure AD のみのシングルテナントに対応します。<br><br>これは、ディレクトリの外部にアプリを登録している場合を除き、既定のオプションです。 アプリがディレクトリの外部に登録される場合、既定のオプションは Azure AD マルチテナントと個人の Microsoft アカウントです。 |
       | **任意の組織のディレクトリ内のアカウント** | 企業および教育機関のすべてのユーザーを対象とする場合は、このオプションを選択します。<br><br>このオプションは、Azure AD のみのマルチテナントに対応します。<br><br>アプリを Azure AD のみのシングルテナントとして登録した場合は、 **[認証]** ブレードを使用して、Azure AD マルチテナントに更新したり、シングルテナントに戻したりすることができます。 |
       | **任意の組織のディレクトリ内のアカウントと、個人用の Microsoft アカウント** | 最も広範な顧客のセットを対象とする場合は、このオプションを選択します。<br><br>このオプションは、Azure AD マルチテナントと個人用の Microsoft アカウントに対応します。<br><br>アプリを Azure AD マルチテナントと個人用の Microsoft アカウントとして登録した場合は、これを UI で変更することはできません。 代わりに、アプリケーション マニフェスト エディターを使用して、サポートされているアカウントの種類を変更する必要があります。 |

   - **[リダイレクト URI]\(オプション)** - 構築しているアプリの種類として **[Web]** または **[パブリック クライアント (モバイルとデスクトップ)]** を選択してから、アプリケーションのリダイレクト URI (または応答 URL) を入力します。
       - Web アプリケーションの場合は、アプリのベース URL を指定します。 ローカル マシンで実行されている Web アプリの URL であれば、たとえば `https://localhost:31544` のようになります。 ユーザーはこの URL を使用して、Web クライアント アプリケーションにサインインすることになります。
       - パブリック クライアント アプリケーションの場合は、トークン応答を返すために Azure AD によって使用される URI を指定します。 アプリケーション固有の値 (たとえば、`myapp://auth`) を入力します。

     Web アプリケーションまたはネイティブ アプリケーションの具体的な例を見るには、[クイック スタート](https://docs.microsoft.com/azure/active-directory/develop)を参照してください。

1. 終了したら、 **[登録]** を選択します。

    [![Azure portal で新しいアプリケーションを登録する画面の画像](./media/quickstart-add-azure-ad-app-preview/new-app-registration-expanded.png)](./media/quickstart-add-azure-ad-app-preview/new-app-registration-expanded.png#lightbox)

Azure AD によりアプリに一意のアプリケーション (クライアント) ID が割り当てられ、アプリケーションの **[概要]** ページが表示されます。 アプリケーションにさらに機能を追加するには、ブランド、証明書とシークレット、API のアクセス許可など、その他の構成オプションを選択できます。

[![新たに登録されたアプリの概要ページの例](./media/quickstart-add-azure-ad-app-preview/new-app-overview-page-expanded.png)](./media/quickstart-add-azure-ad-app-preview/new-app-overview-page-expanded.png#lightbox)

## <a name="next-steps"></a>次の手順

- [アクセス許可と同意](v2-permissions-and-consent.md)について学習します。
- アプリケーション登録で追加の構成機能 (資格情報、アクセス許可など) を有効にしたり、他のテナントのユーザーによるサインインを有効にしたりするには、以下のクイック スタートを参照してください。
    - [Web API にアクセスするようにクライアント アプリケーションを構成する](quickstart-configure-app-access-web-apis.md)
    - [Web API を公開するようにアプリケーションを構成する](quickstart-configure-app-expose-web-apis.md)
    - [アプリケーションによってサポートされるアカウントを変更する](quickstart-modify-supported-accounts.md)
- 迅速にアプリを構築し、トークンの取得、トークンの更新、ユーザーへのサインイン、ユーザー情報の表示などの機能を追加するには、[クイック スタート](https://docs.microsoft.com/azure/active-directory/develop)を選択します。
- 登録されたアプリケーションを表す 2 つの Azure AD オブジェクトと、両者間の関係については、[Application objects and service principal objects](app-objects-and-service-principals.md)\(アプリケーション オブジェクトとサービス プリンシパル オブジェクト\) を参照してください。
- アプリを開発するときに使用するブランド化ガイドラインについては、「[アプリケーションのブランド化ガイドライン](howto-add-branding-in-azure-ad-apps.md)」を参照してください。
