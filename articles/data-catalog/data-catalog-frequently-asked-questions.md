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
ms.date: 01/23/2017
ms.author: maroche
translationtype: Human Translation
ms.sourcegitcommit: 219dcbfdca145bedb570eb9ef747ee00cc0342eb
ms.openlocfilehash: 76cc9c79851cc040e6a36c5499a77c0d95948154


---
# <a name="azure-data-catalog-frequently-asked-questions"></a>Azure Data Catalog のよく寄せられる質問
この記事では、 **Microsoft Azure Data Catalog** サービスに関連する、よく寄せられる質問の回答を示します。

## <a name="q-what-is-azure-data-catalog"></a>Q: Azure Data Catalog とは何ですか。
A: Microsoft Azure Data Catalog は、Microsoft Azure クラウドでホストされる完全に管理されたサービスで、エンタープライズ データ ソースの登録のシステムと検出のシステムとして機能します。 Azure Data Catalog は、アナリストからデータ サイエンティスト、開発者まで、すべてのユーザーによるデータ ソースの登録、検出、理解、および使用を可能にする機能を備えています。

## <a name="q-what-customer-challenges-does-azure-data-catalog-solve"></a>Q: Azure Data Catalog はどのようなお客様の課題を解決しますか。
Azure Data Catalog は、ユーザーのエンタープライズ データ ソースの検出と理解を可能にすることで、データ ソースの検出と "ダーク データ" の課題に対処します。

## <a name="q-who-are-the-target-audiences-for-azure-data-catalog"></a>Q: Azure Data Catalog の対象ユーザーはだれですか。
Azure Data Catalog は、次の技術者と技術者以外のユーザー向けの機能を備えています。

* データ開発者、BI、および分析のプロフェッショナル: 他のユーザーが使用するデータおよび分析用コンテンツの生成を担当するユーザー
* データ スチュワード: データの意味、意図された使用方法、目的などのデータに関する知識を持つユーザー
* データ コンシューマー: 選択したツールを使用して、ジョブの実行に必要なデータを簡単に検出し、理解し、接続できる必要があるユーザー
* 中央 IT: ビジネス ユーザー向けに、数百のデータ ソースを探索可能にする必要があり、データの使用方法と使用者を監視し続ける必要があるユーザー

## <a name="q-what-is-the-azure-data-catalog-region-availability"></a>Q: Azure Data Catalog の提供先のリージョンはどこですか。
Azure Data Catalog サービスは、現在、次のデータ センターで使用できます。

* 米国西部
* 米国東部
* 西ヨーロッパ
* 北ヨーロッパ
* オーストラリア東部
* 東南アジア

## <a name="q-what-are-the-limits-on-the-number-of-data-assets-in-azure-data-catalog"></a>Q: Azure Data Catalog のデータ資産の数の制限はどのくらいですか。
Free エディションの Azure Data Catalog は、5,000 の登録済みデータ資産に制限されます。

Standard Edition の Azure Data Catalog は、最大 100,000 の登録済みのデータ資産をサポートします。

## <a name="q-what-are-the-supported-data-source-and-asset-types"></a>Q: サポートされているデータ ソースと資産の種類は何ですか。
現在サポートされているデータ ソースの一覧については、「 [Azure Data Catalog でサポートされるデータ ソース](data-catalog-dsr.md) 」をご覧ください。

## <a name="q-how-do-i-request-support-for-another-data-source"></a>Q: 別のデータ ソースのサポートを要求するにはどうすればよいですか。
機能要求やその他のフィードバックは [Azure Data Catalog フォーラム](http://go.microsoft.com/fwlink/?LinkID=616424&clcid=0x409)で送信することができます。

## <a name="q-how-do-i-get-started-with-azure-data-catalog"></a>Q: Azure Data Catalog はどのように使用を開始するのですか。
使用を開始する最適な場所は、「 [Data Catalog の概要](data-catalog-get-started.md)」の手順に従うことです。 この記事は、サービスの機能を網羅した概要です。

## <a name="q-how-do-i-register-my-data"></a>Q: データの登録方法を教えてください。
データを Azure Data Catalog に登録するには、Azure Data Catalog ポータルの [発行] 領域から Azure Data Catalog 登録ツールを起動します。 Azure Data Catalog 発行アプリケーションで、Azure Data Catalog ポータルへのアクセスに使用した同じ資格情報を使用して、ログインし、登録するデータ ソースと特定の資産を選択します。

## <a name="q-what-properties-are-extracted-for-data-assets-that-are-registered"></a>Q: 登録されているデータ資産に対し、どのようなプロパティが抽出されますか。
具体的なプロパティはデータ ソースごとに異なりますが、一般に、Azure Data Catalog 発行サービスでは次の情報が抽出されます。

* 資産名
* 資産の種類
* 資産の説明
* 属性/列名
* 属性/列データ型
* 属性/列の説明

> [!IMPORTANT]
> データ資産を Azure Data Catalog に登録しても、クラウドへのデータの移動もコピーも行われません。 データ ソースから資産を登録すると、資産のメタデータが Azure にコピーされますが、データは、既存のデータ ソースの場所に残ります。 このルールの唯一の例外は、ユーザーが資産の登録時に、プレビュー レコードまたはデータ プロファイルをアップロードするように選択した場合です。 プレビューを含む場合、最大 20 のレコードが各資産からコピーされ、Azure Data Catalog にスナップショットとして格納されます。 データ プロファイルを含む場合、集計情報 (テーブルのサイズ、列あたりの null 値の割合、および列の最小、最大、および平均値など) が計算され、カタログ内に格納されたメタデータに含められます。
>
>

<br/>

> [!NOTE]
> ファーストクラス **Description** プロパティを持つ SQL Server Analysis Services などのデータ ソースに対し、Azure Data Catalog 発行アプリケーションはそのプロパティ値を抽出します。 ファーストクラス **Description** プロパティがない SQL Server リレーショナル データベースに対しては、Azure Data Catalog 発行アプリケーションはオブジェクトと列の ms_description 拡張プロパティから値を抽出します。 詳細については、TechNet 「 [データベース オブジェクトでの拡張プロパティの使用](https://technet.microsoft.com/library/ms190243%28v=sql.105%29.aspx)」を参照してください。
>
>

## <a name="q-how-long-should-it-take-for-newly-registered-assets-to-appear-in-azure-data-catalog"></a>Q: 新しく登録された資産が Azure Data Catalog に表示されるまでにどのくらいの時間がかかりますか。
Azure Data Catalog に資産を登録した後に、それらが Azure Data Catalog ポータルに表示されるまで、5 ～ 10 秒の時間がかかる可能性があります。

## <a name="q-how-do-i-annotate-and-enrich-the-metadata-for-my-registered-data-assets"></a>Q: 登録したデータ資産のメタデータに注釈を付け、強化するには、どうするのですか。
登録した資産にメタデータを指定する最も簡単な方法は、Azure Data Catalog ポータルで資産を選択し、選択したオブジェクトのプロパティ ペインまたはスキーマ ペインで、メタデータの値を入力することです。

エキスパートやタグなどのいくつかのメタデータは、登録プロセス中に指定することもできます。 Azure Data Catalog 発行サービスに指定された値は、その時点で登録されているすべての資産に適用されます。 ポータルに最近登録されたオブジェクトの追加の注釈を表示するには、Azure Data Catalog 発行アプリケーションの最終画面の **[ポータルの表示]** ボタンをクリックします。

## <a name="q-how-do-i-delete-my-registered-data-objects"></a>Q: 登録済みのデータ オブジェクトを削除するにはどうすればよいですか。
Azure Data Catalog からオブジェクトを削除するには、ポータルでオブジェクトを選択し、 **[削除]** ボタンをクリックします。 これにより、オブジェクトのメタデータが Azure Data Catalog から削除されますが、実際の基になるデータ ソースには影響しません。

## <a name="q-what-is-an-expert"></a>Q: エキスパートとは何ですか。
エキスパートは、データ オブジェクトに関して、情報に基づいた、ある観点を持つ個人です。 オブジェクトには、複数のエキスパートを指定できます。 エキスパートは、オブジェクトの "所有者" である必要はありません。エキスパートは単純に、データをどのように使用でき、使用すべきかを知っている者です。

## <a name="q-how-do-i-share-information-with-the-azure-data-catalog-team-if-i-encounter-problems"></a>Q: 問題が発生した場合に、Azure Data Catalog チームに情報を伝えるにはどうすればよいですか。
Azure Data Catalog フォーラムを使用して、問題の報告、情報の共有、質問の投稿をしてください。 フォーラムは http://go.microsoft.com/fwlink/?LinkID=616424&clcid=0x409 にあります。

## <a name="q-does-azure-data-catalog-work-with-this-other-data-source-im-interested-in"></a>Q: Azure Data Catalog は興味があるこの他のデータ ソースを処理しますか。
Azure Data Catalog へのデータ ソースの追加には積極的に取り組んでいるところです。 サポートされることを期待するデータ ソースがある場合は、 [Azure Data Catalog フォーラム](http://go.microsoft.com/fwlink/?LinkID=616424&clcid=0x409)で、それを提案 (またはそれが既に提案されている場合は、その指示を表明) してください。

## <a name="q-how-is-azure-data-catalog-related-to-the-data-catalog-in-power-bi-for-office-365"></a>Q: Azure Data Catalog は Power BI for Office 365 のデータ カタログとどのように関連しますか。
Azure Data Catalog は Data Catalog の進化と考えることができます。 Azure Data Catalog は、データ ソースの発行と検出のための同様の機能を提供しますが、より広範なシナリオに焦点を合わせ、Office 365 に依存しません。 Azure Data Catalog の一般提供開始後すぐに、2 つのカタログは 1 つのサービスに結合されます。

## <a name="q-what-permissions-does-a-user-need-to-register-assets-with-azure-data-catalog"></a>Q: ユーザーは Azure Data Catalog に資産を登録するために、どのようなアクセス許可が必要ですか。
Azure Data Catalog 登録ツールを実行するユーザーは、データ ソースに対し、そのソースからメタデータを読み取ることができるアクセス許可が必要です。 ユーザーがプレビューも含めるように選択した場合、登録されるオブジェクトからのデータの読み取りができるアクセス許可も必要です。

## <a name="q-will-azure-data-catalog-be-made-available-for-on-premises-deployment-as-well"></a>Q: Azure Data Catalog は、オンプレミスのデプロイメントにも使用できますか。
Azure Data Catalog は、クラウドとオンプレミスの両方のデータ ソースを操作できるクラウド サービスであり、ハイブリッド データ ソース検出ソリューションを提供します。 現在オンプレミスで実行する Azure Data Catalog のバージョンの計画はありません。

## <a name="q-can-we-extract-more--richer-metadata-from-the-data-sources-we-register"></a>Q: 登録するデータ ソースから抽出するメタデータを追加したり、強化したりできますか。
Azure Data Catalog の機能の拡張に積極的に取り組んでいるところです。 登録時にデータ ソースから抽出したいと考える追加のメタデータがある場合は、 [Azure Data Catalog フォーラム](http://go.microsoft.com/fwlink/?LinkID=616424&clcid=0x409)で、それを提案して (または既に提案されている場合は、賛成票を投じて) ください。 将来、サード パーティが、拡張 API によって、新しいデータ ソースの種類を追加できるようにします。

## <a name="q-how-do-i-restrict-the-visibility-of-registered-data-assets-so-that-only-certain-people-can-discover-them"></a>Q: 特定のユーザーだけが検出できるように、登録されているデータ資産の可視性を制限するにはどうすればいいですか。
A: Azure Data Catalog で、データ資産を選択し、[所有権の取得] ボタンをクリックします。 Azure Data Catalog のデータ資産の所有者は、可視性設定を変更して、すべてのカタログ ユーザーが、所有している資産を検出できるようにするか、または特定のユーザーに可視性を制限することができます。

## <a name="q-how-do-i-update-the-registration-for-a-data-asset-to-that-changes-in-the-data-source-are-reflected-in-the-catalog"></a>Q: データ ソースの変更がカタログに反映されるように、データ資産の登録を更新するにはどうすればいいですか。
A: カタログに既に登録されているデータ資産のメタデータを更新するには、その資産を含むデータ ソースを再登録するだけです。 テーブルまたはビューの列の追加や削除などのデータ ソースのすべての変更は、カタログで更新されますが、ユーザーによって提供されたすべての注釈は維持されます。

## <a name="q-my-question-isnt-answered-here--what-should-i-do"></a>Q: ここに質問の答えがありません。どうすればいいですか。
[Azure Data Catalog フォーラム](http://go.microsoft.com/fwlink/?LinkID=616424&clcid=0x409)にアクセスしてください。 そこで寄せられている質問に、ここでの方法が見つかります。



<!--HONumber=Nov16_HO3-->


