---
title: "Visual Studio での Azure プライベート クラウドへのアクセス | Microsoft Docs"
description: "Visual Studio を使用してプライベート クラウドのリソースにアクセスする方法について説明します。"
services: visual-studio-online
documentationcenter: na
author: TomArcher
manager: douge
editor: 
ms.assetid: 9d733c8d-703b-44e7-a210-bb75874c45c8
ms.service: multiple
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: multiple
ms.date: 03/19/2017
ms.author: tarcher
translationtype: Human Translation
ms.sourcegitcommit: 0d8472cb3b0d891d2b184621d62830d1ccd5e2e7
ms.openlocfilehash: b29a3299433e57709ea31de3c7849230ea09c09a
ms.lasthandoff: 03/21/2017


---
# <a name="accessing-private-azure-clouds-with-visual-studio"></a>Visual Studio での Azure プライベート クラウドへのアクセス
既定では、Visual Studio は Azure パブリック クラウドの REST エンドポイントをサポートします。 このトピックでは、プライベート クラウドの証明書を使用して Visual Studio からプライベート クラウドにアクセスし、操作する方法を説明します。

## <a name="to-access-a-private-azure-cloud-in-visual-studio"></a>Visual Studio で Azure プライベート クラウドにアクセスするには
1. プライベート クラウドの [Azure クラシック ポータル](http://go.microsoft.com/fwlink/?LinkID=213885)で、発行設定ファイルをダウンロードするか、管理者に連絡して発行設定ファイルを入手します。 パブリック版の Azure では、このファイルをダウンロードするためのリンクは [https://manage.windowsazure.com/publishsettings/](https://manage.windowsazure.com/publishsettings/) です (ダウンロードしたファイルの拡張子は `.publishsettings` である必要があります)

1. Visual Studio を開きます

1. **サーバー エクスプローラー**で **[Azure]** ノードを右クリックし、コンテキスト メニューの **[サブスクリプションの管理およびフィルター]** をクリックします。
   
    ![Manage subscriptions command](./media/vs-azure-tools-access-private-azure-clouds-with-visual-studio/IC790778.png)

1. **[Microsoft Azure サブスクリプションの管理]** ダイアログで、**[証明書]** タブを選択し、**[インポート]** を選択します。
   
    ![Importing Azure certificates](./media/vs-azure-tools-access-private-azure-clouds-with-visual-studio/IC790779.png)

1. **[Microsoft Azure サブスクリプションのインポート]** ダイアログで、**[参照]** を選択します。

    ![[Microsoft Azure サブスクリプションのインポート] ダイアログの [参照] ボタン](./media/vs-azure-tools-access-private-azure-clouds-with-visual-studio/browse-button.png)

1. **[開く]** ダイアログで、発行設定ファイルを保存したディレクトリを参照し、ファイルを選択してから **[開く]** を選択します。

    ![発行設定ファイルの選択](./media/vs-azure-tools-access-private-azure-clouds-with-visual-studio/select-publish-settings-file.png)

1. **[Microsoft Azure サブスクリプションのインポート]** ダイアログに戻ったら、**[インポート]** を選択します。

    ![発行設定ファイルのインポート](./media/vs-azure-tools-access-private-azure-clouds-with-visual-studio/IC790780.png)

    証明書が発行設定ファイルから Visual Studio にインポートされ、プライベート クラウド リソースを操作できるようになりました。
   
## <a name="next-steps"></a>次のステップ
- [Visual Studio から Azure クラウド サービスへの発行](https://msdn.microsoft.com/library/azure/ee460772.aspx)
- [方法: 発行設定とサブスクリプション情報のダウンロードとインポート](https://msdn.microsoft.com/library/dn385850\(v=nav.70\).aspx)

