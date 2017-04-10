---
title: "Azure Search とは何か | Microsoft Docs"
description: "Azure Search は完全に管理されたホスト型クラウド検索サービスです。 この機能の概要で詳しく説明します。"
services: search
manager: jhubbard
author: ashmaka
documentationcenter: 
ms.assetid: 50bed849-b716-4cc9-bbbc-b5b34e2c6153
ms.service: search
ms.devlang: NA
ms.workload: search
ms.topic: article
ms.tgt_pltfrm: na
ms.date: 01/12/2017
ms.author: ashmaka
experimental: true
experiment_id: heidist-20170221
translationtype: Human Translation
ms.sourcegitcommit: 292c9150822363aba3336b1efce579dc5362cb14
ms.openlocfilehash: 99b3babee9b252b2d741515391295d8b1dc5c747
ms.lasthandoff: 01/14/2017

---
# <a name="what-is-azure-search"></a>Azure Search とは
Azure Search は、サーバーとインフラストラクチャの管理を Microsoft に委任するクラウドの Search-as-a-service (サービスとしての検索) ソリューションです。データを取り込んだら、Web サイトやモバイル アプリケーションに検索機能を追加して、すぐに利用を開始できます。 簡単な [REST API](https://msdn.microsoft.com/library/azure/dn798935.aspx) または [.NET SDK](search-howto-dotnet-sdk.md) を使用してアプリケーションに強力な検索エクスペリエンスを簡単に追加できるので、検索インフラストラクチャを管理する必要も、検索のエキスパートになる必要もありません。

## <a name="give-your-users-a-powerful-search-experience"></a>ユーザーに強力な検索エクスペリエンスを提供する
**強力なクエリ** を [簡単なクエリ構文](https://msdn.microsoft.com/library/azure/dn798920.aspx)で作成できます。 さらに、 [Lucene クエリ構文](https://msdn.microsoft.com/library/azure/mt589323.aspx) では、あいまい検索、近接検索、項目ブースト、および正規表現を有効にすることができます。 さらに、Azure Search は、アプリケーションで発音照合と正規表現を使用して、複雑な検索クエリを処理できるカスタム字句アナライザーもサポートしています。

**言語サポート** が [含まれています](https://msdn.microsoft.com/library/azure/dn879793.aspx)。 Lucene アナライザーと Microsoft アナライザー (Office および Bing での長年にわたる自然言語処理により改良されています) の両方を使用することで、Azure Search はアプリケーションの検索ボックス内のテキストを分析し、動詞の時制や名詞の性、不規則な複数形の名詞 (例: 'mouse' と 'mice')、二重複合語、(スペースを使用しない言語の) 改行などをインテリジェントに処理できます。

**検索候補** を有効にすることができます。 [インデックス内の実際のドキュメントが提案されます。](https://msdn.microsoft.com/library/azure/dn798936.aspx) 

**ヒットの強調表示** [により](https://msdn.microsoft.com/library/azure/dn798927.aspx) 、ユーザーに各結果内のクエリとの一致を含むテキストのスニペットが表示されます。 強調表示されるスニペットを返すフィールドを選択できます。

**ファセット ナビゲーション** を検索結果ページに簡単に追加できます。 Azure Search では、[1 つのクエリ パラメーター](https://msdn.microsoft.com/library/azure/dn798927.aspx)を使用するだけで、アプリケーションの UI にファセット検索エクスペリエンスを構築するために必要なすべての情報が返されるので、ユーザーはドリル ダウンして、検索結果をフィルター処理 (提供価格やブランドによってカタログ項目をフィルター処理するなど) できます。

**地理空間** サポートにより、地理的な位置をインテリジェントに処理、フィルター、および表示できます。 Azure Search により、ユーザーは指定した場所との検索結果の近接度に基づいて、または特定のリージョンに基づいて、データを探索できます。 「 [チャンネル 9: Azure Search と地理空間データ](https://channel9.msdn.com/Shows/Data-Exposed/Azure-Search-and-Geospatial-Data)」という動画でそのしくみが説明されています。

**フィルター** を使用することで、ファセット ナビゲーションをアプリケーションの UI に簡単に組み込み、クエリ形成を拡張し、ユーザーまたは開発者が指定した条件に基づいてフィルター処理することができます。 強力なフィルターを作成するには、 [OData 構文](https://msdn.microsoft.com/library/azure/dn798921.aspx)を使用します。

## <a name="empower-your-developers-with-an-easy-to-use-service"></a>使いやすいサービスにより開発者の生産性を高める
**高可用性** により、極めて信頼性の高い検索サービス エクスペリエンスを確保します。 適切に拡張された場合に、 [Azure Search は 99.9% の SLA を実現](https://azure.microsoft.com/support/legal/sla/search/v1_0/)します。

**完全に管理** された Azure Search ではインフラストラクチャ管理をまったく必要としません。 サービスは&2; 次元での拡張によって、ニーズに合わせて簡単にカスタマイズでき、より大きなドキュメント ストレージ、より高いクエリ負荷、またはその両方を処理できます。

Azure Search では、[インデクサー](https://msdn.microsoft.com/library/azure/dn946891.aspx)を使用した**データ統合**により、Azure SQL Database、Azure DocumentDB、または [Azure Blob Storage](search-howto-indexing-azure-blob-storage.md) を自動的にクロールし、検索インデックスの内容とプライマリ データ ストアを同期できます。

**ドキュメント クラッキング** (現時点ではプレビュー) を使用して、Microsoft Office のほか PDF や HTML ドキュメントなどの [主要なファイル形式を読み取り、インデックスを作成する](search-howto-indexing-azure-blob-storage.md) ことができます。

**検索トラフィック分析** を [簡単に収集し、分析して](search-traffic-analytics.md) 、ユーザーが検索ボックスに入力した内容からインサイトを明らかにします。

**簡単なスコアリング** は Azure Search の主な利点です。 [スコアリング プロファイル](https://msdn.microsoft.com/library/azure/dn798928.aspx) を使用して、組織はドキュメント自体の値の関数として、関連性をモデル化できます。 たとえば、新しい製品や割り引き製品を検索結果の上位に表示することが望ましい場合があります。 あるいは、追跡記録し、個別に保存しておいた顧客の検索傾向に基づいてパーソナライズされたスコアリングのタグを利用し、スコアリング プロファイルを作成できます。

**並べ替え** は、インデックス スキーマを介して、複数のフィールドで利用でき、クエリ時に&1; つの検索パラメーターで切り替えることができます。

**ページング** とスロットルは、Azure Search が検索結果に適用する [微調整された制御によって簡単に行うことができます](search-pagination-page-layout.md) 。  

**検索エクスプローラー** を使用して、アカウントの Azure ポータルから、すべてのインデックスに対してクエリを発行できるため、簡単にクエリをテストし、スコアリング プロファイルを改善できます。

## <a name="how-it-works"></a>動作のしくみ
### <a name="1-provision-service"></a>1.サービスのプロビジョニング
[Azure Portal](https://portal.azure.com/)または [Azure Resource Management API](https://msdn.microsoft.com/library/azure/dn832684.aspx) を使用して、Azure Search サービスを高速化できます。

検索サービスの構成方法に応じて、他の Azure Search サブスクライバーとサービスを共有する Free レベル、またはリソースを独自のサービス専用にする [価格レベル](https://azure.microsoft.com/pricing/details/search/) を利用します。 サービスをプロビジョニングするときに、サービスをホストするデータ センターのリージョンも選択します。

選択したサービスのレベルに応じて、2 つの次元でサービスを拡張できます。1) クエリの大きな負荷を処理するために、レプリカを追加して容量を増やし、2) ドキュメントの増加に備えて、パーティションを追加して記憶域を追加します。 ドキュメントの記憶域とクエリのスループットを別々に処理することによって、特定のニーズに応じて検索サービスをカスタマイズできます。

### <a name="2-create-index"></a>手順&2;.インデックスの作成
コンテンツを Azure Search サービスにアップロードする前に、まず Azure Search インデックスを定義する必要があります。 インデックスは、データを保持し、検索クエリを受け付けることができるデータベース テーブルに似ています。 データベースのフィールドに似た、検索するドキュメントの構造にマップするインデックス スキーマを定義します。

このようなインデックスのスキーマは Azure Portal で作成するか、[.NET SDK](search-howto-dotnet-sdk.md) または [REST API](https://msdn.microsoft.com/library/azure/dn798941.aspx) を使用して、プログラムによって作成できます。 インデックスが定義されたら、データを Azure Search サービスにアップロードできます。データには順番にインデックスが付けられます。

### <a name="3-index-data"></a>3.データのインデックス付け
フィールドとインデックスの属性を定義した後は、コンテンツをインデックスにアップロードする準備ができます。 インデックスにデータをアップロードするには、プッシュ モデルまたはプル モデルを使用できます。

プル モデルは、オンデマンドまたは定期的な更新用に構成可能なインデクサーによって提供され (「 [インデクサー操作 (Azure Search Service REST API)](https://msdn.microsoft.com/library/azure/dn946891.aspx)」を参照)、Azure DocumentDB、Azure SQL Database、Azure Blob Storage、または Azure VM でホストされている SQL Server から簡単にデータとデータ変更を取り込むことができます。

プッシュ モデルは SDK または REST API によって提供され、更新したドキュメントをインデックスに送信するために使用されます。 JSON 形式を使用して、事実上すべてのデータセットからデータをプッシュできます。 データの読み込み方法については、「[ドキュメントの追加、更新、削除](https://msdn.microsoft.com/library/azure/dn798930.aspx)」または「[.NET SDK の使用方法](search-howto-dotnet-sdk.md)」を参照してください。

### <a name="4-search"></a>4.Search
Azure Search インデックスを入力したら、REST API または .NET SDK によって簡単な HTTP 要求を使用して、サービス エンドポイントに [検索クエリを発行](https://msdn.microsoft.com/library/azure/dn798927.aspx) できます。

## <a name="try-it-now-for-free"></a>今すぐお試しください (無料)
Azure サブスクライバーは [Free レベルでサービスをプロビジョニング](search-create-service-portal.md)できます。

サブスクライバーでない場合は、無料で [Azure アカウントを開く](https://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A261C142F)ことができます。Azure の有料サービスを試用できるクレジットが提供されます。このクレジットを使い切ってもアカウントは維持されるため、Websites など無料の Azure サービスをご利用になれます。 明示的に設定を変更して課金を求めない限り、クレジット カードに課金されることはありません。

または、[MSDN サブスクライバーの特典を有効にする](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/?WT.mc_id=A261C142F) こともできます - MSDN サブスクリプションにより、有料の Azure のサービスを使用できるクレジットが毎月与えられます。 



