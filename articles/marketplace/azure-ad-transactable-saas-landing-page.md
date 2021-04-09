---
title: 取引可能な SaaS オファー用のランディング ページをコマーシャル マーケットプレースに作成する
description: 取引可能な SaaS オファー用のランディング ページを作成する方法について説明します。
author: mingshen-ms
ms.author: mingshen
ms.reviewer: dannyevers
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: how-to
ms.date: 09/02/2020
ms.openlocfilehash: 4bfc29472373a53bcebb2ba59134d1f3702d4793
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "102549874"
---
# <a name="build-the-landing-page-for-your-transactable-saas-offer-in-the-commercial-marketplace"></a>取引可能な SaaS オファー用のランディング ページをコマーシャル マーケットプレースに作成する

この記事では、Microsoft コマーシャル マーケットプレースで販売される取引可能な SaaS アプリ用のランディング ページを作成する手順について説明します。

## <a name="overview"></a>概要

ランディング ページは、サービスとしてのソフトウェア (SaaS) オファーのための "ロビー" と考えることができます。 オファーをサブスクライブした後、購入者はコマーシャル マーケットプレースによってランディング ページにリダイレクトされ、そこで SaaS アプリケーションのサブスクリプションをアクティブにして構成します。 これは、購入内容とアカウントの詳細を購入者が確認するための注文確認手順と見なすことができます。 お客様は、Azure Active Directory (Azure AD) と Microsoft Graph を使用して、購入者がシングル サインオン (SSO) を利用できるようにしたり、購入者に関する重要な詳細情報を入手したりします。これには、その名前、メール アドレス、組織が含まれ、これを使用してサブスクリプションの確認とアクティブ化ができます。

サブスクリプションをアクティブにするために必要な情報は限られており、それは Azure AD と Microsoft Graph によって提供されるため、基本的な同意では得られない情報を要求する必要はないはずです。 追加の同意がないとアプリケーションに必要なユーザーの詳細情報が得られないのであれば、サブスクリプションのアクティブ化が完了した後にその情報を要求する必要があります。 そうすることで購入者はスムーズにサブスクリプションをアクティブ化でき、中止のリスクが減少します。

通常、ランディング ページには次の要素が含まれます。

- 購入したオファーとプランの名前、および支払方法を表示する。
- 購入者のアカウントの詳細 (姓と名、組織、メールなど) を表示する。
- さまざまなアカウントの詳細を確認または置換するよう購入者に促す。
- アクティブ化の後、それに続く手順へ購入者を導く。 これには、ウェルカム メールの受信、サブスクリプションの管理、サポートの利用、ドキュメントの確認などがあります。

> [!NOTE]
> また、アクティブ化した後で購入者がサブスクリプションを管理する場合も、このランディング ページに移動させられます。 購入者のサブスクリプションがアクティブになった後、SSO を使用してユーザーがサインインできるようにする必要があります。 アカウントのプロファイルまたは構成ページにユーザーを移動させることをお勧めします。

次のセクションで、ランディング ページを作成する手順について説明します。

1. ランディング ページ用の [Azure AD アプリの登録を作成する](#create-an-azure-ad-app-registration)。
1. アプリの[開始点としてコード サンプルを使用する](#use-a-code-sample-as-a-starting-point)。
1. [2 つの Azure AD アプリを使用して、運用環境のセキュリティを向上させる](#use-two-azure-ad-apps-to-improve-security-in-production)。
1. コマーシャル マーケットプレースによって URL に追加された[マーケットプレース購入 ID トークンを解決する](#resolve-the-marketplace-purchase-identification-token)。
1. [ID トークン内にエンコードされている要求から情報を読み取る](#read-information-from-claims-encoded-in-the-id-token)。これはサインイン後に Azure AD から受信されるものであり、要求と共に送信されます。
1. [Microsoft Graph API を使用](#use-the-microsoft-graph-api)して、必要に応じて追加情報を収集します。

## <a name="create-an-azure-ad-app-registration"></a>Azure AD アプリの登録を作成する

コマーシャル マーケットプレースは、Azure AD と完全に統合されています。 マーケットプレースにアクセスした購入者は、[Azure AD アカウントまたは Microsoft アカウント (MSA)](../active-directory/fundamentals/active-directory-whatis.md#terminology) で認証されています。 購入後、購入者はコマーシャル マーケットプレースからランディング ページ URL に移動し、SaaS アプリケーションのサブスクリプションをアクティブにして管理します。 購入者が Azure AD SSO を使用してアプリケーションにサインインできるようにする必要があります。 (ランディング ページの URL は、そのオファーの [[技術的な構成]](plan-saas-offer.md#technical-information) ページで指定されます)。

ID を使用するための最初の手順として、ランディング ページが Azure AD アプリケーションとして登録されていることを確認します。 アプリケーションを登録すると、Azure AD を使用してユーザーを認証したり、ユーザー リソースへのアクセスを要求したりできるようになります。 これは、アプリケーションの定義と見なすことができます。これにより、サービスは、アプリの設定に基づいてアプリにトークンを発行する方法を認識できるようになります。

### <a name="register-a-new-application-using-the-azure-portal"></a>Azure portal を使用して新しいアプリケーションを登録します

作業を開始するには、[新しいアプリケーションの登録](../active-directory/develop/quickstart-register-app.md)に関する記事の手順に従います。 他の企業のユーザーがアプリにアクセスできるようにするには、アプリケーションを使用できるユーザーを指定するように求められたときに、マルチテナント オプションの 1 つを選択する必要があります。

Microsoft Graph API に対してクエリを実行する場合は、[Web API にアクセスするように新しいアプリケーションを構成](../active-directory/develop/quickstart-configure-app-access-web-apis.md)します。 このアプリケーションの API アクセス許可を選択する場合、オンボード プロセスをスムーズかつ自動的に実行するのに必要な購入者に関する基本情報は、**User.Read** の既定値で十分に収集できます。 **[needs admin consent]\(管理者の同意が必要\)** というラベルが付けられている API アクセス許可を要求しないでください。要求すると、管理者以外のユーザーは誰もランディング ページにアクセスできなくなります。

オンボード プロセスやプロビジョニング プロセスの一部として管理者特権のアクセス許可が必要な場合は、Azure AD の[増分同意](../active-directory/azuread-dev/azure-ad-endpoint-comparison.md)機能を使用することを検討します。この機能を使用すると、マーケットプレースからリダイレクトされたすべての購入者は最初、ランディング ページを操作できます。

## <a name="use-a-code-sample-as-a-starting-point"></a>開始点としてコード サンプルを使用する

Azure AD ログインが有効化されているシンプルな Web サイトを実装するサンプル アプリをいくつかご用意しています。 アプリケーションが Azure AD に登録されると、図 1 に示すように、一般的なアプリケーションの種類と開発スタックの一覧が **[クイック スタート]** ブレードに表示されます。 環境に合うものを選択し、ダウンロードとセットアップの手順に従います。

"***図 1:Azure portal の [クイック スタート] ブレード***

:::image type="content" source="./media/azure-ad-saas/azure-ad-quickstart-blade.png" alt-text="Azure portal の [クイック スタート] ブレードの表示。":::

コードをダウンロードし、開発環境を設定したら、前の手順で記録したアプリケーション ID、テナント ID、クライアント シークレットを反映するように、アプリの構成設定を変更します。 実際の手順は、使用しているサンプルによって異なることにご注意ください。

## <a name="use-two-azure-ad-apps-to-improve-security-in-production"></a>2 つの Azure AD アプリを使用して、運用環境のセキュリティを向上する

この記事では、コマーシャル マーケットプレース SaaS オファー用のランディング ページを実装するためのアーキテクチャの簡略化されたバージョンについて説明します。 運用環境でページを実行する場合は、セキュリティで保護された別のアプリケーションのみを通して SaaS Fulfillment API と通信して、セキュリティを強化することをお勧めします。 そのためには、次の 2 つの新しいアプリケーションを作成する必要があります。

- 1 つ目は、ここまでで説明したマルチテナント ランディング ページ アプリケーションです。ただし、SaaS Fulfillment API に接続する機能はありません。 この機能は、次に示す別のアプリケーションにオフロードされます。
- 2 つ目は、SaaS Fulfillment API との通信機能を持つためのアプリケーションです。 このアプリケーションはシングル テナントでなければならず、自分の組織によってのみ使用されます。また、API へのアクセスをこのアプリだけに限定するためにアクセス制御リストを確立できます。

これにより、[懸念事項の分離](/dotnet/architecture/modern-web-apps-azure/architectural-principles#separation-of-concerns)の原則を遵守したシナリオでこのソリューションを使用できるようになります。 たとえば、このランディング ページでは、最初の登録済み Azure AD アプリを使用してユーザーのサインインが処理されます。 ユーザーがサインインすると、ランディング ページでは 2 番目の Azure AD を使用してアクセス トークンが要求され、SaaS Fulfillment API の呼び出しと、解決操作の呼び出しが行われます。

## <a name="resolve-the-marketplace-purchase-identification-token"></a>マーケットプレース購入 ID トークンを解決する

購入者がランディング ページにリダイレクトされると、URL パラメーターにトークンが追加されます。 このトークンは、Azure AD によって発行されたトークンやサービス間認証に使用されるアクセス トークンとは別のもので、[SaaS Fulfillment API](./partner-center-portal/pc-saas-fulfillment-api-v2.md#resolve-a-purchased-subscription) 解決呼び出しによってサブスクリプションの詳細を取得するための入力として使用されます。 SaaS Fulfillment API へのすべての呼び出しと同様に、サービス間の要求の認証は、アプリのサービス間認証用 Azure AD アプリケーション ID ユーザーに基づくアクセス トークンを使用して行われます。

> [!NOTE]
> ほとんどの場合、2 つ目のシングル テナント アプリケーションからこの呼び出しを行うことをお勧めします。 この記事で前に紹介した「[2 つの Azure AD アプリを使用して、運用環境のセキュリティを向上する](#use-two-azure-ad-apps-to-improve-security-in-production)」を参照してください。

### <a name="request-an-access-token"></a>アクセス トークンを要求する

SaaS Fulfillment API を使用してアプリケーションを認証するには、アクセス トークンが必要です。これは、Azure AD OAuth エンドポイントを呼び出せば生成できます。 「[公開元の承認トークンを取得する方法](./partner-center-portal/pc-saas-registration.md#how-to-get-the-publishers-authorization-token)」を参照してください。

### <a name="call-the-resolve-endpoint"></a>解決エンドポイントを呼び出す

SaaS Fulfillment API では、[解決](./partner-center-portal/pc-saas-fulfillment-api-v2.md#resolve-a-purchased-subscription)エンドポイントを実装します。これを呼び出すことで、マーケットプレース トークンの有効性を確認することや、サブスクリプションに関する情報を返すことができます。

## <a name="read-information-from-claims-encoded-in-the-id-token"></a>ID トークンにエンコードされている要求から情報を読み取る

[OpenID Connect](../active-directory/develop/v2-protocols-oidc.md) フローの一部として、購入者がランディング ページにリダイレクトされると、Azure AD によって [ID トークン](../active-directory/develop/id-tokens.md)がその要求に追加されます。 このトークンには、アクティブ化プロセスで役立つ複数の基本情報が含まれています。これには、次の表に示す情報などが含まれます。

| 値 | 説明 |
| ------------ | ------------- |
| aud | このトークンの対象ユーザー。 この場合は、アプリケーション ID に一致し、検証されている必要があります。 |
| preferred_username | アクセスしているユーザーのプライマリ ユーザー名。 これはメール アドレス、電話番号、またはその他の識別子である場合があります。 |
| email | ユーザーのメール アドレス。 このフィールドは空である可能性があることに注意してください。 |
| name | トークンのサブジェクトを識別する、人間が判読できる値。 この場合は、購入者の名前です。 |
| oid | アプリケーションにまたがるユーザーを一意に識別する、Microsoft ID システム内の識別子。 Microsoft Graph は、この値を特定のユーザー アカウントの ID プロパティとして返します。 |
| tid | 購入者が属している Azure AD テナントを表す識別子。 MSA ID の場合、これは常に ``9188040d-6c67-4c5b-b112-36a304b66dad`` です。 詳細については、次のセクション「Microsoft Graph API を使用する」の注を参照してください。 |
| sub | この特定のアプリケーション内のユーザーを一意に識別する識別子。 |
|||

## <a name="use-the-microsoft-graph-api"></a>Microsoft Graph API を使用する

ID トークンには購入者を識別するための基本的な情報が含まれていますが、オンボード プロセスを完了するために、アクティブ化プロセスで追加の詳細情報 (購入者の会社など) が必要になる場合があります。 このような情報を要求するために [Microsoft Graph API](/graph/use-the-api) を使用すると、これらの詳細の再入力をユーザーに要求することを避けることができます。 標準的な **User.Read** アクセス許可には、既定で次の情報が含まれています。

| 値 | 説明 |
| ------------ | ------------- |
| displayName | ユーザーのアドレス帳に表示される名前。 |
| givenName | ユーザーの名。 |
| jobTitle | ユーザーの役職。 |
| mail | ユーザーの SMTP アドレス。 |
| MobilePhone | ユーザーの携帯電話番号 (代表)。 |
| preferredLanguage | ユーザーの優先する言語の ISO 639-1 コード。 |
| surname | ユーザーの姓。 |
|||

要求に含めるために、ユーザーの会社の名前やユーザーの所在地 (国) などの追加のプロパティを選択できます。 詳細については、[user リソースの種類のプロパティ](/graph/api/resources/user#properties)を参照してください。

Azure AD に登録されているほとんどのアプリは、会社の Azure AD テナントからユーザーの情報を読み取るための委任されたアクセス許可を付与します。 その情報を取得するための Microsoft Graph へのすべての要求には、認証のためのアクセス トークンが必要です。 アクセス トークンを生成するための具体的な手順は、使用しているテクノロジ スタックによって異なりますが、サンプル コードには例が含まれています。 詳細については、「[ユーザーの代わりにアクセスを取得](/graph/auth-v2-user)」を参照してください。

> [!NOTE]
> MSA テナント (テナント ID は ``9188040d-6c67-4c5b-b112-36a304b66dad``) のアカウントでは、ID トークンで既に収集されている内容より詳細な情報は返されません。 そのため、これらのアカウントでは Graph API へのこの呼び出しをスキップできます。

## <a name="next-steps"></a>次のステップ

- [コマーシャル マーケットプレースでの SaaS オファーの作成方法](create-new-saas-offer.md)