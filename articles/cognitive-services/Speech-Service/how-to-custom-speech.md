---
title: Custom Speech の概要 - 音声サービス
titleSuffix: Azure Cognitive Services
description: Custom Speech は、ご利用のアプリケーション、ツール、製品に使用する音声テキスト変換の正確性を評価して改善することのできる一連のオンライン ツールです。 作業を始めるにあたって必要なのは、少数のテスト オーディオ ファイルだけです。 以下のリンクに従って、カスタム音声テキスト変換エクスペリエンスの作成を開始しましょう。
services: cognitive-services
author: trevorbye
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 02/17/2020
ms.author: trbye
ms.openlocfilehash: b9ac473c3864b4be06c978e7d6a3555bb4550d2a
ms.sourcegitcommit: 269da970ef8d6fab1e0a5c1a781e4e550ffd2c55
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/10/2020
ms.locfileid: "88056841"
---
# <a name="what-is-custom-speech"></a>Custom Speech とは

[Custom Speech](https://aka.ms/customspeech) は、ご利用のアプリケーション、ツール、製品に使用する Microsoft の音声テキスト変換の正確性を評価して改善することのできる一連のオンライン ツールです。 作業を始めるにあたって必要なのは、少数のテスト オーディオ ファイルだけです。 以下のリンクに従って、カスタム音声テキスト変換エクスペリエンスの作成を開始しましょう。

## <a name="whats-in-custom-speech"></a>Custom Speech の内容

Custom Speech で何らかの操作を行うには、Azure アカウントと音声サービスのサブスクリプションが必要です。 アカウントの取得後、データの準備、モデルのトレーニングとテスト、認識品質の調査、正確性の評価を行い、最後にそのカスタム音声テキスト変換モデルをデプロイして使用することができます。

この図は、[Custom Speech ポータル](https://aka.ms/customspeech)を構成する諸要素を示しています。 それぞれのステップの詳細については、下のリンクを使用してください。

![Custom Speech ポータルを構成するさまざまな要素を示した図。](./media/custom-speech/custom-speech-overview.png)

1. [サブスクライブしてプロジェクトを作成する](#set-up-your-azure-account) - Azure アカウントを作成し、音声サービスをサブスクライブします。 この統合されたサブスクリプションにより、音声テキスト変換、テキスト読み上げ、音声翻訳、および [Custom Speech ポータル](https://speech.microsoft.com/customspeech)にアクセスできるようになります。 その後、音声サービス サブスクリプションを使用して、初めての Custom Speech プロジェクトを作成します。

2. [テスト データをアップロードする](how-to-custom-speech-test-data.md) - テスト データ (オーディオ ファイル) をアップロードして、ご利用のアプリケーション、ツール、製品に使用する Microsoft の音声テキスト変換プランを評価します。

3. [認識品質を調査する](how-to-custom-speech-inspect-data.md) - [Custom Speech ポータル](https://speech.microsoft.com/customspeech)を使用して、アップロードした音声を再生し、テスト データの音声認識品質を調査します。 定量的な測定については、[データの調査](how-to-custom-speech-inspect-data.md)に関するページを参照してください。

4. [正確性を評価する](how-to-custom-speech-evaluate-data.md) - 音声テキスト変換モデルの正確性を評価します。 さらにトレーニングが必要であるかどうかは、[Custom Speech ポータル](https://speech.microsoft.com/customspeech)で得られる "*ワード エラー率*" を使用して判断できます。 正確性に問題がなければ、音声サービスの API を直接使用できます。 相対平均で 5% から 20% 正確性を高めたい場合、ポータルの **[トレーニング]** タブを使用して、追加のトレーニング データ (人間がラベル付けしたトランスクリプトと関連テキストなど) をアップロードします。

5. [精度を向上させる](how-to-custom-speech-improve-accuracy.md) - 追加のトレーニング データを戦略的に選択して、実際のシナリオに基づいて音声テキスト変換モデルの品質を高めます。

6. [モデルをトレーニングする](how-to-custom-speech-train-model.md) - ご自分の音声テスト データと一緒に、文書化されたトランスクリプト (10 から 1,000 時間) と関連テキスト (200 MB 未満) を入力することにより、音声テキスト変換モデルの正確性を高めます。 このデータが音声テキスト変換モデルのトレーニングに役立てられます。 トレーニング後、再テストを行い、その結果に問題がなければ、モデルをデプロイすることができます。

7. [モデルをデプロイする](how-to-custom-speech-deploy-model.md) - 音声テキスト変換モデルのカスタム エンドポイントを作成し、それを自分のアプリケーション、ツール、製品で使用します。

## <a name="set-up-your-azure-account"></a>Azure アカウントの設定

[Custom Speech ポータル](https://speech.microsoft.com/customspeech)を使用してカスタム モデルを作成するには、音声サービス サブスクリプションが必要です。 次の手順に従って、標準的な音声サービス サブスクリプションを作成してください:[Speech サブスクリプションの作成](get-started.md#new-resource)。

> [!NOTE]
> 必ず Standard (S0) サブスクリプションを作成してください。無料 (F0) サブスクリプションはサポートされません。

Azure アカウントと音声サービス サブスクリプションを作成したら、[Custom Speech ポータル](https://speech.microsoft.com/customspeech)にサインインして、ご利用のサブスクリプションを接続する必要があります。

1. Azure portal から音声サービス サブスクリプション キーを取得します。
2. [Custom Speech ポータル](https://aka.ms/custom-speech)にサインインします。
3. 作業に必要なサブスクリプションを選択し、音声プロジェクトを作成します。
4. ご利用のサブスクリプションに変更を加えたい場合は、上部のナビゲーションにある**歯車**アイコンを使用します。

## <a name="how-to-create-a-project"></a>プロジェクトを作成する方法

データ、モデル、テスト、エンドポイントなどのコンテンツは、[Custom Speech ポータル](https://speech.microsoft.com/customspeech)の **[プロジェクト]** にまとめられます。 プロジェクトは、ドメインと国および言語ごとに作成されます。 たとえば、米国の英語を使用するコール センターのプロジェクトを作成することが考えられます。

初めてのプロジェクトを作成するには、 **[音声テキスト変換/Custom Speech]** を選択し、 **[新しいプロジェクト]** をクリックします。 ウィザードの手順に従ってプロジェクトを作成します。 プロジェクトの作成後、4 つのタブが表示されます ( **[データ]** 、 **[テスト中]** 、 **[トレーニング]** 、 **[デプロイ]** )。 それぞれのタブの使い方については、「[次の手順](#next-steps)」に記載のリンクを使用してください。

> [!IMPORTANT]
> [Custom Speech ポータル](https://aka.ms/custom-speech)は最近更新されました。 CRIS.ai ポータルで、または API を使用して以前のデータ、モデル、テスト、および公開されたエンドポイントを作成した場合は、新しいポータルで新しいプロジェクトを作成して、これらの古いエンティティに接続する必要があります。

## <a name="next-steps"></a>次のステップ

* [データを準備してテストする](how-to-custom-speech-test-data.md)
* [データを検査する](how-to-custom-speech-inspect-data.md)
* [データを評価する](how-to-custom-speech-evaluate-data.md)
* [モデルをトレーニングする](how-to-custom-speech-train-model.md)
* [モデルをデプロイする](how-to-custom-speech-deploy-model.md)
