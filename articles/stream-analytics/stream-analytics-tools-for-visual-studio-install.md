---
title: Visual Studio の Azure Stream Analytics ツールの設定
description: この記事では、Visual Studio の Azure Stream Analytics ツールのインストールの要件と設定方法について説明します。
services: stream-analytics
author: su-jie
ms.author: sujie
manager: kfile
ms.reviewer: jasonh
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 05/22/2018
ms.openlocfilehash: 673f4935dce28b30c10e6abf4c7d22e00c1dd73a
ms.sourcegitcommit: fcb674cc4e43ac5e4583e0098d06af7b398bd9a9
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/18/2019
ms.locfileid: "56343912"
---
# <a name="install-azure-stream-analytics-tools-for-visual-studio"></a>Visual Studio の Azure Stream Analytics ツールのインストール
Azure Stream Analytics ツールでは、Visual Studio 2017、2015、2013 がサポートされます。 この記事では、ツールのインストールとアンインストールの方法を説明します。

ツールの使用について詳しくは、[Visual Studio の Azure Stream Analytics ツール](stream-analytics-quick-create-vs.md)に関する記事をご覧ください。

## <a name="install"></a>Install
### <a name="recommended-visual-studio-2019-and-2017"></a>推奨:Visual Studio 2019 および 2017
* [Visual Studio 2019 (プレビュー 2 以降) および Visual Studio 2017 (15.3 以降)](https://www.visualstudio.com/) をダウンロードしてください。 Enterprise (Ultimate/Premium)、Professional、Community の各エディションがサポートされています。 Express エディションはサポートされていません。 Mac 上の Visual Studio 2017 はサポートされていません。 
* Stream Analytics ツールは、Visual Studio 2017 の **[Azure の開発]** および **[データの保存と処理]** ワークロードの一部です。 Visual Studio のインストールの一環として、この 2 つのワークロードのいずれかを有効にします。

次のように **[データの保存と処理]** ワークロードを有効にします。

![[データの保存と処理] ワークロードが選択される](./media/stream-analytics-tools-for-visual-studio-install/stream-analytics-tools-for-vs-2017-install-01.png)

次のように **[Azure の開発]** ワークロードを有効にします。

![[Azure の開発] ワークロードが選択される](./media/stream-analytics-tools-for-visual-studio-install/stream-analytics-tools-for-vs-2017-install-02.png)

* [ツール] メニューの **[拡張機能と更新プログラム]** を選択します。 インストールされた拡張機能で Azure Data Lake および Stream Analytics ツールを見つけて、**[更新]** をクリックし、最新の拡張機能をインストールします。 

![Visual Studio の拡張機能と更新プログラム](./media/stream-analytics-tools-for-visual-studio-install/stream-analytics-tools-for-vs-extensions-updates.png)

### <a name="visual-studio-2015-2013"></a>Visual Studio 2015、2013
* Visual Studio 2015 または Visual Studio 2013 Update 4 をインストールします。 Enterprise (Ultimate/Premium)、Professional、Community の各エディションがサポートされています。 Express エディションはサポートされていません。 
* [Web Platform Installer](https://www.microsoft.com/web/downloads/platform.aspx) を使用して、Microsoft Azure SDK for .NET バージョン 2.7.1 以上をインストールします。
* [Visual Studio の Azure Stream Analytics ツール](https://www.microsoft.com/en-us/download/details.aspx?id=49504)をインストールします。

## <a name="update"></a>アップデート

### <a name="visual-studio-2019-and-2017"></a>Visual Studio 2019 および 2017
新しいバージョンの通知が Visual Studio の通知に表示されます。

![Visual Studio の新しいバージョンの通知](./media/stream-analytics-tools-for-visual-studio-install/stream-analytics-new-version-reminder-vs-tools.png)

### <a name="visual-studio-2015-and-2013"></a>Visual Studio 2015 および 2013
インストールされている Visual Studio の Stream Analytics ツールによって、新しいバージョンが自動的に確認されます。 最新バージョンをインストールするにはポップアップ ウィンドウの指示に従います。 


## <a name="uninstall"></a>アンインストール

### <a name="visual-studio-2019-and-2017"></a>Visual Studio 2019 および 2017
Visual Studio インストーラーをダブルクリックし、**[変更]** を選択します。 **[データの保存と処理]** ワークロードまたは **[Azure の開発]** ワークロードから、**[Azure Data Lake および Stream Analytics ツール]** チェック ボックスをオフにします。

### <a name="visual-studio-2015-and-2013"></a>Visual Studio 2015 および 2013
コントロール パネルに移動し、**Microsoft Azure Data Lake and Stream Analytics tools for Visual Studio** をアンインストールします。





