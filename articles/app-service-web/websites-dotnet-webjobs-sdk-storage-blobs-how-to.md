---
title: "Web ジョブ SDK で Azure Blob Storage を使用する方法"
description: "Web ジョブ SDK で Azure Blob Storage を使用する方法を説明します。 コンテナーに新しい BLOB が表示されたら、プロセスをトリガーし、 有害な BLOB を処理します。"
services: app-service\web, storage
documentationcenter: .net
author: tdykstra
manager: erikre
editor: 
ms.assetid: bf32f919-f7bc-4aaa-916e-461c02f2e26c
ms.service: app-service-web
ms.workload: web
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: article
ms.date: 06/01/2016
ms.author: glenga
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: 968df0fde8b042cdea369e566ecdb62937a3b8ee
ms.lasthandoff: 11/17/2016


---
# <a name="how-to-use-azure-blob-storage-with-the-webjobs-sdk"></a>Web ジョブ SDK で Azure Blob Storage を使用する方法
## <a name="overview"></a>概要
このガイドでは、Azure BLOB が作成または更新されたときにプロセスをトリガーする方法を示す C# コード サンプルについて説明します。 コード サンプルでは [WebJobs SDK](websites-dotnet-webjobs-sdk.md) Version 1.x を使用しています。

BLOB を作成する方法を示すコード サンプルについては、「 [Web ジョブ SDK を使用して Azure Queue Storage を操作する方法](websites-dotnet-webjobs-sdk-storage-queues-how-to.md)」をご覧ください 

このガイドは、Visual Studio で[ストレージ アカウント](websites-dotnet-webjobs-sdk-get-started.md)または[複数のストレージ アカウント](https://github.com/Azure/azure-webjobs-sdk/blob/master/test/Microsoft.Azure.WebJobs.Host.EndToEndTests/MultipleStorageAccountsEndToEndTests.cs)を指定する接続文字列を使って Web ジョブ プロジェクトを作成する方法を理解していることを前提としています。

## <a id="trigger"></a> BLOB が作成または更新されたときに、関数をトリガーする方法
このセクションでは、 `BlobTrigger` 属性を使用する方法を示しています。 

> [!NOTE]
> WebJobs SDK は、ログ ファイルをスキャンし、新しい BLOB や変更された BLOB を監視します。 このプロセスはリアルタイムではありません。関数は、BLOB が作成されてから数分またはそれ以上経過しないとトリガーされない可能性があります。 また、[ストレージ ログの作成は "ベスト エフォート"](https://msdn.microsoft.com/library/azure/hh343262.aspx) ベースで行われます。そのため、すべてのイベントがキャプチャされる保証はありません。 ある条件下では、ログが欠落する可能性があります。 BLOB トリガーの速度と信頼性の制限がアプリケーションで許容されない場合は、BLOB を作成する際にキュー メッセージを作成し、BLOB を処理する関数で `BlobTrigger` 属性ではなく [QueueTrigger](websites-dotnet-webjobs-sdk-storage-queues-how-to.md#trigger) 属性を使用することをお勧めします。
> 
> 

### <a name="single-placeholder-for-blob-name-with-extension"></a>拡張子を持つ BLOB 名の 1 つのプレース ホルダー
次のコード サンプルは、"*入力*" コンテナーに表示されるテキスト BLOB を "*出力*" コンテナーにコピーします。

        public static void CopyBlob([BlobTrigger("input/{name}")] TextReader input,
            [Blob("output/{name}")] out string output)
        {
            output = input.ReadToEnd();
        }

属性コンストラクターが、コンテナー名と BLOB 名のプレース ホルダーを指定する文字列パラメーターを取得します。 たとえば、*Blob1.txt* という名前の BLOB が "*入力*" コンテナーで作成されると、*Blob1.txt* という名前の BLOB が "*出力*" コンテナーに作成されます。 

次のコード サンプルに示すように、BLOB 名のプレース ホルダーを持つ名前のパターンを指定できます。

        public static void CopyBlob([BlobTrigger("input/original-{name}")] TextReader input,
            [Blob("output/copy-{name}")] out string output)
        {
            output = input.ReadToEnd();
        }

このコードは「元-」で始まる名前の BLOB のみをコピーします。 たとえば、"*入力*" コンテナーの *original-Blob1.txt* は、"*出力*" コンテナーの *copy-Blob1.txt* にコピーされます。

名前に波括弧がある BLOB 名に新しいパターンを指定する必要がある場合は、波括弧を二重にします。 たとえば、以下のような名前を持つ *イメージ* コンテナーに blob を見つける場合は、

        {20140101}-soundfile.mp3

これをパターンとして使用します。

        images/{{20140101}}-{name}

この例では、*name* プレース ホルダーの値は、*soundfile.mp3* になります。 

### <a name="separate-blob-name-and-extension-placeholders"></a>別の BLOB 名と拡張子のプレース ホルダー
次のコード サンプルでは、"*入力*" コンテナーに表示される BLOB が "*出力*" コンテナーにコピーされる際にファイル拡張子を変更します。 コードは、"*入力*" BLOB の拡張子をログに記録し、"*出力*" BLOB の拡張子を *.txt* に設定します。

        public static void CopyBlobToTxtFile([BlobTrigger("input/{name}.{ext}")] TextReader input,
            [Blob("output/{name}.txt")] out string output,
            string name,
            string ext,
            TextWriter logger)
        {
            logger.WriteLine("Blob name:" + name);
            logger.WriteLine("Blob extension:" + ext);
            output = input.ReadToEnd();
        }

## <a id="types"></a> BLOB にバインドすることができる種類
次の種類の `BlobTrigger` 属性を使用できます。

* `string`
* `TextReader`
* `Stream`
* `ICloudBlob`
* `CloudBlockBlob`
* `CloudPageBlob`
* `CloudBlobContainer`
* `CloudBlobDirectory`
* `IEnumerable<CloudBlockBlob>`
* `IEnumerable<CloudPageBlob>`
*  [ICloudBlobStreamBinder](#icbsb) 

Azure のストレージ アカウントを直接操作する場合は、メソッド シグネチャに `CloudStorageAccount` パラメーターを追加することもできます。

例については、 [GitHub.com の azure-webjobs-sdk リポジトリにある BLOB バインド コード](https://github.com/Azure/azure-webjobs-sdk/blob/master/test/Microsoft.Azure.WebJobs.Host.EndToEndTests/BlobBindingEndToEndTests.cs)を参照してください。

## <a id="string"></a> 文字列にバインドすることによってテキスト BLOB のコンテンツを取得する
テキスト BLOB がある場合は、  `BlobTrigger`を `string` パラメーターに適用できます。 次のコード サンプルでは、テキスト BLOB を `logMessage` という名前の `string` パラメーターにバインドします。 関数は、そのパラメーターを使用して Web ジョブ SDK のダッシュボードに、BLOB の内容を記述します。 

        public static void WriteLog([BlobTrigger("input/{name}")] string logMessage,
            string name, 
            TextWriter logger)
        {
             logger.WriteLine("Blob name: {0}", name);
             logger.WriteLine("Content:");
             logger.WriteLine(logMessage);
        }

## <a id="icbsb"></a>ICloudBlobStreamBinder を使用してシリアル化した BLOB の内容を取得する
次のコード サンプルでは、`ICloudBlobStreamBinder` を実装するクラスを使用して `BlobTrigger` 属性を有効化して BLOB を `WebImage` 型にバインドします。

        public static void WaterMark(
            [BlobTrigger("images3/{name}")] WebImage input,
            [Blob("images3-watermarked/{name}")] out WebImage output)
        {
            output = input.AddTextWatermark("WebJobs SDK", 
                horizontalAlign: "Center", verticalAlign: "Middle",
                fontSize: 48, opacity: 50);
        }
        public static void Resize(
            [BlobTrigger("images3-watermarked/{name}")] WebImage input,
            [Blob("images3-resized/{name}")] out WebImage output)
        {
            var width = 180;
            var height = Convert.ToInt32(input.Height * 180 / input.Width);
            output = input.Resize(width, height);
        }

`WebImage` バインド コードは、`ICloudBlobStreamBinder` から派生した `WebImageBinder` クラスに提供されます。

        public class WebImageBinder : ICloudBlobStreamBinder<WebImage>
        {
            public Task<WebImage> ReadFromStreamAsync(Stream input, 
                System.Threading.CancellationToken cancellationToken)
            {
                return Task.FromResult<WebImage>(new WebImage(input));
            }
            public Task WriteToStreamAsync(WebImage value, Stream output,
                System.Threading.CancellationToken cancellationToken)
            {
                var bytes = value.GetBytes();
                return output.WriteAsync(bytes, 0, bytes.Length, cancellationToken);
            }
        }

## <a name="getting-the-blob-path-for-the-triggering-blob"></a>トリガー BLOB の BLOB パスの取得
コンテナー名と、関数をトリガーした BLOB のコンテナー名と BLOB 名を取得するには、関数のシグネチャに `blobTrigger` 文字列パラメーターを含めます。

        public static void WriteLog([BlobTrigger("input/{name}")] string logMessage,
            string name,
            string blobTrigger,
            TextWriter logger)
        {
             logger.WriteLine("Full blob path: {0}", blobTrigger);
             logger.WriteLine("Content:");
             logger.WriteLine(logMessage);
        }


## <a id="poison"></a> 有害な BLOB の処理方法
`BlobTrigger` 関数が失敗した場合、失敗が一時的なエラーによって発生した場合は、SDK は再度関数を呼び出します。 失敗が BLOB のコンテンツによって発生した場合は、BLOB の処理を試みるたびに関数は失敗します。 既定では、SDK は特定の BLOB に対して最大 5 回、関数を呼び出します。 5 回目が失敗すると、SDK はメッセージは、 *webjobs-blobtrigger-poison*という名前のキューにメッセージを追加します。

再試行回数の最大値の設定は変更可能です。 同じ [MaxDequeueCount](websites-dotnet-webjobs-sdk-storage-queues-how-to.md#configqueue) 設定は、有害な BLOB の処理と有害キュー メッセージの処理に使用されます。 

有害な BLOB のキュー メッセージは次のプロパティを持つ JSON オブジェクトです。

* FunctionId (*{WebJob name}*.Functions.*{Function name}* の形式。例: ebJob1.Functions.CopyBlob)
* BLOB の種類 ("BlockBlob" か "PageBlob")
* コンテナー名
* BlobName
* ETag (BLOB のバージョン識別子。たとえば、"0x8D1DC6E70A277EF")

次のコード サンプルでは、 `CopyBlob` 関数は、呼び出されるたびに障害が発生するコードを含んでいます。 SDK は呼び出しを最大試行回数実行すると、メッセージが有害 BLOB キューに作成され、そのメッセージは、 `LogPoisonBlob` 関数によって処理されます。 

        public static void CopyBlob([BlobTrigger("input/{name}")] TextReader input,
            [Blob("textblobs/output-{name}")] out string output)
        {
            throw new Exception("Exception for testing poison blob handling");
            output = input.ReadToEnd();
        }

        public static void LogPoisonBlob(
        [QueueTrigger("webjobs-blobtrigger-poison")] PoisonBlobMessage message,
            TextWriter logger)
        {
            logger.WriteLine("FunctionId: {0}", message.FunctionId);
            logger.WriteLine("BlobType: {0}", message.BlobType);
            logger.WriteLine("ContainerName: {0}", message.ContainerName);
            logger.WriteLine("BlobName: {0}", message.BlobName);
            logger.WriteLine("ETag: {0}", message.ETag);
        }

SDK は、自動的にJSON メッセージを逆シリアル化します。 ここでは `PoisonBlobMessage` クラスです。 

        public class PoisonBlobMessage
        {
            public string FunctionId { get; set; }
            public string BlobType { get; set; }
            public string ContainerName { get; set; }
            public string BlobName { get; set; }
            public string ETag { get; set; }
        }

### <a id="polling"></a> BLOB のポーリング アルゴリズム
Web ジョブ SDK は、アプリケーションの起動時に `BlobTrigger` 属性が指定するすべてのコンテナーをスキャンします。 大容量のストレージ アカウントでは、このスキャンに時間がかかるため、新しい BLOB が見つかり、 `BlobTrigger` 関数が実行されるまでにしばらく時間がかかります。

アプリケーションの起動後に新しいまたは変更された BLOB を検出するために、SDK は定期的に BLOB ストレージ ログを読み取ります。 BLOB のログはバッファーされ、10 分程度ごとに物理的に記述されるので、BLOB が作成されるか更新され、対応する `BlobTrigger` 関数が実行されるにはかなり時間がかかる可能性があります。 

`Blob` 属性を使用して作成する BLOB には例外があります。 Web ジョブ SDK は新しい BLOB を作成すると、一致するすべての `BlobTrigger` 関数に新しい BLOB をすぐに渡します。 そのため、一連の BLOB 入力と BLOB 出力がある場合は、SDK は効率的に処理できます。 ただし、その他の方法で作成または更新された BLOB に対して 低遅延で実行される BLOB 処理関数を使用する場合は、`BlobTrigger` ではなく `QueueTrigger` をお勧めします。

### <a id="receipts"></a> BLOB の配信確認メッセージ
Web ジョブ SDK では、 `BlobTrigger` 関数は同一の新しいまたは更新された BLOB を複数回呼び出しません。 これは *BLOB の配信確認メッセージ* を維持して、特定の BLOB バージョンが処理されているかどうかを判断するためです。

BLOB の配信確認メッセージは、AzureWebJobsStorage 接続文字列が指定した Azure ストレージ アカウントの *azure-webjobs-hosts* という名前のコンテナーに格納されています。 BLOB の配信確認メッセージには次の情報が含まれています。

* BLOB に対して呼び出された関数 ("*{WebJob name}*.Functions.*{Function name}*"。例: "WebJob1.Functions.CopyBlob")
* コンテナーの名前
* BLOB の種類 ("BlockBlob" か "PageBlob")
* BLOB の名前
* ETag (BLOB のバージョン識別子。たとえば、"0x8D1DC6E70A277EF")

BLOB を強制的に再処理する場合は、 *azure-webjobs-hosts* コンテナーからその BLOB の配信確認メッセージを手動で削除します。

## <a id="queues"></a>キューの記事で扱う関連トピック
キュー メッセージによってトリガーされる BLOB 処理の方法、BLOB 処理に固有ではない Web ジョブ SDK のシナリオについては、「 [Web ジョブ SDK を使用して Azure キュー ストレージを操作する方法](websites-dotnet-webjobs-sdk-storage-queues-how-to.md)」をご覧ください 

その記事では、以下のような関連トピックが紹介されています。

* Async 関数
* 複数のインスタンス
* グレースフル シャットダウン
* 関数本体での Web ジョブ SDK 属性の使用
* コード内で SDK の接続文字列を設定する。
* コードの Web ジョブ SDK コンストラクター パラメーター値の設定
* 有害 BLOB を処理するために `MaxDequeueCount` を構成します。
* 手動での関数のトリガー
* ログの書き込み

## <a id="nextsteps"></a> 次のステップ
このガイドでは、Azure BLOB を操作するための一般的なシナリオの処理方法を示すコードのサンプルを提供しました。 Azure Web ジョブ および Web ジョブ SDK の使用方法の詳細については、「 [Azure Web ジョブの推奨リソース](http://go.microsoft.com/fwlink/?linkid=390226)」を参照してください。


