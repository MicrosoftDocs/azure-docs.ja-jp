---
title: カスタムドメイン名を購入する
description: App Service ドメインを購入し、アプリ Azure App Service のカスタムドメインとして使用する方法について説明します。
ms.assetid: 70fb0e6e-8727-4cca-ba82-98a4d21586ff
ms.topic: article
ms.date: 11/30/2020
ms.custom: seodec18
ms.openlocfilehash: cdcf22a42375949cc4d6be0b4f3062cee26219d6
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/03/2021
ms.locfileid: "101704856"
---
# <a name="buy-a-custom-domain-name-for-azure-app-service"></a>Azure App Service のカスタム ドメイン名を購入する

App Service ドメインは、Azure で直接管理されるカスタム ドメインです。 App Service ドメインを使うと、[Azure App Service](overview.md) のカスタム ドメインの管理が容易になります。 このチュートリアルでは、App Service ドメインを購入し、Azure App Service に DNS 名を割り当てる方法を説明します。

Azure VM または Azure Storage については、「[Assign App Service domain to Azure VM or Azure Storage](https://azure.github.io/AppService/2017/07/31/Assign-App-Service-domain-to-Azure-VM-or-Azure-Storage)」(App Service ドメインを Azure VM または Azure Storage に割り当てる) を参照してください。 Cloud Services については、「[Azure クラウド サービスのカスタム ドメイン名の構成](../cloud-services/cloud-services-custom-domain-name-portal.md)」を参照してください。

## <a name="prerequisites"></a>前提条件

このチュートリアルを完了するには、以下が必要です。

* [App Service アプリを作成する](./index.yml)か、別のチュートリアルで作成したアプリを使用します。 アプリは Azure Public リージョンにある必要があります。 現時点では、Azure の各国のクラウドはサポートされていません。
* [サブスクリプションで使用制限を削除](../cost-management-billing/manage/spending-limit.md#remove)します。 無料のサブスクリプション クレジットで App Service ドメインを購入することはできません。

## <a name="buy-an-app-service-domain"></a>App Service ドメインを購入する

App Service ドメインの料金については、[App Service の価格に関するページ](https://azure.microsoft.com/pricing/details/app-service/windows/)にアクセスし、「App Service ドメイン」までスクロールしてください。

1. [Azure Portal](https://portal.azure.com) を開き、Azure アカウントでサインインします。

1. 検索バーで「**App Service ドメイン**」を検索して選択します。

    ![Azure App Service ドメインへのポータル ナビゲーション](./media/app-service-web-tutorial-custom-domain/view-app-service-domains.png)

1. **[App Service ドメイン]** ビューで、 **[追加]** をクリックします。

    ![[App Service ドメイン] で [追加] をクリックする](./media/app-service-web-tutorial-custom-domain/add-app-service-domain.png)

1. **[クリックして、新しいバージョンの App Service ドメインの作成エクスペリエンスをお試しください]** を選択します。

    ![新しいエクスペリエンスで App Service ドメインを作成する](./media/app-service-web-tutorial-custom-domain/select-new-create-experience.png)

### <a name="basics-tab"></a>[基本] タブ

1. **[基本]** タブで、次の表を使用して設定を構成します。  

   | 設定  | 説明 |
   | -------- | ----------- |
   | **サブスクリプション** | ドメインの購入に使用するサブスクリプション。 |
   | **リソース グループ** | ドメインを配置するリソース グループ。 たとえば、アプリが含まれるリソース グループなどです。 |
   | **ドメイン** | 目的のドメインを入力します。 たとえば、 **contoso.com** などのドメインです。 目的のドメインが使用できない場合は、使用可能なドメインの候補一覧から選択することや、別のドメインを試すことができます。 |

    > [!NOTE]
    > App Service ドメインでは、_com_、_net_、_co.uk_、_org_、_nl_、_in_、_biz_、_org.uk_、_co.in_ の各 [トップレベル ドメイン](https://wikipedia.org/wiki/Top-level_domain)がサポートされています。
    >
    >
    
2. 完了したら、 **[次へ:連絡先情報]\(次へ: 連絡先情報\)** をクリックします。

### <a name="contact-information-tab"></a>[連絡先情報] タブ

1. ドメイン登録のために [ICANN](https://go.microsoft.com/fwlink/?linkid=2116641) で必要とされる情報を入力します。 

    すべての必須フィールドにできるだけ正確に入力することが重要です。 連絡先情報のデータに誤りがあると、ドメインを購入できないことがあります。

1. 完了したら、 **[次へ: 高度]** を選択します。

### <a name="advanced-tab"></a>[詳細設定] タブ

1. **[詳細設定]** タブで、オプションの設定を構成します。  

   | 設定  | 説明 |
   | -------- | ----------- |
   | **自動更新** | 既定で有効です。 App Service ドメインが 1 年更新として登録されます。 自動更新により、ドメイン登録は期限切れにならず、ドメインの所有権が保持されます。 Azure サブスクリプションに対して、更新時に年単位のドメイン登録料金が自動的に課金されます。 オプトアウトするには、 **[無効にする]** を選択します。 自動更新が無効になっている場合は、[手動で更新](#renew-the-domain)できます。 |
   | **プライバシー保護** | 既定で有効です。 プライバシー保護により、WHOIS データベースで、ドメイン登録の連絡先情報が非表示になります。 プライバシー保護は、年単位のドメイン登録料金に既に含まれています。 オプトアウトするには、 **[無効にする]** を選択します。 |

2. 完了したら、**次へ: タグ** を選択します。

### <a name="finish"></a>[完了]

1. **[タグ]** タブで、App Service ドメインに使用するタグを設定します。 タグ付けは、App Service ドメインを使用するために必須ではありませんが、[リソースを管理するのに役立つ Azure の機能](../azure-resource-manager/management/tag-resources.md)です。

1. **[次へ: 確認と作成]** をクリックします。

1. **[確認と作成]** タブで、ドメインの注文を確認します。 完了したら、 **[作成]** をクリックします。

    > [!NOTE]
    > App Service ドメインはドメイン登録のために GoDaddy を使用し、Azure DNS を使用してドメインをホストします。 年単位のドメイン登録料に加えて、Azure DNS の使用料が適用されます。 詳しくは、「[Azure DNS の価格](https://azure.microsoft.com/pricing/details/dns/)」をご覧ください。
    >
    >

1. ドメインの登録が完了すると、 **[リソースに移動]** ボタンが表示されます。 これを選択すると、管理ページが表示されます。

    ![App Service ドメインが作成されました。 リソースに移動](./media/app-service-web-tutorial-custom-domain/deployment-complete.png)

これで、このカスタム ドメインに App Service アプリを割り当てる準備は完了です。

## <a name="prepare-the-app"></a>アプリの準備

Web アプリにカスタム DNS 名をマップするには、Web アプリの [App Service プラン](https://azure.microsoft.com/pricing/details/app-service/)が有料レベル (Azure Functions の Shared、Basic、Standard、Premium、または従量課金) である必要があります。 この手順では、App Service アプリがサポートされている価格レベルであることを確認します。

[!INCLUDE [app-service-dev-test-note](../../includes/app-service-dev-test-note.md)]

### <a name="navigate-to-the-app-in-the-azure-portal"></a>Azure Portal でアプリに移動する

1. 上部の検索バーで、「**App Services**」を検索して選択します。

    ![App Services を探す](./media/app-service-web-tutorial-custom-domain/app-services.png)

1. アプリの名前を選択します。

    ![Azure アプリへのポータル ナビゲーション](./media/app-service-web-tutorial-custom-domain/select-app.png)

    App Service アプリの管理ページが表示されます。  

### <a name="check-the-pricing-tier"></a>価格レベルの確認

1. アプリ ページの左側のナビゲーションで、 **[設定]** セクションまでスクロールし、 **[スケール アップ (App Service のプラン)]** を選択します。

    ![スケール アップ メニュー](./media/app-service-web-tutorial-custom-domain/scale-up-menu.png)

1. アプリの現在のレベルが青色の枠線で強調表示されます。 アプリが **F1** レベルに含まれていないことを確認します。 カスタム DNS は、**F1** レベルではサポートされていません。 

    :::image type="content" source="./media/app-service-web-tutorial-custom-domain/check-pricing-tier.png" alt-text="スケールアップ (App Service プラン) が選択されているアプリ ページの左側のナビゲーション メニューのスクリーンショット。":::

1. App Service プランが **F1** レベルではない場合は、 **[スケール アップ]** ページを閉じて、「[ドメインを購入する](#buy-an-app-service-domain)」に進みます。

### <a name="scale-up-the-app-service-plan"></a>App Service プランをスケール アップする

1. 非 Free レベルのいずれかを選びます (**D1**、**B1**、**B2**、**B3**、または **運用** カテゴリのいずれかのレベル)。 その他のオプションについては、 **[See additional options]\(その他のオプションを参照する\)** をクリックします。

1. **[Apply]** をクリックします。

    :::image type="content" source="./media/app-service-web-tutorial-custom-domain/choose-pricing-tier.png" alt-text="[運用] タブ、[B1 プラン]、[適用] ボタンが強調表示されている [運用] カテゴリのカスタム ドメイン価格レベルのスクリーンショット。":::

    次の通知が表示されたら、スケール操作は完了です。

    ![スケール操作の確認](./media/app-service-web-tutorial-custom-domain/scale-notification.png)

## <a name="map-app-service-domain-to-your-app"></a>App Service ドメインをアプリにマップする

同じサブスクリプション内にある限り、App Service ドメイン内のホスト名を App Service アプリにマップするのは簡単です。 App Service ドメインまたはそのサブドメインのいずれかをアプリに直接マップすると、Azure によって必要な DNS レコードが作成されます。

> [!NOTE]
> ドメインとアプリが異なるサブスクリプションに存在する場合は、[外部で購入したドメインをマップする](app-service-web-tutorial-custom-domain.md)のと同じように、App Service ドメインをアプリにマップします。 この場合、Azure DNS は外部ドメイン プロバイダーであり、[必要な DNS レコードを手動で追加する](#manage-custom-dns-records)必要があります。
>

### <a name="map-the-domain"></a>ドメインのマップ

1. アプリ ページの左側のナビゲーションで、 **[設定]** セクションまでスクロールし、 **[カスタム ドメイン]** を選択します。

    ![[カスタム ドメイン] メニューを示すスクリーンショット。](./media/app-service-web-tutorial-custom-domain/custom-domain-menu.png)

1. **[カスタム ドメインの追加]** を選択します。

    ![[ホスト名の追加] 項目を示すスクリーンショット。](./media/app-service-web-tutorial-custom-domain/add-host-name-cname.png)

1. App Service ドメイン (**contoso.com** など) またはサブドメイン (**www.contoso.com** など) を入力し、 **[検証]** をクリックします。

    > [!NOTE]
    > App Service のドメイン名に入力ミスがある場合、一部の DNS レコードが不足していることを示す検証エラーがページの下部に表示されます。 これらのレコードを App Service ドメインに手動で追加する必要はありません。 ドメイン名を確実に正しく入力し、 **[検証]** を再びクリックします。
    >
    > ![検証エラーを示すスクリーンショット。](./media/app-service-web-tutorial-custom-domain/verification-error-cname.png)

1. **[ホスト名レコード タイプ]** をそのまま使用し、 **[カスタム ドメインの追加]** をクリックします。

    ![[カスタム ドメインの追加] ボタンを示すスクリーンショット。](./media/app-service-web-tutorial-custom-domain/validate-domain-name-cname.png)

1. アプリの **[カスタム ドメイン]** ページに新しいカスタム ドメインが反映されるまで時間がかかることがあります。 ブラウザーを最新の情報に更新して、データを更新してみてください。

    ![CNAME レコードの追加を示すスクリーンショット。](./media/app-service-web-tutorial-custom-domain/cname-record-added.png)

    > [!NOTE]
    > カスタム ドメインの **[セキュリティ保護なし]** というラベルは、まだ TLS/SSL 証明書にバインドされていないことを意味します。 ブラウザーからカスタム ドメインへの HTTPS 要求を実行すると、ブラウザーに応じてエラーまたは警告が表示されます。 TLS バインドを追加するには、「[Azure App Service で TLS/SSL バインドを使用してカスタム DNS 名をセキュリティで保護する](configure-ssl-bindings.md)」を参照してください。
    
### <a name="test-the-custom-domain"></a>カスタム ドメインをテストする

カスタム ドメインをテストするには、ブラウザーでそれに移動します。

## <a name="renew-the-domain"></a>ドメインを更新する

購入した App Service ドメインは、購入時点から 1 年間有効です。 既定では、ドメインは、翌年の支払方法に課金することによって自動的に更新するように構成されています。 ドメイン名は手動で更新できます。

自動更新をオフにする場合やドメインを手動で更新する場合は、次の手順に従います。

1. 検索バーで「**App Service ドメイン**」を検索して選択します。

    ![Azure App Service ドメインへのポータル ナビゲーション](./media/app-service-web-tutorial-custom-domain/view-app-service-domains.png)

1. **[App Service ドメイン]** セクションで、構成するドメインを選択します。

1. ドメインの左側のナビゲーションで、 **[ドメインの更新]** を選択します。 ドメインの自動更新を停止するには、 **[オフ]** を選択します。 設定はすぐに反映されます。

    ![ドメインを自動的に更新するオプションを示すスクリーンショット。](./media/custom-dns-web-site-buydomains-web-app/dncmntask-cname-buydomains-autorenew.png)

    > [!NOTE]
    > ページ外に移動する際に、 **[OK]** をクリックして、"保存されていない編集は破棄されます" というエラーを無視します。
    >

ドメインを手動で更新するには、 **[ドメインの更新]** を選択します。 ただし、このボタンは、[ドメインの有効期限の 90 日前](#when-domain-expires)になるまではアクティブになりません。

ドメインの更新が成功すると、24 時間以内にメールの通知を受け取ります。

## <a name="when-domain-expires"></a>ドメインの有効期限が切れると

Azure は、有効期限が切れる、または有効期限が切れた App Service ドメインを次のように処理します。

* 自動更新が無効になっている場合: ドメインの有効期限が切れる 90 日前に、更新通知のメールがお客様に送信され、 **[ドメインの更新]** ボタンがポータルでアクティブになります。
* 自動更新が有効になっている場合: ドメインの有効期限満了日の翌日に、Azure は、ドメイン名更新の請求を試行します。
* 自動更新中にエラーが発生した (たとえば、記録にあるクレジット カードの有効期限が切れている) 場合、または自動更新が無効になっていて、ドメインを有効期限切れさせてしまった場合、Azure は、ドメインの有効期限満了をお客様に通知して、お使いのドメイン名を一時的にそのままにしておきます。 ドメイン名は、[手動で更新](#renew-the-domain)できます。
* 有効期限満了から 4 日目と 12 日目に、Azure は追加の通知メールを送信します。 ドメイン名は、[手動で更新](#renew-the-domain)できます。 有効期限満了から 5 日目には、期限切れのドメインの DNS 解決が停止します。
* 有効期限満了から 19 日目には、お使いのドメインは保留状態のままですが、償還費用の対象になります。 お客様は、カスタマー サポートに電話してお使いのドメイン名を更新できますが、その際、該当する更新費用と償還費用をお支払いいただくことになります。
* 有効期限満了から 25 日目には、Azure は、お使いのドメイン名を、ドメイン名業界のオークション サービスを使ってオークションに出品します。 お客様は、カスタマー サポートに電話してお使いのドメイン名を更新できますが、その際、該当する更新費用と償還費用をお支払いいただくことになります。
* 有効期限満了から 30 日目には、お使いのドメインは回復できなくなります。

<a name="custom"></a>

## <a name="manage-custom-dns-records"></a>カスタム DNS レコードを管理する

Azure では、App Service ドメインの DNS レコードは [Azure DNS](https://azure.microsoft.com/services/dns/) を使って管理されます。 外部から購入したドメインの場合と同じように、DNS レコードを追加、削除、更新できます。 カスタム DNS レコードを管理するには:

1. 検索バーで「**App Service ドメイン**」を検索して選択します。

    ![Azure App Service ドメインへのポータル ナビゲーション](./media/app-service-web-tutorial-custom-domain/view-app-service-domains.png)

1. **[App Service ドメイン]** セクションで、構成するドメインを選択します。

1. **[概要]** ページで **[DNS レコードの管理]** を選択します。

    ![DNS レコードにアクセスする場所を示すスクリーンショット。](./media/custom-dns-web-site-buydomains-web-app/dncmntask-cname-buydomains-dns-zone.png)

DNS レコードの編集方法について詳しくは、「[Azure Portal で DNS ゾーンを管理する方法](../dns/dns-operations-dnszones-portal.md)」をご覧ください。

## <a name="cancel-purchase-delete-domain"></a>購入を取り消す (ドメインを削除する)

App Service ドメイン購入後 5 日間は、購入をキャンセルできます。その場合、全額が返金されます。 5 日を過ぎると、App Service ドメインを削除することはできますが、返金を受け取ることはできません。

1. 検索バーで「**App Service ドメイン**」を検索して選択します。

    ![Azure App Service ドメインへのポータル ナビゲーション](./media/app-service-web-tutorial-custom-domain/view-app-service-domains.png)

1. **[App Service ドメイン]** セクションで、構成するドメインを選択します。

1. ドメインの左側のナビゲーションで、 **[ホスト名のバインド]** を選択します。 すべての Azure サービスのホスト名のバインドが一覧表示されます。

    ![[ホスト名のバインド] ページを示すスクリーンショット。](./media/custom-dns-web-site-buydomains-web-app/dncmntask-cname-buydomains-hostname-bindings.png)

1. **[...]**  >  **[削除]** の順に選んで、ホスト名のバインドを削除します。 すべてのバインドを削除した後、 **[保存]** を選びます。

    <!-- ![Screenshot that shows where to delete the hostname bindings.](./media/custom-dns-web-site-buydomains-web-app/dncmntask-cname-buydomains-delete-hostname-bindings.png) -->

1. ドメインの左側のナビゲーションで、 **[概要]** を選びます。 

1. 購入したドメインのキャンセル期間が経過していない場合は、 **[購入の取り消し]** を選びます。 経過している場合は、代わりに **[削除]** ボタンが表示されます。 返金を受け取らないでドメインを削除するには、 **[削除]** を選びます。

    ![購入したドメインを削除またはキャンセルする場所を示すスクリーンショット。](./media/custom-dns-web-site-buydomains-web-app/dncmntask-cname-buydomains-cancel.png)

1. **[はい]** を選択して操作を確定します。

    操作が完了すると、ドメインはサブスクリプションから解放され、他のユーザーが再び購入できるようになります。 

## <a name="direct-default-url-to-a-custom-directory"></a>既定の URL でカスタム ディレクトリを参照する

既定では、App Service は Web 要求をアプリ コードのルート ディレクトリに送信します。 それらを `public` などのサブディレクトリに送信する場合は、「[カスタム ディレクトリにリダイレクトする](app-service-web-tutorial-custom-domain.md#redirect-to-a-custom-directory)」を参照してください。

## <a name="next-steps"></a>次のステップ

カスタム TLS/SSL 証明書を App Service にバインドする方法を確認します。

> [!div class="nextstepaction"]
> [Azure App Service で TLS バインドを使用してカスタム DNS 名をセキュリティで保護する](configure-ssl-bindings.md)
