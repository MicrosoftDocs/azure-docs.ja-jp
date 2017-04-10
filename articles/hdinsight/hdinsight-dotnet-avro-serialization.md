---
title: "Microsoft Avro ライブラリを使用したデータのシリアル化 | Microsoft Docs"
description: "Azure HDInsight が Avro を使用してビッグ データをシリアル化する方法について説明します。"
services: hdinsight
documentationcenter: 
tags: azure-portal
author: mumian
manager: jhubbard
editor: cgronlun
ms.assetid: c78dc20d-5d8d-4366-94ac-abbe89aaac58
ms.service: hdinsight
ms.workload: big-data
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/06/2017
ms.author: jgao
translationtype: Human Translation
ms.sourcegitcommit: dcda8b30adde930ab373a087d6955b900365c4cc
ms.openlocfilehash: 2d3250eb1c0855d185228b0bca990b255f9aa9bd


---
# <a name="serialize-data-in-hadoop-with-the-microsoft-avro-library"></a>Microsoft Avro ライブラリを使用した Hadoop のデータのシリアル化
このトピックでは、<a href="https://hadoopsdk.codeplex.com/wikipage?title=Avro%20Library" target="_blank">Microsoft Avro Library</a> を使用して、オブジェクトとその他のデータ構造をストリームにシリアル化し、メモリ、データベース、またはファイルに格納する方法を示します。さらに、逆シリアル化を行って元のオブジェクトを回復する方法についても説明します。

[!INCLUDE [windows-only](../../includes/hdinsight-windows-only.md)]

## <a name="apache-avro"></a>Apache Avro
<a href="https://hadoopsdk.codeplex.com/wikipage?title=Avro%20Library" target="_blank">Microsoft Avro ライブラリ</a>は、Microsoft.NET 環境向けに Apache Avro データ シリアル化システムを実装します。 Apache Avro は、シリアル化のためのコンパクトなバイナリ データ交換形式を提供します。 Apache Avro は、<a href="http://www.json.org" target="_blank">JSON</a> を使用して、言語の相互運用性を保証する、言語に依存しないスキーマを定義します。 ある言語でシリアル化されたデータは、別の言語で読むことができます。 現在、C、C++、C#、Java、PHP、Python、および Ruby がサポートされています。 この形式の詳細については、<a href="http://avro.apache.org/docs/current/spec.html" target="_blank">Apache Avro の仕様</a>を参照してください。 現在のバージョンの Microsoft Avro ライブラリでは、このシリアル化のリモート プロシージャ コール (RPC) の部分をサポートしていないことに注意してください。

Avro システムにおけるオブジェクトのシリアル化表現は、スキーマと実際の値の 2 つの部分で構成されます。 Avro スキーマは、シリアル化されたデータの言語に依存しないデータ モデルを JSON 形式で記述します。 Avro スキーマは、データのバイナリ表現と共存します。 スキーマをバイナリ表現から切り離すことで、値ごとのオーバーヘッドなしにそれぞれのオブジェクトを書き込めるため、シリアル化が高速になり、表現を小さくできます。

## <a name="the-hadoop-scenario"></a>Hadoop のシナリオ
Apache Avro のシリアル化形式は、Azure HDInsight やその他の Apache Hadoop 環境で広く使用されています。 Avro は、Hadoop MapReduce ジョブ内の複雑なデータ構造を表すのに便利です。 Avro ファイル (Avro オブジェクト コンテナー ファイル) の形式は、分散型 MapReduce プログラミング モデルをサポートするように設計されています。 分散を可能にする重要な機能として、ファイル内の任意のポイントを検索し、特定のブロックから読み取りを開始できるという意味で、ファイルが "分割可能" であることがあります。

## <a name="serialization-in-avro-library"></a>Avro ライブラリでのシリアル化
.NET Library for Avro では、オブジェクトをシリアル化する方法として次の 2 つの方法をサポートしています。

* **リフレクション** : これらの型に対応する JSON スキーマは、シリアル化する .NET 型のデータのコントラクト属性を考慮に入れたうえで、自動的に作成されます。
* **汎用レコード**: JSON スキーマは、シリアル化するデータのスキーマを記述する .NET 型がないときに [**AvroRecord**](http://msdn.microsoft.com/library/microsoft.hadoop.avro.avrorecord.aspx) クラスによって表されるレコードに明示的に指定されます。

ストリームのライターとリーダーの両方でデータ スキーマがわかっている場合は、データをスキーマなしで送信できます。 Avro オブジェクト コンテナー ファイルを使用する場合は、ファイル内にスキーマを保存する必要があります。 データ圧縮に使用するコーデックなどの他のパラメーターを指定することもできます。 これらのシナリオについては、次のコード例で詳しく説明します。

## <a name="install-avro-library"></a>Avro Library のインストール
ライブラリをインストールするには、以下が必要です。

* <a href="http://www.microsoft.com/download/details.aspx?id=17851" target="_blank">Microsoft .NET Framework 4</a>
* <a href="http://james.newtonking.com/json" target="_blank">Newtonsoft Json.NET</a> (6.0.4 以降)

Newtonsoft.Json.dll 依存関係は Microsoft Avro ライブラリのインストールと共に自動的にダウンロードされます。 この手順は、次のセクションで説明しています。

Microsoft Avro ライブラリは、NuGet パッケージとして配布されています。この NuGet パッケージは、次の手順に従って Visual Studio からインストールできます。

1. **[プロジェクト]** タブの **[NuGet パッケージの管理]** を選択します。
2. **[オンライン検索]** ボックスを使用して "Microsoft.Hadoop.Avro" を検索します。
3. **[Microsoft Azure HDInsight Avro ライブラリ]** の横にある **[インストール]** をクリックします。

Newtonsoft.Json.dll (6.0.4 以降) 依存関係も Microsoft Avro ライブラリと共に自動的にダウンロードされます。

<a href="https://hadoopsdk.codeplex.com/wikipage?title=Avro%20Library" target="_blank">Microsoft Avro ライブラリのホーム ページ</a>にアクセスして現在のリリース ノートを参照することをお勧めします。

Microsoft Avro ライブラリのソース コードは、<a href="https://hadoopsdk.codeplex.com/wikipage?title=Avro%20Library" target="_blank">Microsoft Avro ライブラリのホーム ページ</a>から入手できます。

## <a name="compile-schemas-using-avro-library"></a>Avro ライブラリを使用したスキーマのコンパイル
Microsoft Avro ライブラリには、コード生成ユーティリティがあります。このユーティリティにより、あらかじめ定義した JSON スキーマに基づいて、C# のデータ型を自動的に作成できます。 このコード生成ユーティリティは、バイナリの実行可能ファイルとして配布されませんが、次の手順に従って簡単に作成できます。

1. <a href="http://hadoopsdk.codeplex.com/SourceControl/latest#" target="_blank">Microsoft .NET SDK For Hadoop</a> から、最新バージョンの HDInsight SDK ソース コードが含まれる zip ファイルをダウンロードします  (**[ダウンロード]** タブでなく **[ダウンロード]** アイコンをクリックします)。
2. .NET Framework 4 がインストールされたコンピューターのディレクトリに HDInsight SDK を展開し、インターネットに接続して、必要な依存関係の NuGet パッケージをダウンロードします。 ここでは、ソース コードを C:\SDK に展開します。
3. C:\SDK\src\Microsoft.Hadoop.Avro.Tools フォルダーに移動し、build.bat を実行します (このファイルにより、.NET Framework の 32 ビット配布の MS ビルドが呼び出されます。 64 ビット版が必要な場合は、ファイル内のコメントに従って build.bat を編集します)。ビルドが成功したことを確認します  (一部のシステムでは、MSBuild で警告が生成されます。 ビルド エラーが発生しない限り、これらの警告はユーティリティに影響しません)。
4. コンパイル済みのユーティリティは、C:\SDK\Bin\Unsigned\Release\Microsoft.Hadoop.Avro.Tools にあります。

コマンドラインの構文を確認するには、コード生成ユーティリティがあるフォルダーで、次のコマンドを実行します。`Microsoft.Hadoop.Avro.Tools help /c:codegen`

ユーティリティをテストするには、ソース コードと一緒に提供されている JSON のサンプル スキーマ ファイルから、C# クラスを生成します。 次のコマンドを実行します。

    Microsoft.Hadoop.Avro.Tools codegen /i:C:\SDK\src\Microsoft.Hadoop.Avro.Tools\SampleJSON\SampleJSONSchema.avsc /o:

このコマンドは、SensorData.cs と Location.cs の 2 つの C# ファイルを現在のディレクトリに作成するようになっています。

コード生成ユーティリティで使用されるロジックを理解し、JSON スキーマを C# のデータ型に変換する方法については、C:\SDK\src\Microsoft.Hadoop.Avro.Tools\Doc にある GenerationVerification.feature ファイルを参照してください。

前述したファイルに記述されているロジックを使用して、JSON スキーマから名前空間が抽出されます。 スキーマから抽出された名前空間は、ユーティリティのコマンド ラインで /n パラメーターにより抽出される名前空間よりも優先されます。 スキーマ内に含まれる名前空間をオーバーライドする場合は、/nf パラメーターを使用します。 たとえば、SampleJSONSchema.avsc の名前空間をすべて my.own.nspace に変更する場合は、次のコマンドを実行します。

    Microsoft.Hadoop.Avro.Tools codegen /i:C:\SDK\src\Microsoft.Hadoop.Avro.Tools\SampleJSON\SampleJSONSchema.avsc /o:. /nf:my.own.nspace

## <a name="samples"></a>サンプル
このトピックで紹介する 6 つの例は、Microsoft Avro ライブラリでサポートされている、それぞれ異なるシナリオに対応しています。 Microsoft Avro ライブラリは、あらゆるストリームに対応するように設計されています。 これらの例では、簡潔性と一貫性のために、ファイル ストリームやデータベースではなくメモリ ストリームを使用してデータを操作しています。 運用環境では、実際のシナリオ要件、データ ソースとその量、パフォーマンス制約、その他の要因に応じて方法を選択してください。

最初の 2 つの例では、リフレクションと汎用レコードを使用してデータをメモリ ストリーム バッファーにシリアル化および逆シリアル化する方法を紹介します。 これらの 2 つのケースのスキーマは、アウト オブ バンドでリーダーとライターによって共有されると見なされます。

3 つ目と 4 つ目の例では、リフレクションと汎用レコードを Avro オブジェクト コンテナー ファイルを使用してデータをシリアル化および逆シリアル化する方法を紹介します。 データを Avro コンテナー ファイルに格納するときはスキーマを逆シリアル化のために共有する必要があるため、必ずスキーマが一緒に格納されます。

最初の 4 つの例を含むサンプルは、 <a href="http://code.msdn.microsoft.com/windowsazure/Serialize-data-with-the-86055923" target="_blank">Azure コード サンプル</a> サイトからダウンロードできます。

5 つ目の例では、Avro オブジェクト コンテナー ファイルに対してカスタム圧縮コーデックを使用する方法を紹介します。 この例を含むサンプルは、 <a href="http://code.msdn.microsoft.com/windowsazure/Serialize-data-with-the-67159111" target="_blank">Azure コード サンプル</a> サイトからダウンロードできます。

6 つ目の例では、Avro のシリアル化によりデータを Azure BLOB ストレージにアップロードし、その後 Hive と HDInsight (Hadoop) クラスターを使用して分析する方法を紹介します。 このサンプルは、 <a href="https://code.msdn.microsoft.com/windowsazure/Using-Avro-to-upload-data-ae81b1e3" target="_blank">Azure コード サンプル</a> サイトからダウンロードできます。

このトピックで説明するこれら 6 つの例へのリンクを、次に示します。

* <a href="#Scenario1">**リフレクションを使用したシリアル化**</a>: シリアル化する型の JSON スキーマがデータ コントラクト属性から自動的に作成されます。
* <a href="#Scenario2">**汎用レコードを使用したシリアル化**</a>: リフレクション用に使用できる .NET 型がないときにレコードに明示的に JSON スキーマを指定します。
* <a href="#Scenario3">**オブジェクト コンテナー ファイルとリフレクションを使用したシリアル化**</a>: JSON スキーマは自動的に作成され、Avro オブジェクト コンテナー ファイルを介してシリアル化されたデータと共有されます。
* <a href="#Scenario4">**オブジェクト コンテナー ファイルと汎用レコードを使用したシリアル化**</a>: JSON スキーマはシリアル化の前に明示的に指定され、Avro オブジェクト コンテナー ファイルを介してデータと共有されます。
* <a href="#Scenario5">**オブジェクト コンテナー ファイルとカスタム圧縮コーデックを使用したシリアル化**</a>: この例では、Deflate データ圧縮コーデックのカスタマイズされた .NET 実装を使用して Avro オブジェクト コンテナー ファイルを作成する方法を紹介します。
* <a href="#Scenario6">**Avro を使用した Microsoft Azure HDInsight サービスのデータのアップロード**</a>: この例では、Avro のシリアル化による HDInsight サービスの操作方法を示します。 この例を実行するには、有効な Azure サブスクリプションと、Azure HDInsight クラスターへのアクセスが必要です。

### <a name="a-namescenario1asample-1-serialization-with-reflection"></a><a name="Scenario1"></a>例 1: リフレクションを使用したシリアル化
Microsoft Avro ライブラリでリフレクションを使用して、型の JSON スキーマを、シリアル化する C# オブジェクトのデータ コントラクト属性から自動的に作成できます。 Microsoft Avro ライブラリは、[**IAvroSeralizer<T>**](http://msdn.microsoft.com/library/dn627341.aspx) を作成して、シリアル化するフィールドを識別します。

この例では、オブジェクト (**Location** 構造体をメンバーとして含む **SensorData** クラス) をメモリ ストリームにシリアル化した後、このストリームを逆シリアル化します。 次に、その結果を初期インスタンスと比較して、回復された **SensorData** オブジェクトが元のオブジェクトと同じかどうかを確認します。

この例のスキーマはリーダーとライターによって共有されると見なされるため、Avro オブジェクト コンテナー形式は必要ありません。 スキーマをデータと共に共有する必要がある場合にリフレクションとオブジェクト コンテナー形式を使用してデータをメモリ バッファーにシリアル化および逆シリアル化する方法の例については、「<a href="#Scenario3">オブジェクト コンテナー ファイルとリフレクションを使用したシリアル化</a>」を参照してください。

    namespace Microsoft.Hadoop.Avro.Sample
    {
        using System;
        using System.Collections.Generic;
        using System.IO;
        using System.Linq;
        using System.Runtime.Serialization;
        using Microsoft.Hadoop.Avro.Container;
        using Microsoft.Hadoop.Avro;

        //Sample class used in serialization samples
        [DataContract(Name = "SensorDataValue", Namespace = "Sensors")]
        internal class SensorData
        {
            [DataMember(Name = "Location")]
            public Location Position { get; set; }

            [DataMember(Name = "Value")]
            public byte[] Value { get; set; }
        }

        //Sample struct used in serialization samples
        [DataContract]
        internal struct Location
        {
            [DataMember]
            public int Floor { get; set; }

            [DataMember]
            public int Room { get; set; }
        }

        //This class contains all methods demonstrating
        //the usage of Microsoft Avro Library
        public class AvroSample
        {

            //Serialize and deserialize sample data set represented as an object using reflection.
            //No explicit schema definition is required - schema of serialized objects is automatically built.
            public void SerializeDeserializeObjectUsingReflection()
            {

                Console.WriteLine("SERIALIZATION USING REFLECTION\n");
                Console.WriteLine("Serializing Sample Data Set...");

                //Create a new AvroSerializer instance and specify a custom serialization strategy AvroDataContractResolver
                //for serializing only properties attributed with DataContract/DateMember
                var avroSerializer = AvroSerializer.Create<SensorData>();

                //Create a memory stream buffer
                using (var buffer = new MemoryStream())
                {
                    //Create a data set by using sample class and struct
                    var expected = new SensorData { Value = new byte[] { 1, 2, 3, 4, 5 }, Position = new Location { Room = 243, Floor = 1 } };

                    //Serialize the data to the specified stream
                    avroSerializer.Serialize(buffer, expected);


                    Console.WriteLine("Deserializing Sample Data Set...");

                    //Prepare the stream for deserializing the data
                    buffer.Seek(0, SeekOrigin.Begin);

                    //Deserialize data from the stream and cast it to the same type used for serialization
                    var actual = avroSerializer.Deserialize(buffer);

                    Console.WriteLine("Comparing Initial and Deserialized Data Sets...");

                    //Finally, verify that deserialized data matches the original one
                    bool isEqual = this.Equal(expected, actual);

                    Console.WriteLine("Result of Data Set Identity Comparison is {0}", isEqual);

                }
            }

            //
            //Helper methods
            //

            //Comparing two SensorData objects
            private bool Equal(SensorData left, SensorData right)
            {
                return left.Position.Equals(right.Position) && left.Value.SequenceEqual(right.Value);
            }



            static void Main()
            {

                string sectionDivider = "---------------------------------------- ";

                //Create an instance of AvroSample Class and invoke methods
                //illustrating different serializing approaches
                AvroSample Sample = new AvroSample();

                //Serialization to memory using reflection
                Sample.SerializeDeserializeObjectUsingReflection();

                Console.WriteLine(sectionDivider);
                Console.WriteLine("Press any key to exit.");
                Console.Read();
            }
        }
    }
    // The example is expected to display the following output:
    // SERIALIZATION USING REFLECTION
    //
    // Serializing Sample Data Set...
    // Deserializing Sample Data Set...
    // Comparing Initial and Deserialized Data Sets...
    // Result of Data Set Identity Comparison is True
    // ----------------------------------------
    // Press any key to exit.


### <a name="sample-2-serialization-with-a-generic-record"></a>例 2: 汎用レコードを使用したシリアル化
.NET クラスとデータ コントラクトを使用してデータを表現できないためにリフレクションを使用できない場合は、JSON スキーマを明示的に汎用レコードに指定できます。 この方法は、一般にリフレクションを使用する方法よりも低速です。 このような場合、データのスキーマは、コンパイル時までわからないなど、動的になる場合もあります。 この動的シナリオの例としては、実行時に Avro 形式に変換されるまでそのスキーマがわからない、コンマ区切り値 (CSV) ファイルとして表現されるデータが該当します。

この例では、 [**AvroRecord**](http://msdn.microsoft.com/library/microsoft.hadoop.avro.avrorecord.aspx) を使用して明示的に JSON スキーマを指定する方法、データを設定する方法、データをシリアル化および逆シリアル化する方法を示します。 次に、その結果を初期インスタンスと比較して、回復されたレコードが元のレコードと同じかどうかを確認します。

この例のスキーマはリーダーとライターによって共有されると見なされるため、Avro オブジェクト コンテナー形式は必要ありません。 スキーマをシリアル化されたデータと共に含める必要がある場合に汎用レコードとオブジェクト コンテナー形式を使用してデータをメモリ バッファーにシリアル化および逆シリアル化する方法の例については、「<a href="#Scenario4">オブジェクト コンテナー ファイルと汎用レコードを使用したシリアル化</a>」を参照してください。

    namespace Microsoft.Hadoop.Avro.Sample
    {
    using System;
    using System.Collections.Generic;
    using System.IO;
    using System.Linq;
    using System.Runtime.Serialization;
    using Microsoft.Hadoop.Avro.Container;
    using Microsoft.Hadoop.Avro.Schema;
    using Microsoft.Hadoop.Avro;

    //This class contains all methods demonstrating
    //the usage of Microsoft Avro Library
    public class AvroSample
    {

        //Serialize and deserialize sample data set by using a generic record.
        //A generic record is a special class with the schema explicitly defined in JSON.
        //All serialized data should be mapped to the fields of the generic record,
        //which in turn will be then serialized.
        public void SerializeDeserializeObjectUsingGenericRecords()
        {
            Console.WriteLine("SERIALIZATION USING GENERIC RECORD\n");
            Console.WriteLine("Defining the Schema and creating Sample Data Set...");

            //Define the schema in JSON
            const string Schema = @"{
                                ""type"":""record"",
                                ""name"":""Microsoft.Hadoop.Avro.Specifications.SensorData"",
                                ""fields"":
                                    [
                                        {
                                            ""name"":""Location"",
                                            ""type"":
                                                {
                                                    ""type"":""record"",
                                                    ""name"":""Microsoft.Hadoop.Avro.Specifications.Location"",
                                                    ""fields"":
                                                        [
                                                            { ""name"":""Floor"", ""type"":""int"" },
                                                            { ""name"":""Room"", ""type"":""int"" }
                                                        ]
                                                }
                                        },
                                        { ""name"":""Value"", ""type"":""bytes"" }
                                    ]
                            }";

            //Create a generic serializer based on the schema
            var serializer = AvroSerializer.CreateGeneric(Schema);
            var rootSchema = serializer.WriterSchema as RecordSchema;

            //Create a memory stream buffer
            using (var stream = new MemoryStream())
            {
                //Create a generic record to represent the data
                dynamic location = new AvroRecord(rootSchema.GetField("Location").TypeSchema);
                location.Floor = 1;
                location.Room = 243;

                dynamic expected = new AvroRecord(serializer.WriterSchema);
                expected.Location = location;
                expected.Value = new byte[] { 1, 2, 3, 4, 5 };

                Console.WriteLine("Serializing Sample Data Set...");

                //Serialize the data
                serializer.Serialize(stream, expected);

                stream.Seek(0, SeekOrigin.Begin);

                Console.WriteLine("Deserializing Sample Data Set...");

                //Deserialize the data into a generic record
                dynamic actual = serializer.Deserialize(stream);

                Console.WriteLine("Comparing Initial and Deserialized Data Sets...");

                //Finally, verify the results
                bool isEqual = expected.Location.Floor.Equals(actual.Location.Floor);
                isEqual = isEqual && expected.Location.Room.Equals(actual.Location.Room);
                isEqual = isEqual && ((byte[])expected.Value).SequenceEqual((byte[])actual.Value);
                Console.WriteLine("Result of Data Set Identity Comparison is {0}", isEqual);
            }
        }

        static void Main()
        {

            string sectionDivider = "---------------------------------------- ";

            //Create an instance of AvroSample class and invoke methods
            //illustrating different serializing approaches
            AvroSample Sample = new AvroSample();

            //Serialization to memory using generic record
            Sample.SerializeDeserializeObjectUsingGenericRecords();

            Console.WriteLine(sectionDivider);
            Console.WriteLine("Press any key to exit.");
            Console.Read();
        }
    }
    }
    // The example is expected to display the following output:
    // SERIALIZATION USING GENERIC RECORD
    //
    // Defining the Schema and creating Sample Data Set...
    // Serializing Sample Data Set...
    // Deserializing Sample Data Set...
    // Comparing Initial and Deserialized Data Sets...
    // Result of Data Set Identity Comparison is True
    // ----------------------------------------
    // Press any key to exit.


### <a name="sample-3-serialization-using-object-container-files-and-serialization-with-reflection"></a>例 3: オブジェクト コンテナー ファイルを使用したシリアル化とリフレクションを使用したシリアル化
この例は、<a href="#Scenario1">最初の例</a>と同様に、スキーマはリフレクションを使用して暗黙的に指定されています。 相違点は、ここでは、スキーマは逆シリアル化するリーダーにはわからないということが前提である点です。 シリアル化される **SensorData** オブジェクトとその暗黙的に指定されたスキーマは、[**AvroContainer**](http://msdn.microsoft.com/library/microsoft.hadoop.avro.container.avrocontainer.aspx) クラスによって表される Avro オブジェクト コンテナー ファイルに格納されます。

この例では、データを [**SequentialWriter<SensorData>**](http://msdn.microsoft.com/library/dn627340.aspx) を使用してシリアル化し、[**SequentialReader<SensorData>**](http://msdn.microsoft.com/library/dn627340.aspx) を使用して逆シリアル化しています。 次に、その結果を初期のインスタンスと比較して、同一性を確認します。

さらに、オブジェクト コンテナー ファイル内のデータを、.NET Framework 4 の既定の [**Deflate**][deflate-100] 圧縮コーデックを使用して圧縮します。 .NET Framework 4.5 で提供されるより優れた最新バージョンの [**Deflate**][deflate-110] 圧縮コーデックの使い方については、このトピックの <a href="#Scenario5"> 5 番目の例</a>を参照してください。

    namespace Microsoft.Hadoop.Avro.Sample
    {
        using System;
        using System.Collections.Generic;
        using System.IO;
        using System.Linq;
        using System.Runtime.Serialization;
        using Microsoft.Hadoop.Avro.Container;
        using Microsoft.Hadoop.Avro;

        //Sample class used in serialization samples
        [DataContract(Name = "SensorDataValue", Namespace = "Sensors")]
        internal class SensorData
        {
            [DataMember(Name = "Location")]
            public Location Position { get; set; }

            [DataMember(Name = "Value")]
            public byte[] Value { get; set; }
        }

        //Sample struct used in serialization samples
        [DataContract]
        internal struct Location
        {
            [DataMember]
            public int Floor { get; set; }

            [DataMember]
            public int Room { get; set; }
        }

        //This class contains all methods demonstrating
        //the usage of Microsoft Avro Library
        public class AvroSample
        {

            //Serializes and deserializes the sample data set by using reflection and Avro object container files.
            //Serialized data is compressed with the Deflate codec.
            public void SerializeDeserializeUsingObjectContainersReflection()
            {

                Console.WriteLine("SERIALIZATION USING REFLECTION AND AVRO OBJECT CONTAINER FILES\n");

                //Path for Avro object container file
                string path = "AvroSampleReflectionDeflate.avro";

                //Create a data set by using sample class and struct
                var testData = new List<SensorData>
                        {
                            new SensorData { Value = new byte[] { 1, 2, 3, 4, 5 }, Position = new Location { Room = 243, Floor = 1 } },
                            new SensorData { Value = new byte[] { 6, 7, 8, 9 }, Position = new Location { Room = 244, Floor = 1 } }
                        };

                //Serializing and saving data to file.
                //Creating a memory stream buffer.
                using (var buffer = new MemoryStream())
                {
                    Console.WriteLine("Serializing Sample Data Set...");

                    //Create a SequentialWriter instance for type SensorData, which can serialize a sequence of SensorData objects to stream.
                    //Data will be compressed using the Deflate codec.
                    using (var w = AvroContainer.CreateWriter<SensorData>(buffer, Codec.Deflate))
                    {
                        using (var writer = new SequentialWriter<SensorData>(w, 24))
                        {
                            // Serialize the data to stream by using the sequential writer
                            testData.ForEach(writer.Write);
                        }
                    }

                    //Save stream to file
                    Console.WriteLine("Saving serialized data to file...");
                    if (!WriteFile(buffer, path))
                    {
                        Console.WriteLine("Error during file operation. Quitting method");
                        return;
                    }
                }

                //Reading and deserializing data.
                //Creating a memory stream buffer.
                using (var buffer = new MemoryStream())
                {
                    Console.WriteLine("Reading data from file...");

                    //Reading data from object container file
                    if (!ReadFile(buffer, path))
                    {
                        Console.WriteLine("Error during file operation. Quitting method");
                        return;
                    }

                    Console.WriteLine("Deserializing Sample Data Set...");

                    //Prepare the stream for deserializing the data
                    buffer.Seek(0, SeekOrigin.Begin);

                    //Create a SequentialReader instance for type SensorData, which will deserialize all serialized objects from the given stream.
                    //It allows iterating over the deserialized objects because it implements the IEnumerable<T> interface.
                    using (var reader = new SequentialReader<SensorData>(
                        AvroContainer.CreateReader<SensorData>(buffer, true)))
                    {
                        var results = reader.Objects;

                        //Finally, verify that deserialized data matches the original one
                        Console.WriteLine("Comparing Initial and Deserialized Data Sets...");
                        int count = 1;
                        var pairs = testData.Zip(results, (serialized, deserialized) => new { expected = serialized, actual = deserialized });
                        foreach (var pair in pairs)
                        {
                            bool isEqual = this.Equal(pair.expected, pair.actual);
                            Console.WriteLine("For Pair {0} result of Data Set Identity Comparison is {1}", count, isEqual);
                            count++;
                        }
                    }
                }

                //Delete the file
                RemoveFile(path);
            }

            //
            //Helper methods
            //

            //Comparing two SensorData objects
            private bool Equal(SensorData left, SensorData right)
            {
                return left.Position.Equals(right.Position) && left.Value.SequenceEqual(right.Value);
            }

            //Saving memory stream to a new file with the given path
            private bool WriteFile(MemoryStream InputStream, string path)
            {
                if (!File.Exists(path))
                {
                    try
                    {
                        using (FileStream fs = File.Create(path))
                        {
                            InputStream.Seek(0, SeekOrigin.Begin);
                            InputStream.CopyTo(fs);
                        }
                        return true;
                    }
                    catch (Exception e)
                    {
                        Console.WriteLine("The following exception was thrown during creation and writing to the file \"{0}\"", path);
                        Console.WriteLine(e.Message);
                        return false;
                    }
                }
                else
                {
                    Console.WriteLine("Can not create file \"{0}\". File already exists", path);
                    return false;

                }
            }

            //Reading a file content by using the given path to a memory stream
            private bool ReadFile(MemoryStream OutputStream, string path)
            {
                try
                {
                    using (FileStream fs = File.Open(path, FileMode.Open))
                    {
                        fs.CopyTo(OutputStream);
                    }
                    return true;
                }
                catch (Exception e)
                {
                    Console.WriteLine("The following exception was thrown during reading from the file \"{0}\"", path);
                    Console.WriteLine(e.Message);
                    return false;
                }
            }

            //Deleting file by using given path
            private void RemoveFile(string path)
            {
                if (File.Exists(path))
                {
                    try
                    {
                        File.Delete(path);
                    }
                    catch (Exception e)
                    {
                        Console.WriteLine("The following exception was thrown during deleting the file \"{0}\"", path);
                        Console.WriteLine(e.Message);
                    }
                }
                else
                {
                    Console.WriteLine("Can not delete file \"{0}\". File does not exist", path);
                }
            }

            static void Main()
            {

                string sectionDivider = "---------------------------------------- ";

                //Create an instance of AvroSample class and invoke methods
                //illustrating different serializing approaches
                AvroSample Sample = new AvroSample();

                //Serialization using reflection to Avro object container file
                Sample.SerializeDeserializeUsingObjectContainersReflection();

                Console.WriteLine(sectionDivider);
                Console.WriteLine("Press any key to exit.");
                Console.Read();
            }
        }
    }
    // The example is expected to display the following output:
    // SERIALIZATION USING REFLECTION AND AVRO OBJECT CONTAINER FILES
    //
    // Serializing Sample Data Set...
    // Saving serialized data to file...
    // Reading data from file...
    // Deserializing Sample Data Set...
    // Comparing Initial and Deserialized Data Sets...
    // For Pair 1 result of Data Set Identity Comparison is True
    // For Pair 2 result of Data Set Identity Comparison is True
    // ----------------------------------------
    // Press any key to exit.


### <a name="sample-4-serialization-using-object-container-files-and-serialization-with-generic-record"></a>例 4: オブジェクト コンテナー ファイルを使用したシリアル化と汎用レコードを使用したシリアル化
この例は、<a href="#Scenario2"> 2 番目の例</a>のシナリオと同様に、JSON でスキーマが明示的に指定されています。 相違点は、ここでは、スキーマは逆シリアル化するリーダーにはわからないということが前提である点です。

明示的に定義された JSON スキーマを使用してテスト データ セットが [**AvroRecord**](http://msdn.microsoft.com/library/microsoft.hadoop.avro.avrorecord.aspx) オブジェクトのリストに収集され、[**AvroContainer**](http://msdn.microsoft.com/library/microsoft.hadoop.avro.container.avrocontainer.aspx) クラスによって表されるオブジェクト コンテナー ファイルに格納されます。 このコンテナー ファイルは、未圧縮のデータをメモリ ストリームにシリアル化してこれをファイルに保存するために使用されるライターを作成します。 リーダーの作成に使用する [**Codec.Null**](http://msdn.microsoft.com/library/microsoft.hadoop.avro.container.codec.null.aspx) パラメーターは、このデータを圧縮しないことを指定しています。

次に、ファイルからデータが読み取られ、オブジェクトのコレクションに逆シリアルされます。 このコレクションを Avro レコードの初期リストと比較して、その同一性を確認しています。

    namespace Microsoft.Hadoop.Avro.Sample
    {
        using System;
        using System.Collections.Generic;
        using System.IO;
        using System.Linq;
        using System.Runtime.Serialization;
        using Microsoft.Hadoop.Avro.Container;
        using Microsoft.Hadoop.Avro.Schema;
        using Microsoft.Hadoop.Avro;

        //This class contains all methods demonstrating
        //the usage of Microsoft Avro Library
        public class AvroSample
        {

            //Serializes and deserializes a sample data set by using a generic record and Avro object container files.
            //Serialized data is not compressed.
            public void SerializeDeserializeUsingObjectContainersGenericRecord()
            {
                Console.WriteLine("SERIALIZATION USING GENERIC RECORD AND AVRO OBJECT CONTAINER FILES\n");

                //Path for Avro object container file
                string path = "AvroSampleGenericRecordNullCodec.avro";

                Console.WriteLine("Defining the Schema and creating Sample Data Set...");

                //Define the schema in JSON
                const string Schema = @"{
                                ""type"":""record"",
                                ""name"":""Microsoft.Hadoop.Avro.Specifications.SensorData"",
                                ""fields"":
                                    [
                                        {
                                            ""name"":""Location"",
                                            ""type"":
                                                {
                                                    ""type"":""record"",
                                                    ""name"":""Microsoft.Hadoop.Avro.Specifications.Location"",
                                                    ""fields"":
                                                        [
                                                            { ""name"":""Floor"", ""type"":""int"" },
                                                            { ""name"":""Room"", ""type"":""int"" }
                                                        ]
                                                }
                                        },
                                        { ""name"":""Value"", ""type"":""bytes"" }
                                    ]
                            }";

                //Create a generic serializer based on the schema
                var serializer = AvroSerializer.CreateGeneric(Schema);
                var rootSchema = serializer.WriterSchema as RecordSchema;

                //Create a generic record to represent the data
                var testData = new List<AvroRecord>();

                dynamic expected1 = new AvroRecord(rootSchema);
                dynamic location1 = new AvroRecord(rootSchema.GetField("Location").TypeSchema);
                location1.Floor = 1;
                location1.Room = 243;
                expected1.Location = location1;
                expected1.Value = new byte[] { 1, 2, 3, 4, 5 };
                testData.Add(expected1);

                dynamic expected2 = new AvroRecord(rootSchema);
                dynamic location2 = new AvroRecord(rootSchema.GetField("Location").TypeSchema);
                location2.Floor = 1;
                location2.Room = 244;
                expected2.Location = location2;
                expected2.Value = new byte[] { 6, 7, 8, 9 };
                testData.Add(expected2);

                //Serializing and saving data to file.
                //Create a MemoryStream buffer.
                using (var buffer = new MemoryStream())
                {
                    Console.WriteLine("Serializing Sample Data Set...");

                    //Create a SequentialWriter instance for type SensorData, which can serialize a sequence of SensorData objects to stream.
                    //Data will not be compressed (Null compression codec).
                    using (var writer = AvroContainer.CreateGenericWriter(Schema, buffer, Codec.Null))
                    {
                        using (var streamWriter = new SequentialWriter<object>(writer, 24))
                        {
                            // Serialize the data to stream by using the sequential writer
                            testData.ForEach(streamWriter.Write);
                        }
                    }

                    Console.WriteLine("Saving serialized data to file...");

                    //Save stream to file
                    if (!WriteFile(buffer, path))
                    {
                        Console.WriteLine("Error during file operation. Quitting method");
                        return;
                    }
                }

                //Reading and deserializing the data.
                //Create a memory stream buffer.
                using (var buffer = new MemoryStream())
                {
                    Console.WriteLine("Reading data from file...");

                    //Reading data from object container file
                    if (!ReadFile(buffer, path))
                    {
                        Console.WriteLine("Error during file operation. Quitting method");
                        return;
                    }

                    Console.WriteLine("Deserializing Sample Data Set...");

                    //Prepare the stream for deserializing the data
                    buffer.Seek(0, SeekOrigin.Begin);

                    //Create a SequentialReader instance for type SensorData, which will deserialize all serialized objects from the given stream.
                    //It allows iterating over the deserialized objects because it implements the IEnumerable<T> interface.
                    using (var reader = AvroContainer.CreateGenericReader(buffer))
                    {
                        using (var streamReader = new SequentialReader<object>(reader))
                        {
                            var results = streamReader.Objects;

                            Console.WriteLine("Comparing Initial and Deserialized Data Sets...");

                            //Finally, verify the results
                            var pairs = testData.Zip(results, (serialized, deserialized) => new { expected = (dynamic)serialized, actual = (dynamic)deserialized });
                            int count = 1;
                            foreach (var pair in pairs)
                            {
                                bool isEqual = pair.expected.Location.Floor.Equals(pair.actual.Location.Floor);
                                isEqual = isEqual && pair.expected.Location.Room.Equals(pair.actual.Location.Room);
                                isEqual = isEqual && ((byte[])pair.expected.Value).SequenceEqual((byte[])pair.actual.Value);
                                Console.WriteLine("For Pair {0} result of Data Set Identity Comparison is {1}", count, isEqual.ToString());
                                count++;
                            }
                        }
                    }
                }

                //Delete the file
                RemoveFile(path);
            }

            //
            //Helper methods
            //

            //Saving memory stream to a new file with the given path
            private bool WriteFile(MemoryStream InputStream, string path)
            {
                if (!File.Exists(path))
                {
                    try
                    {
                        using (FileStream fs = File.Create(path))
                        {
                            InputStream.Seek(0, SeekOrigin.Begin);
                            InputStream.CopyTo(fs);
                        }
                        return true;
                    }
                    catch (Exception e)
                    {
                        Console.WriteLine("The following exception was thrown during creation and writing to the file \"{0}\"", path);
                        Console.WriteLine(e.Message);
                        return false;
                    }
                }
                else
                {
                    Console.WriteLine("Can not create file \"{0}\". File already exists", path);
                    return false;

                }
            }

            //Reading a file content by using the given path to a memory stream
            private bool ReadFile(MemoryStream OutputStream, string path)
            {
                try
                {
                    using (FileStream fs = File.Open(path, FileMode.Open))
                    {
                        fs.CopyTo(OutputStream);
                    }
                    return true;
                }
                catch (Exception e)
                {
                    Console.WriteLine("The following exception was thrown during reading from the file \"{0}\"", path);
                    Console.WriteLine(e.Message);
                    return false;
                }
            }

            //Deleting file by using the given path
            private void RemoveFile(string path)
            {
                if (File.Exists(path))
                {
                    try
                    {
                        File.Delete(path);
                    }
                    catch (Exception e)
                    {
                        Console.WriteLine("The following exception was thrown during deleting the file \"{0}\"", path);
                        Console.WriteLine(e.Message);
                    }
                }
                else
                {
                    Console.WriteLine("Can not delete file \"{0}\". File does not exist", path);
                }
            }

            static void Main()
            {

                string sectionDivider = "---------------------------------------- ";

                //Create an instance of the AvroSample class and invoke methods
                //illustrating different serializing approaches
                AvroSample Sample = new AvroSample();

                //Serialization using generic record to Avro object container file
                Sample.SerializeDeserializeUsingObjectContainersGenericRecord();

                Console.WriteLine(sectionDivider);
                Console.WriteLine("Press any key to exit.");
                Console.Read();
            }
        }
    }
    // The example is expected to display the following output:
    // SERIALIZATION USING GENERIC RECORD AND AVRO OBJECT CONTAINER FILES
    //
    // Defining the Schema and creating Sample Data Set...
    // Serializing Sample Data Set...
    // Saving serialized data to file...
    // Reading data from file...
    // Deserializing Sample Data Set...
    // Comparing Initial and Deserialized Data Sets...
    // For Pair 1 result of Data Set Identity Comparison is True
    // For Pair 2 result of Data Set Identity Comparison is True
    // ----------------------------------------
    // Press any key to exit.




### <a name="sample-5-serialization-using-object-container-files-with-a-custom-compression-codec"></a>例 5: オブジェクト コンテナー ファイルとカスタム圧縮コーデックを使用したシリアル化
5 つ目の例では、Avro オブジェクト コンテナー ファイルに対してカスタム圧縮コーデックを使用する方法を紹介します。 この例を含むサンプルは、 [Azure コード サンプル](http://code.msdn.microsoft.com/windowsazure/Serialize-data-with-the-67159111) サイトからダウンロードできます。

[Avro 仕様](http://avro.apache.org/docs/current/spec.html#Required+Codecs)では、(既定の **Null** と **Deflate** 以外に) オプションの圧縮コーデックを使用することもできます。 この例には、(サポートされるオプションのコーデックとして [Avro Specification](http://avro.apache.org/docs/current/spec.html#snappy)に明記されている) Snappy のような新しいコーデックは実装されていません。 この例では、既定の .NET Framework 4 バージョンよりも [zlib](http://zlib.net/) 圧縮ライブラリに基づくより優れた圧縮アルゴリズムを提供する [**Deflate**][deflate-110] コーデックの .NET Framework 4.5 実装の使用法を紹介します。

    //
    // This code needs to be compiled with the parameter Target Framework set as ".NET Framework 4.5"
    // to ensure the desired implementation of the Deflate compression algorithm is used.
    // Ensure your C# project is set up accordingly.
    //

    namespace Microsoft.Hadoop.Avro.Sample
    {
        using System;
        using System.Collections.Generic;
        using System.Diagnostics;
        using System.IO;
        using System.IO.Compression;
        using System.Linq;
        using System.Runtime.Serialization;
        using Microsoft.Hadoop.Avro.Container;
        using Microsoft.Hadoop.Avro;

        #region Defining objects for serialization
        //Sample class used in serialization samples
        [DataContract(Name = "SensorDataValue", Namespace = "Sensors")]
        internal class SensorData
        {
            [DataMember(Name = "Location")]
            public Location Position { get; set; }

            [DataMember(Name = "Value")]
            public byte[] Value { get; set; }
        }

        //Sample struct used in serialization samples
        [DataContract]
        internal struct Location
        {
            [DataMember]
            public int Floor { get; set; }

            [DataMember]
            public int Room { get; set; }
        }
        #endregion

        #region Defining custom codec based on .NET Framework V.4.5 Deflate
        //Avro.NET codec class contains two methods,
        //GetCompressedStreamOver(Stream uncompressed) and GetDecompressedStreamOver(Stream compressed),
        //which are the key ones for data compression.
        //To enable a custom codec, one needs to implement these methods for the required codec.

        #region Defining Compression and Decompression Streams
        //DeflateStream (class from System.IO.Compression namespace that implements Deflate algorithm)
        //cannot be directly used for Avro because it does not support vital operations like Seek.
        //Thus one needs to implement two classes inherited from stream
        //(one for compressed and one for decompressed stream)
        //that use Deflate compression and implement all required features.
        internal sealed class CompressionStreamDeflate45 : Stream
        {
            private readonly Stream buffer;
            private DeflateStream compressionStream;

            public CompressionStreamDeflate45(Stream buffer)
            {
                Debug.Assert(buffer != null, "Buffer is not allowed to be null.");

                this.compressionStream = new DeflateStream(buffer, CompressionLevel.Fastest, true);
                this.buffer = buffer;
            }

            public override bool CanRead
            {
                get { return this.buffer.CanRead; }
            }

            public override bool CanSeek
            {
                get { return true; }
            }

            public override bool CanWrite
            {
                get { return this.buffer.CanWrite; }
            }

            public override void Flush()
            {
                this.compressionStream.Close();
            }

            public override long Length
            {
                get { return this.buffer.Length; }
            }

            public override long Position
            {
                get
                {
                    return this.buffer.Position;
                }

                set
                {
                    this.buffer.Position = value;
                }
            }

            public override int Read(byte[] buffer, int offset, int count)
            {
                return this.buffer.Read(buffer, offset, count);
            }

            public override long Seek(long offset, SeekOrigin origin)
            {
                return this.buffer.Seek(offset, origin);
            }

            public override void SetLength(long value)
            {
                throw new NotSupportedException();
            }

            public override void Write(byte[] buffer, int offset, int count)
            {
                this.compressionStream.Write(buffer, offset, count);
            }

            protected override void Dispose(bool disposed)
            {
                base.Dispose(disposed);

                if (disposed)
                {
                    this.compressionStream.Dispose();
                    this.compressionStream = null;
                }
            }
        }

        internal sealed class DecompressionStreamDeflate45 : Stream
        {
            private readonly DeflateStream decompressed;

            public DecompressionStreamDeflate45(Stream compressed)
            {
                this.decompressed = new DeflateStream(compressed, CompressionMode.Decompress, true);
            }

            public override bool CanRead
            {
                get { return true; }
            }

            public override bool CanSeek
            {
                get { return true; }
            }

            public override bool CanWrite
            {
                get { return false; }
            }

            public override void Flush()
            {
                this.decompressed.Close();
            }

            public override long Length
            {
                get { return this.decompressed.Length; }
            }

            public override long Position
            {
                get
                {
                    return this.decompressed.Position;
                }

                set
                {
                    throw new NotSupportedException();
                }
            }

            public override int Read(byte[] buffer, int offset, int count)
            {
                return this.decompressed.Read(buffer, offset, count);
            }

            public override long Seek(long offset, SeekOrigin origin)
            {
                throw new NotSupportedException();
            }

            public override void SetLength(long value)
            {
                throw new NotSupportedException();
            }

            public override void Write(byte[] buffer, int offset, int count)
            {
                throw new NotSupportedException();
            }

            protected override void Dispose(bool disposing)
            {
                base.Dispose(disposing);

                if (disposing)
                {
                    this.decompressed.Dispose();
                }
            }
        }
        #endregion

        #region Define Codec
        //Define the actual codec class containing the required methods for manipulating streams:
        //GetCompressedStreamOver(Stream uncompressed) and GetDecompressedStreamOver(Stream compressed).
        //Codec class uses classes for compressed and decompressed streams defined above.
        internal sealed class DeflateCodec45 : Codec
        {

            //We merely use different IMPLEMENTATIONS of Deflate, so CodecName remains "deflate"
            public static readonly string CodecName = "deflate";

            public DeflateCodec45()
                : base(CodecName)
            {
            }

            public override Stream GetCompressedStreamOver(Stream decompressed)
            {
                if (decompressed == null)
                {
                    throw new ArgumentNullException("decompressed");
                }

                return new CompressionStreamDeflate45(decompressed);
            }

            public override Stream GetDecompressedStreamOver(Stream compressed)
            {
                if (compressed == null)
                {
                    throw new ArgumentNullException("compressed");
                }

                return new DecompressionStreamDeflate45(compressed);
            }
        }
        #endregion

        #region Define modified Codec Factory
        //Define modified codec factory to be used in the reader.
        //It will catch the attempt to use "Deflate" and provide  a custom codec.
        //For all other cases, it will rely on the base class (CodecFactory).
        internal sealed class CodecFactoryDeflate45 : CodecFactory
        {

            public override Codec Create(string codecName)
            {
                if (codecName == DeflateCodec45.CodecName)
                    return new DeflateCodec45();
                else
                    return base.Create(codecName);
            }
        }
        #endregion

        #endregion

        #region Sample Class with demonstration methods
        //This class contains methods demonstrating
        //the usage of Microsoft Avro Library
        public class AvroSample
        {

            //Serializes and deserializes sample data set by using reflection and Avro object container files.
            //Serialized data is compressed with the custom compression codec (Deflate of .NET Framework 4.5).
            //
            //This sample uses memory stream for all operations related to serialization, deserialization and
            //object container manipulation, though file stream could be easily used.
            public void SerializeDeserializeUsingObjectContainersReflectionCustomCodec()
            {

                Console.WriteLine("SERIALIZATION USING REFLECTION, AVRO OBJECT CONTAINER FILES AND CUSTOM CODEC\n");

                //Path for Avro object container file
                string path = "AvroSampleReflectionDeflate45.avro";

                //Create a data set by using sample class and struct
                var testData = new List<SensorData>
                        {
                            new SensorData { Value = new byte[] { 1, 2, 3, 4, 5 }, Position = new Location { Room = 243, Floor = 1 } },
                            new SensorData { Value = new byte[] { 6, 7, 8, 9 }, Position = new Location { Room = 244, Floor = 1 } }
                        };

                //Serializing and saving data to file.
                //Creating a memory stream buffer.
                using (var buffer = new MemoryStream())
                {
                    Console.WriteLine("Serializing Sample Data Set...");

                    //Create a SequentialWriter instance for type SensorData, which can serialize a sequence of SensorData objects to stream.
                    //Here the custom codec is introduced. For convenience, the next commented code line shows how to use built-in Deflate.
                    //Note that because the sample deals with different IMPLEMENTATIONS of Deflate, built-in and custom codecs are interchangeable
                    //in read-write operations.
                    //using (var w = AvroContainer.CreateWriter<SensorData>(buffer, Codec.Deflate))
                    using (var w = AvroContainer.CreateWriter<SensorData>(buffer, new DeflateCodec45()))
                    {
                        using (var writer = new SequentialWriter<SensorData>(w, 24))
                        {
                            // Serialize the data to stream using the sequential writer
                            testData.ForEach(writer.Write);
                        }
                    }

                    //Save stream to file
                    Console.WriteLine("Saving serialized data to file...");
                    if (!WriteFile(buffer, path))
                    {
                        Console.WriteLine("Error during file operation. Quitting method");
                        return;
                    }
                }

                //Reading and deserializing data.
                //Creating a memory stream buffer.
                using (var buffer = new MemoryStream())
                {
                    Console.WriteLine("Reading data from file...");

                    //Reading data from object container file
                    if (!ReadFile(buffer, path))
                    {
                        Console.WriteLine("Error during file operation. Quitting method");
                        return;
                    }

                    Console.WriteLine("Deserializing Sample Data Set...");

                    //Prepare the stream for deserializing the data
                    buffer.Seek(0, SeekOrigin.Begin);

                    //Because of SequentialReader<T> constructor signature, an AvroSerializerSettings instance is required
                    //when codec factory is explicitly specified.
                    //You may comment the line below if you want to use built-in Deflate (see next comment).
                    AvroSerializerSettings settings = new AvroSerializerSettings();

                    //Create a SequentialReader instance for type SensorData, which will deserialize all serialized objects from the given stream.
                    //It allows iterating over the deserialized objects because it implements the IEnumerable<T> interface.
                    //Here the custom codec factory is introduced.
                    //For convenience, the next commented code line shows how to use built-in Deflate
                    //(no explicit Codec Factory parameter is required in this case).
                    //Note that because the sample deals with different IMPLEMENTATIONS of Deflate, built-in and custom codecs are interchangeable
                    //in read-write operations.
                    //using (var reader = new SequentialReader<SensorData>(AvroContainer.CreateReader<SensorData>(buffer, true)))
                    using (var reader = new SequentialReader<SensorData>(
                        AvroContainer.CreateReader<SensorData>(buffer, true, settings, new CodecFactoryDeflate45())))
                    {
                        var results = reader.Objects;

                        //Finally, verify that deserialized data matches the original one
                        Console.WriteLine("Comparing Initial and Deserialized Data Sets...");
                        bool isEqual;
                        int count = 1;
                        var pairs = testData.Zip(results, (serialized, deserialized) => new { expected = serialized, actual = deserialized });
                        foreach (var pair in pairs)
                        {
                            isEqual = this.Equal(pair.expected, pair.actual);
                            Console.WriteLine("For Pair {0} result of Data Set Identity Comparison is {1}", count, isEqual.ToString());
                            count++;
                        }
                    }
                }

                //Delete the file
                RemoveFile(path);
            }
        #endregion

            #region Helper Methods

            //Comparing two SensorData objects
            private bool Equal(SensorData left, SensorData right)
            {
                return left.Position.Equals(right.Position) && left.Value.SequenceEqual(right.Value);
            }

            //Saving memory stream to a new file with the given path
            private bool WriteFile(MemoryStream InputStream, string path)
            {
                if (!File.Exists(path))
                {
                    try
                    {
                        using (FileStream fs = File.Create(path))
                        {
                            InputStream.Seek(0, SeekOrigin.Begin);
                            InputStream.CopyTo(fs);
                        }
                        return true;
                    }
                    catch (Exception e)
                    {
                        Console.WriteLine("The following exception was thrown during creation and writing to the file \"{0}\"", path);
                        Console.WriteLine(e.Message);
                        return false;
                    }
                }
                else
                {
                    Console.WriteLine("Can not create file \"{0}\". File already exists", path);
                    return false;

                }
            }

            //Reading file content by using the given path to a memory stream
            private bool ReadFile(MemoryStream OutputStream, string path)
            {
                try
                {
                    using (FileStream fs = File.Open(path, FileMode.Open))
                    {
                        fs.CopyTo(OutputStream);
                    }
                    return true;
                }
                catch (Exception e)
                {
                    Console.WriteLine("The following exception was thrown during reading from the file \"{0}\"", path);
                    Console.WriteLine(e.Message);
                    return false;
                }
            }

            //Deleting file by using given path
            private void RemoveFile(string path)
            {
                if (File.Exists(path))
                {
                    try
                    {
                        File.Delete(path);
                    }
                    catch (Exception e)
                    {
                        Console.WriteLine("The following exception was thrown during deleting the file \"{0}\"", path);
                        Console.WriteLine(e.Message);
                    }
                }
                else
                {
                    Console.WriteLine("Can not delete file \"{0}\". File does not exist", path);
                }
            }
            #endregion

            static void Main()
            {

                string sectionDivider = "---------------------------------------- ";

                //Create an instance of AvroSample Class and invoke methods
                //illustrating different serializing approaches
                AvroSample Sample = new AvroSample();

                //Serialization using reflection to Avro object container file using custom codec
                Sample.SerializeDeserializeUsingObjectContainersReflectionCustomCodec();

                Console.WriteLine(sectionDivider);
                Console.WriteLine("Press any key to exit.");
                Console.Read();
            }
        }
    }
    // The example is expected to display the following output:
    // SERIALIZATION USING REFLECTION, AVRO OBJECT CONTAINER FILES AND CUSTOM CODEC
    //
    // Serializing Sample Data Set...
    // Saving serialized data to file...
    // Reading data from file...
    // Deserializing Sample Data Set...
    // Comparing Initial and Deserialized Data Sets...
    // For Pair 1 result of Data Set Identity Comparison is True
    //For Pair 2 result of Data Set Identity Comparison is True
    // ----------------------------------------
    // Press any key to exit.

### <a name="sample-6-using-avro-to-upload-data-for-the-microsoft-azure-hdinsight-service"></a>例 6: Avro を使用した Microsoft Azure HDInsight サービスのデータのアップロード
6 つ目の例では、Azure HDInsight サービスの操作に関連する、いくつかのプログラム方法を紹介します。 この例を含むサンプルは、 [Azure コード サンプル](https://code.msdn.microsoft.com/windowsazure/Using-Avro-to-upload-data-ae81b1e3) サイトからダウンロードできます。

このサンプルでは、次の処理を実行します。

* 既存の HDInsight サービス クラスターに接続する。
* いくつかの CSV ファイルをシリアル化し、結果を Azure BLOB ストレージにアップロードする。 CSV ファイルはサンプルと一緒に配布され、 [Infochimps](http://www.infochimps.com/) から配信される、1970 ～ 2010 年の間の AMEX Stock 履歴データから抽出されます。 サンプルから CSV ファイルのデータを読み取り、レコードを **Stock** クラスのインスタンスに変換してから、リフレクションを使用してそれらをシリアル化します。 Microsoft Avro ライブラリのコード生成ユーティリティを使用して、Stock 型の定義が JSON スキーマから作成されます。
* **Stocks** という新しい外部テーブルが Hive に作成され、前の手順でアップロードしたデータとリンクされます。
* Hive を使用して **Stocks** テーブルに関するクエリを実行します。

さらにサンプルでは、主要な操作を実行する前後に、クリーンアップ手順を実行します。 クリーンアップ中、関連する Azure BLOB のデータとフォルダーはすべて削除され、Hive テーブルが消去されます。 クリーンアップ手順は、サンプルのコマンド ラインから呼び出すこともできます。

このサンプルの前提条件は次のとおりです。

* 有効な Microsoft Azure サブスクリプションとその ID。
* サブスクリプションの管理証明書と対応する秘密キー。 証明書は、サンプルを実行するコンピューターの、現在のユーザーの Private Storage にインストールする必要があります。
* アクティブな HDInsight クラスター。
* 前の前提条件で HDInsight クラスターにリンクされた Azure Storage アカウントと、対応するプライマリまたはセカンダリのアクセス キー。

この前提条件の情報をすべてサンプル構成ファイルに入力してから、サンプルを実行する必要があります。 その方法は次の 2 つです。

* サンプルのルート ディレクトリにある app.config ファイルを編集し、その後サンプルを作成する。または、
* 最初にサンプルを作成してから、ビルド ディレクトリの AvroHDISample.exe.config を編集する。

どちらの場合も、編集はすべて **<appSettings>** 設定セクションで行います。 ファイルのコメントに従ってください。
次のコマンドを実行して、サンプルをコマンド ラインから実行します (サンプルを含む zip ファイルは、基本的に C:\AvroHDISample に抽出されます。それ以外の場所を指定する場合は、該当するファイル パスを使用します)。

    AvroHDISample run C:\AvroHDISample\Data

クラスターをクリーンアップするには、次のコマンドを実行します。

    AvroHDISample clean

[deflate-100]: http://msdn.microsoft.com/library/system.io.compression.deflatestream(v=vs.100).aspx
[deflate-110]: http://msdn.microsoft.com/library/system.io.compression.deflatestream(v=vs.110).aspx



<!--HONumber=Dec16_HO2-->


