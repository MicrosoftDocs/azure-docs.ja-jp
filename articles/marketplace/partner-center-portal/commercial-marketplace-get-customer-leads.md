---
title: 潜在顧客の構成 | Azure Marketplace
description: 商業マーケットプレースで潜在顧客を構成します。
services: Azure, Marketplace, commercial marketplace, Partner Center
author: qianw211
ms.service: marketplace
ms.topic: conceptual
ms.date: 08/01/2019
ms.author: evansma
ms.openlocfilehash: 31dcc8c1e35b627b231dbe2a62998c8514d05a20
ms.sourcegitcommit: d3dced0ff3ba8e78d003060d9dafb56763184d69
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/22/2019
ms.locfileid: "69901543"
---
# <a name="customer-leads-from-your-marketplace-offer"></a>マーケットプレース オファーからの潜在顧客

リードとは、[Azure Marketplace](https://azuremarketplace.microsoft.com) からのオファーまたは [AppSource](https://appsource.microsoft.com) からのオファーに関心を示しているか、それをデプロイしている顧客です。 オファーがマーケットプレースに発行された後で、潜在顧客を受け取ります。 この記事では、次のことについて説明します。

* マーケットプレース オファーによって潜在顧客を生成して、ビジネス チャンスを逃さないようにする方法。 
* CRM をオファーに接続して、リードを 1 か所で集中的に管理できるようにします。
* 連絡をくれた顧客をフォローアップできるように、マイクロソフトから送信されるリード データを理解します。

## <a name="generate-customer-leads"></a>潜在顧客の生成

リードが生成される場所を次に示します。

1. 顧客は、マーケットプレースから [連絡する] を選択した後、自身の情報を共有することに同意します。 このリードは**初めて関心を示した**リードであり、この場合は製品を入手することに関心を示した顧客に関する情報がマイクロソフトによって共有されます。 リードは、取得ファンネルの最上位になります。

      ![Dynamics 365 の [連絡する]](./media/commercial-marketplace-get-customer-leads/dynamics-365-contact-me.png)

2. 顧客がオファーを取得するために [今すぐ入手する] または [作成] ([Azure portal](https://portal.azure.com/) 内) を選択した場合、このリードは**アクティブ リード**になり、製品のデプロイを開始した顧客に関する情報がマイクロソフトによって共有されます。

    ![SQL の [今すぐ入手する]](./media/commercial-marketplace-get-customer-leads/sql-get-it-now.png)

    ![Windows Server の [作成]](./media/commercial-marketplace-get-customer-leads/windows-server-create.png)

3. 顧客は、"体験版" を取得するか、オファーの "無料試用版" を開始します。 体験版または無料試用版は、自社のビジネスを潜在的顧客と即座に共有するための、参入障壁がない、進度の速い機会です。

    ![Dynamics 365 の体験版](./media/commercial-marketplace-get-customer-leads/dynamics-365-test-drive.png)

    ![Dynamics 365 の体験版](./media/commercial-marketplace-get-customer-leads/dynamics-365-free-trial.png)

## <a name="connect-to-your-crm-system"></a>CRM システムに接続する

[!INCLUDE [Test drive content](./includes/connect-lead-management.md)]

## <a name="understand-lead-data"></a>リード データについて

顧客獲得プロセスの間に受け取った各リードに対しては、特定のフィールドにデータが含まれます。 最初に探すフィールドは `LeadSource` フィールドで、次の形式に従います:**Source-Action** | **Offer**。

**Sources**:このフィールドの値は、リードを生成したマーケットプレースに基づいて設定されます。 指定できる値は、`"AzureMarketplace"`、`"AzurePortal"`、`"AppSource (SPZA)"` です。

**Actions**:このフィールドの値は、顧客がマーケットプレース内で行ったアクションに基づいて設定されます。このアクションにより、リードが生成されています。 

次のいずれかの値になります。

- "INS" -- インストール。 このアクションは、Azure Marketplace または AppSource で顧客が製品を購入したことを示します。
- "PLT" -- パートナー主導の試用版 (Partner Led Trial) を表します。 このアクションは、AppSource で顧客が [連絡する] オプションを使用したことを示します。
- "DNC" -- 連絡不要 (Do Not Contact) を表します。 このアクションは、AppSource で、アプリ ページにクロスリストされたパートナーへの問い合わせが要求されたことを示します。 この顧客がアプリでクロスリストされたことが通知されますが、顧客に連絡する必要はありません。
- "Create" -- このアクションは、Azure portal 内でのみ使用され、顧客が自身のアカウントにオファーを購入すると生成されます。
- "StartTestDrive" -- このアクションは、体験版に対してのみ使用され、顧客が体験版を開始すると生成されます。

**Offers**:マーケットプレースに自分のオファーが複数ある場合があります。 このフィールドの値は、リードを生成したオファーに基づいて設定されます。 発行者 ID とオファー ID は両方ともこのフィールドで送信されます。これらは、オファーをマーケットプレースに発行したときに指定した値です。

次の例では、有効な形式 `publisherid.offerid` の値の例を示します。 

1. `checkpoint.check-point-r77-10sg-byol`
1. `bitnami.openedxcypress`
1. `docusign.3701c77e-1cfa-4c56-91e6-3ed0b622145`

## <a name="customer-info"></a>顧客の情報

顧客の情報は、複数のフィールドを介して送信されます。 次の例では、リードに含まれる顧客情報が示されています。

- FirstName: John
- LastName: Smith
- Email: jsmith\@microsoft.com
- Phone: 1234567890
- Country: US
- Company: Microsoft
- タイトル:CTO

>[!Note]
>個々のリードについて、上記の例のすべてのデータが常に得られるわけではありません。 潜在顧客のセクションで説明したように、リードは複数のステップから得られるので、リードに対応する最良の方法は、レコードの重複を排除し、フォローアップをパーソナライズすることです。 これにより、顧客はそれぞれ適切なメッセージを受け取ることができ、固有の関係を築くことができます。

## <a name="best-practices-for-lead-management"></a>リード管理のベスト プラクティス

1. "*プロセス*" - マイルストーン、KPI、明確なチーム所有権を持つ明確な販売プロセスを定義します。
2. "*修飾子*" - リードが完全に修飾されているかどうかを示す前提条件を定義します。 販売担当者とマーケティング担当者が完全な販売プロセスを実行する前に、リードを慎重に見込みありと評価するようにします。
3. "*フォローアップ*" - フォローアップを忘れないでください。一般的なトランザクションでは、5 から 12 回のフォローアップの電話が必要です
4. "*育成*" - 利益率を高めるために、リードを育成します。

## <a name="leads-frequently-asked-questions"></a>リードについてよく寄せられる質問

### <a name="where-can-i-get-help-in-setting-up-my-lead-destination"></a>リードの送信先の設定方法に関するヘルプはどこで入手できますか?

[こちら](#connect-to-your-crm-system)のドキュメントを参照するか、aka.ms/marketplacepublishersupport からサポート チケットを発行し、 **[offer creation]\(オファーの作成\)** → **自分のオファーの種類** → **[lead management configuration]\(リード管理構成\)** を選択できます。

### <a name="am-i-required-to-configure-a-lead-destination-in-order-to-publish-an-offer-on-the-marketplace"></a>マーケットプレースでオファーを公開するには、リードのターゲットを構成する必要がありますか?

回答は、発行しているオファーの種類によって異なります。 [連絡する] として表示する SaaS および Dynamics 365 for Customer Engagement オファー、すべての Dynamics 365 for Operations オファー、すべての Dynamics 365 Business Central オファー、すべてのコンサルティング サービス オファーには、リードのターゲットへの接続が必要です。 自分のオファーの種類が一覧にない場合は、これは必須ではありません。 ただし、ビジネス チャンスを逃さないように、リードのターゲットを構成することをお勧めします。

### <a name="how-can-i-find-the-test-lead"></a>テスト リードを確認するにはどうすればよいですか?

リードのターゲット内で `“MSFT_TEST”` を検索します。次に示すのは、マイクロソフトのテスト リードのサンプルです。

```
company = MSFT_TEST_636573304831318844
country = US
description = MSFT_TEST_636573304831318844
email = MSFT_TEST_636573304831318844@test.com
encoding = UTF-8
encoding = UTF-8
first_name = MSFT_TEST_636573304831318844
last_name = MSFT_TEST_636573304831318844
lead_source = MSFT_TEST_636573304831318844-MSFT_TEST_636573304831318844|<Offer Name>
oid = 00Do0000000ZHog
phone = 1234567890
title = MSFT_TEST_636573304831318844
```

### <a name="i-have-a-live-offer-but-im-not-seeing-any-leads"></a>ライブ オファーがあるのですが、リードが表示されません。

リードのターゲットへの接続が有効であることを確認します。 パートナー センター内でオファーの [発行] をクリックすると、マイクロソフトからテスト リードが送信されます。 テスト リードが表示された場合、接続は有効です。 また、マーケットプレース内の一覧で [今すぐ入手する]、[連絡する]、または [無料試用版] をクリックして、プレビュー ステップ中にオファーのプレビューを取得してみることで、リードの接続をテストすることもできます。

また、正しいデータを探していることを確認してください。 このドキュメントの「[リード データについて](#understand-lead-data)」セクションの内容では、リードのターゲットに送信されるリード データについて説明しています。

### <a name="i-have-configured-azure-blob-as-my-lead-destination-why-dont-i-see-the-lead"></a>リードのターゲットとして Azure BLOB を構成したのですが、リードが表示されないのはなぜですか?

Azure BLOB リードのターゲットはサポートされなくなったため、オファーによって生成された潜在顧客は失われています。 他のいずれかの[リードのターゲット オプション](./commercial-marketplace-get-customer-leads.md)に切り替えてください。 

### <a name="i-received-an-email-from-marketplace-why-cant-i-find-the-lead-in-my-crm"></a>Marketplace からの電子メールを受信したのですが、CRM でリードを確認できないのはなぜですか?

エンドユーザーの電子メール ドメインが .edu である可能性があります。 プライバシー上の理由により、.edu ドメインからの PII データを渡すことはできません。 aka.ms/marketplacepublishersupport からサポート チケットを送信してください。

### <a name="i-have-configured-azure-table-as-my-lead-destination-how-can-i-view-the-leads"></a>リードのターゲットとして Azure テーブルを構成したのですが、リードを表示するにはどうすればよいですか?

Azure テーブルに格納されているリード データには Azure Portal からアクセスできます。また、[Azure Storage Explorer](https://azure.microsoft.com/features/storage-explorer/) を無料でダウンロードしてインストールし、Azure ストレージ アカウントのテーブル データを表示することもできます。

### <a name="i-have-configured-azure-table-as-my-lead-destination-can-i-get-notified-whenever-a-new-lead-is-sent-by-marketplace"></a>リードのターゲットとして Azure Table を構成したのですが、Marketplace から新しいリードが送信された際に通知を受け取ることはできますか?

はい。[こちら](./commercial-marketplace-lead-management-instructions-azure-table.md)のドキュメントで、Azure テーブルにリードが追加された場合は、メールを送信する Microsoft フローの設定手順に従います。

### <a name="i-have-configured-salesforce-as-my-lead-destination-why-cant-i-find-the-leads"></a>リードのターゲットとして Salesforce を構成したのですが、リードが見つからないのはなぜですか?

"Web-to-Lead" フォームが候補リストに基づく必須フィールドになっているかどうかを確認してください。 なっている場合は、フィールドを任意指定のテキスト フィールドに切り替えてください。

### <a name="there-was-an-issue-with-my-lead-destination-and-i-missed-some-leads-can-i-have-them-sent-to-me-in-an-email"></a>リードのターゲットに関して問題があり、一部のリードを取得できませんでした。 リードを電子メールで受信することはできますか?

PII (個人を特定できる情報) ポリシーがあるため、セキュリティ保護されていない電子メールでリード情報を共有することはできません。

### <a name="i-have-configured-azure-table-as-my-lead-destination-how-much-will-it-cost"></a>リードのターゲットとして Azure テーブルを構成したのですが、コストはどれくらいになりますか?

リード生成データは少量です (ほぼすべてのパブリッシャーで 1 GB 未満です)。 コストは受信したリードの数によって異なりますが、1 か月間に 1,000 件のリードを受信した場合、コストは約 50 セントになります。 ストレージの価格について詳しくは、[ストレージの価格](https://azure.microsoft.com/pricing/details/storage/)に関する記事をご覧ください。

質問の回答がまだない場合は、aka.ms/marketplacepublishersupport からサポートに連絡し、 **[offer creation]\(オファーの作成\)** → **自分のオファーの種類** → **[lead management configuration]\(リード管理構成\)** を選択できます。 

## <a name="next-steps"></a>次の手順

技術的な設定が完了したら、これらのリードを現在の販売/マーケティング戦略と運用プロセスに組み込む必要があります。 Microsoft は、パートナーの販売プロセス全体について理解を深めることに関心を持っており、パートナーを成功に導くために、パートナーと緊密に連携して質の高いリードと十分なデータを提供していきたいと考えてます。 顧客を成功に導く上で役立つ追加情報と共に送信されるリードを最適化し、強化する方法についてフィードバックをお待ちしています。 Marketplace Leads によって販売チームがより大きな成果を挙げることができるようにするための[フィードバック](mailto:AzureMarketOnboard@microsoft.com)や提案に関心がある場合は、ぜひお知らせください。
