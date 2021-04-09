---
title: Azure Media Analytics での顔編集チュートリアル | Microsoft Docs
description: このトピックでは、Azure Media Services Explorer (AMSE) と Azure Media Redactor Visualizer (オープン ソース ツール) を使用して編集ワークフロー全体を実行する方法について詳しく説明します。
services: media-services
documentationcenter: ''
author: IngridAtMicrosoft
manager: femila
editor: ''
ms.assetid: d6fa21b8-d80a-41b7-80c1-ff1761bc68f2
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: article
ms.date: 03/10/2021
ms.author: ril
ms.reviewer: juliako
ms.openlocfilehash: 1a7e20681dfa7da7ce30f46a7c4b0b6df6f78916
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "103009560"
---
# <a name="redact-faces-with-azure-media-analytics-walkthrough"></a>Azure Media Analytics での顔編集チュートリアル

[!INCLUDE [media services api v2 logo](./includes/v2-hr.md)]

## <a name="overview"></a>概要

**Azure Media Redactor** は、クラウドでスケーラブルな顔編集を提供する [Azure Media Analytics](./legacy-components.md) メディア プロセッサ (MP) です。 顔編集では、ビデオを編集して選択した個人の顔をぼかすことができます。 顔編集サービスは、公共の安全やニュース媒体などに使用していただけます。 複数人の顔を含んでいる映像の場合、顔編集を手作業で行うと数分の映像でも数時間かかりますが、このサービスを使えば数ステップの簡単な手順で完了します。 詳細については、[こちらの投稿](https://azure.microsoft.com/blog/azure-media-redactor/)を参照してください。

**Azure Media Redactor** の詳細については、[顔編集の概要](media-services-face-redaction.md)に関するトピックを参照してください。

このトピックでは、Azure Media Services Explorer (AMSE) と Azure Media Redactor Visualizer (オープン ソース ツール) を使用して編集ワークフロー全体を実行する方法について詳しく説明します。

詳細については、 [このブログ](https://azure.microsoft.com/blog/redaction-preview-available-globally) をご覧ください。

## <a name="azure-media-services-explorer-workflow"></a>Azure Media Services Explorer のワークフロー

Redactor の使用を開始する最も簡単な方法は、GitHub のオープン ソース AMSE ツールを使用することです。 注釈 json または顔の jpg イメージにアクセスする必要がない場合は、**結合** モードによる簡略化されたワークフローを実行できます。

### <a name="download-and-setup"></a>ダウンロードとセットアップ

1. AMS v2 用 AMSE ツールを[こちら](https://aka.ms/amseforv2)からダウンロードします。
1. サービス キーを使用して、Media Services アカウントにログインします。

    アカウント名とキーの情報を取得するには、[Azure Portal](https://portal.azure.com/) に移動して AMS アカウントを選択します。 [設定]、[キー] の順にクリックします。 [キーの管理] ウィンドウに、アカウント名、プライマリ キー、セカンダリ キーが表示されます。 アカウント名とプライマリ キーの値をコピーします。

![スクリーンショットは、アカウント名とキーを入力できる Microsoft Azure Media Services を示しています。](./media/media-services-redactor-walkthrough/media-services-redactor-walkthrough001.png)

### <a name="first-pass--analyze-mode"></a>最初のパス – 分析モード

1. メディア ファイルをアップロードします ([資産]、[アップロード] の順にクリックするか、ドラッグ アンド ドロップします)。 
1. マウスを右クリックし、[Media Analytics]、[Azure Media Redactor]、[分析モード] の順に選択して、メディア ファイルを処理します。 


![スクリーンショットに、Azure Media Redactor を持つプロセス資産があるメニューが示されています。](./media/media-services-redactor-walkthrough/media-services-redactor-walkthrough002.png)

![スクリーンショットに Azure Media Redactor が示され、最初のパスに分析モードが選択されています。](./media/media-services-redactor-walkthrough/media-services-redactor-walkthrough003.png)

顔の位置データを含む json ファイルと、検出された顔の jpg が出力されます。 

![スクリーンショットは、分析の出力を示しています。](./media/media-services-redactor-walkthrough/media-services-redactor-walkthrough004.png)

### <a name="second-pass--redact-mode"></a>2 回目のパス – 編集モード

1. 元のビデオ資産を最初のパスからの出力にアップロードし、プライマリ資産として設定します。 

    ![スクリーンショットに、アップロード ボタンとプライマリとして設定ボタンが示されています。](./media/media-services-redactor-walkthrough/media-services-redactor-walkthrough005.png)

2. (省略可能) 修正する ID の改行区切りリストを含む "Dance_idlist.txt" ファイルをアップロードします。 

    ![スクリーンショットは、テキスト ファイルをアップロードするオプションが示されています。](./media/media-services-redactor-walkthrough/media-services-redactor-walkthrough006.png)

3. (省略可能) annotations.json ファイルを編集します (境界ボックスの境界を広げるなど)。 
4. 最初のパスからの出力資産を右クリックし、[Redactor] を選択し、**修正** モードで実行します。 

    ![スクリーンショットに Azure Media Redactor が示され、2 番目のパスに編集モードが選択されています。](./media/media-services-redactor-walkthrough/media-services-redactor-walkthrough007.png)

5. 修正後の最終出力資産をダウンロードまたは共有します。 

    ![スクリーンショットに、[ダウンロード] ボタンが示されています。](./media/media-services-redactor-walkthrough/media-services-redactor-walkthrough008.png)

## <a name="azure-media-redactor-visualizer-open-source-tool"></a>Azure Media Redactor Visualizer オープン ソース ツール

オープン ソースの [ビジュアライザー ツール](https://github.com/Microsoft/azure-media-redactor-visualizer)は、開発者が注釈形式の解析と出力の使用を開始することを支援するように設計されています。

リポジトリを複製した後、プロジェクトを実行するためには、FFMPEG を[公式サイト](https://ffmpeg.org/download.html)からダウンロードする必要があります。

JSON 注釈データを解析しようとしている開発者は、Models.MetaData のサンプル コードを参照してください。

### <a name="set-up-the-tool"></a>ツールをセットアップする

1.  ソリューション全体をダウンロードし、ビルドします。 

    ![スクリーンショットは、メニューから選択されたビルド ソリューションを示しています。](./media/media-services-redactor-walkthrough/media-services-redactor-walkthrough009.png)

2.  FFMPEG を[こちら](https://ffmpeg.org/download.html)からダウンロードします。 このプロジェクトは、もともと、バージョン be1d324 (2016-10-04) で開発され、静的リンクが設定されていました。 
3.  ffmpeg.exe と ffprobe.exe を、AzureMediaRedactor.exe と同じ出力フォルダーにコピーします。 

    ![スクリーンショットに、ffmpeg や ffmpeg を含むフォルダーの内容が示されています。](./media/media-services-redactor-walkthrough/media-services-redactor-walkthrough010.png)

4. AzureMediaRedactor.exe を実行します。 

### <a name="use-the-tool"></a>ツールを使用する

1. Redactor MP を分析モードで使用して、ビデオを Azure Media Services アカウントで処理します。 
2. 元のビデオ ファイルと Redaction による分析ジョブの出力の両方をダウンロードします。 
3. ビジュアライザー アプリケーションを実行し、上記のファイルを選択します。 

    ![スクリーンショットに、Azure Media Redactor のアップロード中ファイルが示されています。](./media/media-services-redactor-walkthrough/media-services-redactor-walkthrough011.png)

4. ファイルをプレビューします。 右側のサイドバーを使用して、ぼかしたい顔を選択します。 
    
    ![スクリーンショットに、ぼかしの対象となる顔をプレビューおよび選択できる Azure Media Redactor が示されています。](./media/media-services-redactor-walkthrough/media-services-redactor-walkthrough012.png)

5.  下部にあるテキスト フィールドには、顔の ID が表示されます。 これらの ID を 改行区切りリストとして含む "idlist.txt" という名前のファイルを作成します。 

    >[!NOTE]
    > idlist.txt は ANSI で保存してください。 メモ帳を利用し、ANSI で保存できます。
    
6.  このファイルを、手順 1 で生成された出力資産にアップロードします。 元のビデオをこの資産にアップロードし、プライマリ資産として設定します。 
7.  この資産に対して "修正" モードで修正ジョブを実行して、修正後の最終ビデオを取得します。 

## <a name="next-steps"></a>次のステップ 

[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>フィードバックの提供
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

## <a name="related-links"></a>関連リンク
[Azure Media Services Analytics の概要](./legacy-components.md)

[Azure Media Analytics デモ](http://amslabs.azurewebsites.net/demos/Analytics.html)

[Azure Media Analytics の顔編集の発表](https://azure.microsoft.com/blog/azure-media-redactor/)