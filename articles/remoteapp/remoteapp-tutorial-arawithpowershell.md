---
title: "Azure RemoteApp での PowerShell コマンドレットの使用 | Microsoft Docs"
description: "Azure RemoteApp で Windows PowerShell コマンドレットを使用する方法について説明します。"
services: remoteapp
documentationcenter: 
author: guscatalano
manager: mbaldwin
editor: 
ms.assetid: 7d3d5ded-6f73-4de6-a8ac-c1d622e842a2
ms.service: remoteapp
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: compute
ms.date: 11/23/2016
ms.author: mbaldwin
translationtype: Human Translation
ms.sourcegitcommit: c40545833da86426d3e71955b8eb8627db3c1e4b
ms.openlocfilehash: 4f2b61e7c97beb7556bffca89c3608aaee831ddc


---
# <a name="use-windows-powershell-cmdlets-with-azure-remoteapp"></a>Azure RemoteApp での Windows PowerShell コマンドレットの使用
> [!IMPORTANT]
> Azure RemoteApp の提供は終了しました。 詳細については、 [お知らせ](https://go.microsoft.com/fwlink/?linkid=821148) をご覧ください。
> 
> 

 Azure RemoteApp PowerShell コマンドレットを使用して、コレクションを管理、保守できます。 次の情報を使用して、作業を開始しましょう。

## <a name="get-the-cmdlets"></a>コマンドレットの取得
- - -
最初に Azure Powershell コマンドレットを[ここ](http://go.microsoft.com/?linkid=9811175)からダウンロードします。RemoteApp のコマンドレットもそこに含まれています。 

詳細については、 [Azure RemoteApp コマンドレットのヘルプ](https://msdn.microsoft.com/library/mt428031.aspx)をご覧ください。

## <a name="configure-azure-cmdlets-to-use-your-subscription"></a>サブスクリプションを使用するために Azure コマンドレットを構成します。
- - -
[このガイド](/powershell/azureps-cmdlets-docs) に従うと、Azure サブスクリプションに対してコマンドレットを使用できるようになります。

次の手順に従って、すぐに作業を開始できます。

1. [Azure PowerShell コマンドレット](http://go.microsoft.com/?linkid=9811175)をダウンロードしてインストールします。
2. Microsoft Azure PowerShell を起動します。
3. **Add-AzureAccount** を実行して、Azure サブスクリプションに対して認証します。 メッセージが表示されたら、Azure ポータルへのサインインに使用するのと同じユーザー名とパスワードを入力します。  
4. **Get-AzureSubscription** を実行して、自分のユーザー アカウントに関連付けられたサブスクリプションを表示します。 
5. **Select-AzureSubscription** を実行して、PowerShell コンソールで使用するサブスクリプション名または ID を指定します。

これで、Azure PowerShell コンソールが構成され、使用できるようになりました。 Azure PowerShell コンソールを起動するたびに、2 ～ 5 の手順を繰り返してください。  

## <a name="create-a-cloud-collection"></a>Create a cloud collection (クラウド コレクションを作成する)
- - -
次のコマンドを実行するだけです。

    New-AzureRemoteAppCollection -Collectionname RAppO365Col1 -ImageName "Office 365 ProPlus (Subscription required)" -Plan Basic -Location "West US" - Description "Office 365 Collection."

上記のコマンドを実行すると、Microsoft Office 365 アプリケーション  (Excel、OneNote、Outlook、PowerPoint、Visio および Word) が自動的に発行されます。

コレクションの作成には、完了まで 30 分以上かかる場合があります。 そのため、このコマンドは追跡に使用できる次のような ID を返します。

    Get-AzureRemoteAppOperationResult -TrackingId xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx

コレクションが完成したら、次のコマンドを使用して、コレクションにユーザーを追加できます。

    Add-AzureRemoteAppUser -CollectionName RAppO365Col1 -Type microsoftAccount -UserUpn someone@domain.com

これで完了です。 ユーザーは、 [ここ](https://www.remoteapp.windowsazure.com/)にある Azure RemoteApp クライアントを使用してアプリケーションに接続できるはずです。

## <a name="available-cmdlets"></a>利用可能なコマンドレット
他にも多くのコマンドがあり、それらのコマンドに関するドキュメントも間もなく公開される予定です。

基本的な RemoteApp コレクションのコマンドレット: 

* New-AzureRemoteAppCollection
* Get-AzureRemoteAppCollection
* Set-AzureRemoteAppCollection
* Update-AzureRemoteAppCollection
* Remove-AzureRemoteAppCollection
* Add-AzureRemoteAppUser
* Get-AzureRemoteAppUser
* Remove-AzureRemoteAppUser
* Get-AzureRemoteAppSession
* Disconnect-AzureRemoteAppSession
* Invoke-AzureRemoteAppSessionLogoff
* Send-AzureRemoteAppSessionMessage
* Get-AzureRemoteAppProgram
* Get-AzureRemoteAppStartMenuProgram
* Publish-AzureRemoteAppProgram
* Unpublish-AzureRemoteAppProgram
* Get-AzureRemoteAppCollectionUsageDetails
* Get-AzureRemoteAppCollectionUsageSummary
* Get-AzureRemoteAppPlan

RemoteApp 仮想ネットワークのコマンドレット:

* New-AzureRemoteAppVNet
* Get-AzureRemoteAppVNet
* Set-AzureRemoteAppVNet
* Remove-AzureRemoteAppVNet
* Get-AzureRemoteAppVpnDevice
* Get-- AzureRemoteAppVpnDeviceConfigScript
* Reset-AzureRemoteAppVpnSharedKey

RemoteApp テンプレート イメージのコマンドレット:

* New-AzureRemoteAppTemplateImage
* Get-AzureRemoteAppTemplateImage
* Rename-AzureRemoteAppTemplateImage
* Remove-AzureRemoteAppTemplateImage

その他の RemoteApp のコマンドレット:

* Get-AzureRemoteAppLocation
* Get-AzureRemoteAppWorkspace
* Set-AzureRemoteAppWorkspace
* Get-AzureRemoteAppOperationResult




<!--HONumber=Dec16_HO1-->


