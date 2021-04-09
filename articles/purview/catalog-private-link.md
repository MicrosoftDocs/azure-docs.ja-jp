---
title: プライベート エンドポイントを使用して Purview へのアクセスをセキュリティで保護する
description: この記事では、Purview アカウントのプライベート エンドポイントを設定する方法について説明します
author: viseshag
ms.author: viseshag
ms.service: purview
ms.subservice: purview-data-catalog
ms.topic: how-to
ms.date: 03/02/2021
ms.openlocfilehash: 3193c5c00789b793a5b5beaf662f94ab9525888a
ms.sourcegitcommit: 4b7a53cca4197db8166874831b9f93f716e38e30
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/04/2021
ms.locfileid: "102107171"
---
# <a name="use-private-endpoints-for-your-purview-account"></a>Purview アカウントのプライベート エンドポイントを使用する

Purview アカウントのプライベート エンドポイントを使用すると、仮想ネットワーク (VNet) 上のクライアントはプライベート リンクを介してカタログに安全にアクセスできるようになります。 プライベート エンドポイントでは、Purview アカウントの VNet アドレス空間からの IP アドレスが使用されます。 VNet 上のクライアントと Purview アカウントの間のネットワーク トラフィックは、VNet と Microsoft バックボーン ネットワーク上のプライベート リンクを経由することで、パブリック インターネットに露出することはありません。

## <a name="create-purview-account-with-a-private-endpoint"></a>プライベート エンドポイントを備えた Purview アカウントの作成

1. [Azure portal](https://portal.azure.com)、Purview アカウントの順に移動します。

1. 基本情報を入力し、 **[ネットワーク]** タブで、[接続方法] を [プライベート エンドポイント] に設定します。プライベート エンドポイントとペアリングする **[サブスクリプション]、[仮想ネットワーク]、[サブネット]** の詳細を入力して、[インジェスト] プライベート エンドポイントを設定します。

    :::image type="content" source="media/catalog-private-link/create-pe-azure-portal.png" alt-text="アカウント作成時の PE の作成":::

1. また、必要に応じて、各インジェスト プライベート エンドポイントの **[プライベート DNS ゾーン]** を設定することもできます。

1. [追加] をクリックして、Purview アカウントのプライベート エンドポイントを追加します。

1. [プライベート エンドポイントの作成] ブレードで、[Purview sub-resource]\(Purview サブリソース\) を **[アカウント]** に設定し、ご使用の仮想ネットワークとサブネットを選択し、DNS を登録する [プライベート DNS ゾーン] を選択します (獲得 DNS サーバーを利用することも、仮想マシン上のホスト ファイルを使用して DNS レコードを作成することもできます)。

    :::image type="content" source="media/catalog-private-link/create-pe-account.png" alt-text="アカウント作成時の PE の作成":::

1. **[OK]** を選択します。

1. **[Review + create]\(レビュー + 作成\)** を選択します。 [確認および作成] ページが表示され、Azure によって構成が検証されます。

1. "検証に成功しました" というメッセージが表示されたら、 **[作成]** を選択します。

    :::image type="content" source="media/catalog-private-link/validation-passed.png" alt-text="アカウント作成の &quot;検証に成功しました&quot;":::

## <a name="create-a-private-endpoint-for-the-purview-web-portal"></a>Purview Web ポータルのプライベート エンドポイントを作成する

1. 作成したばかりの Purview アカウントに移動し、[設定] セクションで [プライベート エンドポイント接続] を選択します。
    
1. [+ プライベート エンドポイント] をクリックして、新しいプライベート エンドポイントを作成します。

    :::image type="content" source="media/catalog-private-link/pe-portal.png" alt-text="ポータルのプライベート エンドポイントを作成する":::

1. 基本情報を入力します。

1. [リソース] タブで、[リソースの種類] として **[Microsoft.Purview/accounts]** を選択します。

1. [リソース] として、新しく作成した Purview アカウントを選択し、[対象サブリソース] として **[ポータル]** を選択します。
    :::image type="content" source="media/catalog-private-link/pe-portal-details.png" alt-text="ポータルのプライベート エンドポイントの詳細":::

1. [構成] タブで、[仮想ネットワーク] と [プライベート DNS ゾーン] を選択します。[概要] ページに移動し、 **[作成]** をクリックして、ポータルのプライベート エンドポイントを作成します。

## <a name="enabling-access-to-azure-active-directory"></a>Azure Active Directory へのアクセスを有効にする

> [!NOTE]
> VM、VPN ゲートウェイ、または VNET ピアリング ゲートウェイはパブリック インターネットにアクセスできる場合、プライベート エンドポイントで有効になっている Purview ポータルおよび Purview アカウントにアクセスできるため、以下の残りの手順に従う必要はありません。 プライベート ネットワークで、ネットワーク セキュリティ グループの規則がすべてのパブリック インターネット トラフィックを拒否するように設定されている場合、AAD アクセスを有効にするためにいくつかの規則を追加する必要があります。 このためには、以下の手順に従ってください。

以下の手順は、Azure VM から Purview に安全にアクセスするためのものです。 VPN または他の Vnet ピアリング ゲートウェイを使用している場合も同様の手順に従う必要があります。

1. Azure portal で VM に移動し、[設定] セクションの [ネットワーク] タブを選択します。 次に、[送信ポートの規則] を選択し、[送信ポートの規則を追加する] をクリックします。

    :::image type="content" source="media/catalog-private-link/outbound-rule-add.png" alt-text="アウトバウンド規則の追加":::

2. [送信ポートの規則を追加する] ブレードで、 *[宛先]* として [サービス タグ] を選択し、[宛先サービス タグ] として **[AzureActiveDirectory]** を選択し、[宛先ポート範囲] として [*] を、[アクション] として [許可] を選択します。 **[優先順位] は、すべてのインターネットを拒否した規則よりも高くする必要があります**。 ルールを作成します。

    :::image type="content" source="media/catalog-private-link/outbound-rule-details.png" alt-text="アウトバウンド規則の詳細の追加":::
 
3. 同じ手順に従って、'**AzureResourceManager**' サービス タグを許可する別の規則を作成します。 Azure portal にアクセスする必要がある場合は、'*AzurePortal*' サービス タグの規則も追加できます。

4. VM に接続してブラウザーを開き、ブラウザー コンソールに移動し (Ctrl + Shift + J キー)、[ネットワーク] タブに切り替えてネットワーク要求を監視します。 URL ボックスに 「web.purview.azure.com」と入力し、AAD の資格情報を使用してログインを試みます。 おそらくログインに失敗する可能性があります。コンソールの [ネットワーク] タブで、AAD が access aadcdn.msauth.net にアクセスしようとしていますが、ブロックされていることを確認できます。

    :::image type="content" source="media/catalog-private-link/login-fail.png" alt-text="ログイン失敗の詳細":::
 
5. この場合は、VM でコマンド プロンプトを開いて、この URL (aadcdn.msauth.net) に ping を実行した後、VM のネットワーク セキュリティ規則で IP の送信ポート規則を追加します。 [宛先] を [IP アドレス] に設定し、[宛先 IP アドレス] を aadcdn の IP に設定します。 ロード バランサーとトラフィック マネージャーのために、AAD CDN の IP が動的になる場合があります。 その IP を取得したら、それを VM のホスト ファイルに追加して、ブラウザーがその IP にアクセスして、AAD CDN を取得するように強制します。

    :::image type="content" source="media/catalog-private-link/ping.png" alt-text="ping のテスト":::

    :::image type="content" source="media/catalog-private-link/aadcdn-rule.png" alt-text="AAD CDN 規則":::
 
6. 新しい規則を作成したら、VM に戻り、AAD の資格情報を再度使用して、ログインを試みます。 ログインに成功すると、Purview ポータルを使用できるようになります。 ただし、場合によっては、AAD は他のドメインにリダイレクトし、使用されているアカウントの種類に基づいてログインします。 たとえば、live.com アカウントの場合、AAD は live.com にリダイレクトしてログインし、これらの要求は再度ブロックされます。 Microsoft の従業員アカウントの場合、AAD はログイン情報について msft.sts.microsoft.com にアクセスします。 ブラウザーの [ネットワーク] タブでネットワーク要求をチェックして、ブロックされているドメインの要求を確認し、前の手順を再実行して IP を取得し、その IP の要求を許可するための送信ポート規則をネットワーク セキュリティ グループに追加します (可能な場合は、URL と IP を VM のホスト ファイルに追加して DNS 解決を修正します)。 ログイン ドメインの正確な IP 範囲がわかっている場合は、それらをネットワーク規則に直接追加することもできます。

7. これで、AAD へのログインが正常に完了します。 Purview ポータルは正常に読み込まれますが、特定の Purview アカウントにしかアクセスできないため、すべての Purview アカウントの一覧表示は機能しません。 プライベート エンドポイントを正常に設定できた Purview アカウントにアクセスするには、「*web.purview.azure.com/resource/{PurviewAccountName}* 」と入力します。

##  <a name="enable-private-endpoint-on-existing-purview-accounts"></a>既存の Purview アカウントでプライベート エンドポイントを有効にする

Purview アカウントを作成した後、Purview プライベート エンドポイントを追加するには、次の 2 つの方法があります。
- Azure portal (Purview アカウント) の使用
- プライベート リンク センターの使用

### <a name="using-the-azure-portal-purview-account"></a>Azure portal (Purview アカウント) の使用

1. Azure portal から Purview アカウントに移動し、 **[設定]** の **[ネットワーク]** セクションで [プライベート エンドポイント接続] を選択します。

:::image type="content" source="media/catalog-private-link/pe-portal.png" alt-text="ポータルのプライベート エンドポイントを作成する":::
    
2. [+ プライベート エンドポイント] をクリックして、新しいプライベート エンドポイントを作成します。

3. 基本情報を入力します。

4. [リソース] タブで、[リソースの種類] として **[Microsoft.Purview/accounts]** を選択します。

5. [リソース] として Purview アカウントを選択し、[対象サブリソース] として **[アカウント]** を選択します。

6. [構成] タブで、 **[仮想ネットワーク]** と **[プライベート DNS ゾーン]** を選択します。[概要] ページに移動し、 **[作成]** を選択してポータルのプライベート エンドポイントを作成します。

> [!NOTE]
> [対象サブリソース] として **[ポータル]** を選択した場合も、上記と同じ手順に従う必要があります。

### <a name="using-the-private-link-center"></a>プライベート リンク センターの使用

1. [Azure Portal](https://portal.azure.com) に移動します。

2. ページの上部にある検索バーで "プライベート リンク" を検索し、最初のオプションをクリックして、[プライベート リンク] ブレードに移動します。

3. [+ 追加] をクリックして、基本的な詳細を入力します。

    :::image type="content" source="media/catalog-private-link/private-link-center.png" alt-text="プライベート リンク センターから PE を作成する":::

4. [リソース] として、既に作成している Purview アカウントを選択し、[対象サブリソース] として **[アカウント]** を選択します。

5. [構成] タブで、[仮想ネットワーク] と [プライベート DNS ゾーン] を選択します。[概要] ページに移動し、 **[作成]** を選択してアカウントのプライベート エンドポイントを作成します。

> [!NOTE]
> [対象サブリソース] として **[ポータル]** を選択した場合も、上記と同じ手順に従う必要があります。

## <a name="next-steps"></a>次のステップ

- [Azure Purview Data Catalog の参照](how-to-browse-catalog.md)
- [Azure Purview データ カタログを検索する](how-to-search-catalog.md)  