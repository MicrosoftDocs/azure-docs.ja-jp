---
title: カスタム キーワードを作成する - Speech サービス
titleSuffix: Azure Cognitive Services
description: デバイスは常にキーワード (またはフレーズ) をリッスンしています。 ユーザーがキーワードを読み上げると、ユーザーが読み上げを止めるまで、デバイスは後続のすべての音声をクラウドに送信します。 キーワードをカスタマイズすることは、デバイスを差別化し、ブランドを強化する上で効果的な方法です。
services: cognitive-services
author: trevorbye
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 07/20/2019
ms.author: trbye
ms.custom: devx-track-csharp
ms.openlocfilehash: d80f244f7b5e17d730451093070b971e9aa041b9
ms.sourcegitcommit: 62e1884457b64fd798da8ada59dbf623ef27fe97
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/26/2020
ms.locfileid: "88919014"
---
# <a name="custom-keyword-basics"></a>カスタム キーワードの基本

この記事では、Speech Studio と Speech SDK を使用してカスタム キーワードを操作する方法の基本について説明します。 キーワードは、音声で製品を有効できるようにする単語または短い語句です。 Speech Studio でキーワード モデルを作成した後、アプリケーションの Speech SDK で使用するモデル ファイルをエクスポートします。

## <a name="prerequisites"></a>前提条件

この記事の手順では、Speech サブスクリプションと Speech SDK が必要です。 まだサブスクリプションをお持ちでない場合は、[Speech Service を無料でお試しください](get-started.md)。 SDK を入手するには、ご利用のプラットフォームの[インストール ガイド](quickstarts/setup-platform.md)を参照してください。

## <a name="create-a-keyword-in-speech-studio"></a>Speech Studio でキーワードを作成する

カスタム キーワードを使用する前に、[Speech Studio](https://aka.ms/sdsdk-speechportal) の [[Custom Keyword]\(カスタム キーワード\)](https://aka.ms/sdsdk-wakewordportal) ページを使用してキーワードを作成する必要があります。 キーワードを指定すると、Speech SDK で使用できる `.table` ファイルが生成されます。

> [!IMPORTANT]
> カスタム キーワード モデルと、生成される `.table` ファイルは、Speech Studio で**のみ**作成できます。
> SDK または REST 呼び出しを使用してカスタム キーワードを作成することはできません。

1. [Speech Studio](https://aka.ms/sdsdk-speechportal)に移動して**サインイン**します。音声サブスクリプションをまだ持っていない場合は、[ **[サブスクリプションを作成する]** ](https://go.microsoft.com/fwlink/?linkid=2086754) を選択します。

1. [[カスタム キーワード]](https://aka.ms/sdsdk-wakewordportal) ページで **[新しいプロジェクト]** を作成します。 

1. **[名前]** と任意で **[説明]** を入力し、言語を選択します。 プロジェクトは言語あたり 1 つ必要になります。現在のところ、サポートは `en-US` 言語に限定されています。

    ![キーワード プロジェクトについて説明する](media/custom-keyword/custom-kws-portal-new-project.png)

1. 一覧からプロジェクトを選択します。 

    ![キーワード プロジェクトを選択する](media/custom-keyword/custom-kws-portal-project-list.png)

1. 新しいキーワード モデルを作成するには、 **[モデルのトレーニング]** をクリックします。

1. モデルの **[名前]** 、 **[説明]** (省略可能)、および任意の **[キーワード]** を入力し、 **[次へ]** をクリックします。 効果的なキーワードを選択する方法については、[ガイドライン](speech-devices-sdk-kws-guidelines.md#choose-an-effective-keyword)を参照してください。

    ![キーワードを入力する](media/custom-keyword/custom-kws-portal-new-model.png)

1. ポータルで、キーワードの発音候補が作成されます。 再生ボタンをクリックして各工法をリッスンし、間違った発音の横にあるチェック ボックスをオフにします。 正しい発音のチェック ボックスのみをオンにした後、 **[トレーニング]** をクリックし、キーワード モデルの生成を開始します。 

    ![キーワードを確認する](media/custom-keyword/custom-kws-portal-choose-prons.png)

1. モデルが生成されるまでに最大で 30 分かかる場合があります。 モデルが完了すると、キーワードの一覧が **[処理中]** から **[成功]** に変わります。 これでファイルをダウンロードできます。

    ![キーワードを確認する](media/custom-keyword/custom-kws-portal-download-model.png)

1. ダウンロードしたファイルは `.zip` アーカイブです。 アーカイブを抽出すると、`.table` 拡張子を持つファイルが表示されます。 これは、次のセクションの SDK で使用するファイルであるため、パスをメモしておいてください。 ファイル名にはキーワード名が反映されます。たとえば、**Activate device** というキーワードのファイル名は `Activate_device.table` になります。

## <a name="use-a-keyword-model-with-the-sdk"></a>SDK でのキーワード モデルの使用

まず、`KeywordRecognitionModel` を返す `FromFile()` 静的関数を使用して、キーワード モデル ファイルを読み込みます。 Speech Studio からダウンロードした `.table` ファイルへのパスを使用します。 また、既定のマイクを使用して `AudioConfig` を作成し、オーディオ構成を使用して新しい `KeywordRecognizer` をインスタンス化します。

```csharp
using Microsoft.CognitiveServices.Speech;
using Microsoft.CognitiveServices.Speech.Audio;

var keywordModel = KeywordRecognitionModel.FromFile("your/path/to/Activate_device.table");
using var audioConfig = AudioConfig.FromDefaultMicrophoneInput();
using var keywordRecognizer = new KeywordRecognizer(audioConfig);
```

次に、モデル オブジェクトを渡すことによって、`RecognizeOnceAsync()` を 1 回呼び出すだけでキーワード認識が実行されます。 これにより、キーワードが認識されるまで継続されるキーワード認識セッションが開始されます。 そのため、このデザイン パターンは、一般的にはマルチスレッド アプリケーションや、ウェイクワードを無期限に待ち続けるような場合に使用します。

```csharp
KeywordRecognitionResult result = await keywordRecognizer.RecognizeOnceAsync(keywordModel);
```

> [!NOTE]
> ここで示す例では、ローカル キーワード認識を使用しています。これは、認証コンテキストに `SpeechConfig` オブジェクトを必要とせず、バックエンドに接続しないためです。 ただし、[継続的なバックエンド接続を利用](https://docs.microsoft.com/azure/cognitive-services/speech-service/tutorial-voice-enable-your-bot-speech-sdk#view-the-source-code-that-enables-keyword)することで、キーワード認識と検証の両方を実行することができます。

## <a name="next-steps"></a>次のステップ

カスタム キーワードを [Speech Devices SDK クイックスタート](https://aka.ms/sdsdk-quickstart)でテストする。
