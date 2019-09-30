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
ms.date: 08/30/2019
ms.openlocfilehash: 08f0456b6a8b663f0af68fbb179de14b9e3acf79
ms.sourcegitcommit: 083aa7cc8fc958fc75365462aed542f1b5409623
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/11/2019
ms.locfileid: "70914072"
---
# <a name="install-data-lake-tools-for-visual-studio"></a>Data Lake Tools for Visual Studio のインストール

Visual Studio を使用して Azure Data Lake Analytics アカウントを作成します。 [U-SQL](data-lake-analytics-u-sql-get-started.md) でジョブを定義し、Data Lake Analytics サービスにジョブを送信することができます。 Data Lake Analytics の詳細については、「 [Azure Data Lake Analytics の概要](data-lake-analytics-overview.md)」を参照してください。

## <a name="prerequisites"></a>前提条件

* **Visual Studio**:Express を除くすべてのエディションがサポートされます。

  * Visual Studio 2019
  * Visual Studio 2017
  * Visual Studio 2015
  * Visual Studio 2013

* **Microsoft Azure SDK for .NET** バージョン 2.7.1 以上。 [Web プラットフォーム インストーラー](https://www.microsoft.com/web/downloads/platform.aspx)を使用してインストールします。
* **Data Lake Analytics** アカウント。 アカウントを作成するには、「[Azure Portal で Azure Data Lake Analytics の使用を開始する](data-lake-analytics-get-started-portal.md)」を参照してください。

## <a name="install-azure-data-lake-tools-for-visual-studio-2017-or-visual-studio-2019"></a>Azure Data Lake Tools for Visual Studio 2017 または Visual Studio 2019

Azure Data Lake Tools for Visual Studio は、Visual Studio 2017 15.3 以降でサポートされます。 このツールは、 **[データの保存と処理]** ワークロードと **[Azure の開発]** ワークロードに含まれます。 Visual Studio のインストールの一環として、この 2 つのワークロードのいずれかを有効にします。

次のように **[データの保存と処理]** ワークロードを有効にします。

![[データの保存と処理] ワークロードを有効にする](./media/data-lake-analytics-data-lake-tools-get-started/data-lake-tools-for-vs-2019-install-01.png)

次のように **[Azure の開発]** ワークロードを有効にします。

![[Azure の開発] ワークロードを選択する](./media/data-lake-analytics-data-lake-tools-get-started/data-lake-tools-for-vs-2019-install-02.png)

## <a name="install-azure-data-lake-tools-for-visual-studio-2013-and-2015"></a>Azure Data Lake Tools for Visual Studio 2013 と 2015 をインストールする

[Microsoft Azure Data Lake and Stream Analytics Tools for Visual Studio](https://aka.ms/adltoolsvs) をダウンロードしてインストールします。 インストール後、Visual Studio には次の変更が含まれています。

* **サーバー エクスプローラー** > の **[Azure]** ノードに **[Data Lake Analytics]** ノードが含まれている。
* **[ツール]** メニューに **[Data Lake]** 項目がある。

## <a name="next-steps"></a>次の手順

* 診断情報のログについては、「[Azure Data Lake Analytics の診断ログへのアクセス](data-lake-analytics-diagnostic-logs.md)」を参照してください。
* より複雑なクエリを表示する場合は、「 [チュートリアル: Azure Data Lake Analytics を使用して Web サイトのログを分析する](data-lake-analytics-analyze-weblogs.md)」をご覧ください。
* 頂点実行ビューの使用方法については、「[Data Lake Tools for Visual Studio での頂点実行ビューの使用](data-lake-analytics-data-lake-tools-use-vertex-execution-view.md)」を参照してください。
