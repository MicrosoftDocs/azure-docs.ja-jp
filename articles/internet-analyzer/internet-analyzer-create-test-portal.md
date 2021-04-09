---
title: ポータルを使用して Internet Analyzer テストを作成する | Microsoft Docs
description: この記事では、最初の Internet Analyzer テストを作成する方法について説明します。
services: internet-analyzer
author: megan-beatty
ms.service: internet-analyzer
ms.topic: tutorial
ms.date: 10/16/2019
ms.author: mebeatty
ms.openlocfilehash: 64bbed9b558d4c20889b28a5247e2113d20daa77
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "73509836"
---
# <a name="create-an-internet-analyzer-test-using-portal-preview"></a>ポータルを使用して Internet Analyzer テストを作成する (プレビュー)

Internet Analyzer リソースを作成する方法は 2 つあります。Azure portal を使用する方法と、[CLI](internet-analyzer-cli.md) を使用する方法です。 このセクションは、ポータルを使用して新しい Azure Internet Analyzer リソースを作成するのに役立ちます。

> [!IMPORTANT]
> このパブリック プレビュー版はサービス レベル アグリーメントなしで提供されています。運用環境のワークロードに使用することは避けてください。 特定の機能はサポート対象ではなく、機能が制限されることがあるか、Azure の場所によっては利用できない場合があります。 詳しくは、「[Microsoft Azure プレビューの追加使用条件](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)」をご覧ください。
>

## <a name="before-you-begin"></a>開始する前に

パブリック プレビューは世界中で使用できます。ただしデータ ストレージは、プレビュー期間中は *米国西部 2* に限定されます。

## <a name="basics"></a>基本

1. [Azure Internet Analyzer の FAQ](internet-analyzer-faq.md) に記載されている「**プレビューに参加するにはどうすればよいですか?** 」の指示に従って、Internet Analyzer プレビュー版へのアクセスを取得します。
2. [Azure portal](https://preview.portal.azure.com) 内のホーム ページから **[+ リソースの作成]** をクリックします。 Internet Analyzer は現在、Azure portal のプレビュー バージョンからのみ使用できます。
3. **[新規]** ページの *[マーケットプレースを検索]* フィールドで "Internet Analyzer" を検索します。
4. **[Internet Analyzer (プレビュー)]** をクリックします。 公開元が *Microsoft* で、カテゴリが *ネットワーク* であることを確認してください。
5. **[Internet Analyzer (プレビュー)**] ページで **[作成]** をクリックし、**[Create an Internet Analyzer] (Internet Analyzer の作成)** ページを開きます。
6. Internet Analyzer リソースの以下の構成設定を指定します。

    * **[サブスクリプション]:** 新しい Internet Analyzer リソースをホストする Azure サブスクリプション。 "**_プレビューへのアクセスを要求するのに使用したのと同じサブスクリプション ID を使用します。_**"
    * **[リソース グループ]:** そこに新しい Internet Analyzer リソースが作成される Azure リソース グループ。 既存のリソース グループがない場合は、新しいものを作成できます。
    * **[名前]:** 新しい Internet Analyzer リソース プロファイルの名前。
    * **[リージョン]:** :リソースが作成される Azure パブリック リージョンです。 プレビュー期間中に使用できるのは *米国西部 2* のみです。

7. プロファイル設定の指定を終えたら **[レビュー + 作成]** をクリックします。

## <a name="configuration"></a>構成

基本手順を完了することは、テストを構成し、JavaScript クライアントを埋め込むための前提条件です。 プロファイルを作成したら、**[設定]、[構成]** と移動し、最初のテストを構成します。

1. **[テスト名]** ボックスでテストの名前を指定します。
2. **[説明]** フィールドにテストの説明を追加します。
3. **[Configure Endpoint] (エンドポイントの構成)** をクリックします。右側からタブが表示されます。 構成するエンドポイントの種類 (1 つの Azure リージョン、複数の Azure リージョン、またはカスタム エンドポイント) を選択します。

    >
    ***事前に構成済みのエンドポイント: 単一および複数の Azure リージョンの組み合わせ***
    * [事前に構成済みの Azure エンドポイントのリスト](internet-analyzer-faq.md)から、1 つのリージョンまたはリージョンのセットを選択します。
    * 次に、評価するアプリケーションまたはコンテンツ配信アーキテクチャの種類を選択します。
        * 1 つの Azure リージョン: サイト アクセラレーション ([Azure Front Door](https://azure.microsoft.com/services/frontdoor/))、静的コンテンツ キャッシング ([Microsoft 向け Azure CDN](https://azure.microsoft.com/services/cdn/))、またはなし。
        * 複数の Azure リージョン: サイト アクセラレーション ([Azure Front Door](https://azure.microsoft.com/services/frontdoor/))、DNS ステアリング ([Azure Traffic Manager](https://azure.microsoft.com/services/traffic-manager/))  

    ***カスタム エンドポイント***
    * [[Create Custom Endpoint] (カスタムエンドポイントの作成)](internet-analyzer-custom-endpoint.md) ページの指示に従います。
    * 1 ピクセル画像の HTTPS URL の、ポータル内での場所を貼り付けます。
    >

4. **[追加]** をクリックしてテストにエンドポイントを追加します。
5. 手順 1 ～ 4 を繰り返して 2 つ目のエンドポイントを構成します。 エンドポイント B は常に、エンドポイント A を基準として測定されます。エンドポイント構成時には、どちらのエンドポイントをテスト コントロールにするかを検討してください。
6. **[保存]** ボタンをクリックしてテストを保存します。 テストを保存すると、そのテストのエンドポイントは編集できなくなります。
7. 開始するテストを選択し、**[テストの開始]** をクリックします。 これにより、テストの **_[状態]_ *_ が _* _[実行中]_** に変わります。 テストはいつでも開始できますが、テストで測定値の収集を開始するには、JavaScript クライアントが埋め込まれている必要があります。
8. 任意の時点でより多くのテストを追加します。 独自の JavaScript クライアントは、1 つのテストが作成されるまで生成されないことに注意してください。

## <a name="embed-client"></a>クライアントを埋め込む

どのテストを開始する場合でも、Web アプリケーションに JavaScript クライアントが埋め込まれている必要があります。 少なくとも 1 つのテストを構成した後、**[確認と作成]** をクリックし、**[設定]、[構成]** と移動して、JavaScript クライアントをコピーします。 具体的な手順は、「[Internet Analyzer クライアントの埋め込み](internet-analyzer-embed-client.md)」ページに記載されています。  

## <a name="next-steps"></a>次のステップ

* [Internet Analyzer の FAQ](internet-analyzer-faq.md) を読む
* [Internet Analyzer クライアント](internet-analyzer-embed-client.md)の埋め込みと[カスタム エンドポイント](internet-analyzer-custom-endpoint.md)の作成の詳細を確認する。
