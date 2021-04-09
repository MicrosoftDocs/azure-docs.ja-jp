---
title: 既存のフロント ドアにルート ドメインまたは頂点ドメインをオンボードする - Azure portal
description: Azure portal を使用して既存のフロント ドアにルート ドメインまたは頂点ドメインをオンボードする方法を説明します。
services: front-door
author: duongau
ms.service: frontdoor
ms.topic: how-to
ms.date: 11/13/2020
ms.author: duau
ms.openlocfilehash: 55eefe7a7490df050aa7ebc2bb41fbadcc8d8279
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "94646340"
---
# <a name="onboard-a-root-or-apex-domain-on-your-front-door"></a>フロント ドアにルート ドメインまたは頂点ドメインをオンボードする
Azure Front Door では、CNAME レコードを使用して、カスタム ドメインのオンボードに対するドメインの所有権が検証されます。 Front Door によって、Front Door プロファイルに関連付けられているフロントエンド IP アドレスが公開されることはありません。 そのため、頂点ドメインは、Azure Front Door にオンボードするのが目的の場合は、IP アドレスにマップすることはできません。

DNS プロトコルでは、ゾーンの頂点での CNAME レコードの割り当てができません。 たとえば、ドメインが `contoso.com` の場合、`somelabel.contoso.com` に対する CNAME レコードは作成できますが、`contoso.com` 自体に対する CNAME を作成することはできません。 Azure Front Door の背後でアプリケーションの負荷分散を行っているアプリケーションの所有者にとっては、この制限によって問題が生じます。 Front Door プロファイルを使用するには CNAME レコードを作成する必要があるため、ゾーンの頂点から Front Door プロファイルをポイントすることはできません。

この問題は、Azure DNS のエイリアス レコードを使用することで解決できます。 CNAME レコードとは異なり、エイリアス レコードはゾーンの頂点に作成されます。 アプリケーションの所有者はこれを使用して、ゾーンの頂点のレコードがパブリック エンドポイントを持つ Front Door プロファイルを指すようにすることができます。 アプリケーションの所有者は、DNS ゾーン内の他のドメインで使用されているのと同じ Front Door プロファイルをポイントします。 たとえば、`contoso.com` と `www.contoso.com` で、同じ Front Door プロファイルをポイントできます。 

頂点またはルート ドメインを Front Door プロファイルにマッピングするには、基本的に CNAME フラット化または DNS 追跡が必要です。 DNS プロバイダーが IP アドレスに到達するまで CNAME エントリを再帰的に解決するメカニズムです。 この機能は、Front Door エンドポイント用に Azure DNS でサポートされています。 

> [!NOTE]
> CNAME のフラット化または DNS の追跡をサポートする DNS プロバイダーは他にもありますが、Azure Front Door では、お客様がドメインをホストする場合は Azure DNS を使うことが推奨されます。

Azure portal を使って、頂点ドメインをお使いのフロント ドアにオンボードし、TLS 終了用の証明書とそれを関連付けることによって HTTPS を有効にできます。 頂点ドメインは、ルート ドメインまたはネイキッド ドメインとも呼ばれます。

## <a name="create-an-alias-record-for-zone-apex"></a>ゾーンの頂点に対するエイリアス レコードを作成する

1. オンボード対象のドメインの **Azure DNS** 構成を開きます。

1. ゾーンの頂点のレコードを作成または編集します。

1. レコードの **種類** として *A* を選択し、 **[Alias record set]\(エイリアス レコード セット\)** で *[はい]* を選択します。 **[Alias type]\(エイリアスの種類\)** は *[Azure resource]\(Azure リソース\)* に設定する必要があります。

1. Front Door プロファイルをホストする Azure サブスクリプションを選択します。 次に、 **[Azure リソース]** ドロップダウンから Front Door リソースを選択します。

1. **[OK]** を選択して変更を送信します。

    :::image type="content" source="./media/front-door-apex-domain/front-door-apex-alias-record.png" alt-text="ゾーンの頂点に対するエイリアス レコード":::

1. 上のステップでは、Front Door リソースを指定するゾーンの頂点のレコードと、Front Door プロファイルへのドメインのオンボードに使用される "afdverify" (例: `afdverify.contosonews.com`) をマップする CNAME レコードが作成されます。

## <a name="onboard-the-custom-domain-on-your-front-door"></a>カスタム ドメインをフロント ドアにオンボードする

1. [Front Door デザイナー] タブで、[フロントエンド ホスト] セクションの [+] アイコンを選択して、新しいカスタム ドメインを追加します。

1. [カスタム ホスト名] フィールドに、ルート ドメインまたは頂点ドメインの名前を入力します (例: `contosonews.com`)。

1. ドメインからご自分の Front Door への CNAME マッピングが検証されたら、 **[追加]** を選択してカスタム ドメインを追加します。

1. **[保存]** を選択して、変更を送信します。

   :::image type="content" source="./media/front-door-apex-domain/front-door-onboard-apex-domain.png" alt-text="[カスタム ドメイン] メニュー":::

## <a name="enable-https-on-your-custom-domain"></a>カスタム ドメインで HTTPS を有効にする

1. 追加されたカスタム ドメインを選択し、 **[カスタム ドメイン HTTPS]** セクションで、状態を **[有効]** に変更します。

1. **[証明書の管理の種類]** で、 *[Use my own certificate]\(独自の証明書を使用する\)* を選択します。

   :::image type="content" source="./media/front-door-apex-domain/front-door-onboard-apex-custom-domain.png" alt-text="カスタムドメインの HTTPS 設定":::    

   > [!WARNING]
   > Front Door 管理の証明書の管理の種類は、頂点ドメインまたはルート ドメインに対しては現在はサポートされていません。 Front Door に対する頂点ドメインまたはルート ドメインで HTTPS を有効にするために使用できる唯一のオプションは、Azure Key Vault でホストされている独自のカスタム TLS/SSL 証明書を使用することです。

1. 次のステップに進む前に、UI で説明されているように、Front Door がご自分のキー コンテナーにアクセスするための適切なアクセス許可を設定したことを確認します。

1. 現在のサブスクリプションから **Key Vault アカウント** を選択し、適切な証明書にマップするための適切な **シークレット** と **シークレットのバージョン** を選択します。

1. **[更新]** を選択して選択内容を保存し、 **[保存]** を選択します。

1. 数分経った後で **[更新]** を選択し、カスタム ドメインをもう一度選択して、証明書のプロビジョニングの進行状況を確認します。 

> [!WARNING]
> 頂点ドメインに対する適切なルーティング規則を作成したこと、または既存のルーティング規則にドメインを追加したことを確認します。

## <a name="next-steps"></a>次のステップ

- [フロント ドアの作成](quickstart-create-front-door.md)方法について学習します。
- [Front Door のしくみ](front-door-routing-architecture.md)について学習します。
