---
title: "U-SQL ジョブのデバッグ | Microsoft Docs"
description: "Visual Studio を使用して、U-SQL の失敗した頂点をデバッグする方法について説明します。"
services: data-lake-analytics
documentationcenter: 
author: saveenr
manager: jhubbard
editor: cgronlun
ms.assetid: bcd0b01e-1755-4112-8e8a-a5cabdca4df2
ms.service: data-lake-analytics
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 09/02/2016
ms.author: saveenr
ms.openlocfilehash: 2a77c72d3062272305208934d6406d040266c753
ms.sourcegitcommit: 02e69c4a9d17645633357fe3d46677c2ff22c85a
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/03/2017
---
# <a name="debug-user-defined-c-code-for-failed-u-sql-jobs"></a>失敗した U-SQL ジョブに対するユーザー定義の C# コードをデバッグする

U-SQL は C# を使用した拡張モデルを提供しているため、独自のコードを記述してカスタムの抽出やレジューサなどの機能を追加できます。 詳細については、「[U-SQL プログラミング ガイド](https://docs.microsoft.com/en-us/azure/data-lake-analytics/data-lake-analytics-u-sql-programmability-guide#use-user-defined-functions-udf)」をご覧ください。 実際にはどのコードもデバッグが必要ですが、大規模なデータ システムでは、ログ ファイルのように限られたランタイムのデバッグ情報しか提供されない可能性があります。

Azure Data Lake Tools for Visual Studio が提供する "**失敗した頂点のデバッグ**" と呼ばれる機能を使用すると、失敗したジョブをクラウドからローカル コンピューターに複製して、デバッグできます。 ローカルのクローンは、入力データとユーザー コードを含むクラウド環境全体をキャプチャします。

次のビデオでは、Azure Data Lake Tools for Visual Studio の失敗した頂点のデバッグ機能をデモンストレーションします。

> [!VIDEO https://e0d1.wpc.azureedge.net/80E0D1/OfficeMixProdMediaBlobStorage/asset-d3aeab42-6149-4ecc-b044-aa624901ab32/b0fc0373c8f94f1bb8cd39da1310adb8.mp4?sv=2012-02-12&sr=c&si=a91fad76-cfdd-4513-9668-483de39e739c&sig=K%2FR%2FdnIi9S6P%2FBlB3iLAEV5pYu6OJFBDlQy%2FQtZ7E7M%3D&se=2116-07-19T09:27:30Z&rscd=attachment%3B%20filename%3DDebugyourcustomcodeinUSQLADLA.mp4]
>

> [!NOTE]
> Visual Studio では、[Microsoft Visual C++ 2015 再頒布可能パッケージ Update 3](https://www.microsoft.com/en-us/download/details.aspx?id=53840) と [Windows ユニバーサル C ランタイム](https://www.microsoft.com/download/details.aspx?id=50410)の 2 つの更新プログラムが必要です (インストールが済んでいない場合)。

## <a name="download-failed-vertex-to-local-machine"></a>失敗した頂点をローカル コンピューターにダウンロードする

Azure Data Lake Tools for Visual Studio で失敗したジョブを開くと、詳細なエラー メッセージが記載された警告バーがエラー タブに表示されます。

1. **[ダウンロード]** をクリックして必要なリソースと入力ストリームをすべてダウンロードします。 ダウンロードが完了しない場合は、**[再試行]** をクリックします。

2. ダウンロードが完了したら **[開く]** をクリックし、ローカルのデバッグ環境を生成します。 デバッグのソリューションを備えた新しい Visual Studio インスタンスが自動的に作成されて開きます。

![Azure Data Lake Analytics の U-SQL デバッグ Visual Studio 頂点のダウンロード](./media/data-lake-analytics-debug-u-sql-jobs/data-lake-analytics-download-vertex.png)

ジョブには、コードビハインドのソース ファイルや登録されたアセンブリが含まれる場合があり、これら 2 つの種類では、デバッグのシナリオが異なります。

- [コードビハインドで失敗したジョブをデバッグする](#debug-job-failed-with-code-behind)
- [アセンブリで失敗したジョブをデバッグする](#debug-job-failed-with-assemblies)


## <a name="debug-job-failed-with-code-behind"></a>コードビハインドで失敗したジョブをデバッグする

U-SQL ジョブが失敗し、ジョブにユーザー コード (U-SQL プロジェクトで通常 `Script.usql.cs` という名前) が含まれる場合、このソース コードはデバッグ ソリューションにインポートされます。  そこから Visual Studio のデバッグ ツール (ウォッチ、変数など) を使用して、問題のトラブルシューティングを行うことができます。

> [!NOTE]
> デバッグする前に、[例外設定] ウィンドウ (**Ctrl + Alt + E**) で **[Common Language Runtime Exceptions]\(共通言語ランタイム例外\)** をチェックしてください。

![Azure Data Lake Analytics の U-SQL デバッグ Visual Studio 設定](./media/data-lake-analytics-debug-u-sql-jobs/data-lake-analytics-clr-exception-setting.png)

1. **F5** キーを押してコードビハインド コードを実行します。 例外によって停止されるまで実行されます。

2. `ADLTool_Codebehind.usql.cs` ファイルを開いてブレークポイントを設定してから、**F5** キーを押してステップ バイ ステップでコードをデバッグします。

    ![Azure Data Lake Analytics U-SQL デバッグ例外](./media/data-lake-analytics-debug-u-sql-jobs/data-lake-analytics-debug-exception.png)

## <a name="debug-job-failed-with-assemblies"></a>アセンブリで失敗したジョブをデバッグする

登録されたアセンブリを U-SQL スクリプトで使用すると、ソース コードはシステムで自動的に取得されません。 この場合は、ソリューションにアセンブリのソース コード ファイルを手動で追加します。

### <a name="configure-the-solution"></a>ソリューションの構成

1. **[Solution ’VertexDebug’]\(ソリューション 'VertexDebug'\)(右クリック) > [追加] > [既存のプロジェクト…]** を選択してアセンブリのソース コードを検索し、プロジェクトをデバッグ ソリューションに追加します。

    ![Azure Data Lake Analytics U-SQL デバッグ追加プロジェクト](./media/data-lake-analytics-debug-u-sql-jobs/data-lake-analytics-add-project-to-debug-solution.png)

2. ソリューションで **[LocalVertexHost](右クリック) > [プロパティ]** を選択し、**[作業ディレクトリ]** パスをコピーします。

3. **アセンブリのソース コード プロジェクト (右クリック) > [プロパティ]** で、左側にある **[ビルド]** を選択し、コピーしたパスを **[出力] > [出力パス]** に貼り付けます。

    ![Azure Data Lake Analytics U-SQL デバッグ PDB パス設定](./media/data-lake-analytics-debug-u-sql-jobs/data-lake-analytics-set-pdb-path.png)

4. **Ctrl + Alt + E** キーを押して、[例外設定] ウィンドウで**[Common Language Runtime Exceptions]\(共通言語ランタイム例外\)** をチェックします。

### <a name="start-debug"></a>デバッグの開始

1. **アセンブリのソース コード プロジェクト (右クリック) > [再構築]** を選択して PDB ファイルを `LocalVertexHost` 作業ディレクトリに出力します。

2. **F5** キーを押すと、プロジェクトが、例外によって停止されるまで実行されます。 次の警告メッセージが表示される場合がありますが、このメッセージは無視しても安全性に問題はありません。 デバッグ画面が表示されるまで最大 1 分間かかることがあります。

    ![Azure Data Lake Analytics の U-SQL デバッグ Visual Studio 警告](./media/data-lake-analytics-debug-u-sql-jobs/data-lake-analytics-visual-studio-u-sql-debug-warning.png)

3. ソース コードを開いてブレークポイントを設定してから、**F5** キーを押してステップ バイ ステップでコードをデバッグします。

Visual Studio のデバッグ ツール (ウォッチ、変数など) を使用して、問題をトラブルシューティングすることもできます。

> [!NOTE]
> コードを変更して更新された PDB ファイルを生成するたびに、アセンブリのソース コード プロジェクトをリビルドします。

デバッグが終了し、プロジェクトが正常に完了すると、出力ウィンドウに次のメッセージが表示されます。

```
The Program 'LocalVertexHost.exe' has exited with code 0 (0x0).
```

![Azure Data Lake Analytics の U-SQL デバッグ成功](./media/data-lake-analytics-debug-u-sql-jobs/data-lake-analytics-debug-succeed.png)

## <a name="resubmit-the-job"></a>ジョブを再送信する

デバッグが完了したら、失敗したジョブを再送信します。

1. コードビハインド ソリューションのジョブの場合は、コードビハインドのソース ファイル (通常は `Script.usql.cs`) に C# コードをコピーします。
2. アセンブリのジョブの場合は、次の手順で更新された .dll アセンブリを ADLA データベースに登録します。
    1. サーバー エクスプローラーまたは Cloud Explorer から、**[ADLA アカウント] > [データベース]** ノードを展開します。
    2. 右クリック**アセンブリ**ADLA データベースと新しい .dll アセンブリを登録および: ![Azure データ Lake Analytics U-SQL デバッグ アセンブリを登録します。](./media/data-lake-analytics-debug-u-sql-jobs/data-lake-analytics-register-assembly.png)
3. ジョブを再送信します。

## <a name="next-steps"></a>次のステップ

- [U-SQL プログラミング ガイド](data-lake-analytics-u-sql-programmability-guide.md)
- [Azure Data Lake Analytics ジョブの U-SQL ユーザー定義演算子の開発](data-lake-analytics-u-sql-develop-user-defined-operators.md)
- [チュートリアル: Data Lake Tools for Visual Studio を使用する U-SQL スクリプトの開発](data-lake-analytics-data-lake-tools-get-started.md)
