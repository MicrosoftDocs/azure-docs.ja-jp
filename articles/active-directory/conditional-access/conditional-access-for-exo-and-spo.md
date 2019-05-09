---
title: SharePoint Online と Exchange Online に Azure Active Directory の条件付きアクセスを設定する | Microsoft Docs
description: SharePoint Online と Exchange Online に Azure Active Directory の条件付きアクセスを設定する方法を学習します。
services: active-directory
documentationcenter: ''
author: MicrosoftGuyJFlo
manager: daveba
editor: ''
ms.assetid: 62349fba-3cc0-4ab5-babe-372b3389eff6
ms.service: active-directory
ms.subservice: conditional-access
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 02/27/2019
ms.author: joflore
ms.reviewer: calebb
ms.collection: M365-identity-device-management
ms.openlocfilehash: a90cd381dbe3feaad110c7f10ae328915c051d0a
ms.sourcegitcommit: 6da4959d3a1ffcd8a781b709578668471ec6bf1b
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/27/2019
ms.locfileid: "58517727"
---
# <a name="how-to-set-up-sharepoint-online-and-exchange-online-for-azure-active-directory-conditional-access"></a>方法:SharePoint Online と Exchange Online に Azure Active Directory の条件付きアクセスを設定する 

[Azure Active Directory (Azure AD) の条件付きアクセス](overview.md)を使うと、ユーザーがどのようにクラウド アプリにアクセスするかを制御できます。 条件付きアクセスを使って SharePoint Online と Exchange Online に対するアクセスを制御するには、以下が必要になります:

- 条件付きアクセス シナリオがサポートされているかどうかを確認する。
- クライアント アプリが条件付きアクセス ポリシーの適用を回避できないようにする。   

この記事では、この 2 つのケースに対応する方法を説明します。


## <a name="what-you-need-to-know"></a>知っておくべきこと

以下のいずれかから認証が試行された場合には、クラウド アプリの保護に Azure AD の条件付きアクセスを使用できます:

- Web ブラウザー

- [先進認証](https://support.office.com/article/Using-Office-365-modern-authentication-with-Office-clients-776c0036-66fd-41cb-8928-5495c0f9168a)を使用するクライアント アプリ

- Exchange ActiveSync 

一部のクラウド アプリでは、レガシ認証プロトコルもサポートされています。 たとえば、SharePoint Online や Exchange Online がこれに該当します。 クライアント アプリでクラウド アプリへのアクセスにレガシ認証プロトコルを利用できる場合、そのようなアクセスの試行に対しては Azure AD の条件付きアクセス ポリシーを適用できません。 クライアント アプリがポリシーの適用を回避する事態を防ぐため、該当するクラウド アプリで先進認証のみを有効にできるかどうかを確認する必要があります。 

条件付きアクセスが適用されないクライアント アプリの例を以下に示します:

- Office 2010 およびそれ以前

- 先進認証を有効にしていない場合の Office 2013



 
## <a name="control-access-to-sharepoint-online"></a>SharePoint Online に対するアクセスの制御

SharePoint Online は、先進認証のほかにレガシ認証プロトコルもサポートしています。 レガシ認証プロトコルが有効になっていると、先進認証を使用していないクライアントについては SharePoint 向けの条件付きアクセス ポリシーが適用されなくなります。

**[Set-SPOTenant](https://technet.microsoft.com/library/fp161390.aspx)** コマンドレットを使うと、SharePoint へのアクセスに関してレガシ認証プロトコルを無効にできます: 

    Set-SPOTenant -LegacyAuthProtocolsEnabled $false

## <a name="control-access-to-exchange-online"></a>Exchange Online に対するアクセスの制御

Exchange Online に条件付きアクセス ポリシーを設定するときは、以下を確認する必要があります:

- Exchange ActiveSync

- レガシ認証プロトコル



### <a name="exchange-activesync"></a>Exchange ActiveSync

Exchange Active Sync は先進認証をサポートしているものの、条件付きアクセスのシナリオのサポートに関していくつか制約があります:

- ポリシーで **Exchange Active Sync クライアント**を選択した場合は、他の条件を構成することはできません。  

    ![デバイス プラットフォーム](./media/conditional-access-for-exo-and-spo/05.png)

- 多要素認証の要件の設定はサポートされていません  

    ![条件付きアクセス](./media/conditional-access-for-exo-and-spo/01.png)

Exchange ActiveSync から Exchange Online へのアクセスを効果的に保護するには、以下を行います:

- 以下の手順に従って、サポートされている条件付きアクセス ポリシーを構成します:

    a. クラウド アプリとして **Office 365 Exchange Online** のみを選択します。  

    ![条件付きアクセス](./media/conditional-access-for-exo-and-spo/04.png)

    b. **クライアント アプリ**として、**Exchange Active Sync** を選択します。  

    ![デバイス プラットフォーム](./media/conditional-access-for-exo-and-spo/03.png)

- Active Directory フェデレーション サービス (AD FS) の規則を使って Exchange ActiveSync をブロックします。

        @RuleName = "Block Exchange ActiveSync"
        c1:[Type == "http://schemas.microsoft.com/2012/01/requestcontext/claims/x-ms-client-application", Value == "Microsoft.Exchange.ActiveSync"]
        => issue(Type = "http://schemas.microsoft.com/authorization/claims/permit", Value = "false");




### <a name="legacy-authentication-protocols"></a>レガシ認証プロトコル

Exchange Online は、先進認証のほかにレガシ認証プロトコルもサポートしています。 レガシ認証プロトコルが有効になっていると、先進認証を使用していないクライアントについては Exchange Online 向けの条件付きアクセス ポリシーが適用されなくなります。

AD FS 規則を設定すると、Exchange Online でレガシ認証プロトコルを無効にすることができます。 これにより、以下のものからのアクセスがブロックされます:

- 先進認証が有効になっていない以前の Office クライアント (Office 2013 など) 

- 以前のバージョンの Office


## <a name="set-up-ad-fs-rules"></a>AD FS 規則の設定

AD FS レベルでトラフィックを有効にしたり、ブロックしたりするには、以下に示した発行承認規則を使用できます。 

### <a name="block-legacy-traffic-from-the-extranet"></a>エクストラネットからのレガシ トラフィックをブロックする

以下の 3 つの規則を適用します: 

- アクセスを有効化:
    - Exchange ActiveSync トラフィック
    - ブラウザーのトラフィック
    - 先進認証のトラフィック
- アクセスをブロック: 
    - エクストラネットのレガシ クライアント アプリ

**規則 1**

    @RuleName = "Allow all intranet traffic"
    c1:[Type == "http://schemas.microsoft.com/ws/2012/01/insidecorporatenetwork", Value == "true"]
    => issue(Type = "http://schemas.microsoft.com/authorization/claims/permit", Value = "true");

**規則 2**

    @RuleName = "Allow Exchange ActiveSync"
    c1:[Type == "http://schemas.microsoft.com/2012/01/requestcontext/claims/x-ms-client-application", Value == "Microsoft.Exchange.ActiveSync"]
    => issue(Type = "http://schemas.microsoft.com/authorization/claims/permit", Value = "true");

**規則 3**

    @RuleName = "Allow extranet browser and browser dialog traffic"
    c1:[Type == "http://schemas.microsoft.com/ws/2012/01/insidecorporatenetwork", Value == "false"] &&
    c2:[Type == "http://schemas.microsoft.com/2012/01/requestcontext/claims/x-ms-endpoint-absolute-path", Value =~ "(/adfs/ls)|(/adfs/oauth2)"]
    => issue(Type = "http://schemas.microsoft.com/authorization/claims/permit", Value = "true");

### <a name="block-legacy-traffic-from-anywhere"></a>流入元を問わずレガシ トラフィックをブロックする

以下の 3 つの規則を適用します:

- アクセスを有効化: 
    - Exchange ActiveSync トラフィック
    - ブラウザーのトラフィック
    - 先進認証のトラフィック
- アクセスをブロック: 
    - あらゆる場所のレガシ アプリ

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

## <a name="next-steps"></a>次の手順

詳細については、「[Azure Active Directory の条件付きアクセス](overview.md)」を参照してください。

要求規則の構成については、「[要求規則の構成](https://docs.microsoft.com/windows-server/identity/ad-fs/operations/configure-claim-rules)」を参照してください。 






