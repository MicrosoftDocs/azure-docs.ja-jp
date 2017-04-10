---
title: "Azure App Service でのカスタム ドメイン名の構成 (GoDaddy)"
description: "GoDaddy から購入したカスタム ドメイン名を Azure Web Apps で使用する方法"
services: app-service
documentationcenter: 
author: erikre
manager: erikre
editor: jimbe
ms.assetid: 33233e30-5846-488f-83f3-b32e5c114564
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/12/2016
ms.author: cephalin
translationtype: Human Translation
ms.sourcegitcommit: b1a633a86bd1b5997d5cbf66b16ec351f1043901
ms.openlocfilehash: fe8b4b097f7ff157d624c09b8dbf26f94401395c
ms.lasthandoff: 01/20/2017


---
# <a name="configure-a-custom-domain-name-in-azure-app-service-purchased-directly-from-godaddy"></a>Azure App Service でのカスタム ドメイン名の構成 (GoDaddy から直接購入した場合)
[!INCLUDE [web-selector](../../includes/websites-custom-domain-selector.md)]

[!INCLUDE [intro](../../includes/custom-dns-web-site-intro.md)]

Azure App Service Web Apps からドメインを購入した場合は、「 [Web アプリ用のドメインの購入](custom-dns-web-site-buydomains-web-app.md)」の最後の手順をご覧ください。

この記事では、[GoDaddy](https://godaddy.com) から直接購入したカスタム ドメイン名を [App Service Web Apps](http://go.microsoft.com/fwlink/?LinkId=529714) で使用する手順を示します。

[!INCLUDE [introfooter](../../includes/custom-dns-web-site-intro-notes.md)]

<a name="understanding-records"></a>

## <a name="understanding-dns-records"></a>DNS レコードについて
[!INCLUDE [understandingdns](../../includes/custom-dns-web-site-understanding-dns-raw.md)]

<a name="bkmk_configurecname"></a>

## <a name="add-a-dns-record-for-your-custom-domain"></a>カスタム ドメインの DNS レコードの追加
カスタム ドメインを App Service の Web アプリケーションに関連付けるには、GoDaddy のツールを使用して、新しいエントリをカスタム ドメインの DNS テーブルに追加する必要があります。 次の手順を使用して GoDaddy.com の DNS ツールを見つけます。

1. GoDaddy.com のアカウントにログオンし、**[My Account]**、**[Manage my domains]** の順に選択します。 最後に、Azure Web アプリで使用するドメイン名をドロップダウン メニューで選択し、 **[Manage DNS]**を選択します。
   
    ![GoDaddy のカスタム ドメイン ページ](./media/web-sites-godaddy-custom-domain-name/godaddy-customdomain.png)
2. **[Domain details]** ページの **[DNS Zone File]** タブまでスクロールします。 このセクションを使用して、ドメイン名の DNS レコードを追加したり変更したりします。
   
    ![[DNS Zone File] タブ](./media/web-sites-godaddy-custom-domain-name/godaddy-zonetab.png)
   
    既存のレコードを追加するには、 **[Add Record]** を選択します。
   
    既存のレコードを**編集**するには、レコードの横にあるペンと紙のアイコンを選択します。
   
   > [!NOTE]
   > 新しいレコードを追加する前に、GoDaddy では一般的なサブドメイン (Editor では **[Host]**) の DNS レコード (**[email]**、**[files]**、**[mail]** など) が既に作成されていることに注意してください。 使用する名前が既に存在する場合は、新しいレコードを作成せずに既存のレコードを変更してください。
   > 
   > 
3. レコードを追加する場合は、まずレコードの種類を選択する必要があります。
   
    ![レコードの種類の選択](./media/web-sites-godaddy-custom-domain-name/godaddy-selectrecordtype.png)
   
    次に、**[Host]** (カスタム ドメインまたはサブドメイン) および **[Points to]** を指定する必要があります。
   
    ![ゾーン レコードの追加](./media/web-sites-godaddy-custom-domain-name/godaddy-addzonerecord.png)
   
   * **A (ホスト) レコード**を追加するときは、**[Host]** フィールドを、**@** (**contoso.com** などのルート ドメイン名)、* (複数のサブドメインに一致するワイルドカード)、または使用するサブドメイン (**www** など) のいずれかに設定する必要があります。また、**[Points to]** フィールドを、Azure Web アプリの IP アドレスに設定する必要があります。
   * **CNAME (エイリアス) レコード**を追加するときは、**[Host]** フィールドを、使用するサブドメインに設定する必要があります。 たとえば **www**にします。 **[Points to]** フィールドを、Azure Web アプリの **.azurewebsites.net** ドメイン名に設定する必要があります。 たとえば、 **contoso.azurewebsites.net**にします。
4. **[Add Another]**をクリックします。
5. レコードの種類として **[TXT]** を選択し、**[Host]** の値に「**@**」を、**[Points to]** の値に「**&lt;Web アプリ名&gt;.azurewebsites.net**」を指定します。
   
   > [!NOTE]
   > この TXT レコードは、Azure で A レコードまたは最初の TXT レコードで記述されたドメインの所有者であることの検証に使用されます。 ドメインが Azure ポータルで Web アプリにマップされた後で、この TXT レコードエントリを削除できます。
   > 
   > 
6. レコードの追加または変更が完了したら、 **[Finish]** をクリックして変更を保存します。

<a name="enabledomain"></a>

## <a name="enable-the-domain-name-on-your-web-app"></a>Web アプリケーションでのドメイン名の有効化
[!INCLUDE [modes](../../includes/custom-dns-web-site-enable-on-web-site.md)]

> [!NOTE]
> Azure アカウントにサインアップする前に Azure App Service の使用を開始したい場合は、「[Azure App Service アプリケーションの作成](https://azure.microsoft.com/try/app-service/)」を参照してください。そこでは、App Service で有効期間の短いスターター Web アプリをすぐに作成できます。 このサービスの利用にあたり、クレジット カードは必要ありません。契約も必要ありません。
> 
> 

## <a name="whats-changed"></a>変更内容
* Websites から App Service への変更ガイドについては、「 [Azure App Service と既存の Azure サービス](http://go.microsoft.com/fwlink/?LinkId=529714)


