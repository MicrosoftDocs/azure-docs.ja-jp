---
author: baanders
description: Azure Digital Twins のインクルード ファイル - インスタンスを管理する方法
ms.service: digital-twins
ms.topic: include
ms.date: 11/11/2020
ms.author: baanders
ms.openlocfilehash: 02f6c59a76a3fdb7bd4360570b29d7b40a1aff8d
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "102473787"
---
この記事では、[Azure Digital Twins .NET (C#) **SDK**](/dotnet/api/overview/azure/digitaltwins/management) を使用して、さまざまな管理操作を実行する方法について説明します。 また、その他の言語の SDK を使用して、これらの同じ管理呼び出しを作成することもできます。その他の言語の SDK については、"[*Azure Digital Twins の API および SDK を使用する方法*](../articles/digital-twins/how-to-use-apis-sdks.md)" に関するページで参照してください。

> [!TIP] 
> すべての SDK メソッドに同期バージョンと非同期バージョンがあることに注意してください。 ページング呼び出しの場合、非同期メソッドは `AsyncPageable<T>` を返し、同期バージョンが `Pageable<T>` を返します。

別の管理オプションとして、このトピック領域の Azure Digital Twins [**REST API**](/rest/api/azure-digitaltwins/) を、Postman などの REST クライアントを介して直接呼び出すこともできます。 この方法の手順については、「[*方法: Postman で要求を行う*](../articles/digital-twins/how-to-use-postman.md)」を参照してください。

最後に、Azure Digital Twins **CLI** を使用して、同じ管理操作を実行できます。 CLI の使用に関する詳細については、["*Azure Digital Twins CLI を使用する方法*](../articles/digital-twins/how-to-use-cli.md)" に関するページを参照してください。