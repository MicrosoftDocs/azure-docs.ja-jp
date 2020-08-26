---
title: ジョブの結果をダウンロードする - Azure Media Services
description: この記事では、ジョブの結果をダウンロードする方法について説明します。
services: media-services
documentationcenter: ''
author: IngridAtMicrosoft
manager: femila
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: article
ms.date: 03/04/2020
ms.author: inhenkel
ms.openlocfilehash: 0a2e4d1ac5cf0945ab75e58bb42d23d10d6c89aa
ms.sourcegitcommit: d661149f8db075800242bef070ea30f82448981e
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/19/2020
ms.locfileid: "88607240"
---
# <a name="download-the-results-of-a-job"></a>ジョブの結果をダウンロードする

Azure Media Services では、ご利用のビデオを処理するとき (エンコードや分析など)、自分の[ジョブ](transforms-jobs-concept.md)の結果を格納するために出力[アセット](assets-concept.md)を作成する必要があります。 Media Service と Storage API を使用して、それらの結果をローカル フォルダーにダウンロードすることができます。 

この記事では、Java と .NET SDK を使用して結果をダウンロードする方法について説明します。

## <a name="java"></a>Java

```java
/**
    * Use Media Service and Storage APIs to download the output files to a local folder
    * @param manager               The entry point of Azure Media resource management
    * @param resourceGroup         The name of the resource group within the Azure subscription
    * @param accountName           The Media Services account name
    * @param assetName             The asset name
    * @param outputFolder          The output folder for downloaded files.
    * @throws StorageException
    * @throws URISyntaxException
    * @throws IOException
    */
private static void downloadResults(MediaManager manager, String resourceGroup, String accountName,
        String assetName, File outputFolder) throws StorageException, URISyntaxException, IOException {
    ListContainerSasInput parameters = new ListContainerSasInput()
        .withPermissions(AssetContainerPermission.READ)
        .withExpiryTime(DateTime.now().plusHours(1));
    AssetContainerSas assetContainerSas = manager.assets()
        .listContainerSasAsync(resourceGroup, accountName, assetName, parameters).toBlocking().first();
    
    String strSas = assetContainerSas.assetContainerSasUrls().get(0);
    CloudBlobContainer container = new CloudBlobContainer(new URI(strSas));

    File directory = new File(outputFolder, assetName);
    directory.mkdir();

    ArrayList<ListBlobItem>  blobs = container.listBlobsSegmented(null, true, EnumSet.noneOf(BlobListingDetails.class), 200, null, null, null).getResults();

    for (ListBlobItem blobItem: blobs) {
        if (blobItem instanceof CloudBlockBlob) {
            CloudBlockBlob blob = (CloudBlockBlob)blobItem;
            File downloadTo = new File(directory, blob.getName());

            blob.downloadToFile(downloadTo.getPath());
        }
    }

    System.out.println("Download complete.");
}
```

完全なコード サンプルを参照してください: [EncodingWithMESPredefinedPreset](https://github.com/Azure-Samples/media-services-v3-java/blob/master/VideoEncoding/EncodingWithMESPredefinedPreset/src/main/java/sample/EncodingWithMESPredefinedPreset.java)

## <a name="net"></a>.NET

```csharp
/// <summary>
/// Use Media Service and Storage APIs to download the output files to a local folder
/// </summary>
/// <param name="client">The Media Services client.</param>
/// <param name="resourceGroupName">The name of the resource group within the Azure subscription.</param>
/// <param name="accountName">The Media Services account name.</param>
/// <param name="assetName">The asset name.</param>
/// <param name="resultsFolder">The output folder name for downloaded files.</param>
/// <returns>A task.</returns>
private async static Task DownloadResults(IAzureMediaServicesClient client, string resourceGroupName, string accountName, string assetName, string resultsFolder)
{
    AssetContainerSas assetContainerSas = client.Assets.ListContainerSas(
                    resourceGroupName, 
                    accountName, 
                    assetName,
                    permissions: AssetContainerPermission.Read, 
                    expiryTime: DateTime.UtcNow.AddHours(1).ToUniversalTime()
                    );

    Uri containerSasUrl = new Uri(assetContainerSas.AssetContainerSasUrls.FirstOrDefault());
    CloudBlobContainer container = new CloudBlobContainer(containerSasUrl);

    string directory = Path.Combine(resultsFolder, assetName);
    Directory.CreateDirectory(directory);

    Console.WriteLine("Downloading results to {0}.", directory);
    
    var blobs = container.ListBlobsSegmentedAsync(null,true, BlobListingDetails.None,200,null,null,null).Result;
    
    foreach (var blobItem in blobs.Results)
    {
        if (blobItem is CloudBlockBlob)
        {
            CloudBlockBlob blob = blobItem as CloudBlockBlob;
            string filename = Path.Combine(directory, blob.Name);

            await blob.DownloadToFileAsync(filename, FileMode.Create);
        }
    }

    Console.WriteLine("Download complete.");
}
```

完全なコード サンプルを参照してください: [EncodingWithMESPredefinedPreset](https://github.com/Azure-Samples/media-services-v3-dotnet/blob/master/VideoEncoding/EncodingWithMESPredefinedPreset/Program.cs)

## <a name="next-steps"></a>次のステップ

[HTTPS URL からジョブの入力を作成する](job-input-from-http-how-to.md)
