---
title: "データ ソースの文書化の方法 | Microsoft Docs"
description: "この記事では、Azure Data Catalog でデータ資産を文書化する方法について重点的に説明しています。"
services: data-catalog
documentationcenter: 
author: spelluru
manager: NA
editor: 
tags: 
ms.assetid: 053b1701-b848-4ada-b726-6f485caa9961
ms.service: data-catalog
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: data-catalog
ms.date: 01/19/2017
ms.author: spelluru
translationtype: Human Translation
ms.sourcegitcommit: 219dcbfdca145bedb570eb9ef747ee00cc0342eb
ms.openlocfilehash: a869c52c3e3574e4ffc958a82daaa12b2f6cb6c1


---
# <a name="document-data-sources"></a>データ ソースの文書化
## <a name="introduction"></a>はじめに
**Microsoft Azure Data Catalog** は、完全に管理されたクラウド サービスであり、エンタープライズ データ ソースの登録のシステムと検出のシステムとして機能します。 つまり、 **Azure Data Catalog** を使用すると、ユーザーはデータ ソースを検出、 *理解*、使用でき、組織は既存のデータからより多くの価値を引き出すことができます。

データ ソースが **Azure Data Catalog** に登録されると、そのメタデータはサービスによってコピーされてインデックスが付けられます。ただし、これで終わりではありません。 **Azure Data Catalog** では、ユーザーが包括的な文書を独自に用意し、データ ソースの使用法や一般的なシナリオを記述することができます。

「 [データ ソースに注釈を付ける方法](data-catalog-how-to-annotate.md)」では、データ ソースについて熟知している担当者がタグや説明を注釈として追加する機能を紹介しています。 **Azure Data Catalog** ポータルには、ユーザーがデータ資産とコンテナーを詳細に文書化できるよう、リッチ テキスト エディターが備わっています。 エディターは、段落の書式設定 (見出しなど) やテキストの書式設定、記号付きの箇条書き、番号付きの箇条書き、表に対応します。

単純な注釈であればタグと説明で十分です。 しかし、データ ソースの用途や業務上のシナリオをデータの利用者が理解しやすいよう、データ ソースについて熟知している担当者が包括的かつ詳細な文書を提供することもできます。 データ ソースの文書化は簡単です。 データ資産またはコンテナーを選択し、 **[ドキュメント]**を選択するだけです。

![](media/data-catalog-documentation/data-catalog-documentation.png)

## <a name="documenting-data-assets"></a>データ資産の文書化
**Azure Data Catalog** における文書化の利点は、データ カタログをコンテンツのリポジトリとして、既にあるデータ資産を網羅した概要を作成できることです。 ユーザーは、コンテナーやテーブルが表す詳細なコンテンツを広範囲に探ることができます。 既に別のコンテンツ リポジトリ (SharePoint やファイル共有など) にコンテンツがある場合、その既存のコンテンツを参照する、資産ドキュメントのリンクを追加することができます。 この機能によって、既存のドキュメントが発見されやすくなります。

> [!NOTE]
> 文書化の内容そのものは、検索インデックスに追加されません。
>
>

![](media/data-catalog-documentation/data-catalog-documentation2.png)

データ資産コンテナーの特徴や価値を記述しただけのものから、コンテナー内のテーブルのスキーマを詳細に記述したものまで、文書化にはさまざまなレベルがあります。 文書化のレベルは、ビジネス ニーズに応じて決めることが大切です。 ただしデータ資産の文書化には、良い点と悪い点があるのが普通です。

* コンテナーのみを文書化: すべてのコンテンツが一元化されますが、しかるべき情報に基づく判断の拠り所となる細かなデータが得られない可能性もあります。
* テーブルのみを文書化: 対象のオブジェクトに固有のコンテンツとなりますが、ドキュメントが複数の場所に分散されます。
* コンテナーとテーブルを文書化: 最も包括的な手法ですが、ドキュメントのメンテナンスの手間が増える可能性があります。

## <a name="summary"></a>概要
**Azure Data Catalog** でデータ ソースを文書化することにより、ニーズに応じた詳細度でデータ資産の概要を作成できます。  既存のコンテンツ リポジトリに保存されているコンテンツは、リンクを使って関連付けることで、既にある文書やデータ資産を一元化することができます。 ユーザーは適切なデータ資産を発見したうえで、ドキュメント全体を余さず利用することができます。



<!--HONumber=Nov16_HO3-->


