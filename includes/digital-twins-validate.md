---
author: baanders
description: Azure Digital Twins モデルの検証方法を説明するファイルを含める
ms.service: digital-twins
ms.topic: include
ms.date: 8/7/2020
ms.author: baanders
ms.openlocfilehash: 0146660fa50f9a136cd82483200ca2e6c6cc0b89
ms.sourcegitcommit: 25bb515efe62bfb8a8377293b56c3163f46122bf
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/07/2020
ms.locfileid: "87985880"
---
> [!TIP]
> モデルの作成後、モデルは、Azure Digital Twins インスタンスにアップロードする前に、オフラインで検証することが推奨されます。

モデル ドキュメントを検証して、DTDL が正しいことを確認するために、言語に依存しないサンプルが用意されています。 これはこちらにあります。[**DTDL 検証ツール サンプル**](https://docs.microsoft.com/samples/azure-samples/dtdl-validator/dtdl-validator)

DTDL 検証ツールのサンプルは、NuGet でクライアント側ライブラリとして提供されている .NET DTDL パーサー ライブラリに基づいて構築されています。[**Microsoft.Azure.DigitalTwins.Parser**](https://nuget.org/packages/Microsoft.Azure.DigitalTwins.Parser/). また、このライブラリを直接使用し、自分独自の検証ソリューションを設計することもできます。 パーサー ライブラリを使用する場合は、Azure Digital Twins が実行されているバージョンと互換性のあるバージョンを使用するようにしてください。 プレビュー期間中のこれのバージョンは、*3.7.0* です。

使用例を含む検証ツールのサンプルとパーサー ライブラリの詳細については、"[*モデルを解析および検証する方法*](../articles/digital-twins/how-to-parse-models.md)" に関するページを参照してください。