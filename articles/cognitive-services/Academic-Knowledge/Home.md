---
title: Academic Knowledge API とは
titlesuffix: Azure Cognitive Services
description: Academic Knowledge API を使用してユーザーのクエリを解釈し、Academic Graph から豊富な情報を取得します。
services: cognitive-services
author: darrine
manager: nitinme
ms.service: cognitive-services
ms.subservice: academic-knowledge
ms.topic: overview
ms.date: 10/30/2018
ms.author: darrine
ms.openlocfilehash: b15ed5e2b31ed817d3f6558858e2b7285f98a70f
ms.sourcegitcommit: bd15a37170e57b651c54d8b194e5a99b5bcfb58f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/07/2019
ms.locfileid: "57551723"
---
# <a name="academic-knowledge-api"></a>Academic Knowledge API

Academic Knowledge API へようこそ。 このサービスでは、教育や学習を目的とするユーザーのクエリを解釈し、Microsoft Academic Graph (MAG) から豊富な情報を取得できます。 MAG ナレッジ ベースは、学術的な活動をモデル化するエンティティ (研究分野、著者、機関、論文、開催地、およびイベント) から成る Web 規模の異種エンティティ グラフです。 

MAG データは、Bing Web インデックスだけではなく、Bing からの社内ナレッジ ベースからも掘り出されます。 継続的な Bing のインデックス化を受けて、この API には、Web のフォローによる検出と Bing によるインデックス化から取得された最新の情報が含まれます。 このデータセットに基づいて、Academic Knowledge API は、プロアクティブな提案を伴う反応の速い検索、豊富な研究論文のグラフ検索の結果、および論文と関連エントリのセットに含まれる属性値のヒストグラム分布がシームレスに結合された知識主導型の対話式ダイアログを実現できます。

Microsoft Academic Graph については、[https://aka.ms/academicgraph](https://aka.ms/academicgraph) を参照してください。

Academic Knowledge API は、Cognitive Services Preview から Cognitive Services Labs に移動されました。 プロジェクトの新しいホームページは [https://labs.cognitive.microsoft.com/en-us/project-academic-knowledge](https://labs.cognitive.microsoft.com/en-us/project-academic-knowledge) です。 既存の API キーは、2018 年 5 月 24日 まで機能します。 この日を過ぎたら、新しい API キーを生成してください。 既存のキーの有効期限が切れた後、有料プレビューは使用できなくなることに注意してください。 この API の無料レベルでは目的を十分に果たせない場合は、Microsoft のチームにお問い合わせください。 

## <a name="features"></a>機能
Academic Knowledge API は、次の 4 つの関連する REST エンドポイントで構成されます。  
  1. **interpret** – ユーザーの自然言語によるクエリ文字列を解釈します。 注釈付きの解釈を返し、ユーザーの入力内容を予測する検索ボックスの高性能な自動補完を実現します。  
  2. **evaluate** – クエリ式を評価し、Academic Knowledge エンティティの結果を返します。  
  3. **calchistogram** – クエリ式によって返されたアカデミック エンティティの属性値について、分布ヒストグラムを計算します。たとえば、特定の著者の年ごとの引用の分布を計算します。  
  
これらの API メソッドを一緒に使用して、リッチなセマンティック検索エクスペリエンスを作成できます。 ユーザーのクエリ文字列が指定されると、**interpret** メソッドによってクエリの注釈付きのバージョンと構造化されたクエリ式が用意され、必要に応じて、内在するアカデミック データのセマンティクスに基づいてユーザーのクエリが補完されます。 たとえば、ユーザーが文字列「*latent s*」と入力した場合、**interpret**メソッドは、ランク付けされた解釈のセットを用意し、*latent semantic analysis* (研究分野)、*latent structure analysis* (論文)、または *latent s* で始まるその他のエンティティ式を検索できることをユーザーに提示します。 この情報を使用して、ユーザーを目的の検索結果に迅速にガイドできます。

**evaluate** メソッドを使用して、一致する論文エンティティのセットをアカデミック ナレッジ ベースから取得でき、**calchistogram** メソッドを使用して、論文エンティティのセットの属性値の分布を計算できます。この分布を使用して、検索結果をさらにフィルター処理できます。        

## <a name="getting-started"></a>Getting Started (概要) 
左側にあるサブトピックで、詳細なドキュメントを確認してください。  例の読みやすさを向上させるために、REST API 呼び出しには URL エンコードされていない文字 (スペースなど) が含まれていることに注意してください。  コードには、適切な URL エンコードを適用する必要があります。
