---
title: 既存の Azure CDN エンドポイントにルート ドメインまたは頂点ドメインをオンボードする - Azure portal
description: Azure portal を使用して既存の Azure CDN エンドポイントにルート ドメインまたは頂点ドメインをオンボードする方法について説明します。
services: cdn
author: asudbring
ms.service: azure-cdn
ms.topic: how-to
ms.date: 11/07/2020
ms.author: allensu
ms.openlocfilehash: 8ab4f698c7149d8d57f790e221ccbe35ec090fe6
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "94369890"
---
# <a name="onboard-a-root-or-apex-domain-to-an-existing-azure-cdn-endpoint"></a>既存の Azure CDN エンドポイントにルート ドメインまたは頂点ドメインをオンボードする

Azure CDN では、CNAME レコードを使用して、カスタム ドメインのオンボードに対するドメインの所有権が検証されます。 CDN によって、CDN プロファイルに関連付けられているフロントエンド IP アドレスが公開されることはありません。 Azure CDN にオンボードすることが目的の場合は、頂点ドメインを IP アドレスにマップすることはできません。

DNS プロトコルでは、ゾーンの頂点での CNAME レコードの割り当てができません。 たとえば、ドメインが `contoso.com` の場合、`somelabel.contoso.com` に対する CNAME レコードは作成できますが、`contoso.com` 自体に対する CNAME を作成することはできません。 Azure CDN の背後でアプリケーションの負荷分散を行っているアプリケーションの所有者にとっては、この制限によって問題が生じます。 CDN プロファイルを使用するには CNAME レコードを作成する必要があるため、ゾーンの頂点から CDN プロファイルをポイントすることはできません。

この問題は、Azure DNS のエイリアス レコードを使用することで解決できます。 CNAME レコードとは異なり、エイリアス レコードはゾーンの頂点に作成されます。 アプリケーションの所有者はこれを使用して、ゾーンの頂点のレコードがパブリック エンドポイントを持つ CDN プロファイルを指すようにすることができます。 アプリケーションの所有者は、DNS ゾーン内の他のドメインで使用されているのと同じ CDN プロファイルをポイントします。 たとえば、`contoso.com` と `www.contoso.com` で、同じ CDN プロファイルをポイントできます。 

頂点ドメインまたはルート ドメインを CDN プロファイルにマッピングするには、CNAME フラット化または DNS 追跡が必要です。 DNS プロバイダーが IP アドレスに到達するまで CNAME エントリを再帰的に解決するメカニズムです。 この機能は、CDN エンドポイント用に Azure DNS でサポートされています。 

> [!NOTE]
> CNAME のフラット化または DNS の追跡をサポートする DNS プロバイダーは他にもありますが、Azure CDN では、お客様がドメインをホストする場合は Azure DNS を使うことが推奨されます。

Azure portal を使って、頂点ドメインをお使いの CDN にオンボードし、TLS 終了用の証明書とそれを関連付けることによって HTTPS を有効にできます。 頂点ドメインは、ルート ドメインまたはネイキッド ドメインとも呼ばれます。

## <a name="create-an-alias-record-for-zone-apex"></a>ゾーンの頂点に対するエイリアス レコードを作成する

1. オンボード対象のドメインの **Azure DNS** 構成を開きます。

2. **[+ レコード セット]** を選択します。

3. **[レコード セットの追加]** で、次の情報を入力または選択します。

    | 設定 | 値 |
    | ------- | ------|
    | 名前 | **@** を入力します。 |
    | Type | **[A]** を選択します。 |
    | エイリアスのレコード セット | **[はい]** を選択します。 |
    | エイリアスの種類 | **[Azure リソース]** を選択します。 |
    | サブスクリプションを選択します。 | サブスクリプションを選択します。 |
    | Azure リソース | CDN エンドポイントを選択します。 |

4. **[TTL]** の値を入力します。

5. **[OK]** を選択して変更を送信します。

    :::image type="content" source="./media/onboard-apex-domain/cdn-apex-alias-record.png" alt-text="ゾーンの頂点に対するエイリアス レコード":::

6. 上記の手順により、CDN リソースを指すゾーンの頂点のレコードが作成されます。 CDN プロファイルでドメインをオンボードするために、CNAME レコードマッピング **cdnverify** が使用されます。
    1. たとえば、**cdnverify.contoso.com** などです。
    

## <a name="onboard-the-custom-domain-on-your-cdn"></a>カスタム ドメインを CDN にオンボードする

カスタム ドメインを登録したら、それを CDN エンドポイントに追加できます。 

1. [Azure portal](https://portal.azure.com/) にサインインし、カスタム ドメインにマップするエンドポイントを含む CDN プロファイルを参照します。
    
2. **[CDN のプロファイル]** ページで、カスタム ドメインに関連付ける CDN エンドポイントを選択します。

    :::image type="content" source="media/onboard-apex-domain/cdn-endpoint-selection.png" alt-text="CDN エンドポイントの選択" border="true":::
    
3. **[+ カスタム ドメイン]** を選択します。 

   :::image type="content" source="media/onboard-apex-domain/cdn-custom-domain-button.png" alt-text="カスタム ドメインの追加ボタン" border="true":::

4. **[カスタム ドメインの追加]** の **[エンドポイントのホスト名]** は自動的に入力されます。これは、CDN エンドポイントの URL ( **\<endpoint-hostname>** .azureedge.net) から得られます。 この値は変更しないでください。

5. **[カスタム ホスト名]** に、CNAME レコードのソース ドメインとして使用するカスタム ルートまたは頂点ドメインを入力します。 
    1. たとえば、 **contoso.com** などのドメインです。 **cdnverify サブドメイン名は使用しないでください**。

    :::image type="content" source="media/onboard-apex-domain/cdn-add-custom-domain.png" alt-text="カスタム ドメインの追加" border="true":::

6. **[追加]** を選択します。

   入力したカスタム ドメイン名に対する CNAME レコードが存在するかどうかが Azure によって確認されます。 CNAME が正しければ、カスタム ドメインが検証されます。 

   新しいカスタム ドメインの設定がすべての CDN エッジ ノードに反映されるまでに、少し時間がかかる場合があります。 
    - **Azure CDN Standard from Microsoft** プロファイルの場合、通常、反映は 10 分以内で完了します。 
    - **Azure CDN Standard from Akamai** プロファイルの場合、通常、反映は 1 分以内で完了します。 
    - **Azure CDN Standard from Verizon** プロファイルおよび **Azure CDN Premium from Verizon** プロファイルの場合、通常、反映は 10 分で完了します。   

## <a name="enable-https-on-your-custom-domain"></a>カスタム ドメインで HTTPS を有効にする

Azure CDN のカスタム ドメインで HTTPS を有効にする方法の詳細については、次の記事を参照してください: [チュートリアル:Azure CDN カスタム ドメインで HTTPS を構成する](cdn-custom-ssl.md)

## <a name="next-steps"></a>次のステップ

- [CDN エンドポイントを作成する](cdn-create-new-endpoint.md)方法について確認します。
