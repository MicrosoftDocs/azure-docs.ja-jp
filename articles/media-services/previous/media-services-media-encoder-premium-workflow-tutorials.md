---
title: 高度なメディア エンコーダー プレミアム ワークフローのチュートリアル
description: このドキュメントでは、メディア エンコーダー プレミアム ワークフローを使って高度な処理を行う方法をわかりやすく解説します。ワークフロー デザイナーを使って複雑なワークフローを作成する方法についても取り上げています。
services: media-services
documentationcenter: ''
author: xstof
manager: femila
editor: ''
ms.assetid: 1ba52865-b4a8-4ca0-ac96-920d55b9d15b
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/18/2019
ms.author: christoc;xpouyat;juliako
ms.openlocfilehash: d227e3618c138e6661cc4be7caa2b9a3ba1af3f1
ms.sourcegitcommit: 1c2cf60ff7da5e1e01952ed18ea9a85ba333774c
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/12/2019
ms.locfileid: "59523836"
---
# <a name="advanced-media-encoder-premium-workflow-tutorials"></a>高度なメディア エンコーダー プレミアム ワークフローのチュートリアル
## <a name="overview"></a>概要
このドキュメントには、**ワークフロー デザイナー**でワークフローをカスタマイズする方法を示したチュートリアルが含まれています。 実際のワークフロー ファイルは、[こちら](https://github.com/Azure/azure-media-services-samples/tree/master/Encoding%20Presets/VoD/MediaEncoderPremiumWorkfows/PremiumEncoderWorkflowSamples)から入手できます。  

## <a name="toc"></a>目次
次のトピックについて説明します。

* [MXF をシングル ビットレートの MP4 にエンコードする](media-services-media-encoder-premium-workflow-tutorials.md#MXF_to_MP4)
  * [新しいワークフローの開始](media-services-media-encoder-premium-workflow-tutorials.md#MXF_to_MP4_start_new)
  * [メディア ファイル入力の使用](media-services-media-encoder-premium-workflow-tutorials.md#MXF_to_MP4_with_file_input)
  * [メディア ストリームの調査](media-services-media-encoder-premium-workflow-tutorials.md#MXF_to_MP4_streams)
  * [.MP4 ファイル生成用のビデオ エンコーダーを追加する](media-services-media-encoder-premium-workflow-tutorials.md#MXF_to_MP4_file_generation)
  * [オーディオ ストリームのエンコード](media-services-media-encoder-premium-workflow-tutorials.md#MXF_to_MP4_audio)
  * [オーディオ ストリームとビデオ ストリームを MP4 コンテナーに多重化する](media-services-media-encoder-premium-workflow-tutorials.md#MXF_to_MP4_audio_and_fideo)
  * [MP4 ファイルの作成](media-services-media-encoder-premium-workflow-tutorials.md#MXF_to_MP4_writing_mp4)
  * [出力ファイルから Media Services 資産を作成する](media-services-media-encoder-premium-workflow-tutorials.md#MXF_to_MP4_asset_from_output)
  * [完成したワークフローをローカルでテストする](media-services-media-encoder-premium-workflow-tutorials.md#MXF_to_MP4_test)
* [MXF をマルチビットレートの MP4 にエンコードする (動的パッケージング対応)](media-services-media-encoder-premium-workflow-tutorials.md#MXF_to_MP4_with_dyn_packaging)
  * [MP4 出力の追加](media-services-media-encoder-premium-workflow-tutorials.md#MXF_to_MP4_with_dyn_packaging_more_outputs)
  * [ファイル出力名の構成](media-services-media-encoder-premium-workflow-tutorials.md#MXF_to_MP4_with_dyn_packaging_conf_output_names)
  * [独立したオーディオ トラックの追加](media-services-media-encoder-premium-workflow-tutorials.md#MXF_to_MP4_with_dyn_packaging_audio_tracks)
  * ["ISM" SMIL ファイルの追加](media-services-media-encoder-premium-workflow-tutorials.md#MXF_to_MP4_with_dyn_packaging_ism_file)
* [MXF をマルチビットレートの MP4 にエンコードする (設計の改良)](media-services-media-encoder-premium-workflow-tutorials.md#MXF_to__multibitrate_MP4)
  * 改良するワークフローの全体像
  * [ファイルの名前付け規則](media-services-media-encoder-premium-workflow-tutorials.md#MXF_to__multibitrate_MP4_file_naming)
  * [コンポーネントのプロパティをワークフローのルートに公開する](media-services-media-encoder-premium-workflow-tutorials.md#MXF_to__multibitrate_MP4_publishing)
  * [生成される出力ファイル名に公開プロパティの値を使用する](media-services-media-encoder-premium-workflow-tutorials.md#MXF_to__multibitrate_MP4_output_files)
* [マルチビットレートの MP4 出力にサムネイルを追加する](media-services-media-encoder-premium-workflow-tutorials.md#thumbnails_to__multibitrate_MP4)
  * サムネイルの追加先となるワークフローの全体像
  * [JPG エンコードの追加](media-services-media-encoder-premium-workflow-tutorials.md#thumbnails_to__multibitrate_MP4__with_jpg)
  * [色空間の変換処理](media-services-media-encoder-premium-workflow-tutorials.md#thumbnails_to__multibitrate_MP4_color_space)
  * [サムネイルの作成](media-services-media-encoder-premium-workflow-tutorials.md#thumbnails_to__multibitrate_MP4_writing_thumbnails)
  * [ワークフローで発生したエラーの検出](media-services-media-encoder-premium-workflow-tutorials.md#thumbnails_to__multibitrate_MP4_errors)
  * [完成したワークフロー](media-services-media-encoder-premium-workflow-tutorials.md#thumbnails_to__multibitrate_MP4_finish)
* [マルチビットレートの MP4 出力を時間に基づいてトリミングする](media-services-media-encoder-premium-workflow-tutorials.md#time_based_trim)
  * [トリミングの追加先となるワークフローの全体像](media-services-media-encoder-premium-workflow-tutorials.md#time_based_trim_start)
  * [ストリーム トリマーの使用](media-services-media-encoder-premium-workflow-tutorials.md#time_based_trim_use_stream_trimmer)
  * [完成したワークフロー](media-services-media-encoder-premium-workflow-tutorials.md#time_based_trim_finish)
* [スクリプト コンポーネントの概要](media-services-media-encoder-premium-workflow-tutorials.md#scripting)
  * [ワークフロー内スクリプトの作成: hello world](media-services-media-encoder-premium-workflow-tutorials.md#scripting_hello_world)
* [マルチビットレートの MP4 出力をフレームに基づいてトリミングする](media-services-media-encoder-premium-workflow-tutorials.md#frame_based_trim)
  * [トリミングの追加先となるワークフローの全体像](media-services-media-encoder-premium-workflow-tutorials.md#frame_based_trim_start)
  * [クリップ リスト XML の使用](media-services-media-encoder-premium-workflow-tutorials.md#frame_based_trim_clip_list)
  * [スクリプト コンポーネントからのクリップ リストの変更](media-services-media-encoder-premium-workflow-tutorials.md#frame_based_trim_modify_clip_list)
  * [トリミングが不要な場合に備えて ClippingEnabled プロパティを追加する](media-services-media-encoder-premium-workflow-tutorials.md#frame_based_trim_clippingenabled_prop)

## <a id="MXF_to_MP4"></a>MXF をシングル ビットレートの MP4 にエンコードする
このセクションでは、入力ファイルとなる .MXF から、オーディオを AAC-HE でエンコードしたシングル ビットレートの .MP4 ファイルを作成する方法を示します。

### <a id="MXF_to_MP4_start_new"></a>新しいワークフローの開始
ワークフロー デザイナーを開いて [ファイル] > [新しいワークスペース] > [トランスコード設計] の順に選択します

新しいワークフローには 3 つの要素があります。

* プライマリ ソース ファイル
* クリップ リスト XML
* 出力ファイル/資産  

![新しいエンコード ワークフロー](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-transcode-blueprint.png)

*新しいエンコード ワークフロー*

### <a id="MXF_to_MP4_with_file_input"></a>メディア ファイル入力の使用
入力メディア ファイルを受け取るためにはまず、メディア ファイル入力コンポーネントを追加する必要があります。 ワークフローにコンポーネントを追加するには、[リポジトリの検索] ボックスでコンポーネントを探し、必要な項目をデザイナー ウィンドウにドラッグします。 メディア ファイル入力に対してこの操作を繰り返し、プライマリ ソース ファイル コンポーネントを、メディア ファイル入力からファイル名入力ピンに接続します。

![接続されたメディア ファイル入力](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-file-input.png)

*接続されたメディア ファイル入力*

最初に、カスタム ワークフローの設計に使用する適切なサンプル ファイルを特定します。 そのためには、デザイナー ウィンドウの背景をクリックし、右側のプロパティ ウィンドウから [プライマリ ソース ファイル] プロパティを探します。 フォルダー アイコンをクリックし、ワークフローのテストに必要なファイルを選択します。 メディア ファイル入力コンポーネントはそのファイルを調査し、調査したサンプル ファイルの詳細が、その出力ピンに反映されるように設定します。

![設定されたメディア ファイル入力](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-populated-media-file-input.png)

*設定されたメディア ファイル入力*

入力は設定されました。次は、出力エンコードの設定を指定します。 プライマリ ソース ファイルを構成したときと同じように、今度は [出力フォルダー変数] プロパティを次のように構成します。

![入力と出力のプロパティを構成したところ](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-configured-io-properties.png)

*入力と出力のプロパティを構成したところ*

### <a id="MXF_to_MP4_streams"></a>メディア ストリームの調査
ワークフローを流れるストリームの内容を詳しく調べたい場合があります。 ワークフローの途中にあるいずれかのコンポーネントの出力ピンまたは入力ピンをクリックすれば、そのポイントのストリームを調査することができます。 ここでは、メディア ファイル入力の非圧縮ビデオ出力ピンをクリックしてみます。 出力されるビデオを詳細に示したダイアログが表示されます。

![[非圧縮ビデオ] の出力ピンを調査](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-inspecting-uncompressed-video-output.png)

*[非圧縮ビデオ] の出力ピンを調査*

このケースでは、解像度 1920 x 1080、1 秒あたりのフレーム数が 24、サンプリング方式が 4:2:2 の約 2 分のビデオを示しています。

### <a id="MXF_to_MP4_file_generation"></a>.MP4 ファイル生成用のビデオ エンコーダーを追加する
メディア ファイル入力では、1 つの非圧縮ビデオと、複数の非圧縮オーディオ出力ピンを利用できます。 入力されたビデオをエンコードするには、(このケースでは .MP4 ファイルを生成するために) エンコード コンポーネントをワークフローに追加する必要があります。

ビデオ ストリームを H.264 にエンコードするには、デザイナー画面に AVC ビデオ エンコーダー コンポーネントを追加します。 このコンポーネントは、非圧縮ビデオ ストリームを入力として受け取り、その出力ピンで AVC 圧縮ビデオ ストリームを生成します。

![未接続の AVC エンコーダー](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-unconnected-avc-encoder.png)

*未接続の AVC エンコーダー*

実際に行われるエンコードは、そのプロパティによって決まります。 重要な設定をいくつか見てみましょう。

* 出力の幅 と 出力の高さ: エンコード後のビデオの解像度を決定します。 ここでは 640x360 が適切です。
* [フレーム レート]: [パススルー] に設定した場合、ソース フレーム レートがそのまま採用されます。この設定はオーバーライドすることもできます。 そのようなフレームレート変換には動き補正が適用されません。
* プロファイルとレベル: AVC のプロファイルとレベルを決定します。 各種のレベルとプロファイルについてさらに詳しい情報が必要な場合は、AVC ビデオ エンコーダー コンポーネントで疑問符アイコンをクリックしてください。個々のレベルについて詳しく説明したヘルプ ページが表示されます。 この例では、メイン プロファイルとレベル 3.2 (既定値) を使用します。
* [レート コントロール モード] と [ビットレート (kbps)]: このシナリオでは、1200 kbps での固定ビットレート (CBR) 出力を選択します
* ビデオ形式: H.264 ストリームに書き込まれる VUI (Video Usability Information) に関する情報を提供します (画質を高めるためにデコーダーによって使用されるサイド情報であり、正確なデコードに必須というわけではありません)。
* NTSC (主に米国と日本で使用、30 fps)
* PAL (主にヨーロッパで使用、25 fps)
* GOP サイズ モード: ここでは、キー インターバルを 2 秒とするクローズド GOP の固定 GOP サイズを設定します。 2 秒を設定することで、Azure Media Services によって提供される動的パッケージングとの互換性が確保されます。

AVC エンコーダーにデータを入力するには、メディア ファイル入力コンポーネントの非圧縮ビデオ出力ピンを、AVC エンコーダーの非圧縮ビデオ入力ピンに接続する必要があります。

![Connected AVC Encoder](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-connected-avc-encoder.png)

*AVC メイン エンコーダーを接続したところ*

### <a id="MXF_to_MP4_audio"></a>オーディオ ストリームのエンコード
この時点で、まだ圧縮されていない元のオーディオ ストリームを圧縮する必要があります。 オーディオ ストリームを圧縮するために、AAC エンコーダー (ドルビー) コンポーネントをワークフローに追加します。

![未接続の AVC エンコーダー](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-unconnected-aac-encoder.png)

*未接続の AAC エンコーダー*

ここで 1 つ困ったことが生じました。AAC エンコーダーには、非圧縮オーディオ入力ピンが 1 つしか存在しません。一方、メディア ファイル入力には多くの場合、2 系統 (左オーディオ チャンネル用と右オーディオ チャンネル用) の非圧縮オーディオ ストリームが存在します  (サラウンド サウンドを使用している場合は、これが 6 チャンネルになります)。メディア ファイル入力ソースからのオーディオを AAC オーディオ エンコーダーに直接接続することはできません。 AAC コンポーネントが入力として想定しているのは、いわゆる "インターリーブ" されたオーディオ ストリーム (左右のチャンネルが交互に配置された単一のストリーム) です。 どのオーディオ トラックがソース内のどの位置に存在するかがソース メディア ファイルからわかっていれば、左右のスピーカー位置を正しく割り当てて、そのようなインターリーブ オーディオ ストリームを生成することができます。

まず、必要なソース オーディオ チャンネルからインターリーブ ストリームを生成する必要があります。 この処理を自動的に行ってくれるのがオーディオ ストリーム インターリーバー コンポーネントです。 このコンポーネントをワークフローに追加し、メディア ファイル入力からのオーディオ出力をそこに接続します。

![オーディオ ストリーム インターリーバーを接続したところ](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-connected-audio-stream-interleaver.png)

*オーディオ ストリーム インターリーバーを接続したところ*

ここまででインターリーブしたオーディオ ストリームは得られましたが、左右のスピーカー位置の割り当て先をまだ指定していません。 これを指定するためにスピーカー ポジション アサイナーを利用します。

![スピーカー ポジション アサイナーの追加](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-adding-speaker-position-assigner.png)

*スピーカー ポジション アサイナーの追加*

エンコーダー プリセット フィルターを "カスタム" とし、チャンネル プリセットを "2.0 (L,R)" として、ステレオ入力ストリーム用にスピーカー ポジション アサイナーを構成します  (これで左スピーカー位置がチャンネル 1 に、右スピーカー位置がチャンネル 2 に割り当てられます)。

スピーカー ポジション アサイナーの出力を AAC エンコーダーの入力に接続します。 そのうえで、"2.0 (L,R)" チャンネル プリセットを処理するよう AAC エンコーダーに命令する必要があります。そうすることで、入力として処理する対象がステレオ オーディオであることを AAC エンコーダーが認識できます。

### <a id="MXF_to_MP4_audio_and_fideo"></a>オーディオ ストリームとビデオ ストリームを MP4 コンテナーに多重化する
AVC でエンコードしたビデオ ストリームと AAC でエンコードしたオーディオ ストリームを 1 つの .MP4 コンテナーに取り込むことができます。 複数の異なるストリームを 1 つのストリームにミキシングする処理を "多重化" (または "マルチプレキシング") といいます。 ここでは、オーディオ ストリームとビデオ ストリームをインターリーブして 1 つの .MP4 パッケージに統合します。 .MP4 コンテナーに対してこの作業を行うコンポーネントは、ISO MPEG-4 マルチプレクサーと呼ばれています。 このコンポーネントをデザイナー画面に追加し、その入力に AVC ビデオ エンコーダーと AAC エンコーダーの両方を接続します。

![MPEG4 マルチプレクサーを接続したところ](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-connected-mpeg4-multiplexer.png)

*MPEG4 マルチプレクサーを接続したところ*

### <a id="MXF_to_MP4_writing_mp4"></a>MP4 ファイルの作成
出力ファイルを作成するときは、ファイル出力コンポーネントを使用します。 このコンポーネントを ISO MPEG-4 マルチプレクサーの出力に接続することで、その出力をディスクに書き込むことができます。 そこで [コンテナー (MPEG-4)] の出力ピンを、ファイル出力コンポーネントの [書き込み] の入力ピンに接続します。

![ファイル出力を接続したところ](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-connected-file-output.png)

*ファイル出力を接続したところ*

使用されるファイル名は、File プロパティによって指定されます。 このプロパティを特定の値にハードコーディングすることもできますが、通常は式を使って設定します。

出力ファイル名のプロパティをワークフローで式から自動的に設定するには、ファイル名の横 (フォルダー アイコンの横) にあるボタンをクリックします。 ドロップダウン メニューから [式] を選択してください。 式エディターが起動します。 まずエディターの内容を消去してください。

![空の式エディター](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-empty-expression-editor.png)

*空の式エディター*

式エディターでは、変数と組み合わせて任意のリテラル値を入力できます。 変数はドル記号で始まります。 エディターで $ キーを押すとドロップダウン ボックスが表示され、利用可能な変数を選択できます。 ここでは、出力ディレクトリと入力ファイルのベース名に変数を使用しています。

    ${ROOT_outputWriteDirectory}\\${ROOT_sourceFileBaseName}.MP4

![式エディターへの入力](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-expression-editor.png)

*式エディターへの入力*

> [!NOTE]
> Azure で実行されるエンコード ジョブの出力ファイルを確認するには、式エディターで値を指定する必要があります。
>
>

[OK] をクリックして式を確定すると、File プロパティがこの時点でどのような値に解決されるのかをプロパティ ウィンドウでプレビューできます。

![出力ディレクトリをファイル式によって解決](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-file-expression-resolves-output-dir.png)

*出力ディレクトリをファイル式によって解決*

### <a id="MXF_to_MP4_asset_from_output"></a>出力ファイルから Media Services 資産を作成する
MP4 の出力ファイルを作成するところまでは完了しましたが、これで終わりではありません。このワークフローを実行した結果として Media Services から出力される資産にそのファイルが従属することを示す必要があります。 そのために使用するのが、ワークフロー キャンバス上の [出力ファイル/資産] ノードです。 このノードに入力されるすべてのファイルが、Azure Media Services によって生成される資産の一部となります。

ファイル出力コンポーネントを出力ファイル/資産コンポーネントに接続すればワークフローは完成です。

![完成したワークフロー](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-finished-workflow.png)

*完成したワークフロー*

### <a id="MXF_to_MP4_test"></a>完成したワークフローをローカルでテストする
ワークフローをローカルでテストするには、画面上部にあるツール バーの再生ボタンをクリックします。 ワークフローの実行が完了したら、先ほど構成した出力フォルダーに生成されている出力結果を調べます。 MXF 入力ソース ファイルからエンコードされた MP4 出力ファイルが生成されていることを確認できます。

## <a id="MXF_to_MP4_with_dyn_packaging"></a>MXF をマルチビットレートの MP4 にエンコードする (動的パッケージング対応)
このチュートリアルでは、入力ファイルとなる単一の .MXF から、オーディオを AAC でエンコードしたマルチ ビットレートの一連の MP4 ファイルを作成します。

マルチビットレートの資産を出力して、Azure Media Services の動的パッケージング機能と連動させるには、GOP 単位でアラインメントされたそれぞれビットレートと解像度の異なる複数の MP4 ファイルを生成する必要があります。 以降、この点について「 [MXF をシングル ビットレートの MP4 にエンコードする](media-services-media-encoder-premium-workflow-tutorials.md#MXF_to_MP4) 」のチュートリアルを出発点として説明していきます。

![出発点となるワークフロー](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-starting-workflow.png)

*出発点となるワークフロー*

### <a id="MXF_to_MP4_with_dyn_packaging_more_outputs"></a>MP4 出力の追加
最終的に Azure Media Services から生成される資産の各 MP4 ファイルによって、それぞれ異なるビットレートと解像度がサポートされます。 それでは MP4 出力ファイルをワークフローに追加しましょう。

ビデオ エンコーダーを確実にすべて同じ設定で作成するためには、既にある AVC ビデオ エンコーダーを複製し、解像度とビットレートを変更するのが簡単です。解像度が 960 x 540、1 秒あたりのフレーム数が 25、2.5 Mbps のビデオ エンコーダーを追加しましょう。 既存のエンコーダーを複製するには、そのエンコーダーをコピーしてデザイナー画面に貼り付けます。

この新しい AVC コンポーネントにメディア ファイル入力の [非圧縮ビデオ] の出力ピンを接続してください。

![2 つ目の AVC エンコーダーを接続したところ](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-second-avc-encoder-connected.png)

*2 つ目の AVC エンコーダーを接続したところ*

次に、960 x 540 を 2.5 Mbps で出力するように新しい AVC エンコーダーの構成に変更を加えます。 この設定には、[出力の幅]、[出力の高さ]、[ビットレート (kbps)] の各プロパティを使用します。

最終的に生成される資産を Azure Media Services の動的パッケージング機能と連動させることを考えると、ストリーミングのエンドポイントが、これらの MP4 ファイルから HLS/フラグメント化 MP4/DASH フラグメントを生成できることが必要です。しかも、それらのフラグメントは、クライアントがビットレートを切り替えたときに途切れなくスムーズにビデオとオーディオを再生できるように正確にアラインメントされていなければなりません。 この条件を満たすためには、2 つの AVC エンコーダーのプロパティで、両方の MP4 ファイルの GOP ("Group Of Pictures") サイズを 2 秒に設定する必要があります。次のように設定してください。

* [GOP サイズ モード] を [固定 GOP サイズ] に設定
* [キー フレーム インターバル] を 2 秒に設定
* [GOP IDR コントロール] を [クローズド GOP] に設定 (すべての GOP を独立させて依存関係を排除)

ワークフローを見やすくするために、1 つ目の AVC エンコーダーの名前を "AVC Video Encoder 640x360 1200 kbps" に、2 つ目の AVC エンコーダーの名前を "AVC Video Encoder 960x540 2500 kbps" に変更します。

次に、2 つ目の ISO MPEG-4 マルチプレクサーと 2 つ目のファイル出力を追加します。 このマルチプレクサーを新しい AVC エンコーダーに接続し、その出力をファイル出力に接続します。 さらに、AAC オーディオ エンコーダーの出力も、新しいマルチプレクサーの入力に接続してください。 そのうえでファイル出力を [出力ファイル/資産] ノードに接続し、生成される Media Services 資産に追加します。

![2 つ目のマルチプレクサーとファイル出力を接続したところ](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-second-muxer-file-output-connected.png)

*2 つ目のマルチプレクサーとファイル出力を接続したところ*

Azure Media Services の動的パッケージング機能と連動させるために、マルチプレクサーの [Chunk Mode] を [GOP カウントまたは期間] に構成し、[チャンクあたりの GOP 数] を 1 に設定します  (既定値)。

![マルチプレクサーのチャンク モード](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-muxer-chunk-modes.png)

*マルチプレクサーのチャンク モード*

注: 資産の出力に追加するビットレートと解像度の組み合わせごとにこの作業を繰り返す必要があります。

### <a id="MXF_to_MP4_with_dyn_packaging_conf_output_names"></a>ファイル出力名の構成
ここまで、出力される資産として既に複数のファイルを追加してきました。 そのため、個々の出力ファイルの名前が重複しないようにする必要があります。場合によっては、自分が作業している内容がファイル名から明らかとなるように、ファイルの名前付け規則も必要になるでしょう。

ファイル出力の名前付けは、デザイナーから式を使って制御できます。 いずれかのファイル出力コンポーネントのプロパティ ウィンドウを開き、File プロパティの式エディターを開きます。 1 つ目の出力ファイルは、次の式で設定しました (「 [MXF をシングル ビットレートの MP4 にエンコードする](media-services-media-encoder-premium-workflow-tutorials.md#MXF_to_MP4)」を参照)。

    ${ROOT_outputWriteDirectory}\${ROOT_sourceFileBaseName}.MP4

これを見ると、ファイル名は、2 つの変数によって決定されることがわかります。書き込み先となる出力ディレクトリとソース ファイルのベース名です。 前者はワークフローのルートにプロパティとして公開され、後者は入力ファイルから取得されます。 出力ディレクトリは、ローカル テスト用です。Azure Media Services でクラウドベースのメディア プロセッサによってワークフローが実行されたときに、このプロパティはワークフロー エンジンによってオーバーライドされます。
両方の出力ファイルに一貫した名前を付けるために、1 つ目のファイル名の式を次のように変更します。

    ${ROOT_outputWriteDirectory}\${ROOT_sourceFileBaseName}_640x360_1.MP4

同様に 2 つ目の式を次のように変更します。

    ${ROOT_outputWriteDirectory}\${ROOT_sourceFileBaseName}_960x540_2.MP4

ここでひとまずテスト ランを実行し、両方の MP4 出力ファイルが正しく生成されることを確認します。

### <a id="MXF_to_MP4_with_dyn_packaging_audio_tracks"></a>独立したオーディオ トラックの追加
この後、MP4 出力ファイルと一緒に .ism ファイルを生成する際、アダプティブ ストリーミングに使用するオーディオ トラックとして、オーディオのみの MP4 ファイルが必要になります。 このファイルを作成するには、もう 1 つマルチプレクサー (ISO-MPEG-4 マルチプレクサー) をワークフローに追加し、そのトラック 1 の入力ピンに AAC エンコーダーの出力ピンを接続します。

![オーディオ マルチプレクサーを追加したところ](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-audio-muxer-added.png)

*オーディオ マルチプレクサーを追加したところ*

このマルチプレクサーから送信されたストリームを出力するための第 3 のファイル出力コンポーネントを作成し、次のようにファイル名の式を構成します。

    ${ROOT_outputWriteDirectory}\${ROOT_sourceFileBaseName}_128kbps_audio.MP4

![オーディオ マルチプレクサーによって生成されるファイル出力](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-audio-muxer-creating-file-output.png)

*オーディオ マルチプレクサーによって生成されるファイル出力*

### <a id="MXF_to_MP4_with_dyn_packaging_ism_file"></a>.ISM SMIL ファイルの追加
最終的に得られる Media Services 資産に含まれる 2 つの MP4 ファイル (とオーディオのみの MP4) を動的パッケージング機能と連動させるためには、SMIL (Synchronized Multimedia Integration Language) とも呼ばれるマニフェスト ファイルが必要となります。 どの MP4 ファイルを動的パッケージングに使用でき、そのうちどのファイルをオーディオ ストリーミング用と見なすかが、このファイルを介して Azure Media Services に伝えられます。 以下に示したのは、オーディオ ストリームを 1 つ含んだ一連の MP4 の一般的なマニフェスト ファイルの例です。

```xml
    <?xml version="1.0" encoding="utf-8" standalone="yes"?>
    <smil xmlns="https://www.w3.org/2001/SMIL20/Language">
      <head>
        <meta name="formats" content="mp4" />
      </head>
      <body>
        <switch>
          <video src="H264_1900kbps_AAC_und_ch2_96kbps.mp4" />
          <video src="H264_1300kbps_AAC_und_ch2_96kbps.mp4" />
          <video src="H264_900kbps_AAC_und_ch2_96kbps.mp4" />
          <audio src="AAC_ch2_96kbps.mp4" title="AAC_und_ch2_96kbps" />
        </switch>
      </body>
    </smil>
```

.ism ファイルの switch ステートメントには、各 MP4 ビデオ ファイルの参照に加え、音声のみを含んだ MP4 オーディオ ファイルの参照が 1 つまたは複数記述されます。

必要な一連の MP4 に対するマニフェスト ファイルは、"AMS マニフェスト ライター" というコンポーネントを使って生成できます。 このコンポーネントをデザイン画面にドラッグし、3 つのファイル出力コンポーネントの [書き込み完了] 出力ピンを AMS マニフェスト ライターの入力に接続してください。 そのうえで、AMS マニフェスト ライターの出力を [出力ファイル/資産] に接続します。

出力される .ism ファイルについても他のファイル出力コンポーネントと同様、次の式を使って名前を構成します。

    ${ROOT_outputWriteDirectory}\\${ROOT_sourceFileBaseName}_manifest.ism

完成したワークフローは次のようになります。

![MXF からマルチビットレートの MP4 を生成するワークフローが完成](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-finished-mxf-to-multibitrate-mp4-workflow.png)

*MXF からマルチビットレートの MP4 を生成するワークフローが完成*

## <a id="MXF_to__multibitrate_MP4"></a>MXF をマルチビットレートの MP4 にエンコードする (設計の改良)
[前のワークフロー チュートリアル](media-services-media-encoder-premium-workflow-tutorials.md#MXF_to_MP4_with_dyn_packaging)では、単一の MXF 入力資産を変換し、出力資産としてマルチビットレートの MP4 ファイル、音声のみの MP4 ファイル、マニフェスト ファイル (Azure Media Services の動的パッケージング機能との連携に必要) を生成する方法を紹介しました。

このチュートリアルでは、それらに少し手を加えて利便性を高める方法を探っていきます。

### <a id="MXF_to_multibitrate_MP4_overview"></a>改良するワークフローの全体像
![改良するマルチビットレートの MP4 ワークフロー](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-multibitrate-mp4-workflow-to-enhance.png)

*改良するマルチビットレートの MP4 ワークフロー*

### <a id="MXF_to__multibitrate_MP4_file_naming"></a>ファイルの名前付け規則
先ほどのワークフローでは、生成する出力ファイル名のベースとして単純な式を指定しました。 ただし、すべての出力ファイルの構成要素に対して個別にそのような式を指定するのは無駄に手間がかかります。

たとえば、1 つ目のビデオ ファイルに使用されているファイル出力コンポーネントは、次の式を使って構成されています。

    ${ROOT_outputWriteDirectory}\${ROOT_sourceFileBaseName}_640x360_1.MP4

同様に、2 つ目の出力ビデオの式は次のように指定されています。

    ${ROOT_outputWriteDirectory}\\${ROOT_sourceFileBaseName}_960x540_2.MP4

もしハードコーディングを止めて柔軟性を高めることができれば、わかりやすくミスも減ってよいのではないでしょうか。 さいわい、それを実現する方法があります。デザイナーが備えている式の機能と、ワークフローのルートにカスタム プロパティを作成する機能とを組み合わせることで、式の無駄をなくすことができます。

ここでは個々の MP4 ファイルのビットレートからファイル名の構成を取得するとしましょう。 ビットレートに対する構成を一か所 (ワークフロー図のルート) で行い、ファイル名を生成する段階でそこにアクセスするようにします。 そのためにまず、両方の AVC エンコーダーのビットレート プロパティをワークフローのルートに対して公開します。 こうすることで、ルートだけでなく、AVC エンコーダーからもビットレート プロパティにアクセスできるようになります (2 か所に表示されますが、基になる値は 1 つだけです)。

### <a id="MXF_to__multibitrate_MP4_publishing"></a>コンポーネントのプロパティをワークフローのルートに公開する
1 つ目の AVC エンコーダーを開いて [ビットレート (kbps)] プロパティに移動し、ドロップダウンから [公開] を選択します。

![ビットレート プロパティの公開](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-publishing-bitrate-property.png)

*ビットレート プロパティの公開*

プロパティの公開ダイアログで、公開先をワークフロー図のルートとし、公開名に「video1bitrate」を、表示名 (見やすい形式の名前) に「Video 1 Bitrate」を入力します。 カスタム グループ名として「Streaming Bitrates」を指定し、[公開] をクリックします。

![ビットレート プロパティの公開](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-publishing-dialog-for-bitrate-property.png)

*ビットレート プロパティの公開ダイアログ*

2 つ目の AVC エンコーダーのビットレート プロパティについても同じ手順を行います。同じカスタム グループ "Streaming Bitrates" で「video2bitrate」という名前を付け、表示名は「Video 2 Bitrate」とします。

ワークフロー ルートのプロパティを調べると、公開された 2 つのプロパティと共にカスタム グループが表示されます。 どちらも、それぞれの AVC エンコーダーに適用されたビットレートの値を反映しています。

![Published bitrate props on workflow root](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-published-bitrate-props-on-workflow-root.png)

これらのプロパティには、次のようにしてコードまたは式からアクセスできます。

* ルートの直下に位置するコンポーネントのインライン コードからアクセスする場合: node.getPropertyAsString('../video1bitrate', null)
* 式からアクセスする場合: ${ROOT_video1bitrate}

最後に、オーディオ トラックのビットレートも "Streaming Bitrates" グループに公開しておきましょう。 AAC エンコーダーのプロパティからビットレートの設定を探し、その横にあるドロップダウンから [公開] を選択してください。 公開先をワークフロー図のルートとし、名前に「audio1bitrate」を、表示名に「Audio 1 Bitrate」を入力して、カスタム グループ "Streaming Bitrates" に追加します。

![オーディオ ビットレートの公開ダイアログ](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-publishing-dialog-for-audio-bitrate.png)

*オーディオ ビットレートの公開ダイアログ*

![ビデオとオーディオのプロパティをルートに設定したところ](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-resulting-video-and-audio-props-on-root.png)

*ビデオとオーディオのプロパティをルートに設定したところ*

3 つの値のいずれかを変更すると、リンク先 (公開元) である個々のコンポーネントの値も再構成され変更されることに注目してください。

### <a id="MXF_to__multibitrate_MP4_output_files"></a>生成される出力ファイル名に公開プロパティの値を使用する
今度は、生成するファイル名をハードコーディングするのではなく、ワークフロー図のルートに公開したビットレート プロパティを使用するように、各ファイル出力コンポーネントでファイル名の式を変更しましょう。 まず 1 つ目のファイル出力の File プロパティを探し、その式を次のように編集します。

    ${ROOT_outputWriteDirectory}\${ROOT_sourceFileBaseName}_${ROOT_video1bitrate}kbps.MP4

この式の各種パラメーターは、式ウィンドウを表示した状態でキーボードからドル記号を入力することによってアクセスしたり入力したりすることができます。 利用可能なパラメーターの 1 つが、先ほど公開した video1bitrate プロパティです。

![式内からパラメーターにアクセス](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-accessing-parameters-within-an-expression.png)

*式内からパラメーターにアクセス*

2 つ目のビデオのファイル出力についても同じ作業を行います。

    ${ROOT_outputWriteDirectory}\${ROOT_sourceFileBaseName}_${ROOT_video2bitrate}kbps.MP4

音声のみのファイル出力も同様に編集します。

    ${ROOT_outputWriteDirectory}\${ROOT_sourceFileBaseName}_${ROOT_audio1bitrate}bps_audio.MP4

以後、ビデオ ファイルまたはオーディオ ファイルのビットレートを変更した場合、それぞれ対応するエンコーダーが再構成され、ビットレートに基づくファイルの名前付け規則が自動的に反映されます。

## <a id="thumbnails_to__multibitrate_MP4"></a>マルチビットレートの MP4 出力にサムネイルを追加する
今度は、 [MXF 入力からマルチビットレートの MP4 出力](media-services-media-encoder-premium-workflow-tutorials.md#MXF_to_MP4_with_dyn_packaging)を生成するワークフローを発展させて、出力にサムネイルを追加する方法を見ていきましょう。

### <a id="thumbnails_to__multibitrate_MP4_overview"></a>サムネイルの追加先となるワークフローの全体像
![出発点となるマルチビットレートの MP4 ワークフロー](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-multibitrate-mp4-workflow-to-start-from.png)

*出発点となるマルチビットレートの MP4 ワークフロー*

### <a id="thumbnails_to__multibitrate_MP4__with_jpg"></a>JPG エンコードの追加
サムネイル生成の核となるのは、JPG エンコーダー コンポーネント、つまり JPG ファイルを出力する機能です。

![JPG エンコーダー](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-jpg-encoder.png)

*JPG エンコーダー*

しかし、メディア ファイル入力から JPG エンコーダーに非圧縮ビデオ ストリームを直接接続することはできません。 エンコーダーには個々のフレームを渡す必要があります。 これは、ビデオ フレーム ゲート コンポーネントを使って行うことができます。

![フレーム ゲートを JPG エンコーダーに接続](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-connect-frame-gate-to-jpg-encoder.png)

*フレーム ゲートを JPG エンコーダーに接続*

フレーム ゲートを使用すると一定間隔 (秒数またはフレーム数) でビデオ フレームを渡すことができます。 この間隔とタイム オフセット (この処理が実行されるまでの時間) は、そのプロパティで構成することができます。

![ビデオ フレーム ゲートのプロパティ](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-video-frame-gate-properties.png)

*ビデオ フレーム ゲートのプロパティ*

[モード] を [時間 (秒)] に、[間隔] を「60」に設定して、サムネイルを 1 分間隔で作成することにしましょう。

### <a id="thumbnails_to__multibitrate_MP4_color_space"></a>色空間の変換処理
一見、フレーム ゲートの [非圧縮ビデオ] ピンとメディア ファイル入力の [非圧縮ビデオ] ピンとを接続するのが正しいように思えますが、そのようにした場合、警告が表示されます。

![入力色空間エラー](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-input-color-space-error.png)

*入力色空間エラー*

これは、最初に MXF から入力された未加工の非圧縮ビデオ ストリームにおける色情報の表現が、JPG エンコーダー側の要件と異なることが原因です。 具体的には、いわゆる "RGB" や "グレースケール" の "色空間" が入力として想定されています。 つまり、ビデオ フレーム ゲートの入力ビデオ ストリームに対してあらかじめ、色空間に関する変換を適用しておく必要があります。

[色空間コンバーター - Intel] をワークフローにドラッグし、フレーム ゲートに接続してください。

![色空間コンバーターの接続](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-connect-color-space-convertor.png)

*色空間コンバーターの接続*

プロパティ ウィンドウで、[プリセット] ボックスの一覧の [BGR 24] という項目を選択してください。

### <a id="thumbnails_to__multibitrate_MP4_writing_thumbnails"></a>サムネイルの作成
これまで見てきた MP4 ビデオとは異なり、JPG エンコーダー コンポーネントからは複数のファイルが出力されます。 そこで、"シーン サーチ JPG ファイル ライター" というコンポーネントを使用します。このコンポーネントは、JPG 形式のサムネイルを入力として受け取り、末尾に異なる数字を持ったファイル名でそれらを出力します  (この数字は通常、サムネイルの取得元となったストリーム内の秒数/単位数を示します)。

![シーン サーチ JPG ファイル ライターを追加](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-scene-search-jpg-file-writer.png)

*シーン サーチ JPG ファイル ライターを追加*

[出力フォルダー パス] プロパティに「${ROOT_outputWriteDirectory}」という式を設定します。

また、[ファイル名プレフィックス] プロパティには次のように指定します。

    ${ROOT_sourceFileBaseName}_thumb_

このプレフィックスによって、サムネイル ファイルに付けられる名前が決まります。 ファイル名の最後には、ストリームにおけるサムネイルの位置を示す数字が付加されます。

![シーン サーチ JPG ファイル ライターのプロパティ](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-scene-search-jpg-file-writer-properties.png)

*シーン サーチ JPG ファイル ライターのプロパティ*

シーン サーチ JPG ファイル ライターを [出力ファイル/資産] ノードに接続します。

### <a id="thumbnails_to__multibitrate_MP4_errors"></a>ワークフローで発生したエラーの検出
色空間コンバーターの入力は、未加工の非圧縮ビデオ出力に接続してください。 これで、ワークフローのローカル テスト ランを実行できます。 ワークフローの実行が突然停止し、エラーの発生を示す赤色の枠がコンポーネントに表示されることがあります。

![色空間コンバーター エラー](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-color-space-converter-error.png)

*色空間コンバーター エラー*

色空間コンバーター コンポーネントの右上隅にある小さな赤色の "E" アイコンをクリックすると、エンコードに失敗した理由が表示されます。

![色空間コンバーター エラー ダイアログ](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-color-space-converter-error-dialog.png)

*色空間コンバーター エラー ダイアログ*

この画面からわかるように、指定した YUV から RGB への変換を行うには、色空間コンバーターに使用する入力色空間の標準が rec601 である必要があります。 ストリームで rec601 であるという指定が欠けていたようです  (Rec 601 は、インターレース方式のアナログ ビデオ信号をデジタル ビデオ形式にエンコードするための標準です。 1 ラインあたりの 720 輝度サンプルと 360 色差サンプルをカバーする有効領域を指定します。 このカラー符号化システムは YCbCr 4:2:2 として知られています。)

このエラーを修正するためには、処理の対象が rec601 のコンテンツであることをストリームのメタデータで指定する必要があります。 そのために、ビデオ データ タイプ アップデーター コンポーネントを使用します。 これを未加工のソースと色空間変換コンポーネントとの間に配置しましょう。 このデータ タイプ アップデーターによって、特定の種類のビデオ データが備えているプロパティに手動で変更を加えることができます。 これにより、ストリームに色空間がまだ定義されていない場合、"Rec 601" の色空間を示すタグがビデオ データ タイプ アップデーターによって付けられます  これでストリームに色空間がまだ定義されていない場合、"Rec 601" の色空間を示すタグがビデオ データ タイプ アップデーターによって付けられます ([オーバーライド] チェック ボックスをオンにしない限り、既存のメタデータはオーバーライドされません)。

![データ タイプ アップデーターで色空間標準を更新](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-update-color-space-standard-on-data-type.png)

*データ タイプ アップデーターで色空間標準を更新*

### <a id="thumbnails_to__multibitrate_MP4_finish"></a>完成したワークフロー
これで目的のワークフローが完成しました。テスト ランを実行して正しく動作するか確認してみましょう。

![複数の MP4 出力とサムネイルを生成するワークフローが完成](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-finished-workflow-for-multi-mp4-thumbnails.png)

*複数の MP4 出力とサムネイルを生成するワークフローが完成*

## <a id="time_based_trim"></a>マルチビットレートの MP4 出力を時間に基づいてトリミングする
今度は、 [MXF 入力からマルチビットレートの MP4 出力](media-services-media-encoder-premium-workflow-tutorials.md#MXF_to_MP4_with_dyn_packaging)を生成するワークフローを発展させ、時間に基づいてソース ビデオをトリミングする方法を探ってみましょう。

### <a id="time_based_trim_start"></a>トリミングの追加先となるワークフローの全体像
![トリミングの追加先となる出発点のワークフロー](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-starting-workflow-to-add-trimming.png)

*トリミングの追加先となる出発点のワークフロー*

### <a id="time_based_trim_use_stream_trimmer"></a>ストリーム トリマーの使用
ストリーム トリマー コンポーネントを使用すると、入力ストリームの開始と終了をタイミング情報 (秒、分など) に基づいてトリミングすることができます。フレーム ベースのトリミングは、このトリマーではサポートされません。

![ストリーム トリマー](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-stream-trimmer.png)

*ストリーム トリマー*

AVC エンコーダーとスピーカー ポジション アサイナーは、直接メディア ファイル入力に関連付けるのではなく、ストリーム トリマーを介在させます  (ビデオ信号用に 1 つ、インターリーブ音声信号用に 1 つ)。

![ストリーム トリマーを介在させる](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-put-stream-trimmer-in-between.png)

*ストリーム トリマーを介在させる*

ビデオの再生開始 15 秒から 60 秒までの間の映像と音声のみを処理するようにトリマーを構成してみましょう。

ビデオ ストリーム トリマーのプロパティに移動して、[開始時間] プロパティを 15 秒に、[終了時間] プロパティを 60 秒に設定します。 開始時間と終了時間をオーディオ トリマーとビデオ トリマーの両者間で確実に合わせるために、これらのプロパティをワークフローのルートに公開します。

![ストリーム トリマーから開始時間プロパティを公開](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-publish-start-time-from-stream-trimmer.png)

*ストリーム トリマーから開始時間プロパティを公開*

![開始時間プロパティの公開ダイアログ](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-publish-dialog-for-start-time.png)

*開始時間プロパティの公開ダイアログ*

![終了時間プロパティの公開ダイアログ](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-publish-dialog-for-end-time.png)

*終了時間プロパティの公開ダイアログ*

ここでワークフローのルートを見ると、両方のプロパティがきちんと表示され、そこから設定できる状態になっています。

![公開されたプロパティをルートで表示したところ](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-published-properties-available-on-root.png)

*公開されたプロパティをルートで表示したところ*

次に、オーディオ トリマーのトリミング プロパティを開いて、開始時間と終了時間の両方を式を使って構成します。この式の中で、ワークフローのルートに公開されたプロパティを参照することになります。

音声のトリミングに関して、次のように開始時間を指定します。

    ${ROOT_TrimmingStartTime}

同様に、終了時間を次のように指定します。

    ${ROOT_TrimmingEndTime}

### <a id="time_based_trim_finish"></a>完成したワークフロー
![完成したワークフロー](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-finished-workflow-time-base-trimming.png)

*完成したワークフロー*

## <a id="scripting"></a>スクリプト コンポーネントの概要
スクリプト コンポーネントは、ワークフローの実行段階で任意のスクリプトを実行できます。 実行できるスクリプトは 4 種類あります。それぞれ明確な特性を備え、ワークフローのライフサイクルの中で実行される場所が決まっています。

* **commandScript**
* **realizeScript**
* **processInputScript**
* **lifeCycleScript**

それぞれの種類については、スクリプト コンポーネントのドキュメントで詳しく解説されています。 [以降のセクションでは](media-services-media-encoder-premium-workflow-tutorials.md#frame_based_trim)、**realizeScript** スクリプト コンポーネントを使って、ワークフローの開始時にクリップ リストの XML を動的に作成します。 このスクリプトは、コンポーネントのセットアップ時に呼び出されます。呼び出されるのは、ライフサイクルの中で 1 回だけです。

### <a id="scripting_hello_world"></a>ワークフロー内スクリプトの作成: hello world
スクリプト コンポーネントをデザイナー画面にドラッグして名前を変更します (例: "SetClipListXML")。

![スクリプト コンポーネントの追加](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-add-scripted-comp.png)

*スクリプト コンポーネントの追加*

スクリプト コンポーネントのプロパティを詳しく見てみると、4 つのスクリプト タイプが表示され、それぞれ異なるスクリプトとして構成できることがわかります。

![スクリプト コンポーネントのプロパティ](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-scripted-comp-properties.png)

*スクリプト コンポーネントのプロパティ*

processInputScript の内容を消去し、realizeScript のエディターを開きます。 これでスクリプトを作成する準備が整いました。

スクリプトの作成には Groovy を使用します。Groovy は、Java プラットフォーム向けの動的にコンパイルされるスクリプト言語で、Java との互換性が確保されています。 実際、ほとんどの Java コードは Groovy コードとして有効です。

では、realizeScript のコンテキストで単純な Groovy スクリプト (hello world) を作成しましょう。 エディターで次のように入力してください。

    node.log("hello world");

ローカル テスト ランを実行します。 実行後、スクリプト コンポーネントの [システム] タブで [ログ] プロパティを確認します。

![Hello world のログ出力](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-log-output.png)

*Hello world のログ出力*

先ほど node オブジェクトの log メソッドを呼び出しましたが、この node オブジェクトが参照しているのは、現在の "ノード"、つまりスクリプトの作成先となっているコンポーネントです。 このように、すべてのコンポーネントにはログ データを出力する機能があり、[システム] タブからそのログにアクセスすることができます。この例では、"hello world" という文字列リテラルを出力しました。 スクリプトが "実際に何をしているか" について深く知ることができるという点で、これはきわめて優れたデバッグ ツールといえます。

このスクリプト環境内から、他のコンポーネントのプロパティにアクセスすることもできます。 次のコードを実行してみてください。

```java
    //inspect current node:
    def nodepath = node.getNodePath();
    node.log("this node path: " + nodepath);

    //walking up to other nodes:
    def parentnode = node.getParentNode();
    def parentnodepath = parentnode.getNodePath();
    node.log("parent node path: " + parentnodepath);

    //read properties from a node:
    def sourceFileExt = parentnode.getPropertyAsString( "sourceFileExtension", null );
    def sourceFileName = parentnode.getPropertyAsString("sourceFileBaseName", null);
    node.log("source file name with extension " + sourceFileExt + " is: " + sourceFileName);
```

ログ ウィンドウには次のように表示されます。

![ノード パスへのアクセスに関するログ出力](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-log-output2.png)

*ノード パスへのアクセスに関するログ出力*

## <a id="frame_based_trim"></a>マルチビットレートの MP4 出力をフレームに基づいてトリミングする
今度は、 [MXF 入力からマルチビットレートの MP4 出力](media-services-media-encoder-premium-workflow-tutorials.md#MXF_to_MP4_with_dyn_packaging)を生成するワークフローを発展させ、フレーム数に基づいてソース ビデオをトリミングする方法を探ってみましょう。

### <a id="frame_based_trim_start"></a>トリミングの追加先となるワークフローの全体像
![トリミングの追加先となるワークフロー](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-workflow-start-adding-trimming-to.png)

*トリミングの追加先となるワークフロー*

### <a id="frame_based_trim_clip_list"></a>クリップ リスト XML の使用
これまでに紹介してきたワークフローはすべて、ビデオの入力ソースとしてメディア ファイル入力コンポーネントを使用してきました。 しかし、これから紹介するシナリオに限っては、クリップ リスト ソース コンポーネントを使用します。 ただし、クリップ リスト ソースは、本当に必要なとき (次のケースのように、クリップ リストのトリミング機能を利用する場合など) 以外は使用しないでください。やみくもにこの方法を使用してはいけません。

メディア ファイル入力をクリップ リスト ソースに切り替えるには、クリップ リスト ソース コンポーネントをデザイン画面にドラッグし、[クリップ リスト XML] ピンをワークフロー デザイナーの [クリップ リスト XML] ノードに接続します。 これで、入力ビデオに応じて出力ピンがクリップ リスト ソースに追加されます。 次に、クリップ リスト ソースの非圧縮ビデオ ピンと非圧縮オーディオ ピンをそれぞれ AVC エンコーダーとオーディオ ストリーム インターリーバーに接続します。 この時点でメディア ファイル入力を削除してください。

![メディア ファイル入力をクリップ リスト ソースに置き換え](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-replaced-media-file-with-clip-source.png)

*メディア ファイル入力をクリップ リスト ソースに置き換え*

クリップ リスト ソース コンポーネントは、その入力として "クリップ リスト XML" を受け取ります。 ローカル テストに使用するソース ファイルを選択すると、このクリップ リスト XML の値が自動的に追加されます。

![クリップ リスト XML プロパティの値が自動的に追加される](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-auto-populated-clip-list-xml-property.png)

*クリップ リスト XML プロパティの値が自動的に追加される*

この XML をクローズアップして見てみましょう。

![クリップ リストの編集ダイアログ](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-edit-clip-list-dialog.png)

*クリップ リストの編集ダイアログ*

ただし、このコードだけでは、クリップ リスト XML の機能がわかりません。 そこで次のように、ビデオ ソースとオーディオ ソースの両方に "trim" 要素を追加してみましょう。

![クリップ リストに trim 要素を追加したところ](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-adding-trim-element-to-clip-list.png)

*クリップ リストに trim 要素を追加したところ*

このようにクリップ リスト XML を変更して、ローカル テスト ランを実行すると、ビデオの再生開始 10 秒から 20 秒までの区間が正しくトリミングされます。

ところが、Azure Media Services 内で動作するワークフローにこれとまったく同じクリップ リスト XML を適用しても、ローカルでテストしたときのようには実行されません。 Azure Premium Encoder が起動すると、その都度、エンコード ジョブに与えられた入力ファイルに基づいてクリップ リスト XML が生成されます。 せっかく XML に対して行った変更が、残念ながらすべてオーバーライドされてしまいます。

もちろん対処法はあります。エンコード ジョブが開始されたときにクリップ リスト XML が上書きされるのであれば、ワークフローの開始後、動的に再生成すればよいのです。 このようなカスタム アクションは、"スクリプト コンポーネント" を使って実行できます。 詳細については、「[スクリプト コンポーネントの概要](media-services-media-encoder-premium-workflow-tutorials.md#scripting)」を参照してください。

スクリプト コンポーネントをデザイナー画面にドラッグして名前を "SetClipListXML" に変更します。

![スクリプト コンポーネントの追加](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-add-scripted-comp.png)

*スクリプト コンポーネントの追加*

スクリプト コンポーネントのプロパティを詳しく見てみると、4 つのスクリプト タイプが表示され、それぞれ異なるスクリプトとして構成できることがわかります。

![スクリプト コンポーネントのプロパティ](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-scripted-comp-properties.png)

*スクリプト コンポーネントのプロパティ*

### <a id="frame_based_trim_modify_clip_list"></a>スクリプト コンポーネントからのクリップ リストの変更
ワークフローの開始時に生成されたクリップ リスト XML を書き換えるには、そのクリップ リスト XML のプロパティと内容にアクセスする必要があります。 以下のようなコードが必要となります。

```java
    // get cliplist xml:
    def clipListXML = node.getProperty("../clipListXml");
    node.log("clip list xml coming in: " + clipListXML);
```

![入力されたクリップ リストをログに記録](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-incoming-clip-list-logged.png)

*入力されたクリップ リストをログに記録*

まず、トリミングする区間を決める必要があります。 高度な技術知識は必要ありません。ワークフロー図のルートに 2 つのプロパティを公開しましょう。 デザイナー画面を右クリックし、[プロパティの追加] を選択します。

* 1 つ目のプロパティ:"ClippingTimeStart" (型:"TIMECODE")
* 2 つ目のプロパティ:"ClippingTimeEnd" (型:"TIMECODE")

![クリッピングの開始時間のプロパティを追加するダイアログ](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-clip-start-time.png)

*クリッピングの開始時間のプロパティを追加するダイアログ*

![クリップ区間を表す時間のプロパティがワークフローのルート上に公開された](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-clip-time-props.png)

*クリップ区間を表す時間のプロパティがワークフローのルート上に公開された*

2 つのプロパティを適切な値に構成します。

![クリッピングの開始プロパティと終了プロパティの構成](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-configure-clip-start-end-prop.png)

*クリッピングの開始プロパティと終了プロパティの構成*

これでスクリプト内から次のようにして 2 つのプロパティにアクセスすることができます。

```java
    // get start and end of clipping:
    def clipstart = node.getProperty("../ClippingTimeStart").toString();
    def clipend = node.getProperty("../ClippingTimeEnd").toString();

    node.log("clipping start: " + clipstart);
    node.log("clipping end: " + clipend);
```

![クリッピングの開始と終了を示すログ ウィンドウ](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-show-start-end-clip.png)

*クリッピングの開始と終了を示すログ ウィンドウ*

単純な正規表現を使ってもっと扱いやすい形式となるように timecode 型の文字列を解析しましょう。

```java
    //parse the start timing:
    def startregresult = (~/(\d\d:\d\d:\d\d:\d\d)\/(\d\d)/).matcher(clipstart);
    startregresult.matches();
    def starttimecode = startregresult.group(1);
    node.log("timecode start is: " + starttimecode);
    def startframerate = startregresult.group(2);
    node.log("framerate start is: " + startframerate);

    //parse the end timing:
    def endregresult = (~/(\d\d:\d\d:\d\d:\d\d)\/(\d\d)/).matcher(clipend);
    endregresult.matches();
    def endtimecode = endregresult.group(1);
    node.log("timecode end is: " + endtimecode);
    def endframerate = endregresult.group(2);
    node.log("framerate end is: " + endframerate);
```

![timecode を解析してログ ウィンドウに出力](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-output-parsed-timecode.png)

*timecode を解析してログ ウィンドウに出力*

この情報があれば、クリップ リスト XML に変更を加えることができます。開始時間と終了時間を反映し、正確なフレーム数に基づいて動画をクリッピングすることが可能です。

![trim 要素を追加するスクリプト コード](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-add-trim-elements.png)

*trim 要素を追加するスクリプト コード*

この処理には、通常の文字列操作が使用されています。 変更されたクリップ リスト XML は、"setProperty" メソッドで、ワークフローのルートにある clipListXML プロパティに書き戻されます。 もう一度テスト ランを実行すると、ログ ウィンドウに次のように表示されます。

![出力されたクリップ リストのログ](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-log-result-clip-list.png)

*出力されたクリップ リストのログ*

ビデオ ストリームとオーディオ ストリームがどのようにクリッピングされたかを確認するためにテスト ランを実行します。 ところが、トリミング ポイントの値を変えながら複数回にわたってテスト ランを実行すると、それらの値が考慮されていないことがわかります。 この理由は、Azure のランタイムとは異なり、デザイナーでは、実行の都度、クリップ リスト XML がオーバーライドされることはないためです。 つまり、この XML が変換されるのは、最初にクリッピングの開始と終了を設定したときだけです。それ以外は、ワークフローが 2 つ目以降の trim 要素を追加できないようにするガード句 (if(`clipListXML.indexOf("<trim>") == -1`)) によって処理がスキップされます。

ローカルでワークフローをテストしやすいように、既存の trim 要素があるかどうかを調べる監視コードを追加した方がよさそうです。 そうすれば、既存の要素を削除したうえで、新しい値を使って xml を更新することができます。 これは純粋な文字列操作よりも、文字どおり xml オブジェクト モデルの解析によって行った方がおそらく安全です。

ただし、そのようなコードを追加するためにはまず、スクリプトの先頭にいくつかの import ステートメントを追加しなければなりません。

```java
    import javax.xml.parsers.*;
    import org.xml.sax.*;
    import org.w3c.dom.*;
    import javax.xml.*;
    import javax.xml.xpath.*;
    import javax.xml.transform.*;
    import javax.xml.transform.stream.*;
    import javax.xml.transform.dom.*;
```

そのステートメントに続けて、必要な削除処理コードを追加します。

```java
    //for local testing: delete any pre-existing trim elements from the clip list xml by parsing the xml into a DOM:
    DocumentBuilderFactory factory=DocumentBuilderFactory.newInstance();
    DocumentBuilder builder=factory.newDocumentBuilder();
    InputSource is=new InputSource(new StringReader(clipListXML));
    Document dom=builder.parse(is);

    //find the trim element inside videoSource and audioSource and remove it if it exists already:
    XPath xpath = XPathFactory.newInstance().newXPath();
    String findAllTrimElements = "//trim";
    NodeList trimelems = xpath.evaluate(findAllTrimElements,dom,XPathConstants.NODESET);

    //copy trim nodes into a "to-be-deleted" collection
    Set<Element> elementsToDelete = new HashSet<Element>();
    for (int i = 0; i < trimelems.getLength(); i++) {
        Element e = (Element)trimelems.item(i);
        elementsToDelete.add(e);
    }

    node.log("about to delete any existing trim nodes");
     //delete the trim nodes:
    elementsToDelete.each{
        e -> e.getParentNode().removeChild(e);
    };
    node.log("deleted any existing trim nodes");

    //serialize the modified clip list xml dom into a string:
    def transformer = TransformerFactory.newInstance().newTransformer();
    StreamResult result = new StreamResult(new StringWriter());
    DOMSource source = new DOMSource(dom);
    transformer.transform(source, result);
    clipListXML = result.getWriter().toString();
```

このコードの挿入位置は、クリップ リスト XML に trim 要素を追加する処理の直前です。

これで、必要に応じて変更を繰り返し適用しながらワークフローを実行し、調整することができます。    

### <a id="frame_based_trim_clippingenabled_prop"></a>トリミングが不要な場合に備えて ClippingEnabled プロパティを追加する
トリミングが不要な場合もあるかもしれませんので、最後にワークフローを微調整しましょう。トリミング/クリッピングを有効にするかどうかを指定するブール型のフラグを追加します。

"BOOLEAN" 型の "ClippingEnabled" という新しいプロパティをこれまでと同様、ワークフローのルートに公開します。

![クリッピングを有効にするためのプロパティを公開](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-enable-clip.png)

*クリッピングを有効にするためのプロパティを公開*

トリミングが必要かどうかを以下の簡単なガード句を使ってチェックし、クリップ リストに変更を加える必要があるかどうかを判断することができます。

```java
    //check if clipping is required:
    def clippingrequired = node.getProperty("../ClippingEnabled");
    node.log("clipping required: " + clippingrequired.toString());
    if(clippingrequired == null || clippingrequired == false)
    {
        node.setProperty("../clipListXml",clipListXML);
        node.log("no clipping required");
        return;
    }
```

### <a id="code"></a>完成したコード

```java
    import javax.xml.parsers.*;
    import org.xml.sax.*;
    import org.w3c.dom.*;
    import javax.xml.*;
    import javax.xml.xpath.*;
    import javax.xml.transform.*;
    import javax.xml.transform.stream.*;
    import javax.xml.transform.dom.*;

    // get cliplist xml:
    def clipListXML = node.getProperty("../clipListXml");
    node.log("clip list xml coming in: \n" + clipListXML);
    // get start and end of clipping:
    def clipstart = node.getProperty("../ClippingTimeStart").toString();
    def clipend = node.getProperty("../ClippingTimeEnd").toString();

    //parse the start timing:
    def startregresult = (~/(\d\d:\d\d:\d\d:\d\d)\/(\d\d)/).matcher(clipstart);
    startregresult.matches();
    def starttimecode = startregresult.group(1);
    node.log("timecode start is: " + starttimecode);
    def startframerate = startregresult.group(2);
    node.log("framerate start is: " + startframerate);

    //parse the end timing:
    def endregresult = (~/(\d\d:\d\d:\d\d:\d\d)\/(\d\d)/).matcher(clipend);
    endregresult.matches();
    def endtimecode = endregresult.group(1);
    node.log("timecode end is: " + endtimecode);
    def endframerate = endregresult.group(2);

    node.log("framerate end is: " + endframerate);

    //for local testing: delete any pre-existing trim elements
    //from the clip list xml by parsing the xml into a DOM:

    DocumentBuilderFactory factory=DocumentBuilderFactory.newInstance();
    DocumentBuilder builder=factory.newDocumentBuilder();
    InputSource is=new InputSource(new StringReader(clipListXML));
    Document dom=builder.parse(is);

    //find the trim element inside videoSource and audioSource and remove it if it exists already:
    XPath xpath = XPathFactory.newInstance().newXPath();
    String findAllTrimElements = "//trim";
    NodeList trimelems = xpath.evaluate(findAllTrimElements, dom, XPathConstants.NODESET);

    //copy trim nodes into a "to-be-deleted" collection
    Set<Element> elementsToDelete = new HashSet<Element>();
    for (int i = 0; i < trimelems.getLength(); i++) {
        Element e = (Element)trimelems.item(i);
        elementsToDelete.add(e);
    }

    node.log("about to delete any existing trim nodes");
    //delete the trim nodes:
    elementsToDelete.each{ e ->
        e.getParentNode().removeChild(e);
    };
    node.log("deleted any existing trim nodes");

    //serialize the modified clip list xml dom into a string:
    def transformer = TransformerFactory.newInstance().newTransformer();
    StreamResult result = new StreamResult(new StringWriter());
    DOMSource source = new DOMSource(dom);
    transformer.transform(source, result);
    clipListXML = result.getWriter().toString();

    //check if clipping is required:
    def clippingrequired = node.getProperty("../ClippingEnabled");
    node.log("clipping required: " + clippingrequired.toString());
    if(clippingrequired == null || clippingrequired == false)
    {
        node.setProperty("../clipListXml",clipListXML);
        node.log("no clipping required");
        return;
    }

    //add trim elements to cliplist xml
    if ( clipListXML.indexOf("<trim>") == -1 )
    {
        //trim video
        clipListXML = clipListXML.replace("<videoSource>","<videoSource>\n <trim>\n <inPoint fps=\""+
            startframerate +"\">" + starttimecode +
            "</inPoint>\n" + "<outPoint fps=\"" + endframerate +"\"> " + endtimecode +
            " </outPoint>\n </trim> \n");
        //trim audio
        clipListXML = clipListXML.replace("<audioSource>","<audioSource>\n <trim>\n <inPoint fps=\""+
            startframerate +"\">" + starttimecode +
            "</inPoint>\n" + "<outPoint fps=\""+ endframerate +"\">" +
            endtimecode + "</outPoint>\n </trim>\n");
        node.log( "clip list going out: \n" +clipListXML );
        node.setProperty("../clipListXml",clipListXML);
    }
```

## <a name="also-see"></a>関連トピック
[Azure Media Services の Premium Encoding の紹介 (ブログの投稿)](https://azure.microsoft.com/blog/2015/03/05/introducing-premium-encoding-in-azure-media-services)

[Azure Media Services で Premium Encoding を使用する方法 (ブログの投稿)](https://azure.microsoft.com/blog/2015/03/06/how-to-use-premium-encoding-in-azure-media-services)

[Azure Media Services を使用してオンデマンド コンテンツをエンコードする](media-services-encode-asset.md#media-encoder-premium-workflow)

[メディア エンコーダー プレミアム ワークフローの形式とコーデック](media-services-premium-workflow-encoder-formats.md)

[サンプルのワークフロー ファイル](https://github.com/Azure/azure-media-services-samples/tree/master/Encoding%20Presets/VoD/MediaEncoderPremiumWorkfows)

[Azure Media Services Explorer ツール](https://aka.ms/amse)

## <a name="media-services-learning-paths"></a>Media Services のラーニング パス
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>フィードバックの提供
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]
