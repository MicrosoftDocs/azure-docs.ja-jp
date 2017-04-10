---
title: "PowerShell を使った SSL 証明書のバインド"
description: "PowerShell を使って SSL 証明書を Web アプリにバインドする方法について説明します。"
services: app-service\web
documentationcenter: 
author: ahmedelnably
manager: stefsch
editor: 
ms.assetid: 8ce32508-e982-48d3-b878-0e526afda537
ms.service: app-service-web
ms.workload: web
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/13/2016
ms.author: aelnably
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: e0302c4e4e63383a2b19bd6f02f3be8d83f5ff98


---
# <a name="azure-app-service-ssl-certificate-binding-using-powershell"></a>PowerShell を使った Azure App Service SSL 証明書のバインド
Microsoft Azure PowerShell バージョン 1.1.0 のリリースに伴って新しいコマンドレットが追加され、既存の SSL 証明書または新しい SSL 証明書を既存の Web アプリにバインドできるようになりました。

[!INCLUDE [app-service-web-to-api-and-mobile](../../includes/app-service-web-to-api-and-mobile.md)]

Azure Resource Manager ベースの Azure PowerShell コマンドレットを使用して Web アプリを管理する方法については、 [Azure Resource Manager ベースの Azure Web アプリ用 PowerShell コマンド](app-service-web-app-azure-resource-manager-powershell.md)

## <a name="uploading-and-binding-a-new-ssl-certificate"></a>新しい SSL 証明書のアップロードとバインド
シナリオ: ユーザーは、いずれかの Web アプリに SSL 証明書をバインドしたいと考えています。

Web アプリを含むリソース グループの名前、Web アプリ名、ユーザーのコンピューターにおける証明書の .pfx ファイルのパス、証明書のパスワード、カスタム ホスト名がわかっていれば、次の PowerShell コマンドを使ってその SSL バインドを作成できます。

    New-AzureRmWebAppSSLBinding -ResourceGroupName myresourcegroup -WebAppName mytestapp -CertificateFilePath PathToPfxFile -CertificatePassword PlainTextPwd -Name www.contoso.com

Web アプリに SSL バインドを追加する前に、ホスト名 (カスタム ドメイン) を構成しておく必要があります。 ホスト名が構成されていない場合は、New-AzureRmWebAppSSLBinding の実行時に "ホスト名" が存在しないことを示すエラーが表示されます。 ホスト名は、直接ポータルから、または Azure PowerShell を使用して追加できます。 次の PowerShell スニペットでは、New-AzureRmWebAppSSLBinding を実行する前に、ホスト名を構成できます。   

    $webApp = Get-AzureRmWebApp -Name mytestapp -ResourceGroupName myresourcegroup  
    $hostNames = $webApp.HostNames  
    $HostNames.Add("www.contoso.com")  
    Set-AzureRmWebApp -Name mytestapp -ResourceGroupName myresourcegroup -HostNames $HostNames   

Set-AzureRmWebApp コマンドレットでは Web アプリのホスト名が上書きされる点を理解しておくことが重要です。 そのため、上記の PowerShell スニペットでは、Web アプリのホスト名の既存のリストに追加されます。  

## <a name="uploading-and-binding-an-existing-ssl-certificate"></a>既存の SSL 証明書のアップロードとバインド
シナリオ: ユーザーは、いずれかの Web アプリに、アップロード済みの SSL 証明書をバインドしたいと考えています。

次のコマンドを使って、特定のリソース グループにアップロードしてある証明書の一覧を取得できます。

    Get-AzureRmWebAppCertificate -ResourceGroupName myresourcegroup

証明書は、特定の場所とリソース グループに対してローカルであるため、必要な証明書とは別の場所およびリソース グループに構成済みの Web アプリがある場合は、証明書をもう一度アップロードする必要があります。 

Web アプリを含むリソース グループの名前、Web アプリ名、証明書の拇印、カスタム ホスト名がわかっていれば、次の PowerShell コマンドを使ってその SSL バインドを作成できます。

    New-AzureRmWebAppSSLBinding -ResourceGroupName myresourcegroup -WebAppName mytestapp -Thumbprint <certificate thumbprint> -Name www.contoso.com

## <a name="deleting-an-existing-ssl-binding"></a>既存の SSL バインドの削除
シナリオ: ユーザーは、既存の SSL バインドを削除したいと考えています。

Web アプリを含むリソース グループの名前、Web アプリ名、カスタム ホスト名がわかっていれば、次の PowerShell コマンドを使ってその SSL バインドを削除できます。

    Remove-AzureRmWebAppSSLBinding -ResourceGroupName myresourcegroup -WebAppName mytestapp -Name www.contoso.com

削除対象の SSL バインドがその場所でその証明書を使っている最後のバインドであった場合も、既定でその証明書は削除されますが、DeleteCertificate オプションを使って証明書を保持しておくこともできます。

    Remove-AzureRmWebAppSSLBinding -ResourceGroupName myresourcegroup -WebAppName mytestapp -Name www.contoso.com -DeleteCertificate $false

### <a name="references"></a>参照
* [Azure Resource Manager ベースの Azure Web アプリ用 PowerShell コマンド](app-service-web-app-azure-resource-manager-powershell.md)
* [App Service 環境の概要](app-service-app-service-environment-intro.md)
* [Azure リソース マネージャーでの Azure PowerShell の使用](../powershell-azure-resource-manager.md)




<!--HONumber=Nov16_HO3-->


