---
title: JavaScript での Bing Speech Recognition API の利用 | Microsoft Docs
titlesuffix: Azure Cognitive Services
description: Cognitive Services の Bing Speech Recognition API を使用して、連続して音声をテキストに変換するアプリケーションを開発します。
services: cognitive-services
author: zhouwangzw
manager: wolfma
ms.service: cognitive-services
ms.subservice: bing-speech
ms.topic: article
ms.date: 09/18/2018
ms.author: zhouwang
ROBOTS: NOINDEX,NOFOLLOW
ms.openlocfilehash: 17901ad40a48e9ee8d1a8b872b04ad52b75b3a52
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/13/2019
ms.locfileid: "60515210"
---
# <a name="get-started-with-the-speech-recognition-api-in-javascript"></a>JavaScript での Speech Recognition API の利用

[!INCLUDE [Deprecation note](../../../../includes/cognitive-services-bing-speech-api-deprecation-note.md)]

Speech Recognition API を使用して音声をテキストに変換するアプリケーションを開発できます。 JavaScript クライアント ライブラリは、[Speech Service WebSocket プロトコル](../API-Reference-REST/websocketprotocol.md)を使用して、話しかけると同時に文字に起こされたテキストを受け取ることができます。 この記事では、JavaScript で Speech Recognition API の使用を開始するのに役立ちます。

## <a name="prerequisites"></a>前提条件

### <a name="subscribe-to-the-speech-recognition-api-and-get-a-free-trial-subscription-key"></a>Speech Recognition API をサブスクライブし、無料試用版のサブスクリプション キーを取得する

Speech API は、Cognitive Services の一部です。 無料試用版のサブスクリプション キーは、[Cognitive Services サブスクリプション](https://azure.microsoft.com/try/cognitive-services/) ページから取得できます。 Speech API を選択したら、 **[Get API Key]** (API キーの取得) を選択してキーを取得します。 プライマリ キーおよびセカンダリ キーを返します。 両方のキーが同じクォータに関連付けられているため、どちらのキーでも使用できます。

> [!IMPORTANT]
> サブスクリプション キーを取得します。 Speech クライアント ライブラリを使用するには、まず[サブスクリプション キー](https://azure.microsoft.com/try/cognitive-services/)を取得する必要があります。

## <a name="get-started"></a>作業開始

このセクションでは、サンプルの HTML ページを読み込むために必要な手順について説明します。 サンプルは [GitHub リポジトリ](https://github.com/Azure-Samples/SpeechToText-WebSockets-Javascript)にあります。 リポジトリから**サンプルを直接開く**か、リポジトリの**ローカル コピーからサンプルを開く**ことができます。

> [!NOTE]
> 一部のブラウザーでは、セキュリティで保護されていないマイクのアクセスをブロックします。 このため、サポートされるすべてのブラウザーで機能するように、HTTPS に 'sample'/'your app' をホストすることをお勧めします。

### <a name="open-the-sample-directly"></a>サンプルを直接開く

上記のようにサブスクリプション キーを取得します。 その後、 [サンプルへのリンク](https://htmlpreview.github.io/?https://github.com/Azure-Samples/SpeechToText-WebSockets-Javascript/blob/preview/samples/browser/Sample.html) を開きます。 これにより、既定のブラウザーでページが読み込まれます ([htmlPreview](https://github.com/htmlpreview/htmlpreview.github.com) を使用してレンダリングされます)。

### <a name="open-the-sample-from-a-local-copy"></a>ローカル コピーからサンプルを開く

サンプルをローカルで試すには、次のようにこのリポジトリを複製します。

```
git clone https://github.com/Azure-Samples/SpeechToText-WebSockets-Javascript
```

TypeScript のソースをコンパイルし、それらを 1 つの JavaScript ファイルにバンドルします ([npm](https://www.npmjs.com/) がコンピューターにインストールされている必要があります)。 次のように複製されたリポジトリのルートに変更し、コマンドを実行します。

```
cd SpeechToText-WebSockets-Javascript && npm run bundle
```

普段使用しているブラウザーで `samples\browser\Sample.html` を開きます。

## <a name="next-steps"></a>次の手順

自身の Web ページに SDK を組み込む方法の詳細は、[こちら](https://github.com/Azure-Samples/SpeechToText-WebSockets-Javascript)で確認できます。

## <a name="remarks"></a>解説

- Speech Recognition API は、3 つの[認識モード](../concepts.md#recognition-modes)をサポートします。 Sample.html ファイルの **Setup()** 関数を更新することで、モードを切り替えることができます。 サンプルでは、モードが既定で `Interactive` に設定されています。 モードを変更するには、パラメーター `SR.RecognitionMode.Interactive` を別のモードに更新します。 たとえば、パラメーターを `SR.RecognitionMode.Conversation` に変更します。
- サポートされている言語の完全な一覧については、「[サポートされている言語](../API-Reference-REST/supportedlanguages.md)」をご覧ください。

## <a name="related-topics"></a>関連トピック

- [JavaScript Speech Recognition API のサンプル リポジトリ](https://github.com/Azure-Samples/SpeechToText-WebSockets-Javascript)
- [REST API の概要](GetStartedREST.md)
