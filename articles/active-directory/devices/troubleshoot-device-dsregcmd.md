---
title: dsregcmd コマンドを使用してトラブルシューティングする - Azure Active Directory
description: Azure AD 内のデバイスの状態を把握するための dsregcmd からの出力の使用
services: active-directory
ms.service: active-directory
ms.subservice: devices
ms.topic: troubleshooting
ms.date: 11/21/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: spunukol
ms.collection: M365-identity-device-management
ms.openlocfilehash: 614b3f927dc22bc534168f8fe95cc2f97031b621
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "98725525"
---
# <a name="troubleshooting-devices-using-the-dsregcmd-command"></a>dsregcmd コマンドを使用したデバイスのトラブルシューティング

dsregcmd /status ユーティリティは、ドメイン ユーザー アカウントとして実行する必要があります。

## <a name="device-state"></a>デバイスの状態

このセクションには、デバイスの参加状態のパラメーターが一覧に示されます。 次の表は、デバイスがさまざまな参加状態になる条件を示しています。

| AzureAdJoined | EnterpriseJoined | DomainJoined | デバイスの状態 |
| ---   | ---   | ---   | ---   |
| YES | NO | NO | Azure AD 参加済み |
| NO | NO | YES | ドメイン参加済み |
| YES | NO | YES | ハイブリッド AD 参加済み |
| NO | YES | YES | オンプレミス DRS 参加済み |

> [!NOTE]
> Workplace Join (Azure AD 登録済み) 状態は、[User State]\(ユーザー状態\) セクションに表示されます

- **AzureAdJoined:** -デバイスが Azure AD に参加している場合は、"YES" に設定されます。 それ以外の場合は "NO" です。
- **Enterprisejoined:** - デバイスがオンプレミスの DRS に参加している場合は "YES" に設定されます。 デバイスに EnterpriseJoined と AzureAdJoined の両方を指定することはできません。
- **DomainJoined:** -デバイスがドメイン (AD) に参加している場合は、"YES" に設定されます。
- **DomainName:** -デバイスがドメインに参加している場合は、ドメインの名前に設定されます。

### <a name="sample-device-state-output"></a>デバイスの状態の出力例

```
+----------------------------------------------------------------------+
| Device State                                                         |
+----------------------------------------------------------------------+
             AzureAdJoined : YES
          EnterpriseJoined : NO
              DomainJoined : YES
                DomainName : HYBRIDADFS
+----------------------------------------------------------------------+
```

## <a name="device-details"></a>[デバイスの詳細]

デバイスが Azure AD 参加済みまたはハイブリッド Azure AD 参加済み (Azure AD に未登録) の場合にのみ表示されます。 このセクションには、クラウドに格納されている詳細を識別するデバイスの一覧が示されます。

- **DeviceId:** -Azure AD テナント内のデバイスの一意の ID
- **Thumbprint:** -デバイス証明書の拇印 
- **DeviceCertificateValidity:** -デバイス証明書の有効性
- **KeyContainerId:** -デバイス証明書に関連付けられているデバイスの秘密キーの ContainerId
- **Keyprovider:** -デバイスの秘密キーを格納するために使用される KeyProvider (ハードウェア/ソフトウェア)。
- **TpmProtected:** - デバイスの秘密キーがハードウェア TPM に格納されている場合は "YES"。

### <a name="sample-device-details-output"></a>デバイスの詳細の出力例

```
+----------------------------------------------------------------------+
| Device Details                                                       |
+----------------------------------------------------------------------+

                  DeviceId : e92325d0-xxxx-xxxx-xxxx-94ae875dxxxx
                Thumbprint : D293213EF327483560EED8410CAE36BB67208179
 DeviceCertificateValidity : [ 2019-01-11 21:02:50.000 UTC -- 2029-01-11 21:32:50.000 UTC ]
            KeyContainerId : 13e68a58-xxxx-xxxx-xxxx-a20a2411xxxx
               KeyProvider : Microsoft Software Key Storage Provider
              TpmProtected : NO
+----------------------------------------------------------------------+
```

## <a name="tenant-details"></a>Tenant details (テナントの詳細)

デバイスが Azure AD 参加済みまたはハイブリッド Azure AD 参加済み (Azure AD に未登録) の場合にのみ表示されます。 このセクションには、デバイスが Azure AD に参加している場合の一般的なテナントの詳細が示されます。

> [!NOTE]
> このセクションの MDM URL が空である場合は、MDM が構成されなかったか、または現在のユーザーが MDM 登録の範囲内にないことを示します。 Azure AD のモビリティ設定をチェックして MDM の構成を確認してください。

> [!NOTE]
> MDM の URL が表示されている場合でも、デバイスが MDM によって管理されているわけではありません。 この情報は、デバイス自体が管理されていない場合でも、テナントが自動登録のための MDM 構成を持っている場合に表示されます。 

### <a name="sample-tenant-details-output"></a>テナントの詳細の出力例

```
+----------------------------------------------------------------------+
| Tenant Details                                                       |
+----------------------------------------------------------------------+

                TenantName : HybridADFS
                  TenantId : 96fa76d0-xxxx-xxxx-xxxx-eb60cc22xxxx
                       Idp : login.windows.net
               AuthCodeUrl : https://login.microsoftonline.com/96fa76d0-xxxx-xxxx-xxxx-eb60cc22xxxx/oauth2/authorize
            AccessTokenUrl : https://login.microsoftonline.com/96fa76d0-xxxx-xxxx-xxxx-eb60cc22xxxx/oauth2/token
                    MdmUrl : https://enrollment.manage-beta.microsoft.com/EnrollmentServer/Discovery.svc
                 MdmTouUrl : https://portal.manage-beta.microsoft.com/TermsOfUse.aspx
          MdmComplianceUrl : https://portal.manage-beta.microsoft.com/?portalAction=Compliance
               SettingsUrl : eyJVxxxxIjpbImh0dHBzOi8va2FpbGFuaS5vbmUubWljcm9zb2Z0LmNvbS8iLCJodHRwczovL2thaWxhbmkxLm9uZS5taWNyb3NvZnQuY29tLyxxxx==
            JoinSrvVersion : 1.0
                JoinSrvUrl : https://enterpriseregistration.windows.net/EnrollmentServer/device/
                 JoinSrvId : urn:ms-drs:enterpriseregistration.windows.net
             KeySrvVersion : 1.0
                 KeySrvUrl : https://enterpriseregistration.windows.net/EnrollmentServer/key/
                  KeySrvId : urn:ms-drs:enterpriseregistration.windows.net
        WebAuthNSrvVersion : 1.0
            WebAuthNSrvUrl : https://enterpriseregistration.windows.net/webauthn/96fa76d0-xxxx-xxxx-xxxx-eb60cc22xxxx/
             WebAuthNSrvId : urn:ms-drs:enterpriseregistration.windows.net
    DeviceManagementSrvVer : 1.0
    DeviceManagementSrvUrl : https://enterpriseregistration.windows.net/manage/96fa76d0-xxxx-xxxx-xxxx-eb60cc22xxxx/
     DeviceManagementSrvId : urn:ms-drs:enterpriseregistration.windows.net
+----------------------------------------------------------------------+
```

## <a name="user-state"></a>ユーザー状態

このセクションには、現在デバイスにログインしているユーザーのさまざまな属性の状態が示されます。

> [!NOTE]
> 有効な状態を取得するには、コマンドをユーザー コンテキストで実行する必要があります。

- **NgcSet:** - 現在ログオンしているユーザーに対して Windows Hello キーが設定されている場合は、"YES" に設定されます。
- **NgcKeyId:** - Windows Hello キーの ID (現在ログオンしているユーザーに対して設定されている場合)。
- **CanReset:** - ユーザーが Windows Hello キーをリセットできるかどうかを示します。 
- **使用可能な値:** - DestructiveOnly、NonDestructiveOnly、DestructiveAndNonDestructive、またはエラーの場合は Unknown。 
- **WorkplaceJoined:** - Azure AD の登録済みアカウントが現在の NTUSER コンテキストでデバイスに追加された場合は、"YES" に設定されます。
- **WamDefaultSet:** - ログインしているユーザーに対して WAM の既定の WebAccount が作成されている場合は "YES" に設定されます。 このフィールドでは、dsreg/status が管理者特権でのコマンド プロンプトから実行されている場合にエラーが表示されることがあります。 
- **WamDefaultAuthority:** - Azure AD の場合は "organizations" に設定されます。
- **WamDefaultId:** - Azure AD の場合は常に "https://login.microsoft.com" です。
- **WamDefaultGUID:** - 既定の WAM WebAccount の WAM プロバイダーの (Azure AD/Microsoft アカウント) GUID。 

### <a name="sample-user-state-output"></a>ユーザーの状態の出力例

```
+----------------------------------------------------------------------+
| User State                                                           |
+----------------------------------------------------------------------+

                    NgcSet : YES
                  NgcKeyId : {FA0DB076-A5D7-4844-82D8-50A2FB42EC7B}
                  CanReset : DestructiveAndNonDestructive
           WorkplaceJoined : NO
             WamDefaultSet : YES
       WamDefaultAuthority : organizations
              WamDefaultId : https://login.microsoft.com
            WamDefaultGUID : { B16898C6-A148-4967-9171-64D755DA8520 } (AzureAd)

+----------------------------------------------------------------------+
```

## <a name="sso-state"></a>SSO state (SSO 状態)

Azure AD 登録済みデバイスについては、このセクションを無視してかまいません。

> [!NOTE]
> ユーザーの有効な状態を取得するには、コマンドをユーザー コンテキストで実行する必要があります。

- **AzureAdPrt:** - ログオン ユーザーのデバイスに PRT が存在する場合は、"YES" に設定されます。
- **AzureAdPrtUpdateTime:** - PRT が最後に更新されたときの UTC 時刻に設定されます。
- **AzureAdPrtExpiryTime:** - 更新されない場合に PRT が期限切れになる UTC 時刻に設定されます。
- **AzureAdPrtAuthority:** - Azure AD 機関の URL
- **EnterprisePrt:** - オンプレミス ADFS からの PRT がデバイスにある場合は、"YES" に設定されます。 ハイブリッド Azure AD 参加済みデバイスの場合、デバイスには Azure AD とオンプレミスの AD からの PRT を同時に指定できます。 オンプレミスの参加済みデバイスでは、エンタープライズ PRT のみ指定されます。
- **EnterprisePrtUpdateTime:** - エンタープライズ PRT が最後に更新されたときの UTC 時刻に設定されます。
- **EnterprisePrtExpiryTime:** - 更新されない場合に PRT が期限切れになる UTC 時刻に設定されます。
- **EnterprisePrtAuthority:** - ADFS 機関 URL

### <a name="sample-sso-state-output"></a>SSO 状態の出力例

```
+----------------------------------------------------------------------+
| SSO State                                                            |
+----------------------------------------------------------------------+

                AzureAdPrt : YES
      AzureAdPrtUpdateTime : 2019-01-24 19:15:26.000 UTC
      AzureAdPrtExpiryTime : 2019-02-07 19:15:26.000 UTC
       AzureAdPrtAuthority : https://login.microsoftonline.com/96fa76d0-xxxx-xxxx-xxxx-eb60cc22xxxx
             EnterprisePrt : YES
   EnterprisePrtUpdateTime : 2019-01-24 19:15:33.000 UTC
   EnterprisePrtExpiryTime : 2019-02-07 19:15:33.000 UTC
    EnterprisePrtAuthority : https://fs.hybridadfs.nttest.microsoft.com:443/adfs

+----------------------------------------------------------------------+
```

## <a name="diagnostic-data"></a>診断データ

### <a name="pre-join-diagnostics"></a>参加前の診断

このセクションは、デバイスがドメイン参加済みで、ハイブリッド Azure AD 参加ができない場合にのみ表示されます。

このセクションでは、参加エラーの診断に役立つさまざまなテストを実行します。 このセクションには、前の (?) の詳細も含まれています。 この情報には、エラー フェーズ、エラー コード、サーバー要求 ID、サーバー応答の http 状態、サーバー応答のエラー メッセージが含まれます。

- **User Context:** - 診断が実行されるコンテキスト。 指定できる値SYSTEM、UN-ELEVATED User、ELEVATED User。 

   > [!NOTE]
   > 実際の参加は SYSTEM コンテキストで実行されるため、SYSTEM コンテキストでの診断の実行が、実際の参加シナリオに最も近いものになります。 SYSTEM コンテキストで診断を実行するには、管理者特権でのコマンド プロンプトから dsregcmd /status コマンドを実行する必要があります。

- **Client Time:** - システムの UTC 時刻。
- **AD Connectivity Test:** - ドメイン コントローラーへの接続テストを実行します。 このテストでエラーが発生すると、事前チェック フェーズで参加エラーが発生する可能性があります。
- **AD Configuration Test:** - テストでは、SCP オブジェクトがオンプレミスの AD フォレストで適切に構成されているかどうかを読み取って確認します。 このテストでエラーが発生すると、検出フェーズでエラー コード0x801c001d で参加エラーが発生する可能性があります。
- **DRS Discovery Test:** - テストでは、検出メタデータ エンドポイントから DRS エンドポイントを取得し、ユーザー領域要求を実行します。 このテストでエラーが発生すると、検出フェーズで参加エラーが発生する可能性があります。
- **DRS Connectivity Test:** - DRS エンドポイントへの基本接続テストを実行します。
- **Token acquisition Test:** -ユーザー テナントがフェデレーションされている場合、テストでは Azure AD 認証トークンの取得が試行されます。 このテストでエラーが発生すると、認証フェーズで参加エラーが発生する可能性があります。 認証に失敗した場合、下のレジストリ キー設定でフォールバックが明示的に無効になっていない限り、フォールバックとして同期参加が試行されます。
```
    Keyname: Computer\HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows\CurrentVersion\CDJ
    Value: FallbackToSyncJoin
    Type:  REG_DWORD
    Value: 0x0 -> Disabled
    Value: 0x1 -> Enabled
    Default (No Key): Enabled
 ```
- **Fallback to Sync-Join:** - 上のレジストリ キー (認証失敗で同期参加にフォールバックすることを避けるためのもの) が存在しない場合、"Enabled" に設定されます。 このオプションは、Windows 10 1803 以降で使用できます。
- **Previous Registration:** - 前回の参加試行が発生した時刻。 失敗した参加試行だけがログに記録されます。
- **Error Phase:** - 中止された参加のステージ。 可能値は、pre-check、discover、auth、join です。
- **Client ErrorCode:** - 返されたクライアント エラー コード (HRESULT)。
- **Server ErrorCode:** - サーバーに要求が送信され、サーバーがエラー コードを返して応答した場合のサーバー エラー コード。 
- **Server Message:** - エラーコードと共に返されたサーバー メッセージ。
- **Https Status:** - サーバーから返された HTTP の状態。
- **Request ID:** - サーバーに送信されるクライアントの requestId。 サーバー側のログと相関させるのに役立ちます。

### <a name="sample-pre-join-diagnostics-output"></a>参加前の診断の出力例

次の例は、検出エラーで失敗した診断テストを示しています。

```
+----------------------------------------------------------------------+
| Diagnostic Data                                                      |
+----------------------------------------------------------------------+

     Diagnostics Reference : www.microsoft.com/aadjerrors
              User Context : SYSTEM
               Client Time : 2019-01-31 09:25:31.000 UTC
      AD Connectivity Test : PASS
     AD Configuration Test : PASS
        DRS Discovery Test : FAIL [0x801c0021/0x801c000c]
     DRS Connectivity Test : SKIPPED
    Token acquisition Test : SKIPPED
     Fallback to Sync-Join : ENABLED

     Previous Registration : 2019-01-31 09:23:30.000 UTC
               Error Phase : discover
          Client ErrorCode : 0x801c0021

+----------------------------------------------------------------------+
```

次の例では、診断テストは成功していますが、同期参加に予期される登録の試行がディレクトリ エラーで失敗しています。 Azure AD Connect の同期ジョブが完了すると、デバイスを参加させることができます。

```
+----------------------------------------------------------------------+
| Diagnostic Data                                                      |
+----------------------------------------------------------------------+

     Diagnostics Reference : www.microsoft.com/aadjerrors
              User Context : SYSTEM
               Client Time : 2019-01-31 09:16:50.000 UTC
      AD Connectivity Test : PASS
     AD Configuration Test : PASS
        DRS Discovery Test : PASS
     DRS Connectivity Test : PASS
    Token acquisition Test : PASS
     Fallback to Sync-Join : ENABLED

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

### <a name="post-join-diagnostics"></a>参加後の診断

このセクションには、クラウドに参加しているデバイスで実行されたサニティ チェックの出力が表示されます。

- **AadRecoveryEnabled:** - "YES" の場合、デバイスに格納されているキーは使用できず、デバイスは復旧対象としてマークされます。 次回のサインインでは復旧フローがトリガーされ、デバイスが再登録されます。
- **KeySignTest:** - "PASSED" の場合、デバイス キーは正常な状態です。 KeySignTest が失敗した場合、デバイスは通常、復旧対象としてマークされます。 次回のサインインでは復旧フローがトリガーされ、デバイスが再登録されます。 ハイブリッド Azure AD 参加済みデバイスの場合、復旧はサイレントです。 Azure AD 参加済みまたは Azure AD 登録済みの場合、デバイスは必要に応じてデバイスの復旧と再登録のためにユーザー認証を要求します。 **KeySignTest には、昇格された特権が必要です。**

#### <a name="sample-post-join-diagnostics-output"></a>参加後の診断の出力例

```
+----------------------------------------------------------------------+
| Diagnostic Data                                                      |
+----------------------------------------------------------------------+

         AadRecoveryEnabled: NO
               KeySignTest : PASSED
+----------------------------------------------------------------------+
```

## <a name="ngc-prerequisite-check"></a>NGC の前提条件チェック

このセクションでは、Windows Hello for Business (WHFB) をプロビジョニングするための前提条件チェックを実行します。 

> [!NOTE]
> ユーザーが既に WHFB を適切に構成している場合は、dsregcmd /status で NGC の前提条件チェックの詳細が表示されないことがあります。

- **IsDeviceJoined:** - デバイスが Azure AD に参加している場合は、"YES" に設定されます。
- **IsUserAzureAD:** - ログインしているユーザーが Azure AD に存在する場合は、"YES" に設定されます。
- **PolicyEnabled:** - デバイスで WHFB ポリシーが有効になっている場合は "YES" に設定されます。
- **PostLogonEnabled:** - WHFB 登録がプラットフォームによってネイティブでトリガーされる場合は、"YES" に設定されます。 "NO" に設定されている場合は、Windows Hello for Business の登録がカスタム メカニズムによってトリガーされることを示します。
- **DeviceEligible:** - デバイスが WHFB に登録するためのハードウェア要件を満たしている場合は、"YES" に設定されます。
- **SessionIsNotRemote:** - 現在のユーザーがリモートではなくデバイスに直接ログインしている場合は、"YES" に設定されます。
- **CertEnrollment:** - WHFB 証明書信頼展開に固有であり、WHFB の証明書登録機関を示します。 WHFB ポリシーのソースがグループ ポリシーの場合は "enrollment authority" に設定され、ソースが MDM の場合は "mobile device management" に設定されます。 それ以外の場合は "none" になります。
- **AdfsRefreshToken:** - WHFB 証明書信頼展開固有です。 CertEnrollment が "enrollment authority" の場合にのみ存在します。 ユーザー用のエンタープライズ PRT がデバイスにあるかどうかを示します。
- **AdfsRaIsReady:** - WHFB 証明書信頼展開固有です。  CertEnrollment が "enrollment authority" の場合にのみ存在します。 探索メタデータに示されている ADFS で WHFB がサポートされ、"*かつ*" ログオン証明書テンプレートを利用できる場合は "YES" に設定されます。
- **LogonCertTemplateReady:** - WHFB 証明書信頼展開固有です。 CertEnrollment が "enrollment authority" の場合にのみ存在します。 ログオン証明書テンプレートの状態が有効であり、ADFS RA のトラブルシューティングに役立つ場合は、"YES" に設定されます。
- **PreReqResult:** - すべての WHFB の前提条件評価の結果が提示されます。 ユーザーが次回サインインするときに、ログオン後のタスクとして WHFB 登録を起動する場合は、"Will Provision" に設定されます。

### <a name="sample-ngc-prerequisite-check-output"></a>NGC の前提条件チェックの出力例

```
+----------------------------------------------------------------------+
| Ngc Prerequisite Check                                               |
+----------------------------------------------------------------------+

            IsDeviceJoined : YES
             IsUserAzureAD : YES
             PolicyEnabled : YES
          PostLogonEnabled : YES
            DeviceEligible : YES
        SessionIsNotRemote : YES
            CertEnrollment : enrollment authority
          AdfsRefreshToken : YES
             AdfsRaIsReady : YES
    LogonCertTemplateReady : YES ( StateReady )
              PreReqResult : WillProvision
+----------------------------------------------------------------------+
```

## <a name="next-steps"></a>次のステップ

ご不明な点がある場合は、[デバイス管理の FAQ](faq.md) をご覧ください。
