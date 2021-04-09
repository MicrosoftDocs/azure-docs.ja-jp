---
title: クエリの要件
titleSuffix: Azure Cognitive Services
services: cognitive-services
author: mrbullwinkle
manager: nitinme
ms.service: cognitive-services
ms.subservice: metrics-advisor
ms.topic: include
ms.date: 09/30/2020
ms.author: mbullwin
ms.openlocfilehash: 777e3261bd6f842861ed6622b78589eedaaf65d7
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/30/2021
ms.locfileid: "92043207"
---
クエリ内で `@StartTime` パラメーターを使用し、1 つのタイムスタンプのメトリック データを取得します。 Metrics Advisor によって、クエリの実行時、パラメーターが `yyyy-MM-ddTHH:mm:ss` 形式の文字列に置換されます。

> [!IMPORTANT]
> クエリからは、各タイムスタンプで、ディメンションの各組み合わせに対して多くても 1 つのレコードが返される必要があります。 また、クエリによって返されるすべてのレコードで、タイムスタンプが同じになっている必要があります。 Metrics Advisor によってタイムスタンプごとにこのクエリが実行され、データが取り込まれます。 詳細と例については、[クエリの FAQ セクション](../faq.md#how-do-i-write-a-valid-query-for-ingesting-my-data)を参照してください。 