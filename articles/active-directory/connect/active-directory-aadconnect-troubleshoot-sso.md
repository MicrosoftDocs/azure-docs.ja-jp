---
title: "Azure AD Connect: シームレス シングル サインオンのトラブルシューティングを行う | Microsoft Docs"
description: "このトピックでは、Azure Active Directory シームレス シングル サインオン (Azure AD シームレス SSO) のトラブルシューティング方法について説明します。"
services: active-directory
keywords: "Azure AD Connect とは, Active Directory のインストール, Azure AD に必要なコンポーネント, SSO, シングル サインオン"
documentationcenter: 
author: swkrish
manager: femila
ms.assetid: 9f994aca-6088-40f5-b2cc-c753a4f41da7
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/04/2017
ms.author: billmath
ms.translationtype: HT
ms.sourcegitcommit: 1dbb1d5aae55a4c926b9d8632b416a740a375684
ms.openlocfilehash: bc4ff9125553c8918df3a1f84041560a5b7d4cd8
ms.contentlocale: ja-jp
ms.lasthandoff: 08/07/2017

---

# <a name="troubleshoot-azure-active-directory-seamless-single-sign-on"></a>Azure Active Directory シームレス シングル サインオンのトラブルシューティングを行う

この記事は、Azure AD シームレス シングル サインオンに関する一般的な問題のトラブルシューティング情報を見つける助けとなります。

## <a name="known-issues"></a>既知の問題

- 30 以上の AD フォレストを同期している場合は、Azure AD Connect を使用するシームレスな SSO を有効にすることはできません。 この問題を回避するには、テナントでこの機能を[手動で有効](#manual-reset-of-azure-ad-seamless-sso)にします。
- Azure AD サービスの URL (https://autologon.microsoftazuread-sso.com、https://aadg.windows.net.nsatc.net) を "ローカル イントラネット" ゾーンではなく "信頼済みサイト" ゾーンに追加すると、**ユーザーのサインインがブロック**されます。
- シームレス SSO は、Firefox および Edge のプライベート ブラウズ モードでは動作しません。 また、Internet Explorer の拡張保護モードがオンになっている場合も動作しません。

>[!IMPORTANT]
>お客様から報告された問題を調査するために、Edge のサポートを最近ロールバックしました。

## <a name="check-status-of-the-feature"></a>機能の状態の確認

テナントでシームレス SSO 機能が引き続き **[有効]** になっていることを確認します。 機能の状態は、[Azure Active Directory 管理センター](https://aad.portal.azure.com/)の **[Azure AD Connect]** ブレードで確認できます。

![Azure Active Directory 管理センター - [Azure AD Connect] ブレード](./media/active-directory-aadconnect-sso/sso10.png)

## <a name="sign-in-failure-reasons-on-the-azure-active-directory-admin-center"></a>Azure Active Directory 管理センターでのサインイン失敗の理由

シームレス SSO 使用時のユーザーのサインインに関する問題のトラブルシューティングを行う場合、[Azure Active Directory 管理センター](https://aad.portal.azure.com/)で[サインイン アクティビティ レポート](../active-directory-reporting-activity-sign-ins.md)を調べることは、適切な始め方です。

![Azure Active Directory 管理センター - サインイン レポート](./media/active-directory-aadconnect-sso/sso9.png)

[Azure Active Directory 管理センター](https://aad.portal.azure.com/)で **[Azure Active Directory]** -> **[サインイン]** に移動し、特定のユーザーのサインイン アクティビティをクリックします。 **[サインインのエラー コード]** フィールドを探します。 次の表を使用して、そのフィールドの値を、失敗の理由と解決策にマップします。

|サインイン エラー コード|サインインが失敗した理由|解決策
| --- | --- | ---
| 81001 | ユーザーの Kerberos チケットが大きすぎます。 | ユーザーのグループ メンバーシップを減らしてやり直してください。
| 81002 | ユーザーの Kerberos チケットを検証できません。 | 「[トラブルシューティングのチェックリスト](#troubleshooting-checklist)」を参照してください。
| 81003 | ユーザーの Kerberos チケットを検証できません。 | 「[トラブルシューティングのチェックリスト](#troubleshooting-checklist)」を参照してください。
| 81004 | Kerberos 認証を試みましたが失敗しました。 | 「[トラブルシューティングのチェックリスト](#troubleshooting-checklist)」を参照してください。
| 81008 | ユーザーの Kerberos チケットを検証できません。 | 「[トラブルシューティングのチェックリスト](#troubleshooting-checklist)」を参照してください。
| 81009 | ユーザーの Kerberos チケットを検証できません。 | 「[トラブルシューティングのチェックリスト](#troubleshooting-checklist)」を参照してください。
| 81010 | シームレス SSO に失敗しました。ユーザーの Kerberos チケットが期限切れか無効です。 | ユーザーは、企業ネットワーク内のドメインに参加しているデバイスからサインインする必要があります。
| 81011 | ユーザーの Kerberos チケット内の情報では、ユーザー オブジェクトが見つかりません。 | Azure AD Connect を使用してユーザー情報を Azure AD と同期します。
| 81012 | Azure AD にサインインしようとしているユーザーは、デバイスにサインインしているユーザーと異なります。 | 別のデバイスからサインインします。
| 81013 | ユーザーの Kerberos チケット内の情報では、ユーザー オブジェクトが見つかりません。 |Azure AD Connect を使用してユーザー情報を Azure AD と同期します。 

## <a name="troubleshooting-checklist"></a>トラブルシューティングのチェックリスト

シームレス SSO の問題のトラブルシューティングを行うには、次のチェックリストを使用します。

- Azure AD Connect でシームレス SSO 機能が有効になっていることを確認します。 (ポートのブロックなどが原因で) この機能を有効にすることができない場合は、すべての[前提条件](active-directory-aadconnect-sso-quick-start.md#step-1-check-prerequisites)が満たされていることを確認します。
- 両方の Azure AD URL (https://autologon.microsoftazuread-sso.com と https://aadg.windows.net.nsatc.net) がユーザーのイントラネット ゾーン設定の一部になっていることを確認します。
- 会社のデバイスが AD ドメインに参加していることを確認します。
- ユーザーが AD ドメイン アカウントを使用してデバイスにログオンしていることを確認します。
- ユーザーのアカウントが、シームレス SSO が設定されている AD フォレストからのものであることを確認します。
- デバイスが企業ネットワークに接続されていることを確認します。
- デバイスの時刻が Active Directory の時刻およびドメイン コントローラーの時刻と同期されており、時刻のずれが 5 分以内であることを確認します。
- コマンド プロンプトから **klist** コマンド使用して、デバイス上の既存の Kerberos チケットを一覧表示します。 `AZUREADSSOACCT` コンピューター アカウントに対して発行されたチケットが存在することを確認します。 ユーザーの Kerberos チケットは、通常は 12 時間有効です。 Active Directory で別の設定が行われていることがあります。
- **klist purge** コマンドを使用してデバイスから既存の Kerberos チケットを消去してからやり直します。
- JavaScript に関連する問題があるかどうかを確認するために、ブラウザーのコンソール ログ ([開発者ツール] の下) を確認します。
- [ドメイン コント ローラーのログ](#domain-controller-logs)も確認します。

### <a name="domain-controller-logs"></a>ドメイン コントローラーのログ

ドメイン コントローラーで成功の監査が有効になっている場合、ユーザーがシームレス SSO を使用してサインインするたびに、セキュリティ エントリがイベント ログに記録されます。 これらのセキュリティ イベントは、次のクエリを使用して見つけることができます (コンピューター アカウント **AzureADSSOAcc$** に関連付けられているイベント **4769** を探します)。

```
    <QueryList>
      <Query Id="0" Path="Security">
    <Select Path="Security">*[EventData[Data[@Name='ServiceName'] and (Data='AZUREADSSOACC$')]]</Select>
      </Query>
    </QueryList>
```

## <a name="manual-reset-of-the-feature"></a>機能の手動リセット

トラブルシューティングを行っても改善しなかった場合は、テナントでシームレス SSO 機能を手動でリセットできます。 Azure AD Connect が実行されているオンプレミス サーバーで次の手順を実行します。

### <a name="step-1-import-the-seamless-sso-powershell-module"></a>手順 1: Seamless SSO PowerShell モジュールをインポートする

1. 最初に、[Microsoft Online Services サインイン アシスタント](http://go.microsoft.com/fwlink/?LinkID=286152)をダウンロードしてインストールします。
2. 次に、 [64-bit Azure Active Directory Module for Windows PowerShell](http://go.microsoft.com/fwlink/p/?linkid=236297)をダウンロードしてインストールします。
3. `%programfiles%\Microsoft Azure Active Directory Connect` フォルダーに移動します。
4. 以下のコマンドを使用して、Seamless SSO PowerShell モジュールをインポートします。`Import-Module .\AzureADSSO.psd1`

### <a name="step-2-get-the-list-of-ad-forests-on-which-seamless-sso-has-been-enabled"></a>手順 2: シームレス SSO が有効になっている AD フォレストのリストを取得する

1. PowerShell を管理者として実行します。 PowerShell で、`New-AzureADSSOAuthenticationContext` を呼び出します。 求められたら、テナントのグローバル管理者の資格情報を入力します。
2. `Get-AzureADSSOStatus` を呼び出します。 このコマンドでは、この機能が有効になっている AD フォレストのリスト ("ドメイン" リストを参照) が表示されます。

### <a name="step-3-disable-seamless-sso-for-each-ad-forest-that-it-was-set-it-up-on"></a>手順 3: 機能が有効に設定されている各 AD フォレストのシームレス SSO を無効にする

1. `$creds = Get-Credential` を呼び出します。 メッセージが表示されたら、目的の AD フォレストのドメイン管理者の資格情報を入力します。
2. `Disable-AzureADSSOForest -OnPremCredentials $creds` を呼び出します。 このコマンドは、この特定の AD フォレスト用のオンプレミスのドメイン コントローラーから `AZUREADSSOACCT` コンピューター アカウントを削除します。
3. 機能が有効に設定されている AD フォレストごとに、上記の手順を繰り返します。

### <a name="step-4-enable-seamless-sso-for-each-ad-forest"></a>手順 4: 各 AD フォレストのシームレス SSO を有効にする

1. `Enable-AzureADSSOForest` を呼び出します。 メッセージが表示されたら、目的の AD フォレストのドメイン管理者の資格情報を入力します。
2. 機能が有効に設定されている AD フォレストごとに、上記の手順を繰り返します。

### <a name="step-5-enable-the-feature-on-your-tenant"></a>手順 5. テナントで機能を有効にする

`Enable-AzureADSSO` を呼び出し、`Enable: ` プロンプトで「true」と入力して、テナントでこの機能を有効にします。

