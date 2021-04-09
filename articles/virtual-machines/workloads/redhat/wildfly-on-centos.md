---
title: クイックスタート - CentOS の WildFly
description: CentOS VM 上の WildFly に Java アプリケーションをデプロイします
author: Theresa-Nguyen
ms.author: bicnguy
ms.topic: quickstart
ms.service: virtual-machines
ms.subservice: redhat
ms.collection: linux
ms.assetid: 7aa21ef8-9cfb-43e0-bfda-3f10a2a2f3ef
ms.date: 10/23/2020
ms.openlocfilehash: 533d2881688598430ca05e964595352edf993dff
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "101675993"
---
# <a name="quickstart-wildfly-on-centos-8"></a>クイックスタート: CentOS 8 上の WildFly

このクイックスタートでは、CentOS 8 VM に存在するスタンドアロンの WildFly ノードをデプロイする方法を紹介します。 これは、Azure 上でエンタープライズ Java アプリケーションの開発とテストをするうえで最適です。 このクイックスタートをデプロイする際に、アプリケーション サーバー サブスクリプションは必要ありません。

## <a name="prerequisites"></a>前提条件

* アクティブなサブスクリプションが含まれる Azure アカウント。 Azure サブスクリプションをお持ちでない場合は、[MSDN サブスクライバーの特典](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details)を有効にするか、[無料アカウントを作成](https://azure.microsoft.com/pricing/free-trial)してください。

## <a name="use-case"></a>使用事例

WildFly は、Azure 上でエンタープライズ Java アプリケーションの開発とテストをするうえで最適です。 WildFly 18 のサーバー構成プロファイルで利用できるテクノロジの一覧は、[WildFly の入門ガイド](https://docs.wildfly.org/18/Getting_Started_Guide.html#getting-started-with-wildfly)を参照してください。

WildFly は、ユース ケースに応じてスタンドアロン モードまたはクラスター モードを利用できます。 複数のノードから成るクラスタで WildFly を使うと、重要な Jakarta EE アプリケーションに高可用性を確保できます。 アプリケーションの構成をわずかに変更して、クラスターにアプリケーションをデプロイするだけです。 詳細については、[WildFly の高可用性ガイド](https://docs.wildfly.org/18/High_Availability_Guide.html)を参照してください。

## <a name="configuration-choice"></a>構成の選択肢

WildFly は、**スタンドアロン サーバー** モードで起動することができます。このモードでは、スタンドアロン サーバーのインスタンスが、JBoss Application Server (AS) 3、4、5、または 6 のインスタンスと同じく、独立したプロセスとなります。 スタンドアロンのインスタンスを起動できるスクリプトとしては、standalone.sh と standalone.bat の 2 つがあります。 スタンドアロンのインスタンスが複数存在する場合には、マルチサーバーの管理をユーザーの責任で調整する必要があります。

構成フォルダーにある構成ファイルを使うと、別の構成で WildFly インスタンスを起動することもできます。

スタンドアロン サーバーの構成ファイルには次のものがあります。

- standalone.xml (既定) - この構成が、WildFly インスタンスの起動に既定で使用されるファイルです。 これは Jakarta Web Profile 互換の認定を受けた構成であり、必要なテクノロジを備えています。
   
- standalone-ha.xml - Jakarta EE Web Profile 8 互換の認定を受けた構成であり、高可用性を確保します (Web アプリケーション向け)。
   
- standalone-full.xml - Jakarta EE アプリケーションをホストすることを目的とし、Jakarta EE Platform 8 互換の認定を受けた構成で、必要なテクノロジがすべて揃っています。

- standalone-full-ha.xml - Jakarta EE アプリケーションをホストすることを目的とし、Jakarta EE Platform 8 互換の認定を受けた構成で、高可用性を実現します。

あらかじめ用意されている別の構成でスタンドアロンの WildFly サーバーを起動する場合には、サーバー構成ファイルに引数 --server-config を使用します。

たとえば、クラスタリング機能を備えた Jakarta EE Platform 8 を使用するには、次のコマンドを使用します。

```
./standalone.sh --server-config=standalone-full-ha.xml
```

各種の構成の詳細については、[WildFly の入門ガイド](https://docs.wildfly.org/18/Getting_Started_Guide.html#wildfly-10-configurations)を参照してください。

## <a name="licensing-support-and-subscription-notes"></a>ライセンス、サポート、サブスクリプションに関する注記

Azure CentOS 8 イメージは従量課金制 (PAYG) の VM イメージであり、ユーザーがライセンスを取得する必要はありません。 VM を初めて起動したときに、VM の OS のライセンス認証が自動的に実行され、その後は 1 時間単位で料金が課金されます。 これは、Microsoft の Linux VM について 1 時間あたりに発生する料金に追加する形で課金されます。 詳細については、[Linux VM の価格](https://azure.microsoft.com/pricing/details/virtual-machines/linux/#linux)に関するページを参照してください。 WildFly はダウンロード、使用とも無料であり、Red Hat のサブスクリプションやライセンスも必要ありません。

## <a name="how-to-consume"></a>使用方法

テンプレートのデプロイ方法は次の 3 種類があります。

- PowerShell を使う - 次のコマンドを実行してテンプレートをデプロイします (Azure PowerShell のインストールと構成に関する詳細については、[Azure PowerShell](/powershell/azure/) に関するページを参照してください)。

    ```
    New-AzResourceGroup -Name <resource-group-name> -Location <resource-group-location> #use this command when you need to create a new Resource Group for your deployment
    ```

    ```
    New-AzResourceGroupDeployment -ResourceGroupName <resource-group-name> -TemplateUri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/wildfly-standalone-centos8/azuredeploy.json
    ```
    
- Azure CLI を使う - 次のコマンドを実行してテンプレートをデプロイします (Azure クロスプラットフォーム コマンドライン インターフェイスのインストールと構成に関する詳細については、[Azure クロスプラットフォーム コマンドライン](/cli/azure/install-azure-cli)に関するページを参照してください)。

    ```
    az group create --name <resource-group-name> --location <resource-group-location> #use this command when you need to create a new Resource Group for your deployment
    ```

    ```
    az deployment group create --resource-group <my-resource-group> --template-uri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/wildfly-standalone-centos8/azuredeploy.json
    ```

- Azure portal を使う - <a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fwildfly-standalone-centos8%2Fazuredeploy.json" target="_blank">ここ</a>をクリックして Azure portal にログインし、テンプレートをデプロイします。

## <a name="arm-template"></a>ARM テンプレート

<a href="https://github.com/Azure/azure-quickstart-templates/tree/master/wildfly-standalone-centos8" target="_blank"> WildFly 18 on CentOS 8 (stand-alone VM)</a> - リソース グループ (RG) 内に CentOS 8 VM 上の WildFly 18.0.1.Final のスタンドアロン ノードを作成するためのクイックスタート テンプレートです。VM のプライベート IP、仮想ネットワーク、および診断ストレージ アカウントが含まれています。 また、WildFly 上に JBoss-EAP on Azure という名前のサンプル Java アプリケーションもデプロイされます。

## <a name="resource-links"></a>リソース リンク

* [WildFly 18](https://docs.wildfly.org/18/) の詳細
* [Azure 上の Linux ディストリビューション](../../linux/endorsed-distros.md)の詳細
* [Java 開発者向け Azure ドキュメント](https://github.com/JasonFreeberg/jboss-on-app-service)

## <a name="next-steps"></a>次のステップ

運用環境に備えて、Red Hat JBoss EAP の Azure クイックスタート ARM テンプレートを確認してください。

サンプル アプリケーションを備えたスタンドアロンの RHEL 仮想マシン

*  <a href="https://github.com/Azure/azure-quickstart-templates/tree/master/jboss-eap-standalone-rhel" target="_blank"> JBoss EAP on RHEL (stand-alone VM)</a>

サンプル アプリケーションを備え、クラスター化された RHEL 仮想マシン

* <a href="https://github.com/Azure/azure-quickstart-templates/tree/master/jboss-eap-clustered-multivm-rhel" target="_blank"> JBoss EAP on RHEL (clustered, multi-VM)</a>

サンプル アプリケーションを備え、クラスター化された RHEL 仮想マシン スケール セット

* <a href="https://github.com/Azure/azure-quickstart-templates/tree/master/jboss-eap-clustered-vmss-rhel" target="_blank"> JBoss EAP on RHEL (clustered, VMSS)</a>