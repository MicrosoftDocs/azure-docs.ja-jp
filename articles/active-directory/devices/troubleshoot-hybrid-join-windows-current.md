---
title: ハイブリッド Azure Active Directory 参加済みデバイスのトラブルシューティング
description: Windows 10 と Windows Server 2016 のハイブリッド Azure Active Directory 参加済みデバイスのトラブルシューティング。
services: active-directory
ms.service: active-directory
ms.subservice: devices
ms.topic: troubleshooting
ms.date: 11/21/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: jairoc
ms.collection: M365-identity-device-management
ms.custom: has-adal-ref
ms.openlocfilehash: 1eb4194354a07d5b580f07cfe5962785a4e100bd
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "104578024"
---
# <a name="troubleshooting-hybrid-azure-active-directory-joined-devices"></a>ハイブリッド Azure Active Directory 参加済みデバイスのトラブルシューティング

この記事の内容は、Windows 10 または Windows Server 2016 を実行しているデバイスに適用されます。

その他の Windows クライアントについては、「[ハイブリッド Azure Active Directory 参加済みダウンレベル デバイスのトラブルシューティング](troubleshoot-hybrid-join-windows-legacy.md)」の記事を参照してください。

この記事は、[ハイブリッド Azure Active Directory 参加済みデバイスの構成](hybrid-azuread-join-plan.md)が済んでいて、次のシナリオに対応していることが前提となります。

- デバイスベースの条件付きアクセス
- [設定のエンタープライズ ローミング](./enterprise-state-roaming-overview.md)
- [Windows Hello for Business](/windows/security/identity-protection/hello-for-business/hello-identity-verification)

このドキュメントでは、潜在的な問題を解決するためのトラブルシューティング ガイダンスを示します。

Windows 10 および Windows Server 2016 でハイブリッド Azure Active Directory 参加がサポートされるのは、Windows 10 November 2015 Update 以降となります。

## <a name="troubleshoot-join-failures"></a>参加エラーのトラブルシューティング

### <a name="step-1-retrieve-the-join-status"></a>手順 1:参加状態を取得する

**参加状態を取得するには:**

1. 管理者としてコマンド プロンプトを開きます。
2. 「`dsregcmd /status`」と入力します。

```
+----------------------------------------------------------------------+
| Device State                                                         |
+----------------------------------------------------------------------+

    AzureAdJoined: YES
 EnterpriseJoined: NO
         DeviceId: 5820fbe9-60c8-43b0-bb11-44aee233e4e7
       Thumbprint: B753A6679CE720451921302CA873794D94C6204A
   KeyContainerId: bae6a60b-1d2f-4d2a-a298-33385f6d05e9
      KeyProvider: Microsoft Platform Crypto Provider
     TpmProtected: YES
     KeySignTest: : MUST Run elevated to test.
              Idp: login.windows.net
         TenantId: 72b988bf-xxxx-xxxx-xxxx-2d7cd011xxxx
       TenantName: Contoso
      AuthCodeUrl: https://login.microsoftonline.com/msitsupp.microsoft.com/oauth2/authorize
   AccessTokenUrl: https://login.microsoftonline.com/msitsupp.microsoft.com/oauth2/token
           MdmUrl: https://enrollment.manage-beta.microsoft.com/EnrollmentServer/Discovery.svc
        MdmTouUrl: https://portal.manage-beta.microsoft.com/TermsOfUse.aspx
  dmComplianceUrl: https://portal.manage-beta.microsoft.com/?portalAction=Compliance
      SettingsUrl: eyJVcmlzIjpbImh0dHBzOi8va2FpbGFuaS5vbmUubWljcm9zb2Z0LmNvbS8iLCJodHRwczovL2thaWxhbmkxLm9uZS5taWNyb3NvZnQuY29tLyJdfQ==
   JoinSrvVersion: 1.0
       JoinSrvUrl: https://enterpriseregistration.windows.net/EnrollmentServer/device/
        JoinSrvId: urn:ms-drs:enterpriseregistration.windows.net
    KeySrvVersion: 1.0
        KeySrvUrl: https://enterpriseregistration.windows.net/EnrollmentServer/key/
         KeySrvId: urn:ms-drs:enterpriseregistration.windows.net
     DomainJoined: YES
       DomainName: CONTOSO

+----------------------------------------------------------------------+
| User State                                                           |
+----------------------------------------------------------------------+

             NgcSet: YES
           NgcKeyId: {C7A9AEDC-780E-4FDA-B200-1AE15561A46B}
    WorkplaceJoined: NO
      WamDefaultSet: YES
WamDefaultAuthority: organizations
       WamDefaultId: https://login.microsoft.com
     WamDefaultGUID: {B16898C6-A148-4967-9171-64D755DA8520} (AzureAd)
         AzureAdPrt: YES
```

### <a name="step-2-evaluate-the-join-status"></a>手順 2:参加状態を評価する

以下のフィールドを確認し、必要な値が設定されていることを確認します。

#### <a name="domainjoined--yes"></a>DomainJoined :YES

このフィールドは、デバイスがオンプレミス Active Directory に参加しているかどうかを示します。 この値が **NO** である場合、デバイスはハイブリッド Azure AD 参加を実行できません。

#### <a name="workplacejoined--no"></a>WorkplaceJoined :NO

このフィールドは、デバイスが ("*ワークプレースに参加済み*" としてマークされた) 個人所有のデバイスとして Azure AD に登録されているかどうかを示します。 ドメインに参加していて、なおかつハイブリッド Azure AD 参加済みのコンピューターでは、この値は **NO** になります。 この値が **YES** である場合、ハイブリッド Azure AD 参加が完了する前に、職場または学校アカウントが追加されています。 この場合、Windows 10 バージョン 1607 以降を使用しているときは、このアカウントは無視されます。

#### <a name="azureadjoined--yes"></a>AzureAdJoined :YES

このフィールドは、デバイスが参加しているかどうかを示します。 デバイスが Azure AD 参加済みデバイスであるか、ハイブリッド Azure AD 参加済みデバイスである場合、この値は **YES** になります。
この値が **NO** である場合、Azure AD への参加は済んでいません。

詳細なトラブルシューティングを行うには、次の手順に進みます。

### <a name="step-3-find-the-phase-in-which-join-failed-and-the-errorcode"></a>手順 3:参加がエラーとなったフェーズとエラー コードを確認する

#### <a name="windows-10-1803-and-above"></a>Windows 10 1803 以降

参加状態の出力の [Diagnostic Data] セクションで [Previous Registration] サブセクションを探します。 このセクションは、デバイスがドメイン参加済みで、ハイブリッド Azure AD 参加ができない場合にのみ表示されます。
[Error Phase] フィールドは参加エラーのフェーズを表し、[Client ErrorCode] は参加操作のエラー コードを表します。

```
+----------------------------------------------------------------------+
     Previous Registration : 2019-01-31 09:16:43.000 UTC
         Registration Type : sync
               Error Phase : join
          Client ErrorCode : 0x801c03f2
          Server ErrorCode : DirectoryError
            Server Message : The device object by the given id (e92325d0-xxxx-xxxx-xxxx-94ae875d5245) is not found.
              Https Status : 400
                Request Id : 6bff0bd9-820b-484b-ab20-2a4f7b76c58e
+----------------------------------------------------------------------+
```

#### <a name="older-windows-10-versions"></a>Windows 10 の古いバージョン

イベント ビューアーのログで、参加エラーのフェーズとエラー コードを確認します。

1. イベント ビューアーで **[User Device Registration]\(ユーザー デバイス登録\)** イベントのログを開きます。 **[Applications and Services Log]\(アプリケーションとサービスのログ\)**  >  **[Microsoft]**  >  **[Windows]**  >  **[User Device Registration]\(ユーザー デバイス登録\)** の下にあります。
2. イベント ID が 304、305、307 のイベントを探します。

:::image type="content" source="./media/troubleshoot-hybrid-join-windows-current/1.png" alt-text="イベント ビューアーのスクリーンショット。ID 304 のイベントが選択され、その情報が表示され、エラー コードとフェーズが強調表示されている。" border="false":::

:::image type="content" source="./media/troubleshoot-hybrid-join-windows-current/2.png" alt-text="イベント ビューアーのスクリーンショット。ID 305 のイベントが表示され、その情報が表示され、エラー コードが強調表示されている。" border="false":::

### <a name="step-4-check-for-possible-causes-and-resolutions-from-the-lists-below"></a>手順 4:以下の一覧から考えられる原因と解決策を確認する

#### <a name="pre-check-phase"></a>事前チェック フェーズ

考えられるエラーの原因:

- デバイスがドメイン コントローラーを認識していない。
   - デバイスは、組織の内部ネットワークに配置するか、オンプレミスの Active Directory (AD) ドメイン コントローラーをネットワークで認識する VPN に配置する必要があります。

#### <a name="discover-phase"></a>検出フェーズ

考えられるエラーの原因:

- サービス接続ポイント (SCP) オブジェクトが正しく構成されていない、または DC から SCP オブジェクトを読み取ることができません。
   - デバイスが属している AD フォレストに、Azure AD 内の検証済みドメイン名を指す有効な SCP オブジェクトが必要です。
   - 詳細については、[サービス接続ポイントの構成](hybrid-azuread-join-federated-domains.md#configure-hybrid-azure-ad-join)に関するセクションを参照してください。
- 検出エンドポイントから検出メタデータに接続してフェッチできません。
   - 登録と承認のエンドポイントを検出するには、デバイスがシステム コンテキストで `https://enterpriseregistration.windows.net` にアクセスできる必要があります。
   - オンプレミス環境に送信プロキシが必要である場合は、デバイスのコンピューター アカウントが送信プロキシに対する検出とサイレント認証を実行できることを IT 管理者が確認する必要があります。
- ユーザー領域エンドポイントに接続して領域の検出を実行できません。 (Windows 10 バージョン 1809 以降のみ)
   - 検証済みドメインの領域検出を実行してドメインの種類 (管理対象/フェデレーション) を判別するには、デバイスがシステム コンテキストで `https://login.microsoftonline.com` にアクセスできる必要があります。
   - オンプレミス環境に送信プロキシが必要である場合は、デバイスのシステム コンテキストで送信プロキシに対する検出とサイレント認証を実行できることを IT 管理者が確認する必要があります。

**一般的なエラー コード:**

- **DSREG_AUTOJOIN_ADCONFIG_READ_FAILED** (0x801c001d/-2145648611)
   - 理由:SCP オブジェクトを読み取れず、Azure AD テナント情報を取得できません。
   - 解決策:[サービス接続ポイントの構成](hybrid-azuread-join-federated-domains.md#configure-hybrid-azure-ad-join)に関するセクションを参照してください。
- **DSREG_AUTOJOIN_DISC_FAILED** (0x801c0021/-2145648607)
   - 理由:一般的な検出エラー。 DRS から検出メタデータを取得できませんでした。
   - 解決策:この後のサブエラーを確認して詳細を調べます。
- **DSREG_AUTOJOIN_DISC_WAIT_TIMEOUT** (0x801c001f/-2145648609)
   - 理由:検出の実行中に操作がタイムアウトになりました。
   - 解決策:システム コンテキストで `https://enterpriseregistration.windows.net` にアクセスできることを確認します。 詳細については、[ネットワーク接続の要件](hybrid-azuread-join-managed-domains.md#prerequisites)に関するセクションを参照してください。
- **DSREG_AUTOJOIN_USERREALM_DISCOVERY_FAILED** (0x801c0021/-2145648611)
   - 理由:一般的な領域検出エラー。 STS からドメインの種類 (管理対象/フェデレーション) を判別できませんでした。
   - 解決策:この後のサブエラーを確認して詳細を調べます。

**一般的なサブエラー コード:**

検出エラー コードのサブエラー コードを確認するには、次のいずれかの方法を使用します。

##### <a name="windows-10-1803-and-above"></a>Windows 10 1803 以降

参加状態の出力の [Diagnostic Data] セクションで [DRS Discovery Test] を探します。 このセクションは、デバイスがドメイン参加済みで、ハイブリッド Azure AD 参加ができない場合にのみ表示されます。

```
+----------------------------------------------------------------------+
| Diagnostic Data                                                      |
+----------------------------------------------------------------------+

     Diagnostics Reference : www.microsoft.com/aadjerrors
              User Context : UN-ELEVATED User
               Client Time : 2019-06-05 08:25:29.000 UTC
      AD Connectivity Test : PASS
     AD Configuration Test : PASS
        DRS Discovery Test : FAIL [0x801c0021/0x80072ee2]
     DRS Connectivity Test : SKIPPED
    Token acquisition Test : SKIPPED
     Fallback to Sync-Join : ENABLED

+----------------------------------------------------------------------+
```

##### <a name="older-windows-10-versions"></a>Windows 10 の古いバージョン

イベント ビューアーのログで、参加エラーのフェーズとエラー コードを確認します。

1. イベント ビューアーで **[User Device Registration]\(ユーザー デバイス登録\)** イベントのログを開きます。 **[Applications and Services Log]\(アプリケーションとサービスのログ\)**  >  **[Microsoft]**  >  **[Windows]**  >  **[User Device Registration]\(ユーザー デバイス登録\)** の下にあります。
2. イベント ID が 201 のイベントを探します。

:::image type="content" source="./media/troubleshoot-hybrid-join-windows-current/5.png" alt-text="イベント ビューア―のスクリーンショット。ID 201 のイベントが選択され、その情報が表示され、エラー コードが強調表示されている。" border="false":::

###### <a name="network-errors"></a>ネットワーク エラー

- **WININET_E_CANNOT_CONNECT** (0x80072efd/-2147012867)
   - 理由:サーバーとの接続を確立できませんでした。
   - 解決策:必要な Microsoft リソースとのネットワーク接続を確認します。 詳細については、[ネットワーク接続の要件](hybrid-azuread-join-managed-domains.md#prerequisites)に関するページを参照してください。
- **WININET_E_TIMEOUT** (0x80072ee2/-2147012894)
   - 理由:一般的なネットワーク タイムアウト。
   - 解決策:必要な Microsoft リソースとのネットワーク接続を確認します。 詳細については、[ネットワーク接続の要件](hybrid-azuread-join-managed-domains.md#prerequisites)に関するページを参照してください。
- **WININET_E_DECODING_FAILED** (0x80072f8f/-2147012721)
   - 理由:ネットワーク スタックがサーバーからの応答をデコードできませんでした。
   - 解決策:ネットワーク プロキシがサーバーの応答を妨害したり変更したりしていないことを確認します。

###### <a name="http-errors"></a>HTTP エラー

- **DSREG_DISCOVERY_TENANT_NOT_FOUND** (0x801c003a/-2145648582)
   - 理由:SCP オブジェクトが間違ったテナント ID で構成されています。 あるいは、テナントにアクティブなサブスクリプションが見つかりませんでした。
   - 解決策:SCP オブジェクトが正しい Azure AD テナント ID で構成されていることと、アクティブなサブスクリプションがテナントに存在することを確認します。
- **DSREG_SERVER_BUSY** (0x801c0025/-2145648603)
   - 理由:DRS サーバーからの HTTP 503。
   - 解決策:サーバーを現在使用できません。 サーバーがオンラインに戻れば、今後の参加試行が成功する可能性があります。

###### <a name="other-errors"></a>その他のエラー

- **E_INVALIDDATA** (0x8007000d/-2147024883)
   - 理由:サーバー応答 JSON を解析できませんでした。 HTML 認証ページでプロキシが HTTP 200 を返したことが原因と考えられます。
   - 解決策:オンプレミス環境に送信プロキシが必要である場合は、デバイスのシステム コンテキストで送信プロキシに対する検出とサイレント認証を実行できることを IT 管理者が確認する必要があります。

#### <a name="authentication-phase"></a>認証フェーズ

フェデレーション ドメイン アカウントにのみ適用されます。

エラーの原因:

- DRS リソースのアクセス トークンをサイレントに取得できません。
   - Windows 10 デバイスは、アクティブな WS-Trust エンドポイントに対する統合 Windows 認証を使用して、フェデレーション サービスから認証トークンを取得します。 詳細:[フェデレーション サービスの構成](hybrid-azuread-join-manual.md#set-up-issuance-of-claims)

**一般的なエラー コード:**

イベント ビューアーのログで、エラー コード、サブエラー コード、サーバー エラー コード、およびサーバー エラー メッセージを確認します。

1. イベント ビューアーで **[User Device Registration]\(ユーザー デバイス登録\)** イベントのログを開きます。 **[Applications and Services Log]\(アプリケーションとサービスのログ\)**  >  **[Microsoft]**  >  **[Windows]**  >  **[User Device Registration]\(ユーザー デバイス登録\)** の下にあります。
2. イベント ID が 305 のイベントを探します。

:::image type="content" source="./media/troubleshoot-hybrid-join-windows-current/3.png" alt-text="イベント ビューア―のスクリーンショット。ID 305 のイベントが表示されている。その情報が表示され、ADAL エラー コードと状態が強調表示されている。" border="false":::

##### <a name="configuration-errors"></a>構成エラー

- **ERROR_ADAL_PROTOCOL_NOT_SUPPORTED** (0xcaa90017/-894894057)
   - 理由:認証プロトコルが WS-Trust ではありません。
   - 解決策:オンプレミスの ID プロバイダーが WS-Trust をサポートしている必要があります。
- **ERROR_ADAL_FAILED_TO_PARSE_XML** (0xcaa9002c/-894894036)
   - 理由:オンプレミスのフェデレーション サービスから XML 応答が返されませんでした。
   - 解決策:MEX エンドポイントから有効な XML が返されることを確認します。 プロキシが妨害を行ったり XML 以外の応答を返したりしていないことを確認します。
- **ERROR_ADAL_COULDNOT_DISCOVER_USERNAME_PASSWORD_ENDPOINT** (0xcaa90023/-894894045)
   - 理由:ユーザー名/パスワード認証のエンドポイントを検出できませんでした。
   - 解決策:オンプレミスの ID プロバイダーの設定を確認します。 WS-Trust エンドポイントが有効になっていることを確認し、MEX 応答にこれらの正しいエンドポイントが含まれていることを確認します。

##### <a name="network-errors"></a>ネットワーク エラー

- **ERROR_ADAL_INTERNET_TIMEOUT** (0xcaa82ee2/-894947614)
   - 理由:一般的なネットワーク タイムアウト。
   - 解決策:システム コンテキストで `https://login.microsoftonline.com` にアクセスできることを確認します。 システム コンテキストでオンプレミスの ID プロバイダーにアクセスできることを確認します。 詳細については、[ネットワーク接続の要件](hybrid-azuread-join-managed-domains.md#prerequisites)に関するページを参照してください。
- **ERROR_ADAL_INTERNET_CONNECTION_ABORTED** (0xcaa82efe/-894947586)
   - 理由:認証エンドポイントとの接続が中止されました。
   - 解決策:しばらくしてからやり直すか、または別の安定したネットワークの場所から参加を試みます。
- **ERROR_ADAL_INTERNET_SECURE_FAILURE** (0xcaa82f8f/-894947441)
   - 理由:以前は SSL (Secure Sockets Layer) として知られていた、サーバーによって送信されたトランスポート層セキュリティ (TLS) 証明書を検証できませんでした。
   - 解決策:クライアント時間のずれを確認します。 しばらくしてからやり直すか、または別の安定したネットワークの場所から参加を試みます。
- **ERROR_ADAL_INTERNET_CANNOT_CONNECT** (0xcaa82efd/-894947587)
   - 理由:`https://login.microsoftonline.com` への接続に失敗しました。
   - 解決策:`https://login.microsoftonline.com` とのネットワーク接続を確認します。

##### <a name="other-errors"></a>その他のエラー

- **ERROR_ADAL_SERVER_ERROR_INVALID_GRANT** (0xcaa20003/-895352829)
   - 理由:オンプレミスの ID プロバイダーからの SAML トークンが Azure AD で受け付けられませんでした。
   - 解決策:フェデレーション サーバーの設定を確認します。 認証ログでサーバー エラー コードを探します。
- **ERROR_ADAL_WSTRUST_REQUEST_SECURITYTOKEN_FAILED** (0xcaa90014/-894894060)
   - 理由:サーバーの WS-Trust 応答でエラー例外が報告され、アサーションの取得に失敗しました。
   - 解決策:フェデレーション サーバーの設定を確認します。 認証ログでサーバー エラー コードを探します。
- **ERROR_ADAL_WSTRUST_TOKEN_REQUEST_FAIL** (0xcaa90006/-894894074)
   - 理由:トークン エンドポイントからアクセス トークンを取得するときにエラーが発生しました。
   - 解決策:ADAL ログで根本的なエラーを探します。
- **ERROR_ADAL_OPERATION_PENDING** (0xcaa1002d/-895418323)
   - 理由:一般的な ADAL エラー
   - 解決策:認証ログで、サブエラー コードまたはサーバー エラー コードを探します。

#### <a name="join-phase"></a>参加フェーズ

エラーの原因:

登録の種類を確認し、以下の一覧でエラー コードを探します。

#### <a name="windows-10-1803-and-above"></a>Windows 10 1803 以降

参加状態の出力の [Diagnostic Data] セクションで [Previous Registration] サブセクションを探します。 このセクションは、デバイスがドメイン参加済みで、ハイブリッド Azure AD 参加ができない場合にのみ表示されます。
[Registration Type] フィールドは、実行された参加の種類を表します。

```
+----------------------------------------------------------------------+
     Previous Registration : 2019-01-31 09:16:43.000 UTC
         Registration Type : sync
               Error Phase : join
          Client ErrorCode : 0x801c03f2
          Server ErrorCode : DirectoryError
            Server Message : The device object by the given id (e92325d0-7ac4-4714-88a1-94ae875d5245) is not found.
              Https Status : 400
                Request Id : 6bff0bd9-820b-484b-ab20-2a4f7b76c58e
+----------------------------------------------------------------------+
```

#### <a name="older-windows-10-versions"></a>Windows 10 の古いバージョン

イベント ビューアーのログで、参加エラーのフェーズとエラー コードを確認します。

1. イベント ビューアーで **[User Device Registration]\(ユーザー デバイス登録\)** イベントのログを開きます。 **[Applications and Services Log]\(アプリケーションとサービスのログ\)**  >  **[Microsoft]**  >  **[Windows]**  >  **[User Device Registration]\(ユーザー デバイス登録\)** の下にあります。
2. イベント ID が 204 のイベントを探します。

:::image type="content" source="./media/troubleshoot-hybrid-join-windows-current/4.png" alt-text="イベント ビューア―のスクリーンショット。ID 204 のイベントに関する情報が表示され、エラー コード、HTTP 状態、およびメッセージが強調表示されている。" border="false":::

##### <a name="http-errors-returned-from-drs-server"></a>DRS サーバーから返される HTTP エラー

- **DSREG_E_DIRECTORY_FAILURE** (0x801c03f2/-2145647630)
   - 理由:DRS から次のエラー コードのエラー応答を受け取りました。"DirectoryError"
   - 解決策:考えられる原因と解決策については、サーバー エラー コードを参照してください。
- **DSREG_E_DEVICE_AUTHENTICATION_ERROR** (0x801c0002/-2145648638)
   - 理由:DRS から次のエラー コードのエラー応答を受け取りました。"AuthenticationError" (エラー サブコードは "DeviceNotFound" 以外)
   - 解決策:考えられる原因と解決策については、サーバー エラー コードを参照してください。
- **DSREG_E_DEVICE_INTERNALSERVICE_ERROR** (0x801c0006/-2145648634)
   - 理由:DRS から次のエラー コードのエラー応答を受け取りました。"DirectoryError"
   - 解決策:考えられる原因と解決策については、サーバー エラー コードを参照してください。

##### <a name="tpm-errors"></a>TPM エラー

- **NTE_BAD_KEYSET** (0x80090016/-2146893802)
   - 理由:TPM 操作が失敗したか、または無効でした。
   - 解決策:無効な sysprep イメージが原因と考えられます。 sysprep イメージの作成元であるマシンが、Azure AD に参加していない、ハイブリッド Azure AD に参加していない、あるいは Azure AD に登録されていないことを確認します。
- **TPM_E_PCP_INTERNAL_ERROR** (0x80290407/-2144795641)
   - 理由:一般的な TPM エラー。
   - 解決策:このエラーが発生したデバイスで TPM を無効にします。 Windows 10 バージョン1809 以降では、TPM エラーが自動的に検知され、TPM を使用することなくハイブリッド Azure AD への参加が完了します。
- **TPM_E_NOTFIPS** (0x80280036/-2144862154)
   - 理由:FIPS モードの TPM は現在サポートされていません。
   - 解決策:このエラーが発生したデバイスで TPM を無効にします。 Windows 1809 では、TPM エラーが自動的に検知され、TPM を使用することなくハイブリッド Azure AD への参加が完了します。
- **NTE_AUTHENTICATION_IGNORED** (0x80090031/-2146893775)
   - 理由:TPM がロックアウトされました。
   - 解決策:一時的なエラーです。 クールダウン時間が終わるまで待ちます。 しばらくしてから参加を試みると成功します。 詳細については、[TPM の基礎](/windows/security/information-protection/tpm/tpm-fundamentals#anti-hammering)に関する記事を参照してください。

##### <a name="network-errors"></a>ネットワーク エラー

- **WININET_E_TIMEOUT** (0x80072ee2/-2147012894)
   - 理由:DRS でデバイスを登録するときの一般的なネットワーク タイムアウトです。
   - 解決策:`https://enterpriseregistration.windows.net` とのネットワーク接続を確認します。
- **WININET_E_NAME_NOT_RESOLVED** (0x80072ee7/-2147012889)
   - 理由:サーバーの名前またはアドレスを解決できませんでした。
   - 解決策:`https://enterpriseregistration.windows.net` とのネットワーク接続を確認します。 ネットワーク内およびデバイスでのホスト名の DNS 解決が正確であることを確認します。
- **WININET_E_CONNECTION_ABORTED** (0x80072efe/-2147012866)
   - 理由:サーバーとの接続が異常終了しました。
   - 解決策:しばらくしてからやり直すか、または別の安定したネットワークの場所から参加を試みます。

##### <a name="federated-join-server-errors"></a>サーバーのフェデレーション参加エラー

| サーバー エラー コード | サーバー エラー メッセージ | 考えられる原因 | 解像度 |
| --- | --- | --- | --- |
| DirectoryError | Your request is throttled temporarily.\(要求は一時的に調整されました。\) Please try after 300 seconds.\(300 秒後にもう一度お試しください。\) | 予期されるエラーです。 複数の登録要求が立て続けに行われたことが原因と考えられます。 | クールダウン時間が終わったら参加を再試行してください。 |

##### <a name="sync-join-server-errors"></a>サーバーの同期参加エラー

| サーバー エラー コード | サーバー エラー メッセージ | 考えられる原因 | 解像度 |
| --- | --- | --- | --- |
| DirectoryError | AADSTS90002:Tenant <UUID> not found.\(テナントが見つかりません\)。 This error may happen if there are no active subscriptions for the tenant.\(このエラーは、テナントにアクティブなサブスクリプションがない場合に発生することがあります。\) Check with your subscription administrator.\(サブスクリプション管理者にご確認ください。\) | SCP オブジェクト内のテナント ID が正しくありません。 | SCP オブジェクトが正しい Azure AD テナント ID で構成されていることと、アクティブなサブスクリプションがテナントに存在することを確認します。 |
| DirectoryError | The device object by the given ID is not found.\(指定された ID のデバイス オブジェクトが見つかりませんでした。\) | 同期参加で予期されるエラーです。 デバイス オブジェクトが AD から Azure AD に同期されていません。 | Azure AD Connect の同期が完了するのを待ち、同期完了後に次の参加を試みると問題が解決します。 |
| AuthenticationError | The verification of the target computer's SID\(ターゲット コンピューターの SID の検証\) | Azure AD デバイスの証明書が、同期参加時に BLOB に署名するために使用された証明書と一致しません。 通常このエラーは、同期がまだ完了していないことを示します。 |  Azure AD Connect の同期が完了するのを待ち、同期完了後に次の参加を試みると問題が解決します。 |

### <a name="step-5-collect-logs-and-contact-microsoft-support"></a>手順 5:ログを収集して Microsoft サポートに問い合わせる

[https://github.com/CSS-Identity/DRS/tree/main/Auth](https://github.com/CSS-Identity/DRS/tree/main/Auth) から Auth.zip ファイルをダウンロードします。

1. ファイルを解凍し、インクルード ファイルの **start-auth.txt** と **stop-auth.txt** という名前を **start-auth.cmd** と **stop-auth.cmd** に変更します。
1. 管理者特権でのコマンド プロンプトから、**start-auth.cmd** を実行します。
1. アカウントの切り替えを使用して、問題のあるユーザーとの別のセッションに切り替えます。
1. 問題を再現します。
1. アカウントの切り替えを使用して、トレースを実行している管理者セッションに戻ります。
1. 管理者特権でのコマンド プロンプトから、**stop-auth.cmd** を実行します。
1. スクリプトが実行されたフォルダーから **Authlogs** フォルダーを Zip 形式で送信します。

## <a name="troubleshoot-post-join-issues"></a>参加後の問題のトラブルシューティング

### <a name="retrieve-the-join-status"></a>参加状態を取得する

#### <a name="wamdefaultset-yes-and-azureadprt-yes"></a>WamDefaultSet:YES および AzureADPrt:YES

これらのフィールドは、ユーザーがデバイスへのサインイン時に Azure AD に対して正常に認証されたことを示します。
これらの値が **NO** である場合、次のことが原因として考えられます。

- 登録時にデバイスに関連する TPM のストレージ キーに問題がある (管理者特権での実行時に KeySignTest を確認)
- 代替ログイン ID
- HTTP プロキシが見つからない

## <a name="known-issues"></a>既知の問題
- [設定] -> [アカウント] -> [職場または学校にアクセスする] で、Hybrid Azure AD 参加済みデバイスには、モバイル ホットスポットまたは外部 WiFi ネットワークに接続されているときに、Azure AD 用に 1 つとオンプレミス AD 用に 1 つという 2 つの異なるアカウントが表示される場合があります。 これは UI のみの問題であり、機能には影響しません。

## <a name="next-steps"></a>次のステップ

[dsregcmd コマンドを使用したデバイスのトラブルシューティング](troubleshoot-device-dsregcmd.md)に進みます。

ご不明な点がある場合は、[デバイス管理の FAQ](faq.md) をご覧ください。
