---
title: クイック スタート:Microsoft ID プラットフォームでアプリを登録する | Azure
description: このクイックスタートでは、アプリケーションを Microsoft ID プラットフォームに登録する方法について説明します。
services: active-directory
author: mmacy
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: quickstart
ms.workload: identity
ms.date: 09/03/2020
ms.author: marsma
ms.custom: aaddev, identityplatformtop40, contperf-fy21q1, contperf-fy21q2
ms.reviewer: aragra, lenalepa, sureshja
ms.openlocfilehash: c72ae2a2e6dbd2278bdd78f26c145386be22764e
ms.sourcegitcommit: 24a12d4692c4a4c97f6e31a5fbda971695c4cd68
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/05/2021
ms.locfileid: "102175432"
---
# <a name="quickstart-register-an-application-with-the-microsoft-identity-platform"></a>クイック スタート:Microsoft ID プラットフォームにアプリケーションを登録する

このクイックスタートでは、Azure portal でアプリを登録し、Microsoft ID プラットフォームでアプリケーションとそのユーザーに認証および承認サービスを提供できるようにします。

Microsoft ID プラットフォームは、登録済みのアプリケーションに対してのみ ID およびアクセス管理 (IAM) を実行します。 Web またはモバイル アプリのようなクライアント アプリケーション、またはクライアント アプリを支援する Web API のいずれであっても、それを登録することにより、アプリケーションと ID プロバイダー、つまり Microsoft ID プラットフォームとの間の信頼関係が確立されます。

## <a name="prerequisites"></a>前提条件

* アクティブなサブスクリプションが含まれる Azure アカウント。 [無料でアカウントを作成できます](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。
* [テナントの設定](quickstart-create-new-tenant.md)に関するクイックスタートを完了していること。

## <a name="register-an-application"></a>アプリケーションを登録する

アプリケーションを登録すると、アプリケーションと Microsoft ID プラットフォームとの間の信頼関係が確立されます。 この信頼は一方向です。つまり、アプリは Microsoft ID プラットフォームを信頼しますが、その逆はありません。

アプリ登録を作成するには、次の手順に従います。

1. <a href="https://portal.azure.com/" target="_blank">Azure portal</a> にサインインします。
1. 複数のテナントにアクセスできる場合は、トップ メニューで、 **[ディレクトリとサブスクリプション]** フィルター (:::image type="icon" source="./media/common/portal-directory-subscription-filter.png" border="false":::) を使用して、アプリケーションを登録するテナントを選択します。
1. **Azure Active Directory** を検索して選択します。
1. **[管理]** で **[アプリの登録]**  >  **[新規登録]** の順に選択します。
1. アプリケーションの表示 **名** を入力します。 表示名は、サインイン時など、アプリケーションのユーザーがアプリを使用するときに表示されることがあります。
    表示名はいつでも変更できます。また、複数のアプリの登録で同じ名前を共有できます。 ID プラットフォーム内では、表示名ではなく、アプリ登録の自動生成されたアプリケーション (クライアント) ID によってアプリが一意に識別されます。
1. アプリケーションを使用できるユーザーを指定します。これは、"*サインインの対象ユーザー*" と呼ばれることもあります。

    | サポートされているアカウントの種類 | 説明 |
    |-------------------------|-------------|
    | **この組織のディレクトリ内のアカウントのみ** | "*ご利用の*" テナント内のユーザー (またはゲスト) によってのみ使用されるアプリケーションをビルドしている場合、このオプションを選択します。<br><br>このアプリは、多くの場合、"*基幹業務*" (LOB) アプリケーションと呼ばれ、Microsoft ID プラットフォームの "*シングルテナント*" アプリケーションです。 |
    | **任意の組織のディレクトリ内のアカウント** | "*任意*" の Azure Active Directory (Azure AD) テナント内のユーザーがアプリケーションを使用できるようにする場合は、このオプションを選択します。 このオプションは、たとえば、複数の組織に提供する目的でサービスとしてのソフトウェア (SaaS) アプリケーションをビルドしている場合に適しています。<br><br>Microsoft ID プラットフォームでは、この種類のアプリは、"*マルチテナント*" アプリケーションと呼ばれます。 |
    | **任意の組織のディレクトリ内のアカウントと、個人用の Microsoft アカウント** | 最も広範な顧客のセットを対象とする場合は、このオプションを選択します。<br><br>個人用 "*Microsoft アカウント*" を持つユーザーもサポートできる "*マルチテナント*" アプリケーションを登録するには、このオプションを選択します。 |
    | **個人用 Microsoft アカウント** | 個人用 Microsoft アカウントを持つユーザー専用のアプリケーションを構築している場合は、このオプションを選択します。 個人用 Microsoft アカウントには、Skype、Xbox、Live、Hotmail のアカウントが含まれます。 |

1. **[リダイレクト URI (省略可能)]** には何も入力しないでください。 リダイレクト URI は、次のセクションで構成します。
1. **[登録]** を選択して、初期のアプリ登録を完了します。

    :::image type="content" source="media/quickstart-register-app/portal-02-app-reg-01.png" alt-text="[アプリケーションの登録] ペインを示す、Web ブラウザー内の Azure portal のスクリーンショット。":::

登録が完了すると、Azure portal に、アプリの登録の **[概要]** ペインが表示されます。 **[アプリケーション (クライアント) ID]** の値を確認します。 この値は、"*クライアント ID*" とも呼ばれ、Microsoft ID プラットフォーム内のアプリケーションを一意に識別します。

アプリケーションのコード (より一般的には、アプリケーションで使用される認証ライブラリ) でも、このクライアント ID を使用します。 この ID は、ID プラットフォームから受信するセキュリティ トークンの検証過程で使用されます。

:::image type="content" source="media/quickstart-register-app/portal-03-app-reg-02.png" alt-text="アプリの登録の [概要] ペインを示す、Web ブラウザー内の Azure portal のスクリーンショット。":::

## <a name="add-a-redirect-uri"></a>リダイレクト URI を追加する

"*リダイレクト URI*" は、認証後に Microsoft ID プラットフォームによってユーザーのクライアントがリダイレクトされ、セキュリティ トークンが送信される場所です。

たとえば、運用 Web アプリケーションでは、多くの場合、リダイレクト URI は、アプリが実行されているパブリック エンドポイント (例: `https://contoso.com/auth-response`) です。 開発時には、アプリをローカルで実行するエンドポイント (例: `https://127.0.0.1/auth-response` または `http://localhost/auth-response`) も追加するのが一般的です。

登録済みのアプリケーションのリダイレクト URI を追加および変更するには、その[プラットフォーム設定](#configure-platform-settings)を構成します。

### <a name="configure-platform-settings"></a>プラットフォーム設定を構成する

リダイレクト URI など、アプリケーションの種類ごとの設定は、Azure portal の **[プラットフォーム構成]** で構成します。 **Web** や **シングルページ アプリケーション** などの一部のプラットフォームでは、リダイレクト URI を手動で指定する必要があります。 モバイルやデスクトップなどの他のプラットフォームでは、他の設定を構成するときに自動的に生成されるリダイレクト URI から選択できます。

ターゲットのプラットフォームまたはデバイスに基づいてアプリケーション設定を構成するには、次の手順を行います。

1. Azure portal の **[アプリの登録]** で、対象のアプリケーションを選択します。
1. **[管理]** で、 **[認証]** を選択します。
1. **[プラットフォーム構成]** で **[プラットフォームを追加]** を選択します。
1. **[プラットフォームの構成]** で、アプリケーションの種類 (プラットフォーム) のタイルを選択して、その設定を構成します。

    :::image type="content" source="media/quickstart-register-app/portal-04-app-reg-03-platform-config.png" alt-text="Azure portal の [プラットフォームの構成] ペインのスクリーンショット。" border="false":::

    | プラットフォーム | 構成設定 |
    | -------- | ---------------------- |
    | **Web** | アプリの **リダイレクト URI** を入力します。 この URI は、認証後に Microsoft ID プラットフォームによってユーザーのクライアントがリダイレクトされ、セキュリティ トークンが送信される場所です。<br/><br/>サーバーで実行される標準の Web アプリケーションについては、このプラットフォームを選択します。 |
    | **シングルページ アプリケーション** | アプリの **リダイレクト URI** を入力します。 この URI は、認証後に Microsoft ID プラットフォームによってユーザーのクライアントがリダイレクトされ、セキュリティ トークンが送信される場所です。<br/><br/>JavaScript や、Angular、Vue.js、React.js、Blazor WebAssembly などのフレームワークを使用してクライアント側 Web アプリを構築している場合は、このプラットフォームを選択します。 |
    | **iOS / macOS** | アプリの **バンドル ID** を入力します。 これは、 **[ビルド設定]** または *Info.plist* 内の Xcode で検索します。<br/><br/>**バンドル ID** を指定すると、リダイレクト URI が自動的に生成されます。 |
    | **Android** | アプリの **パッケージ名** を入力します。 これは、*AndroidManifest.xml* ファイル内で検索します。 また、**署名ハッシュ** も生成して入力します。<br/><br/>これらの設定を指定すると、リダイレクト URI が自動的に生成されます。 |
    | **モバイル アプリケーションとデスクトップ アプリケーション** | **推奨されるリダイレクト URI** のいずれかを選択します。 または、**カスタム リダイレクト URI** を指定します。<br/><br/>デスクトップ アプリケーションの場合は、次をお勧めします<br/>`https://login.microsoftonline.com/common/oauth2/nativeclient`<br/><br/>最新の Microsoft Authentication Library (MSAL) を使用していない、またはブローカーを使用していないモバイル アプリケーションには、このプラットフォームを選択します。 また、デスクトップ アプリケーションにも、このプラットフォームを選択します。 |
1. **[構成する]** を選択して、プラットフォームの構成を完了します。

### <a name="redirect-uri-restrictions"></a>リダイレクト URI の制限

アプリ登録に追加するリダイレクト URI の形式には、いくつかの制限があります。 これらの制限の詳細については、「[リダイレクト URI (応答 URL) に関する制約と制限](reply-url.md)」を参照してください。

## <a name="add-credentials"></a>資格情報を追加する

資格情報は、Web API にアクセスする [Confidential クライアント アプリケーション](msal-client-applications.md)によって使用されます。 Confidential クライアントの例としては、[Web アプリ](scenario-web-app-call-api-overview.md)、その他の [Web API](scenario-protected-web-api-overview.md)、または[サービス型およびデーモン型アプリケーション](scenario-daemon-overview.md)などがあります。 資格情報により、アプリケーションはそれ自体として認証され、実行時にユーザーによる操作は必要ありません。 

証明書とクライアント シークレット (文字列) の両方を資格情報として Confidential クライアント アプリの登録に追加できます。

:::image type="content" source="media/quickstart-register-app/portal-05-app-reg-04-credentials.png" alt-text="[アプリの登録] の [証明書およびシークレット] ペインを示す Azure portal のスクリーンショット。":::

### <a name="add-a-certificate"></a>証明書を追加する

資格情報の種類としては、証明書 ("*公開キー*" と呼ばれることもあります) が推奨されます。 クライアント シークレットよりも確実性が高くなります。 アプリケーションにおける認証方法として証明書を使用する方法について詳しくは、「[Microsoft ID プラットフォーム アプリケーションの認証証明書資格情報](active-directory-certificate-credentials.md)」を参照してください。

1. Azure portal の **[アプリの登録]** で、対象のアプリケーションを選択します。
1. **[証明書およびシークレット]**  >  **[証明書のアップロード]** の順に選択します。
1. アップロードするファイルを選択します。 ファイルの種類は *.cer*、 *.pem*、 *.crt* のいずれかである必要があります。
1. **[追加]** を選択します。

### <a name="add-a-client-secret"></a>クライアント シークレットの追加

クライアント シークレットは、"*アプリケーション パスワード*" とも呼ばれます。 これは、アプリが自身の身元を示すために証明書の代わりに使用できる文字列値です。 2 種類の資格情報のうち、より使いやすいのはクライアント シークレットです。 多くの場合は開発時に使用されますが、証明書より安全性が低いと見なされています。 運用環境で実行するアプリケーションでは、証明書を使用してください。 

アプリケーションのセキュリティに関する推奨事項について詳しくは、「[Microsoft ID プラットフォームのベスト プラクティスと推奨事項](identity-platform-integration-checklist.md#security)」を参照してください。

1. Azure portal の **[アプリの登録]** で、対象のアプリケーションを選択します。
1. **[証明書およびシークレット]**  >   **[新しいクライアント シークレット]** の順に選択します。
1. クライアント シークレットの説明を追加します。
1. 期間を選択します。
1. **[追加]** を選択します。
1. クライアント アプリケーションのコードで使用できるように、"*シークレットの値を記録します*"。 このページからの移動後は、このシークレットの値は "*二度と表示されません*"。


## <a name="next-steps"></a>次のステップ

クライアント アプリケーションでは、通常、Web API 内のリソースにアクセスする必要があります。 Microsoft ID プラットフォームを使用することで、クライアント アプリケーションを保護できます。 また、プラットフォームを使用して、Web API に対する、スコープ指定されたアクセス許可ベースのアクセスを承認することもできます。

このシリーズの次のクイックスタートに進んで、Web API 用に別のアプリ登録を作成し、そのスコープを公開します。

> [!div class="nextstepaction"]
> [Web API を公開するようにアプリケーションを構成する](quickstart-configure-app-expose-web-apis.md)
