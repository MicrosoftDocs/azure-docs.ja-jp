---
title: リンクと URL の Azure AD アプリ プロキシの変換 | Microsoft Docs
description: Azure AD アプリケーション プロキシで公開されているアプリのハードコードされたリンクをリダイレクトする方法について説明します。
services: active-directory
documentationcenter: ''
author: kenwith
manager: daveba
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.date: 08/15/2019
ms.author: kenwith
ms.reviewer: japere
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: bb1935242790333a91b47ccecc19d934b8145085
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "101688333"
---
# <a name="redirect-hard-coded-links-for-apps-published-with-azure-ad-application-proxy"></a>Azure AD アプリケーション プロキシで公開されているアプリのハードコードされたリンクをリダイレクトする

Azure AD アプリケーション プロキシを使うと、リモートのユーザーまたは自分のデバイスを使っているユーザーがオンプレミスのアプリを使用できるようになります。 ただし、一部のアプリは、HTML に埋め込まれたローカル リンクと共に開発されました。 現時点では、アプリをリモートで使用しているときには、それらのリンクは正常に機能しません。 お互いをポイントするオンプレミス アプリケーションが複数ある場合、お客様のユーザーは、オフィスにいないときにそのリンクが機能し続けることを期待します。 

お客様の企業ネットワークの内でも外でも、リンクを同じように機能させるのに最適な方法は、アプリの外部 URL をその内部 URL と一致するように構成することです。 外部 URL に、規定値のアプリケーション プロキシ ドメインではなく、お客様の企業ドメイン名が含まれるように構成するには、[カスタム ドメイン](application-proxy-configure-custom-domain.md)を使用します。


テナントでカスタム ドメインを使用できない場合、この機能を提供するその他のいくつかのオプションがあります。 これらはすべて、カスタム ドメインとの互換性も相互の互換性もあるため、カスタム ドメインやその他のソリューションを必要に応じて構成できます。

> [!NOTE]
> JavaScript によって生成されたハードコーディングされている内部 URL では、リンク変換はサポートされません。

**オプション 1: Microsoft Edge を使用する** – このソリューションは、Microsoft Edge ブラウザーを使用してアプリケーションにアクセスすることをユーザーに推奨または要求することを計画している場合にのみ適用されます。 すべての公開されている URL を処理します。 

**オプション 2: MyApps 拡張機能を使用する** – このソリューションは、クライアント側のブラウザー拡張機能をインストールすることをユーザーに要求しますが、すべての公開されている URL を処理し、ほとんどの一般的なブラウザーと連携します。 

**オプション 3:リンク変換設定を使用する** – これは、ユーザーからは見えない管理者側の設定です。 ただし、HTML および CSS 内のみの URL が処理されます。   

これら 3 つの機能は、ユーザーの居場所にかかわらず、リンクを機能させ続けます。 内部エンドポイントまたはポートを直接ポイントするアプリがある場合は、公開されている外部アプリケーション プロキシ URL にこれらの内部 URL をマップできます。 

 
> [!NOTE]
> 最後のオプションはテナント専用です。いかなる理由であっても、カスタム ドメインのアプリに同じ内部および外部 URL を含めることはできません。 この機能を有効にする前に、[Azure AD アプリケーション プロキシでカスタム ドメイン](application-proxy-configure-custom-domain.md)が機能するかどうかをご確認ください。 
> 
> または、リンク変換を構成したいアプリケーションが SharePoint の場合は、「[SharePoint 2013 の代替アクセス マッピングを構成する](/SharePoint/administration/configure-alternate-access-mappings)」で、リンクをマッピングするための別の方法をご覧ください。 

 
### <a name="option-1-microsoft-edge-integration"></a>オプション 1: Microsoft Edge 統合 

Microsoft Edge を使用して、アプリケーションとコンテンツの保護を強化することができます。 このソリューションを使用するには、Microsoft Edge を使用してアプリケーションにアクセスすることをユーザーに要求または推奨する必要があります。 アプリケーション プロキシで公開されているすべての内部 URL は Edge によって認識され、対応する外部 URL にリダイレクトされます。 これにより、すべてのハードコードされた内部 URL が機能すること、またユーザーがブラウザーに移動して内部 URL を直接入力した場合に、ユーザーがリモートであってもその URL が機能することが保証されます。  

このオプションの構成方法などの詳細については、「[iOS および Android 用の Edge と Microsoft Intune を使用して Web アクセスを管理する](/mem/intune/apps/manage-microsoft-edge)」ドキュメントをご覧ください。  

### <a name="option-2-myapps-browser-extension"></a>オプション 2:MyApps ブラウザー拡張機能 

MyApps ブラウザー拡張機能を使用すると、アプリケーション プロキシで公開されているすべての内部 URL は拡張機能によって認識され、対応する外部 URL にリダイレクトされます。 これにより、すべてのハードコードされた内部 URL が機能すること、またユーザーがブラウザーのアドレス バーに移動して内部 URL を直接入力した場合に、ユーザーがリモートであってもその URL が機能することが保証されます。  

この機能を使用するには、ユーザーが拡張機能をダウンロードしてログインしている必要があります。 管理者またはユーザーに必要なその他の構成はありません。 

このオプションの構成方法などの詳細については、[MyApps Browser 拡張機能](../user-help/my-apps-portal-end-user-access.md#download-and-install-the-my-apps-secure-sign-in-extension)のドキュメントを参照してください。

> [!NOTE]
> MyApps ブラウザー拡張機能では、ワイルドカード URL のリンク変換はサポートされていません。

### <a name="option-3-link-translation-setting"></a>オプション 3:リンク変換設定 

リンク変換が有効になっている場合、アプリケーション プロキシ サービスが HTML と CSS を通じて公開されている内部リンクを検索し、それらを変換して、ユーザーの操作が中断されないようにします。 より高パフォーマンスなエクスペリエンスをユーザーに提供するため、MyApps Browser 拡張機能の使用はリンク変換設定よりも優先されます。

> [!NOTE]
> オプション 2 または 3 を使用している場合は、一度に 1 つのみを有効にするようにします。

## <a name="how-link-translation-works"></a>リンク変換の仕組み

認証後に、プロキシ サーバーからユーザーにアプリケーション データが渡されると、アプリケーション プロキシによってハードコードされたリンクについてアプリケーションがスキャンされ、それぞれ公開されている外部 URL で置き換えられます。

アプリケーション プロキシでは、アプリケーションが UTF-8 でエンコードされていることを前提としています。 そうなっていない場合は、HTTP 応答ヘッダーでエンコードの種類を `Content-Type:text/html;charset=utf-8` のように指定してください。

### <a name="which-links-are-affected"></a>影響を受けるリンク

リンク変換機能は、アプリの本文のコード タグ内にあるリンクだけを検索します。 アプリケーション プロキシには、ヘッダー内の cookie や URL を変換するための別の機能があります。 

オンプレミスのアプリケーションの内部リンクには 2 つの一般的な種類があります。

- `/claims/claims.html` のようなローカル ファイル構造の共有リソースをポイントする **相対内部リンク**。 これらのリンクは、アプリケーション プロキシ経由で公開されるアプリで自動的に動作し、リンク変換の有無にかかわらず機能し続けます。 
- `http://expenses` のような他のオンプレミスのアプリまたは `http://expenses/logo.jpg` のような公開されたファイルへの **ハードコードされた内部リンク**。 リンク変換機能は、ハードコードされた内部リンクで機能し、リモート ユーザーが経由する必要がある外部 URL をポイントするように変更します。

アプリケーション プロキシがリンク変換をサポートする HTML コード タグの属性の完全な一覧には、以下のものが含まれます。
* a (href)
* audio (src)
* base (href)
* button (formaction)
* div (data-background、style、data-src)
* embed (src)
* form (action)
* frame (src)
* head (profile)
* html (manifest)
* iframe (longdesc、src)
* img (longdesc、src)
* input (formaction、src、value)
* link (href)
* menuitem (icon)
* meta (content)
* object (archive、data、codebase)
* script (src)
* source (src)
* track (src)
* video (src、poster)

さらに、CSS 内では URL 属性も変換されます。

### <a name="how-do-apps-link-to-each-other"></a>アプリが相互にリンクする方法

リンク変換は、アプリケーションごとに有効化されるため、アプリ レベルでユーザー エクスペリエンスを制御できます。 変換するアプリ "*への*" リンクではなく、そのアプリ "*から*" のリンクが必要な場合に、アプリのリンク変換をオンにします。 

たとえば、すべてが相互にリンクする、アプリケーション プロキシ経由で公開された 3 つのアプリケーション (福利厚生、経費、出張) があるとします。 アプリケーション プロキシ経由で公開されていないフィードバックという 4 番目のアプリがあります。

福利厚生アプリのリンク変換を有効にすると、経費と出張へのリンクは、それらのアプリの外部 URL にリダイレクトされますが、フィードバックへのリンクは外部 URL がないためリダイレクトされません。 経費と出張のアプリに対してはリンク変換が有効になっていないため、この 2 つのアプリから福利厚生へのリンクは機能しません。

![リンク変換が有効になっている場合の福利厚生から他のアプリへのリンク](./media/application-proxy-configure-hard-coded-link-translation/one_app.png)

### <a name="which-links-arent-translated"></a>変換されないリンク

パフォーマンスとセキュリティを向上させるために、一部のリンクは変換されません。

- コード タグ内にないリンク。 
- HTML や CSS の中にないリンク。 
- URL エンコード形式のリンク。
- 他のプログラムから開かれた内部リンク。 電子メールまたはインスタント メッセージで送信されたか、他のドキュメントに含まれているリンクは変換されません。 ユーザーは、外部 URL に移動するために知っておく必要があります。

この 2 つのシナリオのいずれかをサポートする必要がある場合は、リンク変換ではなく同じ内部および外部 URL を使用します。  

## <a name="enable-link-translation"></a>リンク変換の有効化

リンク変換の使用開始は、ボタンをクリックするのと同じくらい簡単です。

1. [Azure Portal](https://portal.azure.com) に管理者としてサインインします。
2. **[Azure Active Directory]**  >  **[エンタープライズ アプリケーション]**  >  **[すべてのアプリケーション]** に移動し、管理するアプリを選び、 **[アプリケーション プロキシ]** を選択します。
3. **[Translate URLs in application body]\(アプリケーション本文の URL を変換する\)** を **[はい]** にします。

   ![[はい] を選択してアプリケーション本文の URL を変換する](./media/application-proxy-configure-hard-coded-link-translation/select_yes.png)
4. **[保存]** を選択して変更を保存します。

これで、ユーザーがこのアプリケーションにアクセスすると、テナントのアプリケーション プロキシ経由で公開されている内部 URL がプロキシによって自動的にスキャンされます。

## <a name="send-feedback"></a>フィードバックの送信

この機能がすべてのアプリで動作するようにするためにご協力ください。 HTML と CSS では 30 以上のタグが検索されます。 変換されていないリンクが生成された例がある場合は、コード スニペットを[アプリケーション プロキシのフィードバック](mailto:aadapfeedback@microsoft.com)に送信してください。 

## <a name="next-steps"></a>次のステップ
[Azure AD アプリケーション プロキシとともにカスタム ドメインを使用](application-proxy-configure-custom-domain.md)して、同じ内部および外部 URL を含める

[SharePoint 2013 の代替アクセス マッピングを構成する](/SharePoint/administration/configure-alternate-access-mappings)
