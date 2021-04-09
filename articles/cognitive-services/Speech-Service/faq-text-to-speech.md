---
title: テキスト読み上げについてよく寄せられる質問
titleSuffix: Azure Cognitive Services
description: テキスト読み上げサービスについてよく寄せられる質問の回答を紹介します。
services: cognitive-services
author: PanosPeriorellis
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 08/20/2020
ms.author: panosper
ms.openlocfilehash: 67284225299641a71050342056ad7ca91bf39740
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/30/2021
ms.locfileid: "96353716"
---
# <a name="text-to-speech-frequently-asked-questions"></a>テキスト読み上げについてよく寄せられる質問

疑問点への回答がこのよく寄せられる質問で見つからない場合は、[その他のサポート オプション](../cognitive-services-support-options.md?context=%2fazure%2fcognitive-services%2fspeech-service%2fcontext%2fcontext%253fcontext%253d%2fazure%2fcognitive-services%2fspeech-service%2fcontext%2fcontext)を確認してください。

## <a name="general"></a>全般

**Q:標準音声モデルとカスタム音声モデルの違いは何ですか。**

**A**: (_音声フォント_ とも呼ばれる) 標準音声モデルは、Microsoft が所有するデータを使用してトレーニングされ、既にクラウドにデプロイされています。 カスタム音声モデルを使用する場合は、平均的なモデルを適用し、話者の音声スタイルの音色や表現方法を転送します。またはユーザーが準備したトレーニング データを使用して完全に新しいモデルをトレーニングします。 今日、ますます多くの顧客が、独自のブランド化された音声をボットに備えたいと考えています。 カスタム音声の構築プラットフォームは、そのオプションに適切な選択肢です。

**Q:標準音声モデルを使用する場合は、どこから開始すればよいですか。**

**A**: 45 以上の言語の 80 を超える標準音声モデルが HTTP 要求経由で使用できます。 最初に、[サブスクリプション キー](./overview.md#try-the-speech-service-for-free)を取得します。 展開済みの音声モデルに REST 呼び出しを実行するには、「[REST API](./overview.md#reference-docs)」を参照してください。

**Q:カスタム音声モデルを使用する場合、API は標準音声と同じですか。**

**A**: カスタム音声モデルを作成し、デプロイした場合、使用しているモデルに固有のエンドポイントを得られます。 その音声を使用してアプリで話すには、HTTP 要求でそのエンドポイントを指定する必要があります。 カスタム エンドポイントにも、テキスト読み上げサービスの REST API で使用可能な同じ機能が用意されています。 「[カスタム エンドポイントを作成して使用する](./how-to-custom-voice-create-voice.md#create-and-use-a-custom-voice-endpoint)」方法を参照してください。

**Q:カスタム音声モデルの作成には、自分でトレーニング データを準備する必要がありますか。**

**A**: はい。カスタム音声モデルの場合、ご自分でトレーニング データを用意する必要があります。

カスタマイズされた音声モデルを作成するには、音声データのコレクションが必要です。 このコレクションは、一連の音声録音のオーディオ ファイルと、文字起こしされた各オーディオ ファイルのテキスト ファイルで構成されます。 デジタル音声の結果は、トレーニング データの品質に大きく依存します。 良質のテキスト読み上げ音声を生成するには、高品質のスタンド マイクを使用して静かな部屋で録音を行うことが重要です。 一定の音量、話す速さ、ピッチ、さらには話の表現方法における一貫性までもが、優れたデジタル音声の構築に不可欠です。 音声は、録音スタジオで録音することを強くお勧めします。

現時点では、オンライン録音のサポートは提供しておらず、推奨している録音スタジオもありません。 形式に関する要件については、[録音および文字起こしを準備する方法](./how-to-custom-voice-create-voice.md)を参照してください。

**Q:カスタム音声トレーニングのために音声データを録音するには、どのようなスクリプトを使用すればよいですか。**

**A**: 音声録音のスクリプトには制限はありません。 独自のスクリプトを使用して音声を録音できます。 音声データに十分な音声カバレッジがあることだけは確認してください。 カスタム音声は、50 個程度の少量の異なる文 (約 3 から 5 分の音声) の音声データでトレーニングを開始します。 提供するデータが多いほど、音声はより自然になります。 2000 を超える文 (約 3 - 4 時間の音声) の録音の提供で、完全な音声フォントのトレーニングを開始できます。 高品質の完全な音声には、6000 を超える文 (約 8 - 10 時間の音声) の録音を準備する必要があります。

録音用のスクリプトの準備に役立つ追加のサービスを提供しています。 詳細については、[Custom Voice カスタマー サポート](mailto:customvoice@microsoft.com?subject=Inquiries%20about%20scripts%20generation%20for%20Custom%20Voice%20creation)に問い合わせてください。

**Q:既定値またはポータルで提供されているものより高いコンカレンシーが必要な場合はどうすればよいですか。**

**A**: モデルは同時要求を 20 ずつ処理するようスケールアップできます。 より高いスケーリングの詳細については、[Custom Voice のカスタマー サポート](mailto:customvoice@microsoft.com?subject=Inquiries%20about%20scripts%20generation%20for%20Custom%20Voice%20creation)に問い合わせてください。

**Q:自分のモデルをダウンロードしてローカルで実行できますか。**

**A**: モデルをダウンロードしてローカルで実行することはできません。

**Q:ユーザーの要求は調整されますか。**

**A**: 「[音声サービスのクォータと制限](speech-services-quotas-and-limits.md)」を参照してください。

## <a name="next-steps"></a>次のステップ

- [トラブルシューティング](troubleshooting.md)
- [リリース ノート](releasenotes.md)