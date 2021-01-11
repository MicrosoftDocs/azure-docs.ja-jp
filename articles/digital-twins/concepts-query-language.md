---
title: クエリ言語
titleSuffix: Azure Digital Twins
description: Azure Digital Twins クエリ言語の基本について理解します。
author: baanders
ms.author: baanders
ms.date: 3/26/2020
ms.topic: conceptual
ms.service: digital-twins
ms.openlocfilehash: 29e1fa603600e246031f2a86aae3b0876b4910ba
ms.sourcegitcommit: 97a0d868b9d36072ec5e872b3c77fa33b9ce7194
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/04/2020
ms.locfileid: "87562475"
---
# <a name="about-the-query-language-for-azure-digital-twins"></a>Azure Digital Twins 用のクエリ言語について

Azure Digital Twins の中心は、**デジタル ツイン**と**リレーションシップ**から構築された[**ツイン グラフ**](concepts-twins-graph.md)であることを思い出してください。 このグラフに対してクエリを実行し、デジタル ツインとそれに含まれるリレーションシップに関する情報を取得することができます。 これらのクエリは、**Azure Digital Twins クエリ言語**と呼ばれる、SQL に似たクエリ言語で記述されます。

クライアント アプリからサービスにクエリを送信するには、Azure Digital Twins の [**Query API**](https://docs.microsoft.com/dotnet/api/azure.digitaltwins.core.digitaltwinsclient.query?view=azure-dotnet-preview) を使用します。 これにより、開発者はクエリを作成し、フィルターを適用して、ツイン グラフ内のデジタル ツインのセットや、Azure Digital Twins のシナリオに関するその他の情報を検索できます。

[!INCLUDE [digital-twins-query-operations.md](../../includes/digital-twins-query-operations.md)]

## <a name="next-steps"></a>次のステップ

クエリの作成方法を学習し、クライアント コードの例を見るには、[*方法:ツイン グラフにクエリを実行する*](how-to-query-graph.md)方法に関する記事を参照してください。
