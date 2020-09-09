---
title: Microsoft コマーシャル マーケットプレースからのリード管理
description: Microsoft AppSource および Azure Marketplace のオファーからリードを生成し、受け取る方法について説明します
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: article
author: keferna
ms.author: keferna
ms.date: 03/30/2020
ms.openlocfilehash: 9ee433f226b37c8ffd6ad466cca7cbd844d53524
ms.sourcegitcommit: 3543d3b4f6c6f496d22ea5f97d8cd2700ac9a481
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/20/2020
ms.locfileid: "86535979"
---
# <a name="customer-leads-from-your-commercial-marketplace-offer"></a>コマーシャル マーケットプレース オファーからの顧客リード

リードとは、[Microsoft AppSource](https://appsource.microsoft.com) および [Azure Marketplace](https://azuremarketplace.microsoft.com) からのオファーに関心を示しているか、それをデプロイしている顧客です。 オファーがコマーシャル マーケットプレースに発行された後で、リードを受け取ることができます。 この記事では、リード管理の次の概念について説明します。

* コマーシャル マーケットプレース オファーによってリードを生成して、ビジネス チャンスを逃さないようにする方法。 
* カスタマー リレーションシップ マネジメント (CRM) システムをオファーに接続して、潜在顧客を 1 か所で管理できるようにする方法。
* 連絡をくれた顧客をフォローアップできるように、Microsoft から送信されるリード データ。

## <a name="generate-customer-leads"></a>潜在顧客の生成

リードが生成される場所を次に示します。

- 顧客は、コマーシャル マーケットプレースから **[連絡する]** を選択した後、自身の情報を共有することに同意します。 このリードは、*初めて関心を示した*リードです。 製品を入手することに関心を示した顧客に関する情報が Microsoft によって共有されます。 リードは、取得ファンネルの最上位になります。

    ![Dynamics 365 の [連絡する]](./media/commercial-marketplace-get-customer-leads/dynamics-365-contact-me.png)

- 顧客は、 **[今すぐ入手する]** (または [Azure portal](https://portal.azure.com/)で **[作成]** を選択) します。 このリードは*アクティブ*なリードです。 製品のデプロイを始めた顧客に関する情報が Microsoft によって共有されます。

    ![SQL の [今すぐ入手する] ボタン](./media/commercial-marketplace-get-customer-leads/sql-get-it-now.png)

    ![Windows Server の [作成] ボタン](./media/commercial-marketplace-get-customer-leads/windows-server-create.png)

- お客様は **[体験版]** または **[無料試用版]** を選択して、オファーを試します。 体験版または無料試用版は、自社のビジネスを潜在的顧客と即座に共有するための、参入障壁がない、進度の速い機会です。

    ![Dynamics 365 の [体験版] ボタン](./media/commercial-marketplace-get-customer-leads/dynamics-365-test-drive.png)

    ![Dynamics 365 の [無料試用版] ボタン](./media/commercial-marketplace-get-customer-leads/dynamics-365-free-trial.png)

## <a name="connect-to-your-crm-system"></a>CRM システムに接続する

[!INCLUDE [Test drive content](./includes/connect-lead-management.md)]

## <a name="understand-lead-data"></a>リード データについて

顧客獲得プロセスの間に受け取った各リードに対しては、特定のフィールドにデータが含まれます。 最初に探すフィールドは `LeadSource` フィールドで、次の形式に従います:**Source-Action** | **Offer**。

**Sources**:このフィールドの値は、リードを生成したマーケットプレースに基づいて設定されます。 指定できる値は、`"AzureMarketplace"`、`"AzurePortal"`、`"AppSource (SPZA)"` です。

**Actions**:このフィールドの値は、顧客がマーケットプレース内で行ったアクションに基づいて設定されます。このアクションにより、リードが生成されています。

次のいずれかの値になります。

- **"INS"** :*インストール*を表します。 このアクションは、Azure Marketplace または AppSource で顧客が製品を取得したことを示します。
- **"PLT"** :*パートナー主導の試用版 (Partner Led Trial)* を表します。 このアクションは、AppSource で顧客が **[連絡する]** オプションを選択したことを示します。
- **"DNC"** :*連絡不要*を表します。 このアクションは、AppSource で、アプリ ページにクロスリストされたパートナーへの問い合わせが要求されたことを示します。 この顧客がアプリでクロスリストされたことを通知しますが、顧客に連絡する必要はありません。
- **"Create"** :このアクションは、Azure portal 内でのみ使用され、顧客が自身のアカウントにオファーを購入すると生成されます。
- **"StartTestDrive"** :このアクションは、**体験版**オプションに対してのみ使用され、顧客が体験版を開始すると生成されます。

**Offers**:コマーシャル マーケットプレースには複数のプランがある場合があります。 このフィールドの値は、リードを生成したオファーに基づいて設定されます。 発行者 ID とオファー ID は両方ともこのフィールドで送信されます。これらは、オファーをマーケットプレースに発行したときに指定した値です。

次の例では、有効な形式 `publisherid.offerid` の値を示します。 

- `checkpoint.check-point-r77-10sg-byol`
- `bitnami.openedxcypress`
- `docusign.3701c77e-1cfa-4c56-91e6-3ed0b622145`

## <a name="customer-information"></a>顧客情報

顧客の情報は、複数のフィールドを介して送信されます。 次の例では、リードに含まれる顧客情報が示されています。

- FirstName: John
- LastName: Smith
- Email: jsmith\@microsoft.com
- Phone:1234567890
- 国: US
- Company: Microsoft
- タイトル:CTO

>[!NOTE]
>個々のリードについて、上記の例のすべてのデータが常に得られるわけではありません。 「潜在顧客の生成」セクションで説明したように、リードは複数のステップから得られるので、リードに対応する最良の方法は、レコードの重複を排除し、フォローアップをパーソナライズすることです。 これにより、顧客はそれぞれ適切なメッセージを受け取り、固有の関係を築くことができます。

## <a name="best-practices-for-lead-management"></a>リード管理のベスト プラクティス

販売サイクルを通じてリードを促進するためのいくつかの推奨事項を次に示します。

- **プロセス**: マイルストーン、分析、明確なチーム所有権を持つ明確な販売プロセスを定義します。
- **限定**:リードが完全に条件に合致しているかどうかを示す前提条件を定義します。 販売担当者とマーケティング担当者が完全な販売プロセスを実行する前に、リードを慎重に見込みありと評価するように確認します。
- **フォローアップ**:24 時間以内に忘れずにフォローアップしてください。 顧客が体験版をデプロイした直後に、リードを任意の CRM に追加します。リードの熱が冷めないうちにメールを送信します。 お客様の製品がリードの問題解決に役立っているかどうかをより深く理解するために、電話連絡のスケジュール設定を要求します。 一般的なトランザクションでは、多くのフォローアップの電話が必要です。
- **育成**:利益率を高めるために、リードを育成します。 様子を見ますが、やり過ぎないようにします。 リードをクローズする前に、少なくとも数回メールを送信することをお勧めします。1 回試みただけで諦めないようにしましょう。 その顧客は製品に直接関わり、無料試用版に時間を費やしたことを忘れないでください。有望な見込み客です。

## <a name="common-questions-about-lead-management"></a>リード管理についてよく寄せられる質問

### <a name="where-can-i-get-help-in-setting-up-my-lead-destination"></a>リードの送信先の設定方法に関するヘルプはどこで入手できますか?

「[CRM システムに接続する](#connect-to-your-crm-system)」セクションの手順に従います。または、[パートナー センターのヘルプとサポート](https://aka.ms/marketplacepublishersupport)を通じてサポート チケットを送信します。 次に、 **[オファーの作成]**  >  **[オファーの種類]**  >  **[潜在顧客管理の構成]** を選択し します。

### <a name="am-i-required-to-configure-a-lead-destination-in-order-to-publish-an-offer-in-the-commercial-marketplace"></a>コマーシャル マーケットプレースでオファーを公開するには、リードのターゲットを構成する必要がありますか?

回答は、発行しているオファーの種類によって異なります。 サービスとしてのソフトウェア (SaaS) および Dynamics 365 for Customer Engagement では、 **[連絡する]** を使用してすべての Dynamics 365 for Finance and Operations オファー、すべての Dynamics 365 Business Central オファー、すべてのコンサルティング サービス オファーを表示します。 その結果、リードのターゲットに接続する必要があります。 オファーの種類が一覧に表示されていない場合は、リードのターゲットへの接続は必要ありません。 ビジネス チャンスを逃さないように、リードのターゲットを構成することをお勧めします。

### <a name="how-can-i-find-the-test-lead"></a>テスト リードを確認するにはどうすればよいですか?

リードのターゲットで `"MSFT_TEST"` を検索します。 Microsoft のテスト リードの例を以下に示します。 テスト リードの形式は、リードのターゲットによって異なります。

```
{
    "UserDetails": {
      "FirstName": "MSFT_TEST_636573304831318844",
      "LastName": "MSFT_TEST_636573304831318844",
      "Email": "MSFT_TEST_636573304831318844@test.com",
      "Phone": "1234567890",
      "Country": "US",
      "Company": "MSFT_TEST_636573304831318844",
      "Title": "MSFT_TEST_636573304831318844"
    },
    "LeadSource": "AzureMarketplace",
    "ActionCode": "INS",
    "OfferTitle": "Contoso Test"
    "Description": "MSFT_TEST_636573304831318844"
}
```

### <a name="i-have-a-live-offer-but-why-am-i-not-seeing-any-leads"></a>ライブ オファーがあるのですが、なぜリードが表示されないのですか?

リードのターゲットへの接続が有効であることを確認します。 パートナー センター内でオファーの **[発行]** を選択すると、Microsoft からテスト リードが送信されます。 テスト リードが表示された場合、接続は有効です。 また、プレビュー手順でオファーのプレビューの取得を試みることによっても、リードの接続をテストすることができます。 コマーシャル マーケットプレースの一覧にある、 **[今すぐ入手する]** 、 **[連絡する]** 、または、 **[無料試用版]** を選択します。

また、正しいデータを探していることを確認してください。 この記事の「[リード データについて](#understand-lead-data)」セクションの内容では、リードのターゲットに送信されるリード データについて説明しています。

### <a name="i-configured-azure-blob-storage-as-my-lead-destination-but-why-dont-i-see-the-lead"></a>リードのターゲットとして Azure BLOB ストレージを構成したのですが、リードが表示されないのはなぜですか?

Azure BLOB ストレージはリードのターゲットとしてサポートされなくなったため、オファーによって生成された潜在顧客は失われています。 他のいずれかの[リードのターゲット オプション](./commercial-marketplace-get-customer-leads.md)に切り替えてください。 

### <a name="i-received-an-email-from-the-commercial-marketplace-but-why-cant-i-find-the-lead-in-my-crm"></a>コマーシャル マーケットプレースからの電子メールを受信したのですが、CRM でリードを確認できないのはなぜですか?

エンドユーザーの電子メール ドメインが .edu である可能性があります。 プライバシー上の理由から、個人情報が .edu ドメインから渡されることはありません。 [パートナー センターのヘルプとサポート](https://aka.ms/marketplacepublishersupport)を通じてサポート チケットを送信します。

### <a name="i-configured-an-azure-table-as-my-lead-destination-how-can-i-view-the-leads"></a>Azure テーブルをリードのターゲットとして構成しました。 リードを表示するにはどうすればよいですか?

Azure テーブルに格納されているリード データには、Azure portal からアクセスできます。 また、zure Storage アカウントのテーブル データを表示するために [Azure Storage Explorer](https://azure.microsoft.com/features/storage-explorer/) を無料でダウンロードしてインストールすることもできます。

### <a name="i-configured-an-azure-table-as-my-lead-destination-can-i-get-notified-whenever-a-new-commercial-marketplace-lead-is-sent"></a>Azure テーブルをリードのターゲットとして構成しました。 新しいコマーシャル マーケットプレースのリードが送信されるたびに通知を受け取ることはできますか?

はい。 [Azure テーブルを使用したリード管理の構成](./commercial-marketplace-lead-management-instructions-azure-table.md)に関するページの手順に従い、Azure テーブルにリードが追加された場合にメールを送信する Microsoft フローを設定します。

### <a name="i-configured-salesforce-as-my-lead-destination-but-why-cant-i-find-the-leads"></a>リードのターゲットとして Salesforce を構成したのですが、リードが見つからないのはなぜですか?

Web-to-Lead フォームが候補リストに基づく必須フィールドになっているかどうかを確認してください。 なっている場合は、フィールドを任意指定のテキスト フィールドに切り替えてください。

### <a name="there-was-an-issue-with-my-lead-destination-and-i-missed-some-leads-can-i-have-them-sent-to-me-in-an-email"></a>リードのターゲットに関して問題があり、一部のリードを取得できませんでした。 リードを電子メールで受信することはできますか?

個人情報に関するポリシーがあるため、セキュリティ保護されていない電子メールでリード情報を共有することはできません。

### <a name="i-configured-an-azure-table-as-my-lead-destination-how-much-will-it-cost"></a>Azure テーブルをリードのターゲットとして構成しました。 料金はいくらかかりますか?

リード生成データは小さいものです。 ほとんどすべてのパブリッシャーで 1 GB 未満です。 コストは、受信したリードの数によって異なります。 たとえば、1 か月に 1,000 のリードを受信した場合、コストは約 50 セントになります。 ストレージの価格の詳細については、「[Azure Storage の価格の概要](https://azure.microsoft.com/pricing/details/storage/)」を参照してください。

質問への回答がなかった場合は、[パートナー センターのヘルプとサポート](https://aka.ms/marketplacepublishersupport)から Microsoft サポートにお問い合わせください。 次に、 **[オファーの作成]**  >  **[オファーの種類]**  >  **[潜在顧客管理の構成]** を選択し します。

### <a name="im-receiving-email-notifications-when-new-customer-leads-are-received-how-can-i-configure-someone-else-to-receive-these-emails"></a>新しい顧客リードを受信したときに私宛に電子メール通知が届きます。 これらの電子メールを受信する別のユーザーを構成するにはどうすればよいですか?

パートナーセンターでオファーにアクセスします。 **[オファーのセットアップ]** ページに移動し、 **[リード管理]**  >  **[編集]** を選択します。 **[連絡先の電子メール]** フィールドの電子メール アドレスを更新します。

## <a name="next-steps"></a>次のステップ

技術的な設定が完了したら、これらのリードを現在の販売/マーケティング戦略と運用プロセスに組み込みます。 Microsoft は、パートナーの販売プロセス全体について理解を深めることに関心を持っており、パートナーを成功に導くために、パートナーと緊密に連携して質の高いリードと十分なデータを提供していきたいと考えてます。 顧客を成功に導く上で役立つ追加情報と共に送信されるリードを最適化し、強化する方法についてフィードバックをお待ちしています。 コマーシャル マーケットプレースのリードについて販売チームがより大きな成果を挙げることができるようにするための[フィードバック](mailto:AzureMarketOnboard@microsoft.com)や提案に関心がある場合は、ぜひお知らせください。
