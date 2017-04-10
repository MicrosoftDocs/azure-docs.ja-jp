---
title: "RemoteApp ハイブリッド コレクション作成のトラブルシューティング | Microsoft Docs"
description: "RemoteApp のハイブリッド コレクション作成でエラーが発生したときのトラブルシューティング方法について説明します。"
services: remoteapp
documentationcenter: 
author: vkbucha
manager: mbaldwin
ms.assetid: b32033ee-8d52-4e74-bb78-86ca873c34e2
ms.service: remoteapp
ms.workload: compute
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/23/2016
ms.author: mbaldwin
translationtype: Human Translation
ms.sourcegitcommit: e4d94d3f9736378d93e93be6645ed04ade763ca3
ms.openlocfilehash: 537fbe37b0ee21cb5d2c9f1ba437ac178850fee2


---
# <a name="troubleshoot-creating-azure-remoteapp-hybrid-collections"></a>Azure RemoteApp ハイブリッド コレクション作成のトラブルシューティング
> [!IMPORTANT]
> Azure RemoteApp の提供は終了しました。 詳細については、 [お知らせ](https://go.microsoft.com/fwlink/?linkid=821148) をご覧ください。
> 
> 

ハイブリッド コレクション では、Azure クラウドにホストされ、データはクラウドに格納されますが、ユーザーはローカル ネットワークに格納されたデータとリソースにアクセスすることもできます。 ユーザーは、Azure Active Directory と同期また連携した企業資格情報でログインしてアプリにアクセスできます。 既存の Azure Virtual Network を使用するハイブリッド コレクションをデプロイすることも、新しい仮想ネットワークを作成することもできます。 今後、予測される Azure RemoteApp の成長に十分な CIDR 範囲で仮想ネットワーク サブネットを作成または使用することが推奨されます。

コレクションをまだ作成していませんか。 手順については、「 [ハイブリッド コレクションの作成](remoteapp-create-hybrid-deployment.md) 」を参照してください。

コレクションの作成で問題が発生した場合、あるいはコレクションが予想どおりに動作しない場合、次の情報を確認してください。

## <a name="your-image-is-invalid"></a>イメージが無効です
Azure がコレクションにプロビジョニングするのを待っているとき、「GoldImageInvalid」のようなメッセージが表示された場合、テンプレート イメージが [定義されているイメージ要件](remoteapp-imagereqs.md)に一致していません。 その場合、 [要件](remoteapp-imagereqs.md)を読み、イメージを修正し、コレクションをもう一度作成します。

## <a name="does-your-vnet-have-network-security-groups-defined"></a>VNET にはネットワーク セキュリティ グループが定義されていますか。
コレクションに使用しているサブネットでネットワーク セキュリティ グループを定義している場合、サブネット内からこれらの [URL とポート](remoteapp-ports.md) にアクセスできることを確認します。

厳密な制御のために、サブネットに配置した VM にネットワーク セキュリティ グループを追加できます。

## <a name="are-you-using-your-own-dns-servers-and-are-they-accessible-from-your-vnet-subnet"></a>独自の DNS サーバーを使用していますか。 そのサーバーには VNET サブネットからアクセスできますか。
> [!NOTE]
> VNET の DNS サーバーが常に稼動していること、そして VNET でホストされている仮想マシンを常に解決できることを、確認する必要があります。 Google DNS をこれに使用しないでください。
> 
> 

ハイブリッド コレクションには、独自の DNS サーバーを使用します。 それは仮想ネットワークの構築時にネットワーク構成スキーマに指定するか、管理ポータルで指定します。 DNS サーバーは (ラウンド ロビン方式ではなく) フェールオーバー方式で指定されている順序で使用されます。  
「 [VM とロール インスタンスの名前解決](../virtual-network/virtual-networks-name-resolution-for-vms-and-role-instances.md) 」を参考にして、DNS サーバーが正しく構成されていることを確認してください。

コレクションの DNS サーバーにはそのコレクションに指定した VNET サブネットからアクセスできて利用できることを確認します。

次に例を示します。

    <VirtualNetworkConfiguration>
    <Dns>
      <DnsServers>
        <DnsServer name="" IPAddress=""/>
      </DnsServers>
    </Dns>
    </VirtualNetworkConfiguration>

![DNS を定義する](./media/remoteapp-hybridtrouble/dnsvpn.png)

## <a name="are-you-using-an-active-directory-domain-controller-in-your-collection"></a>コレクションで Active Directory ドメイン コント ローラーを使用していますか。
現在のところ、1 つの Active Directory ドメインだけを Azure RemoteApp に関連付けることができます。 ハイブリッド コレクションは、Windows Server Active Directory のデプロイから DirSync ツールを使用して同期されている Azure Active Directory アカウントのみをサポートします。具体的には、パスワード同期オプションで同期されているか、または Active Directory フェデレーション サービス (AD FS) の構成されたフェデレーションのいずれかで同期されます。 オンプレミスのドメインの UPN ドメイン サフィックスと一致するカスタム ドメインを作成し、ディレクトリ統合を設定する必要があります。

詳細については、「 [Azure RemoteApp の Active Directory を構成する](remoteapp-ad.md) 」を参照してください。

入力したドメイン詳細が有効であることと、Azure Remote App に使用されるサブネットで作成された VM からドメイン コントローラーに到達できることを確認します。 また、入力したサービス アカウント資格情報で指定ドメインにコンピューターを追加できることと、指定の AD 名を VNET の指定 DNS から解決できることを確認します。

## <a name="what-domain-name-did-you-specify-when-you-created-your-collection"></a>コレクションを作成したとき、どのようなドメイン名を指定しましたか。
作成または追加したドメイン名は (Azure AD ドメイン名ではなく) 内部ドメイン名であり、解決可能な DNS 形式 (contoso.local) になっている必要があります。 たとえば、Active Directory 内部名 (contoso.local) と Active Directory UPN (contoso.com) が与えられている場合、コレクションの作成時にその内部名を使用する必要があります。




<!--HONumber=Dec16_HO2-->


