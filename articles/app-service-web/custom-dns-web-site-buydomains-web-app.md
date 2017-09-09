---
title: "Azure Web Apps のカスタム ドメイン名を購入する"
description: "Azure App Service の Web アプリでカスタム ドメイン名を購入する方法について説明します。"
services: app-service\web
documentationcenter: 
author: cephalin
manager: cfowler
editor: 
ms.assetid: 70fb0e6e-8727-4cca-ba82-98a4d21586ff
ms.service: app-service-web
ms.workload: web
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/17/2016
ms.author: cephalin
ms.translationtype: HT
ms.sourcegitcommit: 8b857b4a629618d84f66da28d46f79c2b74171df
ms.openlocfilehash: 44d350d2d098be14ad254066a8528fe117200ec4
ms.contentlocale: ja-jp
ms.lasthandoff: 08/04/2017

---
# <a name="buy-a-custom-domain-name-for-azure-web-apps"></a>Azure Web Apps のカスタム ドメイン名を購入する

App Service ドメイン (プレビュー) は、Azure で直接管理されるトップレベル ドメインです。 App Service ドメインを使うと、[Azure Web Apps](app-service-web-overview.md) のカスタム ドメインの管理が容易になります。 このチュートリアルでは、App Service ドメインを購入し、Azure Web Apps に DNS 名を割り当てる方法を説明します。

この記事は、Azure App Service (Web Apps、API Apps、Mobile Apps、Logic Apps) を対象としています。 Azure VM または Azure Storage については、「[Assign App Service domain to Azure VM or Azure Storage](https://blogs.msdn.microsoft.com/appserviceteam/2017/07/31/assign-app-service-domain-to-azure-vm-or-azure-storage/)」(App Service ドメインを Azure VM または Azure Storage に割り当てる) を参照してください。 Cloud Services については、「[Azure クラウド サービスのカスタム ドメイン名の構成](../cloud-services/cloud-services-custom-domain-name-portal.md)」を参照してください。

## <a name="prerequisites"></a>前提条件

このチュートリアルを完了するには、以下が必要です。

* [App Service アプリを作成する](/azure/app-service/)か、別のチュートリアルで作成したアプリを使用します。

## <a name="prepare-the-app"></a>アプリの準備

Azure Web Apps でカスタム ドメインを使うには、Web アプリの [App Service プラン](https://azure.microsoft.com/pricing/details/app-service/)が有料レベル (**Shared**、**Basic**、**Standard**、または **Premium**) である必要があります。 この手順では、Web アプリが、サポートされている価格レベルであることを確認します。

### <a name="sign-in-to-azure"></a>Azure へのサインイン

[Azure Portal](https://portal.azure.com) を開き、Azure アカウントでサインインします。

### <a name="navigate-to-the-app-in-the-azure-portal"></a>Azure Portal でアプリに移動する

左側のメニューで、**[App Services]** を選択し、アプリの名前をクリックします。

![Azure アプリへのポータル ナビゲーション](./media/app-service-web-tutorial-custom-domain/select-app.png)

App Service アプリの管理ページが表示されます。  

### <a name="check-the-pricing-tier"></a>価格レベルの確認

アプリ ページの左側のナビゲーションで、**[設定]** セクションまでスクロールし、**[スケール アップ (App Service のプラン)]** を選択します。

![スケール アップ メニュー](./media/app-service-web-tutorial-custom-domain/scale-up-menu.png)

アプリの現在のレベルが青色の枠線で強調表示されます。 アプリが **Free** レベルに含まれていないことを確認します。 カスタム DNS は、**Free** レベルではサポートされていません。 

![価格レベルの確認](./media/app-service-web-tutorial-custom-domain/check-pricing-tier.png)

App Service プランが **Free** ではない場合は、**[価格レベルの選択]** ページを閉じて、「[ドメインの購入](#buy-the-domain)」に進んでください。

### <a name="scale-up-the-app-service-plan"></a>App Service プランをスケール アップする

Free 以外のレベル (**Shared**、**Basic**、**Standard**、または **Premium**) を選択します。 

**[選択]** をクリックします。

![価格レベルの確認](./media/app-service-web-tutorial-custom-domain/choose-pricing-tier.png)

次の通知が表示されたら、スケール操作は完了です。

![スケール操作の確認](./media/app-service-web-tutorial-custom-domain/scale-notification.png)

## <a name="buy-the-domain"></a>ドメインを購入する

### <a name="sign-in-to-azure"></a>Azure へのサインイン
[Azure Portal](https://portal.azure.com/) を開き、Azure アカウントでサインインします。

### <a name="launch-buy-domains"></a>ドメインの購入を開始する
**[Web Apps]** タブで、Web アプリの名前をクリックし、**[設定]**、**[カスタム ドメイン]** の順に選択します。
   
![](./media/custom-dns-web-site-buydomains-web-app/dncmntask-cname-6.png)

**[カスタム ドメイン]** ページで **[ドメインの購入]** をクリックします。

![](./media/custom-dns-web-site-buydomains-web-app/dncmntask-cname-buydomains-1.png)

### <a name="configure-the-domain-purchase"></a>ドメインの購入を構成する

**[App Service ドメイン]** ページの **[ドメインの検索]** ボックスに購入するドメイン名を入力して、`Enter` キーを押します。 推奨される使用可能なドメインがテキスト ボックスの下に表示されます。 購入するドメインを 1 つ以上選びます。 
   
![](./media/custom-dns-web-site-buydomains-web-app/dncmntask-cname-buydomains-2.png)

**[連絡先情報]** をクリックし、ドメインの連絡先情報フォームに入力します。 終了したら、**[OK]** をクリックして [App Service ドメイン] ページに戻ります。
   
すべての必須フィールドにできるだけ正確に入力することが重要です。 連絡先情報に誤ったデータを入力した場合は、ドメインを購入できないことがあります。 

次に、ドメインに必要なオプションを選びます。 次の表の説明をご覧ください。

| 設定 | 推奨値 | 説明 |
|-|-|-|
|自動更新 | **有効にする** | App Service ドメインを毎年自動的に更新します。 更新時には、同じ購入価格がクレジット カードに課金されます。 |
|プライバシー保護 | 有効化 | "プライバシー保護" にオプトインします。これは、購入価格に含まれており "_無料_" です (_.co.in_ や _.co.uk_ など、レジストリがプライバシー保護をサポートしていないトップレベル ドメインを除きます)。 |
| 既定のホスト名の割り当て | **www** および **@** | 必要に応じて、適切なホスト名バインドを選びます。 ドメイン購入操作が完了すると、選んだホスト名で Web アプリにアクセスできるようになります。 Web アプリが [Azure Traffic Manager](https://azure.microsoft.com/services/traffic-manager/) の背後にある場合、Traffic Manager は A レコードをサポートしていないため、ルート ドメイン (@) を割り当てるオプションは表示されません。 ドメインの購入が完了した後で、ホスト名の割り当てを変更することができます。 |

### <a name="accept-terms-and-purchase"></a>ライセンス条項に同意して購入する

**[法律条項]** をクリックして条項と料金を確認した後、**[購入]** をクリックします。

> [!NOTE]
> App Service ドメインは、Azure DNS を使ってドメインをホストします。 ドメイン登録手数料だけでなく、Azure DNS の使用料が適用されます。 詳しくは、「[Azure DNS の価格](https://azure.microsoft.com/pricing/details/dns/)」をご覧ください。
>
>

**[App Service ドメイン]** ページに戻り、**[OK]** をクリックします。 操作が進行している間、次の通知が表示されます。

![](./media/custom-dns-web-site-buydomains-web-app/dncmntask-cname-buydomains-validate.png)

![](./media/custom-dns-web-site-buydomains-web-app/dncmntask-cname-buydomains-purchase-success.png)

### <a name="test-the-hostnames"></a>ホスト名をテストする

Web アプリに既定のホスト名を割り当てた場合、選択した各ホスト名に対する成功通知も表示されます。 

![](./media/custom-dns-web-site-buydomains-web-app/dncmntask-cname-buydomains-bind-success.png)

選んだホスト名は、**[カスタム ドメイン]** ページの **[ホスト名]** セクションにも表示されます。 

![](./media/custom-dns-web-site-buydomains-web-app/dncmntask-cname-buydomains-hostnames-added.png)

ホスト名をテストするには、一覧表示されているホスト名にブラウザーで移動します。 前掲のスクリーンショットの例では、_kontoso.net_ と _www.kontoso.net_ に移動してみます。

## <a name="assign-hostnames-to-web-app"></a>Web アプリにホスト名を割り当てる

購入処理の間に Web アプリに既定のホスト名を割り当てなかった場合、または一覧にないホスト名を割り当てる必要がある場合は、いつでもホスト名を割り当てることができます。

また、App Service ドメインのホスト名を他の Web アプリに割り当てることもできます。 手順は、App Service ドメインと Web アプリが同じサブスクリプションに属しているかどうかによって異なります。

- 異なるサブスクリプション: 外部から購入したドメインと同様に、App Service ドメインのカスタム DNS レコードを Web アプリにマップします。 App Service ドメインにカスタム DNS 名を追加する方法については、「[カスタム DNS レコードを管理する](#custom)」をご覧ください。 外部で購入したドメインを Web アプリにマップする方法については、「[既存のカスタム DNS 名を Azure Web Apps にマップする](app-service-web-tutorial-custom-domain.md)」をご覧ください。 
- 同じサブスクリプション: 次の手順のようにします。

### <a name="launch-add-hostname"></a>ホスト名の追加を開始する
**[App Services]** ページで、ホスト名を割り当てる Web アプリの名前を選び、**[設定]**、**[カスタム ドメイン]** の順に選びます。

![](./media/custom-dns-web-site-buydomains-web-app/dncmntask-cname-6.png)

購入したドメインが **[App Service ドメイン]** セクションの一覧に表示されていることを確認します。ただし、まだ選択しないでください。 

![](./media/custom-dns-web-site-buydomains-web-app/dncmntask-cname-buydomains-select-domain.png)

> [!NOTE]
> 同じサブスクリプションに含まれるすべての App Service ドメインが、Web アプリの **[カスタム ドメイン]** ページに表示されます。 ドメインが Web アプリのサブスクリプションに含まれるのに、Web アプリの **[カスタム ドメイン]** ページに表示されない場合は、**[カスタム ドメイン]** ページを開きなおすか、Web ページを更新してみてください。 また、Azure Portal の上部にある通知ベルで、進捗状況または作成エラーを確認してください。
>
>

**[ホスト名の追加]** を選択します。

### <a name="configure-hostname"></a>ホスト名を構成する
**[ホスト名の追加]** ダイアログ ボックスで、App Service ドメインまたは任意のサブドメインの完全修飾ドメイン名を入力します。 次に例を示します。

- kontoso.net
- www.kontoso.net
- abc.kontoso.net

終わったら、**[検証]** を選びます。 ホスト名のレコードの種類が自動的に選ばれます。

**[ホスト名の追加]** を選択します。

操作が終わると、割り当てられたホスト名の成功通知が表示されます。  

![](./media/custom-dns-web-site-buydomains-web-app/dncmntask-cname-buydomains-bind-success.png)

### <a name="close-add-hostname"></a>ホスト名の追加を閉じる
**[ホスト名の追加]** ページで、必要に応じて他のホスト名を Web アプリに割り当てます。 終わったら、**[ホスト名の追加]** ページを閉じます。

新しく割り当てたホスト名がアプリの **[カスタム ドメイン]** ページに表示されます。

![](./media/custom-dns-web-site-buydomains-web-app/dncmntask-cname-buydomains-hostnames-added2.png)

### <a name="test-the-hostnames"></a>ホスト名をテストする

一覧表示されているホスト名にブラウザーで移動します。 前掲のスクリーンショットの例では、_abc.kontoso.net_ に移動してみます。

<a name="custom" />

## <a name="manage-custom-dns-records"></a>カスタム DNS レコードを管理する

Azure では、App Service ドメインの DNS レコードは [Azure DNS](https://azure.microsoft.com/services/dns/) を使って管理されます。 外部から購入したドメインの場合と同じように、DNS レコードを追加、削除、更新できます。

### <a name="open-app-service-domain"></a>App Service ドメインを開く

Azure Portal の左側のメニューから、**[その他のサービス]** > **[App Service ドメイン]** の順に選びます。

![](./media/custom-dns-web-site-buydomains-web-app/dncmntask-cname-buydomains-access.png)

管理するドメインを選びます。 

### <a name="access-dns-zone"></a>DNS ゾーンにアクセスする

ドメインの左側のメニューで、**[DNS ゾーン]** を選びます。

![](./media/custom-dns-web-site-buydomains-web-app/dncmntask-cname-buydomains-dns-zone.png)

Azure DNS に格納されている App Service ドメインの [[DNS ゾーン]](../dns/dns-zones-records.md) ページが開きます。 DNS レコードの編集方法について詳しくは、「[Azure Portal で DNS ゾーンを管理する方法](../dns/dns-operations-dnszones-portal.md)」をご覧ください。

## <a name="cancel-purchase-delete-domain"></a>購入を取り消す (ドメインを削除する)

App Service ドメイン購入後 5 日間は、購入をキャンセルできます。その場合、全額が返金されます。 5 日を過ぎると、App Service ドメインを削除することはできますが、返金を受け取ることはできません。

### <a name="open-app-service-domain"></a>App Service ドメインを開く

Azure Portal の左側のメニューから、**[その他のサービス]** > **[App Service ドメイン]** の順に選びます。

![](./media/custom-dns-web-site-buydomains-web-app/dncmntask-cname-buydomains-access.png)

キャンセルまたは削除するドメインを選びます。 

### <a name="delete-hostname-bindings"></a>ホスト名のバインドの削除

ドメインの左側のメニューで、**[ホスト名のバインド]** を選びます。 すべての Azure サービスのホスト名のバインドが一覧表示されます。

![](./media/custom-dns-web-site-buydomains-web-app/dncmntask-cname-buydomains-hostname-bindings.png)

ホスト名のバインドをすべて削除するまで、App Service ドメインを削除することはできません。

**[...]** > **[削除]** の順に選んで、ホスト名のバインドを削除します。 すべてのバインドを削除した後、**[保存]** を選びます。

![](./media/custom-dns-web-site-buydomains-web-app/dncmntask-cname-buydomains-delete-hostname-bindings.png)

### <a name="cancel-or-delete"></a>キャンセルまたは削除する

ドメインの左側のメニューで、**[概要]** を選びます。 

購入したドメインのキャンセル期間が経過していない場合は、**[購入の取り消し]** を選びます。 経過している場合は、代わりに **[削除]** ボタンが表示されます。 返金を受け取らないでドメインを削除するには、**[削除]** を選びます。

![](./media/custom-dns-web-site-buydomains-web-app/dncmntask-cname-buydomains-cancel.png)

**[OK]** を選び、操作を確定します。 操作を続けない場合は、確認ダイアログの外側のどこかをクリックします。

操作が完了すると、ドメインはサブスクリプションから解放され、他のユーザーが再び購入できるようになります。 

