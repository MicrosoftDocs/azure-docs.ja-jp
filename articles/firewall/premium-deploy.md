---
title: Azure Firewall Premium プレビューをデプロイして構成する
description: Azure Firewall Premium のデプロイと構成の方法について説明します。
author: vhorne
ms.service: firewall
services: firewall
ms.topic: how-to
ms.date: 02/16/2021
ms.author: victorh
ms.openlocfilehash: fa106fac683619706f4be330ad1c4bff7b56f2dd
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "101721788"
---
# <a name="deploy-and-configure-azure-firewall-premium-preview"></a>Azure Firewall Premium プレビューをデプロイして構成する

> [!IMPORTANT]
> 現在、Azure Firewall Premium はパブリック プレビュー段階にあります。
> このプレビュー バージョンはサービス レベル アグリーメントなしで提供されています。運用環境のワークロードに使用することはお勧めできません。 特定の機能はサポート対象ではなく、機能が制限されることがあります。 詳しくは、[Microsoft Azure プレビューの追加使用条件](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)に関するページをご覧ください。

 Azure Firewall Premium プレビューは、機密度が高く、規制された環境に必要な機能を備えた次世代のファイアウォールです。 このパックには、次の機能があります。

- **TLS 検査** - アウトバウンド トラフィックを復号化し、データを処理し、その後にデータを暗号化して宛先に送信します。
- **IDPS** - ネットワーク侵入検出と防止システム (IDPS) を使用すると、ネットワーク アクティビティを監視して悪意のあるアクティビティがないか確認し、このアクティビティに関する情報をログに記録し、それを報告して、任意でそのブロックを試みることができます。
- **URL フィルタリング** - Azure Firewall の FQDN フィルタリング機能を拡張して URL 全体が考慮対象になるようにします。 たとえば、`www.contoso.com` の代わりに `www.contoso.com/a/c` となります。
- **Web カテゴリ** - 管理者は、ギャンブルの Web サイトやソーシャル メディアの Web サイトといった Web サイト カテゴリへのユーザーのアクセスを許可または禁止できます。

詳細については、「[Azure Firewall Premium の機能](premium-features.md)」を参照してください。

テンプレートを使用して、3 つのサブネットを含む中央の VNet (10.0.0.0/16) を備えたテスト環境をデプロイします。
- worker サブネット (10.0.10.0/24)
- Azure Bastion サブネット (10.0.20.0/24)
- ファイアウォール サブネット (10.0.100.0/24)

このテスト環境では、単純化するために中央の VNet が 1 つ使用されています。 運用を目的とする場合は、ピアリングされた VNet を含む[ハブとスポークのトポロジ](/azure/architecture/reference-architectures/hybrid-networking/hub-spoke)がより一般的です。

:::image type="content" source="media/premium-deploy/premium-topology.png" alt-text="中央の VNet トポロジ":::

worker 仮想マシンは、ファイアウォールを介して HTTP/S 要求を送信するクライアントです。

## <a name="prerequisites"></a>前提条件

Azure サブスクリプションをお持ちでない場合は、開始する前に [無料アカウント](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) を作成してください。

## <a name="deploy-the-infrastructure"></a>インフラストラクチャをデプロイする

テンプレートを使用すると、IDPS、TLS 検査、URL フィルタリング、Web カテゴリが有効になった Azure Firewall Premium の完全なテスト環境がデプロイされます。

- 新しい Azure Firewall Premium と、そのコア機能 (IDPS、TLS 検査、URL フィルタリング、Web カテゴリ) を簡単に検証できるようにする事前定義の設定を含むファイアウォール ポリシー
- Key Vault とマネージド ID を含む、すべての依存関係をデプロイします。 運用環境では、これらのリソースが既に作成されていて、同じテンプレート内では必要ない場合があります。
- 自己署名のルート CA を生成し、生成された Key Vault にデプロイします。
-  派生した中間 CA を生成して、Windows テスト仮想マシン (WorkerVM) にデプロイします。
- bastion ホスト (BastionHost) もデプロイされ、Windows テスト マシン (WorkerVM) への接続に使用できます。



[![Azure へのデプロイ](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-azurefirewall-premium%2Fazuredeploy.json)

## <a name="test-the-firewall"></a>ファイアウォールをテストする

これで、IDPS、TLS 検査、Web フィルタリング、Web カテゴリをテストできます。

### <a name="add-firewall-diagnostics-settings"></a>ファイアウォール診断設定を追加する

ファイアウォール ログを収集するには、ファイアウォール ログを収集するための診断設定を追加する必要があります。

1. **DemoFirewall** を選択し、 **[モニター]** の下で、 **[診断設定]** を選択します。
2. **[診断設定の追加]** を選択します。
3. **[診断設定の名前]** に、「*fw-diag*」と入力します。
4. **[ログ]** の下で、**AzureFirewallApplicationRule** と **AzureFirewallNetworkRule** を選択します。
5. **[宛先の詳細]** で、 **[Log Analytics ワークスペースに送信する]** を選択します。
6. **[保存]** を選択します。

### <a name="idps-tests"></a>IDPS のテスト

IDPS をテストするには、適切なサーバー証明書を持つ独自の内部 Web サーバーをデプロイする必要があります。 Azure Firewall Premium プレビューの証明書の要件に関する詳細については、「[Azure Firewall Premium プレビューの証明書](premium-certificates.md)」を参照してください。

`curl` を使用して、さまざまな HTTP ヘッダーを制御し、悪意のあるトラフィックをシミュレートできます。

#### <a name="to-test-idps-for-http-traffic"></a>HTTP トラフィックの場合に IDPS をテストするには:

1. WorkerVM 仮想マシンで、管理者のコマンド プロンプト ウィンドウを開きます。
2. コマンド プロンプトに、次のコマンドを入力します。

   `curl -A "BlackSun" <your web server address>`
3. Web サーバーの応答が表示されます。
4. Azure portal でファイアウォール ネットワーク規則のログに移動し、次のメッセージのようなアラートを見つけます。

   :::image type="content" source="media/premium-deploy/alert-message.png" alt-text="アラート メッセージ":::

   > [!NOTE]
   > ログにデータが表示され始めるまでに、しばらく時間がかかる可能性があります。 ログにデータが表示されるようになるまで、少なくとも 20 分を見込んでください。
5. 署名 2008983 の署名規則を追加します。

   1. **DemoFirewallPolicy** を選択し、 **[設定]** で **[IDPS(プレビュー)]** を選択します。
   1. **[署名規則]** タブを選択します。
   1. **[Signature ID]\(署名 ID\)** で、空いているテキスト ボックスに「*2008983*」と入力します。
   1. **[モード]** で、 **[禁止]** を選択します。
   1. **[保存]** を選択します。
   1. デプロイが完了するのを待ってから続行します。



6. WorkerVM で、`curl` コマンドをもう一度実行します。

   `curl -A "BlackSun" <your web server address>`

   HTTP 要求はファイアウォールによってブロックされるようになっているため、接続タイムアウトが満了すると、次の出力が表示されます。

   `read tcp 10.0.100.5:55734->10.0.20.10:80: read: connection reset by peer`

7. Azure portal で監視ログに移動し、ブロックされた要求に関するメッセージを見つけます。
<!---8. Now you can bypass the IDPS function using the **Bypass list**.

   1. On the **IDPS (preview)** page, select the **Bypass list** tab.
   2. Edit **MyRule** and set **Destination** to *10.0.20.10, which is the ServerVM private IP address.
   3. Select **Save**.
1. Run the test again: `curl -A "BlackSun" http://server.2020-private-preview.com` and now you should get the `Hello World` response and no log alert. --->

#### <a name="to-test-idps-for-https-traffic"></a>HTTPS トラフィックの場合に IDPS をテストするには

HTTP ではなく HTTPS を使用してこれらの curl テストを繰り返します。 次に例を示します。

`curl --ssl-no-revoke -A "BlackSun" <your web server address>`

HTTP のテストで得られたものと同じ結果が表示されるはずです。

### <a name="tls-inspection-with-url-filtering"></a>URL フィルタリングによる TLS 検査

URL フィルタリングによる TLS 検査をテストするには、次の手順を使用します。

1. ファイアウォール ポリシーのアプリケーション規則を編集し、`AllowURL` という名前の新しい規則を `AllowWeb` 規則コレクションに追加します。 ターゲット URL として `www.nytimes.com/section/world`、ソース IP アドレスとして **\* *_、宛先の種類として _* URL (プレビュー)** を構成し、 **[TLS 検査 (プレビュー)]** とプロトコル **http, https** を選択します。

3. デプロイが完了したら、WorkerVM でブラウザーを開き、`https://www.nytimes.com/section/world` に移動して、HTML 応答がブラウザーに想定どおりに表示されることを検証します。
4. Azure portal で、アプリケーション規則の監視ログ内で URL 全体を表示できます。

      :::image type="content" source="media/premium-deploy/alert-message-url.png" alt-text="URL を表示するアラート メッセージ":::

HTML ページの中には、禁止されている他の URL を参照しているために不完全に見えるものがあります。 この問題を解決するために、次の方法をとることができます。

- HTML ページに他のドメインへのリンクが含まれている場合は、それらのドメインを新しいアプリケーション規則に追加して、これらの FQDN へのアクセスを許可することができます。
- HTML ページにサブ URL へのリンクが含まれている場合は、規則を変更し、URL にアスタリスクを追加できます。 例: `targetURLs=www.nytimes.com/section/world*`

   または、新しい URL を規則に追加することもできます。 次に例を示します。 

   `www.nytimes.com/section/world, www.nytimes.com/section/world/*`

### <a name="web-categories-testing"></a>Web カテゴリのテスト

スポーツの Web サイトへのアクセスを許可するアプリケーション規則を作成してみましょう。
1. ポータルから、リソース グループを開いて **DemoFirewallPolicy** を選択します。
2. **[Application Rules]\(アプリケーション規則\)** を選択し、次に **[規則コレクションの追加]** を選択します。
3. **[名前]** に「*GeneralWeb*」、 **[優先度]** に「*103*」と入力し、 **[規則コレクション グループ]** で **DefaultApplicationRuleCollectionGroup** を選択します。
4. **[規則]** で、 **[名前]** に「*AllowSports*」、 **[接続元]** に「 *\** 」、 **[プロトコル]** に「*http, https*」を入力し、 **[TLS 検査]** を選択し、 **[宛先の種類]** で *[Web カテゴリ (プレビュー)]* を選択し、 **[宛先]** で *[Sports]\(スポーツ\)* を選択します。
5. **[追加]** を選択します。

      :::image type="content" source="media/premium-deploy/web-categories.png" alt-text="スポーツ Web カテゴリ":::
6. デプロイが完了したら、**WorkerVM** に移動し、Web ブラウザーを開いて `https://www.nfl.com` に移動します。

   NFL Web ページが表示され、アプリケーション規則のログに、**Web カテゴリ:スポーツ** の規則が一致し、要求が許可されたことが示されます。

## <a name="next-steps"></a>次のステップ

- [Azure portal での Azure Firewall Premium プレビュー](premium-portal.md)