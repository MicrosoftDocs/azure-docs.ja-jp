---
title: エンコードの移行ガイダンス
description: この記事では、Azure Media Services v2 から v3 への移行に役立つエンコードのシナリオ ベースのガイダンスを示します。
services: media-services
author: IngridAtMicrosoft
manager: femila
ms.service: media-services
ms.topic: conceptual
ms.workload: media
ms.date: 03/17/2021
ms.author: inhenkel
ms.openlocfilehash: 915fdcb059d9e7bf9e1853040b90b82a0457652e
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "104598407"
---
# <a name="encoding-scenario-based-migration-guidance"></a>エンコードのシナリオ ベースの移行ガイダンス

![移行ガイドのロゴ](./media/migration-guide/azure-media-services-logo-migration-guide.svg)

<hr color="#5ea0ef" size="10">

![移行の手順 2](./media/migration-guide/steps-4.svg)

この記事では、Azure Media Services v2 から v3 への移行に役立つエンコードのシナリオ ベースのガイダンスを示します。

## <a name="prerequisites"></a>前提条件

エンコード ワークフローの変更を開始する前に、ストレージが管理される方法の違いを理解しておくことをお勧めします。  AMS V3 では、Azure Storage API は、Media Services アカウントに関連付けられているストレージ アカウントを管理するために使用されます。

> [!NOTE]
> v2 で作成されたジョブやタスクは、変換に関連付けられていないため v3 では表示されません。 v3 の変換およびジョブに切り替えることをお勧めします。

## <a name="encoding-workflow-comparison"></a>エンコード ワークフローの比較

V2 と V3 のエンコード ワークフローを視覚的に比較するために、次のフローチャートを数分で調べてみましょう。

### <a name="v2-encoding-workflow"></a>V2 のエンコード ワークフロー

次の画像をクリックすると、より大きなバージョンが表示されます。

[![V2 のエンコード ワークフロー](./media/migration-guide/V2-pretty.svg) ](./media/migration-guide/V2-pretty.svg#lightbox)

1. セットアップ
    1. 資産を作成するか、または既存の資産を使用します。 新しい資産を使用している場合は、コンテンツをその資産にアップロードします。 既存の資産を使用している場合は、その資産内に既に存在するファイルをエンコードする必要があります。
    2. 次の項目の値を取得します。
        - メディア プロセッサ ID またはオブジェクト
        - 使用するエンコーダーのエンコーダー文字列 (名前)
        - 新しい資産の資産 ID、または既存の資産の資産 ID
    3. 監視のために、ジョブまたはタスク レベルの通知サブスクリプションか、SDK イベント ハンドラーのどちらかを作成します。
2. 1 つまたは複数のタスクを含むジョブを作成します。 各タスクには、上の項目と次の項目が含まれている必要があります。
    - 出力資産を作成する必要のあるディレクティブ。  この出力資産は、システムによって作成されます。
    - 出力資産の省略可能な名前
3. ジョブを送信します。
4. ジョブを監視します。

### <a name="v3-encoding-workflow"></a>V3 のエンコード ワークフロー

[![V3 のエンコード ワークフロー](./media/migration-guide/V3-pretty.svg)](./media/migration-guide/V3-pretty.svg#lightbox)

1. 設定
    1. 資産を作成するか、または既存の資産を使用します。 新しい資産を使用している場合は、コンテンツをその資産にアップロードします。 既存の資産を使用している場合は、その資産内に既に存在するファイルをエンコードする必要があります。 "*その資産にさらにコンテンツをアップロードすることはやめてください。* "
    1. 出力アセットを作成する。  この出力資産は、エンコードされたファイルや入力および出力メタデータが格納される場所です。
    1. 次の変換の値を取得します。
        - Standard Encoder プリセット
        - AMS リソース グループ
        - AMS アカウント名
    1. 変換を作成するか、または既存の変換を使用します。  変換は再利用可能です。 ジョブを送信するたびに新しい変換を作成する必要はありません。
1. ジョブの作成
    1. ジョブについて、次の項目の値を取得します。
        - 変換名
        - 資産の SAS URL のベース URI、ファイル共有の HTTP ソース パス、またはファイルのローカル パス。 `JobInputAsset` では、入力として資産名を使用することもできます。
        - ファイル名
        - 出力資産
        - リソース グループ
        - AMS アカウント名  
1. ジョブを監視するために [Event Grid](monitoring/monitor-events-portal-how-to.md) を使用します。
1. ジョブを送信します。

## <a name="custom-presets-from-v2-to-v3-encoding"></a>V2 から V3 エンコードへのカスタム プリセット

V2 コードでカスタム プリセットを使用して Standard Encoder を呼び出した場合は、ジョブを送信する前に、まず、カスタム Standard Encoder プリセットを使用して新しい変換を作成する必要があります。

カスタム プリセットは現在 JSON であり、XML ベースではなくなりました。 [Transform Open API (Swagger)](https://github.com/Azure/azure-rest-api-specs/blob/master/specification/mediaservices/resource-manager/Microsoft.Media/stable/2020-05-01/examples/transforms-create.json) のドキュメントで定義されているカスタム プリセット スキーマに従って、JSON でプリセットを再作成します。

## <a name="input-and-output-metadata-files-from-an-encoding-job"></a>エンコード ジョブからの入力および出力メタデータ ファイル

v2 では、XML の入力と出力のメタデータ ファイルは、エンコード ジョブの結果として生成されます。 v3 では、メタデータの形式が XML から JSON に変更されました。 メタデータの詳細については、「[入力メタデータ](input-metadata-schema.md)」および「[出力メタデータ](output-metadata-schema.md)」を参照してください。

## <a name="premium-encoder-to-v3-standard-encoder-or-partner-based-solutions"></a>Premium Encoder から v3 Standard Encoder またはパートナー ベースのソリューションへ

v2 API では、Premium Encoder がサポートされなくなりました。 以前に HEVC エンコードにワークフロー ベースの Premium Encoder を使用していた場合は、HEVC エンコードのサポートを含む新しい v3 [Standard Encoder](media-encoder-standard-formats.md) に移行する必要があります。

Premium Encoder の高度なワークフロー機能が必要な場合は、[Imagine Communications](https://imaginecommunications.com)、[Telestream](https://www.telestream.net)、または [Bitmovin](https://bitmovin.com) からの Azure の高度なエンコード パートナー ソリューションの使用を開始することをお勧めします。

## <a name="jobs-with-inputs-that-are-on-https-hosted-urls"></a>HTTPS でホストされた URL に入力が存在するジョブ

現在は、[HTTP(S) のジョブ入力サポート](job-input-from-http-how-to.md)を使用して、Azure ストレージ、ローカル、または外部 Web サーバーに格納されているファイルから V3 でジョブを送信できます。

以前に、ジョブを送信する前にワークフローを使用して Azure BLOB ファイルから空の資産にファイルをコピーした場合は、Azure BLOB ストレージ内のファイルの SAS URL を直接ジョブに渡すことによってそのワークフローを簡略化できる可能性があります。

## <a name="indexer-v1-audio-transcription-to-the-new-audioanalyzer-basic-mode"></a>新しい AudioAnalyzer "基本モード" へのインデクサー v1 の音声の文字起こし

v2 API でインデクサー v1 プロセッサを使用している顧客の場合は、ジョブを送信する前に、[基本モード](how-to-create-basic-audio-transform.md)で新しい `AudioAnalyzer` を呼び出す変換を作成する必要があります。

## <a name="encoding-transforms-and-jobs-concepts-tutorials-and-how-to-guides"></a>エンコード、変換、ジョブの概念、チュートリアル、ハウツー ガイド

### <a name="concepts"></a>概念

- [Media Services を使用したビデオとオーディオのエンコード](encoding-concept.md)
- [Standard Encoder の形式およびコーデック](media-encoder-standard-formats.md)
- [自動生成されたビットレート ラダーでエンコードする](autogen-bitrate-ladder.md)
- [コンテンツに対応したエンコードのプリセットを使用して、特定の解像度に最適なビットレートの値を検索する](content-aware-encoding.md)
- [メディア占有ユニット](concept-media-reserved-units.md)
- [入力メタデータ](input-metadata-schema.md)
- [出力メタデータ](output-metadata-schema.md)
- [Media Services v3 のダイナミック パッケージ: オーディオ コーデック](dynamic-packaging-overview.md#audio-codecs-supported-by-dynamic-packaging)

### <a name="tutorials"></a>チュートリアル

- [チュートリアル:リモート ファイルを URL に基づいてエンコードし、ビデオをストリーム配信する - .NET](stream-files-dotnet-quickstart.md)
- [チュートリアル:Media Services v3 を使用してビデオをアップロード、エンコード、ストリーム配信する](stream-files-tutorial-with-api.md)

### <a name="how-to-guides"></a>ハウツー ガイド

- [HTTPS URL からジョブの入力を作成する](job-input-from-http-how-to.md)
- [ローカル ファイルからジョブの入力を作成する](job-input-from-local-file-how-to.md)
- [基本的なオーディオ変換を作成する](how-to-create-basic-audio-transform.md)
- .NET の場合
  - [カスタム変換を使用してエンコードする方法 - .NET](customize-encoder-presets-how-to.md)
  - [Media Encoder Standard を使用してオーバーレイを作成する方法](how-to-create-overlay.md)
  - [.NET で Encoder Standard を使用してサムネイルを生成する方法](media-services-generate-thumbnails-dotnet.md)
- Azure CLI の場合
  - [カスタム変換を使用してエンコードする方法 - Azure CLI](custom-preset-cli-howto.md)
- REST の場合
  - [カスタム変換を使用してエンコードする方法 - REST](custom-preset-rest-howto.md)
  - [REST で Encoder Standard を使用してサムネイルを生成する方法](media-services-generate-thumbnails-rest.md)
- [Media Services を使用してエンコードを実行する際にビデオをサブクリップする - .NET](subclip-video-dotnet-howto.md)
- [Media Services を使用してエンコードを実行する際にビデオをサブクリップする - REST](subclip-video-rest-howto.md)

## <a name="samples"></a>サンプル

[V2 と V3 のコードをコード サンプルで比較する](migrate-v-2-v-3-migration-samples.md)こともできます。

## <a name="next-steps"></a>次のステップ

[!INCLUDE [migration guide next steps](./includes/migration-guide-next-steps.md)]
