---
title: "負荷分散に Traffic Manager を使用する Azure App Service Web アプリのカスタム ドメイン名を構成します。"
description: "負荷分散のための Traffic Manager を含む Azure App Service Web アプリのカスタム ドメイン名の使用"
services: app-service\web
documentationcenter: 
author: rmcmurray
manager: erikre
editor: 
ms.assetid: 0f96c0e7-0901-489b-a95a-e3b66ca0a1c2
ms.service: app-service-web
ms.workload: web
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/22/2016
ms.author: robmcm
translationtype: Human Translation
ms.sourcegitcommit: ff60ebaddd3a7888cee612f387bd0c50799496ac
ms.openlocfilehash: 532a953c02ef40eb55705eac885d74c5574deed5
ms.lasthandoff: 01/05/2017


---
# <a name="configuring-a-custom-domain-name-for-a-web-app-in-azure-app-service-using-traffic-manager"></a>Traffic Manager を使用して Azure App Service Web アプリのカスタム ドメイン名を構成する
[!INCLUDE [web-selector](../../includes/websites-custom-domain-selector.md)]

[!INCLUDE [intro](../../includes/custom-dns-web-site-intro-traffic-manager.md)]

この記事では、負荷分散に Traffic Manager を利用する Azure App Service でカスタム ドメイン名を使用する一般的な手順を示します。

[!INCLUDE [tmwebsitefooter](../../includes/custom-dns-web-site-traffic-manager-notes.md)]

[!INCLUDE [introfooter](../../includes/custom-dns-web-site-intro-notes.md)]

<a name="understanding-records"></a>

## <a name="understanding-dns-records"></a>DNS レコードについて
[!INCLUDE [understandingdns](../../includes/custom-dns-web-site-understanding-dns-traffic-manager.md)]

<a name="bkmk_configsharedmode"></a>

## <a name="configure-your-web-apps-for-standard-mode"></a>Web アプリの標準モード用の構成
[!INCLUDE [modes](../../includes/custom-dns-web-site-modes-traffic-manager.md)]

<a name="bkmk_configurecname"></a>

## <a name="add-a-dns-record-for-your-custom-domain"></a>カスタム ドメインの DNS レコードの追加
> [!NOTE]
> Azure App Service Web Apps からドメインを購入した場合は、次の手順をスキップして、「 [Web Apps 用のドメインの購入](custom-dns-web-site-buydomains-web-app.md) 」の記事の最後の手順をご覧ください。
> 
> 

カスタム ドメインを Azure App Service の Web アプリに関連付けるには、ドメイン名を購入したドメイン レジストラーのツールを使用して、新しいエントリをカスタム ドメインの DNS テーブルに追加する必要があります。 次の手順を使用して DNS ツールを見つけて利用します。

1. ドメイン レジストラーのアカウントにサインインし、DNS レコードの管理ページを探します。 **[ドメイン名]**、**[DNS]**、**[ネーム サーバー管理]** というラベルが付いたサイトのリンクまたは領域を探します。 多く場合、このページへのリンクが見つかったら、アカウント情報を表示し、 **[My domains]**などのリンクを探します。
2. ドメイン名の管理ページが見つかったら、DNS レコードを編集できるリンクを探します。 これは、**[ゾーン ファイル]**、**[DNS レコード]**、**[詳細]** などの構成リンクとして表示されている場合があります。
   
   * このページには多くの場合、いくつかのレコードが既に作成されています。たとえば、'**@**' or '\*' を "ドメイン パーキング" ページに関連付けるエントリが既にあります。 **www** のような一般的なサブドメインのレコードが含まれることもあります。
   * このページには **CNAME レコード**が表示されるか、レコード タイプを選択するためのドロップダウンが表示されます。 さらに、**A レコード**や **MX レコード**などの他のレコードが表示されることもあります。 場合によっては、CNAME レコードは **Alias Record**などの別の名前で呼ばれています。
   * このページには、**ホスト名**または**ドメイン名**から別のドメイン名に**マッピング**できるフィールドも表示されます。
3. レジストラーによって仕様が異なりますが、一般的には、カスタム ドメイン名 (**contoso.com** など) "*から*"、Web アプリに利用される Traffic Manager のドメイン名 (**contoso.trafficmanager.net など**) "*に*" マッピングします。
   
   > [!NOTE]
   > レコードが既に使用されており、事前にアプリをバインドする必要がある場合は、追加の CNAME レコードを作成できます。 たとえば、**www.contoso.com** を Web アプリに事前にバインドするには、**awverify.www** から **contoso.trafficmanager.net** への CNAME レコードを作成します。 その後、"www" CNAME レコードに変更を加えることなく、"www.contoso.com" を Web アプリに追加できます。 詳細については、「[カスタム ドメインにおける Web アプリの DNS レコードの作成][CREATEDNS]」をご覧ください。
   > 
   > 
4. レジストラーで DNS レコードの追加または変更が完了したら、変更を保存します。

<a name="enabledomain"></a>

## <a name="enable-traffic-manager"></a>Traffic Manager を有効にする
[!INCLUDE [modes](../../includes/custom-dns-web-site-enable-on-traffic-manager.md)]

## <a name="next-steps"></a>次のステップ
詳細については、 [Node.js デベロッパー センター](/develop/nodejs/)を参照してください。

[!INCLUDE [app-service-web-whats-changed](../../includes/app-service-web-whats-changed.md)]

[!INCLUDE [app-service-web-try-app-service](../../includes/app-service-web-try-app-service.md)]

<!-- URL List -->

[CREATEDNS]: ../dns/dns-web-sites-custom-domain.md

