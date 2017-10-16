---
title: "App Services の worker ロールをスケールアウトする - Azure Stack | Microsoft Docs"
description: "Azure Stack App Services の詳細なガイダンス"
services: azure-stack
documentationcenter: 
author: kathm
manager: slinehan
editor: anwestg
ms.assetid: 3cbe87bd-8ae2-47dc-a367-51e67ed4b3c0
ms.service: azure-stack
ms.workload: app-service
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 4/6/2017
ms.author: kathm
ms.translationtype: HT
ms.sourcegitcommit: 988e7fe2ae9f837b661b0c11cf30a90644085e16
ms.openlocfilehash: f844658c6ad2529fd385476be63095bdae7c88e5
ms.contentlocale: ja-jp
ms.lasthandoff: 09/15/2017

---
# <a name="app-service-on-azure-stack-adding-more-worker-roles"></a>Azure Stack 上の App Service: worker ロールを追加する 

ここでは、Azure Stack worker ロールでの App Service を拡大縮小する手順について説明します。 任意のサイズのアプリケーションをサポートする追加の worker ロールを作成する手順について説明します。

> [!NOTE]
> Azure Stack POC 環境の RAM が 96 GB 以下の場合、容量の追加が困難な可能性があります。

既定では、Azure Stack 上の App Service は無料で共有の worker 階層をサポートしています。 他の worker 階層を追加するには、worker ロールを追加する必要があります。

既定の Azure Stack 上の App Service インストールでデプロイされる内容がわからない場合は、「[App Service on Azure Stack overview](azure-stack-app-service-overview.md)」(Azure Stack 上の App Service の概要) を参照してください。

Azure Stack 上の App Service に容量を追加するには、2 つの方法があります。
1.  [[App Service Resource Provider Admin]\(App Service リソース プロバイダー管理\) 内から worker を直接追加します](#Add-additional-workers-directly-from-within-the-App-Service-Resource-Provider-Admin)。
2.  [手動で追加の VM を作成し、App Service リソース プロバイダーに追加します](#Create-additional-VMs-manually-and-add-them-to-the-App-Service-Resource-Provider)。

## <a name="add-additional-workers-directly-within-the-app-service-resource-provider-admin"></a>[App Service Resource Provider Admin]\(App Service リソース プロバイダー管理\) 内から worker を直接追加します。

1.  Azure Stack ポータルにサービス管理者としてログインします。
2.  **[リソースプロバイダー]** に移動し、**[App Service Resource Provider Admin (App Service リソース プロバイダー管理)]** を選択します。![Azure Stack リソース プロバイダー][1]
3.  **[ロール]** をクリックします。  デプロイされているすべての App Service ロールの内訳が表示されます。
4.  **[新しいロール インスタンス]** オプションをクリックします ![新しいロール インスタンスの追加][2]
5.  **[新しいロール インスタンス]** ブレードで次の手順を実行します。
    1. 追加する**ロール インスタンス**数を選択します。  プレビューの最大数は 10 個です。
    2. **[ロールの種類]** を選択します。  このプレビューでは、このオプションは、Web ワーカーに制限されています。
    3. この worker をデプロイする **worker 階層**を選択します。既定の選択肢は、[小]、[中]、[大]、[共有] です。  独自の worker 階層を作成した場合、その worker 階層も選択肢として使用できます。
    4. **[OK]** をクリックして追加の worker をデプロイします。
6.  Azure Stack 上の App Service によって、VM が追加および構成され、必要なすべてのソフトウェアがインストールされ、プロセスの完了時には準備完了とマークされます。  このプロセスには約 80 分かかる可能性があります。
7.  **ロール** ブレードで worker を表示することで、新しい worker の準備状況を監視できます。

>[!NOTE]
>  このプレビューでは、統合された新しいロール インスタンス フローは worker ロールに制限され、サイズ A1 の VM のみがデプロイされます。  今後のリリースで、この機能は拡張する予定です。

## <a name="manually-adding-additional-capacity-to-app-service-on-azure-stack"></a>Azure Stack 上の App Service に容量を手動で追加する

ロールを追加するには、次の手順を実行する必要があります。

1. [新しい仮想マシンを作成する](#step-1-create-a-new-vm-to-support-the-new-instance-size)
2. [仮想マシンを構成する](#step-2-configure-the-virtual-machine)
3. [Azure Stack ポータルで Web ワーカー ロールを構成する](#step-3-configure-the-web-worker-role-in-the-azure-stack-portal)
4. [App Service プランを構成する](#step-4-configure-app-service-plans)

## <a name="step-1-create-a-new-vm-to-support-the-new-instance-size"></a>手順 1: 新しいインスタンス サイズをサポートする新しい VM を作成する
[こちらの記事](azure-stack-provision-vm.md)の説明に従って仮想マシンを作成し、次のように選択されていることを確認します。

* ユーザー名とパスワード: Azure Stack 上の App Service をインストールしたときに指定したものと同じユーザー名とパスワードを指定します。
* サブスクリプション: 既定のプロバイダー サブスクリプションを使用します。
* リソース グループ: **AppService-LOCAL** を選択します。

> [!NOTE]
> Azure Stack 上の App Service をデプロイした先と同じリソース グループに worker ロールの仮想マシンを格納します (今回のリリースで推奨される構成です)。
> 
> 

## <a name="step-2-configure-the-virtual-machine"></a>手順 2: 仮想マシンを構成する
デプロイが完了した後は、Web ワーカー ロールをサポートするために次の構成が必要です。

1. ポータルで **AppService-LOCAL** リソース グループを参照し、手順 1 で作成した新しいマシンを選択します。
2. VM ブレードの接続をクリックして、リモート デスクトップのプロファイルをダウンロードします。  VM に対してリモート デスクトップ セッションを開くプロファイルを開きます。
3. 手順 1 で指定したユーザー名とパスワードを使用して VM にログインします。
4. **[スタート]** ボタンをクリックし、「PowerShell」を入力して PowerShell を開きます。 **PowerShell.exe** を右クリックし、**[管理者として実行]** を選択して管理者モードで PowerShell を開きます。
5. 次の各コマンドを 1 つずつ PowerShell ウィンドウに貼り付け、Enter キーを押します。
   
   ```netsh advfirewall firewall set rule group="File and Printer Sharing" new enable=Yes```
   ```netsh advfirewall firewall set rule group="Windows Management Instrumentation (WMI)" new enable=yes```
   ```reg add HKLM\\SOFTWARE\\Microsoft\\Windows\\CurrentVersion\\Policies\\system /v LocalAccountTokenFilterPolicy /t REG\_DWORD /d 1 /f```
   
6. リモート デスクトップ セッションを閉じます。
7. ポータルから VM を再起動します。

> [!NOTE]
> これらは、Azure Stack 上の App Service の最小要件です。 また、Azure Stack に含まれる Windows 2012 R2 イメージの既定設定です。 これらの手順は、今後の参照のために、また別のイメージを使用する場合のために記載しています。
> 
> 

## <a name="step-3-configure-the-worker-role-in-the-azure-stack-portal"></a>手順 3: Azure Stack ポータルで worker ロールを構成する
1. **ClientVM** 上でサービス管理者としてポータルを開きます。
2. **[リソース プロバイダー]** &gt; **[App Service Resource Provider Admin]\(App Service リソース プロバイダー管理\)** に移動します。![[App Service Resource Provider Admin]\(App Service リソース プロバイダー管理\)][3]
3. 設定ブレードで **[ロール]** をクリックします。![App Service リソース プロバイダーのロール][4]
4. **[ロール インスタンスの追加]** をクリックします。
5. **[サーバー名]** のテキストボックスに、セクション 1 で作成したサーバーの **IP アドレス**を入力します。
6. 追加する **[ロールの種類]** (コントローラー、管理サーバー、フロント エンド、Web ワーカー、パブリッシャー、ファイル サーバー) を選択します。  このインスタンスでは、Web ワーカーを選択します。
7. 新しいインスタンスをデプロイする **[階層]** をクリックします (小、中、大、または共有)。  独自の worker 階層を作成した場合、その worker 階層も選択肢として使用できます。
8. **[OK]** をクリックします。
9. **[ロール]** ビューに戻ります
10. VM を割り当てたロールの種類と worker 階層の組み合わせに対応する行をクリックします。
11. 追加したサーバー名を探します。 状態列を確認し、状態が [準備完了] になってから次の手順に進みます。 このプロセスには約 80 分かかる可能性があります。 ![App Service リソース プロバイダー ロールの準備完了][5]

## <a name="step-4-configure-app-service-plans"></a>手順 4: App Service プランを構成する

1. ClientVM でポータルにサインインします。
2. **[新規作成]** &gt; **[Web and Mobile]\(Web とモバイル\)** に移動します。
3. デプロイするアプリケーションの種類を選択します。
4. アプリケーションの情報を入力し、**[AppService Plan / Location]\(AppService プラン/場所\)** を選択します。
    1. **[Create New]**をクリックします。
    2. プランを作成し、プランに対応する価格レベルを選択します。

> [!NOTE]
> このブレードでは複数のプランを作成できます。 ただし、デプロイする前に、適切なプランを選択していることを確認してください。
> 
> 

既定で使用できる複数の価格レベルの例を次に示します。  特定の worker 階層に使用できる worker がない場合、対応する価格レベルを選択するオプションは使用できません。![Azure Stack 上の App Service の既定の価格レベル][6]

<!--Image references-->
[1]: ./media/azure-stack-app-service-add-worker-roles/azure-stack-resource-providers.png
[2]: ./media/azure-stack-app-service-add-worker-roles/app-service-new-role-instance.png
[3]: ./media/azure-stack-app-service-add-worker-roles/app-service-resource-provider-admin.png
[4]: ./media/azure-stack-app-service-add-worker-roles/app-service-resource-provider-roles.png
[5]: ./media/azure-stack-app-service-add-worker-roles/app-service-resource-provider-role-ready.png
[6]: ./media/azure-stack-app-service-add-worker-roles/app-service-resource-provider-pricing-tier.png

