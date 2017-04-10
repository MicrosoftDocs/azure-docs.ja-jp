---
title: "Visual Studio の Azure アプリケーションの公開ウィザードの使用 | Microsoft Docs"
description: "Visual Studio の Azure アプリケーションの公開ウィザードで、さまざまな設定を構成する方法について説明します。"
services: visual-studio-online
documentationcenter: na
author: TomArcher
manager: douge
editor: 
ms.assetid: 7d8f1ac9-e439-47e0-a183-0642c4ea1920
ms.service: multiple
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/21/2017
ms.author: tarcher
translationtype: Human Translation
ms.sourcegitcommit: 424d8654a047a28ef6e32b73952cf98d28547f4f
ms.openlocfilehash: 4d9e1564c3fcbdfd59edb0e24158df9954c26026
ms.lasthandoff: 03/22/2017


---
# <a name="using-the-visual-studio-publish-azure-application-wizard"></a>Visual Studio の Azure アプリケーションの公開ウィザードの使用
Visual Studio で Web アプリケーションをデプロイしたら、**Azure アプリケーションの公開**ウィザードを使用して、そのアプリケーションを Azure クラウド サービスに発行できます。 

> [!NOTE]
> このトピックでは Web サイトではなくクラウド サービスへのデプロイについて説明します。 Web サイトへのデプロイの詳細については、 [Azure の Web サイトのデプロイ方法](https://social.msdn.microsoft.com/Search/windowsazure?query=How%20to%20Deploy%20an%20Azure%20Web%20Site&Refinement=138&ac=4#refinementChanges=117&pageNumber=1&showMore=false)に関するページを参照してください。
> 
> 

## <a name="accessing-the-publish-azure-application-wizard"></a>Azure アプリケーションの公開ウィザードへのアクセス

Azure アプリケーションの公開ウィザードには、使用する Visual Studio プロジェクトの種類に応じて、2 とおりの方法でアクセスできます。

**Azure クラウド サービス プロジェクトを使用する場合:**

1. Visual Studio で Azure クラウド サービス プロジェクトを開くか新たに作成します。

1. **ソリューション エクスプローラー**でそのプロジェクトを右クリックし、コンテキスト メニューの **[発行]** を選択します。

**Azure で有効になっていない Web アプリケーション プロジェクトを使用する場合:**

1. Visual Studio で Azure クラウド サービス プロジェクトを開くか新たに作成します。

1. **ソリューション エクスプローラー**でそのプロジェクトを右クリックし、コンテキスト メニューの **[変換]** > **[Microsoft Azure クラウド サービス プロジェクトに変換]** を選択します。 

1. **ソリューション エクスプローラー**で新しく作成された Azure プロジェクトを右クリックし、コンテキスト メニューの **[発行]** を選択します。

## <a name="sign-in-page"></a>サインイン ページ

![サインイン ページ](./media/vs-azure-tools-publish-azure-application-wizard/sign-in.png)

**アカウント** - アカウント ドロップダウン リストでアカウントを選択するか、**[アカウントの追加]** を選択します。

**[サブスクリプションの選択]** - デプロイに使用するサブスクリプションを選択します。
   
## <a name="settings-page---common-settings-tab"></a>設定ページ - [共通設定] タブ   

![[共通設定]](./media/vs-azure-tools-publish-azure-application-wizard/settings-common-settings.png)

**[クラウド サービス]** - ドロップダウンを使用して、既存のクラウド サービスを選択するか、**[&lt;新規作成>]** を選択してクラウド サービスを作成します。 クラウド サービスごとに、データ センターがかっこ内に表示されます。 クラウド サービスのデータ センターの場所は、ストレージ アカウントのデータ センターの場所 ([詳細設定]) と同じであることが推奨されます。  

**[環境]** - **[運用]** または **[ステージング]** を選択します。 アプリケーションをテスト環境にデプロイする場合は、ステージング環境を選択します。 

**[ビルド構成]** - **[デバッグ]** または **[リリース]** を選択します。

**[サービス構成]** - **[クラウド]** または **[ローカル]** を選択します。
   
**[すべてのロールのリモート デスクトップを有効にする]** - サービスにリモート接続できるようにする場合は、このチェック ボックスをオンにします。 このオプションは、主にトラブルシューティングのために使用されます。 このチェック ボックスをオンにすると、 **[リモート デスクトップ構成]** ダイアログ ボックスが表示されます。 構成を変更するには、**[設定]** リンクをクリックします。
   
**[すべての Web ロールの Web 配置を有効にする]** - サービスで Web デプロイを有効にするには、このチェック ボックスをオンにします。 この機能を使用するには、**[すべてのロールのリモート デスクトップを有効にする]** をオンにする必要があります。 詳細については、[[Visual Studio 使用した Azure クラウド サービスの発行](https://msdn.microsoft.com/library/azure/ff683672.aspx)](https://msdn.microsoft.com/library/azure/ff683672.aspx)に関するページをご覧ください。 

## <a name="settings-page---advanced-settings-tab"></a>設定ページ - [詳細設定] タブ

![詳細設定](./media/vs-azure-tools-publish-azure-application-wizard/settings-advanced-settings.png)

**[デプロイ ラベル]** - 既定の名前をそのまま使用するか、独自の名前を入力します。 デプロイ ラベルに日付を付加するには、チェック ボックスをオンのままにします。 
   
**[ストレージ アカウント]** - このデプロイに使用するストレージ アカウントを選択するか、**[&lt;新規作成>] を選択してストレージ アカウントを作成します。 ストレージ アカウントごとに、データ センターがかっこ内に表示されます。 ストレージ アカウントのデータ センターの場所は、クラウド サービスのデータ センターの場所 ([共通設定]) と同じであることが推奨されます。  
   
Azure ストレージ アカウントには、アプリケーション デプロイのパッケージが格納されます。 アプリケーションのデプロイ後、パッケージはストレージ アカウントから削除されます。

**[失敗時に配置を削除]**- 発行中にエラーが発生した場合にデプロイを削除するには、このチェック ボックスをオンにします。 クラウド サービスの固定仮想 IP アドレスを保持する場合は、これをオフにする必要があります。

**[配置の更新]** - 更新されたコンポーネントだけをデプロイする場合は、このチェック ボックスをオンにします。 このデプロイは、完全なデプロイより高速な場合があります。 クラウド サービスの固定仮想 IP アドレスを保持する場合は、これをオンにする必要があります。 

**[配置の更新] - [設定]** - このダイアログは、ロールの更新方法をさらに詳しく指定するときに使用します。 **[増分更新]** を選択した場合は、アプリケーションのインスタンスが 1 つずつ更新されるので、アプリケーションをいつでも利用できます。 **[同時更新]** を選択した場合は、アプリケーションのすべてのインスタンスが同時に更新されます。 同時更新の方が高速ですが、更新処理中はサービスが利用できない可能性があります。 

![[配置の設定]](./media/vs-azure-tools-publish-azure-application-wizard/deployment-settings.png)

**[IntelliTrace を有効にする]** - IntelliTrace を有効にするかどうかを指定します。 IntelliTrace を使用すると、ロール インスタンスを Azure で実行する際に広範なデバッグ情報を記録できます。 問題の原因を調べる必要がある場合は、IntelliTrace ログを使用して、コードが Azure で実行されているかのように Visual Studio から調査することができます。 IntelliTrace の使用方法の詳細については、「[Visual Studio と IntelliTrace を使用した発行済みの Azure クラウド サービスのデバッグ](./vs-azure-tools-intellitrace-debug-published-cloud-services.md)」をご覧ください。 

**[プロファイルを有効にする]** - パフォーマンス プロファイルを有効にするかどうかを指定します。 Virtual Studio プロファイラーを使用すると、クラウド サービスの実行方法について計算的側面から詳細な分析が得られます。 Visual Studio プロファイラーの使用方法の詳細については、[Azure クラウド サービスのパフォーマンスのテスト](./vs-azure-tools-performance-profiling-cloud-services.md)に関する記事をご覧ください。

**[すべてのロールのリモート デバッガーを有効にする]** - リモート デバッグを有効にするかどうかを指定します。 Visual Studio を使用したクラウド サービスのデバッグの詳細については、「[Visual Studio での Azure クラウド サービスまたは仮想マシンのデバッグ](./vs-azure-tools-debug-cloud-services-virtual-machines.md)」をご覧ください。

## <a name="diagnostics-settings-page"></a>[診断設定] ページ

![診断設定](./media/vs-azure-tools-publish-azure-application-wizard/diagnostic-settings.png)

診断を使用して、Azure クラウド サービス (または Azure 仮想マシン) のトラブルシューティングを行うことができます。 診断については、「[Azure クラウド サービスおよび仮想マシン用の診断の構成](./vs-azure-tools-diagnostics-for-cloud-services-and-virtual-machines.md)」をご覧ください。 Application Insights については、「[Application Insights とは何か?](./application-insights/app-insights-overview.md)」をご覧ください。

## <a name="summary-page"></a>概要ページ

![概要](./media/vs-azure-tools-publish-azure-application-wizard/summary.png)

**[ターゲット プロファイル]** - 選択した設定から発行プロファイルを作成できます。 たとえば、テスト環境用と運用環境用に 1 つずつプロファイルを作成できます。 このプロファイルを保存するには、 **[保存]** アイコンをクリックします。 ウィザードでプロファイルが作成され、Visual Studio プロジェクトに保存されます。 プロファイル名を変更するには、**[ターゲット プロファイル]** の一覧を開き、**[<管理…>]** を選択します。
   
   > [!NOTE]
   > 発行プロファイルが Visual Studio のソリューション エクスプローラーに表示され、プロファイル設定が .azurePubxml という拡張子を持つファイルに書き込まれます。 設定は、XML タグの属性として保存されます。
   > 
   > 

## <a name="publishing-your-application"></a>アプリケーションの発行

プロジェクトのデプロイのすべての設定を構成したら、ダイアログの下部にある **[発行]** をクリックします。 Visual Studio の **[出力]** ウィンドウでプロセスの状態を監視できます。

## <a name="next-steps"></a>次のステップ
- [Visual Studio から Azure クラウド サービスに Web アプリケーションを移行および発行する](./vs-azure-tools-migrate-publish-web-app-to-cloud-service.md)
- [Visual Studio を使用して Azure クラウド サービスを発行する方法を確認する](./vs-azure-tools-publishing-a-cloud-service.md)
- [Visual Studio と IntelliTrace を使用して発行済みの Azure クラウド サービスをデバッグする](./vs-azure-tools-intellitrace-debug-published-cloud-services.md)
- [Azure クラウド サービスのパフォーマンスをテストする](./vs-azure-tools-performance-profiling-cloud-services.md)
- [Azure クラウド サービスおよび仮想マシン用の診断を構成する](./vs-azure-tools-diagnostics-for-cloud-services-and-virtual-machines.md) 
- [Application Insights とは何か?](./application-insights/app-insights-overview.md)
