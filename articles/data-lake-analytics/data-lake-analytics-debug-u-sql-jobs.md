---
title: Azure Data Lake U-SQL ジョブの C# コードのデバッグ
description: この記事では、Azure Data Lake Tools for Visual Studio を使用して、U-SQL の失敗した頂点をデバッグする方法について説明します。
ms.service: data-lake-analytics
ms.reviewer: jasonh
ms.topic: how-to
ms.date: 11/30/2017
ms.openlocfilehash: db1d57e3904087bc5cb3711b23cfe6bcf18c3455
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "92218019"
---
# <a name="debug-user-defined-c-code-for-failed-u-sql-jobs"></a>失敗した U-SQL ジョブに対するユーザー定義の C# コードをデバッグする

U-SQL は C# を使用した機能拡張モデルを提供しています。 U-SQL スクリプトでは、C# 関数を簡単に呼び出して、SQL のような宣言型言語がサポートしていない分析関数を実行できます。 U-SQL 機能拡張の詳細については、「[U-SQL プログラミング ガイド](./data-lake-analytics-u-sql-programmability-guide.md#use-user-defined-functions-udf)」を参照してください。 

実際にはすべてのコードにデバッグが必要ですが、限られたログ ファイルを使用して、クラウド上にカスタム コードを持つ分散ジョブをデバッグするのは困難です。 [Azure Data Lake Tools for Visual Studio](https://aka.ms/adltoolsvs) には **失敗した頂点のデバッグ** と呼ばれる機能が用意されており、カスタム コードで発生したエラーをより簡単にデバッグするのに役立ちます。 U-SQL ジョブが失敗すると、サービスではエラーの状態が維持されます。このツールは、クラウドのエラー環境をローカル マシンにダウンロードしてデバッグするうえで役立ちます。 ローカルのダウンロードには、入力データとユーザー コードを含むクラウド環境全体がキャプチャされています。

次のビデオでは、Azure Data Lake Tools for Visual Studio の失敗した頂点のデバッグ機能をデモンストレーションします。

> [!VIDEO https://www.youtube.com/embed/3enkNvprfm4]
>

> [!IMPORTANT]
> Visual Studio でこの機能を使用するには、[Microsoft Visual C++ 2015 再頒布可能パッケージ Update 3](https://www.microsoft.com/en-us/download/details.aspx?id=53840) と [Windows ユニバーサル C ランタイム](https://www.microsoft.com/download/details.aspx?id=50410)の 2 つの更新プログラムが必要です。
>

## <a name="download-failed-vertex-to-local-machine"></a>失敗した頂点をローカル コンピューターにダウンロードする

Azure Data Lake Tools for Visual Studio で失敗したジョブを開くと、詳細なエラー メッセージが記載された警告バーがエラー タブに表示されます。

1. **[ダウンロード]** をクリックして必要なリソースと入力ストリームをすべてダウンロードします。 ダウンロードが完了しない場合は、**[再試行]** をクリックします。

2. ダウンロードが完了したら **[開く]** をクリックし、ローカルのデバッグ環境を生成します。 新しいデバッグ ソリューションが開きます。Visual Studio で既存のソリューションを開いている場合は、デバッグする前に必ず保存してから閉じてください。

![Azure Data Lake Analytics の U-SQL デバッグ Visual Studio 頂点のダウンロード](./media/data-lake-analytics-debug-u-sql-jobs/data-lake-analytics-download-vertex.png)

## <a name="configure-the-debugging-environment"></a>デバッグ環境を構成する

> [!NOTE]
> デバッグする前に、[例外設定] ウィンドウ (**Ctrl + Alt + E**) で **[Common Language Runtime Exceptions]\(共通言語ランタイム例外\)** をチェックしてください。

![Azure Data Lake Analytics の U-SQL デバッグ Visual Studio 設定](./media/data-lake-analytics-debug-u-sql-jobs/data-lake-analytics-clr-exception-setting.png)

起動した新しい Visual Studio インスタンスでは、ユーザー定義の C# ソース コードが見つかる場合もあれば、見つからない場合もあります。

1. [ソリューションでソース コードを見つけることができる](#source-code-is-included-in-debugging-solution)

2. [ソリューションでソース コードを見つけることができない](#source-code-is-not-included-in-debugging-solution)

### <a name="source-code-is-included-in-debugging-solution"></a>ソース コードがデバッグ ソリューションに含まれている

C# ソース コードがキャプチャされるケースは 2 つあります。

1. ユーザー コードは、分離コード ファイルで定義されています (通常、U-SQL プロジェクトでは `Script.usql.cs` という名前です)。

2. ユーザー コードは、U-SQL アプリケーションの C# クラス ライブラリ プロジェクトで定義され、アセンブリとして **デバッグ情報** に登録されます。

ソース コードがソリューションにインポートされている場合、Visual Studio のデバッグ ツール (ウォッチ、変数など) を使用して、問題のトラブルシューティングを行うことができます。

1. **F5** キーを押してデバッグを開始します。 コードは、例外によって停止されるまで実行されます。

2. ソース コード ファイルを開いて、ブレークポイントを設定してから、**F5** キーを押して、ステップ バイ ステップでコードをデバッグします。

    ![Azure Data Lake Analytics U-SQL デバッグ例外](./media/data-lake-analytics-debug-u-sql-jobs/data-lake-analytics-debug-exception.png)

### <a name="source-code-is-not-included-in-debugging-solution"></a>ソース コードがデバッグ ソリューションに含まれていない

ユーザー コードが分離コード ファイルに含まれない場合、またはアセンブリを **デバッグ情報** に登録しなかった場合は、ソース コードが、デバッグ ソリューションに自動的に追加されることはありません。 この場合は、ソース コードを追加する手順が別に必要になります。

1. **[ソリューション 'VertexDebug'] \(右クリック) > [追加] > [既存のプロジェクト]** の順に選択し、アセンブリのソース コードを検索し、プロジェクトをデバッグ ソリューションに追加します。

    ![Azure Data Lake Analytics U-SQL デバッグ追加プロジェクト](./media/data-lake-analytics-debug-u-sql-jobs/data-lake-analytics-add-project-to-debug-solution.png)

2. **FailedVertexDebugHost** プロジェクトのプロジェクト フォルダー パスを取得します。 

3. **追加されたアセンブリのソース コード プロジェクト (右クリック) > [プロパティ]** で、左側にある **[ビルド]** タブを選択し、コピーしたパス (末尾は \bin\debug) を **[出力] > [出力パス]** に貼り付けます。 最終的な出力パスは次のようになります。`<DataLakeTemp path>\fd91dd21-776e-4729-a78b-81ad85a4fba6\loiu0t1y.mfo\FailedVertexDebug\FailedVertexDebugHost\bin\Debug\`

    ![Azure Data Lake Analytics U-SQL デバッグ PDB パス設定](./media/data-lake-analytics-debug-u-sql-jobs/data-lake-analytics-set-pdb-path.png)

この設定の後、**F5** キーとブレークポイントを使用してデバッグを開始します。 Visual Studio のデバッグ ツール (ウォッチ、変数など) を使用して、問題をトラブルシューティングすることもできます。

> [!NOTE]
> コードを変更して更新された PDB ファイルを生成するたびに、アセンブリのソース コード プロジェクトをリビルドします。

## <a name="resubmit-the-job"></a>ジョブを再送信する

デバッグが終了し、プロジェクトが正常に完了すると、出力ウィンドウに次のメッセージが表示されます。

`The Program 'LocalVertexHost.exe' has exited with code 0 (0x0).`

![Azure Data Lake Analytics の U-SQL デバッグ成功](./media/data-lake-analytics-debug-u-sql-jobs/data-lake-analytics-debug-succeed.png)

失敗したジョブを再送信するには:

1. 分離コード ソリューションのジョブの場合は、C# コードを、分離コードのソース ファイル (通常は `Script.usql.cs`) に コピーします。

2. アセンブリのジョブの場合は、デバッグ ソリューションで、アセンブリ ソース コード プロジェクトを右クリックし、更新された .dll アセンブリを Azure Data Lake カタログに登録します。

3. U-SQL ジョブを再送信します。

## <a name="next-steps"></a>次のステップ

- [U-SQL プログラミング ガイド](data-lake-analytics-u-sql-programmability-guide.md)
- [Azure Data Lake Analytics ジョブの U-SQL ユーザー定義演算子の開発](data-lake-analytics-u-sql-develop-user-defined-operators.md)
- [ローカル実行と Azure Data Lake U-SQL SDK を使用した U-SQL ジョブのテストおよびデバッグ](data-lake-analytics-data-lake-tools-local-run.md)
- [異常な定期的ジョブをトラブルシューティングする方法](data-lake-analytics-data-lake-tools-debug-recurring-job.md)