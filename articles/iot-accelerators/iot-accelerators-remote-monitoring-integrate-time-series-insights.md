---
title: Time Series Insights とリモート監視の統合 - Azure | Microsoft Docs
description: このハウツーでは、Time Series Insights がまだ含まれていない既存のリモート監視ソリューションに Time Series Insights を構成する方法を説明します。
author: aditidugar
manager: timlt
ms.author: adugar
ms.date: 09/12/2018
ms.topic: conceptual
ms.service: iot-accelerators
services: iot-accelerators
ms.openlocfilehash: 850d8bbb525763e0e7d0c0441173180b7c469dd8
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2019
ms.locfileid: "58085152"
---
# <a name="integrate-azure-time-series-insights-with-remote-monitoring"></a>Azure Time Series Insights とリモート監視の統合

Azure Time Series Insights は、IoT 規模の時系列データをクラウドで管理するための、フル マネージドの分析、ストレージ、視覚化サービスです。 Time Series Insights を使用すると、時系列データを保存、管理し、イベントを同時に調査、視覚化できるほか、根本原因分析を行い、複数のサイトやアセットを比較できます。

リモート監視ソリューション アクセラレータで、自動デプロイと、Time Series Insights との統合が提供されるようになりました。 このハウツーでは、Time Series Insights がまだ含まれていない既存のリモート監視ソリューションに Time Series Insights を構成する方法を説明します。

> [!NOTE]
> Azure China クラウドでは、現在、Time Series Insights はご利用になれません。 Azure China クラウドでの新しいリモート監視ソリューション アクセラレータのデプロイでは、すべてのストレージに Cosmos DB を使用します。

## <a name="prerequisites"></a>前提条件

このハウツーを完了するには、リモート監視ソリューションを既にデプロイしている必要があります。

* [リモート監視ソリューション アクセラレータをデプロイする](quickstart-remote-monitoring-deploy.md)

## <a name="create-a-consumer-group"></a>コンシューマー グループの作成

Time Series Insights にデータをストリーミングするために使用する専用のコンシューマー グループを IoT Hub 内に作成します。

> [!NOTE]
> コンシューマー グループは、Azure IoT Hub からデータをプルするアプリケーションによって使用されます。 コンシューマー グループごとに、最大 5 つの出力コンシューマーが可能です。 5 つの出力シンクすべてに対して、新しいコンシューマー グループを作成する必要があり、最大 32 のコンシューマー グループを作成できます。

1. Azure Portal で [クラウド シェル] ボタンをクリックします。

1. 次のコマンドを実行して、新しいコンシューマー グループを作成します。 リモート監視のデプロイでの IoT ハブの名前と、リモート監視のデプロイの名前をリソース グループ名として使用します。

```azurecli-interactive
az iot hub consumer-group create --hub-name contosorm30526 --name timeseriesinsights --resource-group ContosoRM
```

## <a name="deploy-time-series-insights"></a>Time Series Insights のデプロイ

次に、追加のリソースとして Time Series Insights をリモート監視ソリューションにデプロイし、IoT ハブに接続します。

1. [Azure Portal](https://portal.azure.com/) にサインインします。

1. **[リソースの作成]** > **[モノのインターネット (IoT)]** > **[Time Series Insights]** を選択します。

    ![新しい Time Series Insights](./media/iot-accelerators-remote-monitoring-integrate-time-series-insights/new-time-series-insights.png)

1. Time Series Insights 環境を作成するには、次の表の値を使用します。

    | Setting | 値 |
    | ------- | ----- |
    | 環境名 | 次のスクリーン ショットでは、**contorosrmtsi** という名前を使用しています。 この手順を完了したら、独自の一意の名前を選択してください。 |
    | サブスクリプション | ドロップダウンから Azure サブスクリプションを選択します。 |
    | リソース グループ | **既存のものを使用します**。 既存のリモート監視リソース グループの名前を選択します。 |
    | Location | **[米国東部]** を使用します。 可能な場合は、リモート監視ソリューションと同じリージョンに環境を作成します。 |
    | Sku |**S1** |
    | 容量 | **1** |

    ![Time Series Insights を作成する](./media/iot-accelerators-remote-monitoring-integrate-time-series-insights/new-time-series-insights-create.png)

1. **Create** をクリックしてください。 環境が作成されるまで、少し時間がかかる可能性があります。

## <a name="create-event-source"></a>イベント ソースの作成

IoT Hub に接続する新しいイベント ソースを作成します。 必ず前の手順で作成したコンシューマー グループを使用してください。 Time Series Insights では、各サービスが別のサービスによって使用されていない専用のコンシューマー グループを用意する必要があります。

1. 新しい Time Series Insights 環境に移動します。

1. 左側で、**[イベント ソース]** を選択します。

    ![イベント ソースを表示する](./media/iot-accelerators-remote-monitoring-integrate-time-series-insights/time-series-insights-event-sources.png)

1. **[追加]** をクリックします。

    ![イベント ソースを追加する](./media/iot-accelerators-remote-monitoring-integrate-time-series-insights/time-series-insights-event-sources-add.png)

1. IoT Hub を新しいイベント ソースとして構成するには、次の表の値を使用します。

    | Setting | 値 |
    | ------- | ----- |
    | イベント ソース名 | 次のスクリーン ショットでは、**contosorm-iot-hub** という名前を使用しています。 この手順を完了したら、独自の一意の名前を使用してください。 |
    | ソース | **IoT Hub** |
    | インポート オプション | **使用可能なサブスクリプションの IoT Hub を使用する** |
    | サブスクリプション ID | ドロップダウンから Azure サブスクリプションを選択します。 |
    | IoT Hub 名 | **contosorma57a6**。 リモート監視ソリューションの IoT Hub の名前を使用します。 |
    | IoT Hub ポリシー名 | **iothubowner**。使用するポリシーは、所有者ポリシーであることを確認してください。 |
    | IoT Hub ポリシー キー | このフィールドは自動的に設定されます。 |
    | IoT Hub コンシューマー グループ | **timeseriesinsights**。 |
    | イベントのシリアル化の形式 | **JSON**     | 
    | タイムスタンプ プロパティ名 | 空白 |

    ![イベント ソースを作成する](./media/iot-accelerators-remote-monitoring-integrate-time-series-insights/time-series-insights-event-source-create.png)

1. **Create** をクリックしてください。

## <a name="configure-the-data-access-policy"></a>データ アクセス ポリシーの構成

リモート監視ソリューションへのアクセス権を持つすべてのユーザーが、Time Series Insights エクスプローラーでデータを探索できるようにするため、Azure portal でデータ アクセス ポリシーの下にアプリケーションとユーザーを追加します。 

1. ナビゲーション リストで、**[リソース グループ]** を選択します。

1. **ContosoRM** リソース グループを選択します。

1. Azure リソースの一覧で **[contosormtsi]** を選択します。

1. **[データ アクセス ポリシー]** を選択して、ロールの割り当ての現在の一覧を表示します。

1. **[追加]** を選択して、**[Select User Rule]\(ユーザー ルールの選択\)** ウィンドウを開きます。

   ロールを割り当てるためのアクセス許可がない場合には、**[追加]** オプションが表示されません。

1. **[ロール]** ドロップダウン リストで、**[閲覧者]** や **[共同作成者]** などのロールを選択します。

1. **[選択]** の一覧で、ユーザー、グループ、またはアプリケーションを選択します。 一覧にセキュリティ プリンシパルが表示されない場合には、**[選択]** ボックスに表示名、メール アドレス、オブジェクト識別子を入力してディレクトリを検索します。

1. **[保存]** を選択して、ロールの割り当てを作成します。 何分か待つと、セキュリティ プリンシパルにデータ アクセス ポリシー内のロールが割り当てられます。

> [!NOTE]
> Time Series Insights エクスプローラーにその他のユーザー アクセスを許可する必要がある場合は、[データ アクセスを許可する](../time-series-insights/time-series-insights-data-access.md#grant-data-access)ための手順を使用します。

## <a name="configure-azure-stream-analytics"></a>Azure Stream Analytics の構成 

次の手順では、Cosmos DB へのメッセージ送信を停止し、それらを Time Series Insights にのみ保存するように、Azure Stream Analytics Manager マイクロサービスを構成します。 Cosmos DB でメッセージを複製する場合は、この手順をスキップしてください。

1. ナビゲーション リストで、**[リソース グループ]** を選択します。

1. **ContosoRM** リソース グループを選択します。

1. リソースの一覧で Azure Stream Analytics (ASA) のストリーミング ジョブを検索します。 リソース名は **streamingjobs-** から始まります。

1. 上部にあるボタンをクリックして、ASA ストリーミング ジョブを停止します。

1. ASA クエリを編集し、Cosmos DB 内のメッセージ ストリームを指す **SELECT** 句、**INTO** 句、**FROM** 句を削除します。 これらの句は、クエリの下部にあるべきで、次の例のようにする必要があります。

    ```sql
    SELECT
            CONCAT(T.IoTHub.ConnectionDeviceId, ';', CAST(DATEDIFF(millisecond, '1970-01-01T00:00:00Z', T.EventEnqueuedUtcTime) AS nvarchar(max))) as id,
            1 as [doc.schemaVersion],
            'd2cmessage' as [doc.schema],
            T.IoTHub.ConnectionDeviceId as [device.id],
            'device-sensors;v1' as [device.msg.schema],
            'StreamingJobs' as [data.schema],
            DATEDIFF(millisecond, '1970-01-01T00:00:00Z', System.Timestamp) as [device.msg.created],
            DATEDIFF(millisecond, '1970-01-01T00:00:00Z', T.EventEnqueuedUtcTime) as [device.msg.received],
            udf.removeUnusedProperties(T) as Data
    INTO
        Messages
    FROM
        DeviceTelemetry T PARTITION BY PartitionId TIMESTAMP BY T.EventEnqueuedUtcTime
    ```

6. Azure Stream Analytics ストリーミング ジョブを再開します。

7. コマンド プロンプトで次のコマンドを入力して、Azure Stream Analytics マネージャー マイクロサービスへの最新の変更をプルします。

.NET: 

```cmd/sh
docker pull azureiotpcs/asa-manager-dotnet:1.0.2
```

Java:

```cmd/sh
docker pull azureiotpcs/asa-manager-java:1.0.2
```

## <a name="configure-the-telemetry-microservice"></a>テレメトリ マイクロサービスの構成

コマンド プロンプトに次のコマンドを入力して、最新のテレメトリ マイクロサービスをプルします。

.NET:

```cmd/sh
docker pull azureiotpcs/telemetry-dotnet:1.0.2
```

Java:

```cmd/sh
docker pull azureiotpcs/telemetry-java:1.0.2
```

## <a name="optional-configure-the-web-ui-to-link-to-the-time-series-insights-explorer"></a>*[省略可能]* Time Series Insights エクスプローラーにリンクするように Web UI を構成する

Time Series Insights エクスプローラーでデータを簡単に表示するため、環境に簡単にリンクできるように UI をカスタマイズすることをお勧めします。 これを行うには、次のコマンドを使用して Web UI への最新の変更をプルします。

```cmd/sh
docker pull azureiotpcs/pcs-remote-monitoring-webui:1.0.2
```

## <a name="configure-the-environment-variables"></a>環境変数の構成

Time Series Insights の統合を完了するには、更新されたマイクロサービス用にデプロイの環境を構成する必要があります。

### <a name="basic-deployments"></a>Basic デプロイ

更新されたマイクロサービス用に `basic` デプロイの環境を構成します。

1. Azure portal の左側のウィンドウで、**[Azure Active Directory]** タブをクリックします。

1. **[アプリの登録]** をクリックします。

1. **ContosoRM** アプリケーションを検索してクリックします。

1. **[設定]** > **[キー]** の順に移動して、アプリケーション用に新しいキーを作成します。 キー値を安全な場所にコピーしてください。

1. 最新のタグを使用して、GitHub リポジトリから[最新の docker compose yaml ファイル](https://github.com/Azure/pcs-cli/tree/5a9b4e0dbe313172eff19236e54a4d461d4f3e51/solutions/remotemonitoring/single-vm)をプルします。 

1. [SSH キーを作成して使用する方法](https://docs.microsoft.com/azure/virtual-machines/linux/ssh-from-windows)に記載されている手順に従って、VM に SSH で接続します。

1. 接続したら、`cd /app` を入力します。

1. docker compose yaml ファイル内の各マイクロサービスと VM の `env-setup` のスクリプトに次の環境変数を追加します。

    ```sh
    PCS_TELEMETRY_STORAGE_TYPE=tsi
    PCS_TSI_FQDN={TSI Data Access FQDN}
    PCS_AAD_TENANT={AAD Tenant Id}
    PCS_AAD_APPID={AAD application Id}
    PCS_AAD_APPSECRET={AAD application key}
    ```

1. **テレメトリ サービス**に移動して、上記と同じ環境変数を追加することで、docker compose ファイルも編集します。

1. **ASA マネージャー サービス**に移動して、`PCS_TELEMETRY_STORAGE_TYPE` を追加することで、docker compose ファイルを編集します。

1. VM から `sudo ./start.sh` を使用して docker コンテナーを再起動します。

### <a name="standard-deployments"></a>Standard デプロイ

上記の更新されたマイクロサービス用に `standard` デプロイの環境を構成します。

1. コマンド ラインで `kubectl proxy` を実行します。 詳細については、[Kubernetes API へのアクセス](https://kubernetes.io/docs/tasks/access-kubernetes-api/http-proxy-access-api/#using-kubectl-to-start-a-proxy-server)に関するページを参照してください。

1. Kubernetes 管理コンソールを開きます。

1. TSI に次の新しい環境変数を追加するための構成マップを検索します。

    ```yaml
    telemetry.storage.type: "tsi"
    telemetry.tsi.fqdn: "{TSI Data Access FQDN}"
    security.auth.serviceprincipal.secret: "{AAD application service principal secret}"
    ```

4. テレメトリ サービス ポッドのテンプレート yaml ファイルを編集します。

    ```yaml
    - name: PCS_AAD_TENANT
        valueFrom:
        configMapKeyRef:
            name: deployment-configmap
            key: security.auth.tenant
    - name: PCS_AAD_APPID
        valueFrom:
        configMapKeyRef:
            name: deployment-configmap
            key: security.auth.audience
    - name: PCS_AAD_APPSECRET
        valueFrom:
        configMapKeyRef:
            name: deployment-configmap
            key: security.auth.serviceprincipal.secret
    - name: PCS_TELEMETRY_STORAGE_TYPE
        valueFrom:
        configMapKeyRef:
            name: deployment-configmap
            key: telemetry.storage.type
    - name: PCS_TSI_FQDN
        valueFrom:
        configMapKeyRef:
            name: deployment-configmap
            key: telemetry.tsi.fqdn
    ```

5. ASA マネージャー サービス ポッドのテンプレート yaml ファイルを編集します。

    ```yaml
    - name: PCS_TELEMETRY_STORAGE_TYPE
        valueFrom:
        configMapKeyRef:
            name: deployment-configmap
            key: telemetry.storage.type
    ```

## <a name="next-steps"></a>次の手順

* Time Series Insights エクスプローラーで、データを探索し、アラートを診断する方法については、[根本原因分析の実施](iot-accelerators-remote-monitoring-root-cause-analysis.md)に関するチュートリアルを参照してください。

* Time Series Insights エクスプローラーでのデータの探索とクエリ方法については、ドキュメント「[Azure Time Series Insights エクスプローラー](https://docs.microsoft.com/azure/time-series-insights/time-series-insights-explorer)」を参照してください。
