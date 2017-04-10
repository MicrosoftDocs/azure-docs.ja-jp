---
title: "複数のレコメンデーションの一括取得: Machine Learning Recommendations API | Microsoft Docs"
description: "Azure Machine Learning Recommendations - 複数のレコメンデーションの一括取得"
services: cognitive-services
documentationcenter: 
author: luiscabrer
manager: jhubbard
editor: cgronlun
ms.assetid: 325d4922-8a07-4e67-99e0-f513201f14f7
ms.service: cognitive-services
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/28/2016
ms.author: luisca
translationtype: Human Translation
ms.sourcegitcommit: 0af5a4e2139a202c7f62f48c7a7e8552457ae76d
ms.openlocfilehash: e63218d9c882d84342a3992f05e0a8c9f306d9c6


---
# <a name="get-recommendations-in-batches"></a>複数のレコメンデーションの一括取得
> [!NOTE]
> 複数のレコメンデーションを一括で取得することは、レコメンデーションを&1; 件ずつ取得するよりも難易度の高い作業です。 1 つの要求でレコメンデーションを取得する方法に関する推奨事項については、API を確認してください。
> 
> [Item-to-Item レコメンデーション](https://westus.dev.cognitive.microsoft.com/docs/services/Recommendations.V4.0/operations/56f30d77eda5650db055a3d4)<br>
> [User-to-Item レコメンデーション](https://westus.dev.cognitive.microsoft.com/docs/services/Recommendations.V4.0/operations/56f30d77eda5650db055a3dd)
> 
> バッチ スコアリングが正しく機能するのは、2016 年 7 月 21 日より後に作成されたビルドだけです。
> 
> 

複数のアイテムのレコメンデーションを一度に取得しなければならない場合があります。 たとえば何かの都合で、レコメンデーションのキャッシュを作成したり、取得するレコメンデーションの種類に関する分析を行ったりするときです。

バッチ スコアリング操作は非同期の処理です。 要求を送信し、操作の完了を待って、その結果を収集する必要があります。  

その具体的な手順は次のとおりです。

1. Azure Storage コンテナーを作成します (まだ存在しない場合)。
2. 個々のレコメンデーション要求を記述した入力ファイルを Azure BLOB ストレージにアップロードします。
3. スコアリング バッチ ジョブを開始します。
4. 非同期操作の完了を待機します。
5. 操作が完了したら、BLOB ストレージから結果を収集します。

以上の各手順を個別に見ていきましょう。

## <a name="create-a-storage-container-if-you-dont-have-one-already"></a>Storage コンテナーを作成する (まだ存在しない場合)
[Azure ポータル](https://portal.azure.com) に移動し、ストレージ アカウントをお持ちでない場合は新しいストレージ アカウントを作成します。 そのためには、**[新規]** > **[データ** + **ストレージ]** > **[ストレージ アカウント]** の順に移動します。

ストレージ アカウントの準備が整ったら、バッチ実行の入力と出力の保存場所となる BLOB コンテナーを作成する必要があります。

個々のレコメンデーション要求を記述した入力ファイル (ここでは input.json と呼びます) を BLOB ストレージにアップロードします。
コンテナーを作成した後、Recommendations Service から実行する各要求を記述したファイルをアップロードする必要があります。

1 回のバッチで実行できるのは、特定のビルドからの&1; 種類の要求だけです。 次のセクションで、この情報を定義する方法について説明します。 ここでは、特定のビルドからアイテムのレコメンデーションを実行すると仮定しましょう。 そこで、入力ファイルには、個々の要求の入力情報 (このケースではシード アイテム) を記述します。

次に示したのは input.json ファイルの記述例です。

    {
      "requests": [
      { "SeedItems": [ "C9F-00163", "FKF-00689" ] },
      { "SeedItems": [ "F34-03453" ] },
      { "SeedItems": [ "D16-3244" ] },
      { "SeedItems": [ "C9F-00163", "FKF-00689" ] },
      { "SeedItems": [ "F43-01467" ] },
      { "SeedItems": [ "BD5-06013" ] },
      { "SeedItems": [ "P45-00163", "FKF-00689" ] },
      { "SeedItems": [ "C9A-69320" ] }
      ]
    }

ご覧のように、このファイルは JSON 形式になっています。個々の要求には、レコメンデーション要求を送信するうえで必要な情報が記述されています。 満たすべき要求に関して同様の JSON ファイルを作成し、先ほど Blob Storage に作成したコンテナーにそれをコピーします。

## <a name="kick-start-the-batch-job"></a>バッチ ジョブを開始する
次の手順は、新しいバッチ ジョブの送信です。 詳細については、 [API リファレンス](https://westus.dev.cognitive.microsoft.com/docs/services/Recommendations.V4.0/)を確認してください。

入力ファイルや出力ファイル、エラー ファイルの保存先は、API の要求本文で定義する必要があります。 また、それらの場所にアクセスするために必要な資格情報も定義する必要があります。 加えてバッチ全体に適用するいくつかのパラメーターを指定する必要があります (要求するレコメンデーションの種類、使用するモデル/ビルド、1 回の呼び出しで取得する結果の件数など)。

要求本文の具体的な記述例を次に示します。

    {
      "input": {
        "authenticationType": "PublicOrSas",
        "baseLocation": "https://mystorage1.blob.core.windows.net/",
        "relativeLocation": "container1/batchInput.json",
        "sasBlobToken": "?sv=2015-07_restofToken_…4Z&sp=rw"
      },
      "output": {
        "authenticationType": "PublicOrSas",
        "baseLocation": "https://mystorage1.blob.core.windows.net/",
        "relativeLocation": "container1/batchOutput.json ",
        "sasBlobToken": "?sv=2015-07_restofToken_…4Z&sp=rw"
      },
      "error": {
        "authenticationType": "PublicOrSas",
        "baseLocation": "https://mystorage1.blob.core.windows.net/",
        "relativeLocation": "container1/errors.txt",
        "sasBlobToken": "?sv=2015-07_restofToken_…4Z&sp=rw"
      },
      "job": {
        "apiName": "ItemRecommend",
        "modelId": "9ac12a0a-1add-4bdc-bf42-c6517942b3a6",
        "buildId": 1015703,
        "numberOfResults": 10,
        "includeMetadata": true,
        "minimalScore": 0.0
      }
    }

注意すべき重要な点がいくつかあります。

* 現在 **authenticationType** は必ず **PublicOrSas** に設定する必要があります。
* Blob Storage アカウントからの読み取りと Blob Storage アカウントへの書き込みを Recommendations API に許可するためには、Shared Access Signature (SAS) トークンを取得する必要があります。 SAS トークンを生成する方法の詳細については、 [Recommendations API](../storage/storage-dotnet-shared-access-signature-part-1.md)に関するページを参照してください。
* 現在サポートされている **apiName** は **ItemRecommend** のみです。アイテムとアイテムの結び付きに基づく Item-to-Item レコメンデーションに使用されます。 バッチ処理では現在、User-to-Item レコメンデーションはサポートされていません。

## <a name="wait-for-the-asynchronous-operation-to-finish"></a>非同期操作が終了するまで待機します。
バッチ操作を開始すると、その操作を追跡するうえで必要な情報を伝える Operation-Location ヘッダーが応答で返されます。
バッチ操作の追跡には、ビルド操作を追跡するときと同様、 [Retrieve Operation Status API](https://westus.dev.cognitive.microsoft.com/docs/services/Recommendations.V4.0/operations/56f30d77eda5650db055a3da)を使用します。

## <a name="get-the-results"></a>結果を取得する
操作が完了したら (エラーが発生しなかった場合)、出力 Blob Storage から結果を収集することができます。

出力は、次の例のようになります。 簡潔にするために、この例では&2; つの要求のみから成るバッチの結果を示しています。

    {
      "results":
      [   
        {
          "request": { "seedItems": [ "DAF-00500", "P3T-00003" ] },
          "recommendations": [
            {
              "items": [
                {
                  "itemId": "F5U-00011",
                  "name": "L2 Ethernet Adapter-Win8Pro SC EN/XD/XX Hdwr",
                  "metadata": ""
                }
              ],
              "rating": 0.722,
              "reasoning": [ "People who like the selected items also like 'L2 Ethernet Adapter-Win8Pro SC EN/XD/XX Hdwr'" ]
            },
            {
              "items": [
                {
                  "itemId": "G5Y-00001",
                  "name": "Docking Station for Srf Pro/Pro2 SC EN/XD/ES Hdwr",
                  "metadata": ""
                }
              ],
              "rating": 0.718,
              "reasoning": [ "People who like the selected items also like 'Docking Station for Srf Pro/Pro2 SC EN/XD/ES Hdwr'" ]
            }
          ]
        },
        {
          "request": { "seedItems": [ "C9F-00163" ] },
          "recommendations": [
            {
              "items": [
                {
                  "itemId": "C9F-00172",
                  "name": "Nokia 2K Shell for Nokia Lumia 630/635 - Green",
                  "metadata": ""
                }
              ],
              "rating": 0.649,
              "reasoning": [ "People who like 'MOZO Flip Cover for Nokia Lumia 635 - White' also like 'Nokia 2K Shell for Nokia Lumia 630/635 - Green'" ]
            },
            {
              "items": [
                {
                  "itemId": "C9F-00171",
                  "name": "Nokia 2K Shell for Nokia Lumia 630/635 - Orange",
                  "metadata": ""
                }
              ],
              "rating": 0.647,
              "reasoning": [ "People who like 'MOZO Flip Cover for Nokia Lumia 635 - White' also like 'Nokia 2K Shell for Nokia Lumia 630/635 - Orange'" ]
            },
            {
              "items": [
                {
                  "itemId": "C9F-00170",
                  "name": "Nokia 2K Shell for Nokia Lumia 630/635 - Yellow",
                  "metadata": ""
                }
              ],
              "rating": 0.646,
              "reasoning": [ "People who like 'MOZO Flip Cover for Nokia Lumia 635 - White' also like 'Nokia 2K Shell for Nokia Lumia 630/635 - Yellow'" ]
            }       
          ]
        }
    ]}


## <a name="learn-about-the-limitations"></a>制限事項について
* 一度に呼び出すことのできるバッチ ジョブは各サブスクリプションにつき&1; つだけです。
* バッチ ジョブの入力ファイルは 2 MB 以下にする必要があります。




<!--HONumber=Dec16_HO2-->


