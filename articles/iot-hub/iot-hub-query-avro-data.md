---
title: Azure Data Lake Analytics を使用して Avro データのクエリを実行する | Microsoft Docs
description: メッセージ本文のプロパティを使用してデバイス テレメトリを BLOB ストレージにルーティングし、BLOB ストレージに書き込まれた Avro 形式のデータに対してクエリを実行します。
author: ash2017
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 05/29/2018
ms.author: asrastog
ms.openlocfilehash: 69c890cfc3db04fe625ed7ad008f545c01844834
ms.sourcegitcommit: fd488a828465e7acec50e7a134e1c2cab117bee8
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 01/03/2019
ms.locfileid: "53993486"
---
# <a name="query-avro-data-by-using-azure-data-lake-analytics"></a>Azure Data Lake Analytics を使用して Avro データのクエリを実行する

この記事では、Azure IoT Hub から Azure サービスにメッセージを効率的にルーティングするために Avro データに対してクエリを実行する方法について説明します。 [メッセージ ルーティング](iot-hub-devguide-messages-d2c.md)では、メッセージのプロパティ、メッセージ本文、デバイス ツイン タグ、およびデバイス ツインのプロパティに基づく豊富なクエリを使用して、データをフィルター処理することができます。 メッセージ ルーティングでのクエリ機能の詳細については、メッセージ ルーティングのクエリ構文に関する記事を参照してください。 
<!--[Message Routing Query Syntax](iot-hub-devguide-routing-query-syntax.md). I don't have this article yet. -->

Azure IoT Hub が Azure Blob Storage にメッセージをルーティングするときに、IoT Hub ではメッセージ本文のプロパティとメッセージのプロパティの両方を持つコンテンツが Avro 形式で書き込まれるという問題がありました。 IoT Hub では Avro データ形式の BLOB ストレージへのデータの書き込みのみがサポートされますが、この形式は他のエンドポイントでは使用されません。 詳細については、Azure Storage コンテナーの使用に関する記事を参照してください。 Avro はデータとメッセージの保存には最適な形式ですが、データのクエリ用途には使うのは容易ではありません。 比較すると、JSON または CSV 形式はデータのクエリがはるかに簡単です。

<!-- https://review.docs.microsoft.com/azure/iot-hub/iot-hub-devguide-messages-d2c?branch=pr-en-us-51566#azure-blob-storage  NEW LINK FOR 'WHEN USING STORAGE CONTAINERS' -->

非リレーショナル ビッグデータのニーズと形式に対応し、この課題を克服するために、データの変換とスケーリングのいずれにおいても、ビッグデータ パターンの多くを使用することができます。 そのようなパターンの 1 つとして、"クエリごとの支払い" である Azure Data Lake Analytics があり、この記事では、この点について重点的に取り上げています。 クエリは Hadoop やその他のソリューションで簡単に実行できますが、多くの場合、Data Lake Analytics の方がこの "クエリごとの支払い" アプローチに適しています。 

U-SQL には Avro 用の "エクストラクター" があります。 詳細については、[U-SQL Avro の例](https://github.com/Azure/usql/tree/master/Examples/AvroExamples)を参照してください。

## <a name="query-and-export-avro-data-to-a-csv-file"></a>Avro データのクエリと CSV ファイルへのエクスポート
このセクションでは、Avro データを照会して Azure Blob Storage 内の CSV ファイルにエクスポートしますが、他のリポジトリやデータ ストアに対してもデータを簡単に配置することができます。

1. メッセージ本文のプロパティを使用して Azure Blob Storage エンドポイントにデータをルーティングし、メッセージを選択するように Azure IoT Hub を設定します。

   ![[カスタム エンドポイント] セクション](./media/iot-hub-query-avro-data/query-avro-data-1a.png)

   ![ルーティング規則](./media/iot-hub-query-avro-data/query-avro-data-1b.png)

   ルートおよびカスタム エンドポイントの設定の詳細については、[IoT Hub のメッセージ ルーティング](iot-hub-create-through-portal.md#message-routing-for-an-iot-hub)に関するページを参照してください。

2. 製品ドキュメントで参照されているように、プロパティまたはメッセージ本文にデバイスのエンコード、コンテンツ タイプ、および必要なデータが含まれていることを確認します。 これらの属性を Device Explorer で見ると (下図参照)、それらが正しく設定されていることを確認できます。

   ![イベント ハブのデータ ペイン](./media/iot-hub-query-avro-data/query-avro-data-2.png)

3. Azure Data Lake Store インスタンスと Data Lake Analytics インスタンスを設定します。 Data Lake Analytics インスタンスは、Data Lake Store インスタンスへのルーティングを必要としますが、Azure IoT Hub では、それが行われません。

   ![Data Lake Store インスタンスと Data Lake Analytics インスタンス](./media/iot-hub-query-avro-data/query-avro-data-3.png)

4. Data Lake Analytics で、Azure Blob Storage を追加のストア (Azure IoT Hub がデータをルーティングするのと同じ Blob Storage) として構成します。

   ![[データ ソース] ウィンドウ](./media/iot-hub-query-avro-data/query-avro-data-4.png)
 
5. [U-SQL Avro の例](https://github.com/Azure/usql/tree/master/Examples/AvroExamples)にもあるように、4 つの DLL ファイルが必要となります。 これらのファイルを Data Lake Store インスタンス内の場所にアップロードします。

   ![アップロードされた 4 つの DLL ファイル](./media/iot-hub-query-avro-data/query-avro-data-5.png)

6. Visual Studio で、U-SQL プロジェクトを作成します。
 
   !Create a U-SQL project](./media/iot-hub-query-avro-data/query-avro-data-6.png)

7. 次のスクリプトの内容を、新しく作成されたファイルに貼り付けます。 3 つの強調表示されたセクション (Data Lake Analytics アカウント、関連する DLL ファイルのパス、ストレージ アカウントの正しいパス) を変更します。
    
   ![修正する 3 つのセクション](./media/iot-hub-query-avro-data/query-avro-data-7a.png)

   CSV ファイルに単純な出力を行う実際の U-SQL スクリプト:
    
    ```sql
        DROP ASSEMBLY IF EXISTS [Avro];
        CREATE ASSEMBLY [Avro] FROM @"/Assemblies/Avro/Avro.dll";
        DROP ASSEMBLY IF EXISTS [Microsoft.Analytics.Samples.Formats];
        CREATE ASSEMBLY [Microsoft.Analytics.Samples.Formats] FROM @"/Assemblies/Avro/Microsoft.Analytics.Samples.Formats.dll";
        DROP ASSEMBLY IF EXISTS [Newtonsoft.Json];
        CREATE ASSEMBLY [Newtonsoft.Json] FROM @"/Assemblies/Avro/Newtonsoft.Json.dll";
        DROP ASSEMBLY IF EXISTS [log4net];
        CREATE ASSEMBLY [log4net] FROM @"/Assemblies/Avro/log4net.dll";

        REFERENCE ASSEMBLY [Newtonsoft.Json];
        REFERENCE ASSEMBLY [log4net];
        REFERENCE ASSEMBLY [Avro];
        REFERENCE ASSEMBLY [Microsoft.Analytics.Samples.Formats];

        // Blob container storage account filenames, with any path
        DECLARE @input_file string = @"wasb://hottubrawdata@kevinsayazstorage/kevinsayIoT/{*}/{*}/{*}/{*}/{*}/{*}";
        DECLARE @output_file string = @"/output/output.csv";

        @rs =
        EXTRACT
        EnqueuedTimeUtc string,
        Body byte[]
        FROM @input_file

        USING new Microsoft.Analytics.Samples.Formats.ApacheAvro.AvroExtractor(@"
        {
            ""type"":""record"",
            ""name"":""Message"",
            ""namespace"":""Microsoft.Azure.Devices"",
            ""fields"":
           [{
                ""name"":""EnqueuedTimeUtc"",
                ""type"":""string""
            },
            {
                ""name"":""Properties"",
                ""type"":
                {
                    ""type"":""map"",
                    ""values"":""string""
                }
            },
            {
                ""name"":""SystemProperties"",
                ""type"":
                {
                    ""type"":""map"",
                    ""values"":""string""
                }
            },
            {
                ""name"":""Body"",
                ""type"":[""null"",""bytes""]
            }]
        }"
        );

        @cnt =
        SELECT EnqueuedTimeUtc AS time, Encoding.UTF8.GetString(Body) AS jsonmessage
        FROM @rs;

        OUTPUT @cnt TO @output_file USING Outputters.Text(); 
    ```    

    Data Lake Analytics が次のスクリプトを実行するのにかかった時間は 5分です。その際、分析ユニットは 10 個に制限され、177 個のファイルが処理されました。 その結果は、次の画像に表示されている CSV ファイル出力に示されます。
    
    ![CSV ファイルへの出力の結果](./media/iot-hub-query-avro-data/query-avro-data-7b.png)

    ![CSV ファイルに変換された出力](./media/iot-hub-query-avro-data/query-avro-data-7c.png)

    JSON を解析するには、手順 8. に進んでください。
    
8. ほとんどの IoT メッセージは JSON ファイル形式です。 次の行を追加すると、メッセージを解析して JSON ファイルに出力できます。WHERE 句を追加して必要なデータのみを出力することが可能です。

    ```sql
       @jsonify = 
         SELECT Microsoft.Analytics.Samples.Formats.Json.JsonFunctions.JsonTuple(Encoding.UTF8.GetString(Body)) 
           AS message FROM @rs;
    
        /*
        @cnt =
            SELECT EnqueuedTimeUtc AS time, Encoding.UTF8.GetString(Body) AS jsonmessage
            FROM @rs;
        
        OUTPUT @cnt TO @output_file USING Outputters.Text();
        */
        
        @cnt =
            SELECT message["message"] AS iotmessage,
                   message["event"] AS msgevent,
                   message["object"] AS msgobject,
                   message["status"] AS msgstatus,
                   message["host"] AS msghost
            FROM @jsonify;
            
        OUTPUT @cnt TO @output_file USING Outputters.Text();
    ```

    出力結果には、`SELECT` コマンドの項目ごとに 1 つの列が表示されます。 
    
    ![各項目に対応した列を示す出力](./media/iot-hub-query-avro-data/query-avro-data-8.png)

## <a name="next-steps"></a>次の手順

このチュートリアルでは、Azure IoT Hub から Azure サービスにメッセージを効率的にルーティングするために Avro データに対してクエリを実行する方法について説明しました。

IoT Hub を使用する完全なエンド ツー エンド ソリューションの例については、[Azure IoT ソリューション アクセラレータのドキュメント](/azure/iot-accelerators)を参照してください。

IoT Hub を使用したソリューションの開発に関する詳細については、[IoT Hub 開発者ガイド](iot-hub-devguide.md)をご覧ください。

IoT Hub でのメッセージ ルーティングの詳細については、[IoT Hub でのメッセージの送受信](iot-hub-devguide-messaging.md)に関するページを参照してください。
