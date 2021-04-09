---
title: Visual Studio の Azure Stream Analytics ツールの設定
description: この記事では、Visual Studio の Azure Stream Analytics ツールのインストールの要件と設定方法について説明します。
author: su-jie
ms.author: sujie
ms.service: stream-analytics
ms.topic: how-to
ms.date: 05/22/2018
ms.openlocfilehash: 0077ac8465e8f785e772b384f26e0edc6874a1a0
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "98018702"
---
# <a name="install-azure-stream-analytics-tools-for-visual-studio"></a>Visual Studio の Azure Stream Analytics ツールのインストール

Visual Studio 2019 および Visual Studio 2017 は Azure Data Lake and Stream Analytics Tools をサポートしています。 この記事では、ツールのインストールとアンインストールの方法を説明します。

ツールの使用方法の詳細については、「[クイック スタート:Visual Studio を使用して Azure Stream Analytics ジョブを作成する](stream-analytics-quick-create-vs.md)」を参照してください。

## <a name="install"></a>インストール

Visual Studio Enterprise (Ultimate/Premium)、Professional、Community の各エディションでツールがサポートされています。 Express エディションおよび Visual Studio for Mac ではサポートされていません。

Visual Studio 2019 をお勧めします。

### <a name="install-for-visual-studio-2019-and-2017"></a>Visual Studio 2017 および 2019 のインストール<a name="recommended-visual-studio-2019-and-2017"></a>

Azure Data Lake and Stream Analytics Tools ツールは、 **[Azure の開発]** および **[データの保存と処理]** ワークロードの一部です。 インストール時に、これら 2 つのワークロードのいずれかを有効にします。 Visual Studio が既にインストールされている場合は、 **[ツール]**  >  **[ツールと機能の取得]** を選択して、ワークロードを追加します。

[Visual Studio 2019 (プレビュー 2 以降) または Visual Studio 2017 (15.3 以降)](https://www.visualstudio.com/) をダウンロードしてインストール手順に従ってください。

次のように **[データの保存と処理]** ワークロードを選択します。

![[データの保存と処理] ワークロードが選択される](./media/stream-analytics-tools-for-visual-studio-install/stream-analytics-tools-for-vs-2019-install-01.png)

次のように **[Azure の開発]** ワークロードを選択します。

![[Azure の開発] ワークロードが選択される](./media/stream-analytics-tools-for-visual-studio-install/stream-analytics-tools-for-vs-2019-install-02.png)

ワークロードを追加した後、ツールを更新します。 これは、Visual Studio 2019 の手順です。

1. **[拡張機能]**  >  **[拡張機能の管理]** を選択します。

1. **[拡張機能の管理]** で、 **[更新プログラム]** を選択し、**Azure Data Lake and Stream Analytics Tools** を選択します。

1. **[更新プログラム]** を選択して最新の拡張機能をインストールします。

![Visual Studio の拡張機能と更新プログラム](./media/stream-analytics-tools-for-visual-studio-install/stream-analytics-tools-vs2019-extensions-updates.png)

### <a name="install-for-visual-studio-2015-and-2013"></a>Visual Studio 2015 および 2013 のインストール<a name="visual-studio-2015-2013"></a>

Visual Studio Enterprise (Ultimate/Premium)、Professional、Community の各エディションでツールがサポートされています。 Express エディションではサポートされていません。

* Visual Studio 2015 または Visual Studio 2013 Update 4 をインストールします。
* [Web Platform Installer](https://www.microsoft.com/web/downloads/platform.aspx) を使用して、Microsoft Azure SDK for .NET バージョン 2.7.1 以上をインストールします。
* [Microsoft Azure Data Lake and Stream Analytics Tools for Visual Studio](https://www.microsoft.com/en-us/download/details.aspx?id=49504) をインストールします。

## <a name="update"></a>更新<a name="visual-studio-2019-and-2017"></a><a name="visual-studio-2015-and-2013"></a>

Visual Studio 2019 および Visual Studio 2017 では、新しいバージョンの通知が Visual Studio の通知に表示されます。

Visual Studio 2015 および Visual Studio 2013 では、ツールによって新しいバージョンが自動的に確認されます。 最新バージョンをインストールするには指示に従います。

## <a name="uninstall"></a>アンインストール

Azure Data Lake and Stream Analytics Tools をアンインストールできます。 Visual Studio 2019 または Visual Studio 2017 の場合は、 **[ツール]**  >  **[ツールと機能の取得]** を選択します。 **[Modifying]\(変更\)** で **Azure Data Lake and Stream Analytics Tools** をアンインストールできます。 これは、 **[データの保存と処理]** ワークロード、または **[Azure の開発]** ワークロードのいずれかに表示されます。

Visual Studio 2015 または Visual Studio 2013 からアンインストールするには、 **[コントロール パネル]**  >  **[プログラムと機能]** にアクセスします。 **Microsoft Azure Data Lake and Stream Analytics Tools for Visual Studio** をアンインストールします。
