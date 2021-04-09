---
title: Azure Front Door Standard/Premium SKU 構成にカスタム ドメインを追加する方法
description: このチュートリアルでは、Azure Front Door Standard/Premium SKU にカスタム ドメインをオンボードする方法を説明します。
services: frontdoor
documentationcenter: ''
author: duongau
ms.service: frontdoor
ms.topic: how-to
ms.workload: infrastructure-services
ms.date: 02/18/2021
ms.author: amsriva
ms.openlocfilehash: 164e06024844fb5262586450b737db9c807e373a
ms.sourcegitcommit: 97c48e630ec22edc12a0f8e4e592d1676323d7b0
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/18/2021
ms.locfileid: "101097963"
---
# <a name="create-a-custom-domain-on-azure-front-door-standardpremium-sku-preview-using-the-azure-portal"></a>Azure portal を使用して Azure Front Door Standard/Premium SKU (プレビュー) にカスタム ドメインを作成する

> [!Note]
> このドキュメントは、Azure Front Door Standard/Premium (プレビュー) を対象としています。 Azure Front Door については、 [こちら](../front-door-overview.md)を参照してください。

アプリケーションの配信に Azure Front Door Standard/Premium を使用している場合、独自のドメイン名がエンド ユーザーの要求で示されるようにしたいときは、カスタム ドメインが必要です。 見てわかるドメイン名を使用することは、顧客にとって便利であり、ブランド化の目的にも役立ちます。

Azure Front Door Standard/Premium プロファイルを作成すると、既定のフロントエンド ホストにサブドメイン azurefd.net が含まれるようになります。 このサブドメインは、Azure Front Door Standard/Premium がバックエンドからコンテンツを配信するときに URL に既定で含まれます。 たとえば、「 `https://contoso-frontend.azurefd.net/activeusers.htm` 」のように入力します。 便宜を図るため、Azure Front Door には、カスタム ドメインを既定のホストと関連付けるオプションが用意されています。 このオプションを使用する場合、URL の中で Azure Front Door Standard/Premium 所有のドメイン名の代わりにカスタム ドメインを使用してコンテンツを配信します。 たとえば、「 https://www.contoso.com/photo.png 」のように入力します。

> [!IMPORTANT]
> Azure Front Door Standard/Premium (プレビュー) は現在、パブリック プレビュー段階です。
> このプレビュー バージョンはサービス レベル アグリーメントなしで提供されています。運用環境のワークロードに使用することはお勧めできません。 特定の機能はサポート対象ではなく、機能が制限されることがあります。
> 詳細については、「[**Microsoft Azure プレビューの追加使用条件**](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)」を参照してください。

## <a name="prerequisites"></a>前提条件
* このチュートリアルの手順を完了するには、最初に Front Door を作成する必要があります。 詳細については、[クイックスタート: Front Door Standard/Premium の作成](create-front-door-portal.md)に関するページを参照してください。

* カスタム ドメインがまだない場合は、最初にカスタム ドメインをドメイン プロバイダーから購入する必要があります。 たとえば、[カスタム ドメイン名の購入](../../app-service/manage-custom-dns-buy-domain.md)に関するページを参照してください。

* Azure を使用して [DNS ドメイン](../../dns/dns-overview.md)をホストしている場合は、ドメイン プロバイダーのドメイン ネーム システム (DNS) を Azure DNS に委任する必要があります。 詳細については、「[Azure DNS へのドメインの委任](../../dns/dns-delegate-domain-azure-dns.md)」を参照してください。 DNS ドメインを処理するためにドメイン プロバイダーを使用している場合は、このようにしないと、要求された DNS TXT レコードを入力して手動でドメインを検証する必要があります。

## <a name="add-a-new-custom-domain"></a>新しいカスタム ドメインの追加

カスタム ドメインは、ポータルの [ドメイン] セクションで管理します。 カスタム ドメインを作成し、エンドポイントに関連付ける前に検証することができます。 カスタム ドメインとそのサブドメインは、一度に 1 つのエンドポイントにのみ関連付けることができます。 ただし、同じカスタム ドメインの別のサブドメインを別の Front Door に使用することはできます。 また、異なるサブドメインがあるカスタム ドメインを同じ Front Door エンドポイントにマップすることもできます。

1. Azure Front Door プロファイルの [設定] で、 *[ドメイン]* 、 **[ドメインの追加]** ボタンの順に選択します。

    :::image type="content" source="../media/how-to-add-custom-domain/add-domain-button.png" alt-text="[ドメイン] ランディング ページの [ドメインの追加] ボタンのスクリーンショット。":::

1. **[ドメインの追加]** ページが表示され、カスタム ドメインに関する情報を入力できます。 Azure で管理されている DNS を選択できます。これが推奨されていますが、独自の DNS プロバイダーを使用することもできます。 Azure で管理されている DNS を選択する場合は、既存の DNS ゾーンを選択し、カスタム サブドメインを選択するか、新しいものを作成します。 別の DNS プロバイダーを使用する場合は、カスタム ドメイン名を手動で入力します。 **[追加]** を選択して、カスタム ドメインを追加します。

    :::image type="content" source="../media/how-to-add-custom-domain/add-domain-page.png" alt-text="[ドメインの追加] ページのスクリーンショット。":::

    新しいカスタム ドメインが作成され、[検証の状態] は **[送信しています]** です。

    :::image type="content" source="../media/how-to-add-custom-domain/validation-state-submitting.png" alt-text="ドメインの [検証の状態] が [送信しています] であるスクリーンショット。":::

    [検証の状態] が **[保留中]** に変わるまで待ちます。 この操作には数分かかる場合があります。

    :::image type="content" source="../media/how-to-add-custom-domain/validation-state-pending.png" alt-text="ドメインの [検証の状態] が [保留中] であるスクリーンショット。":::

1. **[保留中]** の [検証の状態] を選択します。 新しいページが表示され、カスタム ドメインの検証に必要な DNS TXT レコード情報が表示されます。 TXT レコードの形式は `_dnsauth.<your_subdomain>` です。 Azure DNS ベースのゾーンを使用している場合は、 **[追加]** ボタンを選択すると、表示されたレコード値を含む新しい TXT レコードが Azure DNS ゾーンに作成されます。 別の DNS プロバイダーを使用している場合は、このページに示されているレコード値を使用して `dnsauth.<your_subdomain>` という名前の新しい TXT レコードを手動で作成します。

    :::image type="content" source="../media/how-to-add-custom-domain/validate-custom-domain.png" alt-text="[カスタム ドメインの検証] ページのスクリーンショット。":::

1. [状態の更新] を選択します。 DNS TXT レコードを使用してドメインが検証されると、[検証の状態] が **[検証済み]** に変わります。 この操作では、検証に数分かかる場合があります。

    :::image type="content" source="../media/how-to-add-custom-domain/domain-status-verified.png" alt-text="検証済みのカスタム ドメインのスクリーンショット。":::

1. ページを閉じて、カスタム ドメインの一覧のランディング ページに戻ります。 カスタム ドメインの [プロビジョニングの状態] が **[プロビジョニング済み]** に変わり、[検証の状態] が **[承認済み]** に変わります。

    :::image type="content" source="../media/how-to-add-custom-domain/provisioned-approved-status.png" alt-text="[プロビジョニング済み] と [承認済み] の状態のスクリーンショット。":::

## <a name="associate-the-custom-domain-with-your-front-door-endpoint"></a>カスタム ドメインを Front Door エンドポイントと関連付ける

カスタム ドメインを検証したら、それを Azure Front Door Standard/Premium エンドポイントに追加することができます。

1. カスタム ドメインが検証されたら、それを既存の Azure Front Door Standard/Premium エンドポイントとルートに関連付けることができます。 **[エンドポイントの関連付け]** リンクを選択して **[エンドポイントとルートの関連付け]** ページを開きます。 関連付けるエンドポイントとルートを選択します。 次に、 **[関連付け]** を選択します。 関連付け操作が完了したら、ページを閉じます。

    :::image type="content" source="../media/how-to-add-custom-domain/associate-endpoint-routes.png" alt-text="[エンドポイントとルートの関連付け] ページのスクリーンショット。":::

    エンドポイントの関連付けの状態は、カスタム ドメインが現在関連付けられているエンドポイントを反映するように変化します。 

    :::image type="content" source="../media/how-to-add-custom-domain/endpoint-association-status.png" alt-text="[エンドポイントの関連付け] リンクのスクリーンショット。":::

1. [DNS の状態] リンクを選択します。

    :::image type="content" source="../media/how-to-add-custom-domain/dns-state-link.png" alt-text="[DNS の状態] リンクのスクリーンショット。":::

1. **[CNAME レコードの追加または更新]** ページが表示され、トラフィックのフローを開始するために事前に指定しておく必要がある CNAME レコード情報が表示されます。 Azure DNS でホストされたゾーンを使用している場合は、ページの **[追加]** ボタンを選択することで CNAME レコードを作成できます。 別の DNS プロバイダーを使用している場合は、ページに示されているように、CNAME レコードの名前と値を手動で入力する必要があります。

    :::image type="content" source="../media/how-to-add-custom-domain/add-update-cname-record.png" alt-text="[CNAME レコードの追加または更新] のスクリーンショット。":::

1. CNAME レコードが作成され、Azure Front Door エンドポイントへのカスタム ドメインの関連付けが完了すると、トラフィック フローが開始されます。

    > [!NOTE]
    > HTTPS が有効になっている場合、証明書のプロビジョニングと伝達には数分かかることがあります。伝達がすべてのエッジの場所に対して行われるためです。 

## <a name="verify-the-custom-domain"></a>カスタム ドメインを確認する

カスタム ドメインを検証して関連付けたら、そのカスタム ドメインがエンドポイントに対して正しく参照されていることを確認します。

:::image type="content" source="../media/how-to-add-custom-domain/verify-configuration.png" alt-text="検証され、関連付けられたカスタム ドメインのスクリーンショット。":::

最後に、アプリケーションのコンテンツに対して、ブラウザーを使用してサービスが提供されていることを確認します。

## <a name="next-steps"></a>次のステップ

カスタム ドメインの HTTPS を有効にする方法については、次のチュートリアルに進んでください。

> [!div class="nextstepaction"]
> [カスタム ドメインに対する HTTPS の有効化]()
