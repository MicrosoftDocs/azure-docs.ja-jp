---
title: Azure Media Indexer によるメディア ファイルのインデックス作成
description: Azure Media Indexer を使用すると、メディア ファイルのコンテンツを検索対応にしたり、字幕やキーワード用にフルテキストのトランスクリプトを生成したりできます。 このトピックでは、Media Indexer の使用方法について説明します。
services: media-services
documentationcenter: ''
author: asolanki
manager: femila
editor: ''
ms.assetid: 827a56b2-58a5-4044-8d5c-3e5356488271
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: article
ms.date: 03/18/2019
ms.author: adsolank;juliako;johndeu
ms.openlocfilehash: a11ae0414d6737f1588515ec19524bcf499f0c74
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/13/2019
ms.locfileid: "61215807"
---
# <a name="indexing-media-files-with-azure-media-indexer"></a>Azure Media Indexer によるメディア ファイルのインデックス作成
Azure Media Indexer を使用すると、メディア ファイルのコンテンツを検索対応にしたり、字幕やキーワード用にフルテキストのトランスクリプトを生成したりできます。 バッチ内の 1 つのメディア ファイルまたは複数のメディア ファイルを処理できます。  

> [!IMPORTANT]
> コンテンツのインデックスを作成する場合は、クリアな (バック グラウンド ミュージック、ノイズ、特殊効果、またはマイク ヒスノイズなどがない) 音声機能を持つメディア ファイルを使用してください。 適切なコンテンツの例としては、記録された会議、講義またはプレゼンテーションなどがあります。 ムービー、テレビ番組、混合音声とサウンド効果を含むもの、バックグラウンド ノイズ (ヒスノイズ) を含む記録状態が良好でないコンテンツは、インデックス作成に適しません。
> 
> 

インデックス作成ジョブは次の出力を生成できます。

* 次の形式のクローズド キャプション ファイル:**SAMI**、**TTML**、および **WebVTT**。
  
    クローズド キャプション ファイルには、Recognizability と呼ばれるタグが含まれています。これは、ソース ビデオ内の音声がどれくらい認識可能であるかに基づいて、インデックス作成ジョブを評価します。  見分けの値を使用して、出力ファイルの使いやすさを検査します。 低いスコアは、オーディオの品質が良好ではないために、インデックスの作成結果が良好ではないという意味です。
* キーワード ファイル (XML)。
* SQL サーバーで使用する Audio Indexing BLOB (AIB) ファイル。
  
    詳細については、「 [AIB ファイルを Azure Media Indexer および SQL Server で使用する](https://azure.microsoft.com/blog/2014/11/03/using-aib-files-with-azure-media-indexer-and-sql-server/)」をご覧ください。

この記事では、ジョブのインデックスを作成して、**資産のインデックス**と**複数のファイルのインデックスを作成**する方法について説明します。

最近の Azure Media Indexer の更新プログラムについては、 [Media Services のブログ](#preset)をご覧ください。

## <a name="using-configuration-and-manifest-files-for-indexing-tasks"></a>タスクのインデックスを作成するための構成とマニフェスト ファイルの使用
タスクの構成を使用して、インデックス作成のタスクの詳細を指定できます。 たとえば、メディア ファイルに使用するメタデータを指定できます。 このメタデータは、言語エンジンによって、そのボキャブラリの展開に使用され、音声認識の精度が大幅に向上します。  希望の出力ファイルを指定することもできます。

マニフェスト ファイルを使用して一度に複数のメディア ファイルを処理できます。

詳細については、「 [Azure Media Indexer 用のタスク プリセット](https://msdn.microsoft.com/library/dn783454.aspx)」をご覧ください。

## <a name="index-an-asset"></a>資産のインデックス作成
次のメソッドは、資産としてメディア ファイルをアップロードし、ジョブを作成して資産のインデックスを作成します。

構成ファイルが指定されていない場合、メディア ファイルのインデックスは、既定のすべての設定を使用して作成されます。

```csharp
    static bool RunIndexingJob(string inputMediaFilePath, string outputFolder, string configurationFile = "")
    {
        // Create an asset and upload the input media file to storage.
        IAsset asset = CreateAssetAndUploadSingleFile(inputMediaFilePath,
            "My Indexing Input Asset",
            AssetCreationOptions.None);

        // Declare a new job.
        IJob job = _context.Jobs.Create("My Indexing Job");

        // Get a reference to the Azure Media Indexer.
        string MediaProcessorName = "Azure Media Indexer";
        IMediaProcessor processor = GetLatestMediaProcessorByName(MediaProcessorName);

        // Read configuration from file if specified.
        string configuration = string.IsNullOrEmpty(configurationFile) ? "" : File.ReadAllText(configurationFile);

        // Create a task with the encoding details, using a string preset.
        ITask task = job.Tasks.AddNew("My Indexing Task",
            processor,
            configuration,
            TaskOptions.None);

        // Specify the input asset to be indexed.
        task.InputAssets.Add(asset);

        // Add an output asset to contain the results of the job.
        task.OutputAssets.AddNew("My Indexing Output Asset", AssetCreationOptions.None);

        // Use the following event handler to check job progress.  
        job.StateChanged += new EventHandler<JobStateChangedEventArgs>(StateChanged);

        // Launch the job.
        job.Submit();

        // Check job execution and wait for job to finish.
        Task progressJobTask = job.GetExecutionProgressTask(CancellationToken.None);
        progressJobTask.Wait();

        // If job state is Error, the event handling
        // method for job progress should log errors.  Here we check
        // for error state and exit if needed.
        if (job.State == JobState.Error)
        {
            Console.WriteLine("Exiting method due to job error.");
            return false;
        }

        // Download the job outputs.
        DownloadAsset(task.OutputAssets.First(), outputFolder);

        return true;
    }

    static IAsset CreateAssetAndUploadSingleFile(string filePath, string assetName, AssetCreationOptions options)
    {
        IAsset asset = _context.Assets.Create(assetName, options);

        var assetFile = asset.AssetFiles.Create(Path.GetFileName(filePath));
        assetFile.Upload(filePath);

        return asset;
    }

    static void DownloadAsset(IAsset asset, string outputDirectory)
    {
        foreach (IAssetFile file in asset.AssetFiles)
        {
            file.Download(Path.Combine(outputDirectory, file.Name));
        }
    }

    static IMediaProcessor GetLatestMediaProcessorByName(string mediaProcessorName)
    {
        var processor = _context.MediaProcessors
        .Where(p => p.Name == mediaProcessorName)
        .ToList()
        .OrderBy(p => new Version(p.Version))
        .LastOrDefault();

        if (processor == null)
            throw new ArgumentException(string.Format("Unknown media processor",
                                                       mediaProcessorName));

        return processor;
    }  
```

<!-- __ -->
### <a id="output_files"></a>出力ファイル
既定では、ジョブのインデックスを作成すると、次の出力ファイルが生成されます。 ファイルは、最初の出力資産に格納されます。

複数の入力メディア ファイルがある場合、インデクサーはジョブの出力に対して "JobResult.txt" という名前のマニフェスト ファイルを生成します。 各入力メディア ファイルでは、結果として得られる AIB、SAMI、TTML、WebVTT、およびキーワード ファイルには順に番号が振られ、"エイリアス" を使用して名前が付けられます。

| ファイル名 | 説明 |
| --- | --- |
| **InputFileName.aib** |オーディオ インデックス BLOB ファイル。 <br/><br/> オーディオ インデックス BLOB (AIB) ファイルは、フルテキスト検索を使用して Microsoft SQL server で検索できるバイナリ ファイルです。  AIB ファイルは、各単語に豊富な代替候補を含み、より充実した検索を実現しているため、単純なキャプション ファイルよりも強力です。 <br/> <br/>Microsoft SQL server 2008 以降を実行しているマシンに Indexer SQL アドオンのインストールする必要があります。 Microsoft SQL サーバーのフル テキスト検索を使用して、AIB を検索すると、WAMI によって生成されたクローズド キャプション ファイルを検索するよりも正確な検索結果が得られます。 これは、クローズド キャプション ファイルがオーディオの各セグメントに信頼度が最上位の単語を含んでいるのに対し、AIB には代替候補として似ているサウンドの単語が含まれているためです。 話された単語の検索が最も重要な場合は、AIB を Microsoft SQL Server と組み合わせて使用することをお勧めします。<br/><br/> アドオンをダウンロードするには、<a href="https://aka.ms/indexersql">[Azure メディア インデクサー SQL アドオン]</a> をクリックします。 <br/><br/>Apache Lucene/Solr などの他の検索エンジンを使用してクローズド キャプションとキーワードの XML ファイルに基づいたビデオをインデックスすることも可能ですが、検索精度は低くなります。 |
| **InputFileName.smi**<br/>**InputFileName.ttml**<br/>**InputFileName.vtt** |SAMI、TTML、および WebVTT 形式のクローズド キャプション (CC) ファイル<br/><br/>オーディオとビデオ ファイルを聴覚障がいを持つユーザーにアクセスできるようにするために使用できます。<br/><br/>クローズド キャプション ファイルには、<b>Recognizability</b> と呼ばれるタグが含まれています。これは、ソース ビデオ内の音声がどれくらい認識可能であるかに基づいて、インデックス作成ジョブを評価します。  <b>Recognizability</b> の値を使用して、出力ファイルの利用価値を検査することができます。 低いスコアは、オーディオの品質が良好ではないために、インデックスの作成結果が良好ではないという意味です。 |
| **InputFileName.kw.xml<br/>InputFileName.info** |キーワードと情報ファイル。 <br/><br/>キーワードのファイルは、頻度とオフセットの情報を含む、音声コンテンツから抽出されたキーワードを含む XML ファイルです。 <br/><br/>情報ファイルは、認識された各用語に関する詳細な情報が含まれるプレーンテキスト ファイルです。 最初の行は特殊で、認識度スコアを含みます。 後続の各行は、開始時刻、終了時刻、言葉/言い回し、信頼度データのタブ区切り一覧になります。 時間は秒単位で、信頼度は 0 ～ 1 の数値として指定されます。 <br/><br/>行の例:"1.20    1.45    word    0.67" <br/><br/>これらのファイルは、音声分析を実行する、Bing、Google または Microsoft SharePoint などの検索エンジンに公開してメディア ファイルをより検索しやすくする、または関連性の高い広告を配信するなど、さまざまな目的で使用できます。 |
| **JobResult.txt** |出力マニフェストは、複数のファイルのインデックスを作成する場合にのみ存在します。次の情報が含まれています。<br/><br/><table border="1"><tr><th>InputFile</th><th>エイリアス</th><th>MediaLength</th><th>Error</th></tr><tr><td>a.mp4</td><td>Media_1</td><td>300</td><td>0</td></tr><tr><td>b.mp4</td><td>Media_2</td><td>0</td><td>3000</td></tr><tr><td>c.mp4</td><td>Media_3</td><td>600</td><td>0</td></tr></table><br/> |

すべての入力メディアのインデックスが正常に作成されない場合は、インデックス作成ジョブはエラー コード 4000 を表示して失敗します。 詳細については、 [エラー コード](#error_codes)をご覧ください

## <a name="index-multiple-files"></a>複数のファイルのインデックス
次のメソッドは、資産として複数のメディア ファイルをアップロードし、ジョブを作成してバッチ内のすべてのファイルのインデックスを作成します。

".lst" 拡張子のマニフェスト ファイルが作成され、資産にアップロードします。 マニフェスト ファイルには、すべての資産ファイルの一覧が含まれています。 詳細については、「 [Azure Media Indexer 用のタスク プリセット](https://msdn.microsoft.com/library/dn783454.aspx)」をご覧ください。

```csharp
    static bool RunBatchIndexingJob(string[] inputMediaFiles, string outputFolder)
    {
        // Create an asset and upload to storage.
        IAsset asset = CreateAssetAndUploadMultipleFiles(inputMediaFiles,
            "My Indexing Input Asset - Batch Mode",
            AssetCreationOptions.None);

        // Create a manifest file that contains all the asset file names and upload to storage.
        string manifestFile = "input.lst";            
        File.WriteAllLines(manifestFile, asset.AssetFiles.Select(f => f.Name).ToArray());
        var assetFile = asset.AssetFiles.Create(Path.GetFileName(manifestFile));
        assetFile.Upload(manifestFile);

        // Declare a new job.
        IJob job = _context.Jobs.Create("My Indexing Job - Batch Mode");

        // Get a reference to the Azure Media Indexer.
        string MediaProcessorName = "Azure Media Indexer";
        IMediaProcessor processor = GetLatestMediaProcessorByName(MediaProcessorName);

        // Read configuration.
        string configuration = File.ReadAllText("batch.config");

        // Create a task with the encoding details, using a string preset.
        ITask task = job.Tasks.AddNew("My Indexing Task - Batch Mode",
            processor,
            configuration,
            TaskOptions.None);

        // Specify the input asset to be indexed.
        task.InputAssets.Add(asset);

        // Add an output asset to contain the results of the job.
        task.OutputAssets.AddNew("My Indexing Output Asset - Batch Mode", AssetCreationOptions.None);

        // Use the following event handler to check job progress.  
        job.StateChanged += new EventHandler<JobStateChangedEventArgs>(StateChanged);

        // Launch the job.
        job.Submit();

        // Check job execution and wait for job to finish.
        Task progressJobTask = job.GetExecutionProgressTask(CancellationToken.None);
        progressJobTask.Wait();

        // If job state is Error, the event handling
        // method for job progress should log errors.  Here we check
        // for error state and exit if needed.
        if (job.State == JobState.Error)
        {
            Console.WriteLine("Exiting method due to job error.");
            return false;
        }

        // Download the job outputs.
        DownloadAsset(task.OutputAssets.First(), outputFolder);

        return true;
    }

    private static IAsset CreateAssetAndUploadMultipleFiles(string[] filePaths, string assetName, AssetCreationOptions options)
    {
        IAsset asset = _context.Assets.Create(assetName, options);

        foreach (string filePath in filePaths)
        {
            var assetFile = asset.AssetFiles.Create(Path.GetFileName(filePath));
            assetFile.Upload(filePath);
        }

        return asset;
    }
```

### <a name="partially-succeeded-job"></a>部分的に成功したジョブ
すべての入力メディアのインデックスが正常に作成されない場合は、インデックス作成ジョブはエラー コード 400 を表示して失敗します。 詳細については、 [エラー コード](#error_codes)をご覧ください

(成功したジョブの場合) と同じ出力が生成されます。 エラー列の値に基づいて、出力マニフェスト ファイルで、入力ファイルが失敗したかどうかを参照します。 失敗した入力ファイルでは、結果として得られる AIB、SAMI、TTML、WebVTT、およびキーワード ファイルは生成されません。

### <a id="preset"></a> Azure Media Indexer 用のタスク プリセット
Azure Media Indexer からの処理は、オプションのタスク プリセットをタスクと共に指定することでカスタマイズできます。  次の表は、この configuration xml の形式の説明です。

| Name | 必須 | 説明 |
| --- | --- | --- |
| **input** |false |インデックスの対象となるアセット ファイル。</p><p>Azure Media Indexer は、メディア ファイル形式としてMP4、WMV、MP3、M4A、WMA、AAC、WAV をサポートしています。</p><p>ファイル名は、**input** 要素の **name** 属性または **list** 属性に指定できます (以下の例を参照)。インデックスの対象となるアセット ファイルを指定しなかった場合は、プライマリ ファイルが選択されます。 プライマリ資産ファイルが設定されていない場合は、入力資産の 1 つ目のファイルのインデックスが作成されます。</p><p>資産ファイル名を明示的に指定するには、次を実行します。<br/>`<input name="TestFile.wmv">`<br/><br/>複数の資産ファイルのインデックスを一度に作成することもできます (最大 10 ファイル)。 これを行うには、次の手順を実行します。<br/><br/><ol class="ordered"><li><p>テキスト ファイル (マニフェスト ファイル) を作成し、.lst という拡張子を指定します。 </p></li><li><p>入力資産に含まれるすべての資産ファイルの名前をこのマニフェスト ファイルに追加します。 </p></li><li><p>マニフェスト ファイルを資産に追加 (アップロード) します。  </p></li><li><p>マニフェスト ファイルの名前を input の list 属性に指定します。<br/>`<input list="input.lst">`</li></ol><br/><br/>注:マニフェスト ファイルに 10 を超えるファイルを追加すると、インデックス作成ジョブが 2006 エラー コードで失敗します。 |
| **metadata** |false |語彙アダプテーション用に指定する資産ファイルのメタデータ。  標準的ではない語彙 (固有名詞など) をインデクサーに認識させる必要があるときに使用します。<br/>`<metadata key="..." value="..."/>` <br/><br/>事前定義済みの**キー**に対して**値**を指定できます。 現在サポートされているキーは<br/><br/>"title" と "description" です。これは、語彙アダプテーションで対象ジョブの言語モデルを微調整し、音声認識の精度を高める目的で使用します。  インターネット検索機能は、これらの値を足掛かりとしてコンテキストに合ったテキスト ドキュメントを検索し、そのコンテンツを使って、インデックス作成タスクの過程で用いられる内部辞書を補強します。<br/>`<metadata key="title" value="[Title of the media file]" />`<br/>`<metadata key="description" value="[Description of the media file] />"` |
| **features** <br/><br/> バージョン 1.2 で追加。 現時点でサポートされている機能は、音声認識 ("ASR") のみです。 |false |音声認識機能には、次の設定キーがあります。<table><tr><th><p>キー</p></th>        <th><p>説明</p></th><th><p>値の例</p></th></tr><tr><td><p>言語</p></td><td><p>マルチメディア ファイル内で認識される自然言語。</p></td><td><p>English、Spanish</p></td></tr><tr><td><p>CaptionFormats</p></td><td><p>出力キャプション形式をセミコロンで区切ったリスト (存在する場合)</p></td><td><p>ttml;sami;webvtt</p></td></tr><tr><td><p>GenerateAIB</p></td><td><p>(SQL Server とお客様の Indexer IFilter で使用するために) AIB ファイルが必要かどうかを指定するブール値のフラグ。  詳細については、「 <a href="https://azure.microsoft.com/blog/2014/11/03/using-aib-files-with-azure-media-indexer-and-sql-server/">AIB ファイルを Azure Media Indexer および SQL Server で使用する</a>」をご覧ください。</p></td><td><p>True、False</p></td></tr><tr><td><p>GenerateKeywords</p></td><td><p>キーワード XML ファイルが必要かどうかを指定するブール型のフラグ。</p></td><td><p>True、False。 </p></td></tr><tr><td><p>ForceFullCaption</p></td><td><p>(信頼レベルに関係なく) フル キャプションを強制するかどうかを指定するブール型のフラグ。  </p><p>既定値は false です。この場合、信頼レベルが 50% 未満の語句は最終的なキャプションの出力から除外され、省略記号 ("...") で置き換えられます。  省略記号は、キャプションの品質管理や監査に用いられます。</p></td><td><p>True、False。 </p></td></tr></table> |

### <a id="error_codes"></a>エラー コード
エラーが発生した場合、Azure Media Indexer は、次のいずれかのエラー コードを返します。

| コード | Name | 考えられる原因 |
| --- | --- | --- |
| 2000 |構成が無効です |構成が無効です |
| 2001 |無効な入力資産 |入力資産、または空の資産がありません。 |
| 2002 |マニフェストが無効です |マニフェストが空か、マニフェストに無効な項目が含まれています。 |
| 2003 |メディア ファイルのダウンロードに失敗しました |マニフェスト ファイルでの URL が無効です。 |
| 2004 |サポートされていないプロトコル |メディア URL のプロトコルはサポートされません。 |
| 2005 |サポートされていないファイルの種類 |入力メディア ファイルの種類がサポートされていません。 |
| 2006 |入力のファイルが多すぎます |入力のマニフェストに 10 個を超えるファイルがあります。 |
| 3000 |メディア ファイルのデコードに失敗しました |メディア コーデックがサポートされていない <br/>or<br/> メディア ファイルが破損している <br/>or<br/> 入力メディアのオーディオ ストリームがありません。 |
| 4000 |バッチのインデックス作成一部成功しました |一部の入力メディア ファイルは、インデックスを付けるできませんでした。 詳細については、「 <a href="#output_files">出力ファイル</a>。 |
| その他 |内部エラー |サポート チームにお問い合わせください。 indexer@microsoft.com |

## <a id="supported_languages"></a>サポートされている言語
現時点では、英語とスペイン語の言語がサポートされています。 詳細については、 [v1.2 リリースのブログ記事](https://azure.microsoft.com/blog/2015/04/13/azure-media-indexer-spanish-v1-2/)をご覧ください。

## <a name="media-services-learning-paths"></a>Media Services のラーニング パス
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>フィードバックの提供
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

## <a name="related-links"></a>関連リンク
[Azure Media Services Analytics の概要](media-services-analytics-overview.md)

[AIB ファイルを Azure Media Indexer および SQL Server で使用する](https://azure.microsoft.com/blog/2014/11/03/using-aib-files-with-azure-media-indexer-and-sql-server/)

[Azure Media Indexer 2 プレビューによるメディア ファイルのインデックス作成](media-services-process-content-with-indexer2.md)

