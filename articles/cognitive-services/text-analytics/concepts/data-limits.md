---
title: Text Analytics API のデータ制限
titleSuffix: Azure Cognitive Services
description: Azure Cognitive Services の Text Analytics API のデータ制限。
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: text-analytics
ms.topic: overview
ms.date: 08/14/2020
ms.author: aahi
ms.reviewer: chtufts
ms.openlocfilehash: 068c2dc698e9f0b6d6f2f6486dff863c1343b178
ms.sourcegitcommit: ef055468d1cb0de4433e1403d6617fede7f5d00e
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/16/2020
ms.locfileid: "88258280"
---
# <a name="data-and-rate-limits-for-the-text-analytics-api"></a>Text Analytics API のデータとレートの制限
<a name="data-limits"></a>

この記事では、サイズの制限と、Text Analytics API にデータを送信できるレートの制限について説明します。 

## <a name="data-limits"></a>データ制限

> [!NOTE]
> * 制限を超えるドキュメントを分析する必要がある場合は、テキストを小さなチャンクに分割してから API に送信することができます。 
> * ドキュメントとは、テキスト文字の 1 つの文字列です。  

| 制限 | 値 |
|------------------------|---------------|
| 1 つのドキュメントの最大サイズ | [StringInfo.LengthInTextElements](https://docs.microsoft.com/dotnet/api/system.globalization.stringinfo.lengthintextelements) によって計測された 5,120 文字。 また、これは Text Analytics for Health コンテナーにも適用されます。 |
| 要求全体の最大サイズ | 1 MB。 また、これは Text Analytics for Health コンテナーにも適用されます。 |

1 回の要求で送信できるドキュメントの最大数は、使用している API のバージョンと機能によって異なります。

#### <a name="version-3"></a>[Version 3](#tab/version-3)

API の v3 では、次の制限が変更されています。 以下の制限を超えると、HTTP 400 エラー コードが生成されます。


| 機能 | 要求あたりのドキュメントの最大数 | 
|----------|-----------|
| 言語検出 | 1000 |
| 感情分析 | 10 |
| キー フレーズ抽出 | 10 |
| 名前付きエンティティの認識 | 5 |
| Entity Linking | 5 |
| Text Analytics for Health コンテナー | 1000 |
#### <a name="version-2"></a>[Version 2](#tab/version-2)

| 機能 | 要求あたりのドキュメントの最大数 | 
|----------|-----------|
| 言語検出 | 1000 |
| 感情分析 | 1000 |
| キー フレーズ抽出 | 1000 |
| 名前付きエンティティの認識 | 1000 |
| Entity Linking | 1000 |

---

## <a name="rate-limits"></a>転送率の制限

レート制限は[価格レベル](https://azure.microsoft.com/pricing/details/cognitive-services/text-analytics/)によって異なります。 これらの制限は、API の両方のバージョンで同じです。 これらのレート制限は、設定されたレート制限がない Text Analytics for Health コンテナーには適用されません。

| レベル          | 1 秒あたりの要求数 | 1 分あたりの要求数 |
|---------------|---------------------|---------------------|
| S/マルチサービス | 1000                | 1000                |
| S0/F0         | 100                 | 該当なし                 |
| S1            | 200                 | 300                 |
| S2            | 300                 | 300                 |
| S3            | 500                 | 500                 |
| S4            | 1000                | 1000                |

要求数は、Text Analytics の機能ごとに個別に測定されます。 たとえば、各機能に対して、利用中の価格レベルで最大数の要求を同時に送信できます。  


## <a name="see-also"></a>関連項目

* [Text Analytics API とは](../overview.md)
* [価格の詳細](https://azure.microsoft.com/pricing/details/cognitive-services/text-analytics/)
