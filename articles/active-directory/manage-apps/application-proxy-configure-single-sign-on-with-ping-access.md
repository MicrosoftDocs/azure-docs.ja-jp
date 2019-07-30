---
title: Azure AD アプリケーション プロキシ用 PingAccess を使用したヘッダーベースの認証 | Microsoft Docs
description: ヘッダーベースの認証に対応するには、PingAccess とアプリケーション プロキシを使ってアプリケーションを発行します。
services: active-directory
documentationcenter: ''
author: msmimart
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 05/08/2019
ms.author: celested
ms.reviewer: harshja
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: 54a99d001f8cb59af3042ce8b6849a2cd9480e99
ms.sourcegitcommit: 0ebc62257be0ab52f524235f8d8ef3353fdaf89e
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/10/2019
ms.locfileid: "67723996"
---
# <a name="header-based-authentication-for-single-sign-on-with-application-proxy-and-pingaccess"></a>アプリケーション プロキシと PingAccess を使用したシングル サインオン用のヘッダーベースの認証

Azure Active Directory (Azure AD) アプリケーション プロキシは、Azure AD の顧客が複数のアプリケーションにアクセスできるように、PingAccess と連携しました。 PingAccess は[既存のアプリケーション プロキシ サービス](application-proxy.md)を拡張し、ヘッダーを使って認証を行うアプリケーションへのシングル サインオン アクセスに対応します。

## <a name="whats-pingaccess-for-azure-ad"></a>PingAccess for Azure AD とは?

PingAccess for Azure AD を使用すると、認証にヘッダーを使用するアプリケーションへのアクセス権およびシングル サイン オン (SSO) をユーザーに与えることができます。 アプリケーション プロキシはこれらのアプリを他のアプリと同様に扱い、Azure AD を使ってアクセスを認証した後、コネクタ サービスを通してトラフィックを渡します。 PingAccess は、アプリケーションの前に位置し、アクセス トークンを Azure AD からヘッダーに変換します。 その後、アプリケーションは、読み取り可能な形式で認証を受信します。

会社のアプリケーションを使用するためにサインインするユーザーは、いかなる違いにも気付きません。 ユーザーはいつもどおり、任意のデバイスでどこからでも作業を行うことができます。 アプリケーション プロキシ コネクタは、その認証の種類に関係なくすべてのアプリにリモート トラフィックを誘導します。そのため負荷分散も引き続き自動的に行われます。

## <a name="how-do-i-get-access"></a>利用方法

このシナリオは Azure Active Directory と PingAccess の連携によって実現されるため、その両方のサービスのライセンスが必要となります。 ただし、Azure Active Directory Premium サブスクリプションには、最大 20 のアプリケーションをカバーする基本的な PingAccess ライセンスが含まれています。 20 を超えるヘッダー ベースのアプリケーションを公開する必要がある場合は、PingAccess から追加のライセンスを購入できます。

詳細については、「 [Azure Active Directory のエディション](../fundamentals/active-directory-whatis.md)」をご覧ください。

## <a name="publish-your-application-in-azure"></a>アプリケーションを Azure に発行する

この記事は、このシナリオで初めてアプリケーションを公開する方を対象としています。 発行手順の詳細を示すだけでなく、アプリケーション プロキシと PingAccess の両方を開始できるように説明します。 両方のサービスは構成済みであり、公開手順の再確認が目的の場合は、「[アプリケーション プロキシを使って Azure AD にアプリを登録する](#add-your-application-to-azure-ad-with-application-proxy)」のセクションに進むことができます。

> [!NOTE]
> このシナリオは Azure AD と PingAccess を連携させることが前提となるため、一部の手順は Ping Identity のサイトでご覧ください。

### <a name="install-an-application-proxy-connector"></a>アプリケーション プロキシ コネクタのインストール

既にアプリケーション プロキシを有効にし、コネクタをインストールしている場合は、このセクションをスキップして「[アプリケーション プロキシを使って Azure AD にアプリを登録する](#add-your-application-to-azure-ad-with-application-proxy)」に進むことができます。

アプリケーション プロキシ コネクタは、遠隔にいる従業員からのトラフィックを発行済みアプリケーションに誘導する Windows Server サービスです。 インストール手順の詳細については、[チュートリアル: Azure Active Directory のアプリケーション プロキシを使用してリモート アクセスするためのオンプレミス アプリケーションを追加する](application-proxy-add-on-premises-application.md)を参照してください。

1. アプリケーション管理者として [Azure Active Directory ポータル](https://aad.portal.azure.com/)にサインインします。 **Azure Active Directory 管理センター**のページが表示されます。
1. **[Azure Active Directory]**  >  **[アプリケーション プロキシ]**  >  **[コネクタ サービスのダウンロード]** の順に選択します。 **[アプリケーション プロキシ コネクタのダウンロード]** ページが表示されます。

   ![アプリケーション プロキシ コネクタのダウンロード](./media/application-proxy-configure-single-sign-on-with-ping-access/application-proxy-connector-download.png)

1. 画面の指示に従ってインストールしてください。

通常、コネクタをダウンロードすれば、ディレクトリに対してアプリケーション プロキシが自動的に有効になります。有効にならなかった場合は、 **[アプリケーション プロキシの有効化]** を選択してください。

### <a name="add-your-application-to-azure-ad-with-application-proxy"></a>アプリケーション プロキシを使って Azure AD にアプリケーションを登録する

Azure ポータルで実行する必要がある操作は 2 つあります。 最初に、アプリケーション プロキシを使用してアプリケーションを発行する必要があります。 次に、アプリケーションに関する情報をいくつか収集する必要があります。その情報が PingAccess の手順で必要になります。

#### <a name="publish-your-application"></a>アプリケーションの発行

最初にアプリケーションを発行する必要があります。 このアクションには、次が含まれます。

- Azure AD へのオンプレミス アプリケーションの追加
- アプリケーションをテストとヘッダー ベースの SSO を選択するためのユーザーの割り当て
- アプリケーションのリダイレクト URL の設定
- ユーザーおよび他のアプリケーションに対するオンプレミス アプリケーションを使用するためのアクセス許可の付与

独自のオンプレミス アプリケーションを発行するには、次の手順に従います。

1. 直前のセクションの手順を省略した場合は、アプリケーション管理者として [Azure Active Directory ポータル](https://aad.portal.azure.com/)にサインインしてください。
1. **[エンタープライズ アプリケーション]**  >  **[新しいアプリケーション]**  >  **[オンプレミスのアプリケーション]** の順に選択します。 **[独自のオンプレミスのアプリケーションの追加]** ページが表示されます。

   ![独自のオンプレミスのアプリケーションの追加](./media/application-proxy-configure-single-sign-on-with-ping-access/add-your-own-on-premises-application.png)
1. 新しいアプリケーションに関する情報を必須フィールドに入力します。 設定には、下のガイダンスを使用します。

   > [!NOTE]
   > この手順の詳細なチュートリアルは、「[オンプレミス アプリを Azure AD に追加する](application-proxy-add-on-premises-application.md#add-an-on-premises-app-to-azure-ad)」を参照してください。

   1. **[内部 URL]** : 通常は、社内ネットワークにおけるアプリのサインイン ページへの URL を指定します。 このシナリオでは、コネクタは PingAccess プロキシをアプリケーションの最初のページとして処理する必要があります。 次の形式を使用します。`https://<host name of your PingAccess server>:<port>` ポートは既定では 3000 ですが、PingAccess で設定できます。

      > [!WARNING]
      > この種類のシングル サインオンの場合、内部 URL は `https` を使用する必要があり、`http` を使用できません。

   1. **事前認証方法**: **[Azure Active Directory]** を選択します。
   1. **ヘッダーの URL を変換する**: **[いいえ]** を選択します。

   > [!NOTE]
   > これが最初のアプリケーションの場合は、ポート 3000 の使用から始めてください。PingAccess 構成を変更した場合は、この設定を更新してください。 後続のアプリケーションの場合、ポートは PingAccess で構成したリスナーと一致する必要があります。 詳細については、[PingAccess のリスナー](https://documentation.pingidentity.com/pingaccess/pa31/index.shtml#Listeners.html)に関するページを参照してください。

1. **[追加]** を選択します。 新しいアプリケーションの概要ページが表示されます。

次に、アプリケーション テストのユーザーを割り当て、ヘッダーベースのシングル サインオンを選択します。

1. アプリケーションのサイドバーから、 **[ユーザーとグループ]**  >  **[ユーザーの追加]**  >  **[ユーザーとグループ] (選択された \<数>)** の順に選択します。 選択するためのユーザーとグループの一覧が表示されます。

   ![ユーザーとグループのリストを示しています](./media/application-proxy-configure-single-sign-on-with-ping-access/users-and-groups.png)

1. アプリケーション テストのユーザーを選択し、 **[選択]** を選択します。 このテスト アカウントでオンプレミスのアプリケーションにアクセスできることを確認します。
1. **[割り当て]** を選択します。
1. アプリケーションのサイドバーから、 **[シングル サインオン]**  >  **[ヘッダーベース]** を選択します。

   > [!TIP]
   > ヘッダー ベースのシングル サインオンを初めて使用する場合は、PingAccess をインストールする必要があります。 Azure サブスクリプションを PingAccess のインストールに自動的に関連付けるには、このシングル サインオン ページのリンクを使用して PingAccess をダウンロードします。 今すぐダウンロード サイトを開くことも、後でこのページに戻ることもできます。

   ![ヘッダー ベースのサインオン画面と PingAccess を示しています](./media/application-proxy-configure-single-sign-on-with-ping-access/sso-header.png)

1. **[保存]** を選択します。

続いてリダイレクト URL が外部 URL に設定されてことを確認しています。

1. **[Azure Active Directory 管理センター]** サイドバーから **[Azure Active Directory]**  >  **[アプリの登録]** の順に選択します。 アプリケーションの一覧を表示します。
1. アプリケーションを選択します。
1. Web およびパブリック クライアントに対して設定されたリダイレクト URI の数を表示した、 **[リダイレクト URI]** の横にあるリンクを選択します。 **[\<アプリケーション名> - 認証]** ページが表示されます。
1. 以前にアプリケーションに割り当てた外部 URL が **[リダイレクト URI]** の一覧にあるかどうかを確認します。 ない場合は、**Web** のリダイレクト URI の種類を使用して外部 URL を追加し、 **[保存]** を選択します。

最後に、ユーザーに読み取りアクセス権を、他のアプリケーションに読み取り/書き込みアクセス権を与えるように、オンプレミス アプリケーションを設定します。

1. アプリケーションの **[アプリの登録]** サイドバーから、 **[API のアクセス許可]**  >  **[アクセス許可の追加]**  >  **[Microsoft API]**  >  **[Microsoft Graph]** の順に選択します。 Windows Azure Active Directory の API を含んだ、**Microsoft Graph** の **[API アクセス許可の要求]** ページが表示されます。

   ![[API アクセス許可の要求] ページを示しています](./media/application-proxy-configure-single-sign-on-with-ping-access/required-permissions.png)

1. **[委任されたアクセス許可]**  >  **[ユーザー]**  >  **[User.Read]** の順に選択します。
1. **[アプリケーションのアクセス許可]**  >  **[アプリケーション]**  >  **[Application.ReadWrite.All]** の順に選択します。
1. **[アクセス許可の追加]** を選択します.
1. **[API のアクセス許可]** ページで、 **[\<ディレクトリ名> に管理者の同意を与えます]** を選択します。

#### <a name="collect-information-for-the-pingaccess-steps"></a>PingAccess の手順で使う情報の収集

PingAccess でアプリケーションを設定するには、次の 3 つの情報 (すべての GUID) を収集する必要があります。

| Azure AD フィールドの名前 | PingAccess フィールドの名前 | データ形式 |
| --- | --- | --- |
| **アプリケーション (クライアント) ID** | **クライアント ID** | GUID |
| **ディレクトリ (テナント) ID** | **発行者** | GUID |
| `PingAccess key` | **クライアント シークレット** | ランダムな文字列 |

この情報を収集します。

1. **[Azure Active Directory 管理センター]** サイドバーから **[Azure Active Directory]**  >  **[アプリの登録]** の順に選択します。 アプリケーションの一覧を表示します。
1. アプリケーションを選択します。 アプリケーションの **[アプリの登録]** ページが表示されます。

   ![アプリケーションの登録の概要](./media/application-proxy-configure-single-sign-on-with-ping-access/registration-overview-for-an-application.png)

1. **[アプリケーション (クライアント) ID]** 値の横で **[クリップボードにコピー]** アイコンを選択し、それをコピーして保存します。 この値は、後から PingAccess のクライアント ID として指定します。
1. **[ディレクトリ (テナント) ID]** 値の横で **[クリップボードにコピー]** も選択し、それをコピーして保存します。 この値は、後から PingAccess の発行者として指定します。
1. アプリケーションの **[アプリの登録]** のサイドバーから、 **[証明書とシークレット]**  >  **[新しいクライアント シークレット]** の順に選択します。 **[クライアント シークレットの追加]** ページが表示されます。

   ![[クライアント シークレットの追加] ページを示しています](./media/application-proxy-configure-single-sign-on-with-ping-access/add-a-client-secret.png)

1. **[説明]** で `PingAccess key` を入力します。
1. **[有効期限]** の下で、PingAccess キーを設定する方法を選択します: **[1 年]** 、 **[2 年]** 、または **[無期限]** 。
1. **[追加]** を選択します。 クライアント シークレットのテーブルに PingAccess キーが、**VALUE** フィールドに自動入力されるランダム文字列で表示されます。
1. PingAccess キーの **VALUE** フィールドの横で **[クリップボードにコピー]** アイコンを選択し、それをコピーして保存します。 この値は、後から PingAccess のクライアント シークレットとして指定します。

### <a name="update-graphapi-to-send-custom-fields-optional"></a>GraphAPI を更新してカスタム フィールドを送信する (省略可能)

PingAccess で消費される access_token 内のその他のトークンを送信するカスタム要求が必要な場合、`acceptMappedClaims` アプリケーション フィールドを `True` に設定します。 Graph エクスプローラーまたは Azure AD ポータルのアプリケーション マニフェストを使用して、この変更を行うことができます。

**次の例では、Graph Explorer を使用しています。**

```
PATCH https://graph.windows.net/myorganization/applications/<object_id_GUID_of_your_application>

{
  "acceptMappedClaims":true
}
```

**次の例では、[Azure Active Directory ポータル](https://aad.portal.azure.com/)を使用して `acceptMappedClaims` フィールドを更新します。**

1. アプリケーション管理者として [Azure Active Directory ポータル](https://aad.portal.azure.com/)にサインインします。
1. **[Azure Active Directory]**  >  **[アプリの登録]** の順に選択します。 アプリケーションの一覧を表示します。
1. アプリケーションを選択します。
1. アプリケーションの **[アプリの登録]** ページのサイドバーから、 **[マニフェスト]** を選択します。 アプリケーションの登録のマニフェスト JSON コードが表示されます。
1. `acceptMappedClaims` フィールドを検索し、値を `True` に変更します。
1. **[保存]** を選択します。

### <a name="use-of-optional-claims-optional"></a>省略可能な要求の使用 (省略可能)

省略可能な要求を使用すると、すべてのユーザーとテナントが持つ、標準だが既定では含まれない要求を追加できます。 アプリケーション マニフェストを変更して、アプリケーションの省略可能な要求を構成することができます。 詳細については、[Azure AD アプリケーション マニフェストの概要に関する記事](https://docs.microsoft.com/azure/active-directory/develop/reference-app-manifest/)を参照してください

PingAccess が消費する access_token に、メール アドレスを含める例:
```
    "optionalClaims": {
        "idToken": [],
        "accessToken": [
            {
                "name": "email",
                "source": null,
                "essential": false,
                "additionalProperties": []
            }
        ],
        "saml2Token": []
    },
```

### <a name="use-of-claims-mapping-policy-optional"></a>要求のマッピング ポリシーの使用 (省略可能)

AzureAD に存在しない属性に対する[要求のマッピング ポリシー (プレビュー)](https://docs.microsoft.com/azure/active-directory/develop/active-directory-claims-mapping#claims-mapping-policy-properties)。 要求のマッピングでは、ADFS またはユーザー オブジェクトでサポートされるカスタム要求を追加することにより、古いオンプレミス アプリをクラウドに移行できます

アプリケーションからカスタム要求を使用し、追加のフィールドを含めるには、[カスタム要求のマップ ポリシーも作成し、そのポリシーをアプリケーションに割り当てる](../develop/active-directory-claims-mapping.md#claims-mapping-policy-assignment)必要があります。

> [!NOTE]
> カスタム要求を使用するには、カスタム ポリシーも定義し、アプリケーションに割り当てる必要があります。 このポリシーには、すべての必要なカスタム属性を含めます。
>
> PowerShell、Azure AD Graph Explorer、または Microsoft Graph から、ポリシーの定義と割り当てを行えます。 PowerShell でこの処理を実行するには、必要に応じてまず `New-AzureADPolicy` を使用してから、`Add-AzureADServicePrincipalPolicy` でアプリケーションに割り当てます。 詳細については、「[要求のマッピング ポリシーの割り当て](../develop/active-directory-claims-mapping.md#claims-mapping-policy-assignment)」を参照してください。

例:
```powershell
$pol = New-AzureADPolicy -Definition @('{"ClaimsMappingPolicy":{"Version":1,"IncludeBasicClaimSet":"true", "ClaimsSchema": [{"Source":"user","ID":"employeeid","JwtClaimType":"employeeid"}]}}') -DisplayName "AdditionalClaims" -Type "ClaimsMappingPolicy"

Add-AzureADServicePrincipalPolicy -Id "<<The object Id of the Enterprise Application you published in the previous step, which requires this claim>>" -RefObjectId $pol.Id
```

### <a name="enable-pingaccess-to-use-custom-claims"></a>PingAccess でカスタム要求を使用できるようにする

PingAccess でカスタム要求を使用できるようにすることは省略可能ですが、アプリケーションで追加の要求を使用すると予想される場合は必須です。

次の手順で PingAccess を構成すると、作成する Web セッション ([設定] > [アクセス] > [Web セッション]) では、 **[Request Profile]** (プロファイルの要求) を選択解除し、 **[Refresh User Attributes]** (ユーザー属性の更新) を **[いいえ]** に設定する必要があります

## <a name="download-pingaccess-and-configure-your-application"></a>PingAccess をダウンロードしてアプリケーションを構成する

これで、Azure Active Directory のすべてのセットアップ手順が完了したので、PingAccess の構成に進むことができます。

このシナリオの PingAccess に関連した領域の詳しい手順については、Ping Identity のドキュメントを参照してください。 Ping Identity の Web サイトの [Microsoft Azure AD アプリケーション プロキシを使用した発行されたアプリケーションを保護するための PingAccess for Azure AD の構成](https://docs.pingidentity.com/bundle/paaad_m_ConfigurePAforMSAzureADSolution_paaad43/page/pa_c_PAAzureSolutionOverview.html)に関するページの手順に従ってください。

これらの手順では、PingAccess をインストールして PingAccess アカウント (まだない場合) を設定できます。 続いて Azure AD OpenID Connect (OIDC) 接続を作成するには、Azure AD ポータルからコピーした **[ディレクトリ (テナント) ID]** 値でトークン プロバイダーを設定します。 次に、PingAccess に Web セッションを作成するために、 **[アプリケーション (クライアント) ID]** および `PingAccess key` の値を使用します。 その後、ID のマッピングをセットアップし、仮想ホスト、サイト、アプリケーションを作成することができます。

### <a name="test-your-application"></a>アプリケーションのテスト

以上の手順がすべて完了すれば、アプリケーションは正常に動作します。 正しく動作するか確認するために、ブラウザーを起動し、Azure にアプリケーションを発行したときに作成した外部 URL にアクセスしてみてください。 サインインには、アプリケーションに割り当てたテスト アカウントを使用します。

## <a name="next-steps"></a>次の手順

- [Microsoft Azure AD アプリケーション プロキシを使用した発行されたアプリケーションを保護するための PingAccess for Azure AD の構成](https://docs.pingidentity.com/bundle/paaad_m_ConfigurePAforMSAzureADSolution_paaad43/page/pa_c_PAAzureSolutionOverview.html)
- [Azure Active Directory でのアプリケーションへのシングル サインオン](what-is-single-sign-on.md)
- [アプリケーション プロキシの問題とエラー メッセージのトラブルシューティング](application-proxy-troubleshoot.md)
