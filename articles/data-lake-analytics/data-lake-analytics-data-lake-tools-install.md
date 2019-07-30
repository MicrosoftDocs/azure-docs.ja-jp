---
title: Azure Data Lake Tools for Visual Studio をインストールする
description: この記事では、Azure Data Lake Tools for Visual Studio のインストール方法について説明します。
services: data-lake-analytics
ms.service: data-lake-analytics
author: saveenr
ms.author: saveenr
ms.reviewer: jasonwhowell
ms.assetid: ad8a6992-02c7-47d4-a108-62fc5a0777a3
ms.topic: conceptual
ms.date: 05/03/2018
ms.openlocfilehash: 3269d38b691ec4dd9573a241c89dadc285787143
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/13/2019
ms.locfileid: "60408137"
---
# <a name="install-data-lake-tools-for-visual-studio"></a>Data Lake Tools for Visual Studio のインストール

Visual Studio を使用して Azure Data Lake Analytics アカウントを作成し、[U-SQL](data-lake-analytics-u-sql-get-started.md) でジョブを定義し、Data Lake Analytics サービスにジョブを送信する方法について説明します。 Data Lake Analytics の詳細については、「 [Azure Data Lake Analytics の概要](data-lake-analytics-overview.md)」を参照してください。

## <a name="prerequisites"></a>前提条件

* **Visual Studio**:Express を除くすべてのエディションがサポートされます。
    * Visual Studio 2017
    * Visual Studio 2015
    * Visual Studio 2013
* **Microsoft Azure SDK for .NET** バージョン 2.7.1 以上。  [Web プラットフォーム インストーラー](https://www.microsoft.com/web/downloads/platform.aspx)を使用してインストールします。
* **Data Lake Analytics** アカウント。 アカウントを作成するには、「[Azure Portal で Azure Data Lake Analytics の使用を開始する](data-lake-analytics-get-started-portal.md)」を参照してください。

## <a name="install-azure-data-lake-tools-for-visual-studio-2017"></a>Azure Data Lake Tools for Visual Studio 2017 をインストールする

Azure Data Lake Tools for Visual Studio は、Visual Studio 2017 15.3 以降でサポートされます。 このツールは、Visual Studio インストーラーの **[データの保存と処理]** ワークロードと **[Azure の開発]** ワークロードに含まれます。 Visual Studio のインストールの一環として、この 2 つのワークロードのいずれかを有効にします。  

次のように **[データの保存と処理]** ワークロードを有効にします。![[データの保存と処理] ワークロードを有効にする](./media/data-lake-analytics-data-lake-tools-get-started/data-lake-tools-for-vs-2017-install-01.png)

次のように **[Azure の開発]** ワークロードを有効にします。![[Azure の開発] ワークロードを有効にする](./media/data-lake-analytics-data-lake-tools-get-started/data-lake-tools-for-vs-2017-install-02.png)

## <a name="install-azure-data-lake-tools-for-visual-studio-2013-and-2015"></a>Azure Data Lake Tools for Visual Studio 2013 と 2015 をインストールする

Azure Data Lake Tools for Visual Studio を [ダウンロード センター](https://aka.ms/adltoolsvs)からダウンロードしてインストールします。 インストールした後、以下を確認してください。
* **サーバー エクスプローラー** > の **[Azure]** ノードに **[Data Lake Analytics]** ノードが含まれている。 
* **[ツール]** メニューに **[Data Lake]** 項目がある。


## <a name="next-steps"></a>次の手順
* 診断情報のログについては、「 [Accessing diagnostics logs for Azure Data Lake Analytics (Azure Data Lake Analytics の診断ログへのアクセス)](data-lake-analytics-diagnostic-logs.md)
* より複雑なクエリを表示する場合は、「 [チュートリアル: Azure Data Lake Analytics を使用して Web サイトのログを分析する](data-lake-analytics-analyze-weblogs.md)」をご覧ください。
* 頂点実行ビューの使用方法については、「[Data Lake Tools for Visual Studio での頂点実行ビューの使用](data-lake-analytics-data-lake-tools-use-vertex-execution-view.md)」を参照してください。
