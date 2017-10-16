---
title: "Azure Functions ランタイムのインストール | Microsoft Docs"
description: "Azure Functions ランタイムをインストールする方法"
services: functions
documentationcenter: 
author: apwestgarth
manager: stefsch
editor: 
ms.assetid: 
ms.service: functions
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: multiple
ms.topic: article
ms.date: 05/08/2017
ms.author: anwestg
ms.openlocfilehash: 1e4188313a87d07f396e5f8edc8969dd5da2c436
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/11/2017
---
# <a name="install-the-azure-functions-runtime-preview"></a>Azure Functions ランタイム プレビューをインストールする

Azure Functions ランタイム プレビューをインストールする場合は、次の手順に従う必要があります。

1. コンピューターが最小要件を満たしていることを確認する
1. [Azure Functions ランタイム プレビュー インストーラー](https://aka.ms/azafr)をダウンロードする 
1. Azure Functions ランタイム プレビューをインストールする
1. Azure Functions ランタイム プレビューの構成を完了する

## <a name="prerequisites"></a>前提条件

Azure Functions ランタイム プレビューをインストールする前に、次の内容を準備する必要があります。

1. Microsoft Windows Server 2016 または Microsoft Windows 10 Creators Update (Professional または Enterprise Edition) を実行しているコンピューター。
1. ネットワーク上で実行される SQL Server のインスタンス。  エディションの最小要件は SQL Server Express です。

## <a name="install-the-azure-functions-runtime-preview"></a>Azure Functions ランタイム プレビューをインストールする

Azure Functions ランタイム プレビュー インストーラーの手順に従って、Azure Functions ランタイム プレビューの管理ロールと worker ロールをインストールします。  同じコンピューターに管理ロールと worker ロールをインストールすることができます。  ただし、多くの関数を追加すると、複数の worker ロールに関数を拡張できるように、追加のマシン上にさらに worker ロールをデプロイする必要があります。

## <a name="install-the-management-and-worker-role-on-the-same-machine"></a>同じコンピューターに管理ロールと worker ロールをインストールする

1. Azure Functions ランタイム プレビュー インストーラーを実行します。

    ![Azure Functions ランタイム プレビュー インストーラー][1]

1. **[次へ] をクリック**し、インストーラーの最初の段階を通過して先に進みます。
1. **使用許諾契約書**の条項を読み終わったら、**チェック ボックスをオン**にして条項に同意し、**[次へ] をクリック**して先に進みます。
1. このコンピューターにインストールするロールである、**Functions 管理ロール**、**Functions worker ロール**、またはその両方を選択したので、**[次へ] をクリック**します。

    ![Azure Functions ランタイム プレビュー インストーラー - ロールの選択][3]

    > [!NOTE]
    > そのために、その他の多数のコンピューターに **Functions worker ロール**をインストールすることができます。次の手順に従ってインストーラーで **Functions worker ロール**を選択するのみです。

1. **[次へ] をクリック**して、**Azure Functions ランタイム インストーラー**をコンピューターにインストールします。
1. インストーラーが完了すると、**Azure Functions ランタイム構成ツール**が起動されます。

    ![Azure Functions ランタイム プレビュー インストーラーの完了][5]

    > [!NOTE]
    > **Windows 10** にインストールしている場合、**コンテナー**機能が前もって有効にされていないときは、インストールを完了するためにコンピューターを再起動するように **Azure Functions ランタイム** インストーラーから求められます。

## <a name="configure-the-azure-functions-runtime"></a>Azure Functions ランタイムを構成する

Azure Functions ランタイムのインストールを完了するには、構成を完了する必要があります。

1. **Azure Functions ランタイム構成ツール**に、コンピューターにインストールされているロールが表示されます。

    ![Azure Functions ランタイム プレビュー構成ツール][6]

1. **[データベース]** タブをクリックして、**SQL Server インスタンスの接続詳細** を入力し、**[適用] をクリック**します。  これは、Azure Functions ランタイムでランタイムをサポートするデータベースを作成するために必要です。
    
    ![Azure Functions ランタイム プレビュー データベース構成][7]

1. **[資格情報]** タブをクリックします。この画面で、すべての Azure Functions をホストするために、FileShare と使用する目的で新しい資格情報を 2 つ作成する必要があります。  **ファイル共有所有者**と**ファイル共有ユーザー**の**ユーザー名とパスワードの組み合わせを指定**し、**[適用]** をクリックします。

    ![Azure Functions ランタイム プレビュー資格情報][8]

1. **[ファイルの共有]** タブをクリックします。この画面で、**ファイル共有の場所**の詳細を指定する必要があります。  そのためには、自分用に作成するか、既存のファイル共有を使用して、**[適用]** をクリックします。  新しいファイル共有の場所を選択した場合は、Azure Functions ランタイムで使用するディレクトリを指定する必要があります。
    
    ![Azure Functions ランタイム プレビュー ファイル共有][9]

1. **[IIS]** タブをクリックします。このタブには、Azure Functions ランタイムのインストールで作成される IIS の Web サイトの詳細が表示されます。  **[適用] をクリック**して完了します。

    ![Azure Functions ランタイム プレビュー IIS][10]

1. **[サービス]** タブをクリックします。このタブには、Azure Functions ランタイム インストールのサービスの状態が表示されます。  初期構成後、**Azure Functions ホスト アクティブ化サービス**が実行されていない場合は、**[サービスの開始]** をクリックします。

    ![Azure Functions ランタイム プレビュー構成の完了][11]

1. 最後に、**Azure Functions ランタイム ポータル**を `https://<machinename>/` として参照します。

    ![Azure Functions ランタイム プレビュー ポータル][12]


<!--Image references-->
[1]: ./media/functions-runtime-install/AzureFunctionsRuntime_Installer1.png
[2]: ./media/functions-runtime-install/AzureFunctionsRuntime_Installer2-EULA.png
[3]: ./media/functions-runtime-install/AzureFunctionsRuntime_Installer3-ChooseRoles.png
[4]: ./media/functions-runtime-install/AzureFunctionsRuntime_Installer4-Install.png
[5]: ./media/functions-runtime-install/AzureFunctionsRuntime_Installer5-InstallComplete.png
[6]: ./media/functions-runtime-install/AzureFunctionsRuntime_Configuration1.png
[7]: ./media/functions-runtime-install/AzureFunctionsRuntime_Configuration2_SQL.png
[8]: ./media/functions-runtime-install/AzureFunctionsRuntime_Configuration3_Credentials.png
[9]: ./media/functions-runtime-install/AzureFunctionsRuntime_Configuration4_Fileshare.png
[10]: ./media/functions-runtime-install/AzureFunctionsRuntime_Configuration5_IIS.png
[11]: ./media/functions-runtime-install/AzureFunctionsRuntime_Configuration6_Services.png
[12]: ./media/functions-runtime-install/AzureFunctionsRuntime_Portal.png