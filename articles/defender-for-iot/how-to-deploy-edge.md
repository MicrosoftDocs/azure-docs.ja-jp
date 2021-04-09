---
title: IoT Edge の Defender for IoT マイクロ エージェントのデプロイ
description: IoT Edge に Defender for IoT セキュリティ エージェントをデプロイする方法について説明します。
ms.topic: conceptual
ms.date: 1/30/2020
ms.openlocfilehash: 38e2aa38ab07a4768e95d91ed0ebc866d7edf39f
ms.sourcegitcommit: f611b3f57027a21f7b229edf8a5b4f4c75f76331
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/22/2021
ms.locfileid: "104784222"
---
# <a name="deploy-a-defender-iot-micro-agent-on-your-iot-edge-device"></a>Defender for IoT マイクロ エージェントを IoT Edge デバイスにデプロイする

**Defender for IoT** モジュールにより、IoT Edge デバイスの包括的なセキュリティ ソリューションが提供されます。
Defender for IoT マイクロ エージェントでは、オペレーティング システムおよびコンテナー システムから未加工のセキュリティ データが収集、集約、分析されて、実行可能なセキュリティ推奨事項とアラートに変換されます。
詳細については、[IoT Edge 向けの Defender for IoT マイクロ エージェント](security-edge-architecture.md)に関する記事を参照してください。

この記事では、IoT Edge デバイスに Defender for IoT マイクロ エージェントをデプロイする方法について説明します。

## <a name="deploy-defender-iot-micro-agent"></a>Defender for IoT マイクロ エージェントのデプロイ

次の手順を使用して、IoT Edge 用の Defender for IoT の Defender for IoT マイクロ エージェントをデプロイします。

### <a name="prerequisites"></a>前提条件

1. IoT Hub で、ご使用のデバイスが [IoT Edge デバイスとして登録されている](../iot-edge/how-to-register-device.md#register-a-new-device)ことを確認してください。

1. Defender for IoT Edge モジュールでは、[AuditD フレームワーク](https://linux.die.net/man/8/auditd)が IoT Edge デバイスにインストールされている必要があります。

    - IoT Edge デバイスで次のコマンドを実行して、そのフレームワークをインストールします。

    `sudo apt-get install auditd audispd-plugins`

    - 次のコマンドを実行して、AuditD がアクティブであることを確認します。

    `sudo systemctl status auditd`<br>
    - 予期される応答: `active (running)`

### <a name="deployment-using-azure-portal"></a>Azure portal を使用したデプロイ

1. Azure portal から **Marketplace** を開きます。

1. **[モ ノのインターネット]** を選択し、 **[Azure Security Center for IoT]** を検索して選択します。

   :::image type="content" source="media/howto/edge-onboarding-8.png" alt-text="Defender for IoT を選択する":::

1. **[作成]** を選択してデプロイを構成します。

1. ご使用の IoT Hub のAzure **サブスクリプション** を選択してから、ご使用の **IoT Hub** を選択します。<br>単一のデバイスをターゲットにするには **[デバイスへのデプロイ]** を選択し、複数のデバイスをターゲットにするには **[大規模にデプロイする]** を選択します。その後、 **[作成]** を選択します。 大規模デプロイの詳細については、[デプロイ方法](../iot-edge/how-to-deploy-at-scale.md)に関する記事を参照してください。

    >[!Note]
    >**[大規模にデプロイする]** を選択した場合は、以下の手順にある **[モジュールの追加]** タブに進む前にデバイス名と詳細を追加してください。

各手順を実行して、Defender for IoT 用の IoT Edge デプロイを完了します。

#### <a name="step-1-modules"></a>手順 1:モジュール

1. **AzureSecurityCenterforIoT** モジュールを選択します。
1. **[モジュールの設定]** タブで、**名前** を **azureiotsecurity** に変更します。
1. **[環境変数]** タブで、必要に応じて変数を追加します (たとえば、*debug level* を追加し、次のいずれかの値に設定することができます: "致命的"、"エラー"、"警告"、または "情報")。
1. **[コンテナーの作成オプション]** タブで、次の構成を追加します。

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

1. **[モジュール ツインの設定]** タブで、次の構成を追加します。

   モジュール ツイン プロパティ:
   
   ``` json
     "ms_iotn:urn_azureiot_Security_SecurityAgentConfiguration"
   ```

   モジュール ツイン プロパティのコンテンツ: 

   ```json
     {

     }
   ```
    
   エージェントの構成の詳細については、「[セキュリティ エージェントを構成する](./how-to-agent-configuration.md)」を参照してください。

1. **[Update]\(更新\)** を選択します。

#### <a name="step-2-runtime-settings"></a>手順 2:ランタイムの設定

1. **[ランタイムの設定]** を選択します。
2. **[Edge Hub]\(Edge ハブ\)** の下で、 **[イメージ]** を **mcr.microsoft.com/azureiotedge-hub:1.0.8.3** に変更します。

    >[!Note]
    > 現在、バージョン 1.0.8.3 以前がサポートされています。

3. **[作成オプション]** が次の構成に設定されていることを確認します。

    ``` json
    {
       "HostConfig":{
          "PortBindings":{
             "8883/tcp":[
                {
                   "HostPort":"8883"
                }
             ],
             "443/tcp":[
                {
                   "HostPort":"443"
                }
             ],
             "5671/tcp":[
                {
                   "HostPort":"5671"
                }
             ]
          }
       }
    }
    ```

4. **[保存]** を選択します。

5. **[次へ]** を選択します。

#### <a name="step-3-specify-routes"></a>手順 3:ルートを指定する

1. **[ルートの指定]** タブで、次の例に従い、**azureiotsecurity** モジュールから **$upstream** にメッセージを転送するルート (明示的または暗黙的) があることを確認します。 ルートが存在する場合にのみ、 **[次へ]** 選択します。

   ルートの例:

    ```Default implicit route
    "route": "FROM /messages/* INTO $upstream"
    ```

    ```Explicit route
    "ASCForIoTRoute": "FROM /messages/modules/azureiotsecurity/* INTO $upstream"
    ```

1. **[次へ]** を選択します。

#### <a name="step-4-review-deployment"></a>手順 4:デプロイを確認する

- **[デプロイの確認]** タブでデプロイ情報を確認し、 **[作成]** を選択してデプロイを完了します。

## <a name="diagnostic-steps"></a>診断手順

問題が発生した場合に IoT Edge の Defender for IoT マイクロ エージェント デバイスの状態を知るには、コンテナー ログを使用するのが最適です。 情報を収集するには、このセクションのコマンドとツールを使います。

### <a name="verify-the-required-containers-are-installed-and-functioning-as-expected"></a>必要なコンテナーがインストールされており、期待どおりに機能していることを確認する

1. IoT Edge デバイスで次のコマンドを実行します。

    `sudo docker ps`

1. 次のコンテナーが実行中であることを確認します。

   | 名前 | イメージ |
   | --- | --- |
   | azureiotsecurity | mcr.microsoft.com/ascforiot/azureiotsecurity:1.0.2 |
   | edgeHub | mcr.microsoft.com/azureiotedge-hub:1.0.8.3 |
   | edgeAgent | mcr.microsoft.com/azureiotedge-agent:1.0.1 |

   最低限必要なコンテナーが存在しない場合は、IoT Edge のデプロイ マニフェストが推奨設定と一致しているかどうかを確認してください。 詳細については、[IoT Edge モジュールのデプロイ](#deployment-using-azure-portal)に関する記事を参照してください。

### <a name="inspect-the-module-logs-for-errors"></a>モジュール ログでエラーを調べる

1. IoT Edge デバイスで次のコマンドを実行します。

   `sudo docker logs azureiotsecurity`

1. より詳細なログについては、環境変数 `logLevel=Debug` を **azureiotsecurity** モジュール デプロイに追加してください。

## <a name="next-steps"></a>次のステップ

構成オプションの詳細については、モジュール構成のハウツー ガイドに進んでください。
> [!div class="nextstepaction"]
> [モジュール構成のハウツー ガイド](./how-to-agent-configuration.md)