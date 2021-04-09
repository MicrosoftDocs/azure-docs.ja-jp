---
title: U-SQL ジョブをローカルに実行する - Azure Data Lake U-SQL SDK
description: U-SQL ジョブを、ローカル ワークステーションでコマンド ラインとプログラミング インターフェイスを使用して実行およびテストする方法について説明します。
ms.service: data-lake-analytics
author: yanacai
ms.reviewer: jasonh
ms.topic: how-to
ms.date: 03/01/2017
ms.openlocfilehash: 8fb60e62a63bfc4562f19d483dc84c99c37676b0
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "92215537"
---
# <a name="run-and-test-u-sql-with-azure-data-lake-u-sql-sdk"></a>Azure Data Lake U-SQL SDK を使用して U-SQL の実行およびテストする

U-SQL スクリプトを開発するとき、通常は、クラウドに送信する前に、U-SQL スクリプトをローカルで実行してテストします。 このシナリオのために、Azure Data Lake には、Azure Data Lake U-SQL SDK と呼ばれる Nuget パッケージが用意されています。このパッケージを使用して、U-SQL の実行のスケーリングとテストを簡単に行うことができます。 この U-SQL テストを CI (継続的インテグレーション) システムと統合して、コンパイルとテストを自動化することもできます。

GUI ツールを使用した U-SQL スクリプトの手動でのローカル実行とデバッグに関心がある場合は、Azure Data Lake Tools for Visual Studio を使用できます。 詳細については、[こちら](data-lake-analytics-data-lake-tools-local-run.md)を参照してください。

## <a name="install-azure-data-lake-u-sql-sdk"></a>Azure Data Lake U-SQL SDK をインストールする

Azure Data Lake U-SQL SDK は、[こちら](https://www.nuget.org/packages/Microsoft.Azure.DataLake.USQL.SDK/)で Nuget.org から入手できます。使用する前に、次の依存関係が用意されていることを確認する必要があります。

### <a name="dependencies"></a>依存関係

Data Lake U-SQL SDK には、以下の依存関係が必要です。

- [Microsoft .NET Framework 4.6 以降](https://www.microsoft.com/download/details.aspx?id=17851)。
- Microsoft Visual C++ 14 および Windows SDK 10.0.10240.0 以降 (この記事では CppSDK と呼んでいます)。 CppSDK を取得する方法は 2 つあります。

  - [Visual Studio Community エディション](https://developer.microsoft.com/downloads/vs-thankyou)をインストールします。 たとえば C:\Program Files (x86)\Windows Kits\10 のように、Program Files フォルダー内に \Windows Kits\10 フォルダーが作成されます。 また、\Windows Kits\10\Lib でも Windows 10 SDK バージョンを確認できます。 このフォルダーが表示されない場合は、Visual Studio を再インストールし、インストール中に必ず Windows 10 SDK を選択してください。 これを Visual Studio と共にインストールすると、U-SQL ローカル コンパイラによって自動的に検出されます。

    ![Data Lake Tools for Visual Studio ローカル実行の Windows 10 SDK](./media/data-lake-analytics-data-lake-tools-local-run/data-lake-tools-for-visual-studio-local-run-windows-10-sdk.png)

  - [Data Lake Tools for Visual Studio](https://aka.ms/adltoolsvs) をインストールします。 事前にパッケージ化された Visual C++ と Windows SDK ファイルは、`C:\Program Files (x86)\Microsoft Visual Studio 14.0\Common7\IDE\Extensions\Microsoft\ADL Tools\X.X.XXXX.X\CppSDK.` にあります

    この場合、U-SQL ローカル コンパイラでは依存関係が自動検出されないため、 CppSDK パスを指定する必要があります。 このファイルは別の場所にコピーすることも、そのまま使用することもできます。

## <a name="understand-basic-concepts"></a>基本的な概念を理解する

### <a name="data-root"></a>データ ルート

データルート フォルダーは、ローカル コンピューティング アカウント用の "ローカル ストア" で、 Data Lake Analytics アカウントの Azure Data Lake Store アカウントに相当します。 別のデータルート フォルダーへの切り換えは、別のストア アカウントへの切り換えと同じです。 さまざまなデータルート フォルダーで通常共有されるデータにアクセスする必要がある場合は、スクリプト内で絶対パスを使用す必要があります。 または、このデータルート フォルダー内に、共有データを指すファイル システムのシンボリック リンクを作成します (たとえば、NTFS で **mklink** を作成します)。

データルート フォルダーは、以下の目的で使用されます。

- データベース、テーブル、テーブル値関数 (TVF)、アセンブリなどのローカル メタデータを格納する。
- U-SQL で相対パスとして定義されている入力および出力パスを検索する。 相対パスを使用することにより、U-SQL プロジェクトを Azure にデプロイしやすくなります。

### <a name="file-path-in-u-sql"></a>U-SQL のファイル パス

U-SQL スクリプトでは、相対パスとローカル絶対パスの両方を使用できます。 相対パスは、指定したデータルート フォルダー パスを基準としています。 サーバー側と互換性のあるスクリプトにするため、パスの区切り文字として "/" を使用することをお勧めします。 以下に相対パスとそれに相当する絶対パスの例をいくつか示します。 次の例では、"C:\LocalRunDataRoot" がデータルート フォルダーです。

|相対パス|絶対パス|
|-------------|-------------|
|/abc/def/input.csv |C:\LocalRunDataRoot\abc\def\input.csv|
|abc/def/input.csv  |C:\LocalRunDataRoot\abc\def\input.csv|
|D:/abc/def/input.csv |D:\abc\def\input.csv|

### <a name="working-directory"></a>作業ディレクトリ

U-SQL スクリプトをローカルで実行すると、コンパイル時に、現在実行中のディレクトリの下に作業ディレクトリが作成されます。 この作業ディレクトリには、コンパイル出力だけでなく、ローカル実行に必要なランタイム ファイルがシャドウ コピーされます。 作業ディレクトリのルート フォルダーは "ScopeWorkDir" と呼ばれ、作業ディレクトリには次のファイルが含まれます。

|ディレクトリ/ファイル|ディレクトリ/ファイル|ディレクトリ/ファイル|定義|説明|
|--------------|--------------|--------------|----------|-----------|
|C6A101DDCB470506| | |ランタイム バージョンのハッシュ文字列|ローカル実行に必要なランタイム ファイルのシャドウ コピー|
| |Script_66AE4909AA0ED06C| |スクリプト名 + スクリプト パスのハッシュ文字列|コンパイル出力と実行ステップのログ記録|
| | |\_script\_.abr|コンパイラの出力|代数ファイル|
| | |\_ScopeCodeGen\_.*|コンパイラの出力|生成されたマネージド コード|
| | |\_ScopeCodeGenEngine\_.*|コンパイラの出力|生成されたネイティブ コード|
| | |参照されたアセンブリ|アセンブリ参照|参照されたアセンブリ ファイル|
| | |deployed_resources|リソースのデプロイ|リソースのデプロイ ファイル|
| | |xxxxxxxx.xxx[1..n]\_\*.\*|実行ログ|実行ステップのログ|

## <a name="use-the-sdk-from-the-command-line"></a>コマンド ラインからの SDK の使用

### <a name="command-line-interface-of-the-helper-application"></a>ヘルパー アプリケーションのコマンド ライン インターフェイス

SDK directory\build\runtime の LocalRunHelper.exe はコマンド ライン ヘルパー アプリケーションであり、よく使用されるローカル実行機能の大部分へのインターフェイスを提供します。 コマンドと引数スイッチの両方で大文字と小文字が区別される点に注意してください。 これを呼び出すには、次のようにします。

```console
LocalRunHelper.exe <command> <Required-Command-Arguments> [Optional-Command-Arguments]
```

引数を指定せずに、または **help** スイッチを使用して LocalRunHelper.exe を実行すると、次のようなヘルプ情報が表示されます。

```console
> LocalRunHelper.exe help
    Command 'help' :  Show usage information
    Command 'compile' :  Compile the script
    Required Arguments :
        -Script param
                Script File Path
    Optional Arguments :
        -Shallow [default value 'False']
                Shallow compile
```

ヘルプ情報には、以下の項目が含まれます。

- **Command**: コマンド名が表示されます。  
- **Required Argument**: 指定する必要のある引数が一覧表示されます。  
- **Optional Argument**: 既定値を持つ省略可能な引数が一覧表示されます。  省略可能なブール引数はパラメーターを持たず、これらの引数が指定されている場合は既定値の反対の値を意味します。

### <a name="return-value-and-logging"></a>戻り値とログ記録

ヘルパー アプリケーションは、**0** (成功) または **-1** (失敗) を返します。 既定では、すべてのメッセージが現在のコンソールに送信されます。 ただし、ほとんどのコマンドでは、出力をログ ファイルにリダイレクトする省略可能な **-MessageOut path_to_log_file** 引数がサポートされています。

### <a name="environment-variable-configuring"></a>構成する環境変数

U-SQL のローカル実行では、依存関係にある特定の CppSDK パスだけではなく、ローカル ストレージ アカウントとしての特定のデータ ルートも必要です。 どちらも、コマンド ラインで引数を設定するか、環境変数を設定することで設定できます。

- **SCOPE_CPP_SDK** 環境変数を設定します。

  Data Lake Tools for Visual Studio のインストールによって Microsoft Visual C++ と Windows SDK がインストールされている場合は、次のフォルダーがあることを確認します。

    `C:\Program Files (x86)\Microsoft Visual Studio 14.0\Common7\IDE\Extensions\Microsoft\Microsoft Azure Data Lake Tools for Visual Studio 2015\X.X.XXXX.X\CppSDK`

  このディレクトリを指す **SCOPE_CPP_SDK** という新しい環境変数を定義します。 または、フォルダーを他の場所にコピーし、**SCOPE_CPP_SDK** をそのフォルダーとして指定します。

  コマンド ラインを使用している場合は、環境変数を設定するだけでなく、 **-CppSDK** 引数を指定できます。 この引数により、既定の CppSDK 環境変数が上書きされます。

- **LOCALRUN_DATAROOT** 環境変数を設定します。

  データ ルートを指す **LOCALRUN_DATAROOT** という新しい環境変数を定義します。

  コマンド ラインを使用している場合は、環境変数を設定するだけでなく、データルート パスで **-DataRoot** 引数を指定できます。 この引数により、既定のデータルート環境変数が上書きされます。 この引数を、実行しているすべてのコマンド ラインに追加して、すべての操作に対する既定のデータルート環境変数を上書きすることができます。

### <a name="sdk-command-line-usage-samples"></a>SDK コマンド ラインの使用例

#### <a name="compile-and-run"></a>コンパイルと実行

**run** コマンドは、スクリプトをコンパイルしてからその結果を実行するために使用します。 コマンド ラインの引数は、**compile** と **run** の引数を組み合わせたものです。

```console
LocalRunHelper run -Script path_to_usql_script.usql [optional_arguments]
```

**run** の省略可能な引数を次に示します。

|引数|既定値|説明|
|--------|-------------|-----------|
|-分離コード|False|スクリプトには .cs 分離コードがあります|
|-CppSDK| |CppSDK ディレクトリ|
|-DataRoot| DataRoot 環境変数|ローカル実行用の DataRoot。既定値は "LOCALRUN_DATAROOT" 環境変数です|
|-MessageOut| |ファイルにダンプされるコンソール上のメッセージ|
|-Parallel|1|指定された並列処理でプランを実行します|
|-References| |分離コードの追加の参照アセンブリまたはデータ ファイルへのパスから成る、";" で区切られたリスト|
|-UdoRedirect|False|Udo アセンブリ リダイレクトの構成を生成します|
|-UseDatabase|master|分離コードの一時アセンブリ登録に使用するデータベース|
|-Verbose|False|ランタイムからの詳細な出力を表示します|
|-WorkDir|現在のディレクトリ|コンパイラの使用と出力用のディレクトリ|
|-RunScopeCEP|0|使用する ScopeCEP モード|
|-ScopeCEPTempPath|temp|データをストリーミングするために使用する一時パス|
|-OptFlags| |オプティマイザー フラグのコンマ区切りリスト|

次に例を示します。

`LocalRunHelper run -Script d:\test\test1.usql -WorkDir d:\test\bin -CodeBehind -References "d:\asm\ref1.dll;d:\asm\ref2.dll" -UseDatabase testDB –Parallel 5 -Verbose`

**compile** と **execute** を組み合わせた方法に加え、コンパイルと、コンパイル済みの実行可能ファイルの実行を個別に行う方法もあります。

#### <a name="compile-a-u-sql-script"></a>U-SQL スクリプトのコンパイル

**compile** コマンドは、U-SQL スクリプトを実行可能ファイルにコンパイルするために使用します。

```console
LocalRunHelper compile -Script path_to_usql_script.usql [optional_arguments]
```

**compile** の省略可能な引数を次に示します。

|引数|説明|
|--------|-----------|
| -CodeBehind (既定値 "False")|スクリプトには .cs 分離コードがあります|
| -CppSDK (既定値 "")|CppSDK ディレクトリ|
| -DataRoot [既定値 "DataRoot 環境変数"]|ローカル実行用の DataRoot。既定値は "LOCALRUN_DATAROOT" 環境変数です|
| -MessageOut (既定値 "")|ファイルにダンプされるコンソール上のメッセージ|
| -References (既定値 "")|分離コードの追加の参照アセンブリまたはデータ ファイルへのパスから成る、";" で区切られたリスト|
| -Shallow (既定値 "False")|簡易コンパイル|
| -UdoRedirect (既定値 "False")|Udo アセンブリ リダイレクトの構成を生成します|
| -UseDatabase (既定値 "master")|分離コードの一時アセンブリ登録に使用するデータベース|
| -WorkDir [既定値 "現在のディレクトリ"]|コンパイラの使用と出力用のディレクトリ|
| -RunScopeCEP [既定値 "0"]|使用する ScopeCEP モード|
| -ScopeCEPTempPath [既定値 "temp"]|データをストリーミングするために使用する一時パス|
| -OptFlags [既定値 '']|オプティマイザー フラグのコンマ区切りリスト|

使用例をいくつか以下に示します。

U-SQL スクリプトのコンパイル:

```console
LocalRunHelper compile -Script d:\test\test1.usql
```

U-SQL スクリプトをコンパイルし、データルート フォルダーを設定します。 これにより環境変数の設定が上書きされることに注意してください。

```console
LocalRunHelper compile -Script d:\test\test1.usql –DataRoot c:\DataRoot
```

U-SQL スクリプトをコンパイルし、作業ディレクトリ、参照アセンブリ、およびデータベースを設定します。

```console
LocalRunHelper compile -Script d:\test\test1.usql -WorkDir d:\test\bin -References "d:\asm\ref1.dll;d:\asm\ref2.dll" -UseDatabase testDB
```

#### <a name="execute-compiled-results"></a>コンパイル結果の実行

**execute** コマンドは、コンパイル結果を実行するために使用されます。

```console
LocalRunHelper execute -Algebra path_to_compiled_algebra_file [optional_arguments]
```

**execute** の省略可能な引数を次に示します。

|引数|既定値|説明|
|--------|-------------|-----------|
|-DataRoot | '' |メタデータ実行のデータ ルート。 既定値は **LOCALRUN_DATAROOT** 環境変数です。|
|-MessageOut | '' |ファイルにダンプされるコンソール上のメッセージ。|
|-Parallel | '1' |生成されたローカル実行ステップが、指定された並列処理レベルで実行されることを示します。|
|-Verbose | "False" |ランタイムから詳細な出力が表示されることを示します。|

使用例を次に示します。

```console
LocalRunHelper execute -Algebra d:\test\workdir\C6A101DDCB470506\Script_66AE4909AA0ED06C\__script__.abr –DataRoot c:\DataRoot –Parallel 5
```

## <a name="use-the-sdk-with-programming-interfaces"></a>プログラミング インターフェイスでの SDK の使用

プログラミング インターフェイスは、すべて LocalRunHelper.exe に配置されています。 このインターフェイスを使って、U-SQL SDK および C# テスト フレームワークの機能を統合し、U-SQL スクリプトのローカル テストを拡張できます。 この記事では、標準的な C# 単体テスト プロジェクトを使用して、これらのインターフェイスで U-SQL スクリプトをテストする方法を示します。

### <a name="step-1-create-c-unit-test-project-and-configuration"></a>手順 1:C# 単体テスト プロジェクトと構成を作成する

- [ファイル]、[新規]、[プロジェクト]、[Visual C#]、[テスト]、[単体テスト プロジェクト] の順に選択して、C# 単体テスト プロジェクトを作成します。
- プロジェクトの参照として LocalRunHelper.exe を追加します。 LocalRunHelper.exe は、Nuget パッケージの \build\runtime\LocalRunHelper.exe に配置されています。

   ![Azure Data Lake U-SQL SDK での参照の追加](./media/data-lake-analytics-u-sql-sdk/data-lake-analytics-u-sql-sdk-add-reference.png)

- U-SQL SDK は **x64 環境のみ** をサポートするため、ビルドのプラットフォーム ターゲットが [x64] に設定されていることを確認します。 これは、[プロジェクトのプロパティ]、[ビルド]、[プラットフォーム ターゲット] の順に選択することで設定できます。

   ![Azure Data Lake U-SQL SDK での x64 プロジェクトの構成](./media/data-lake-analytics-u-sql-sdk/data-lake-analytics-u-sql-sdk-configure-x64.png)

- テスト環境が x64 に設定されていることを確認します。 Visual Studio で、[テスト]、[テストの設定]、[既定のプロセッサ アーキテクチャ]、[x64] の順に選択することで設定できます。

   ![Azure Data Lake U-SQL SDK での x64 テスト環境の構成](./media/data-lake-analytics-u-sql-sdk/data-lake-analytics-u-sql-sdk-configure-test-x64.png)

- NugetPackage\build\runtime\ のすべての依存関係ファイルが、作業ディレクトリ (通常は ProjectFolder\bin\x64\Debug の下のディレクトリ) にコピーされていることを確認します。

### <a name="step-2-create-u-sql-script-test-case"></a>手順 2:U-SQL スクリプトのテスト ケースを作成する

次に示すのは、U-SQL スクリプト テストのサンプル コードです。 テストするには、スクリプト、入力ファイル、および予期される出力ファイルを準備する必要があります。

```usql
using System;
using Microsoft.VisualStudio.TestTools.UnitTesting;
using System.IO;
using System.Text;
using System.Security.Cryptography;
using Microsoft.Analytics.LocalRun;
namespace UnitTestProject1
{
    [TestClass]
    public class USQLUnitTest
    {
        [TestMethod]
        public void TestUSQLScript()
        {
            //Specify the local run message output path
            StreamWriter MessageOutput = new StreamWriter("../../../log.txt");
            LocalRunHelper localrun = new LocalRunHelper(MessageOutput);
            //Configure the DateRoot path, Script Path and CPPSDK path
            localrun.DataRoot = "../../../";
            localrun.ScriptPath = "../../../Script/Script.usql";
            localrun.CppSdkDir = "../../../CppSDK";
            //Run U-SQL script
            localrun.DoRun();
            //Script output
            string Result = Path.Combine(localrun.DataRoot, "Output/result.csv");
            //Expected script output
            string ExpectedResult = "../../../ExpectedOutput/result.csv";
            Test.Helpers.FileAssert.AreEqual(Result, ExpectedResult);
            //Don't forget to close MessageOutput to get logs into file
            MessageOutput.Close();
        }
    }
}
namespace Test.Helpers
{
    public static class FileAssert
    {
        static string GetFileHash(string filename)
        {
            Assert.IsTrue(File.Exists(filename));
            using (var hash = new SHA1Managed())
            {
                var clearBytes = File.ReadAllBytes(filename);
                var hashedBytes = hash.ComputeHash(clearBytes);
                return ConvertBytesToHex(hashedBytes);
            }
        }
        static string ConvertBytesToHex(byte[] bytes)
        {
            var sb = new StringBuilder();
            for (var i = 0; i < bytes.Length; i++)
            {
                sb.Append(bytes[i].ToString("x"));
            }
            return sb.ToString();
        }
        public static void AreEqual(string filename1, string filename2)
        {
            string hash1 = GetFileHash(filename1);
            string hash2 = GetFileHash(filename2);
            Assert.AreEqual(hash1, hash2);
        }
    }
}
```

### <a name="programming-interfaces-in-localrunhelperexe"></a>LocalRunHelper.exe 内のプログラミング インターフェイス

LocalRunHelper.exe は、U-SQL のローカル コンパイルや実行などのプログラミング インターフェイスを提供します。これらのインターフェイスを次に示します。

### <a name="constructor"></a>Constructor

public LocalRunHelper([System.IO.TextWriter messageOutput = null])

|パラメーター|Type|説明|
|---------|----|-----------|
|messageOutput|System.IO.TextWriter|出力メッセージ用。コンソールで使用するには null に設定します|

### <a name="properties"></a>Properties

|プロパティ|Type|説明|
|--------|----|-----------|
|AlgebraPath|string|代数ファイルへのパス (代数ファイルは、コンパイル結果の 1 つです)|
|CodeBehindReferences|string|スクリプトに分離コード参照がある場合は、パスを ';' で区切って指定します|
|CppSdkDir|string|CppSDK ディレクトリ|
|CurrentDir|string|現在のディレクトリ|
|DataRoot|string|データ ルート パス|
|DebuggerMailPath|string|デバッガーのメール スロットのパス|
|GenerateUdoRedirect|[bool]|アセンブリ読み込みリダイレクトのオーバーライド構成を生成するかどうかを示します|
|HasCodeBehind|[bool]|スクリプトに分離コードがあるかどうか|
|InputDir|string|入力データ用のディレクトリ|
|MessagePath|string|メッセージ ダンプ ファイルのパス|
|OutputDir|string|出力データ用のディレクトリ|
|Parallelism|INT|代数を実行する並列処理|
|ParentPid|INT|サービスの監視が終了する親の PID。無視する場合は 0 または負数を設定します|
|ResultPath|string|結果ダンプ ファイルのパス|
|RuntimeDir|string|ランタイム ディレクトリ|
|ScriptPath|string|スクリプトの検出場所|
|Shallow|[bool]|簡易コンパイルかどうか|
|TempDir|string|Temp ディレクトリ|
|UseDataBase|string|分離コードの一時アセンブリ登録に使用するデータベース。既定では master|
|WorkDir|string|任意の作業ディレクトリ|

### <a name="method"></a>Method

|Method|説明|戻り値|パラメーター|
|------|-----------|------|---------|
|public bool DoCompile()|U-SQL スクリプトをコンパイルします|成功時に True| |
|public bool DoExec()|コンパイル結果を実行します|成功時に True| |
|public bool DoRun()|U-SQL スクリプトを実行します (コンパイル + 実行)|成功時に True| |
|public bool IsValidRuntimeDir(string path)|指定されたパスが有効なランタイム パスかどうかを確認します|有効な場合は True|ランタイム ディレクトリのパス|

## <a name="faq-about-common-issue"></a>一般的な問題に関する FAQ

### <a name="error-1"></a>エラー 1

E_CSC_SYSTEM_INTERNAL:内部エラーです。 ファイルを読み込めないか、'ScopeEngineManaged.dll' またはいずれかの依存関係をアセンブルできません。 指定されたモジュールが見つかりません。

次のことを確認してください。

- x64 環境であることを確認します。 ビルドのターゲット プラットフォームとテスト環境は x64 である必要があります。「**手順 1:C# 単体テスト プロジェクトと構成を作成する**」を参照してください。
- NugetPackage\build\runtime\ 内のすべての依存関係ファイルがプロジェクトの作業ディレクトリにコピーされていることを確認します。

## <a name="next-steps"></a>次のステップ

- U-SQL の詳細については、「 [Azure Data Lake Analytics U-SQL 言語の使用](data-lake-analytics-u-sql-get-started.md)」を参照してください。
- 診断情報のログについては、「[Azure Data Lake Analytics の診断ログへのアクセス](data-lake-analytics-diagnostic-logs.md)」を参照してください。
- より複雑なクエリを表示する場合は、[Azure Data Lake Analytics を使用した Web サイトのログの分析](data-lake-analytics-analyze-weblogs.md)に関するページをご覧ください。
- ジョブの詳細を表示する方法については、「[Azure Data Lake Analytics ジョブに対するジョブ ブラウザーとジョブ ビューの使用](data-lake-analytics-data-lake-tools-view-jobs.md)」を参照してください。
- 頂点実行ビューの使用方法については、「[Data Lake Tools for Visual Studio での頂点実行ビューの使用](data-lake-analytics-data-lake-tools-use-vertex-execution-view.md)」を参照してください。
