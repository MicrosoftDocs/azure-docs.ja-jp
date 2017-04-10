---
title: "Windows 10 および Windows Server 2016 の Azure AD ドメイン参加済みコンピューターの自動登録に関するトラブルシューティング | Microsoft Docs"
description: "Windows 10 および Windows Server 2016 の Azure AD ドメイン参加済みコンピューターの自動登録に関するトラブルシューティング。"
services: active-directory
documentationcenter: 
author: MarkusVi
manager: femila
ms.assetid: cdc25576-37f2-4afb-a786-f59ba4c284c2
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/07/2017
ms.author: markvi
translationtype: Human Translation
ms.sourcegitcommit: 8a531f70f0d9e173d6ea9fb72b9c997f73c23244
ms.openlocfilehash: 2faf328e6622b9a1e3b529d62b61061659041fbd
ms.lasthandoff: 03/10/2017


---
# <a name="troubleshooting-auto-registration-of-domain-joined-computers-to-azure-ad--windows-10-and-windows-server-2016"></a>Windows 10 および Windows Server 2016 の Azure AD ドメイン参加済みコンピューターの自動登録に関するトラブルシューティング

このトピックは、次のクライアントに適用されます。

-    Windows 10
-    Windows Server 2016

その他の Windows クライアントについては、「[Windows ダウンレベル クライアントの Azure AD ドメイン参加済みコンピューターの自動登録に関するトラブルシューティング](active-directory-device-registration-troubleshoot-windows-legacy.md)」をご覧ください。

このトピックは、次のシナリオをサポートするために、「[Azure Active Directory への Windows ドメイン参加済みデバイスの自動登録の構成方法](active-directory-device-registration-get-started.md)」の説明に従って、ドメイン参加済みデバイスの自動登録を構成済みであることを前提としています。

- [デバイス ベースの条件付きアクセス](active-directory-conditional-access-automatic-device-registration-setup.md)

- [設定のエンタープライズ ローミング](active-directory-windows-enterprise-state-roaming-overview.md)

- [Windows Hello for Business](active-directory-azureadjoin-passport-deployment.md)


このドキュメントでは、考えられる問題の解決方法について、トラブルシューティングのガイダンスを示します。 

登録は、Windows 10 November 2015 Update 以降でサポートされています。  
上記のシナリオに対応する際には、Anniversary Update を使用することをお勧めします。

## <a name="step-1-retrieve-the-registration-status"></a>手順 1: 登録状態を取得する 

**登録状態を取得するには:**

1. 管理者としてコマンド プロンプトを開きます。

2. 「**dsregcmd/status**」と入力します。



    +----------------------------------------------------------------------+
    | Device State                                                         |  +----------------------------------------------------------------------+
    
        AzureAdJoined : YES
     EnterpriseJoined : NO DeviceId : 5820fbe9-60c8-43b0-bb11-44aee233e4e7 Thumbprint : B753A6679CE720451921302CA873794D94C6204A KeyContainerId : bae6a60b-1d2f-4d2a-a298-33385f6d05e9 KeyProvider : Microsoft Platform Crypto Provider TpmProtected : YES KeySignTest: : MUST Run elevated to test.
                  Idp : login.windows.net TenantId : 72b988bf-86f1-41af-91ab-2d7cd011db47 TenantName : Contoso AuthCodeUrl : https://login.microsoftonline.com/msitsupp.microsoft.com/oauth2/authorize AccessTokenUrl : https://login.microsoftonline.com/msitsupp.microsoft.com/oauth2/token MdmUrl : https://enrollment.manage-beta.microsoft.com/EnrollmentServer/Discovery.svc MdmTouUrl : https://portal.manage-beta.microsoft.com/TermsOfUse.aspx dmComplianceUrl : https://portal.manage-beta.microsoft.com/?portalAction=Compliance SettingsUrl : eyJVcmlzIjpbImh0dHBzOi8va2FpbGFuaS5vbmUubWljcm9zb2Z0LmNvbS8iLCJodHRwczovL2thaWxhbmkxLm9uZS5taWNyb3NvZnQuY29tLyJdfQ== JoinSrvVersion : 1.0 JoinSrvUrl : https://enterpriseregistration.windows.net/EnrollmentServer/device/ JoinSrvId : urn:ms-drs:enterpriseregistration.windows.net KeySrvVersion : 1.0 KeySrvUrl : https://enterpriseregistration.windows.net/EnrollmentServer/key/ KeySrvId : urn:ms-drs:enterpriseregistration.windows.net DomainJoined : YES DomainName : CONTOSO
    
    +----------------------------------------------------------------------+
    | User State                                                           |  +----------------------------------------------------------------------+
    
                 NgcSet : YES
               NgcKeyId : {C7A9AEDC-780E-4FDA-B200-1AE15561A46B}
        WorkplaceJoined : NO
          WamDefaultSet : YES
    WamDefaultAuthority : organizations         WamDefaultId : https://login.microsoft.com       WamDefaultGUID : {B16898C6-A148-4967-9171-64D755DA8520} (AzureAd)           AzureAdPrt : YES



## <a name="step-2-evaluate-the-registration-status"></a>手順 2: 登録状態を評価する 

以下のフィールドを確認し、必要な値が設定されていることを確認します。

### <a name="azureadjoined--yes"></a>AzureAdJoined : YES  

このフィールドは、デバイスが Azure AD に登録されているかどうかを示します。 値が "NO" の場合、登録は完了していません。 

**考えられる原因**

- 登録の際にコンピューターの認証に失敗した。

- コンピューターで検出できない HTTP プロキシが組織に存在する。

- コンピューターが認証する際に Azure AD に到達できないか、登録する際に Azure DRS に到達できない。

- コンピューターが、組織の内部ネットワーク上、またはオンプレミスの AD ドメイン コントローラーが認識できる VPN 上に存在しない可能性がある。

- コンピューターに TPM が存在する場合、TPM が正常な状態ではない可能性がある。

- サービスが正しく構成されていない可能性がある。この場合、前述のドキュメントで構成をもう一度確認する必要があります。 一般的な例を次に示します。

    - フェデレーション サーバーで WS-Trust エンドポイントが有効になっていない。

    - フェデレーション サーバーで、統合 Windows 認証を使用したネットワーク内のコンピューターからの受信認証が許可されていない可能性がある。

    - Azure AD のコンピューターが属する AD フォレスト内の検証済みのドメイン名を参照するサービス接続ポイント オブジェクトがない。

---

### <a name="domainjoined--yes"></a>DomainJoined : YES  

このフィールドは、デバイスがオンプレミス Active Directory に参加しているかどうかを示します。 値が **NO** の場合、デバイスを Azure AD に自動登録することはできません。 デバイスを Azure AD に登録するには、デバイスがオンプレミス Active Directory に参加していることを確認しておく必要があります。 コンピューターを Azure AD に直接参加させる場合は、Azure Active Directory Join の機能に関する記事をご覧ください。

---

### <a name="workplacejoined--no"></a>WorkplaceJoined : NO  

このフィールドは、デバイスが ("社内参加済み" としてマークされた) 個人所有のデバイスとして Azure AD に登録されているかどうかを示します。 Azure AD に登録されたドメイン参加済みコンピューターのこの値が "NO" である必要があるときに、"YES" になっている場合、コンピューターで登録が完了する前に、職場または学校アカウントが追加されています。 この場合、Windows 10 の Anniversary Update バージョン ([ファイル名を指定して実行] ウィンドウまたはコマンド プロンプト ウィンドウで WinVer コマンドを実行したときは 1607) を使用していると、アカウントは無視されます。

---

### <a name="wamdefaultset--yes-and-azureadprt--yes"></a>WamDefaultSet : YES および AzureADPrt : YES
  
これらのフィールドは、ユーザーがデバイスへのサインイン時に Azure AD に対して正常に認証されたことを示します。 これらの値が "NO" の場合、考えられる原因は次のとおりです。

- 登録時にデバイスに関連付けられた TPM のストレージ キー (STK) に問題がある (管理者特権での実行時に KeySignTest を確認)

- 代替ログイン ID

- HTTP プロキシが見つからない

## <a name="next-steps"></a>次のステップ

詳細については、「[デバイスの自動登録に関する FAQ](active-directory-device-registration-faq.md)」をご覧ください。 
