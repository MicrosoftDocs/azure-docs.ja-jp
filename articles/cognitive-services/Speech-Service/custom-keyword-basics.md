---
title: キーワード作成クイックスタート - Speech サービス
titleSuffix: Azure Cognitive Services
description: デバイスは常にキーワード (またはフレーズ) をリッスンしています。 ユーザーがキーワードを読み上げると、ユーザーが読み上げを止めるまで、デバイスは後続のすべての音声をクラウドに送信します。 キーワードをカスタマイズすることは、デバイスを差別化し、ブランドを強化する上で効果的な方法です。
services: cognitive-services
author: trevorbye
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 11/03/2020
ms.author: trbye
ms.custom: devx-track-csharp
zone_pivot_groups: keyword-quickstart
ms.openlocfilehash: 49ac70b6881085f48c8bc3a12e31e4a1aa220c6a
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/30/2021
ms.locfileid: "95021951"
---
# <a name="get-started-with-custom-keyword"></a>Custom Keyword の概要

このクイックスタートでは、Speech Studio と Speech SDK を使用してカスタム キーワードを操作する方法の基本について説明します。 キーワードは、音声で製品を有効できるようにする単語または短い語句です。 Speech Studio でキーワード モデルを作成した後、アプリケーションの Speech SDK で使用するモデル ファイルをエクスポートします。

## <a name="prerequisites"></a>前提条件

この記事の手順では、Speech サブスクリプションと Speech SDK が必要です。 まだサブスクリプションをお持ちでない場合は、[Speech Service を無料でお試しください](overview.md#try-the-speech-service-for-free)。 SDK を入手するには、ご利用のプラットフォームの[インストール ガイド](quickstarts/setup-platform.md)を参照してください。

## <a name="create-a-keyword-in-speech-studio"></a>Speech Studio でキーワードを作成する

カスタム キーワードを使用する前に、[Speech Studio](https://aka.ms/sdsdk-speechportal) の [[Custom Keyword]\(カスタム キーワード\)](https://aka.ms/sdsdk-wakewordportal) ページを使用してキーワードを作成する必要があります。 キーワードを指定すると、Speech SDK で使用できる `.table` ファイルが生成されます。

> [!IMPORTANT]
> カスタム キーワード モデルと、生成される `.table` ファイルは、Speech Studio で **のみ** 作成できます。
> SDK または REST 呼び出しを使用してカスタム キーワードを作成することはできません。

1. [Speech Studio](https://aka.ms/sdsdk-speechportal)に移動して **サインイン** します。音声サブスクリプションをまだ持っていない場合は、[ **[サブスクリプションを作成する]**](https://go.microsoft.com/fwlink/?linkid=2086754) を選択します。

1. [[カスタム キーワード]](https://aka.ms/sdsdk-wakewordportal) ページで **[新しいプロジェクト]** を作成します。 

1. **[名前]** と任意で **[説明]** を入力し、言語を選択します。 プロジェクトは言語あたり 1 つ必要になります。現在のところ、サポートは `en-US` 言語に限定されています。

    ![キーワード プロジェクトについて説明する](media/custom-keyword/custom-kws-portal-new-project.png)

1. 一覧からプロジェクトを選択します。 

    ![キーワード プロジェクトを選択する](media/custom-keyword/custom-kws-portal-project-list.png)

1. 新しいキーワード モデルを作成するには、 **[モデルのトレーニング]** をクリックします。

1. モデルの **[名前]** 、 **[説明]** (省略可能)、および任意の **[キーワード]** を入力し、 **[次へ]** をクリックします。 効果的なキーワードを選択する方法については、[ガイドライン](./custom-keyword-overview.md#choose-an-effective-keyword)を参照してください。

    ![キーワードを入力する](media/custom-keyword/custom-kws-portal-new-model.png)

1. ポータルで、キーワードの発音候補が作成されます。 再生ボタンをクリックして各工法をリッスンし、間違った発音の横にあるチェック ボックスをオフにします。 正しい発音のチェック ボックスのみをオンにした後、 **[トレーニング]** をクリックし、キーワード モデルの生成を開始します。 

    ![正しい発音を選択する場所を示すスクリーンショット。](media/custom-keyword/custom-kws-portal-choose-prons.png)

1. モデルが生成されるまでに最大で 30 分かかる場合があります。 モデルが完了すると、キーワードの一覧が **[処理中]** から **[成功]** に変わります。 これでファイルをダウンロードできます。

    ![キーワードを確認する](media/custom-keyword/custom-kws-portal-download-model.png)

1. ダウンロードしたファイルは `.zip` アーカイブです。 アーカイブを抽出すると、`.table` 拡張子を持つファイルが表示されます。 これは、次のセクションの SDK で使用するファイルであるため、パスをメモしておいてください。 ファイル名にはキーワード名が反映されます。たとえば、**Activate device** というキーワードのファイル名は `Activate_device.table` になります。

## <a name="use-a-keyword-model-with-the-sdk"></a>SDK でのキーワード モデルの使用

::: zone pivot="programming-language-csharp"
[!INCLUDE [C# Basics include](includes/how-to/keyword-recognition/keyword-basics-csharp.md)]
::: zone-end

::: zone pivot="programming-language-python"
[!INCLUDE [Python Basics include](includes/how-to/keyword-recognition/keyword-basics-python.md)]
::: zone-end

::: zone pivot="programming-languages-objectivec-swift"
[!INCLUDE [ObjectiveC/Swift Basics include](includes/how-to/keyword-recognition/keyword-basics-objc.md)]
::: zone-end

## <a name="next-steps"></a>次のステップ

カスタム キーワードを [Speech Devices SDK クイックスタート](./speech-devices-sdk-quickstart.md?pivots=platform-android)でテストする。