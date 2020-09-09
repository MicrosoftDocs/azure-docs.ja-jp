---
title: カスタム音声を使用して合成を改善する - 音声サービス
titleSuffix: Azure Cognitive Services
description: Custom Voice は、ブランド用に認識性の高い固有の音声を作成できる、一連のオンライン ツールです。 作業を始めるにあたって必要なのは、少数のオーディオ ファイルと関連する文字起こしだけです。 以下のリンクに従って、カスタム音声テキスト変換エクスペリエンスの作成を開始しましょう。
services: cognitive-services
author: trevorbye
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 02/17/2020
ms.author: trbye
ms.openlocfilehash: 272367d5311952b45c73febe0e05b1ec2d225261
ms.sourcegitcommit: 269da970ef8d6fab1e0a5c1a781e4e550ffd2c55
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/10/2020
ms.locfileid: "88056688"
---
# <a name="get-started-with-custom-voice"></a>Custom Voice の概要

[Custom Voice](https://aka.ms/customvoice) は、ブランド用に認識性の高い固有の音声を作成できる、一連のオンライン ツールです。 作業を始めるにあたって必要なのは、少数のオーディオ ファイルと関連する文字起こしだけです。 以下のリンクに従って、カスタム テキスト読み上げエクスペリエンスの作成を開始しましょう。

## <a name="whats-in-custom-voice"></a>Custom Voice の内容

Custom Voice を開始する前に、Azure アカウントと音声サービスのサブスクリプションが必要になります。 アカウントを作成したら、データの準備、モデルのトレーニングおよびテスト、音声品質の評価、そしてカスタム音声モデルのデプロイを行えます。

下の図では、[Custom Voice ポータル](https://aka.ms/customvoice)を使用してカスタム音声モデルを作成する手順を示しています。 リンクを使用して、詳細を確認してください。

![Custom Voice のアーキテクチャ図](media/custom-voice/custom-voice-diagram.png)

1. [サブスクライブしてプロジェクトを作成する](#set-up-your-azure-account) - Azure アカウントを作成し、音声サービスのサブスクリプションを作成します。 この一元化されたサブスクリプションを通じて、音声テキスト変換、テキスト読み上げ、音声翻訳、Custom Voice ポータルにアクセスすることができます。 次に、音声サービス サブスクリプションを使用して、最初の Custom Voice プロジェクトを作成します。

2. [データをアップロードする](how-to-custom-voice-create-voice.md#upload-your-datasets) - Custom Voice ポータルまたは Custom Voice API を使用して、データ (オーディオとテキスト) をアップロードします。 ポータルで、発音スコアと信号対雑音比を調査および評価できます。 詳細については、[Custom Voice 用にデータを準備する方法](how-to-custom-voice-prepare-data.md)に関する記事を参照してください。

3. [モデルをトレーニングする](how-to-custom-voice-create-voice.md#build-your-custom-voice-model) - 自分のデータを使用して、カスタム テキスト読み上げ音声モデルを作成します。 モデルはさまざまな言語でトレーニングできます。 トレーニング後にモデルをテストし、結果に満足したら、モデルをデプロイできます。

4. [モデルをデプロイする](how-to-custom-voice-create-voice.md#create-and-use-a-custom-voice-endpoint) - 自分のテキスト読み上げ音声モデルのカスタム エンドポイントを作成して、自分の製品、ツール、アプリケーションの音声合成にそれを使用します。

## <a name="custom-neural-voices"></a>カスタム ニューラル音声

ニューラル音声のカスタマイズ機能は、現在、パブリック プレビュー段階にあり、一部のお客様に限定されています。 使用を開始するには、この[申請フォーム](https://go.microsoft.com/fwlink/?linkid=2108737)に入力してください。

> [!NOTE]
> 信頼のおける AI の設計に対するマイクロソフトの取り組みの一環として、個人および社会の権利を保護し、人とコンピューターとの透明性のあるやり取りを促進することを目的としています。 このため、カスタム ニューラル音声は、すべてのお客様が一般に利用できるわけではありません。 このテクノロジにアクセスできるのは、アプリケーションの審査を受け、弊社の倫理原則に沿ってアプリケーションを使用することを確約した場合のみです。 アプリケーションの制限プロセスの詳細については、[こちら](https://aka.ms/custom-neural-gating-overview)を参照してください。

## <a name="set-up-your-azure-account"></a>Azure アカウントの設定

Custom Speech ポータルを使用してカスタム モデルを作成する前に、音声サービス サブスクリプションが必要です。 Azure で音声サービス サブスクリプションを作成するには、以下の手順に従います。 Azure アカウントがない場合、新しいものにサインアップできます。  

Azure アカウントと音声サービス サブスクリプションを作成したら、Custom Voice ポータルにサインインして、自分のサブスクリプションを接続する必要があります。

1. Azure portal から音声サービス サブスクリプション キーを取得します。
2. [Custom Voice ポータル](https://aka.ms/custom-voice)にサインインします。
3. 自分のサブスクリプションを選択し、音声プロジェクトを作成します。
4. 別の Speech サブスクリプションに切り替えたい場合は、上部のナビゲーションにある歯車アイコンを使用します。

> [!NOTE]
> サービスを使用する前に、Azure で F0 または S0 のキーを作成しておく必要があります。

## <a name="how-to-create-a-project"></a>プロジェクトを作成する方法

データ、モデル、テスト、エンドポイントなどのコンテンツは、Custom Voice ポータルの **[プロジェクト]** にまとめられます。 各プロジェクトは、国と言語、および作成したい音声の性別に固有です。 たとえば、米国の英語 (en-US) を使用するコール センターのチャット ボット用に、女性の音声のプロジェクトを作成できます。

初めてのプロジェクトを作成するには、**[Text-to-Speech/Custom Voice]\(テキスト読み上げ/Custom Voice\)** タブを選択して、**[新しいプロジェクト]** をクリックします。 ウィザードの手順に従ってプロジェクトを作成します。 プロジェクトの作成後、4 つのタブが表示されます: **[データ]** 、 **[トレーニング]** 、 **[テスト中]** 、 **[デプロイ]** 。 それぞれのタブの使い方については、「[次の手順](#next-steps)」に記載のリンクを使用してください。

> [!IMPORTANT]
> [カスタム音声ポータル](https://aka.ms/custom-voice)は最近更新されました。 CRIS.ai ポータルで、または API を使用して以前のデータ、モデル、テスト、および公開されたエンドポイントを作成した場合は、新しいポータルで新しいプロジェクトを作成して、これらの古いエンティティに接続する必要があります。

## <a name="next-steps"></a>次のステップ

- [Custom Voice のデータを用意する](how-to-custom-voice-prepare-data.md)
- [Custom Voice を作成する](how-to-custom-voice-create-voice.md)
- [ガイド:音声サンプルを録音する](record-custom-voice-samples.md)
