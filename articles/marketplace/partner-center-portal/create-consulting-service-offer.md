---
title: コンサルティング サービス オファーを作成する - Microsoft コマーシャル マーケットプレース
description: パートナー センターを使用して、コンサルティング サービス オファーを Microsoft AppSource または Azure Marketplace に公開する方法について説明します。
author: Microsoft-BradleyWright
ms.author: brwrigh
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 06/17/2020
ms.openlocfilehash: 03fa302d2e8aa173419912b99d60734c7a4e0356
ms.sourcegitcommit: d39f2cd3e0b917b351046112ef1b8dc240a47a4f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/25/2020
ms.locfileid: "88814266"
---
# <a name="create-a-consulting-service-offer"></a>コンサルティング サービス オファーの作成

この記事では、[Microsoft AppSource](https://appsource.microsoft.com/) または [Azure Marketplace](https://azuremarketplace.microsoft.com/) のいずれかにコンサルティング サービス オファーを公開する方法について説明します。 AppSource 上で Microsoft [Dynamics 365](https://dynamics.microsoft.com/) および Power Platform に基づくコンサルティング サービス オファーを一覧表示します。 Azure Marketplace 上で Microsoft Azure に基づくコンサルティング サービス オファーを一覧表示します。 開始する前に、まだ[パートナー センターでコマーシャル マーケットプレース アカウントを作成](create-account.md)していなければ、作成します。 ご自分のアカウントがコマーシャル マーケットプレース プログラムに登録されていることを確認します。

オファーを作成する前に、「[コンサルティング サービスの前提条件](consulting-service-prerequisites.md)」にある前提条件を確認してください。

## <a name="publishing-benefits"></a>公開の利点

コマーシャル マーケットプレースに公開する利点:

- Microsoft のブランドを使用して、会社の宣伝ができる。
- AppSource 上で 1 億を超える Office 365 および Dynamics 365 ユーザーに、さらに Azure Marketplace を通じて 20 万を超える組織にリーチできる可能性がある。
- これらのマーケット プレースから質の高い潜在顧客を得ることができる。
- Microsoft のフィールド チームとテレセールス チームによりサービスを宣伝してもらえる。

## <a name="create-a-new-offer"></a>新しいオファーを作成する

1. [パートナー センター](https://partner.microsoft.com/dashboard/home)にサインインします。
2. 左側のナビゲーション メニューで、 **[コマーシャル マーケットプレース]** 、 >  **[概要]** の順に選択します。
3. [概要] ページで、 **[+ 新しいオファー]** 、 >  **[コンサルティング サービス]** の順に選択します。

    ![左側のナビゲーション メニューの画像。](./media/new-offer-consulting-service.png)

>[!NOTE]
>オファーが公開された後、それに対してパートナー センターで行われた編集内容は、そのオファーの再公開後にのみオンライン ストアに表示されます。 変更後は必ず再公開してください。

## <a name="new-offer"></a>新しいプラン

**[オファー ID]** を入力します。 自分のアカウント内の各オファーに対する一意の識別子です。

- この ID は、マーケットプレース オファーの Web アドレスで顧客に表示されます。
- 使用できるのは小文字と数字だけです。 ハイフンとアンダースコアを含めることができますが、スペースは使用できず、文字数は 50 文字に制限されています。 たとえば、「**test-offer-1**」と入力すると、オファーの Web アドレスは `https://azuremarketplace.microsoft.com/marketplace/../test-offer-1` になります。
- **[作成]** を選択した後で、オファー ID を変更することはできません。

**[オファーのエイリアス]** を入力します。 これは、パートナー センター内でオファーに使用される名前です。

- この名前はマーケットプレースでは使用されず、顧客に表示されるオファー名やその他の値とは異なります。
- **[作成]** を選択した後で、オファーの別名を変更することはできません。

**[作成]** を選択してオファーを生成し、続行します。

## <a name="offer-setup"></a>オファーのセットアップ

### <a name="connect-lead-management"></a>リード管理の接続

パートナー センターを使用してオファーをマーケットプレースに公開する場合は、それを顧客関係管理 (CRM) またはマーケティング自動化システムに接続する_必要があります_。 これにより、自社の製品に顧客が関心を示したり、製品を使用したりした場合はすぐにその顧客の連絡先情報を受信できるようになります。

1. **[接続]** を選択して、潜在顧客の送信先となる場所を指定します。 パートナー センターでは、次のシステムがサポートされています。

    - [Dynamics 365](commercial-marketplace-lead-management-instructions-dynamics.md) for Customer Engagement
    - [Marketo](commercial-marketplace-lead-management-instructions-marketo.md)
    - [Salesforce](commercial-marketplace-lead-management-instructions-salesforce.md)

    > [!NOTE]
    > ご利用の CRM システムが上記に記載されていない場合は、[Azure テーブル](commercial-marketplace-lead-management-instructions-azure-table.md)または [HTTPS エンドポイント](commercial-marketplace-lead-management-instructions-https.md)を使用して潜在顧客のデータを格納し、次にそのデータをご利用の CRM システムにエクスポートします。

2. パートナー センターで公開する際は、オファーをリードの宛先に接続します。
3. リードの宛先への接続が正しく構成されていることを確認します。 パートナー センターで公開すると、Microsoft で接続を検証して、テスト リードを送信します。 オファーの公開前に行うプレビュー中は、プレビュー環境でオファーを自分で購入してみることで、リードの接続をテストすることもできます。
4. リードが失われないように、リードの宛先への接続が確実に最新の状態に保たれているようにします。

その他のリード管理リソースをいくつか次に示します。

- [リード管理の概要](commercial-marketplace-get-customer-leads.md)
- [リード管理に関する FAQ](../lead-management-for-cloud-marketplace.md#frequently-asked-questions)
- [一般的なリード構成エラー](../lead-management-for-cloud-marketplace.md#publishing-config-errors))
- [リード管理の概要](https://assetsprod.microsoft.com/mpn/cloud-marketplace-lead-management.pdf) PDF (ポップアップ ブロックが無効になっていることを確認してください)

続行する前に、 **[下書きの保存]** を選択します。

### <a name="properties"></a>Properties

このページでは、コンサルティング サービス オファーで最もよくカバーする主要な製品の設定、コンサルティング サービスの種類の設定、適用可能な製品の選択を行うことができます。

1. ドロップダウン リストから **[主要な製品]** を選択します。
2. ドロップダウン リストから **[コンサルティングサービスの種類]** を選択します。

    - **評価**:お客様の環境を評価し、ソリューションの適用性を確認して、コストとタイミングの見積りを提供します。
    - **ブリーフィング**:ソリューションまたはコンサルティング サービスの概要を紹介し、フレームワーク、デモ、顧客事例を使って顧客の関心を引きます。
    - **実装**:完全に機能するソリューションを生成する完全なインストール。 2 週間以内に実装できるソリューションに限定します。
    - **概念実証**:ソリューションがお客様の要件を満たすかどうかを判断するために、限られた範囲の実装を行います。
    - **ワークショップ**:顧客の施設内で実施される対話形式のエンゲージメント。 これには、トレーニング、ブリーフィング、評価、または顧客のデータまたは環境に対して構築されたデモを含めることができます。

3. 主要な製品として **Azure** を選択した場合は、 **[ソリューションの分野]** を最大 3 つ選択します。 これにより、Azure Marketplace の顧客は、貴社のオファーをさらに容易に見つけられるようになります。 Azure を選択しなかった場合は、この手順をスキップします。

    - Analytics
    - アプリの最新化
    - アーカイブ
    - AI + 機械学習
    - バックアップ
    - ビッグ データ
    - データ プラットフォーム
    - データセンターの管理
    - DevOps
    - ディザスター リカバリー
    - ID
    - モノのインターネット (IoT)
    - 移行
    - ネットワーク
    - セキュリティ
    - ストレージ

1. 主要な製品として **Azure** を選択した場合は、最大 6 つの **[業界]** を選択できます。 これにより、Azure Marketplace の顧客は、貴社のオファーをさらに容易に見つけられるようになります。 業界の完全な一覧については、「[オファーの掲載のベスト プラクティス](../gtm-offer-listing-best-practices.md)」をご覧ください。 Azure を選択しなかった場合は、この手順をスキップします。
1. Azure *以外の*主要製品を選択した場合は、**適用対象の製品**を最大 3 つ選択します。 これにより、AppSource の顧客は、貴社のオファーをさらに容易に見つけられるようになります。 詳細については、「[Microsoft AppSource のコンサルティング サービスの掲載に関するガイドライン](https://go.microsoft.com/fwlink/?LinkId=828734&amp;clcid=0x409)」 (PDF) を参照してください。
1. Azure "*以外の*" 主要な製品を選択した場合は、最大 2 つの **[業界]** と、業界ごとに 2 つの **[業種]** を選択できます。 これにより、AppSource の顧客は、貴社のオファーをさらに容易に見つけられるようになります。 業界と業種の完全な一覧については、「[オファーの掲載のベスト プラクティス](../gtm-offer-listing-best-practices.md)」をご覧ください。
1. コンサルティング サービス オファーのリストに表示するために獲得した**コンピテンシー** を 3 つまで追加できます。 Azure Expert MSP および Azure Networking MSP を除き、少なくとも 1 つのコンピテンシーが必要です。

続行する前に、 **[下書きの保存]** を選択します。

## <a name="offer-listing"></a>オファーのリスト登録

ここでは、マーケットプレースに表示されるオファーの詳細を定義します。 これには、オファーの名前、説明、画像などが含まれます。 このオファーを構成するとき、[コマーシャル マーケットプレース認定ポリシー ページ](https://docs.microsoft.com/legal/marketplace/certification-policies#800-consulting-services)に記載されているポリシーに従ってください。

> [!NOTE]
> オファーの説明が「&quot;このアプリケーションは、[英語以外の言語] でのみ利用可能です&quot;」という文言で始まっている場合、オファーの詳細は英語である必要はありません。また、オファー コンテンツへの役に立つリンクを、オファーのリスト登録の詳細説明で使用されているものとは異なる言語で提供することもできます。

Azure Marketplace でのオファー情報の表示例を次に示します (表示されている価格は例示のみを目的としており、実際のコストを反映することを意図していません)。

:::image type="content" source="media/example-consulting-service-offer.png" alt-text="Azure Marketplace でこのオファーがどのように表示されるかを示しています。":::

#### <a name="call-out-descriptions"></a>コールアウトの説明

1. ロゴ
2. Price
3. ソリューション分野
4. 業界
5. プラン名
6. まとめ
7. 説明
8. スクリーンショット/ビデオ

<br>Microsoft AppSource でのオファー情報の表示例を次に示します (表示されている価格は例示のみを目的としており、実際のコストを反映することを意図していません)。

:::image type="content" source="media/example-consulting-service-offer-appsource.png" alt-text="Microsoft AppSource でこのオファーがどのように表示されるかを示しています。":::

#### <a name="call-out-descriptions"></a>コールアウトの説明

1. ロゴ
2. Price
3. 製品
4. 業界
5. プラン名
6. まとめ
7. 説明
8. スクリーンショット/ビデオ
9. Documents

### <a name="name"></a>名前

ここで入力する名前は、オファリングのタイトルとして表示されます。 このフィールドには、オファーを作成したときに **[オファーの別名]** ボックスに入力したテキストがあらかじめ入力されています。 この名前は後で変更できます。

この名前は、次のようにすることができます。

- 商標登録されている可能性があります (商標または著作権マークを含めることもできます)。
- 文字長が 50 文字を超えてはなりません。
- 絵文字を含めることはできません。

### <a name="search-results-summary"></a>検索結果の概要

オファーの簡単な説明を入力します。 これは最大 100 文字長とすることができ、マーケットプレースの検索結果で使用されます。

### <a name="description"></a>説明

[!INCLUDE [Long description-1](./includes/long-description-1.md)]

[!INCLUDE [Long description-2](./includes/long-description-2.md)]

[!INCLUDE [Long description-3](./includes/long-description-3.md)]

### <a name="keywords"></a>Keywords

主要な製品およびコンサルティング サービスに関連する検索キーワードを最大で 3 つ入力します。 これらを使用することで、オファーをより容易に見つけることができます。

### <a name="duration"></a>Duration

顧客とのこの契約の想定期間を設定します。

### <a name="contact-information"></a>連絡先情報

**第 1** および**第 2** の連絡先として名前、電子メール アドレス、および電話番号を入力する必要があります。 この情報は顧客には表示されません。 これは Microsoft で利用することができ、クラウド ソリューション プロバイダー (CSP) パートナーに提供される場合があります。

### <a name="supporting-documents"></a>サポート ドキュメント

オファー用のサポート ドキュメント (PDF) を最大 3 つ (ただし、少なくとも 1 つ) を追加します。

### <a name="marketplace-images"></a>マーケットプレースの画像

オファーで使用するロゴと画像を提供します。 画像はすべて .png 形式である必要があります。 ぼやけた画像は拒否されます。

[!INCLUDE [logotips](../includes/graphics-suggestions.md)]

>[!Note]
>ファイルのアップロードで問題が発生した場合は、パートナー センターで使用されている https://upload.xboxlive.com サービスがローカル ネットワークでブロックされていないことを確認してください。

#### <a name="store-logos"></a>ストア ロゴ

**大**サイズのロゴに PNG ファイルを指定します。 パートナー センターでは、これを使用して、**小**サイズのロゴを作成します。 これは、必要に応じて、後で別の画像に置き換えることができます。

- **大** (216 x 216 から 350 x 350 px、必須)
- **小** (48 x 48 px、省略可能)

これらのロゴは、リスト登録のさまざまな場所で使用されます。

[!INCLUDE [Logo tips](../includes/graphics-suggestions.md)]

#### <a name="screenshots-optional"></a>スクリーンショット (省略可能)

オファーがどのように動作するかを示すスクリーンショットを最大 5 つ追加します。 それぞれが 1280 x 720 ピクセルのサイズで PNG 形式である必要があります。

#### <a name="videos-optional"></a>ビデオ (省略可能)

オファーをデモンストレーションするビデオを最大 4 つ追加します。 ビデオの名前、Web アドレス (URL)、およびビデオのサムネイル PNG 画像 (1280 x 720 ピクセルのサイズ)。

続行する前に、 **[下書きの保存]** を選択します。

## <a name="pricing-and-availability"></a>価格と可用性

ここでは、価格、市場、秘密キーなどの要素を定義します。

1. **市場**: オファーを利用可能にする市場を設定します。 オファーごとに市場を 1 つだけ選択できます。
    1. 米国またはカナダの市場では、 **[Edit states]\(州の編集\)** (または**州**) を選択して、オファーを利用可能にする場所を指定します。
2. **プレビュー対象ユーザー**: オファーのプライベート対象ユーザーを設定するには、 **[キーの非表示]** を構成します。
3. **価格**: オファーが**無料**オファーまたは**有料**オファーのいずれであるかを指定します。

    > [!NOTE]
    > コンサルティング サービス オファーは、登録情報のみを対象としています。 トランザクションは、コマーシャル マーケットプレースの外部で直接行われます。

4. 有料オファーの場合は、**価格と通貨**を指定すると共に、価格が**固定**であるか**見積もり**であるかを指定します。 見積もりの場合は、価格に影響を与える要因を説明に指定する必要があります。
5. 続行する前に、 **[下書きの保存]** を選択します。

## <a name="review-and-publish"></a>レビューと発行

オファーの必須セクションをすべて完了したら、レビューおよび公開のためにそのオファーを送信することができます。

1. コンサルティング サービス オファーを公開する準備ができたら、 **[レビューと公開]** をクリックします。
2. 最終送信ページで詳細を確認します。
3. オファーの詳細のいずれかで説明が必要だと思われる場合は、必要に応じて認定チームへのメモを書き込んでください。
4. 準備ができたら、 **[送信]** を選択します
5. **[オファーの概要]** ページに、オファーがどのような公開段階にあるかが示されます。

オファーの各公開ステージでの予想される所要期間の詳細については、「[Commercial Marketplace のオファーの発行状態を確認する](publishing-status.md)」をご覧ください。

## <a name="update-your-existing-consulting-service-offers"></a>既存のコンサルティング サービス オファーを更新する

- [コマーシャル マーケットプレースで既存のオファーを更新する](update-existing-offer.md)
