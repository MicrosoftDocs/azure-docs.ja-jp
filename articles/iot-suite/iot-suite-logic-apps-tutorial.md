---
title: "Azure IoT Suite と Logic Apps | Microsoft Docs"
description: "ビジネス プロセスのための Azure IoT Suite への Logic Apps の接続方法に関するチュートリアルです。"
services: 
suite: iot-suite
documentationcenter: 
author: dominicbetts
manager: timlt
editor: 
ms.assetid: 4629a7af-56ca-4b21-a769-5fa18bc3ab07
ms.service: iot-suite
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/09/2017
ms.author: corywink
translationtype: Human Translation
ms.sourcegitcommit: 8a531f70f0d9e173d6ea9fb72b9c997f73c23244
ms.openlocfilehash: 5e3221395082513f842863615d40f7d3ebf2562e
ms.lasthandoff: 03/10/2017


---
# <a name="tutorial-connect-logic-app-to-your-azure-iot-suite-remote-monitoring-preconfigured-solution"></a>チュートリアル: ロジック アプリを Azure IoT Suite リモート監視構成済みソリューションに接続する
[Microsoft Azure IoT Suite][lnk-internetofthings] リモート監視構成済みソリューションは、IoT ソリューションを例示するエンド ツー エンド機能をすぐに使い始められる優れた方法です。 このチュートリアルでは、Logic App を Microsoft Azure IoT Suite リモート監視構成済みソリューションに追加する方法を説明します。 これらの手順は、IoT ソリューションをビジネス プロセスに接続してさらに活用できる方法を示すものです。

"*リモート監視構成済みソリューションのプロビジョニング方法のチュートリアルについては、「[チュートリアル: IoT 事前構成済みソリューションの使用][lnk-getstarted]」を参照してください。*"

このため、このチュートリアルの前に、以下を行う必要があります。

* Azure サブスクリプションでリモート監視の事前構成済みソリューションのプロビジョニングを行います。
* SendGrid アカウントを作成し、ビジネス プロセスをトリガーする電子メールを送信できるようにします。 [SendGrid](https://sendgrid.com/) で **[無料で試す]**をクリックし、無料試用版アカウントにサインアップできます。 無料試用版アカウントに登録したら、SendGrid でメールを送信するアクセス許可を付与する [API キー](https://sendgrid.com/docs/User_Guide/Settings/api_keys.html) を作成する必要があります。 この API キーはチュートリアルの後半で必要になります。

このチュートリアルでは、構成済みソリューションのバックエンドのアクションを編集するために Visual Studio 2015 または Visual Studio 2017 が必要となります。

リモート監視構成済みソリューションを既にプロビジョニングしていることを前提とし、[Azure Portal][lnk-azureportal] でそのソリューションのリソース グループに移動します。 リソース グループの名前は、リモート監視ソリューションのプロビジョニング時に選択したソリューション名と同じです。 リソース グループには、Azure クラシック ポータルにある Azure Active Directory アプリケーション以外の、ソリューション用にプロビジョニングされている Azure リソースがすべて表示されます。 次のスクリーンショットには、リモート監視構成済みソリューションの **[リソース グループ]** ブレードの例が示されています。

![](media/iot-suite-logic-apps-tutorial/resourcegroup.png)

作業を開始するには、構成済みソリューションで使用するロジック アプリをセットアップします。

## <a name="set-up-the-logic-app"></a>ロジック アプリをセットアップする
1. Azure ポータルで、リソース グループ ブレードの上部にある **[追加]** をクリックします。
2. **ロジック アプリ**を検索し、選択してから **[作成]** をクリックします。
3. **名前**を入力し、リモート監視ソリューションのプロビジョニング時に使用したものと同じ**サブスクリプション**と**リソース グループ**を使用します。 **[作成]**をクリックします。
   
    ![](media/iot-suite-logic-apps-tutorial/createlogicapp.png)
4. デプロイが完了すると、ロジック アプリがリソース グループにリソースとしてリストされていることがわかります。
5. [ロジック アプリ] をクリックしてロジック アプリ ブレードに移動し、**空のロジック アプリ** テンプレートを選択して **Logic Apps デザイナー**を開きます。
   
    ![](media/iot-suite-logic-apps-tutorial/logicappsdesigner.png)
6. **[要求]** を選択します。 この操作によって、特定の JSON 形式のペイロードを含む受信 HTTP 要求がトリガーとして機能するようになります。
7. 要求本文の JSON スキーマには、以下のコードを貼り付けます。
   
    ```
    {
      "$schema": "http://json-schema.org/draft-04/schema#",
      "id": "/",
      "properties": {
        "DeviceId": {
          "id": "DeviceId",
          "type": "string"
        },
        "measuredValue": {
          "id": "measuredValue",
          "type": "integer"
        },
        "measurementName": {
          "id": "measurementName",
          "type": "string"
        }
      },
      "required": [
        "DeviceId",
        "measurementName",
        "measuredValue"
      ],
      "type": "object"
    }
    ```
   
   > [!NOTE]
   > ロジック アプリの保存後に HTTP ポストの URL をコピーできますが、まず、アクションを追加する必要があります。
   > 
   > 
8. 手動トリガーで **[+ 新しいステップ]** をクリックします。 次に、 **[アクションの追加]**をクリックします。
   
    ![](media/iot-suite-logic-apps-tutorial/logicappcode.png)
9. **[SendGrid - メールを送信]** を見つけてクリックします。
   
    ![](media/iot-suite-logic-apps-tutorial/logicappaction.png)
10. **SendGridConnection** などの接続名を入力し、SendGrid アカウントのセットアップ時に作成した **SendGrid API キー**を入力して **[作成]** をクリックします。
    
    ![](media/iot-suite-logic-apps-tutorial/sendgridconnection.png)
11. **[送信元]** と **[宛先]** の両方のフィールドに所有しているメール アドレスを追加します。 **[件名]** フィールドに「**リモート監視アラート [DeviceId]**」と入力します。 **[電子メールの本文]** フィールドには、「**デバイス [DeviceId] が [measuredValue] という値の [measurementName] をレポートしました**」と入力します。 **[DeviceId]**、**[measurementName]**、および **[measuredValue]** は、**[前のステップで使用したデータを挿入できます]** セクションでクリックして追加することができます。
    
    ![](media/iot-suite-logic-apps-tutorial/sendgridaction.png)
12. トップ メニューの **[保存]** をクリックします。
13. **[要求]** トリガーをクリックして、**[この URL に対する HTTP POST]** の値をコピーします。 この URL はこのチュートリアルの後半で必要になります。

> [!NOTE]
> Logic Apps では、Office 365 でのアクションを含め、[さまざまな種類のアクション][lnk-logic-apps-actions]を実行できます。 
> 
> 

## <a name="set-up-the-eventprocessor-web-job"></a>EventProcessor Web ジョブをセットアップする
このセクションでは、構成済みのソリューションを、作成したロジック アプリに接続します。 このタスクを完了するには、デバイス センサーの値がしきい値を超えたときにアクションが発生するとロジック アプリをトリガーする URL を追加します。

1. git クライアントを使用して、最新バージョンの [azure-iot-remote-monitoring github リポジトリ][lnk-rmgithub]を複製します。 次に例を示します。
   
    ```
    git clone https://github.com/Azure/azure-iot-remote-monitoring.git
    ```
2. Visual Studio で、リポジトリのローカル コピーの **RemoteMonitoring.sln** を開きます。
3. **Infrastructure\\Repository** フォルダーの **ActionRepository.cs** ファイルを開きます。
4. 以下のように、ロジック アプリからメモした **[この URL に対する HTTP POST]** を使用して **actionIds** ディクショナリを更新します。
   
    ```
    private Dictionary<string,string> actionIds = new Dictionary<string, string>()
    {
        { "Send Message", "<Http Post to this URL>" },
        { "Raise Alarm", "<Http Post to this URL>" }
    };
    ```
5. ソリューションの変更内容を保存して、Visual Studio を終了します。

## <a name="deploy-from-the-command-line"></a>コマンドラインからデプロイする
このセクションでは、更新されたバージョンのリモート監視ソリューションをデプロイして、Azure で現在実行中のバージョンを置き換えます。

1. [デプロイのセットアップ][lnk-devsetup]手順に従って、デプロイ用に環境をセットアップします。
2. ローカルでデプロイする場合は、[ローカル デプロイ][lnk-localdeploy]の手順に従ってください。
3. クラウドにデプロイし、既存のクラウド デプロイを更新する場合は、[クラウド デプロイ][lnk-clouddeploy]の手順に従ってください。 デプロイ名として、元のデプロイの名前を使用します。 たとえば、元のデプロイ名が **demologicapp**の場合は、以下のコマンドを使用します。
   
   ``
   build.cmd cloud release demologicapp
   ``
   
   ビルド スクリプトを実行する場合は、ソリューションのプロビジョニング時に使用したものと同じ Azure アカウント、サブスクリプション、リージョン、および Active Directory インスタンスを必ず使用してください。

## <a name="see-your-logic-app-in-action"></a>Logic App の動作を確認する
リモート監視構成済みソリューションには、ソリューションのプロビジョニング時に既定で&2; つのルールが設定されます。 以下のルールは両方とも **SampleDevice001** デバイスに対するものです。

* 気温 > 38.00
* 湿度 > 48.00

気温ルールは **Raise Alarm** アクションをトリガーし、湿度ルールは **SendMessage** アクションをトリガーします。 両方のアクション ( **ActionRepository** クラス) で同じ URL を使用したと仮定した場合、ロジック アプリはいずれかのルールをトリガーします。 両方のルールで、SendGrid を使用して **宛先** アドレスにアラートの詳細を含むメールを送信します。

> [!NOTE]
> ロジック アプリはしきい値が満たされるたびにトリガーし続けます。 不要なメールが送信されないようにするために、ソリューション ポータルでルールを無効にするか、[Azure Portal][lnk-azureportal] でロジック アプリを無効にすることができます。
> 
> 

メールの受信だけでなく、ポータルでは次のように Logic App の実行時間を確認することもできます。

![](media/iot-suite-logic-apps-tutorial/logicapprun.png)

## <a name="next-steps"></a>次のステップ
これで Logic Apps を使用して構成済みソリューションをビジネス プロセスに接続しました。次を参照することで、構成済みソリューションのカスタマイズのオプションについて、さらに理解を深めることができます。

* [事前構成済みのリモート監視ソリューションによる動的テレメトリの使用][lnk-dynamic]
* [リモート監視構成済みソリューションのデバイス情報メタデータ][lnk-devinfo]

[lnk-dynamic]: iot-suite-dynamic-telemetry.md
[lnk-devinfo]: iot-suite-remote-monitoring-device-info.md

[lnk-internetofthings]: https://azure.microsoft.com/documentation/suites/iot-suite/
[lnk-getstarted]: iot-suite-getstarted-preconfigured-solutions.md
[lnk-azureportal]: https://portal.azure.com
[lnk-logic-apps-actions]: ../connectors/apis-list.md
[lnk-rmgithub]: https://github.com/Azure/azure-iot-remote-monitoring
[lnk-devsetup]: https://github.com/Azure/azure-iot-remote-monitoring/blob/master/Docs/dev-setup.md
[lnk-localdeploy]: https://github.com/Azure/azure-iot-remote-monitoring/blob/master/Docs/local-deployment.md
[lnk-clouddeploy]: https://github.com/Azure/azure-iot-remote-monitoring/blob/master/Docs/cloud-deployment.md

