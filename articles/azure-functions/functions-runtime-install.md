---
title: Azure Functions ランタイムのインストール | Microsoft Docs
description: Azure Functions ランタイム プレビュー 2 をインストールする方法
services: functions
author: apwestgarth
manager: stefsch
ms.assetid: ''
ms.service: azure-functions
ms.topic: conceptual
ms.date: 11/28/2017
ms.author: anwestg
ms.openlocfilehash: 0fbc76f3cb3fea84347ef55e40e1b2bed9b98152
ms.sourcegitcommit: 44e85b95baf7dfb9e92fb38f03c2a1bc31765415
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/28/2019
ms.locfileid: "70085495"
---
# <a name="install-the-azure-functions-runtime-preview-2"></a>Azure Functions ランタイム プレビュー 2 をインストールする

[!INCLUDE [intro](../../includes/functions-runtime-preview-note.md)]

Azure Functions ランタイム プレビュー 2 をインストールする場合は、次の手順に従います。

1. コンピューターが最小要件を満たしていることを確認します。
1. [Azure Functions ランタイム プレビュー インストーラー](https://aka.ms/azafrv2)をダウンロードする
1. Azure Functions ランタイム プレビュー 1 をアンインストールします。
1. Azure Functions ランタイム プレビュー 2 をインストールします。
1. Azure Functions ランタイム プレビュー 2 の構成を完了します。
1. Azure Functions ランタイム プレビューで最初の関数を作成します

## <a name="prerequisites"></a>前提条件

Azure Functions ランタイム プレビューをインストールする前に、次のリソースが使用可能になっている必要があります。

1. Microsoft Windows Server 2016 または Microsoft Windows 10 Creators Update (Professional または Enterprise Edition) を実行しているコンピューター。
1. ネットワーク上で実行される SQL Server のインスタンス。  必要な最小エディションは SQL Server Express です。

## <a name="uninstall-previous-version"></a>以前のバージョンをアンインストールする

以前に Azure Functions ランタイム プレビューをインストールした場合は、最新リリースをインストールする前にアンインストールする必要があります。  Windows の [プログラムの追加と削除] でプログラムを削除して、Azure Functions ランタイム プレビューをアンインストールします。

## <a name="install-the-azure-functions-runtime-preview"></a>Azure Functions ランタイム プレビューをインストールする

Azure Functions ランタイム プレビュー インストーラーの手順に従って、Azure Functions ランタイム プレビューの管理ロールと worker ロールをインストールします。  同じコンピューターに管理ロールと worker ロールをインストールすることができます。  ただし、多くの関数アプリを追加すると、複数の worker に関数を拡張できるように、追加のマシン上にさらに worker ロールをデプロイする必要があります。

## <a name="install-the-management-and-worker-role-on-the-same-machine"></a>同じコンピューターに管理ロールと worker ロールをインストールする

1. Azure Functions ランタイム プレビュー インストーラーを実行します。

    ![Azure Functions ランタイム プレビュー インストーラー][1]

1. **[次へ]** をクリックします。
1. **使用許諾契約書**の条項を読み終わったら、**チェック ボックスをオン**にして条項に同意し、 **[次へ]** をクリックして先に進みます。
1. このマシンにインストールするロールとして **[Functions Management Role]\(Functions 管理ロール\)** と **[Functions Worker Role]\(Functions worker ロール\)** のどちらか一方または両方を選び、 **[次へ]** をクリックします。

    ![Azure Functions ランタイム プレビュー インストーラー - ロールの選択][3]

    > [!NOTE]
    > **Functions worker ロール**は、他の多くのコンピューターにインストールできます。 そのためには、この手順に従い、インストーラーで **[Functions Worker Role]\(Functions worker ロール\)** だけを選びます。

1. **[次へ]** をクリックして、**Azure Functions ランタイム セットアップ ウィザード**でコンピューターへのインストール プロセスを開始します。
1. 完了すると、セットアップ ウィザードによって **Azure Functions ランタイム**構成ツールが起動されます。

    ![Azure Functions ランタイム プレビュー インストーラーの完了][6]

    > [!NOTE]
    > **Windows 10** にインストールしている場合、**コンテナー**機能が前もって有効にされていないときは、インストールを完了するためにコンピューターを再起動するように **Azure Functions ランタイム セットアップ**から求められます。

## <a name="configure-the-azure-functions-runtime"></a>Azure Functions ランタイムを構成する

Azure Functions ランタイムのインストールを完了するには、構成を完了する必要があります。

1. **Azure Functions ランタイム**構成ツールに、コンピューターにインストールされるロールが表示されます。

    ![Azure Functions ランタイム プレビュー構成ツール][7]

1. **[データベース]** タブをクリックし、[データベース マスター キー](https://docs.microsoft.com/sql/relational-databases/security/encryption/sql-server-and-database-encryption-keys-database-engine)の指定など、SQL Server インスタンスの接続の詳細を入力して、 **[適用]** をクリックします。  SQL Server インスタンスへの接続性は、Azure Functions ランタイムでランタイムをサポートするデータベースを作成するために必要です。

    ![Azure Functions ランタイム プレビュー データベース構成][8]

1. **[資格情報]** タブをクリックします。ここで、すべての関数アプリをホストするために、ファイル共有で使用する目的で新しい資格情報を 2 つ作成する必要があります。  **ファイル共有の所有者**および**ファイル共有のユーザー**の **[ユーザー名]** と **[パスワード]** の組み合わせを指定し、 **[適用]** をクリックします。

    ![Azure Functions ランタイム プレビュー資格情報][9]

1. **[ファイルの共有]** タブをクリックします。ここでは、ファイルの共有の場所の詳細を指定する必要があります。  自分用のファイル共有を作成するか、既存のファイル共有を使用して、 **[適用]** をクリックします。  新しいファイル共有の場所を選択した場合は、Azure Functions ランタイムで使用するディレクトリを指定する必要があります。

    ![Azure Functions ランタイム プレビュー ファイル共有][10]

1. **[IIS]** タブをクリックします。このタブには、Azure Functions ランタイム構成ツールで作成される IIS の Web サイトの詳細が表示されます。  Azure Functions ランタイム プレビュー ポータル用にカスタムの DNS 名をここで指定できます。  **[適用]** をクリックして完了します。

    ![Azure Functions ランタイム プレビュー IIS][11]

1. **[サービス]** タブをクリックします。このタブには、Azure Functions ランタイム構成ツールのサービスの状態が表示されます。  初期構成後に **Azure Functions ホスト アクティブ化サービス**が実行されていない場合は、 **[サービスの開始]** をクリックします。

    ![Azure Functions ランタイム プレビュー構成の完了][12]

1. **Azure Functions ランタイム ポータル**を `https://<machinename>.<domain>/` として参照します。

    ![Azure Functions ランタイム プレビュー ポータル][13]

## <a name="create-your-first-function-in-azure-functions-runtime-preview"></a>Azure Functions ランタイム プレビューで最初の関数を作成する

Azure Functions ランタイム プレビューで最初の関数を作成するには

1. **Azure Functions Runtime ポータル**を `https://<machinename>.<domain>`、たとえば `https://mycomputer.mydomain.com` として参照します。

1. **ログイン** を要求されます。ドメインにデプロイした場合はドメイン アカウントのユーザー名とパスワードを使用し、それ以外の場合はローカル アカウントのユーザー名とパスワードを使用してポータルにログインします。

    ![Azure Functions ランタイム プレビュー ポータルのログイン][14]

1. 関数アプリを作成するには、サブスクリプションを作成する必要があります。  ポータルの左上隅で、サブスクリプションの横にある **[+]** オプションをクリックします。

    ![Azure Functions ランタイム プレビュー ポータルのサブスクリプション][15]

1. **[DefaultPlan]** を選択し、サブスクリプションの名前を入力して、 **[作成]** をクリックします。

    ![Azure Functions ランタイム プレビュー ポータルのサブスクリプション プランと名前][16]

1. すべての関数アプリがポータルの左側のウィンドウに表示されます。  新しい Function App を作成するには、見出し **[Function App]** を選択し、 **[+]** オプションをクリックします。

1. 関数アプリの名前を入力し、適切なサブスクリプションを選び、プログラミング対象の Azure Functions ランタイムのバージョンを選んで **[作成]** をクリックします

    ![Azure Functions ランタイム プレビュー ポータルの新しい関数アプリ][17]

1. 新しい関数アプリは、ポータルの左側のウィンドウに表示されます。  関数を選び、ポータルで中央のウィンドウの上部にある **[新しい関数]** をクリックします。

    ![Azure Functions ランタイム プレビュー テンプレート][18]

1. タイマー トリガー関数を選び、右側のポップアップで関数に名前を付け、スケジュールを `*/5 * * * * *` に変更し (この cron 式によってタイマー関数が 5 秒ごとに実行されます)、 **[作成]** をクリックします

    ![Azure Functions ランタイム プレビューの新しいタイマー関数構成][19]

1. 関数が作成されました。  Function App の実行ログは、ポータルの下部にある **[ログ]** ウィンドウを展開して表示できます。

    ![Azure Functions ランタイム プレビュー関数の実行][20]

<!--Image references-->
[1]: ./media/functions-runtime-install/AzureFunctionsRuntime_Installer1.png
[2]: ./media/functions-runtime-install/AzureFunctionsRuntime_Installer2-EULA.png
[3]: ./media/functions-runtime-install/AzureFunctionsRuntime_Installer3-ChooseRoles.png
[4]: ./media/functions-runtime-install/AzureFunctionsRuntime_Installer4-Install.png
[5]: ./media/functions-runtime-install/AzureFunctionsRuntime_Installer5-Progress.png
[6]: ./media/functions-runtime-install/AzureFunctionsRuntime_Installer6-InstallComplete.png
[7]: ./media/functions-runtime-install/AzureFunctionsRuntime_Configuration1.png
[8]: ./media/functions-runtime-install/AzureFunctionsRuntime_Configuration2_SQL.png
[9]: ./media/functions-runtime-install/AzureFunctionsRuntime_Configuration3_Credentials.png
[10]: ./media/functions-runtime-install/AzureFunctionsRuntime_Configuration4_Fileshare.png
[11]: ./media/functions-runtime-install/AzureFunctionsRuntime_Configuration5_IIS.png
[12]: ./media/functions-runtime-install/AzureFunctionsRuntime_Configuration6_Services.png
[13]: ./media/functions-runtime-install/AzureFunctionsRuntime_Portal.png
[14]: ./media/functions-runtime-install/AzureFunctionsRuntime_Portal_Login.png
[15]: ./media/functions-runtime-install/AzureFunctionsRuntime_Portal_Subscriptions.png
[16]: ./media/functions-runtime-install/AzureFunctionsRuntime_Portal_Subscriptions1.png
[17]: ./media/functions-runtime-install/AzureFunctionsRuntime_Portal_NewFunctionApp.png
[18]: ./media/functions-runtime-install/AzureFunctionsRuntime_v1FunctionsTemplates.png
[19]: ./media/functions-runtime-install/AzureFunctionsRuntime_Portal_NewTimerFunction.png
[20]: ./media/functions-runtime-install/AzureFunctionsRuntime_Portal_RunningV2Function.png
