---
title: Azure CLI と IoT 拡張機能を使用して IoT Hub Device Provisioning Service を管理する
description: Azure CLI と IoT 拡張機能を使用して IoT Hub Device Provisioning Service を管理する方法
author: chrissie926
ms.author: menchi
ms.date: 01/17/2018
ms.topic: conceptual
ms.service: iot-dps
services: iot-dps
ms.openlocfilehash: 0e276ca32d7cd02f9668b33b3729757404b13b00
ms.sourcegitcommit: d6b68b907e5158b451239e4c09bb55eccb5fef89
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/20/2019
ms.locfileid: "74229740"
---
# <a name="how-to-use-azure-cli-and-the-iot-extension-to-manage-the-iot-hub-device-provisioning-service"></a>Azure CLI と IoT 拡張機能を使用して IoT Hub Device Provisioning Service を管理する方法

[Azure CLI](https://docs.microsoft.com/cli/azure?view=azure-cli-latest) は、IoT Edge などの Azure リソースを管理するための、オープン ソースのクロス プラットフォーム コマンド ライン ツールです。 Azure CLI は、Windows、Linux、および MacOS で使用できます。 Azure CLI を使用すると、Azure IoT Hub リソース、デバイス プロビジョニング サービス インスタンス、およびリンク済みのハブを簡単に管理することができます。

IoT 拡張機能によって、Azure CLI には、デバイス管理、完全な IoT Edge 対応などの機能が追加されました。

このチュートリアルでは、まず、Azure CLI と IoT 拡張機能のセットアップ手順を完了します。 次に、CLI コマンドで基本的なデバイス プロビジョニング サービス操作を実行する方法について説明します。 

## <a name="installation"></a>インストール 

### <a name="step-1---install-python"></a>手順 1 - Python をインストールする

[Python 2.7x または Python 3.x](https://www.python.org/downloads/) が必要です。

### <a name="step-2---install-the-azure-cli"></a>手順 2 - Azure CLI をインストールする

[インストール手順](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest)に従って、環境に Azure CLI をセットアップします。 Azure CLI のバージョンは、少なくとも 2.0.24 以降である必要があります。 検証するには、`az –version` を使用します。 このバージョンでは、az 拡張機能のコマンドがサポートされ、Knack コマンド フレームワークが導入されています。 簡単に Windows にインストールする方法の 1 つは、[MSI](https://aka.ms/InstallAzureCliWindows) をダウンロードしてインストールすることです。

### <a name="step-3---install-iot-extension"></a>手順 3 - IoT 拡張機能をインストールする

[IoT 拡張機能の readme](https://github.com/Azure/azure-iot-cli-extension) で、拡張機能をインストールするいくつかの方法が説明されています。 最も簡単な方法は、`az extension add --name azure-cli-iot-ext` を実行することです。 インストール後、`az extension list` を使用して、現在インストールされている拡張機能を確認することができます。また、`az extension show --name azure-cli-iot-ext` を使用して、IoT 拡張機能の詳細を表示することもできます。 拡張機能を削除するには、`az extension remove --name azure-cli-iot-ext` を使用します。


## <a name="basic-device-provisioning-service-operations"></a>基本的なデバイス プロビジョニング サービス操作
例では、Azure アカウントへのログイン、Azure リソース グループ (Azure ソリューションの関連リソースを保持するコンテナー) の作成、IoT Hub の作成、デバイス プロビジョニング サービスの作成、既存のデバイス プロビジョニング サービスの一覧表示、およびリンクされた IoT Hub の作成を CLI コマンドで行う方法を示しています。 

開始する前に、前に説明したインストール手順を完了してください。 まだ Azure アカウントがない場合は、今すぐ[無料の Azure アカウントを作成](https://azure.microsoft.com/free/?v=17.39a)することができます。 


### <a name="1-log-in-to-the-azure-account"></a>1.Azure アカウントにログインする
  
    az login

![login][1]

### <a name="2-create-a-resource-group-iothubblogdemo-in-eastus"></a>2.eastus で IoTHubBlogDemo リソース グループを作成する

    az group create -l eastus -n IoTHubBlogDemo

![リソース グループの作成][2]


### <a name="3-create-two-device-provisioning-services"></a>3.2 つのデバイス プロビジョニング サービスを作成する

    az iot dps create --resource-group IoTHubBlogDemo --name demodps

![デバイス プロビジョニング サービスの作成][3]

    az iot dps create --resource-group IoTHubBlogDemo --name demodps2

### <a name="4-list-all-the-existing-device-provisioning-services-under-this-resource-group"></a>4.このリソース グループ内のすべての既存のデバイス プロビジョニング サービスを一覧表示する

    az iot dps list --resource-group IoTHubBlogDemo

![デバイス プロビジョニング サービスの一覧表示][4]


### <a name="5-create-an-iot-hub-blogdemohub-under-the-newly-created-resource-group"></a>5.新しく作成されたリソース グループに、IoT Hub として blogDemoHub を作成する

    az iot hub create --name blogDemoHub --resource-group IoTHubBlogDemo

![IoT Hub を作成する][5]

### <a name="6-link-one-existing-iot-hub-to-a-device-provisioning-service"></a>6.1 つの既存の IoT Hub をデバイス プロビジョニング サービスにリンクする

    az iot dps linked-hub create --resource-group IoTHubBlogDemo --dps-name demodps --connection-string <connection string> -l westus

![Hub をリンクする][5]

<!-- Images -->
[1]: ./media/how-to-manage-dps-with-cli/login.jpg
[2]: ./media/how-to-manage-dps-with-cli/create-resource-group.jpg
[3]: ./media/how-to-manage-dps-with-cli/create-dps.jpg
[4]: ./media/how-to-manage-dps-with-cli/list-dps.jpg
[5]: ./media/how-to-manage-dps-with-cli/create-hub.jpg
[6]: ./media/how-to-manage-dps-with-cli/link-hub.jpg


## <a name="next-steps"></a>次の手順
このチュートリアルでは、以下の内容を学習しました。

> [!div class="checklist"]
> * デバイスを登録する
> * デバイスを起動する
> * デバイスが登録されていることを確認する

次のチュートリアルに進み、負荷分散されたハブに複数のデバイスをプロビジョニングする方法を学習してください。 

> [!div class="nextstepaction"]
> [負荷分散された IoT ハブにデバイスをプロビジョニングする](./tutorial-provision-multiple-hubs.md)
