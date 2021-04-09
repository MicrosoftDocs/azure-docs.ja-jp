---
title: Custom Speech の概要 - 音声サービス
titleSuffix: Azure Cognitive Services
description: Custom Speech は、ご利用のアプリケーション、ツール、製品に使用する Microsoft の音声テキスト変換の正確性を評価して改善することのできる一連のオンライン ツールです。
services: cognitive-services
author: trevorbye
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 02/12/2021
ms.author: trbye
ms.custom: contperf-fy21q2; references_regions
ms.openlocfilehash: 13bf0f2430e0d58dd9ef28061aad897acf94ac3f
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "103493052"
---
# <a name="what-is-custom-speech"></a>Custom Speech とは

[Custom Speech](https://aka.ms/customspeech) は、アプリケーションや製品での Microsoft の音声テキスト変換の正確性を評価して改善するために使用できる、UI ベースのツールのセットです。 作業を始めるにあたって必要なのは、少数のテスト オーディオ ファイルだけです。 この記事のリンクに従って、カスタム音声テキスト変換エクスペリエンスの作成を開始しましょう。

## <a name="whats-in-custom-speech"></a>Custom Speech の内容

Custom Speech で何らかの操作を行うには、Azure アカウントと音声サービスのサブスクリプションが必要です。 アカウントの取得後、データの準備、モデルのトレーニングとテスト、認識品質の調査、正確性の評価を行い、最後にそのカスタム音声テキスト変換モデルをデプロイして使用することができます。

この図は、[Speech Studio の Custom Speech エリア](https://aka.ms/customspeech)を構成する諸要素を示しています。 それぞれのステップの詳細については、下のリンクを使用してください。

![Speech Studio の Custom Speech エリアを構成する要素を強調表示した図。](./media/custom-speech/custom-speech-overview.png)

1. [プロジェクトをサブスクライブして作成する](#set-up-your-azure-account)。 Azure アカウントを作成し、音声サービスをサブスクライブします。 この統合されたサブスクリプションにより、音声テキスト変換、テキスト読み上げ、音声翻訳、[Speech Studio](https://speech.microsoft.com/customspeech) にアクセスできるようになります。 その後、音声サービス サブスクリプションを使用して、初めての Custom Speech プロジェクトを作成します。

1. [テスト データをアップロードする](./how-to-custom-speech-test-and-train.md)。 テスト データ (オーディオ ファイル) をアップロードして、ご利用のアプリケーション、ツール、製品に使用する Microsoft の音声テキスト変換プランを評価します。

1. [認識品質を調査する](how-to-custom-speech-inspect-data.md) [Speech Studio](https://speech.microsoft.com/customspeech) を使用して、アップロードした音声を再生し、テスト データの音声認識品質を調査します。 定量的な測定については、[データの調査](how-to-custom-speech-inspect-data.md)に関するページを参照してください。

1. [正確性を評価して改善する](how-to-custom-speech-evaluate-data.md)。 音声テキスト変換モデルの正確性を評価して改善します。 さらにトレーニングが必要であるかどうかは、[Speech Studio](https://speech.microsoft.com/customspeech) で得られる "*ワード エラー率*" を使用して判断できます。 正確性に問題がなければ、音声サービスの API を直接使用できます。 相対平均で 5% から 20% 正確性を高めたい場合、ポータルの **[トレーニング]** タブを使用して、追加のトレーニング データ (人間がラベル付けしたトランスクリプトと関連テキストなど) をアップロードします。

1. [モデルをトレーニングしてデプロイする](how-to-custom-speech-train-model.md) ご自分の音声テスト データと一緒に、文書化されたトランスクリプト (10 から 1,000 時間) と関連テキスト (200 MB 未満) を入力することにより、音声テキスト変換モデルの正確性を高めます。 このデータが音声テキスト変換モデルのトレーニングに役立てられます。 トレーニング後に、再テストを行います。 その結果に問題がなければ、モデルをカスタム エンドポイントにデプロイすることができます。

## <a name="set-up-your-azure-account"></a>Azure アカウントの設定

[Speech Studio](https://speech.microsoft.com/customspeech) を使用してカスタム モデルを作成する前に、Azure アカウントと音声サービス サブスクリプションを用意する必要があります。 アカウントとサブスクリプションをお持ちでない場合は、[Speech Service を無料でお試しください](overview.md#try-the-speech-service-for-free)。

> [!NOTE]
> 必ず Standard (S0) サブスクリプションを作成してください。 Free (F0) サブスクリプションはサポートされていません。

**オーディオ データ** を使用してカスタム モデルをトレーニングする場合は、トレーニングに使用できる専用のハードウェアを備えた次のいずれかのリージョンを選択してください。 これにより、モデルのトレーニングにかかる時間が短縮され、トレーニングにより多くのオーディオを使用できるようになります。 これらのリージョンでは、音声サービスによって最大 20 時間のオーディオがトレーニングに使用されます。他のリージョンでは、最大 8 時間しか使用されません。

* オーストラリア東部
* カナダ中部
* インド中部
* 米国東部
* 米国東部 2
* 米国中北部
* 北ヨーロッパ
* 米国中南部
* 東南アジア
* 英国南部
* US Gov アリゾナ
* US Gov バージニア州
* 西ヨーロッパ
* 米国西部 2

Azure アカウントと音声サービス サブスクリプションを作成したら、[Speech Studio](https://speech.microsoft.com/customspeech) にサインインして、ご利用のサブスクリプションを接続する必要があります。

1. [Speech Studio](https://aka.ms/custom-speech) にサインインします。
1. 作業に必要なサブスクリプションを選択し、音声プロジェクトを作成します。
1. サブスクリプションを変更する場合は、上部のメニューで [歯車] ボタンを選択します。

## <a name="how-to-create-a-project"></a>プロジェクトを作成する方法

データ、モデル、テスト、エンドポイントなどのコンテンツは、[Speech Studio](https://speech.microsoft.com/customspeech) の "*プロジェクト*" にまとめられます。 プロジェクトは、ドメインと国および言語ごとに作成されます。 たとえば、米国の英語を使用するコール センターのプロジェクトを作成することが考えられます。

初めてのプロジェクトを作成するには、 **[音声テキスト変換/Custom Speech]** を選択し、 **[新しいプロジェクト]** を選択します。 ウィザードの手順に従ってプロジェクトを作成します。 プロジェクトを作成したら、4 つのタブが表示されます ( **[データ]** 、 **[テスト中]** 、 **[トレーニング]** 、 **[デプロイ]** )。 それぞれのタブの使い方については、「[次の手順](#next-steps)」に記載のリンクを使用してください。

> [!IMPORTANT]
> [Speech Studio](https://aka.ms/custom-speech) (以前の名称は "Custom Speech ポータル") が最近更新されました。 CRIS.ai ポータルで、または API を使用して以前のデータ、モデル、テスト、および公開されたエンドポイントを作成した場合は、新しいポータルで新しいプロジェクトを作成して、これらの古いエンティティに接続する必要があります。

## <a name="model-and-endpoint-lifecycle"></a>モデルとエンドポイントのライフサイクル

普通は最新のモデルの方が正確性が高いため、古いモデルは時間の経過と共にあまり役に立たなくなります。 そのため、ベース モデルに加え、ポータルで作成されたカスタム モデルとエンドポイントは、適応に 1 年、デコードに 2 年の有効期限が与えられます。 詳しい説明は「[モデルとエンドポイントのライフサイクル](./how-to-custom-speech-model-and-endpoint-lifecycle.md)」の記事をご覧ください。

## <a name="next-steps"></a>次のステップ

* [データを準備してテストする](./how-to-custom-speech-test-and-train.md)
* [データを検査する](how-to-custom-speech-inspect-data.md)
* [モデルの精度を評価して改善する](how-to-custom-speech-evaluate-data.md)
* [モデルのトレーニングとデプロイ](how-to-custom-speech-train-model.md)
