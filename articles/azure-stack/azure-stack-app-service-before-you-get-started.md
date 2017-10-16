---
title: "App Service on Azure Stack をデプロイする前に | Microsoft Docs"
description: "App Service on Azure Stack をデプロイする前に済ましておく必要がある手順です"
services: azure-stack
documentationcenter: 
author: apwestgarth
manager: stefsch
editor: 
ms.assetid: 
ms.service: azure-stack
ms.workload: app-service
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 7/3/2017
ms.author: anwestg
ms.translationtype: HT
ms.sourcegitcommit: f5c887487ab74934cb65f9f3fa512baeb5dcaf2f
ms.openlocfilehash: 3cba11acc6279f24d0a47af8978610180724c0a2
ms.contentlocale: ja-jp
ms.lasthandoff: 09/15/2017

---
# <a name="before-you-get-started-with-app-service-on-azure-stack"></a>App Service on Azure Stack を開始する前に

Azure App Service on Azure Stack をインストールするには、いくつか必要なものがあります。

- [Azure Stack Development Kit](azure-stack-run-powershell-script.md) のデプロイが完了していること。
- App Service on Azure Stack の小規模なデプロイに十分な領域が Azure Stack システムにあること。  必要なディスク領域は約 20 GB です。
- App Service on Azure Stack 用の仮想マシンを作成するときに使う Windows Server VM イメージ。
- [SQL Server を実行しているサーバー](#SQL-Server)。

>[!NOTE] 
> 以下の手順は "*すべて*"、Azure Stack ホスト コンピューター上で行います。

リソース プロバイダーをデプロイするには、管理者として PowerShell Integrated Scripting Environment (ISE) を実行する必要があります。 このため、Azure Active Directory へのサインインに使う Internet Explorer のプロファイルで、Cookie と JavaScript を許可する必要があります。

## <a name="turn-off-internet-explorer-enhanced-security"></a>Internet Explorer のセキュリティ強化を無効にする

1.  Azure Stack Development Kit のマシンに **AzureStack/administrator** としてサインインし、**サーバー マネージャー**を開きます。

2.  管理者とユーザーの両方について、**[Internet Explorer セキュリティ強化の構成]** をオフにします。

3.  Azure Stack Development Kit のマシンに管理者としてサインインし、**サーバー マネージャー**を開きます。

4.  管理者とユーザーの両方について、**[Internet Explorer セキュリティ強化の構成]** をオフにします。

## <a name="enable-cookies"></a>Cookie を有効にする

1.  **[スタート]** > **[すべてのアプリ]** > **[Windows アクセサリ]** の順に選びます。 **[Internet Explorer]** を右クリックし、 > **[詳細]** > **[管理者として実行]** の順に選択します。

2.  メッセージが表示されたら **[推奨されるセキュリティと互換性の設定を使用する]** を選び、**[OK]** を選びます。

3.  Internet Explorer で、**[ツール]** (歯車アイコン) > **[インターネット オプション]** > **[プライバシー]** > **[詳細設定]** の順に選びます。

4.  **[Advanced] \(詳細設定)** を選択します。 **[承諾する]** チェック ボックスがオンになっていることを確認します。 **[OK]** を 2 回選びます。

5.  Internet Explorer を閉じ、管理者として PowerShell ISE を再起動します。

## <a name="install-powershell-for-azure-stack"></a>PowerShell for Azure Stack をインストールする

PowerShell for Azure Stack をインストールするには、「[PowerShell をインストールする](azure-stack-powershell-install.md)」の手順に従います。

## <a name="use-visual-studio-with-azure-stack"></a>Azure Stack で Visual Studio を使う

Azure Stack で Visual Studio を使うには、「[Visual Studio のインストール](azure-stack-install-visual-studio.md)」の手順に従います。

## <a name="add-a-windows-server-2016-vm-image-to-azure-stack"></a>Windows Server 2016 VM イメージを Azure Stack に追加する

App Service は複数の仮想マシンをデプロイするので、Azure Stack には Windows Server 2016 VM イメージが必要です。 VM イメージをインストールするには、「[Windows Server 2016 VM イメージの Azure Stack Marketplace への追加](azure-stack-add-default-image.md)」の手順に従います。

## <a name="SQL-Server"></a>SQL Server

App Service on Azure Stack は、App Service リソース プロバイダーを実行するため、SQL Server インスタンスにアクセスし、2 つのデータベースを作成してホストする必要があります。  SQL Server VM を Azure Stack にデプロイする場合は、SQL の接続レベルが**パブリック**に設定されている必要があります。  App Service on Azure Stack インストーラーでオプションを設定するときに、使う SQL Server インスタンスを選ぶことができます。

## <a name="next-steps"></a>次のステップ

- [App Service リソース プロバイダーをインストールする](azure-stack-app-service-deploy.md)。

<!--Image references-->
[1]: ./media/azure-stack-app-service-before-you-get-started/PSGallery.png
[2]: ./media/azure-stack-app-service-before-you-get-started/WebPI_InstalledProducts.png

