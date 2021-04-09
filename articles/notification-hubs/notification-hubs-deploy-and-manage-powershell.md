---
title: PowerShell を使用した Notification Hubs のデプロイと管理
description: PowerShell を使用して Notification Hubs の作成と管理を自動化する方法
services: notification-hubs
documentationcenter: ''
author: sethmanheim
manager: femila
editor: jwargo
ms.assetid: 7c58f2c8-0399-42bc-9e1e-a7f073426451
ms.service: notification-hubs
ms.workload: mobile
ms.tgt_pltfrm: powershell
ms.devlang: na
ms.topic: article
ms.date: 01/04/2019
ms.author: sethm
ms.reviewer: jowargo
ms.lastreviewed: 01/04/2019
ms.openlocfilehash: 4534584144f54618d7f3dd39cf5e40bc0464fb21
ms.sourcegitcommit: e6de1702d3958a3bea275645eb46e4f2e0f011af
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "102454987"
---
# <a name="deploy-and-manage-notification-hubs-using-powershell"></a>PowerShell を使用した Notification Hubs のデプロイと管理

## <a name="overview"></a>概要

この記事では PowerShell を使用して Azure Notification Hubs を作成および管理する方法を説明します。 この記事では、次の一般的なオートメーション タスクが表示されます。

- 通知ハブの作成
- 資格情報の設定

通知ハブに新しい Service Bus 名前空間を作成する必要がある場合は、「[PowerShell で Service Bus を管理する](../service-bus-messaging/service-bus-manage-with-ps.md)」をご覧ください。

Notification Hubs は Azure PowerShell に含まれているコマンドレットを使用して直接管理することはできません。 PowerShell からの最良のアプローチは、Microsoft.ServiceBus.dll アセンブリを参照することです。 このアセンブリは、 [Microsoft Azure Notification Hubs NuGet パッケージ](https://www.nuget.org/packages/Microsoft.Azure.NotificationHubs/)で配布されます。

## <a name="prerequisites"></a>前提条件

- Azure サブスクリプション。 Azure はサブスクリプション方式のプラットフォームです。 サブスクリプションの入手方法の詳細については、[購入オプション]、[メンバー オファー]、または[無料試用版]に関するページをご覧ください。
- Azure PowerShell を搭載するコンピューター 手順については、 [Azure PowerShell のインストールおよび構成]を参照してください。
- PowerShell スクリプト、NuGet パッケージ、.NET Framework の一般的理解。

## <a name="including-a-reference-to-the-net-assembly-for-service-bus"></a>Service Bus 用の .NET アセンブリへの参照を含む

Azure Notification Hubs の管理はまだ Azure PowerShell の PowerShell コマンドレットに含まれていません。 通知ハブをプロビジョニングするには、 [Microsoft Azure Notification Hubs NuGet パッケージ](https://www.nuget.org/packages/Microsoft.Azure.NotificationHubs/)に用意されている .NET クライアントを使用できます。

まず、スクリプトが Visual Studio プロジェクトの NuGet パッケージとしてインストールされる **Microsoft.ServiceBus.dll** アセンブリを検出できることを確認します。 柔軟性を持たせるために、スクリプトでは次のステップを実行します。

1. 呼び出されたパスを決定します。
2. `packages` という名前のフォルダーが見つかるまでパスを走査します。 このフォルダーは Visual Studio プロジェクトで NuGet パッケージをインストールする際に作成されます。
3. `Microsoft.Azure.NotificationHubs.dll` という名前のアセンブリの `packages` フォルダーを反復的に検索します。
4. アセンブリを参照するので、タイプは後で利用できるようになります。

次は、こうした手順を PowerShell スクリプトで実装する方法を示しています。

``` powershell
try
{
    # WARNING: Make sure to reference the latest version of Microsoft.Azure.NotificationHubs.dll
    Write-Output "Adding the [Microsoft.Azure.NotificationHubs.dll] assembly to the script..."
    $scriptPath = Split-Path (Get-Variable MyInvocation -Scope 0).Value.MyCommand.Path
    $packagesFolder = (Split-Path $scriptPath -Parent) + "\packages"
    $assembly = Get-ChildItem $packagesFolder -Include "Microsoft.Azure.NotificationHubs.dll" -Recurse
    Add-Type -Path $assembly.FullName

    Write-Output "The [Microsoft.Azure.NotificationHubs.dll] assembly has been successfully added to the script."
}

catch [System.Exception]
{
    Write-Error("Could not add the Microsoft.Azure.NotificationHubs.dll assembly to the script. Make sure you build the solution before running the provisioning script.")
}
```

## <a name="create-the-namespacemanager-class"></a>`NamespaceManager` クラスの作成

Notification Hubs をプロビジョニングするには、SDK から [NamespaceManager](/dotnet/api/microsoft.servicebus.namespacemanager) クラスのインスタンスを作成します。

Azure PowerShell に付属の [Get-AzureSBAuthorizationRule] コマンドレットを使用して、接続文字列の指定に使用する承認規則を取得できます。 `NamespaceManager` インスタンスへの参照は `$NamespaceManager` 変数に保存されます。 `$NamespaceManager` を使用して通知ハブをプロビジョニングします。

``` powershell
$sbr = Get-AzureSBAuthorizationRule -Namespace $Namespace
# Create the NamespaceManager object to create the hub
Write-Output "Creating a NamespaceManager object for the [$Namespace] namespace..."
$NamespaceManager=[Microsoft.Azure.NotificationHubs.NamespaceManager]::CreateFromConnectionString($sbr.ConnectionString);
Write-Output "NamespaceManager object for the [$Namespace] namespace has been successfully created."
```

## <a name="provisioning-a-new-notification-hub"></a>新しい通知ハブのプロビジョニング

新しい通知ハブをプロビジョニングするには、 [.NET API for Notification Hubs]を使用します。

スクリプトのこの部分では 4 つのローカル変数を設定します。

1. `$Namespace`:通知ハブを作成する名前空間の名前に設定します。
2. `$Path`:このパスは新しい通知ハブの名前に設定します。  たとえば、「Myhub」のように設定します。
3. `$WnsPackageSid`:[Windows デベロッパー センター](https://developer.microsoft.com/en-us/windows)からの Windows アプリケーションのパッケージ SID に設定します。
4. `$WnsSecretkey`:[Windows デベロッパー センター](https://developer.microsoft.com/en-us/windows)からの Windows アプリケーションの秘密キーに設定します。

これらの変数は、名前空間に接続し、Windows アプリの Windows 通知サービス (WNS) 資格情報を使用して WNS 通知を処理するように構成された新しい通知ハブを作成するために使用します。 パッケージ SID と秘密キーを取得する方法については、「 [Notification Hubs の使用](notification-hubs-windows-store-dotnet-get-started-wns-push-notification.md) 」を参照してください。

- スクリプト スニペットは `NamespaceManager` オブジェクトを使用して、`$Path` によって特定された通知ハブが存在するかどうかを確認します。
- 存在しない場合は、WNS 資格情報を使用してスクリプトによって `NotificationHubDescription` が作成され、`NamespaceManager` クラスの `CreateNotificationHub` メソッドに渡されます。

``` powershell
$Namespace = "<Enter your namespace>"
$Path  = "<Enter a name for your notification hub>"
$WnsPackageSid = "<your package sid>"
$WnsSecretkey = "<enter your secret key>"

$WnsCredential = New-Object -TypeName Microsoft.Azure.NotificationHubs.WnsCredential -ArgumentList $WnsPackageSid,$WnsSecretkey

# Query the namespace
$CurrentNamespace = Get-AzureSBNamespace -Name $Namespace

# Check if the namespace already exists
if ($CurrentNamespace)
{
    Write-Output "The namespace [$Namespace] in the [$($CurrentNamespace.Region)] region was found."

    # Create the NamespaceManager object used to create a new notification hub
    $sbr = Get-AzureSBAuthorizationRule -Namespace $Namespace
    Write-Output "Creating a NamespaceManager object for the [$Namespace] namespace..."
    $NamespaceManager = [Microsoft.Azure.NotificationHubs.NamespaceManager]::CreateFromConnectionString($sbr.ConnectionString);
    Write-Output "NamespaceManager object for the [$Namespace] namespace has been successfully created."

    # Check to see if the Notification Hub already exists
    if ($NamespaceManager.NotificationHubExists($Path))
    {
        Write-Output "The [$Path] notification hub already exists in the [$Namespace] namespace."  
    }
    else
    {
        Write-Output "Creating the [$Path] notification hub in the [$Namespace] namespace."
        $NHDescription = New-Object -TypeName Microsoft.Azure.NotificationHubs.NotificationHubDescription -ArgumentList $Path;
        $NHDescription.WnsCredential = $WnsCredential;
        $NamespaceManager.CreateNotificationHub($NHDescription);
        Write-Output "The [$Path] notification hub was created in the [$Namespace] namespace."
    }
}
else
{
    Write-Host "The [$Namespace] namespace does not exist."
}
```

## <a name="additional-resources"></a>その他のリソース

- [PowerShell で Service Bus を管理する](../service-bus-messaging/service-bus-manage-with-ps.md)
- [PowerShell スクリプトを使用してService Bus キュー、トピック、サブスクリプションを作成する方法 (ブログの投稿)](/archive/blogs/paolos/how-to-create-service-bus-queues-topics-and-subscriptions-using-a-powershell-script)
- [PowerShell スクリプトを使用して Service Bus の名前空間と Event Hub を作成する方法 (ブログの投稿)](/archive/blogs/paolos/how-to-create-a-service-bus-namespace-and-an-event-hub-using-a-powershell-script)

既製のスクリプトも次のページからダウンロードできます。

- [Service Bus PowerShell Scripts](https://code.msdn.microsoft.com/windowsazure/Service-Bus-PowerShell-a46b7059)

[購入オプション]: https://azure.microsoft.com/pricing/purchase-options/
[メンバー オファー]: https://azure.microsoft.com/pricing/member-offers/
[無料試用版]: https://azure.microsoft.com/pricing/free-trial/
[Azure PowerShell のインストールおよび構成]: /powershell/azure/
[.NET API for Notification Hubs]: /dotnet/api/overview/azure/notification-hubs
[Get-AzureSBNamespace]: /powershell/module/servicemanagement/azure.service/get-azuresbnamespace
[New-AzureSBNamespace]: /powershell/module/servicemanagement/azure.service/new-azuresbnamespace
[Get-AzureSBAuthorizationRule]: /powershell/module/servicemanagement/azure.service/get-azuresbauthorizationrule
