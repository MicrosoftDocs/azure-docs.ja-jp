---
title: Azure Stream Analytics ジョブ用の .NET Standard 関数を開発する (プレビュー)
description: Stream Analytics ジョブ用の C# ユーザー定義関数を記述する方法について説明します。
author: mamccrea
ms.author: mamccrea
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 10/28/2019
ms.custom: seodec18, devx-track-csharp
ms.openlocfilehash: fff1a228e32f115c498678a654f6c8f028772161
ms.sourcegitcommit: 419cf179f9597936378ed5098ef77437dbf16295
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/27/2020
ms.locfileid: "89015681"
---
# <a name="develop-net-standard-user-defined-functions-for-azure-stream-analytics-jobs-preview"></a>Azure Stream Analytics ジョブ用の .NET Standard ユーザー定義関数を開発する (プレビュー)

Azure Stream Analytics では、イベント データのストリームを介して変換や計算を実行するための SQL に似たクエリ言語が提供されます。 多くの組み込み関数がありますが、柔軟性を高める必要がある複雑なシナリオもあります。 .NET Standard ユーザー定義関数 (UDF) では、任意の .NET 標準言語 (C#、F# など) で記述された独自の関数を呼び出して、Stream Analytics クエリ言語を拡張できます。 UDF を使用すると、複雑な数学計算の実行や、ML.NET を使用したカスタム ML モデルのインポートが可能になります。また、欠損データにカスタム欠損値補完ロジックを使用することもできます。 Stream Analytics ジョブの UDF 機能は現在プレビュー段階にあります。運用環境のワークロードでは使用しないでください。

クラウド ジョブ用の .NET ユーザー定義関数は、以下で利用できます。
* 米国中西部
* 北ヨーロッパ
* 米国東部
* 米国西部
* 米国東部 2
* 西ヨーロッパ

別のリージョンでこの機能を使用することに関心がある場合は、[アクセスを要求する](https://aka.ms/ccodereqregion)ことができます。

## <a name="overview"></a>概要
Azure Stream Analytics 用 Visual Studio Tools を使用すると、UDF の記述、ローカルでのジョブのテスト (オフラインでも可能)、Azure への Stream Analytics ジョブの発行が容易になります。 Azure に発行されたら、IoT Hub を使用してジョブを IoT デバイスに展開できます。

UDF は次の 3 つの方法で実装できます。

* ASA プロジェクト内の分離コード ファイル
* ローカル プロジェクトの UDF
* Azure ストレージ アカウントの既存のパッケージ

## <a name="package-path"></a>パッケージ パス

UDF パッケージの形式では、パス `/UserCustomCode/CLR/*` を使用します。 ダイナミック リンク ライブラリ (DLL) とリソースは `/UserCustomCode/CLR/*` フォルダーの下にコピーされるので、システム DLL と Azure Stream Analytics DLL からユーザー DLL を分離できます。 このパッケージ パスは、関数を使用するために使用される方法に関係なく、すべての関数に使用されます。

## <a name="supported-types-and-mapping"></a>サポートされている型とマッピング
Azure Stream Analytics の値を C# で使用するためには、環境間でマーシャリングする必要があります。 マーシャリングは、UDF のすべての入力パラメーターについて実行されます。 次の表に示したように、Azure Stream Analytics のすべての型には、対応する型が C# に存在します。

|**Azure Stream Analytics の型** |**C# の型** |
|---------|---------|
|bigint | long |
|float | double |
|nvarchar(max) | string |
|DATETIME | DateTime |
|Record | Dictionary\<string, object> |
|Array | Object[] |

C# から Azure Stream Analytics へのデータのマーシャリング (UDF の出力値に対して実行されます) にも同じことが言えます。 サポートされる型を次の表に示します。

|**C# の型**  |**Azure Stream Analytics の型**  |
|---------|---------|
|long  |  bigint   |
|double  |  float   |
|string  |  nvarchar(max)   |
|DateTime  |  dateTime   |
|struct  |  Record   |
|object  |  Record   |
|Object[]  |  Array   |
|Dictionary\<string, object>  |  Record   |

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

この例で、**UDFTest** は C# クラス ライブラリ プロジェクトです。**ASAUDFDemo** は Azure Stream Analytics プロジェクトであり、**UDFTest** を参照します。

![Visual Studio の Azure Stream Analytics IoT Edge プロジェクト](./media/stream-analytics-edge-csharp-udf-methods/stream-analytics-edge-udf-demo.png)

1. C# プロジェクトをビルドします。これにより、Azure Stream Analytics クエリから C# UDF への参照を追加できるようになります。
    
   ![Visual Studio で Azure Stream Analytics IoT Edge プロジェクトをビルドする](./media/stream-analytics-edge-csharp-udf-methods/stream-analytics-edge-udf-build-project.png)

2. ASA プロジェクトに C# プロジェクトへの参照を追加します。 [参照] ノードを右クリックし、[参照の追加] を選択します。

   ![Visual Studio で C# プロジェクトへの参照を追加する](./media/stream-analytics-edge-csharp-udf-methods/stream-analytics-edge-udf-add-reference.png)

3. 一覧から C# プロジェクト名を選択します。 
    
   ![参照一覧から C# プロジェクト名を選択する](./media/stream-analytics-edge-csharp-udf-methods/stream-analytics-edge-udf-choose-project-name.png)

4. **ソリューション エクスプローラー**の **[参照]** の下に **[UDFTest]** が表示されます。

   ![ソリューション エクスプローラーにユーザー定義関数参照が表示される](./media/stream-analytics-edge-csharp-udf-methods/stream-analytics-edge-udf-added-reference.png)

5. **[関数]** フォルダーを右クリックし、 **[新しいアイテム]** を選択します。

   ![Azure Stream Analytics Edge ソリューションの [関数] に新しいアイテムを追加する](./media/stream-analytics-edge-csharp-udf-methods/stream-analytics-edge-udf-add-csharp-function.png)

6. Azure Stream Analytics プロジェクトに、C# 関数 **SquareFunction.json** を追加します。

   ![Visual Studio で Stream Analytics Edge のアイテムから C# 関数を選択する](./media/stream-analytics-edge-csharp-udf-methods/stream-analytics-edge-udf-add-csharp-function-2.png)

7. **ソリューション エクスプローラー**で関数をダブルクリックして、構成ダイアログを開きます。

   ![Visual Studio での C# 関数の構成](./media/stream-analytics-edge-csharp-udf-methods/stream-analytics-edge-udf-csharp-function-config.png)

8. C# 関数の構成で、 **[Load from ASA Project Reference]\(ASA プロジェクト参照から読み込む\)** を選択し、ドロップダウン リストから関連するアセンブリ、クラス、メソッドの名前を選択します。 Stream Analytics クエリでメソッド、型、関数を参照するには、クラスを *public* として定義し、オブジェクトを *static public* として定義する必要があります。

   ![Stream Analytics の C# 関数の構成](./media/stream-analytics-edge-csharp-udf-methods/stream-analytics-edge-udf-asa-csharp-function-config.png)

## <a name="existing-packages"></a>既存のパッケージ

任意の IDE で .NET Standard UDF を作成し、Azure Stream Analytics クエリから呼び出すことができます。 まず、コードをコンパイルし、すべての DLL をパッケージ化します。 パッケージの形式では、パス `/UserCustomCode/CLR/*` を使用します。 その後、Azure ストレージ アカウントのコンテナーのルートに `UserCustomCode.zip` をアップロードします。

アセンブリの zip パッケージが Azure ストレージ アカウントにアップロードされたら、Azure Stream Analytics クエリで関数を使用できます。 必要なのは、Stream Analytics ジョブ構成にストレージ情報を含めることだけです。 Visual Studio Tools ではパッケージをダウンロードしないため、このオプションを使用して関数をローカルでテストすることはできません。 パッケージ パスはサービスに直接解析されます。 

ジョブ構成ファイル (`JobConfig.json`) でアセンブリ パスを構成するには、次の操作を行います。

**[User-Defined Code Configuration]\(ユーザー定義コードの構成\)** セクションを展開し、構成に次の推奨値を入力します。

   |**設定**|**推奨値**|
   |-------|---------------|
   |グローバル ストレージ設定のリソース|現在のアカウントからデータ ソースを選択します|
   |グローバル ストレージ設定のサブスクリプション| <お客様のサブスクリプション>|
   |グローバル ストレージ設定のストレージ アカウント| <お客様のストレージ アカウント>|
   |カスタム コード ストレージ設定のリソース|現在のアカウントからデータ ソースを選択します|
   |カスタム コード ストレージ設定のストレージ アカウント|<お客様のストレージ アカウント>|
   |カスタム コード ストレージ設定のコンテナー|<お客様のストレージ コンテナー>|
   |カスタム コード アセンブリ ソース|クラウドの既存のアセンブリ パッケージ|
   |カスタム コード アセンブリ ソース|UserCustomCode.zip|

## <a name="user-logging"></a>ユーザーのログ記録
ログ記録メカニズムを使用すると、ジョブの実行中にカスタム情報をキャプチャできます。 ログ データを使用して、カスタム コードの正確性をリアルタイムでデバッグまたは評価することができます。

`StreamingContext` クラスを使用すると、`StreamingDiagnostics.WriteError` 関数を使用して診断情報を公開できます。 次のコードは、Azure Stream Analytics によって公開されるインターフェイスを示しています。

```csharp
public abstract class StreamingContext
{
    public abstract StreamingDiagnostics Diagnostics { get; }
}

public abstract class StreamingDiagnostics
{
    public abstract void WriteError(string briefMessage, string detailedMessage);
}
```

`StreamingContext` は、UDF メソッドに入力パラメーターとして渡され、UDF 内で使用してカスタム ログ情報を発行できます。 次の例では、`MyUdfMethod` は、クエリによって提供される**データ**入力と、ランタイム エンジンによって提供される、`StreamingContext` としての**コンテキスト**入力を定義します。 

```csharp
public static long MyUdfMethod(long data, StreamingContext context)
{
    // write log
    context.Diagnostics.WriteError("User Log", "This is a log message");
    
    return data;
}
```

`StreamingContext` 値は、SQL クエリで渡す必要はありません。 入力パラメーターが存在する場合、Azure Stream Analytics は、コンテキスト オブジェクトを自動的に提供します。 次のクエリに示すように、`MyUdfMethod` の使用は変わりません。

```sql
SELECT udf.MyUdfMethod(input.value) as udfValue FROM input
```

ログ メッセージにアクセスするには、[診断ログ](data-errors.md)を使用します。

## <a name="limitations"></a>制限事項
現在、UDF プレビューには次の制限があります。

* .NET Standard UDF は Visual Studio でのみ作成でき、Azure に発行できます。 Azure portal の **[関数]** には、.NET Standard UDF の読み取り専用バージョンが表示されます。 .NET Standard 関数の作成は、Azure portal ではサポートされていません。

* Azure portal で .NET Standard UDF を使用すると、ポータルのクエリ エディターにエラーが表示されます。 

* カスタム コードは Azure Stream Analytics エンジンとコンテキストを共有するため、名前空間/dll_name が Azure Stream Analytics コードと競合するものをカスタム コードで参照することはできません。 たとえば、*Newtonsoft Json* を参照することはできません。

* プロジェクトに含まれるサポート ファイルは、ジョブをクラウドに発行するときに使用されるユーザー カスタム コード zip ファイルにコピーされます。 サブフォルダー内のすべてのファイルは、解凍時に、クラウド内のユーザー カスタムコード フォルダーのルートに直接コピーされます。 この zip は、圧縮解除時に "フラット化" されます。

* ユーザー カスタム コードでは、空のフォルダーはサポートしていません。 プロジェクトのサポート ファイルに空のフォルダーは追加しないでください。

## <a name="next-steps"></a>次のステップ

* [チュートリアル:Azure Stream Analytics ジョブの C# ユーザー定義関数を記述する (プレビュー)](stream-analytics-edge-csharp-udf.md)
* [チュートリアル:Azure Stream Analytics の JavaScript ユーザー定義関数](stream-analytics-javascript-user-defined-functions.md)
* [Visual Studio を使用して Azure Stream Analytics ジョブを表示する](stream-analytics-vs-tools.md)
