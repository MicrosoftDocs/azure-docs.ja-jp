---
title: Azure で分析用に公開されているデータ セット
description: Azure の分析サービスとソリューションのプロトタイプ作成とテストに使用できる、公開されているデータ セットについて説明します。
services: sql-database
ms.service: sql-database
ms.subservice: development
ms.custom: sqldbrb=2
ms.devlang: ''
ms.topic: reference
author: stevestein
ms.author: sstein
ms.reviewer: ''
ms.date: 10/01/2018
ms.openlocfilehash: 2e836a175b45c6fca46f9c76760e1cbaef305a47
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "92792584"
---
# <a name="public-data-sets-for-testing-and-prototyping"></a>テストとプロトタイプ作成用の公開されているデータ セット
[!INCLUDE[appliesto-asf](includes/appliesto-asf.md)]

ストレージ サービスおよびソリューションと分析サービスおよびソリューションのプロトタイプ作成とテストに使用できるデータについては、この公開されているデータ セットの一覧を参照してください。

## <a name="us-government-and-agency-data"></a>米国政府と企業のデータ

| データ ソース | データについて | ファイルについて |
|---|---|---|
| [米国政府データ](https://catalog.data.gov/dataset) | 米国の農業、気候、コンシューマー、エコシステム、教育、エネルギー、金融、医療、地方自治体、製造業、海運、海洋、公安、科学調査を対象とする 250,000 件を越えるデータ セット。 | HTML、XML、CSV、JSON、Excel、などの各種形式のさまざまなサイズのファイル。 使用可能なデータ セットをファイル形式でフィルター処理できます。 |
| [米国国勢調査データ](https://www.census.gov/data.html) | 米国の人口統計データです。 | データ セットはさまざまな形式で提供されます。 |
| [NASA の地球科学データ](https://earthdata.nasa.gov/) | 農業、大気、生物圏、気候、雪氷圏、人的事象、水圏、地表、海洋、太陽と地球の相互作用などを対象とする 32,000 件を越えるデータ セット。 | データ セットはさまざまな形式で提供されます。 |
| [航空会社のフライト遅延データとその他の輸送データ](https://www.transtats.bts.gov/OT_Delay/OT_DelayCause1.asp) | "米国運輸省 (DOT) の運輸統計局 (BTS) では、大手航空会社が運航している国内フライトの定刻実績を追跡しています。 定時便、遅延便、欠航便、迂回便の数に関する概要情報が ... この Web サイトに掲載されている集計表に表示されます。" | ファイルは CSV 形式で提供されます。 |
| [交通事故による死亡者数 - Fatality Analysis Reporting System (FARS)](https://www.nhtsa.gov/FARS) | "FARS とは、NHTSA、議会、米国国民に、自動車による交通事故で負った致命傷に関する年次データを提供する全国的な調査です。" | "FARS 照会システムを使用して、死亡事故に関する独自のデータを作成し、オンラインで実行できます。 また、1975 年から現在までのすべての FARS データを FTP サイトからダウンロードすることもできます。" |
| [有毒化学物質データ - EPA Toxicity ForeCaster (ToxCast&trade;) データ](https://www.epa.gov/chemical-research/toxicity-forecaster-toxcasttm-data) | "何千種類もの化学物質に関する、EPA の最新の毒性データです。このデータは公開されており、高スループットで提供されます。 このデータは、EPA の ToxCast 調査を元に生成されました。" | データ セットは、スプレッドシート、R パッケージ、MySQL データベース ファイルなど、さまざまな形式で利用できます。 |
| [有毒化学物質データ - NIH Tox21 Data Challenge 2014](https://tripod.nih.gov/tox21) | "2014 年の Tox21 Data Challenge は、Tox21 (Toxicology in the 21st Century) の取り組みの中でテストを実施した、生物学的経路を破壊して中毒を引き起こす化学物質や化合物の毒性について、科学者の理解を深めることを目的としています。" | データ セットは、SMILES 形式および SDF 形式で利用できます。 データの内容は、"Tox21 で収集した約 10,000 種類の化合物 (Tox21 10K) に関する試金活動データと化学構造" です。 |
| [NCBI のバイオ テクノロジとゲノムのデータ](https://www.ncbi.nlm.nih.gov/guide/data-software/) | 遺伝子、ゲノム、たんぱく質を対象とする複数のデータ セットです。 | データ セットは、テキスト、XML、BLAST などの形式で提供されます。 BLAST アプリが使用できます。 |

## <a name="other-statistical-and-scientific-data"></a>その他の統計データと科学的データ

| データ ソース | データについて | ファイルについて |
|---|---|---|
| [ニューヨーク市のタクシーのデータ](http://www.nyc.gov/html/tlc/html/about/trip_record_data.shtml) | "タクシー移動の記録には、乗車と降車の日時、乗車と降車の場所、移動距離、料金明細、料金の種類、支払いの種類、運転手が報告する乗車人数が入力されたフィールドが含まれています。" | データ セットは月単位の CSV ファイルで提供されます。 |
| [Microsoft Research データ セット - "調査のためのデータ サイエンス"](https://www.microsoft.com/research/academic-program/data-science-microsoft-research/) | 人間とコンピューター間の対話、オーディオ/ビデオ、データ マイニング/情報の取得、地理空間/場所、自然言語処理、ロボット工学/コンピューター ビジョンを対象とする複数のデータ セットです。 | データ セットはさまざまな形式で提供されており、ダウンロード用に zip 形式に圧縮されています。 |
| [Open Science Data Cloud のデータ](https://www.opensciencedatacloud.org/projects/) | "Open Science Data Cloud は、テラバイトやペタバイト規模の科学データ セットを格納、共有、分析するためのリソースを科学界に提供します。"| データ セットはさまざまな形式で提供されます。 |
| [グローバルな気候データ - WorldClim](https://worldclim.org/) | "WorldClim は、約 1 km² の空間分解能を備えた、グローバルな気候レイヤー (グリッド表示された気候データ) のセットです。 これらのデータはマッピングと空間モデリングに使用できます。" | これらのファイルには地理空間データが含まれます。 詳細については、「[Data format (データ形式)](https://worldclim.org/formats1)」を参照してください。 |
| [人類社会に関するデータ - The GDELT Project](https://www.gdeltproject.org/data.html) | "The GDELT Project は、これまで作成された中で、最も大規模かつ包括的で高精度な、人類社会のオープンなデータベースです。" | 生データ ファイルは CSV 形式です。 |
| [Criteo の機械学習用の広告クリック予測データ](https://labs.criteo.com/2013/12/download-terabyte-click-logs/) | "これまで公式にリリースされた中で最大規模の ML データ セットです。" 詳細については、[Criteo の 1 TB クリック予測データ セット](/archive/blogs/machinelearning/now-available-on-azure-ml-criteos-1tb-click-prediction-dataset)に関するページを参照してください。 | |
| [Lemur Project の ClueWeb09 テキスト マイニング データ セット](https://www.lemurproject.org/clueweb09.php/) | "ClueWeb09 データ セットは、情報取得と関連する自然言語技術についての調査を支援するために作成されました。 2009 年 1 月～ 2 月に収集された、10 の言語による約 10 億の Web ページで構成されています。" | 「[Dataset Information (データ セット情報)](https://www.lemurproject.org/clueweb09/datasetInformation.php)」を参照してください。|

## <a name="online-service-data"></a>オンライン サービス データ

| データ ソース | データについて | ファイルについて |
|---|---|---|
| [GitHub Archive](https://www.githubarchive.org/) | "GitHub Archive は、公開されている GitHub の (イベントの) タイムラインを記録してアーカイブし、今後の分析で簡単にアクセスできるようにするためのプロジェクトです。" | JSON でエンコードされたイベント アーカイブを、Web クライアントから .gz (Gzip) 形式でダウンロードします。 |
| [GHTorrent プロジェクトの GitHub アクティビティ データ](http://ghtorrent.org/) | "GHTorrent プロジェクトは、GitHub REST API を通じて提供されるデータの、スケーラブルで照会可能なオフライン ミラーです。 GHTorrent は、GitHub の公開されているイベントのタイムラインを監視します。 イベントごとに、その内容と依存関係を徹底的に取得します。" | MySQL データ ダンプは CSV 形式です。 |
| [Stack Overflow データ ダンプ](https://archive.org/details/stackexchange) | "これは、Stack Overflow を含む Stack Exchange ネットワーク上でユーザーが投稿したすべてのコンテンツの匿名化されたダンプです。" | "Stack Overflow などの各サイトは、bzip2 圧縮を使用する 7-zip で圧縮した XML ファイルで構成された、個別のアーカイブとしてフォーマットされます。 各サイトのアーカイブには、Posts、Users、Votes、Comments、PostHistory、PostLinks が含まれます。" |