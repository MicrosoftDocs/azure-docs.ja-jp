---
title: "Azure App Service へのアクティブなカスタム ドメインの移行 | Microsoft Docs"
description: "既にライブ サイトに割り当てられているカスタム ドメインを、ダウンタイムを発生させずに Azure App Service のアプリに移行する方法について説明します。"
services: app-service
documentationcenter: 
author: cephalin
manager: erikre
editor: jimbe
tags: top-support-issue
ms.assetid: 10da5b8a-1823-41a3-a2ff-a0717c2b5c2d
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/30/2017
ms.author: cephalin
translationtype: Human Translation
ms.sourcegitcommit: aea3e1bae3695636ec0f9bb347dbe205f701684e
ms.openlocfilehash: 4f1b4c630cd1e676db4f8b512a8973ebee6f6eba
ms.lasthandoff: 02/16/2017


---
# <a name="migrate-an-active-custom-domain-to-azure-app-service"></a>Azure App Service へのアクティブなカスタム ドメインの移行

この記事では、ダウンタイムを発生させずにアクティブなカスタム ドメインを [Azure App Service](../app-service/app-service-value-prop-what-is.md) に移行する方法について説明します。

ライブ サイトと、ライブ トラフィックを既に提供しているそのドメイン名を App Service に移行する際、移行プロセス中に DNS 解決にダウンタイムが生じないようにしたいとします。 この場合、事前にドメイン名を Azure アプリにバインドしてドメイン検証を行う必要があります。 

## <a name="prerequisites"></a>前提条件

この記事では、[App Service にカスタム ドメインを手動でマップする](web-sites-custom-domain-name.md)方法を把握していることを前提とします。

## <a name="bind-the-domain-name-preemptively"></a>ドメイン名の事前バインド

カスタム ドメインを事前にバインドする場合は、DNS レコードに変更を加える前に、次に示す両方の作業を実行します。

- ドメインの所有権を検証する
- アプリのドメイン名を有効にする

最後に App Service アプリを指すように DNS レコードを変更すると、クライアントが古いサイトから App Service アプリにリダイレクトされます。DNS 解決でダウンタイムは発生しません。

次の手順に従ってください。

1. 最初に、「[DNS レコードを作成する](web-sites-custom-domain-name.md#createdns)」の手順に従って、DNS レジストリで検証用 TXT レコードを作成します。
追加の TXT レコードでは、&lt;*subdomain*>.&lt;*rootdomain*> を &lt;*appname*>.azurewebsites.net にマップする規則が適用されます。
例については、次の表を参照してください。  
 
    <table cellspacing="0" border="1">
    <tr>
    <th>FQDN の例</th>
    <th>TXT ホスト</th>
    <th>TXT 値</th>
    </tr>
    <tr>
    <td>contoso.com (ルート)</td>
    <td>awverify.contoso.com</td>
    <td>&lt;<i>appname</i>>.azurewebsites.net</td>
    </tr>
    <tr>
    <td>www.contoso.com (サブ)</td>
    <td>awverify.www.contoso.com</td>
    <td>&lt;<i>appname</i>>.azurewebsites.net</td>
    </tr>
    <tr>
    <td>\*.contoso.com (ワイルドカード)</td>
    <td>awverify.\*.contoso.com</td>
    <td>&lt;<i>appname</i>>.azurewebsites.net</td>
    </tr>
    </table>

2. 次に、「[アプリのカスタム ドメイン名を有効にする](web-sites-custom-domain-name.md#enable)」の手順に従って、Azure アプリにカスタム ドメイン名を追加します。

    カスタム ドメインが Azure アプリで有効になります。 残りの作業は、ドメイン レジストラーで DNS レコードを更新することだけです。

3. 最後に、「[DNS レコードを作成する](web-sites-custom-domain-name.md#createdns)」の手順に従って、Azure アプリを指すようにドメインの DNS レコードを更新します。 

    DNS に反映されるとすぐに、ユーザー トラフィックが Azure アプリにリダイレクトされます。

## <a name="next-steps"></a>次のステップ
HTTPS でカスタム ドメイン名をセキュリティ保護する方法を確認します。そのためには、[Azure で SSL 証明書を購入](web-sites-purchase-ssl-web-site.md)するか、[別の場所からの SSL 証明書を使用](web-sites-configure-ssl-certificate.md)します。

> [!NOTE]
> Azure アカウントにサインアップする前に Azure App Service の使用を開始したい場合は、「[Azure App Service アプリケーションの作成](https://azure.microsoft.com/try/app-service/)」を参照してください。そこでは、App Service で有効期間の短いスターター Web アプリをすぐに作成できます。 このサービスの利用にあたり、クレジット カードは必要ありません。契約も必要ありません。
> 
> 

[Azure DNS の概要](../dns/dns-getstarted-create-dnszone.md)  
[カスタム ドメインにおける Web アプリの DNS レコードの作成](../dns/dns-web-sites-custom-domain.md)  
[Azure DNS へのドメインの委任](../dns/dns-domain-delegation.md)


