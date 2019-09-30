---
title: Custom Voice とは - Speech Service
titleSuffix: Azure Cognitive Services
description: Custom Voice は、ブランド用に認識性の高い固有の音声を作成できる、一連のオンライン ツールです。 作業を始めるにあたって必要なのは、少数のオーディオ ファイルと関連する文字起こしだけです。 以下のリンクに従って、カスタム音声テキスト変換エクスペリエンスの作成を開始しましょう。
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 07/05/2019
ms.author: erhopf
ms.openlocfilehash: 9bb654f37ecc4fda46171e859adac0236b51e4b2
ms.sourcegitcommit: 7c5a2a3068e5330b77f3c6738d6de1e03d3c3b7d
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/11/2019
ms.locfileid: "70881483"
---
# <a name="get-started-with-custom-voice"></a>Custom Voice の概要

[Custom Voice](https://aka.ms/customvoice) は、ブランド用に認識性の高い固有の音声を作成できる、一連のオンライン ツールです。 作業を始めるにあたって必要なのは、少数のオーディオ ファイルと関連する文字起こしだけです。 以下のリンクに従って、カスタム テキスト読み上げエクスペリエンスの作成を開始しましょう。

## <a name="whats-in-custom-voice"></a>Custom Voice の内容

Custom Voice を開始する前に、Azure アカウントと Speech Services サブスクリプションが必要になります。 アカウントを作成したら、データの準備、モデルのトレーニングおよびテスト、音声品質の評価、そしてカスタム音声モデルのデプロイを行えます。

下の図では、[Custom Voice ポータル](https://aka.ms/customvoice)を使用してカスタム音声モデルを作成する手順を示しています。 リンクを使用して、詳細を確認してください。

![Custom Voice のアーキテクチャ図](media/custom-voice/custom-voice-diagram.png)

1.  [登録を行ってプロジェクトを作成する](#set-up-your-azure-account) - Azure アカウントを作成し、Speech Services サブスクリプションを作成します。 この一元化されたサブスクリプションを通じて、音声テキスト変換、テキスト読み上げ、音声翻訳、Custom Voice ポータルにアクセスすることができます。 次に、Speech Services サブスクリプションを使用して、最初の Custom Voice プロジェクトを作成します。

2.  [データをアップロードする](how-to-custom-voice-create-voice.md#upload-your-datasets) - Custom Voice ポータルまたは Custom Voice API を使用して、データ (オーディオとテキスト) をアップロードします。 ポータルで、発音スコアと信号対雑音比を調査および評価できます。 詳細については、[Custom Voice 用にデータを準備する方法](how-to-custom-voice-prepare-data.md)に関する記事を参照してください。

3.  [モデルをトレーニングする](how-to-custom-voice-create-voice.md#build-your-custom-voice-model) - 自分のデータを使用して、カスタム テキスト読み上げ音声モデルを作成します。 モデルはさまざまな言語でトレーニングできます。 トレーニング後にモデルをテストし、結果に満足したら、モデルをデプロイできます。

4.  [モデルをデプロイする](how-to-custom-voice-create-voice.md#create-and-use-a-custom-voice-endpoint) - 自分のテキスト読み上げ音声モデルのカスタム エンドポイントを作成して、自分の製品、ツール、アプリケーションの音声合成にそれを使用します。

## <a name="set-up-your-azure-account"></a>Azure アカウントの設定

Custom Speech ポータルを使用してカスタム モデルを作成するには、Speech Services サブスクリプションが必要です。 Azure で Speech Services サブスクリプションを作成するには、以下の手順に従います。 Azure アカウントがない場合、新しいものにサインアップできます。  

Azure アカウントと Speech Services サブスクリプションを作成したら、Custom Voice ポータルにサインインして、自分のサブスクリプションを接続する必要があります。

1. Azure portal から Speech Services サブスクリプション キーを取得します。
2. [Custom Voice ポータル](https://aka.ms/custom-voice)にサインインします。
3. 自分のサブスクリプションを選択し、音声プロジェクトを作成します。
4. 別の Speech サブスクリプションに切り替えたい場合は、上部のナビゲーションにある歯車アイコンを使用します。

> [!NOTE]
> Custom Voice サービスでは、30 日の無料試用版キーはサポートされていません。 サービスを使用する前に、Azure で F0 または S0 のキーを作成しておく必要があります。

## <a name="how-to-create-a-project"></a>プロジェクトを作成する方法

データ、モデル、テスト、エンドポイントなどのコンテンツは、Custom Voice ポータルの **[プロジェクト]** にまとめられます。 各プロジェクトは、国と言語、および作成したい音声の性別に固有です。 たとえば、米国の英語 (en-US) を使用するコール センターのチャット ボット用に、女性の音声のプロジェクトを作成できます。

初めてのプロジェクトを作成するには、 **[Text-to-Speech/Custom Voice]\(テキスト読み上げ/Custom Voice\)** タブを選択して、 **[新しいプロジェクト]** をクリックします。 ウィザードの手順に従ってプロジェクトを使用します。 プロジェクトの作成後、4 つのタブが表示されます: **[データ]** 、 **[トレーニング]** 、 **[テスト中]** 、 **[デプロイ]** 。 それぞれのタブの使い方については、「[次の手順](#next-steps)」に記載のリンクを使用してください。

## <a name="next-steps"></a>次の手順

- [Custom Voice のデータを用意する](how-to-custom-voice.md)
- [カスタム音声を作成する](how-to-custom-voice-create-voice.md)
- [ガイド: 音声サンプルを録音する](record-custom-voice-samples.md)
