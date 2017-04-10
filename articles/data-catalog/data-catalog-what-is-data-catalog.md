---
title: "Azure Data Catalog の概要 | Microsoft Docs"
description: "この記事では、Microsoft Azure Data Catalog の概要 (機能や対処できる問題など) について説明します。 Data Catalog は、アナリストからデータ サイエンティスト、開発者まで、すべてのユーザーによるデータ ソースの登録、検出、理解、および使用を可能にする機能を備えています。"
services: data-catalog
documentationcenter: 
author: steelanddata
manager: NA
editor: 
tags: 
ms.assetid: cc733907-17ec-4153-9f0c-5b3754b2db19
ms.service: data-catalog
ms.devlang: NA
ms.topic: get-started-article
ms.tgt_pltfrm: NA
ms.workload: data-catalog
ms.date: 01/23/2017
ms.author: maroche
translationtype: Human Translation
ms.sourcegitcommit: cd3184059a0970bb7c9b59803cf37125abbbd879
ms.openlocfilehash: 19d4c3990c88f82f60cd253ffdcbd2005d00ad79
ms.lasthandoff: 01/24/2017


---
# <a name="what-is-azure-data-catalog"></a>Azure Data Catalog とは何ですか
Azure Data Catalog は、完全に管理されたクラウド サービスです。Azure Data Catalog を通じて、ユーザーは必要なデータ ソースを検出したり、見つけたデータ ソースについての知見を得たりすることができ、組織は既存の投資からさらに大きな価値を引き出すことができます。 アナリストからデータ サイエンティスト、開発者に至るまで、あらゆるユーザーが Data Catalog の機能を利用してデータ ソースを検出し、理解して利用することができます。 Data Catalog には、メタデータや注釈のクラウドソーシング モデルが組み込まれており、すべてのユーザーがその知見を持ち寄ってデータのコミュニティや文化を構築することができます。

## <a name="discovery-challenges-for-data-consumers"></a>データの利用者にとっての検出の難しさ
従来、エンタープライズ データ ソースの検出は、仲間の知識に基づいた組織的プロセスでした。 このプロセスは、情報資産の活用を考える企業にとって多数の課題があります。

* ユーザーは、別のプロセスの一環でデータ ソースを利用するまで、そのデータ ソースが存在することを知りません。データ ソースが一元的に登録されている場所はありません。
* ユーザーがデータ ソースの場所を知らない場合、クライアント アプリケーションを使用してデータに接続できません。データを利用するには、ユーザーが接続文字列またはパスを知っている必要があります。
* ユーザーがデータ ソースのドキュメントの場所を知らない場合、データの用途を理解できません。データ ソースとドキュメントが複数の場所に保存され、さまざまな状況で使用されます。
* 情報資産について不明な点がある場合、ユーザーはデータを担当するエキスパートまたはチームを特定し、オフラインでエキスパートに確認する必要があります。データと、データの用途に関するエキスパートの観点との間に明確なつながりはありません。
* データ ソースへのアクセスを要求するプロセスを理解しない限り、データ ソースやそのドキュメントを検出できても、必要なデータにアクセスすることはできません。

## <a name="discovery-challenges-for-data-producers"></a>データの作成者にとっての検出の難しさ
データの利用者が、以上に挙げた課題に直面する一方で、情報資産の作成と保守を担当するユーザーも固有の課題を抱えています。

* データ ソースに記述メタデータを使用して注釈を付ける作業は、多くの場合、無駄になります。通常、クライアント アプリケーションは、データ ソースに保存されている記述を無視するためです。
* データ ソースのドキュメントを作成する作業も、多くの場合は無駄になります。ドキュメントとデータ ソースの同期を維持することは継続的な責任であり、内容が古いと見なされてドキュメントを信頼しなくなることがよくあります。
* データ ソースへのアクセスを制限し、データの利用者がアクセス権の要求方法を確実に理解するようにすることが、現在の課題です。

データ ソースのドキュメントの作成と保守は複雑で、時間がかかる作業です。 データ ソースを使用する全員がすぐにドキュメントを利用できるようにするという課題は、さらに大変です。

このような課題が複数存在すると、エンタープライズ データの使用と理解を推奨したいと考える会社にとって大きな障壁になりまする

## <a name="azure-data-catalog-can-help"></a>Azure Data Catalog が解決するもの
Data Catalog はこれまでに述べた問題の解決を目指して設計されており、企業は Data Catalog によって、既存の情報資産から最大の価値を引き出すことができます。 Data Catalog の利点は、管理下のデータを必要とするユーザーが、そのデータ ソースを容易に検出し、把握できるようになることです。

Data Catalog は、データ ソースを登録できるクラウドベースのサービスを提供しています。 データは元の場所に残りますが、メタデータのコピーと、データ ソースの場所に対する参照が Data Catalog に追加されます。 このメタデータのインデックスも作成されるので、検索で簡単に各データ ソースを見つけられるようになり、検出するユーザーにとって理解しやすくなります。

データ ソースを登録すると、登録したユーザーまたは社内の他のユーザーがメタデータを強化できるようになります。 すべてのユーザーが、記述、タグ、その他のメタデータ (ドキュメントやデータ ソースへのアクセス要求のプロセスなど) を指定してデータ ソースに注釈を設定できます。 この記述メタデータで、データ ソースから登録される構造メタデータ (列名やデータ型など) が補足されます。

データ ソースとその用途の検出することは、ソースの登録の主な目的です。 エンタープライズ ユーザーが仕事 (ビジネス インテリジェンス、アプリケーション開発、データ サイエンスなど、適切なデータが必要な作業) のためにデータが必要な場合、Data Catalog の検出エクスペリエンスを使用すると、ニーズに合うデータを簡単に検出し、データを理解して目的に合っているかどうかを評価し、好みのツールでデータ ソースを開いてデータを使用することができます。 同時に、ユーザーは Data Catalog を使用し、カタログに対し、登録済みのデータ ソースをタグ付け、文書化、注釈付け、およびカタログ ユーザーのコミュニティが検出、理解、および使用できる新しいデータ ソースを登録することができます。

![Data Catalog の機能](./media/data-catalog-what-is-data-catalog/data-catalog-capabilities.png)

## <a name="get-started-with-data-catalog"></a>Data Catalog を使ってみる
Data Catalog を今すぐ使用するには、 [www.azuredatacatalog.com](https://www.azuredatacatalog.com)にアクセスしてください。

[こちら](data-catalog-get-started.md)で概要をご覧いただけます。

## <a name="learn-more-about-data-catalog"></a>Data Catalog についてさらに理解を深める
Data Catalog の機能の詳細については、以下のページを参照してください。

* [データ ソースの登録方法](data-catalog-how-to-register.md)
* [データ ソースの探索方法](data-catalog-how-to-discover.md)
* [データ ソースに注釈を付ける方法](data-catalog-how-to-annotate.md)
* [データ ソースの文書化の方法](data-catalog-how-to-documentation.md)
* [データ ソースへの接続方法](data-catalog-how-to-connect.md)
* [ビッグ データを操作する方法](data-catalog-how-to-big-data.md)
* [データ資産を管理する方法](data-catalog-how-to-manage.md)
* [ビジネス用語集を設定する方法](data-catalog-how-to-business-glossary.md)
* [よく寄せられる質問](data-catalog-frequently-asked-questions.md)

