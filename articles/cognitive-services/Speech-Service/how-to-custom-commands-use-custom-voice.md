---
title: 方法:Custom Voice でカスタム コマンドを使用する - 音声サービス
titleSuffix: Azure Cognitive Services
description: この記事では、カスタム コマンド アプリケーションの出力音声を指定します。
services: cognitive-services
author: singhsaumya
manager: yetian
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 06/16/2020
ms.author: sausin
ms.openlocfilehash: b1e488653f210089ee5ed6757fb2f3d1e4fb0fe4
ms.sourcegitcommit: dccb85aed33d9251048024faf7ef23c94d695145
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/28/2020
ms.locfileid: "87293866"
---
# <a name="use-custom-commands-with-custom-voice"></a>カスタム音声でカスタム コマンドを使用する

この記事では、カスタム コマンド アプリケーションのカスタム出力音声を選択する方法について説明します。

## <a name="select-a-custom-voice"></a>Custom Voice を選択する

1. カスタム コマンド アプリケーションで、左側のペインの **[設定]** を選択します。
1. 中央のペインで **[Custom Voice]** を選択します
1. テーブルから目的のカスタムまたはパブリック音声を選択します
1. **[保存]** を選択します。

> [!div class="mx-imgBorder"]
> ![例文とパラメーター](media/custom-commands/select-custom-voice.png)

> [!NOTE]
> - **パブリック音声**の場合、**ニューラル型**は特定のリージョンでのみ使用できます。 使用できるかどうかを確認するには、[リージョンまたはエンドポイントによる標準およびニューラル音声](https://docs.microsoft.com/azure/cognitive-services/speech-service/regions#standard-and-neural-voices)に関するページを参照してください。
> - **カスタム音声**については、Custom Voice プロジェクト ページから作成できます。 「[Custom Voice の概要](./how-to-custom-voice.md)」を参照してください。

これで、アプリケーションは既定の音声ではなく、選択した音声で応答します。
