---
title: Azure Stream Analytics Edge ジョブの .NET Standard 関数の開発 (プレビュー)
description: Stream Analytics Edge ジョブの C# ユーザー定義関数を記述する方法について説明します。
services: stream-analytics
author: mamccrea
ms.author: mamccrea
manager: kfile
ms.reviewer: mamccrea
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 12/06/2018
ms.custom: seodec18
ms.openlocfilehash: 5df4c9dfe18b02ade3a37717da9c68acbfcf1853
ms.sourcegitcommit: 9fb6f44dbdaf9002ac4f411781bf1bd25c191e26
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/08/2018
ms.locfileid: "53106602"
---
# <a name="develop-net-standard-user-defined-functions-for-azure-stream-analytics-edge-jobs-preview"></a>Azure Stream Analytics Edge ジョブの .NET Standard ユーザー定義関数の開発 (プレビュー)

Azure Stream Analytics では、イベント データのストリームを介して変換や計算を実行するための SQL に似たクエリ言語が提供されます。 多くの組み込み関数がありますが、柔軟性を高める必要がある複雑なシナリオもあります。 .NET Standard ユーザー定義関数 (UDF) では、任意の .NET 標準言語 (C#、F# など) で記述された独自の関数を呼び出して、Stream Analytics クエリ言語を拡張できます。 UDF を使用すると、複雑な数学計算の実行や、ML.NET を使用したカスタム ML モデルのインポートが可能になります。また、欠損データにカスタム欠損値補完ロジックを使用することもできます。 Stream Analytics Edge ジョブの UDF 機能は現在プレビュー段階にあります。運用環境のワークロードでは使用しないでください。

## <a name="overview"></a>概要
Azure Stream Analytics 用 Visual Studio Tools を使用すると、UDF の記述、ローカルでのジョブのテスト (オフラインでも可能)、Azure への Stream Analytics ジョブの発行が容易になります。 Azure に発行されたら、IoT Hub を使用してジョブを IoT デバイスに展開できます。

UDF は次の 3 つの方法で実装できます。

* ASA プロジェクト内の分離コード ファイル
* ローカル プロジェクトの UDF
* Azure ストレージ アカウントの既存のパッケージ

## <a name="package-path"></a>パッケージ パス

UDF パッケージの形式では、パス `/UserCustomCode/CLR/*` を使用します。 ダイナミック リンク ライブラリ (DLL) とリソースは `/UserCustomCode/CLR/*` フォルダーの下にコピーされるので、システム DLL と Azure Stream Analytics DLL からユーザー DLL を分離できます。 このパッケージ パスは、関数を使用するために使用される方法に関係なく、すべての関数に使用されます。

## <a name="supported-types-and-mapping"></a>サポートされている型とマッピング

|**UDF の型 (C#)**  |**Azure Stream Analytics の型**  |
|---------|---------|
|long  |  bigint   |
|double  |  double   |
|string  |  nvarchar(max)   |
|dateTime  |  dateTime   |
|struct  |  IRecord   |
|オブジェクト  |  IRecord   |
|Array<object>  |  IArray   |
|dictionary<string, object>  |  IRecord   |

## <a name="codebehind"></a>分離コード
**Script.asql** 分離コードにユーザー定義関数を記述できます。 Visual Studio Tools により、分離コード ファイルがアセンブリ ファイルに自動的にコンパイルされます。 ジョブを Azure に送信すると、アセンブリが ZIP ファイルとしてパッケージ化され、ストレージ アカウントにアップロードされます。 分離コードを使用して C# UDF を記述する方法については、[Stream Analytics Edge ジョブの C# UDF](stream-analytics-edge-csharp-udf.md) に関するチュートリアルをご覧ください。 

## <a name="local-project"></a>ローカル プロジェクト
ユーザー定義関数は、後で Azure Stream Analytics クエリで参照されるアセンブリに記述できます。 これは、手続き型ロジックや再帰など、式言語の枠を超えて .NET Standard 言語の機能を最大限に活用する必要がある複雑な関数に推奨される方法です。 複数の Azure Stream Analytics クエリで関数ロジックを共有する必要がある場合は、ローカル プロジェクトの UDF を使用することもできます。 UDF をローカル プロジェクトに追加すると、Visual Studio からローカルで関数をデバッグしてテストすることができます。

ローカル プロジェクトを参照するには、次の手順に従います。

1. ソリューションに新しいクラス ライブラリを作成します。
2. クラスにコードを記述します。 クラスは *public* として定義し、オブジェクトは *static public* として定義する必要があることに注意してください。 
3. プロジェクトをビルドします。 ツールによって、bin フォルダー内のすべての成果物が ZIP ファイルにパッケージ化され、ZIP ファイルがストレージ アカウントにアップロードされます。 外部参照の場合は、NuGet パッケージの代わりにアセンブリ参照を使用します。
4. Azure Stream Analytics プロジェクトで新しいクラスを参照します。
5. Azure Stream Analytics プロジェクトに新しい関数を追加します。
6. ジョブ構成ファイル (`JobConfig.json`) でアセンブリ パスを構成します。 アセンブリ パスを**ローカル プロジェクト参照または分離コード**に設定します。
7. 関数プロジェクトと Azure Stream Analytics プロジェクトの両方をリビルドします。  

### <a name="example"></a>例

この例では、**UDFTest** は C# クラス ライブラリ プロジェクトです。**ASAEdgeUDFDemo** は Azure Stream Analytics Edge プロジェクトであり、**UDFTest** を参照します。

![Visual Studio の Azure Stream Analytics IoT Edge プロジェクト](./media/stream-analytics-edge-csharp-udf-methods/stream-analytics-edge-udf-demo.png)

1. C# プロジェクトをビルドします。これにより、Azure Stream Analytics クエリから C# UDF への参照を追加できるようになります。
    
   ![Visual Studio で Azure Stream Analytics IoT Edge プロジェクトをビルドする](./media/stream-analytics-edge-csharp-udf-methods/stream-analytics-edge-udf-build-project.png)

2. ASA Edge プロジェクトに C# プロジェクトへの参照を追加します。 [参照] ノードを右クリックし、[参照の追加] を選択します。

   ![Visual Studio で C# プロジェクトへの参照を追加する](./media/stream-analytics-edge-csharp-udf-methods/stream-analytics-edge-udf-add-reference.png)

3. 一覧から C# プロジェクト名を選択します。 
    
   ![参照一覧から C# プロジェクト名を選択する](./media/stream-analytics-edge-csharp-udf-methods/stream-analytics-edge-udf-choose-project-name.png)

4. **ソリューション エクスプローラー**の **[参照]** の下に **[UDFTest]** が表示されます。

   ![ソリューション エクスプローラーにユーザー定義関数参照が表示される](./media/stream-analytics-edge-csharp-udf-methods/stream-analytics-edge-udf-added-reference.png)

5. **[関数]** フォルダーを右クリックし、**[新しいアイテム]** を選択します。

   ![Azure Stream Analytics Edge ソリューションの [関数] に新しいアイテムを追加する](./media/stream-analytics-edge-csharp-udf-methods/stream-analytics-edge-udf-add-csharp-function.png)

6. Azure Stream Analytics プロジェクトに、C# 関数 **SquareFunction.json** を追加します。

   ![Visual Studio で Stream Analytics Edge のアイテムから C# 関数を選択する](./media/stream-analytics-edge-csharp-udf-methods/stream-analytics-edge-udf-add-csharp-function-2.png)

7. **ソリューション エクスプローラー**で関数をダブルクリックして、構成ダイアログを開きます。

   ![Visual Studio での C# 関数の構成](./media/stream-analytics-edge-csharp-udf-methods/stream-analytics-edge-udf-csharp-function-config.png)

8. C# 関数の構成で、**[Load from ASA Project Reference]\(ASA プロジェクト参照から読み込む\)** を選択し、ドロップダウン リストから関連するアセンブリ、クラス、メソッドの名前を選択します。 Stream Analytics Edge クエリでメソッド、型、関数を参照するには、クラスを *public* として定義し、オブジェクトを *static public* として定義する必要があります。

   ![Stream Analytics の C# 関数の構成](./media/stream-analytics-edge-csharp-udf-methods/stream-analytics-edge-udf-asa-csharp-function-config.png)

## <a name="existing-packages"></a>既存のパッケージ

任意の IDE で .NET Standard UDF を作成し、Azure Stream Analytics クエリから呼び出すことができます。 まず、コードをコンパイルし、すべての DLL をパッケージ化します。 パッケージの形式では、パス `/UserCustomCode/CLR/*` を使用します。 その後、Azure ストレージ アカウントのコンテナーのルートに `UserCustomCode.zip` をアップロードします。

アセンブリの zip パッケージが Azure ストレージ アカウントにアップロードされたら、Azure Stream Analytics クエリで関数を使用できます。 必要なのは、Stream Analytics Edge ジョブ構成にストレージ情報を含めることだけです。 Visual Studio Tools ではパッケージをダウンロードしないため、このオプションを使用して関数をローカルでテストすることはできません。 パッケージ パスはサービスに直接解析されます。 

ジョブ構成ファイル (`JobConfig.json`) でアセンブリ パスを構成するには、次の操作を行います。

**[User-Defined Code Configuration]\(ユーザー定義コードの構成\)** セクションを展開し、構成に次の推奨値を入力します。

 |**設定**  |**推奨値**  |
 |---------|---------|
 |アセンブリ ソース  | クラウドの既存のアセンブリ パッケージ。    |
 |リソース  |  現在のアカウントからデータを選択します   |
 |サブスクリプション  |  サブスクリプションを選択します。   |
 |ストレージ アカウント  |  ストレージ アカウントを選択します。   |
 |コンテナー  |  ストレージ アカウントに作成したコンテナーを選択します。   |

![Visual Studio での Azure Stream Analytics Edge ジョブの構成](./media/stream-analytics-edge-csharp-udf-methods/stream-analytics-edge-job-config.png)

## <a name="limitations"></a>制限事項
現在、UDF プレビューには次の制限があります。

* .NET Standard 言語は、IoT Edge の Azure Stream Analytics でのみ使用できます。 クラウド ジョブの場合、JavaScript ユーザー定義関数を記述できます。 詳細については、[Azure Stream Analytics の JavaScript UDF](stream-analytics-javascript-user-defined-functions.md) に関するチュートリアルをご覧ください。

* .NET Standard UDF は Visual Studio でのみ作成でき、Azure に発行できます。 Azure portal の **[関数]** には、.NET Standard UDF の読み取り専用バージョンが表示されます。 .NET Standard 関数の作成は、Azure portal ではサポートされていません。

* Azure portal で .NET Standard UDF を使用すると、ポータルのクエリ エディターにエラーが表示されます。 

* カスタム コードは Azure Stream Analytics エンジンとコンテキストを共有するため、名前空間/dll_name が Azure Stream Analytics コードと競合するものをカスタム コードで参照することはできません。 たとえば、*Newtonsoft Json* を参照することはできません。

## <a name="next-steps"></a>次の手順

* [チュートリアル:Azure Stream Analytics Edge ジョブの C# ユーザー定義関数を記述する (プレビュー)](stream-analytics-edge-csharp-udf.md)
* [チュートリアル:Azure Stream Analytics の JavaScript ユーザー定義関数](stream-analytics-javascript-user-defined-functions.md)
* [Visual Studio を使用して Azure Stream Analytics ジョブを表示する](stream-analytics-vs-tools.md)
