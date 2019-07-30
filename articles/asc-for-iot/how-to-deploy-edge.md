---
title: Azure Security Center for IoT Edge モジュールをデプロイする | Microsoft Docs
description: IoT Edge に Azure Security Center for IoT セキュリティ エージェントをデプロイする方法について説明します。
services: asc-for-iot
ms.service: asc-for-iot
documentationcenter: na
author: mlottner
manager: rkarlin
editor: ''
ms.assetid: 32a9564d-16fd-4b0d-9618-7d78d614ce76
ms.subservice: asc-for-iot
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 04/1/2019
ms.author: mlottner
ms.openlocfilehash: 49ed4c6515f8fb63c3331b05e1bb29b13985e6b3
ms.sourcegitcommit: 6a42dd4b746f3e6de69f7ad0107cc7ad654e39ae
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/07/2019
ms.locfileid: "67618320"
---
# <a name="deploy-a-security-module-on-your-iot-edge-device"></a>IoT Edge デバイスにセキュリティ モジュールをデプロイする

> [!IMPORTANT]
> Azure Security Center for IoT は現在、パブリック プレビュー段階です。
> このプレビュー バージョンはサービス レベル アグリーメントなしで提供されています。運用環境のワークロードに使用することはお勧めできません。 特定の機能はサポート対象ではなく、機能が制限されることがあります。 詳しくは、[Microsoft Azure プレビューの追加使用条件](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)に関するページをご覧ください。

**Azure Security Center (ASC) for IoT** モジュールは、IoT Edge デバイスの包括的なセキュリティ ソリューションを提供します。
セキュリティ モジュールは、オペレーティング システムおよびコンテナー システムから未加工のセキュリティ データを収集、集約して分析し、実践的なセキュリティ推奨事項とアラートに変換します。
詳細については、[IoT Edge 用のセキュリティ モジュール](security-edge-architecture.md)に関する記事を参照してください。

このガイドでは、IoT Edge デバイスにセキュリティ モジュールをデプロイする方法について説明します。

## <a name="deploy-security-module"></a>セキュリティ モジュールをデプロイする

次の手順を使用して、IoT Edge 用の ASC for IoT セキュリティ モジュールをデプロイします。

### <a name="prerequisites"></a>前提条件

- IoT Hub で、ご使用のデバイスが [IoT Edge デバイスとして登録されている](https://docs.microsoft.com/azure/iot-edge/how-to-register-device-portal)ことを確認してください。

- ASC for IoT Edge モジュールでは、[AuditD フレームワーク](https://linux.die.net/man/8/auditd)が IoT Edge デバイスにインストールされている必要があります。

    - IoT Edge デバイスで次のコマンドを実行して、そのフレームワークをインストールします。
   
      `sudo apt-get install auditd audispd-plugins`
   
    - 次のコマンドを実行して、AuditD がアクティブであることを確認します。
   
      `sudo systemctl status auditd`
      
        予期される応答は `active (running)` です。 

### <a name="deployment-using-azure-portal"></a>Azure portal を使用したデプロイ

1. Azure portal から **[Marketplace]** を開きます。

1. **[モ ノのインターネット]** を選択し、 **[Azure Security Center for IoT]** を検索して選択します。

   ![Azure Security Center for IoT を選択します](media/howto/edge-onboarding-8.png)

1. **[作成]** をクリックして、デプロイを構成します。 

1. ご使用の IoT Hub のAzure **サブスクリプション**を選択してから、ご使用の **IoT Hub** を選択します。<br>単一のデバイスをターゲットにするには **[デバイスへのデプロイ]** を選択し、複数のデバイスをターゲットにするには **[大規模にデプロイする]** を選択します。その後、 **[作成]** をクリックします。 大規模デプロイの詳細については、[デプロイ方法](https://docs.microsoft.com/azure/iot-edge/how-to-deploy-monitor)に関する記事を参照してください。 

    >[!Note] 
    >**[大規模にデプロイする]** を選択した場合は、以下の手順にある **[モジュールの追加]** タブに進む前にデバイス名と詳細を追加してください。     

Azure Security Center for IoT 用の IoT Edge デプロイを作成するには、3 つの手順があります。 次のセクションで、手順ごとに説明します。 

#### <a name="step-1-add-modules"></a>手順 1:モジュールを追加する

1. **[モジュールの追加]** タブの **[デプロイ モジュール]** 領域で、 **[AzureSecurityCenterforIoT]** をクリックします。 
   
1. **名前**を **azureiotsecurity** に変更します。
1. **[イメージの URI]** を **mcr.microsoft.com/ascforiot/azureiotsecurity:0.0.3** に変更します。
1. **[コンテナーの作成オプション]** の値が次のように設定されていることを確認します。      
    ``` json
    {
        "NetworkingConfig": {
            "EndpointsConfig": {
                "host": {}
            }
        },
        "HostConfig": {
            "Privileged": true,
            "NetworkMode": "host",
            "PidMode": "host",
            "Binds": [
                "/:/host"
            ]
        }
    }    
    ```
1. **[モジュール ツインの必要なプロパティの設定]** が選択されていることを確認し、構成オブジェクトを次のように変更します。
      
    ``` json
      "properties.desired": {
        "azureiot*com^securityAgentConfiguration^1*0*0": {
        }
      }
      ```

1. **[Save]** をクリックします。
1. タブの一番下までスクロールし、 **[Edge ランタイムの詳細設定を構成する]** を選択します。
   
   >[!Note]
   > IoT Edge ハブの AMQP 通信は**無効にしない**でください。
   > Azure Security Center for IoT モジュールには、IoT Edge ハブとの AMQP 通信が必要です。
   
1. **[Edge ハブ]** の下の **[イメージ]** を **mcr.microsoft.com/ascforiot/edgehub:1.0.9-preview** に変更します。

   >[!Note]
   > Azure Security Center for IoT モジュールでは、SDK バージョン 1.20 に基づき、フォークしたバージョンの IoT Edge ハブが必要です。
   > IoT Edge ハブのイメージを変更することで、IoT Edge デバイスでは、最新の安定版リリースを、フォークしたバージョンの IoT Edge ハブに置き換えることになります。フォークしたバージョンは、IoT Edge サービスで正式にサポートされていません。

1. **[作成オプション]** が次のように設定されていることを確認します。 
         
    ``` json
    {
      "HostConfig": {
        "PortBindings": {
          "8883/tcp": [{"HostPort": "8883"}],
          "443/tcp": [{"HostPort": "443"}],
          "5671/tcp": [{"HostPort": "5671"}]
        }
      }
    }
    ```
      
1. **[Save]** をクリックします。
   
1. **[次へ]** をクリックします。

#### <a name="step-2-specify-routes"></a>手順 2:ルートを指定する 

1. **[ルートの指定]** タブで、**ASCForIoTToIoTHub** ルートを **"FROM /messages/modules/azureiotsecurity/\* INTO $upstream"** に設定し、 **[次へ]** をクリックします。

   ![ルートを指定する](media/howto/edge-onboarding-9.png)

#### <a name="step-3-review-deployment"></a>手順 3:デプロイを確認する

1. **[デプロイの確認]** タブでデプロイ情報を確認し、 **[送信]** を選択してデプロイを完了します。

## <a name="diagnostic-steps"></a>診断手順

問題が発生した場合に IoT Edge セキュリティ モジュール デバイスの状態を知るには、コンテナー ログを使用するのが最適です。 情報を収集するには、このセクションのコマンドとツールを使います。

### <a name="verify-the-required-containers-are-installed-and-functioning-as-expected"></a>必要なコンテナーがインストールされており、期待どおりに機能していることを確認する

1. IoT Edge デバイスで次のコマンドを実行します。
    
     `sudo docker ps`
   
1. 次のコンテナーが実行中であることを確認します。
   
   | Name | イメージ |
   | --- | --- |
   | azureiotsecurity | mcr.microsoft.com/ascforiot/azureiotsecurity:0.0.3 |
   | edgeHub | mcr.microsoft.com/ascforiot/edgehub:1.0.9-preview |
   | edgeAgent | mcr.microsoft.com/azureiotedge-agent:1.0 |
   
   最低限必要なコンテナーが存在しない場合は、IoT Edge のデプロイ マニフェストが推奨設定と一致しているかどうかを確認してください。 詳細については、[IoT Edge モジュールのデプロイ](#deployment-using-azure-portal)に関する記事を参照してください。

### <a name="inspect-the-module-logs-for-errors"></a>モジュール ログでエラーを調べる
   
1. IoT Edge デバイスで次のコマンドを実行します。

   `sudo docker logs azureiotsecurity`
   
1. より詳細なログについては、環境変数 `logLevel=Debug` を **azureiotsecurity** モジュール デプロイに追加してください。

## <a name="next-steps"></a>次の手順

構成オプションの詳細については、モジュール構成のハウツー ガイドに進んでください。 
> [!div class="nextstepaction"]
> [モジュール構成のハウツー ガイド](./how-to-agent-configuration.md)
