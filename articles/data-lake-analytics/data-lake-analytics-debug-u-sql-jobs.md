---
title: "U-SQL ジョブのデバッグ | Microsoft Docs"
description: "Visual Studio を使用して、U-SQL の失敗した頂点をデバッグする方法について説明します。 "
services: data-lake-analytics
documentationcenter: 
author: yanancai
manager: jhubbard
editor: cgronlun
ms.assetid: bcd0b01e-1755-4112-8e8a-a5cabdca4df2
ms.service: data-lake-analytics
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 09/02/2016
ms.author: yanacai
translationtype: Human Translation
ms.sourcegitcommit: a5bb452582f05981a17c2514e0e40db0571bf61d
ms.openlocfilehash: f9b485bfbfbeb8a95ae1908ef6b1733b9cc6999a


---
# <a name="debug-user-defined-c-code-for-failed-u-sql-jobs"></a>失敗した U-SQL ジョブに対するユーザー定義の C# コードをデバッグする

Azure Data Lake Tools for Visual Studio を使用して、ユーザー定義コードのバグで失敗した U-SQL ジョブをデバッグする方法について説明します。

## <a name="background"></a>バックグラウンド

U-SQL は C# を使用した機能拡張モデルを提供しており、ユーザーは、より多くの機能拡張を実現するために、ユーザー定義の C# コード (ユーザー定義の抽出やレジューサなど) を書き込むことができます (詳細については、[U-SQL ユーザー定義コード](https://docs.microsoft.com/en-us/azure/data-lake-analytics/data-lake-analytics-u-sql-programmability-guide#user-defined-functions---udf)に関するセクションを参照してください)。 しかし、ミスを犯さずにコードを書くことができる開発者はいません。また、多くのシステムではログのような限られたランタイムのデバッグ情報しか提供されないため、ビッグ データ システムでのデバッグは困難です。 

ADL Tools for Visual Studio では、**失敗した頂点のデバッグ**と呼ばれる機能を提供しています。この機能を使用して、(中間入力データやユーザー コードなど) 失敗した環境をクラウドからローカル コンピューターに簡単に複製し、クラウドと同じランタイムと入力データで失敗したジョブをデバッグできます。

次のビデオでは、ADL Tools for Visual Studio での**失敗した頂点のデバッグ**の方法をデモンストレーションします。

> [!VIDEO https://e0d1.wpc.azureedge.net/80E0D1/OfficeMixProdMediaBlobStorage/asset-d3aeab42-6149-4ecc-b044-aa624901ab32/b0fc0373c8f94f1bb8cd39da1310adb8.mp4?sv=2012-02-12&sr=c&si=a91fad76-cfdd-4513-9668-483de39e739c&sig=K%2FR%2FdnIi9S6P%2FBlB3iLAEV5pYu6OJFBDlQy%2FQtZ7E7M%3D&se=2116-07-19T09:27:30Z&rscd=attachment%3B%20filename%3DDebugyourcustomcodeinUSQLADLA.mp4]
>
>

> [!NOTE]
> 2 つの Windows アップグレード ([Microsoft Visual C++ 2015 再頒布可能 Update 2](https://www.microsoft.com/download/details.aspx?id=51682) と [Windows Universal C Runtime](https://www.microsoft.com/download/details.aspx?id=50410&wa=wsignin1.0)) を適用していない場合は、Visual Studio がハングまたはクラッシュする可能性があります。
> 
> 

## <a name="prerequisites"></a>前提条件
* [使用の開始](data-lake-analytics-data-lake-tools-get-started.md) に関する記事を確認済みである。

## <a name="download-failed-vertex-to-local"></a>失敗した頂点をローカルにダウンロードする

ADL Tools for Visual Studio で失敗したジョブを開く際に、アラートが表示されます。 詳細なエラー メッセージが、[エラー] タブとウィンドウの上部にある黄色のアラート バーに表示されます。

1. **[ダウンロード]** をクリックして必要なリソースと入力ストリームをすべてダウンロードします。 ダウンロードに失敗した場合は **[再試行]** をクリックします。 

2. ダウンロードが完了したら **[開く]** をクリックし、ローカルのデバッグ環境を生成します。 デバッグのソリューションを備えた新しい Visual Studio インスタンスが作成され、自動的に開きます。 

3. デバッグの手順は、分離コードで失敗したジョブとアセンブリで失敗したジョブでは若干異なります。

    - [分離コードで失敗したジョブをデバッグする](#debug-job-failed-with-code-behind)
    - [アセンブリで失敗したジョブをデバッグする](#debug-job-failed-with-assemblies)

![Azure Data Lake Analytics の U-SQL デバッグ Visual Studio 頂点のダウンロード](./media/data-lake-analytics-debug-u-sql-jobs/data-lake-analytics-download-vertex.png)

## <a name="debug-job-failed-with-code-behind"></a>分離コードで失敗したジョブをデバッグする

分離コード ファイルでユーザー定義のコードを書きこみ (通常は U-SQL プロジェクトで "Script.usql.cs" という名前になります)、この U-SQL ジョブが失敗すると、生成されたデバッグ ソリューションにソース コードが自動的にインポートされ、Visual Studio ベースのデバッグ機能 (ウォッチ、変数など) を使用して問題のトラブルシューティングを行うことができます。 

デバッグする前に、[例外設定] ウィンドウ (**Ctrl + Alt + E**) で **Common Language Runtime Exceptions** をチェックしてください。

![Azure Data Lake Analytics の U-SQL デバッグ Visual Studio 設定](./media/data-lake-analytics-debug-u-sql-jobs/data-lake-analytics-clr-exception-setting.png)

1. **F5** キーを押すと、分離コードのコードが、例外によって停止されるまで自動的に実行されます。

2. プロジェクトで **ADLTool_Codebehind.usql.cs** を開き、ブレークポイントを設定してから F5 キーを押して、コードをステップ バイ ステップでデバッグします。

    ![Azure Data Lake Analytics U-SQL デバッグ例外](./media/data-lake-analytics-debug-u-sql-jobs/data-lake-analytics-debug-exception.png)

## <a name="debug-job-failed-with-assemblies"></a>アセンブリで失敗したジョブをデバッグする

U-SQL スクリプトで登録されているアセンブリを使用する場合、ソース コードはシステムで自動的に取得されません。ユーザー定義のコードをデバッグする前に、いくつかの構成を実行する必要があります。つまり、アセンブリのソース コードを自動的に生成されたソリューションに追加する必要があります。

### <a name="configure-the-solution"></a>ソリューションの構成

1. **[Solution ’VertexDebug’] (ソリューション 'VertexDebug') (右クリック)** > **[追加]** > **[既存のプロジェクト…]** を選択してアセンブリのソース コードを検索し、プロジェクトをデバッグ ソリューションに追加します。

    ![Azure Data Lake Analytics U-SQL デバッグ追加プロジェクト](./media/data-lake-analytics-debug-u-sql-jobs/data-lake-analytics-add-project-to-debug-solution.png)

2. ソリューションで **[LocalVertexHost] (右クリック)** > **[プロパティ]** を選択し、**[作業ディレクトリ]** パスをコピーします。

3. **アセンブリのソース コード プロジェクト (右クリック)** > **[プロパティ]** で、左側にある **[ビルド]** を選択し、手順 2 でコピーしたパスを **[出力]** > **[出力パス]** に貼り付けます。  

    ![Azure Data Lake Analytics U-SQL デバッグ PDB パス設定](./media/data-lake-analytics-debug-u-sql-jobs/data-lake-analytics-set-pdb-path.png)

4. **Ctrl + Alt + E** キーを押して、[例外設定] ウィンドウで**Common Language Runtime Exceptions (共通言語ランタイム例外)** をチェックします。

### <a name="start-debug"></a>デバッグの開始

1. **アセンブリのソース コード プロジェクト (右クリック)** > **[再構築]** を選択して PDB ファイルを LocalVertexHost 作業ディレクトリに出力します。

2. **F5** キーを押すと、プロジェクトが、例外によって停止されるまで自動的に実行されます。 最初に次のメッセージが表示される場合がありますが、このメッセージは無視できます。 デバッグ画面が表示されるまで最大&1; 分間かかることがあります。

    ![Azure Data Lake Analytics の U-SQL デバッグ Visual Studio 警告](./media/data-lake-analytics-debug-u-sql-jobs/data-lake-analytics-visual-studio-u-sql-debug-warning.png)

3. ソース コードを開いてブレークポイントを設定してから、**F5** キーを押してステップ バイ ステップでコードをデバッグします。

その他の Visual Studio ベースのデバッグ機能 (ウォッチ、変数など) を使用して、問題のデバッグを行うこともできます。 

**注**: コードを変更して新しい PDB ファイルを有効にするたびに、アセンブリのソース コード プロジェクトをビルドし直す必要があります。

デバッグが正常に完了すると、出力ウィンドウに次のメッセージが表示されます。

    The Program ‘LocalVertexHost.exe’ has exited with code 0 (0x0).

![Azure Data Lake Analytics の U-SQL デバッグ成功](./media/data-lake-analytics-debug-u-sql-jobs/data-lake-analytics-debug-succeed.png)

## <a name="resubmit-the-job"></a>ジョブを再送信する
デバッグが完了したら、失敗したジョブを再送信することができます。

1. 新しい .dll アセンブリを ADLA データベースに登録します。

    1. サーバー エクスプ ローラー/Cloud Explorer から、**[ADLA アカウント]** > **[データベース]** ノードを展開します。
    2. **[アセンブリ]** を右クリックしてアセンブリを登録します。 
    3. 新しい .dll アセンブリを ADLA データベースに登録します。
    ![Azure Data Lake Analytics の U-SQL デバッグ (アセンブリ登録)](./media/data-lake-analytics-debug-u-sql-jobs/data-lake-analytics-register-assembly.png)
2. または、C# コードを script.usql.cs (C# 分離コード ファイル) にコピーし直します。
3. ジョブを再送信します。

## <a name="next-steps"></a>次のステップ

* [U-SQL プログラミング ガイド](data-lake-analytics-u-sql-programmability-guide.md)
* [Azure Data Lake Analytics ジョブの U-SQL ユーザー定義演算子の開発](data-lake-analytics-u-sql-develop-user-defined-operators.md)
* [チュートリアル: Azure Data Lake Analytics U-SQL 言語の使用](data-lake-analytics-u-sql-get-started.md)
* [チュートリアル: Data Lake Tools for Visual Studio を使用する U-SQL スクリプトの開発](data-lake-analytics-data-lake-tools-get-started.md)





<!--HONumber=Jan17_HO1-->


