---
title: Azure Image Builder サービスのネットワーク オプション
description: Azure VM Image Builder サービスをデプロイするときのネットワーク オプションについて理解します
author: danielsollondon
ms.author: danis
ms.date: 08/10/2020
ms.topic: article
ms.service: virtual-machines
ms.subservice: image-builder
ms.collection: linux
ms.openlocfilehash: cd5027ca6e0ce3dc02da14b7dd6afd6e00e3f92d
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "101669437"
---
# <a name="azure-image-builder-service-networking-options"></a>Azure Image Builder サービスのネットワーク オプション

既存の VNET を使用するかどうかにかかわらず、Azure Image Builder をデプロイすることを選択する必要があります。

## <a name="deploy-without-specifying-an-existing-vnet"></a>既存の VNET を指定せずにデプロイする

既存の VNET を指定しない場合は、Azure Image Builder によってステージング リソース グループに VNET とサブネットが作成されます。 Azure Image Builder サービスへの受信トラフィックを制限するために、パブリック IP リソースがネットワーク セキュリティ グループと共に使用されます。 パブリック IP によって、イメージのビルド時に Azure Image Builder コマンドのチャネルが円滑化されます。 ビルドが完了すると、VM、パブリック IP、ディスク、および VNET は削除されます。 このオプションを使用する場合は、VNET のプロパティを指定しないでください。

## <a name="deploy-using-an-existing-vnet"></a>既存の VNET を使用してデプロイする

VNET とサブネットを指定した場合は、Azure Image Builder によって、選択した VNET にビルド VM がデプロイされます。 VNET 上でアクセス可能なリソースにアクセスできます。 また、他のどの VNET にも接続されていない、サイロ化された VNET を作成することもできます。 VNET を指定した場合、Azure Image Builder ではパブリック IP アドレスは使用されません。 Azure Image Builder サービスからビルド VM への通信には、Azure Private Link テクノロジが使用されます。

詳細については、次のいずれかの例を参照してください。

* [Windows VM の Azure Image Builder を使用して既存の Azure VNET へのアクセスを許可する](../windows/image-builder-vnet.md)
* [Linux VM の Azure Image Builder を使用して既存の Azure VNET へのアクセスを許可する](image-builder-vnet.md)

### <a name="what-is-azure-private-link"></a>Azure Private Link とは

Azure Private Link を使用すると、仮想ネットワークから Azure のサービスとしてのプラットフォーム (PaaS)、顧客所有、または Microsoft パートナーの各サービスへのプライベート接続が可能になります。 ネットワーク アーキテクチャが簡素化され、パブリック インターネットへのデータの公開をなくすことで Azure でのエンドポイント間の接続がセキュリティで保護されます。 詳細については、「[Private Link のドキュメント](../../private-link/index.yml)」をご覧ください。

### <a name="required-permissions-for-an-existing-vnet"></a>既存の VNET に必要なアクセス許可

Azure Image Builder には、既存の VNET を使用するための特定のアクセス許可が必要です。 詳細については、「[Azure CLI を使用して Azure Image Builder サービスのアクセス許可を構成する](image-builder-permissions-cli.md)」または「[PowerShell を使用して Azure Image Builder サービスのアクセス許可を構成する](image-builder-permissions-powershell.md)」をご覧ください。

### <a name="what-is-deployed-during-an-image-build"></a>イメージのビルド時のデプロイ対象

既存の VNET を使用することは、Azure Image Builder によって、追加の VM (プロキシ VM) と、プライベート リンクに接続されている Azure Load Balancer (ALB) がデプロイされることを意味します。 AIB サービスからのトラフィックは、プライベート リンクを経由して ALB に送信されます。 ALB は、ポート 60001 (Linux OS の場合) または ポート 60000 (Windows OS の場合) を使用してプロキシ VM と通信します。 このプロキシは、ポート 22 (Linux OS の場合) またはポート 5986 (Windows OS の場合) を使用してビルド VM にコマンドを送信します。

> [!NOTE]
> VNET は、Azure Image Builder サービスのリージョンと同じリージョンにある必要があります。
> 

### <a name="why-deploy-a-proxy-vm"></a>プロキシ VM をデプロイする理由

パブリック IP のない VM が内部ロード バランサーの内側にある場合、その VM はインターネットにアクセスできません。 VNET に使用される Azure Load Balancer は内部のものです。 プロキシ VM では、ビルド時にビルド VM のインターネット アクセスを許可します。 関連付けられているネットワーク セキュリティ グループを使用すると、ビルド VM のアクセスを制限できます。

デプロイされたプロキシ VM のサイズは、Standard A1_v2 にビルド VM を加えたものになります。 プロキシ VM は、Azure Image Builder サービスとビルド VM の間でコマンドを送信するために使用されます。 プロキシ VM のプロパティは、サイズや OS を含めて変更できません。 Windows と Linux のどちらのイメージのビルドでも、プロキシ VM のプロパティを変更することはできません。

### <a name="image-template-parameters-to-support-vnet"></a>VNET をサポートするためのイメージ テンプレートのパラメーター
```json
"VirtualNetworkConfig": {
        "name": "",
        "subnetName": "",
        "resourceGroupName": ""
        },
```

| 設定 | 説明 |
|---------|---------|
| name | (省略可能) 既存の仮想ネットワークの名前。 |
| subnetName | 指定された仮想ネットワーク内のサブネットの名前。 *name* が指定されている場合にのみ指定する必要があります。 |
| resourceGroupName | 指定された仮想ネットワークを含むリソース グループの名前。 *name* が指定されている場合にのみ指定する必要があります。 |

Private Link サービスには、指定された VNET とサブネットから取得された IP が必要です。 Azure では現在、これらの IP 上でのネットワーク ポリシーはサポートされていません。 そのため、サブネットでネットワーク ポリシーを無効にする必要があります。 詳細については、「[Private Link のドキュメント](../../private-link/index.yml)」をご覧ください。

### <a name="checklist-for-using-your-vnet"></a>VNET を使用するためのチェックリスト

1. Azure Load Balancer (ALB) が NSG 内のプロキシ VM と通信できるようにします
    * [AZ CLI の例](image-builder-vnet.md#add-network-security-group-rule)
    * [PowerShell の例](../windows/image-builder-vnet.md#add-network-security-group-rule)
2. サブネットでプライベート サービス ポリシーを無効にします
    * [AZ CLI の例](image-builder-vnet.md#disable-private-service-policy-on-subnet)
    * [PowerShell の例](../windows/image-builder-vnet.md#disable-private-service-policy-on-subnet)
3. Azure Image Builder で ALB を作成したり、VNET に VM を追加したりできるようにします
    * [AZ CLI の例](image-builder-permissions-cli.md#existing-vnet-azure-role-example)
    * [PowerShell の例](image-builder-permissions-powershell.md#permission-to-customize-images-on-your-vnets)
4. Azure Image Builder でソース イメージの読み取り/書き込みを実行したり、イメージを作成したりできるようにします
    * [AZ CLI の例](image-builder-permissions-cli.md#custom-image-azure-role-example)
    * [PowerShell の例](image-builder-permissions-powershell.md#custom-image-azure-role-example)
5. Azure Image Builder サービスのリージョンと同じリージョンで VNET を使用していることを確認します


## <a name="next-steps"></a>次の手順

詳細については、[Azure Image Builder の概要](../image-builder-overview.md)に関する記事を参照してください。