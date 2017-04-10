---
title: "Azure Data Lake Analytics ジョブの U-SQL アセンブリの開発 | Microsoft Docs"
description: "Data Lake Analytics ジョブで使用および再使用されるアセンブリの開発方法について説明します。 "
services: data-lake-analytics
documentationcenter: 
author: jejiang
manager: jhubbard
editor: cgronlun
ms.service: data-lake-analytics
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 11/30/2016
ms.author: jejiang
translationtype: Human Translation
ms.sourcegitcommit: dcda8b30adde930ab373a087d6955b900365c4cc
ms.openlocfilehash: 98b04d28d1b905dad19ad6cf608733c6554f01cf


---
# <a name="develop-u-sql-assemblies-for-azure-data-lake-analytics-jobs"></a>Azure Data Lake Analytics ジョブの U-SQL アセンブリの開発
分離コードをアセンブリに変換して、Data Lake Analytics ジョブで使用および再使用する方法について説明します。 

U-SQL により、C#、VB.Net、F# など、.NET 言語の独自のカスタム コードを容易に追加できます。 独自のランタイムをデプロイして、他の言語をサポートすることもできます。

カスタム コードを使用する方法として最も簡単なのは、Data Lake Tools for Visual Studio の分離コード機能を使用するというものです。 詳細については、「 [チュートリアル: Data Lake Tools for Visual Studio を使用する U-SQL スクリプトの開発](data-lake-analytics-data-lake-tools-get-started.md)」を参照してください。 分離コードを使用する場合の欠点をいくつか次に示します。

- スクリプトが送信されるたびにソース コードがアップロードされます。
- 分離コードは他のジョブと共有できません。

こうした欠点に対処するには、分離コードをアセンブリに変換し、そのアセンブリを Data Lake Analytics カタログに登録します。

## <a name="prerequisites"></a>前提条件
* Visual Studio 2015、Visual Studio 2013 update 4、または Visual Studio 2012 (Visual C++ インストール済み)。
* Microsoft Azure SDK for .NET バージョン 2.5 以上。  Web Platform Installer を使用してインストールします。
* Data Lake Analytics アカウント。  「[Azure Portal で Azure Data Lake Analytics の使用を開始する](data-lake-analytics-get-started-portal.md)」を参照してください。
* 「 [Azure Data Lake Analytics U-SQL Studio の使用](data-lake-analytics-u-sql-get-started.md) 」チュートリアルを読み進める。
* Azure に接続します。
* ソース データのアップロード。[Azure Data Lake Analytics U-SQL Studio の使用](data-lake-analytics-u-sql-get-started.md)に関するページを参照してください。 

## <a name="develop-assemblies-for-u-sql"></a>U-SQL のアセンブリの開発

**U-SQL ジョブを作成して送信するには**

1. **[ファイル]** メニューの **[新規作成]** をクリックし、**[プロジェクト]** をクリックします。
2. **[インストール済み]**、**[テンプレート]**、**[Azure Data Lake]**、**[U-SQL(ADLA)]** の順に展開し、**クラス ライブラリ (U-SQL アプリケーション)** テンプレートを選択して、**[OK]** をクリックします。
3. Class1.cs でコード記述します。  サンプル コードを次に示します。

        using Microsoft.Analytics.Interfaces;

        namespace USQLApplication_codebehind
        {
            [SqlUserDefinedProcessor]
            public class MyProcessor : IProcessor
            {
                public override IRow Process(IRow input, IUpdatableRow output)
                {
                    output.Set(0, input.Get<string>(0));
                    output.Set(0, input.Get<string>(0));
                    return output.AsReadOnly();
                }
            }
        }
4. **[ビルド]** メニューをクリックし、**[ソリューションのビルド]** をクリックして dll を作成します。

## <a name="register-assemblies"></a>アセンブリの登録

[Data Lake Analytics(U-SQL) カタログの使用](data-lake-analytics-use-u-sql-catalog.md)に関するページをご覧ください。


## <a name="use-the-assemblies"></a>アセンブリの使用

「[Azure Data Lake Tools for Visual Studio Code の使用](data-lake-analytics-data-lake-tools-for-vscode.md)」を参照してください。

## <a name="see-also"></a>関連項目
* [PowerShell で Azure Data Lake Analytics の使用を開始する](data-lake-analytics-get-started-powershell.md)
* [Azure ポータルで Azure Data Lake Analytics の使用を開始する](data-lake-analytics-get-started-portal.md)
* [U-SQL アプリケーションを開発するための Data Lake Tools for Visual Studio の使用](data-lake-analytics-data-lake-tools-get-started.md)
* [Data Lake Analytics(U-SQL) カタログの使用](data-lake-analytics-use-u-sql-catalog.md)
* [Azure Data Lake Tools for Visual Studio Code の使用](data-lake-analytics-data-lake-tools-for-vscode.md)


<!--HONumber=Dec16_HO2-->


