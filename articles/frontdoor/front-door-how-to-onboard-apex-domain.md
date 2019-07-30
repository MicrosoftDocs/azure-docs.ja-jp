---
title: Azure portal を使用して既存のフロント ドアにルート ドメインまたは頂点ドメインをオンボードする
description: Azure portal を使用して既存のフロント ドアにルート ドメインまたは頂点ドメインをオンボードする方法を説明します。
services: front-door
author: sharad4u
ms.service: frontdoor
ms.topic: article
ms.date: 5/21/2019
ms.author: sharadag
ms.openlocfilehash: 8fe8da95a61d2f2bb35095236131670cb6ef0e70
ms.sourcegitcommit: f10ae7078e477531af5b61a7fe64ab0e389830e8
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/05/2019
ms.locfileid: "67605794"
---
# <a name="onboard-a-root-or-apex-domain-on-your-front-door"></a>フロント ドアにルート ドメインまたは頂点ドメインをオンボードする
Azure Front Door では、CNAME レコードを使用して、カスタム ドメインのオンボードに対するドメインの所有権が検証されます。 また、Front Door では、Front Door プロファイルに関連付けられているフロントエンド IP アドレスが公開されないため、Azure Front Door にオンボードすることが目的である場合、頂点ドメインを IP アドレスにマップできません。

DNS プロトコルでは、ゾーンの頂点での CNAME レコードの割り当てができません。 たとえば、ドメインが `contoso.com` の場合、`somelabel.contoso.com` に対する CNAME レコードは作成できますが、`contoso.com` 自体に対する CNAME を作成することはできません。 Azure Front Door の背後でアプリケーションの負荷分散を行っているアプリケーションの所有者にとっては、この制限によって問題が生じます。 Front Door プロファイルを使用するには CNAME レコードを作成する必要があるため、ゾーンの頂点から Front Door プロファイルをポイントすることはできません。

この問題は、Azure DNS でエイリアス レコードを使用すると解決されます。 CNAME レコードとは異なり、エイリアス レコードはゾーンの頂点で作成され、アプリケーションの所有者はそれを使用して、パブリック エンドポイントを含む Front Door プロファイルを、ゾーンの頂点のレコードでポイントできます。 アプリケーションの所有者は、DNS ゾーン内の他のドメインで使用されているのと同じ Front Door プロファイルをポイントします。 たとえば、`contoso.com` と `www.contoso.com` で、同じ Front Door プロファイルをポイントできます。 

基本的に、Front Door プロファイルに頂点ドメインまたはルート ドメインをマッピングするには、CNAME のフラット化または DNS の追跡が必要です。これは、DNS プロバイダーにおいて IP アドレスにヒットするまで CNAME エントリが再帰的に解決されるメカニズムです。 この機能は、Front Door エンドポイント用に Azure DNS でサポートされています。 

> [!NOTE]
> CNAME のフラット化または DNS の追跡をサポートする DNS プロバイダーは他にもありますが、Azure Front Door では、お客様がドメインをホストする場合は Azure DNS を使うことが推奨されます。

Azure portal を使って、頂点ドメインをお使いのフロント ドアにオンボードし、SSL 終了用の証明書とそれを関連付けることによって HTTPS を有効にできます。 頂点ドメインは、ルート ドメインまたはネイキッド ドメインとも呼ばれます。

この記事では、次のことについて説明します。

> [!div class="checklist"]
> * Front Door プロファイルをポイントするエイリアス レコードを作成する
> * ルート ドメインをフロント ドアに追加する
> * ルート ドメインで HTTPS を設定する

> [!NOTE]
> このチュートリアルでは、Front Door プロファイルが既に作成されている必要があります。 他のチュートリアル ([クイックスタート: フロント ドアの作成](./quickstart-create-front-door.md)または [HTTP から HTTPS にリダイレクトするフロント ドアの作成](./front-door-how-to-redirect-https.md)に関する記事) で概要を参照してください。

## <a name="create-an-alias-record-for-zone-apex"></a>ゾーンの頂点に対するエイリアス レコードを作成する

1. オンボード対象のドメインの **Azure DNS** 構成を開きます。
2. ゾーンの頂点のレコードを作成または編集します。
3. レコードの**種類**として _A_ を選択し、 **[Alias record set]\(エイリアス レコード セット\)** で _[はい]_ を選択します。 **[Alias type]\(エイリアスの種類\)** は _[Azure resource]\(Azure リソース\)_ に設定する必要があります。
4. Front Door プロファイルがホストされる Azure サブスクリプションを選択し、 **[Azure resource]\(Azure リソース\)** ドロップダウンから Front Door リソースを選択します。
5. **[OK]** をクリックして変更を送信します。

    ![ゾーンの頂点に対するエイリアス レコード](./media/front-door-apex-domain/front-door-apex-alias-record.png)

6. 上のステップでは、Front Door リソースをポイントするゾーンの頂点のレコードと、Front Door プロファイルへのドメインのオンボードに使用される `afdverify.<name>.azurefd.net` に "afdverify" (例: `afdverify.contosonews.com`) をマップする CNAME レコードが作成されます。

## <a name="onboard-the-custom-domain-on-your-front-door"></a>カスタム ドメインをフロント ドアにオンボードする

1. [Front Door デザイナー] タブで、[フロントエンド ホスト] セクションの [+] アイコンをクリックして、新しいカスタム ドメインを追加します。
2. [カスタム ホスト名] フィールドに、ルート ドメインまたは頂点ドメインの名前を入力します (例: `contosonews.com`)。
3. ドメインからご自分のフロント ドアへの CNAME マッピングが検証されたら、 **[追加]** をクリックしてカスタム ドメインを追加します。
4. **[保存]** をクリックして、変更を送信します。

![[カスタム ドメイン] メニュー](./media/front-door-apex-domain/front-door-onboard-apex-domain.png)

## <a name="enable-https-on-your-custom-domain"></a>カスタム ドメインで HTTPS を有効にする

1. 追加されたカスタム ドメインをクリックし、 **[カスタム ドメイン HTTPS]** セクションで、状態を **[有効]** に変更します。
2. **[証明書の管理の種類]** で、 _[Use my own certificate]\(独自の証明書を使用する\)_ を選択します。

> [!WARNING]
> Front Door 管理の証明書の管理の種類は、頂点ドメインまたはルート ドメインに対しては現在はサポートされていません。 Front Door に対する頂点ドメインまたはルート ドメインで HTTPS を有効にするために使用できる唯一のオプションは、Azure Key Vault でホストされている独自のカスタム SSL 証明を使用することです。

3. 次のステップに進む前に、UI で説明されているように、Front Door がご自分のキー コンテナーにアクセスするための適切なアクセス許可を設定したことを確認します。
4. 現在のサブスクリプションから **Key Vault アカウント**を選択し、適切な証明書にマップするための適切な**シークレット**と**シークレットのバージョン**を選択します。
5. **[更新]** をクリックして選択内容を保存し、 **[保存]** をクリックします。
6. 数分経った後で **[更新]** をクリックし、カスタム ドメインをもう一度クリックして、証明書のプロビジョニングの進行状況を確認します。 

> [!WARNING]
> 頂点ドメインに対する適切なルーティング規則を作成したこと、または既存のルーティング規則にドメインを追加したことを確認します。

## <a name="next-steps"></a>次の手順

- [フロント ドアの作成](quickstart-create-front-door.md)方法について学習します。
- [Front Door のしくみ](front-door-routing-architecture.md)について学習します。