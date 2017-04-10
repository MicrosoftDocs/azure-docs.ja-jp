---
title: "Visual Studio と IntelliTrace を使用した発行済みの Azure クラウド サービスのデバッグ | Microsoft Docs"
description: "Visual Studio と IntelliTrace を使用して、クラウド サービスをデバッグする方法について説明します。"
services: visual-studio-online
documentationcenter: n/a
author: TomArcher
manager: douge
editor: 
ms.assetid: 5e6662fc-b917-43ea-bf2b-4f2fc3d213dc
ms.service: visual-studio-online
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: multiple
ms.workload: na
ms.date: 03/21/2017
ms.author: tarcher
translationtype: Human Translation
ms.sourcegitcommit: 424d8654a047a28ef6e32b73952cf98d28547f4f
ms.openlocfilehash: 4629800edd6c89384264299aad3a638ba275acfe
ms.lasthandoff: 03/22/2017


---
# <a name="debugging-a-published-azure-cloud-service-with-visual-studio-and-intellitrace"></a>Visual Studio と IntelliTrace を使用した発行済みの Azure クラウド サービスのデバッグ
IntelliTrace を使用すると、ロール インスタンスを Azure で実行する際に広範なデバッグ情報を記録できます。 問題の原因を調べる必要がある場合は、IntelliTrace ログを使用して、コードが Azure で実行されているかのように Visual Studio から調査することができます。 実際には、IntelliTrace は Azure アプリケーションが Azure のクラウド サービスとして実行されている際にキー コードの実行および環境データを記録し、その記録されたデータを Visual Studio で再生できるようにします。 

Visual Studio Enterprise がインストールされており、Azure アプリケーションが .NET Framework 4 以降のバージョンを対象としている場合に IntelliTrace を使用することができます。 IntelliTrace は、Azure ロールの情報を収集します。 これらのロールの仮想マシンは、常に 64 ビット オペレーティング システムを実行します。

代わりに、[リモート デバッグ](http://go.microsoft.com/fwlink/p/?LinkId=623041)を使用して、Azure で実行されているクラウド サービスに直接接続することができます。

> [!IMPORTANT]
> IntelliTrace は、デバッグ シナリオのみを対象としており、運用環境のデプロイには使用できません。
> 

## <a name="configure-an-azure-application-for-intellitrace"></a>IntelliTrace の Azure アプリケーションを構成する
IntelliTrace を Azure アプリケーションで有効にするには、Visual Studio Azure プロジェクトからアプリケーションを作成し、発行する必要があります。 IntelliTrace は Azure に発行する前に Azure アプリケーション向けに構成する必要があります。 IntelliTrace を構成せずにアプリケーションを発行する場合は、プロジェクトを再発行する必要があります。 詳細については、[Visual Studio を使用した Azure クラウド サービス プロジェクトの発行](http://go.microsoft.com/fwlink/p/?LinkId=623012)に関する記事をご覧ください。

1. Azure アプリケーションをデプロイする準備ができたら、プロジェクトのビルド ターゲットが **[デバッグ]**に設定されていることを確認します。

1. **ソリューション エクスプローラー**でプロジェクトを右クリックし、コンテキスト メニューの **[発行]** を選択します。
   
1. **[Azure アプリケーションの公開]** ダイアログで Azure サブスクリプションを選択し、**[次へ]** をクリックします。

1. **[設定]** ページで **[詳細設定]** タブを選択します。

1. クラウドへの発行時にアプリケーションの IntelliTrace ログを収集するには、**[IntelliTrace を有効にする]** をオンにします。
   
1. IntelliTrace の基本構成をカスタマイズするには、**[IntelliTrace を有効にする]** の横の **[設定]** をクリックします。

    ![IntelliTrace の設定リンク](./media/vs-azure-tools-intellitrace-debug-published-cloud-services/intellitrace-settings-link.png)
   
1. **[IntelliTrace の設定]** ダイアログで、ログに記録するイベント、呼び出し情報を収集するかどうか、ログの収集対象となるモジュールとプロセス、記録に割り当てる領域のサイズを指定できます。 IntelliTrace の詳細については、 [IntelliTrace によるデバッグ](http://go.microsoft.com/fwlink/?LinkId=214468)に関するページを参照してください。
   
    ![IntelliTrace の設定](./media/vs-azure-tools-intellitrace-debug-published-cloud-services/IC519063.png)

IntelliTrace ログは、IntelliTrace 設定で指定された最大サイズの循環ログ ファイルです(既定のサイズは 250 MB)。 IntelliTrace ログは、仮想マシンのファイル システム内のファイルに収集されます。 ログを要求すると、スナップショットがその時点で取得され、ローカル コンピューターにダウンロードされます。

Azure クラウド サービスが Azure に発行されたら、次の図に示すように、**サーバー エクスプローラー**の Azure ノードから IntelliTrace が有効になっているかどうかを確認できます。

![サーバー エクスプローラー - 有効化された IntelliTrace](./media/vs-azure-tools-intellitrace-debug-published-cloud-services/IC744134.png)

## <a name="download-intellitrace-logs-for-a-role-instance"></a>ロール インスタンスの IntelliTrace ログをダウンロードする
Visual Studio で、次の手順に従ってロール インスタンスの IntelliTrace ログをダウンロードできます。

1. **サーバー エクスプローラー**で **[クラウド サービス]** ノードを展開し、ログをダウンロードするロール インスタンスを見つけます。 

1. ロール インスタンスを右クリックし、コンテキスト メニューの **[IntelliTrace ログの表示]** を選択します。 

    ![[IntelliTrace ログの表示] メニュー オプション](./media/vs-azure-tools-intellitrace-debug-published-cloud-services/view-intellitrace-logs.png)

1. IntelliTrace ログは、ローカル コンピューター上のディレクトリのファイルにダウンロードされます。 IntelliTrace ログを要求するたびに、新しいスナップショットが作成されます。 ログのダウンロード中、Visual Studio の **[Azure のアクティビティ ログ]** ウィンドウに操作の進行状況が表示されます。 次の図に示すように、操作の行項目を展開して詳細を表示することができます。

![VST_IntelliTraceDownloadProgress](./media/vs-azure-tools-intellitrace-debug-published-cloud-services/IC745551.png)

IntelliTrace ログのダウンロード中も、Visual Studio での作業を続行することができます。 ログのダウンロードが完了すると、Visual Studio でログが開きます。

> [!NOTE]
> IntelliTrace ログには、フレームワークによって生成され、後で処理される例外が含まれている場合があります。 内部のフレームワーク コードでは、このような例外がロールの起動時の正常な処理として生成されるため、無視しても問題ありません。
> 
> 

## <a name="next-steps"></a>次のステップ
- [Azure クラウド サービスのデバッグのオプション](vs-azure-tools-debugging-cloud-services-overview.md)
- [Visual Studio を使用した Azure クラウド サービスの発行](vs-azure-tools-publishing-a-cloud-service.md)
