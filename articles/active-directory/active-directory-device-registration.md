---
title: "Azure Active Directory への Windows ドメイン参加済みデバイスの自動デバイス登録 | Microsoft Docs"
description: "IT 管理者は、ドメイン参加済みの Windows デバイスをサイレント モードで自動的に Azure Active Directory (Azure AD) に登録できます。"
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
ms.openlocfilehash: 89f2498419e611011dc7817f4ecdea6f4a84c9bc
ms.lasthandoff: 03/10/2017


---
# <a name="automatic-device-registration-with-azure-active-directory-for-windows-domain-joined-devices"></a>Azure Active Directory への Windows ドメイン参加済みデバイスの自動デバイス登録
IT 管理者は、ドメイン参加済みの Windows デバイスをサイレント モードで自動的に Azure Active Directory (Azure AD) に登録できます。 これは、デバイスに基づいて条件付きでアクセスを許可するポリシーを Office&365; アプリケーションまたは AD FS によってオンプレミスで管理されているアプリケーションに対して構成している場合に役立ちます。 デバイス登録のシナリオの詳細については、「 [Azure Active Directory Device Registration の概要](active-directory-device-registration-get-started.md)」を参照してください。

> [!NOTE]
>  デバイスの自動登録をセットアップする最新手順については、「 [Azure Active Directory への Windows ドメイン参加済みデバイスの自動登録の設定方法](active-directory-conditional-access-automatic-device-registration-setup.md)」を参照してください。
> 
> 

Azure Active Directory への自動デバイス登録は、Active Directory ドメインに参加している Windows 7 および Windows 8.1 のマシンに使用できます。 通常、これらはインフォメーション ワーカーに提供されている、企業が所有するマシンです。

ドメイン参加済みの Windows デバイスの Azure AD への登録を開始するには、次の前提条件に従います。 前提条件を満たしたら、ドメイン参加済みの Windows デバイスの自動デバイス登録を構成します。

## <a name="deploy-ad-fs-and-connect-to-azure-active-directory-using-azure-active-directory-connect"></a>Azure Active Directory Connect を使用して、AD FS をデプロイして Azure Active Directory に接続する
1. Azure Active Directory Connect を使用して Active Directory フェデレーション サービス (AD FS) を Windows Server 2012 R2 にデプロイし、Azure Active Directory とのフェデレーション関係をセットアップします。
2. 追加の Azure Active Directory 証明書利用者信頼の要求規則を構成する
3. AD FS 管理コンソールを開き、**[AD FS]** > **[信頼関係] > [証明書利用者信頼]** に移動します。 [Microsoft Office 365 ID プラットフォーム] 証明書利用者信頼オブジェクトを右クリックし、 **[要求規則の編集…]**
4. **[発行変換規則]** タブで、**[規則の追加]** を選択します。
5. **[要求規則テンプレート]** ボックスの一覧から、**[カスタム規則を使用して要求を送信]** を選択します。 **[次へ]**を選択します。
6. *[要求規則名]* ボックスに「 **Auth Method Claim Rule** 」と入力します。
7. **[要求規則]** ボックスに次の要求規則を入力します。
   
        c:[Type == "http://schemas.microsoft.com/claims/authnmethodsreferences"]
        => issue(claim = c);
8. **[OK]** を&2; 回クリックして、ダイアログ ボックスを完了します。

## <a name="configure-an-additional-azure-active-directory-relying-party-trust-authentication-class-reference"></a>追加の Azure Active Directory 証明書利用者信頼の認証クラス参照を構成する
フェデレーション サーバーで、Windows PowerShell コマンド ウィンドウを開き、次のように入力します。

  `Set-AdfsRelyingPartyTrust -TargetName <RPObjectName> -AllowedAuthenticationClassReferences wiaormultiauthn`

ここで、<RPObjectName> は、Azure Active Directory 証明書利用者信頼オブジェクトの証明書利用者オブジェクト名です。 通常、このオブジェクトは "Microsoft Office 365 ID プラットフォーム" という名前です。

## <a name="ad-fs-global-authentication-policy"></a>AD FS グローバル認証ポリシー
イントラネットで Windows 統合認証を許可するように AD FS グローバル プライマリ認証ポリシーを構成します (これは既定の構成です)。

## <a name="internet-explorer-configuration"></a>Internet Explorer の構成
Windows デバイスで、Internet Explorer のローカル イントラネットのセキュリティ ゾーンに対して、次の設定を構成します。

* 既存のクライアント証明書が&1; つしか存在しない場合の証明書の選択: **有効にする**
* アクティブ スクリプト: **有効にする**
* イントラネット ゾーンでのみ自動的にログオンする: **オン**

これらは、Internet Explorer のローカル イントラネットのセキュリティ ゾーンの既定の設定です。 Internet Explorer で **[インターネット オプション]**  >  **[セキュリティ]** > [ローカル イントラネット] > [レベルのカスタマイズ] に移動すると、これらの設定を表示または管理できます。 また、Active Directory グループ ポリシーを使用して、これらの設定を構成することもできます。

## <a name="network-connectivity"></a>ネットワーク接続
ドメイン参加済みの Windows デバイスを Azure AD に自動的に登録するには、これらのデバイスが AD FS および Active Directory ドメイン コントローラーに接続されている必要があります。 このことは、一般的には、マシンが企業ネットワークに接続されている必要があることを意味します。 この接続には、ワイヤード (有線) 接続、Wi-Fi 接続、DirectAccess、または VPN を使用できます。

## <a name="configure-azure-active-directory-device-registration-discovery"></a>Azure Active Directory Device Registration の検出を構成する
Windows 7 デバイスおよび Windows 8.1 デバイスでは、ユーザー アカウント名と既知のデバイス登録サーバー名を組み合わせた、デバイス登録サーバーが検出されます。 Azure Active Directory Device Registration サービスに関連付けられた A レコードを参照する DNS CNAME レコードを作成する必要があります。 CNAME レコードでは、既知のプレフィックス **enterpriseregistration** の後に、組織のユーザー アカウントで使用されている UPN サフィックスを使用する必要があります。 組織で複数の UPN サフィックスを使用している場合は、DNS に複数の CNAME レコードを作成する必要があります。

たとえば、組織で @contoso.com と @region.contoso.com の&2; つの UPN サフィックスを使用している場合は、次の DNS レコードを作成します。

| エントリ | 型 | Address |
| --- | --- | --- |
| enterpriseregistration.contoso.com |CNAME |enterpriseregistration.windows.net |
| enterpriseregistration.region.contoso.com |CNAME |enterpriseregistration.windows.net |

## <a name="configure-automatic-device-registration-for-windows-7-and-windows-81-domain-joined-devices"></a>Windows 7 および Windows 8.1 のドメイン参加済みデバイスの自動デバイス登録を構成する
以下のリンクを参照して、Windows 7 および Windows 8.1 のドメイン参加済みデバイスの自動デバイス登録を構成します。 続行する前に、上記の前提条件が満たされているようにしてください。

* [Azure Active Directory への Windows ドメイン参加済みデバイスの自動登録の構成方法](active-directory-conditional-access-automatic-device-registration-setup.md)

## <a name="additional-notes"></a>その他のメモ
Azure AD へのデバイス登録により、広範なデバイスの機能が提供されます。 Azure AD Device Registration を使用すると、個人 (BYOD) のモバイル デバイスと、企業が所有するドメイン参加済みデバイスの両方を登録できます。 これらのデバイスは、Office&365; などのホストされるサービスと、AD FS によってオンプレミスで管理されているサービスの両方で使用できます。

モバイル デバイスと従来型デバイスの両方を使用する企業、または Office&365; や Azure AD などの Microsoft サービスを利用する企業は、Azure AD Device Registration サービスを使用して、デバイスを Azure AD に登録する必要があります。企業でモバイル デバイスや Office&365;、Azure AD、Microsoft Intune などの Microsoft サービスを利用せずに、代わりにオンプレミス アプリケーションのみをホストする場合、AD FS を使用してデバイスを Active Directory に登録することができます。

AD FS を使用したデバイス登録のデプロイに関する詳細については、 [ここ](https://technet.microsoft.com/library/dn486831.aspx)を参照してください。

## <a name="next-steps"></a>次のステップ

* [Azure Active Directory Device Registration の概要](active-directory-device-registration-get-started.md)

* [Azure Active Directory への Windows ドメイン参加済みデバイスの自動登録の構成方法](active-directory-conditional-access-automatic-device-registration-setup.md)


