---
title: "Azure Active Directory の条件付きアクセス規則を使用するアプリケーションとブラウザー | Microsoft Docs"
description: "Azure Active Directory の条件付きアクセス制御では、ユーザーを認証するときやアプリケーションのアクセスを許可するために特定の条件がチェックされます。"
services: active-directory
documentationcenter: 
author: MarkusVi
manager: femila
editor: 
ms.assetid: 62349fba-3cc0-4ab5-babe-372b3389eff6
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 03/28/2017
ms.author: markvi
translationtype: Human Translation
ms.sourcegitcommit: 07635b0eb4650f0c30898ea1600697dacb33477c
ms.openlocfilehash: 7dc79fad1a87f015475cd2643c8bec55192b2a15
ms.lasthandoff: 03/28/2017


---
# <a name="applications-and-browsers-that-use-conditional-access-rules-in-azure-active-directory"></a>Azure Active Directory の条件付きアクセス規則を使用するアプリケーションとブラウザー

条件付きアクセス規則は、Azure Active Directory (Azure AD) 接続アプリケーションやフェデレーションが事前に統合されている SaaS (Software as a Service) アプリケーション、パスワード シングル サインオン (SSO) を使用するアプリケーション、業務アプリケーション、Azure AD アプリケーション プロキシを使用するアプリケーションでサポートされます。 条件付きアクセスへの対応状況を詳しく記載したアプリケーション一覧については、「[条件付きアクセスで有効になっているサービス](active-directory-conditional-access-technical-reference.md)」を参照してください。 条件付きアクセスの対象となるのは、先進認証を使用するモバイル アプリケーションとデスクトップ アプリケーションです。 この記事では、モバイル アプリとデスクトップ アプリにおける条件付きアクセスの動作について取り上げます。

先進認証を使用するアプリケーションでは、Azure AD のサインイン ページを使用することができます。 ユーザーは、サインイン ページで多要素認証を求められます。 ユーザーのアクセスがブロックされた場合は、メッセージが表示されます。 Azure AD でデバイスの認証を行うためには先進認証が必須であり、それによってデバイス ベースの条件付きアクセス ポリシーが評価されます。

そこで、条件付きアクセス規則に対応しているアプリケーションと、他のアプリケーション エントリ ポイントを保護するために必要となる手順を理解しておくことが重要となります。

## <a name="applications-that-use-modern-authentication"></a>先進認証を使用するアプリケーション
> [!NOTE]
> Azure AD の条件付きアクセス ポリシーと同等のポリシーが Office 365 にも存在する場合、両方の条件付きアクセス ポリシーを構成することになります。 たとえば、Exchange Online や SharePoint Online の条件付きアクセス ポリシーが該当します。
>
>

Office 365 をはじめとする Azure AD 接続サービス アプリケーションに対する条件付きアクセスは、以下のアプリケーションでサポートされます。

| 対象サービス | Platform | アプリケーション |
| --- | --- | --- |
| Office 365 Exchange Online |Windows 10 |メール/カレンダー/People アプリ、Outlook 2016、Outlook 2013 (先進認証を使用)、Skype for Business (先進認証を使用) |
| Office 365 Exchange Online |Windows 8.1、Windows 7 |Outlook 2016、Outlook 2013 (先進認証を使用)、Skype for Business (先進認証を使用) |
| Office 365 Exchange Online |iOS、Android |Outlook Mobile アプリ |
| Office 365 Exchange Online |Mac OS X |Outlook 2016 (多要素認証と場所のみ対応。デバイス ベースのポリシーは将来サポート予定)、Skype for Business は将来サポート予定 |
| Office 365 SharePoint Online |Windows 10 |Office 2016 アプリ、ユニバーサル Office アプリ、Office 2013 (最新の認証を使用)、OneDrive 同期クライアント ([メモ](https://support.office.com/en-US/article/Azure-Active-Directory-conditional-access-with-the-OneDrive-sync-client-on-Windows-028d73d7-4b86-4ee0-8fb7-9a209434b04e)参照)。Office Groups および SharePoint アプリについては将来サポート予定。 |
| Office 365 SharePoint Online |Windows 8.1、Windows 7 |Office 2016 アプリ、Office 2013 (最新の認証を使用)、OneDrive 同期クライアント ([メモ](https://support.office.com/en-US/article/Azure-Active-Directory-conditional-access-with-the-OneDrive-sync-client-on-Windows-028d73d7-4b86-4ee0-8fb7-9a209434b04e)参照) |
| Office 365 SharePoint Online |iOS、Android |Office モバイル アプリ |
| Office 365 SharePoint Online |Mac OS X |Office 2016 アプリ (多要素認証と場所のみ対応。デバイス ベースのポリシーは将来サポート予定) |
| Office 365 Yammer |Windows 10、iOS (Android サポートは将来サポート予定) |Office Yammer アプリ |
| Dynamics CRM |Windows 10、Windows 8.1、Windows 7、iOS、Android |Dynamics CRM アプリ |
| PowerBI サービス |Windows 10、Windows 8.1、Windows 7、iOS、Android |PowerBI アプリ |
| Azure Remote App サービス |Windows 10、Windows 8.1、Windows 7、iOS、Android、Mac OS X |Azure リモート アプリ |
| 任意のマイ アプリ アプリ サービス |Android および iOS |任意のマイ アプリ アプリ サービス |

## <a name="applications-that-do-not-use-modern-authentication"></a>先進認証を使用しないアプリケーション
先進認証を使用しないアプリへのアクセスをブロックするには、現時点では他の方法を用いる必要があります。 先進認証を使用しないアプリには、条件付きアクセスの規則が適用されません。 これは主に、Exchange と SharePoint アクセスに関する考慮事項となります。 以前のバージョンのアプリでは、古いアクセス制御プロトコルが使用されているケースが大半です。

### <a name="control-access-in-office-365-sharepoint-online"></a>Office 365 SharePoint Online のアクセス制御
SharePoint アクセスについては、Set-SPOTenant コマンドレットを使用することで、従来のプロトコルを無効にできます。 先進認証以外のプロトコルを使用している Office クライアントに SharePoint Online リソースへのアクセスを禁止するには、このコマンドレットを使用してください。

**コマンドの例**:     `Set-SPOTenant -LegacyAuthProtocolsEnabled $false`

### <a name="control-access-in-office-365-exchange-online"></a>Office 365 Exchange Online のアクセス制御
Exchange で用意されているプロトコルは大きく 2 つのカテゴリに分けられます。 以下の選択肢を確認し、自分の組織にとって最適なポリシーを選択してください。

* **Exchange ActiveSync**。 既定では、多要素認証と場所に関する条件付きアクセスのポリシーが Exchange ActiveSync には適用されません。 これらのサービスに対するアクセスを保護するには、Exchange ActiveSync のポリシーを直接構成するか、Active Directory フェデレーション サービス (AD FS) の規則を使用して Exchange ActiveSync をブロックする必要があります。
* **従来のプロトコル**。 従来のプロトコルは、AD FS を使用してブロックすることができます。 最新でない Office クライアント (先進認証に対応していない Office 2013 や、それより前のバージョンの Office など) へのアクセスは、この方法でブロックすることができます。

### <a name="use-ad-fs-to-block-legacy-protocol"></a>AD FS を使用して従来のプロトコルをブロックする
従来のプロトコルを使用したアクセスは、以下の例に示した規則を使用して AD FS レベルでブロックすることができます。 一般に、次の 2 つの構成からいずれかを選ぶことになります。

#### <a name="option-1-allow-exchange-activesync-and-allow-legacy-apps-but-only-on-the-intranet"></a>オプション 1: Exchange ActiveSync を許可し、レガシ アプリケーションについては、イントラネット上でのみ許可する
Microsoft Office 365 ID プラットフォームの AD FS 証明書利用者信頼に次の 3 つの規則を適用すると、Exchange ActiveSync トラフィック、ブラウザー トラフィック、先進認証トラフィックにアクセス権が与えられます。 エクストラネットからのレガシ アプリケーションはブロックされます。

##### <a name="rule-1"></a>規則 1
    @RuleName = "Allow all intranet traffic"
    c1:[Type == "http://schemas.microsoft.com/ws/2012/01/insidecorporatenetwork", Value == "true"]
    => issue(Type = "http://schemas.microsoft.com/authorization/claims/permit", Value = "true");

##### <a name="rule-2"></a>規則 2
    @RuleName = "Allow Exchange ActiveSync"
    c1:[Type == "http://schemas.microsoft.com/2012/01/requestcontext/claims/x-ms-client-application", Value == "Microsoft.Exchange.ActiveSync"]
    => issue(Type = "http://schemas.microsoft.com/authorization/claims/permit", Value = "true");

##### <a name="rule-3"></a>規則 3
    @RuleName = "Allow extranet browser and browser dialog traffic"
    c1:[Type == "http://schemas.microsoft.com/ws/2012/01/insidecorporatenetwork", Value == "false"] &&
    c2:[Type == "http://schemas.microsoft.com/2012/01/requestcontext/claims/x-ms-endpoint-absolute-path", Value =~ "(/adfs/ls)|(/adfs/oauth2)"]
    => issue(Type = "http://schemas.microsoft.com/authorization/claims/permit", Value = "true");

#### <a name="option-2-allow-exchange-activesync-and-block-legacy-apps"></a>オプション 2: Exchange ActiveSync を許可してレガシ アプリケーションをブロックする
Microsoft Office 365 ID プラットフォームの AD FS 証明書利用者信頼に次の 3 つの規則を適用すると、Exchange ActiveSync トラフィック、ブラウザー トラフィック、先進認証トラフィックにアクセス権が与えられます。 レガシ アプリケーションは、どの場所からもブロックされます。

##### <a name="rule-1"></a>規則 1
    @RuleName = "Allow all intranet traffic only for browser and modern authentication clients"
    c1:[Type == "http://schemas.microsoft.com/ws/2012/01/insidecorporatenetwork", Value == "true"] &&
    c2:[Type == "http://schemas.microsoft.com/2012/01/requestcontext/claims/x-ms-endpoint-absolute-path", Value =~ "(/adfs/ls)|(/adfs/oauth2)"]
    => issue(Type = "http://schemas.microsoft.com/authorization/claims/permit", Value = "true");


##### <a name="rule-2"></a>規則 2
    @RuleName = "Allow Exchange ActiveSync"
    c1:[Type == "http://schemas.microsoft.com/2012/01/requestcontext/claims/x-ms-client-application", Value == "Microsoft.Exchange.ActiveSync"]
    => issue(Type = "http://schemas.microsoft.com/authorization/claims/permit", Value = "true");


##### <a name="rule-3"></a>規則 3
    @RuleName = "Allow extranet browser and browser dialog traffic"
    c1:[Type == "http://schemas.microsoft.com/ws/2012/01/insidecorporatenetwork", Value == "false"] &&
    c2:[Type == "http://schemas.microsoft.com/2012/01/requestcontext/claims/x-ms-endpoint-absolute-path", Value =~ "(/adfs/ls)|(/adfs/oauth2)"]
    => issue(Type = "http://schemas.microsoft.com/authorization/claims/permit", Value = "true");


## <a name="supported-browsers"></a>サポートされているブラウザー


| OS                     | ブラウザー                 | サポート |
| :--                    | :--                      | :-:     |
| Windows 10                 | IE、Edge                 | ![○][1] |
| Windows 10                 | Chrome                   | 近日対応予定 |
| Windows 8/8.1            | IE                       | ![○][1] |
| Windows 7                  | IE                       | ![○][1] |
| iOS                     | Safari                   | ![○][1] |
| Android                | Chrome                   | ![○][1] |
| Windows Phone               | IE、Edge                 | ![○][1] |
| Windows Server 2016    | IE、Edge                 | ![○][1] |
| Windows Server 2012 R2 | IE                       | ![○][1] |
| Windows Server2008 R2     | IE                       | ![○][1] |
| Mac OS                 | Safari                   | ![○][1] |
| Mac OS                 | Chrome                   | 近日対応予定 |


## <a name="next-steps"></a>次のステップ

詳細については、「[Azure Active Directory の条件付きアクセス](active-directory-conditional-access.md)」を参照してください。



<!--Image references-->
[1]: ./media/active-directory-conditional-access-supported-apps/ic195031.png



