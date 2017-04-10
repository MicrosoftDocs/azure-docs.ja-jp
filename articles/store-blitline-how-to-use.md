---
title: "Blitline を使用した画像処理の方法 - Azure の機能ガイド"
description: "Azure アプリケーション内で Blitline サービスを使用して画像を処理する方法について説明します。"
services: 
documentationcenter: .net
author: blitline-dev
manager: jason@blitline.com
editor: jason@blitline.com
ms.assetid: 6c711248-0e52-4895-ba9e-8395628de924
ms.service: multiple
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/09/2014
ms.author: support@blitline.com
translationtype: Human Translation
ms.sourcegitcommit: 219dcbfdca145bedb570eb9ef747ee00cc0342eb
ms.openlocfilehash: f8b7e8dd557ddeeaa295a9849d471d74259941c2


---
# <a name="how-to-use-blitline-with-azure-and-azure-storage"></a>Azure と Azure Storage で Blitline を使用する方法
このガイドでは、Blitline サービスへのアクセス方法と Blitline へのジョブの送信方法について説明します。

## <a name="what-is-blitline"></a>Blitline とは
Blitline は、自分で構築する場合と比べてわずかな価格でエンタープライズ レベルの画像処理が可能なクラウド ベースの画像処理サービスです。

画像処理は何度にもわたって繰り返されている作業であり、通常は Web サイトごとに最初から作り直されます。 マイクロソフトでもこの作業を何度も行ってきたので理解しています。 ある日、皆さんのためにマイクロソフトがこれを行うときであると判断しました。 私たちは、この作業をすばやく効率的に行う方法を把握しており、ユーザーの負担を軽減することができます。

詳細については、 [http://www.blitline.com](http://www.blitline.com)を参照してください。

## <a name="what-blitline-is-not"></a>Blitline に当てはまらないこと
Blitline がどのようなときに役立つかは、使い始める前に Blitline に当てはまらないことを知っておいた方が簡単に理解できます。

* Blitline には、画像をアップロードする HTML ウィジェットはありません。 画像は一般に公開するか、制限付きアクセス許可で Blitline からアクセスできるようにする必要があります。
* Blitline には、Aviary.com のようなライブ画像処理機能がありません。
* Blitline は画像のアップロードを受け入れないため、画像を直接 Blitline に送信することはできません。 Azure Storage または Blitline でサポートされる他の場所に画像を送信してから、その画像を取得できる場所を Blitline に指定する必要があります。
* Blitline は並列性がきわめて高いため、同期処理を行いません。 つまり、マイクロソフトに postback_url を伝える必要があり、処理が完了すると通知を受け取ります。

## <a name="create-a-blitline-account"></a>Blitline アカウントの作成
[!INCLUDE [blitline-signup](../includes/blitline-signup.md)]

## <a name="how-to-create-a-blitline-job"></a>Blitline ジョブを作成する方法
Blitline では、JSON を使用して画像に対して実行する操作を定義します。 この JSON は、いくつかの簡単なフィールドで構成されています。

最も簡単な例は次のとおりです。

        json : '{
       "application_id": "MY_APP_ID",
       "src" : "http://cdn.blitline.com/filters/boys.jpeg",
       "functions" : [ {
           "name": "resize_to_fit",
           "params" : { "width": 240, "height": 140 },
           "save" : { "image_identifier" : "external_sample_1" }
       } ]
    }'

この JSON では、"src" 画像として "...boys.jpeg" を取得し、その画像のサイズを 240x140 に変更します。

アプリケーション ID は、Azure の **[接続文字列]** タブまたは **[管理]** タブに表示される ID です。 これは、Blitline でのジョブの実行を可能にする秘密の識別子です。

"save" パラメーターは、処理された画像を配置する場所に関する情報を識別します。 この簡単なケースでは定義されていません。 場所が定義されていない場合、Blitline により独自のクラウドの場所にローカルで (かつ一時的に) 保存されます。 Blitline を作成すると、Blitline により返された JSON からその場所を取得できるようになります。 "image" 識別子は必須であり、保存されたこの特定の画像を識別するときに返されます。

ここでサポートされる*機能*の詳細については、<http://www.blitline.com/docs/functions> を参照してください。

ジョブ オプションに関するドキュメントは、<http://www.blitline.com/docs/api> にも用意されています。

JSON を取得した後は、それを `http://api.blitline.com/job` に **POST** するだけです。

次のような内容の JSON が戻されます。

    {
     "results":
         {"images":
            [{
              "image_identifier":"external_sample_1",
              "s3_url":"https://s3.amazonaws.com/dev.blitline/2011110722/YOUR_APP_ID/CK3f0xBF_2bV6wf7gEZE8w.jpg"
            }],
          "job_id":"4eb8c9f72a50ee2a9900002f"
         }
    }


これは、Blitline が要求を受け取って処理中のキューに配置したことと、完了後は画像が次の場所で取得可能になることを示しています。**https://s3.amazonaws.com/dev.blitline/2011110722/YOUR\_APP\_ID/CK3f0xBF_2bV6wf7gEZE8w.jpg**

## <a name="how-to-save-an-image-to-your-azure-storage-account"></a>画像を Azure ストレージ アカウントに保存する方法
Azure ストレージ アカウントを持っている場合は、処理された画像を Blitline から Azure コンテナーに簡単にプッシュすることができます。 "azure_destination" を追加することで、Blitline からプッシュする場所とアクセス許可を定義します。

たとえば次のようになります。

    job : '{
      "application_id": "YOUR_APP_ID",
      "src" : "http://www.google.com/logos/2011/houdini11-hp.jpg",
         "functions" : [{
         "name": "blur",
         "save" : {
             "image_identifier" : "YOUR_IMAGE_IDENTIFIER",
             "azure_destination" : {
                 "account_name" : "YOUR_AZURE_CONTAINER_NAME",
                 "shared_access_signature" : "SAS_THAT_GIVES_BLITLINE_PERMISSION_TO_WRITE_THIS_OBJECT_TO_CONTAINER",
               }
           }
         }]
       }'


大文字の値を独自の値に設定すると、この JSON を http://api.blitline.com/job に送信できます。"src" 画像は、blur フィルターで処理された後、Azure の送信先にプッシュされます。

### <a name="please-note"></a>注意:
SAS には、送信先ファイルのファイル名を含む SAS URL 全体が含まれている必要があります。

例:

    http://blitline.blob.core.windows.net/sample/image.jpg?sr=b&sv=2012-02-12&st=2013-04-12T03%3A18%3A30Z&se=2013-04-12T04%3A18%3A30Z&sp=w&sig=Bte2hkkbwTT2sqlkkKLop2asByrE0sIfeesOwj7jNA5o%3D


Azure Storage に関する Blitline のドキュメントの最新版を [ここ](http://www.blitline.com/docs/azure_storage)で参照することもできます。

## <a name="next-steps"></a>次のステップ
他のすべての機能については、blitline.com の次のページを参照してください。

* Blitline API エンドポイントのドキュメント <http://www.blitline.com/docs/api>
* Blitline API 関数 <http://www.blitline.com/docs/functions>
* Blitline API の例 <http://www.blitline.com/docs/examples>
* サード パーティの Nuget ライブラリ <http://nuget.org/packages/Blitline.Net>




<!--HONumber=Nov16_HO3-->


