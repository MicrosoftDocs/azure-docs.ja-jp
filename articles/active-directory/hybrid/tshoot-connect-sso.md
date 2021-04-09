---
title: 'Azure Active Directory Connect: シームレス シングル サインオンのトラブルシューティングを行う | Microsoft Docs'
description: このトピックでは、Azure Active Directory シームレス シングル サインオンのトラブルシューティング方法について説明します
services: active-directory
author: billmath
ms.reviewer: swkrish
manager: daveba
ms.assetid: 9f994aca-6088-40f5-b2cc-c753a4f41da7
ms.service: active-directory
ms.workload: identity
ms.topic: troubleshooting
ms.date: 10/07/2019
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: eef58f6e84fb3b4dec947fa3614b6ec1043ff89e
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "101644648"
---
# <a name="troubleshoot-azure-active-directory-seamless-single-sign-on"></a>Azure Active Directory シームレス シングル サインオンのトラブルシューティングを行う

この記事は、Azure Active Directory (Azure AD) シームレス シングル サインオン (シームレス SSO) に関する一般的な問題のトラブルシューティング情報を見つけるうえで役立ちます。

## <a name="known-issues"></a>既知の問題

- 場合によっては、シームレス SSO の有効化に最大 30 分かかることがあります。
- テナントでシームレス SSO を無効にして再度有効にすると、キャッシュされた Kerberos チケットが期限切れになるまで (通常は 10 時間有効)、シングル サインオン機能は利用できません。
- シームレス SSO が成功すると、ユーザーは **[サインインしたままにする]** を選択できません。 この動作が原因で、[SharePoint および OneDrive のマッピングのシナリオ](https://support.microsoft.com/help/2616712/how-to-configure-and-to-troubleshoot-mapped-network-drives-that-connec)は機能しません。
- バージョン 16.0.8730.xxxx 以降の Microsoft 365 Win32 クライアント (Outlook、Word、Excel など) は、非対話型フローを使用してサポートされています。 その他のバージョンはサポートされていません。それらのバージョンでは、ユーザーはパスワードではなく、ユーザー名を入力してサインインします。 OneDrive の場合、サイレント サインオン エクスペリエンス用の [OneDrive サイレント構成機能](https://techcommunity.microsoft.com/t5/Microsoft-OneDrive-Blog/Previews-for-Silent-Sync-Account-Configuration-and-Bandwidth/ba-p/120894)をアクティブにする必要があります。
- シームレス SSO は、Firefox のプライベート ブラウズ モードでは動作しません。
- シームレス SSO は、拡張保護モードがオンの場合は Internet Explorer で動作しません。
- シームレス SSO は、Microsoft Edge (レガシー) のプライベート ブラウズ モードでは動作しません。
- シームレス SSO は、iOS および Android 上のモバイル ブラウザーでは動作しません。
- Active Directory でユーザーが属しているグループ数が多すぎる場合、ユーザーの Kerberos チケットが大きすぎて処理できなくなり、シームレス SSO が失敗する可能性があります。 Azure AD HTTPS 要求のヘッダーは最大サイズが 50 KB です。Cookie など、他の Azure AD アーティファクト (通常、2 から 5 KB) に対応するには、Kerberos チケットのサイズを、制限より小さくする必要があります。 ユーザーのグループ メンバーシップを減らし、再試行することをお勧めします。
- 30 以上の Active Directory フォレストを同期している場合は、Azure AD Connect によるシームレス SSO を有効にすることはできません。 この問題を回避するには、テナントでこの機能を[手動で有効](#manual-reset-of-the-feature)にします。
- Azure AD サービスの URL (`https://autologon.microsoftazuread-sso.com`) を、ローカル イントラネット ゾーンではなく信頼済みサイト ゾーンに追加すると、*ユーザーのサインインがブロック* されます。
- シームレス SSO では、Kerberos の AES256_HMAC_SHA1、AES128_HMAC_SHA1、および RC4_HMAC_MD5 暗号化の種類がサポートされます。 AzureADSSOAcc$ アカウントの暗号化の種類を AES256_HMAC_SHA1 に設定するか、AES タイプまたはRC4 のいずれかに設定してセキュリティを強化することをお勧めします。 暗号化の種類は、Active Directory 内のアカウントの属性の msDS-SupportedEncryptionTypes 属性に格納されます。  AzureADSSOAcc$ アカウントの暗号化の種類が RC4_HMAC_MD5 に設定されていて、それを AES 暗号化の種類のいずれかに変更する場合は、[FAQ ドキュメント](how-to-connect-sso-faq.md)の関連する質問に説明されているように、まず AzureADSSOAcc$ アカウントの Kerberos 復号化キーをロールオーバーするようにしてください。そうしないと、シームレス SSO は行われません。
-  フォレストの信頼関係があるフォレストが複数ある場合、いずれかのフォレストで SSO を有効にすると、すべての信頼されたフォレストで SSO が有効になります。 SSO が既に有効になっているフォレストで SSO を有効にすると、フォレストで SSO が既に有効になっているというエラーが表示されます。

## <a name="check-status-of-feature"></a>機能の状態の確認

テナントでシームレス SSO 機能が引き続き **[有効]** になっていることを確認します。 機能の状態は、[Azure Active Directory 管理センター](https://aad.portal.azure.com/)の **[Azure AD Connect]** ウィンドウで確認できます。

![Azure Active Directory 管理センター: [Azure AD Connect] ウィンドウ](./media/tshoot-connect-sso/sso10.png)

クリックすると、シームレス SSO が有効になっているすべての AD フォレストが表示されます。

![Azure Active Directory 管理センター: シームレス SSO のウィンドウ](./media/tshoot-connect-sso/sso13.png)

## <a name="sign-in-failure-reasons-in-the-azure-active-directory-admin-center-needs-a-premium-license"></a>Azure Active Directory 管理センターでのサインイン失敗の理由 (Premium ライセンスが必要)

テナントに Azure AD Premium ライセンスが関連付けられている場合は、[Azure Active Directory 管理センター](https://aad.portal.azure.com/)で[サインイン アクティビティ レポート](../reports-monitoring/concept-sign-ins.md)を参照することもできます。

![Azure Active Directory 管理センター: サインイン レポート](./media/tshoot-connect-sso/sso9.png)

[Azure Active Directory 管理センター](https://aad.portal.azure.com/)で **[Azure Active Directory]**  >  **[サインイン]** に移動し、特定のユーザーのサインイン アクティビティを選択します。 **[サインインのエラー コード]** フィールドを探します。 次の表を使用して、そのフィールドの値を、失敗の理由と解決策にマップします。

|サインイン エラー コード|サインインが失敗した理由|解像度
| --- | --- | ---
| 81001 | ユーザーの Kerberos チケットが大きすぎます。 | ユーザーのグループ メンバーシップを減らしてやり直してください。
| 81002 | ユーザーの Kerberos チケットを検証できません。 | 「[トラブルシューティングのチェックリスト](#troubleshooting-checklist)」を参照してください。
| 81003 | ユーザーの Kerberos チケットを検証できません。 | 「[トラブルシューティングのチェックリスト](#troubleshooting-checklist)」を参照してください。
| 81004 | Kerberos 認証を試みましたが失敗しました。 | 「[トラブルシューティングのチェックリスト](#troubleshooting-checklist)」を参照してください。
| 81008 | ユーザーの Kerberos チケットを検証できません。 | 「[トラブルシューティングのチェックリスト](#troubleshooting-checklist)」を参照してください。
| 81009 | ユーザーの Kerberos チケットを検証できません。 | 「[トラブルシューティングのチェックリスト](#troubleshooting-checklist)」を参照してください。
| 81010 | シームレス SSO に失敗しました。ユーザーの Kerberos チケットが期限切れか無効です。 | ユーザーは、企業ネットワーク内のドメインに参加しているデバイスからサインインする必要があります。
| 81011 | ユーザーの Kerberos チケット内の情報では、ユーザー オブジェクトが見つかりません。 | Azure AD Connect を使用してユーザー情報を Azure AD と同期します。
| 81012 | Azure AD にサインインしようとしているユーザーは、デバイスにサインインしているユーザーと異なります。 | ユーザーは別のデバイスからサインインする必要があります。
| 81013 | ユーザーの Kerberos チケット内の情報では、ユーザー オブジェクトが見つかりません。 |Azure AD Connect を使用してユーザー情報を Azure AD と同期します。 

## <a name="troubleshooting-checklist"></a>トラブルシューティングのチェックリスト

シームレス SSO の問題のトラブルシューティングを行うには、次のチェックリストを使用します。

- Azure AD Connect でシームレス SSO 機能が有効になっていることを確認します。 (ポートのブロックなどが原因で) この機能を有効にできない場合は、すべての[前提条件](how-to-connect-sso-quick-start.md#step-1-check-the-prerequisites)が満たされていることを確認します。
- [Azure AD Join](../devices/overview.md) とシームレス SSO の両方をテナントで有効にしている場合は、Azure AD Join で問題が発生していないことを確認してください。 デバイスが Azure AD に登録され、ドメインに参加している場合は、Azure AD Join の SSO がシームレス SSO よりも優先されます。 Azure AD の SSO を使用している場合、"Windows に接続済み" というサインイン タイルが表示されます。
- Azure AD の URL (`https://autologon.microsoftazuread-sso.com`) が、ユーザーのイントラネット ゾーンの設定に含まれていることを確認します。
- 会社のデバイスが Active Directory ドメインに参加していることを確認します。 シームレス SSO が機能するために、デバイスが [Azure AD 参加済み](../devices/overview.md)である必要は _ありません_。
- ユーザーが Active Directory ドメイン アカウントでデバイスにログオンしていることを確認します。
- ユーザーのアカウントが、シームレス SSO が設定されている Active Directory フォレストからのものであることを確認します。
- デバイスが企業ネットワークに接続されていることを確認します。
- デバイスの時刻が、Active Directory とドメイン コントローラーの両方の時刻と同期されており、時刻のずれが 5 分以内であることを確認します。
- シームレス SSO を有効にする各 AD フォレストで `AZUREADSSOACC` コンピューター アカウントが存在し、有効になっていることを確認します。 コンピューター アカウントが削除されているか、ない場合は、[PowerShell コマンドレット](#manual-reset-of-the-feature)を使用して再度作成することができます。
- コマンド プロンプトから `klist` コマンド使用して、デバイス上の既存の Kerberos チケットを一覧表示します。 `AZUREADSSOACC` コンピューター アカウントに対して発行されたチケットが存在することを確認します。 ユーザーの Kerberos チケットは、通常は 10 時間有効です。 Active Directory で別の設定が行われていることがあります。
- テナントでシームレス SSO を無効にして再度有効にすると、キャッシュされた Kerberos チケットが期限切れになるまでシングル サインオン機能は利用できません。
- `klist purge` コマンドを使用してデバイスから既存の Kerberos チケットを消去し、やり直します。
- JavaScript に関連する問題があるかどうかを確認するために、ブラウザーのコンソール ログ ( **[開発者ツール]** の下) を確認します。
- [ドメイン コントローラーのログ](#domain-controller-logs)を確認します。

### <a name="domain-controller-logs"></a>ドメイン コントローラーのログ

ドメイン コントローラーで成功の監査を有効にすると、ユーザーがシームレス SSO でサインインするたびに、セキュリティ エントリがイベント ログに記録されます。 こうしたセキュリティ イベントは、次のクエリを使用して検索できます (コンピューター アカウント **AzureADSSOAcc$** に関連付けられているイベント **4769** を探します)。

```
  <QueryList>
    <Query Id="0" Path="Security">
      <Select Path="Security">*[EventData[Data[@Name='ServiceName'] and (Data='AZUREADSSOACC$')]]</Select>
    </Query>
  </QueryList>
```

## <a name="manual-reset-of-the-feature"></a>機能の手動リセット

トラブルシューティングを行っても改善しなかった場合は、テナントでシームレス SSO 機能を手動でリセットできます。 Azure AD Connect が実行されているオンプレミス サーバーで次の手順を実行します。

### <a name="step-1-import-the-seamless-sso-powershell-module"></a>手順 1:Seamless SSO PowerShell モジュールのインポート

1. 最初に [Azure AD PowerShell](/powershell/azure/active-directory/overview) をダウンロードしてインストールします。
2. `%programfiles%\Microsoft Azure Active Directory Connect` フォルダーを参照します。
3. `Import-Module .\AzureADSSO.psd1` コマンドを使用して、Seamless SSO PowerShell モジュールをインポートします。

### <a name="step-2-get-the-list-of-active-directory-forests-on-which-seamless-sso-has-been-enabled"></a>手順 2:シームレス SSO が有効になっている Active Directory フォレストのリストを取得する

1. PowerShell を管理者として実行します。 PowerShell で、`New-AzureADSSOAuthenticationContext` を呼び出します。 求められたら、テナントのグローバル管理者の資格情報を入力します。
2. `Get-AzureADSSOStatus` を呼び出します。 このコマンドでは、この機能が有効になっている Active Directory フォレストの一覧 ("ドメイン" リストを参照) が表示されます。

### <a name="step-3-disable-seamless-sso-for-each-active-directory-forest-where-youve-set-up-the-feature"></a>手順 3:機能を設定した各 Active Directory フォレストのシームレス SSO を無効にする

1. `$creds = Get-Credential` を呼び出します。 求められたら、目的の Active Directory フォレストのドメイン管理者の資格情報を入力します。

   > [!NOTE]
   >ドメイン管理者の資格情報ユーザー名は、SAM アカウント名の形式 (contoso\johndoe または contoso.com\johndoe) で入力する必要があります。 Microsoft はユーザー名のドメイン部分を使用して、DNS を使用してドメイン管理者のドメイン コントローラーを検索します。

   >[!NOTE]
   >使用するドメイン管理者アカウントは、保護されているユーザー グループのメンバーであってはなりません。 そうである場合、操作は失敗します。

2. `Disable-AzureADSSOForest -OnPremCredentials $creds` を呼び出します。 このコマンドは、この特定の Active Directory フォレスト用のオンプレミスのドメイン コントローラーから `AZUREADSSOACC` コンピューター アカウントを削除します。
3. 機能を設定した Active Directory フォレストごとに、前の手順を繰り返します。

### <a name="step-4-enable-seamless-sso-for-each-active-directory-forest"></a>手順 4:各 Active Directory フォレストのシームレス SSO を有効にする

1. `Enable-AzureADSSOForest` を呼び出します。 求められたら、目的の Active Directory フォレストのドメイン管理者の資格情報を入力します。

   > [!NOTE]
   >ドメイン管理者の資格情報ユーザー名は、SAM アカウント名の形式 (contoso\johndoe または contoso.com\johndoe) で入力する必要があります。 Microsoft はユーザー名のドメイン部分を使用して、DNS を使用してドメイン管理者のドメイン コントローラーを検索します。

   >[!NOTE]
   >使用するドメイン管理者アカウントは、保護されているユーザー グループのメンバーであってはなりません。 そうである場合、操作は失敗します。

2. 機能を設定する Active Directory フォレストごとに、前の手順を繰り返します。

### <a name="step-5-enable-the-feature-on-your-tenant"></a>手順 5. テナントで機能を有効にする

テナントで機能を有効にするには、`Enable-AzureADSSO -Enable $true`を呼び出します。
