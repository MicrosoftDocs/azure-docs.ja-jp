---
title: Windows 10 と Windows Server 2016 のハイブリッド Azure Active Directory 参加済みデバイスのトラブルシューティング | Microsoft Docs
description: Windows 10 と Windows Server 2016 のハイブリッド Azure Active Directory 参加済みデバイスのトラブルシューティング。
services: active-directory
ms.service: active-directory
ms.subservice: devices
ms.topic: troubleshooting
ms.date: 06/28/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: jairoc
ms.collection: M365-identity-device-management
ms.openlocfilehash: dfb4b03fb57efecff587a91dfc2ad293be96d9ba
ms.sourcegitcommit: 9b80d1e560b02f74d2237489fa1c6eb7eca5ee10
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/01/2019
ms.locfileid: "67481602"
---
# <a name="troubleshooting-hybrid-azure-active-directory-joined-windows-10-and-windows-server-2016-devices"></a>Windows 10 と Windows Server 2016 のハイブリッド Azure Active Directory 参加済みデバイスのトラブルシューティング 

この記事は、次のクライアントに適用されます。

-   Windows 10
-   Windows Server 2016

その他の Windows クライアントについては、「[ハイブリッド Azure Active Directory 参加済みダウンレベル デバイスのトラブルシューティング](troubleshoot-hybrid-join-windows-legacy.md)」を参照してください。

この記事は、[ハイブリッド Azure Active Directory 参加済みデバイスの構成](hybrid-azuread-join-plan.md)が済んでいて、次のシナリオに対応していることが前提となります。

- デバイスベースの条件付きアクセス
- [設定のエンタープライズ ローミング](../active-directory-windows-enterprise-state-roaming-overview.md)
- [Windows Hello for Business](../active-directory-azureadjoin-passport-deployment.md)

このドキュメントでは、考えられる問題の解決方法について、トラブルシューティングのガイダンスを示します。 

Windows 10 および Windows Server 2016 でハイブリッド Azure Active Directory 参加がサポートされるのは、Windows 10 November 2015 Update 以降となります。 Anniversary Update の使用をお勧めします。

## <a name="step-1-retrieve-the-join-status"></a>手順 1:参加状態を取得する 

**参加状態を取得するには:**

1. 管理者としてコマンド プロンプトを開きます。

2. 「**dsregcmd/status**」と入力します。

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
         TenantId: 72b988bf-86f1-41af-91ab-2d7cd011db47
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

## <a name="step-2-evaluate-the-join-status"></a>手順 2:参加状態を評価する 

以下のフィールドを確認し、必要な値が設定されていることを確認します。

### <a name="azureadjoined--yes"></a>AzureAdJoined :はい  

このフィールドは、デバイスが Azure AD に参加しているかどうかを示します。 この値が **NO** である場合、Azure AD への参加は済んでいません。 

**考えられる原因**

- 参加させるコンピューターの認証に失敗した。
- コンピューターで検出できない HTTP プロキシが組織に存在する。
- コンピューターが認証する際に Azure AD に到達できないか、登録する際に Azure DRS に到達できない。
- コンピューターが、組織の内部ネットワーク上、またはオンプレミスの AD ドメイン コントローラーが認識できる VPN 上に存在しない可能性がある。
- コンピューターに TPM が存在する場合、TPM が正常な状態ではない可能性がある。
- サービスが正しく構成されていない可能性がある。この場合、前述のドキュメントで構成をもう一度確認する必要があります。 一般的な例を次に示します。
   - フェデレーション サーバーで WS-Trust エンドポイントが有効になっていない。
   - フェデレーション サーバーで、統合 Windows 認証を使用したネットワーク内のコンピューターからの受信認証が許可されていない。
   - Azure AD のコンピューターが属する AD フォレスト内の検証済みのドメイン名を参照するサービス接続ポイント オブジェクトがない。

---

### <a name="domainjoined--yes"></a>DomainJoined :はい  

このフィールドは、デバイスがオンプレミス Active Directory に参加しているかどうかを示します。 この値が **NO** である場合、デバイスはハイブリッド Azure AD 参加を実行できません。  

---

### <a name="workplacejoined--no"></a>WorkplaceJoined :NO  

このフィールドは、デバイスが ("*ワークプレースに参加済み*" としてマークされた) 個人所有のデバイスとして Azure AD に登録されているかどうかを示します。 ドメインに参加していて、なおかつハイブリッド Azure AD 参加済みのコンピューターでは、この値は **NO** になります。 この値が **YES** である場合、ハイブリッド Azure AD 参加が完了する前に、職場または学校アカウントが追加されています。 このケースで Anniversary Update バージョンの Windows 10 (1607) を使用すると、そのアカウントは無視されます。

---

### <a name="wamdefaultset--yes-and-azureadprt--yes"></a>WamDefaultSet :YES and AzureADPrt :はい
  
これらのフィールドは、ユーザーがデバイスへのサインイン時に Azure AD に対して正常に認証されたことを示します。 これらの値が **NO** である場合、次のことが原因として考えられます。

- 登録時にデバイスに関連付けられた TPM のストレージ キー (STK) に問題がある (管理者特権での実行時に KeySignTest を確認)
- 代替ログイン ID
- HTTP プロキシが見つからない

## <a name="next-steps"></a>次の手順

ご不明な点がある場合は、[デバイス管理の FAQ](faq.md) をご覧ください。 
