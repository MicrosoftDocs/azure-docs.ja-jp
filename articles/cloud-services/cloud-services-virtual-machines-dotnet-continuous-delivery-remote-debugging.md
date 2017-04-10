---
title: "継続的な配信でのリモート デバッグを有効にする | Microsoft Docs"
description: "継続的な配信を使用した Azure へのデプロイ時にリモート デバッグを有効にする方法を説明します。"
services: cloud-services
documentationcenter: .net
author: TomArcher
manager: douge
editor: 
ms.assetid: 7d423639-3b2f-4ca5-ac5a-9ac19a217c29
ms.service: cloud-services
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-multiple
ms.devlang: dotnet
ms.topic: article
ms.date: 11/18/2016
ms.author: tarcher
translationtype: Human Translation
ms.sourcegitcommit: 4f2230ea0cc5b3e258a1a26a39e99433b04ffe18
ms.openlocfilehash: d011223dbb1ad7b5752875e94a4822be1b6cbe6e
ms.lasthandoff: 03/25/2017


---
# <a name="enable-remote-debugging-when-using-continuous-delivery-to-publish-to-azure"></a>継続的な配信を使用して Azure に発行する場合にリモート デバッグを有効にする
次の手順に従って、 [継続的な配信](cloud-services-dotnet-continuous-delivery.md) を使用して Azure に発行する場合に、クラウド サービスまたは仮想マシンに対して、Azure でリモート デバッグを有効にすることができます。

## <a name="enabling-remote-debugging-for-cloud-services"></a>クラウド サービス用にリモート デバッグを有効にする
1. 「 [Azure のコマンド ライン ビルド](http://msdn.microsoft.com/library/hh535755.aspx)」の説明に従って、ビルド エージェントで Azure の初期環境を設定します。
2. リモート デバッグ ランタイム (msvsmon.exe) がパッケージに必要であるため、**Remote Tools for Visual Studio** をインストールします。

    * [Remote Tools for Visual Studio 2017](https://go.microsoft.com/fwlink/?LinkId=746570)
    * [Remote Tools for Visual Studio 2015](https://go.microsoft.com/fwlink/?LinkId=615470)
    * [Remote Tools for Visual Studio 2013 Update 5](https://www.microsoft.com/download/details.aspx?id=48156)
    
    代替方法として、Visual Studio がインストールされているシステムからリモート デバッグ バイナリをコピーすることもできます。

3. 「 [Azure Cloud Services の証明書の概要](cloud-services-certs-create.md)」の説明に従って、証明書を作成します。 .pfx および RDP 証明書の拇印を保持し、その証明書をターゲット クラウド サービスにアップロードします。
4. MSBuild コマンド ラインで次のオプションを使用し、リモート デバッグを有効にしてビルドおよびパッケージ化します(山かっこの項目については、システムおよびプロジェクト ファイルの実際のパスに置き換えてください。 )
   
        msbuild /TARGET:PUBLISH /PROPERTY:Configuration=Debug;EnableRemoteDebugger=true;VSX64RemoteDebuggerPath="<remote tools path>";RemoteDebuggerConnectorCertificateThumbprint="<thumbprint of the certificate added to the cloud service>";RemoteDebuggerConnectorVersion="2.7" "<path to your VS solution file>"
   
    `VSX64RemoteDebuggerPath` は、msvsmon.exe を含む Remote Tools for Visual Studio のフォルダーへのパスです。
    `RemoteDebuggerConnectorVersion` は、クラウド サービスにおける Azure SDK のバージョンです。 また、Visual Studio にインストールされているバージョンとも一致している必要があります。
5. 前の手順で生成されたパッケージと .cscfg ファイルを使用してターゲット クラウド サービスに発行します。
6. Visual Studio と Azure SDK for .NET がインストールされているマシンに証明書 (.pfx ファイル) をインポートします。 必ず `CurrentUser\My` 証明書ストアにインポートしてください。そうしないと、Visual Studio でデバッガーにアタッチする際にエラーが発生します。

## <a name="enabling-remote-debugging-for-virtual-machines"></a>仮想マシン用にリモート デバッグを有効にする
1. Azure 仮想マシンを作成します。 [Windows Server を実行する仮想マシンの作成](../virtual-machines/virtual-machines-windows-hero-tutorial.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)に関する記事または [Visual Studio での Azure 仮想マシンの作成と管理](../virtual-machines/windows/classic/manage-visual-studio.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json)に関する記事を参照してください。
2. [Azure クラシック ポータル ページ](http://go.microsoft.com/fwlink/p/?LinkID=269851)で、仮想マシンのダッシュボードを表示し、仮想マシンの **RDP 証明書の拇印**を確認します。 この値は、拡張機能構成の `ServerThumbprint` の値に使用されます。
3. 「 [Azure Cloud Services の証明書の概要](cloud-services-certs-create.md) 」の説明に従って、クライアント証明書を作成します (.pfx および RDP 証明書の拇印を保持)。
4. 「 [Azure PowerShell のインストールおよび構成方法](/powershell/azureps-cmdlets-docs)」の説明に従って、Azure PowerShell (バージョン 0.7.4 以降) をインストールします。
5. 次のスクリプトを実行して RemoteDebug 拡張機能を有効にします。 パスおよび個人用データを自分自身のサブスクリプション名、サービス名、拇印などで置き換えます。
   
   > [!NOTE]
   > このスクリプトは Visual Studio 2015 用に構成されています。 Visual Studio 2013 または Visual Studio 2017 を使用している場合は、次の `$referenceName` および `$extensionName` 割り当てを `RemoteDebugVS2013` または `RemoteDebugVS2017` に変更します。

    ```powershell   
    Add-AzureAccount

    Select-AzureSubscription "My Microsoft Subscription"

    $vm = Get-AzureVM -ServiceName "mytestvm1" -Name "mytestvm1"

    $endpoints = @(
                    ,@{Name="RDConnVS2013"; PublicPort=30400; PrivatePort=30398}
                    ,@{Name="RDFwdrVS2013"; PublicPort=31400; PrivatePort=31398}
                )

    foreach($endpoint in $endpoints)
    {
        Add-AzureEndpoint -VM $vm -Name $endpoint.Name -Protocol tcp -PublicPort $endpoint.PublicPort -LocalPort $endpoint.PrivatePort
    }

    $referenceName = "Microsoft.VisualStudio.WindowsAzure.RemoteDebug.RemoteDebugVS2015"
    $publisher = "Microsoft.VisualStudio.WindowsAzure.RemoteDebug"
    $extensionName = "RemoteDebugVS2015"
    $version = "1.*"
    $publicConfiguration = "<PublicConfig><Connector.Enabled>true</Connector.Enabled><ClientThumbprint>56D7D1B25B472268E332F7FC0C87286458BFB6B2</ClientThumbprint><ServerThumbprint>E7DCB00CB916C468CC3228261D6E4EE45C8ED3C6</ServerThumbprint><ConnectorPort>30398</ConnectorPort><ForwarderPort>31398</ForwarderPort></PublicConfig>"

    $vm | Set-AzureVMExtension -ReferenceName $referenceName -Publisher $publisher -ExtensionName $extensionName -Version $version -PublicConfiguration $publicConfiguration

    foreach($extension in $vm.VM.ResourceExtensionReferences)
    {
        if(($extension.ReferenceName -eq $referenceName) `
        -and ($extension.Publisher -eq $publisher) `
        -and ($extension.Name -eq $extensionName) `
        -and ($extension.Version -eq $version))
        {
            $extension.ResourceExtensionParameterValues[0].Key = 'config.txt'
            break
        }
    }

    $vm | Update-AzureVM
    ```

6. Visual Studio と Azure SDK for .NET がインストールされているマシンに証明書 (.pfx) をインポートします。


