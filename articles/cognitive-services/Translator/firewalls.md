---
title: ファイアウォールの内側で翻訳する - Translator
titleSuffix: Azure Cognitive Services
description: Azure Cognitive Services Translator による翻訳は、ドメイン名フィルタリングまたは IP フィルタリングを使用して、ファイアウォールの内側で実行できます。
services: cognitive-services
author: laujan
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.topic: conceptual
ms.date: 05/26/2020
ms.author: lajanuar
ms.openlocfilehash: f5dd72328180574809c812d670f8165ad84963ae
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "98897749"
---
# <a name="how-to-translate-behind-ip-firewalls-with-translator"></a>Translator を使用して IP ファイアウォールの内側で翻訳する方法

Translator による翻訳は、ドメイン名フィルタリングまたは IP フィルタリングを使用して、ファイアウォールの内側で実行できます。 可能であればドメイン名フィルタリングの使用をお勧めします。 IP フィルタリング ファイアウォールの内側から Microsoft Translator を実行することは **お勧めしません**。 この構成は将来、予告なく動作不能になる可能性があります。

## <a name="translator-ip-addresses"></a>Translator の IP アドレス
api.cognitive.microsofttranslator.com の IP アドレス - 2019 年 8 月 21 日時点の Translator:

* **アジア太平洋:** 20.40.125.208、20.43.88.240、20.184.58.62、40.90.139.163、104.44.89.44
* **ヨーロッパ:** 40.90.138.4、40.90.141.99、51.105.170.64、52.155.218.251
* **北米:** 40.90.139.36、40.90.139.2、40.119.2.134、52.224.200.129、52.249.207.163

## <a name="next-steps"></a>次のステップ
> [!div class="nextstepaction"]
> [Translator で IP ファイアウォールの内側で翻訳する](reference/v3-0-translate.md)
