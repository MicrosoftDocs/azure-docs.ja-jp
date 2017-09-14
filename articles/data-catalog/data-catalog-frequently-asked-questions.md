---
title: "Azure Data Catalog のよく寄せられる質問 | Microsoft Docs"
description: "データ ソースの検出、注釈、管理など、Azure Data Catalog についてのよく寄せられる質問です。"
services: data-catalog
documentationcenter: 
author: steelanddata
manager: NA
editor: 
tags: 
ms.assetid: 5c7e209a-458c-4bb4-96bb-7ed178f9528a
ms.service: data-catalog
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: data-catalog
ms.date: 08/15/2017
ms.author: maroche
ms.translationtype: Human Translation
ms.sourcegitcommit: 532ff423ff53567b6ce40c0ea7ec09a689cee1e7
ms.openlocfilehash: ea6b6651a32b9f259186991d9609301fd4dfa131
ms.contentlocale: ja-jp
ms.lasthandoff: 06/06/2017

---
# <a name="azure-data-catalog-frequently-asked-questions"></a>Azure Data Catalog のよく寄せられる質問
この記事では、Azure Data Catalog サービスに関連する、よく寄せられる質問の回答を示します。

## <a name="what-is-azure-data-catalog"></a>Azure Data Catalog とは何ですか
Data Catalog は、Microsoft Azure でホストされる完全に管理されたサービスであり、エンタープライズ データ ソースの登録および検出のシステムとして機能します。 Data Catalog を利用することで、アナリストやデータ サイエンティスト、開発者など、さまざまなユーザーがデータ ソースを登録、検出、理解、消費することができます。

## <a name="what-customer-challenges-does-it-solve"></a>どのようなお客様の課題を解決しますか?
Data Catalog は、ユーザーがエンタープライズ データ ソースの検出と理解ができるように、データ ソースの検出と "ダーク データ" の課題に対処します。

## <a name="what-are-its-target-audiences"></a>対象ユーザーはだれですか?
Data Catalog は、次のような技術者と技術者以外のユーザーを対象にしています。

* データ開発者、BI、および分析のプロフェッショナル: 他のユーザーが使用するデータおよび分析用コンテンツの生成を担当するユーザー。
* データ スチュワード: データの意味、意図された使用方法などのデータに関する知識を持つユーザー。
* データ コンシューマー: 選択したツールを使用して、ジョブの実行に必要なデータを簡単に検出し、理解し、接続できる必要があるユーザー。
* 中央 IT: ビジネス ユーザー向けに、数百のデータ ソースを探索可能にする必要があり、データの使用方法と使用者を監視し続ける必要があるユーザー。

## <a name="what-is-its-availability-by-region"></a>地域ごとの可能性はどうなっていますか?
Data Catalog サービスは、現在、次のデータ センターで使用できます。

* 米国西部
* 米国東部
* 西ヨーロッパ
* 北ヨーロッパ
* オーストラリア東部
* 東南アジア

## <a name="what-are-its-limits-on-the-number-of-data-assets"></a>データ資産の数の制限はどのくらいですか?
Free エディションの Data Catalog は、5,000 の登録済みデータ資産に制限されます。

Standard Edition の Data Catalog は、最大 100,000 の登録済みのデータ資産をサポートします。

## <a name="what-are-its-supported-data-source-and-asset-types"></a>サポートされているデータ ソースと資産の種類は何ですか?
現在サポートされているデータ ソースの一覧については、「[Azure Data Catalog でサポートされるデータ ソース](data-catalog-dsr.md)」をご覧ください。

## <a name="how-do-i-request-support-for-another-data-source"></a>別のデータ ソースのサポートを要求するにはどうすればよいですか?
機能要求やその他のフィードバックを送信するには、[Azure Data Catalog フォーラム](http://go.microsoft.com/fwlink/?LinkID=616424&clcid=0x409)にアクセスします。

## <a name="how-do-i-get-started-with-data-catalog"></a>Data Catalog の使用を開始するにはどうすればよいですか?
使用を開始する最適な方法は、「[Data Catalog の概要](data-catalog-get-started.md)」の手順に従うことです。 この記事は、サービスの機能を網羅した概要です。

## <a name="how-do-i-register-my-data"></a>データの登録方法を教えてください
Data Catalog でデータを登録するには、次の手順を実行します。
1. Azure Data Catalog ポータルの [**発行**] 領域で、Azure Data Catalog 登録ツールを起動します。 
2. Data Catalog 発行アプリケーションで、Data Catalog ポータルへのアクセスに使用するのと同じ資格情報でサインインします。
3. データ ソースと登録する特定の資産を選択します。

## <a name="what-properties-does-it-extract-for-data-assets-that-are-registered"></a>登録されているデータ資産に対し、どのようなプロパティが抽出されますか?
具体的なプロパティはデータ ソースごとに異なりますが、一般に、Data Catalog 発行サービスでは次の情報が抽出されます。

* 資産名
* 資産の種類
* 資産の説明
* 属性/列名
* 属性/列データ型
* 属性/列の説明

> [!IMPORTANT]
> データ資産を Data Catalog に登録しても、クラウドへのデータの移動もコピーも行われません。 データ ソースから資産を登録すると、資産のメタデータが Azure にコピーされますが、データは、既存のデータ ソースの場所に残ります。 このルールの例外は、資産の登録時に、プレビュー レコードまたはデータ プロファイルをアップロードするように選択した場合です。 プレビューを含めると、最大 20 のレコードが各資産からコピーされ、Data Catalog にスナップショットとして格納されます。 データ プロファイルを含めると、集計情報が計算され、カタログに格納されているメタデータに含まれます。 集計情報には、テーブルのサイズ、列あたりの null 値の割合、または列の最小、最大、および平均値を含めることができます。 
>
>

> [!NOTE]
> ファーストクラス **Description** プロパティを持つ SQL Server Analysis Services などのデータ ソースに対し、Data Catalog 発行アプリケーションはそのプロパティ値を抽出します。 ファーストクラス **Description** プロパティがない SQL Server リレーショナル データベースに対しては、Data Catalog 発行アプリケーションはオブジェクトと列の **ms_description** 拡張プロパティから値を抽出します。 詳細については、「[データベース オブジェクトでの拡張プロパティの使用](https://technet.microsoft.com/library/ms190243%28v=sql.105%29.aspx)」を参照してください。
>
>

## <a name="how-long-should-it-take-for-newly-registered-assets-to-appear-in-the-catalog"></a>新しく登録された資産がカタログに表示されるまでにどのくらいの時間がかかりますか?
Data Catalog に資産を登録した後に、それらが Data Catalog ポータルに表示されるまで、5 ～ 10 秒の時間がかかる可能性があります。

## <a name="how-do-i-annotate-and-enrich-the-metadata-for-my-registered-data-assets"></a>登録したデータ資産のメタデータに注釈を付け、強化するには、どうするのですか?
登録した資産にメタデータを指定する最も簡単な方法は、Data Catalog ポータルで資産を選択し、選択したオブジェクトのプロパティ ウィンドウまたはスキーマ ウィンドウで、値を入力することです。

エキスパートやタグなどのいくつかのメタデータは、登録プロセス中に指定することもできます。 Data Catalog 発行サービスで指定した値は、その時点で登録されているすべての資産に適用されます。 ポータルに最近登録されたオブジェクトの追加の注釈を表示するには、Data Catalog 発行アプリケーションの最終画面の [**ポータルの表示**] ボタンをクリックします。

## <a name="how-do-i-delete-my-registered-data-objects"></a>登録済みのデータ オブジェクトを削除するにはどうすればよいですか?
Data Catalog からオブジェクトを削除するには、ポータルでオブジェクトを選択し、[**削除**] ボタンをクリックします。 オブジェクトを削除すると、そのメタデータが Data Catalog から削除されますが、基になるデータ ソースには影響しません。

## <a name="what-is-an-expert"></a>エキスパートとは何ですか?
エキスパートは、データ オブジェクトに関して、情報に基づいた、ある観点を持つ個人です。 オブジェクトには、複数のエキスパートを指定できます。 エキスパートは、オブジェクトの "所有者" である必要はありませんが、単純に、データをどのように使用でき、使用すべきかを知っている者です。

## <a name="how-do-i-share-information-with-the-data-catalog-team-if-i-encounter-problems"></a>問題が発生した場合に、Data Catalog チームに情報を伝えるにはどうすればよいですか?
問題の報告、情報の共有、質問の投稿を行うには、[Azure Data Catalog フォーラム](http://go.microsoft.com/fwlink/?LinkID=616424&clcid=0x409)にアクセスします。

## <a name="does-the-catalog-work-with-another-data-source-that-im-interested-in"></a>カタログは興味がある別のデータ ソースを処理しますか?
Data Catalog へのデータ ソースの追加には積極的に取り組んでいるところです。 サポートされている具体的なデータ ソースを確認する場合は、[Azure Data Catalog フォーラム](http://go.microsoft.com/fwlink/?LinkID=616424&clcid=0x409)に移動して、それを提案 (またはそれが既に提案されている場合は、その指示を表明) してください。

## <a name="how-is-azure-data-catalog-related-to-the-data-catalog-in-power-bi-for-office-365"></a>Azure Data Catalog は Power BI for Office 365 のデータ カタログとどのように関連しますか?
Azure Data Catalog は、Power BI での Data Catalog の進化と考えることができます。 2017 年春以降、Excel 2016 および Power Query for Excel でのクエリの共有と検出を有効にするために Azure Data Catalog が使用されています。 Excel での Data Catalog の機能は、Power BI Pro ライセンスを持つユーザーが使用できます。

## <a name="what-permissions-do-i-need-to-register-assets-with-data-catalog"></a>Data Catalog に資産を登録するには、どのようなアクセス許可が必要ですか?
Data Catalog 登録ツールを実行するには、データ ソースに対し、そのソースからメタデータを読み取ることができるアクセス許可が必要です。 プレビューも含めるには、登録されるオブジェクトからのデータの読み取りができるアクセス許可が必要です。

## <a name="will-data-catalog-be-made-available-for-on-premises-deployment-as-well"></a>Data Catalog は、オンプレミスのデプロイメントにも使用できますか?
Data Catalog は、クラウドとオンプレミスの両方のデータ ソースを操作できるクラウド サービスであり、ハイブリッド データ ソース検出ソリューションを提供します。 現在オンプレミスで実行する Data Catalog のバージョンの計画はありません。

## <a name="can-i-extract-more-or-richer-metadata-from-the-data-sources-i-register"></a>登録するデータ ソースから抽出するメタデータを追加したり、強化したりできますか?
Data Catalog の機能の拡張に積極的に取り組んでいるところです。 登録時にデータ ソースからさらにメタデータを抽出する場合は、[Azure Data Catalog フォーラム](http://go.microsoft.com/fwlink/?LinkID=616424&clcid=0x409)で、それを提案して (または既に提案されている場合は、賛成票を投じて) ください。 将来、サード パーティが、拡張 API によって、新しいデータ ソースの種類を追加できるようにします。

## <a name="how-do-i-restrict-the-visibility-of-registered-data-assets-so-that-only-certain-people-can-discover-them"></a>特定のユーザーだけが検出できるように、登録されているデータ資産の可視性を制限するにはどうすればいいですか?
Data Catalog で、データ資産を選択し、[**所有権の取得**] ボタンをクリックします。 Data Catalog のデータ資産の所有者は、可視性設定を変更して、すべてのユーザーが、所有している資産を検出できるようにするか、または特定のユーザーに可視性を制限することができます。

## <a name="how-do-i-update-the-registration-for-a-data-asset-so-that-changes-in-the-data-source-are-reflected-in-the-catalog"></a>データ ソースの変更がカタログに反映されるように、データ資産の登録を更新するにはどうすればいいですか?
カタログに既に登録されているデータ資産のメタデータを更新するには、その資産を含むデータ ソースを再登録するだけです。 テーブルまたはビューの列の追加や削除などのデータ ソースのすべての変更は、カタログで更新されますが、ユーザーによって提供されたすべての注釈は維持されます。

## <a name="my-question-isnt-answered-here-where-can-i-go-for-answers"></a>ここに質問の答えがありません。 どうすればいいですか?
[Azure Data Catalog フォーム](http://go.microsoft.com/fwlink/?LinkID=616424&clcid=0x409)にアクセスしてください。 そこで寄せられている質問に、ここでの方法が見つかります。

