---
title: リモート監視ソリューション UI のカスタマイズ - Azure | Microsoft Docs
description: この記事では、リモート監視ソリューション アクセラレータ UI にアクセスし、いくつかのカスタマイズを行う方法について説明します。
author: dominicbetts
manager: timlt
ms.author: dobett
ms.service: iot-accelerators
services: iot-accelerators
ms.date: 11/09/2018
ms.topic: conceptual
ms.openlocfilehash: aed63e332375be4f8ed939cf162545c9f366f329
ms.sourcegitcommit: 90dcc3d427af1264d6ac2b9bde6cdad364ceefcc
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/21/2019
ms.locfileid: "58317597"
---
# <a name="customize-the-remote-monitoring-solution-accelerator"></a>リモート監視ソリューション アクセラレータをカスタマイズする

この記事では、ソース コードにアクセスし、リモート監視ソリューション アクセラレータ UI をカスタマイズする方法について説明します。

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="prepare-a-local-development-environment-for-the-ui"></a>UI のローカル開発環境を準備する

リモート監視ソリューション アクセラレータ UI のコードは、React.js フレームワークを使用して実装されます。 [azure-iot-pcs-remote-monitoring-webui](https://github.com/Azure/azure-iot-pcs-remote-monitoring-webui) GitHub リポジトリでソース コードを見つけることができます。

UI を変更するために、そのコピーをローカルで実行できます。 テレメトリの取得などのアクションを実行するために、ローカル コピーはソリューションのデプロイ済みのインスタンスに接続します。

次の手順に、UI 開発のためのローカル環境を設定するプロセスを説明します。

1. **pcs** CLI を使用して、ソリューション アクセラレータの**基本**インスタンスをデプロイします。 デプロイの名前と仮想マシンに提供した資格情報をメモしておきます。 詳しくは、[CLI を使用したデプロイ](iot-accelerators-remote-monitoring-deploy-cli.md)に関するページをご覧ください。

1. ソリューションでマイクロサービスをホストする仮想マシンへの SSH アクセスを有効にするには、Azure portal または Azure Cloud Shell を使用します。 例: 

    ```azurecli-interactive
    az network nsg rule update --name SSH --nsg-name {your solution name}-nsg --resource-group {your solution name} --access Allow
    ```

    SSH アクセスを有効にするのはテストおよび開発中のみにしてください。 SSH を有効にした場合、[使用し終えたらできるだけ早く無効にする必要があります](../security/azure-security-network-security-best-practices.md#disable-rdpssh-access-to-virtual-machines)。

1. Azure Portal または Azure Cloud Shell を使用して、仮想マシンの名前とパブリック IP アドレスを検索します。 例: 

    ```azurecli-interactive
    az resource list --resource-group {your solution name} -o table
    az vm list-ip-addresses --name {your vm name from previous command} --resource-group {your solution name} -o table
    ```

1. SSH を使用して仮想マシンに接続します。 前の手順で取得した IP アドレスと、ソリューションをデプロイするために、**pcs** を実行したときに提供した資格情報を使用します。 `ssh` コマンドは Azure Cloud Shell で使用できます。

1. ローカル UX で接続できるようにするには、仮想マシンの bash シェルで、次のコマンドを実行します。

    ```sh
    cd /app
    sudo ./start.sh --unsafe
    ```

1. コマンドが完了し、Web サイトが起動したことを確認したら、仮想マシンから切断できます。

1. [azure-iot-pcs-remote-monitoring-webui](https://github.com/Azure/azure-iot-pcs-remote-monitoring-webui) リポジトリのローカル コピーで、**.env** ファイルを編集し、デプロイ済みのソリューションの URL を追加します。

    ```config
    NODE_PATH = src/
    REACT_APP_BASE_SERVICE_URL=https://{your solution name}.azurewebsites.net/
    ```

1. コマンド プロンプトで、`azure-iot-pcs-remote-monitoring-webui` フォルダーのローカル コピーに移動します。

1. 必要なライブラリをインストールして UI をローカルで実行するには、次のコマンドを実行します。

    ```cmd/sh
    npm install
    npm start
    ```

1. 前のコマンドは、http:\//localhost:3000/dashboard でローカルに UI を実行します。 サイトの実行中にコードを編集し、動的に更新されることを確認できます。

## <a name="customize-the-layout"></a>レイアウトのカスタマイズ

リモート監視ソリューションの各ページは、ソース コードで*パネル*と呼ばれる一連のコントロールから構成されます。 **ダッシュボード** ページは、概要、マップ、アラーム、テレメトリ、および Analytics の 5 つのパネルで構成されます。 各ページとそのパネルを定義するソース コードを、[pcs-remote-monitoring-webui](https://github.com/Azure/pcs-remote-monitoring-webui) GitHub リポジトリで見つけることができます。 たとえば、**Dashboard** ページ、そのレイアウト、およびページ上のパネルを定義するコードは、[src/components/pages/dashboard](https://github.com/Azure/pcs-remote-monitoring-webui/tree/master/src/components/pages/dashboard) フォルダーにあります。

パネルがその独自のレイアウトとサイズを管理するため、ユーザーはページのレイアウトを簡単に変更できます。 次のことを行うために、`src/components/pages/dashboard/dashboard.js` ファイルの **PageContent** 要素に以下の変更を加えます。

* マップ パネルとテレメトリ パネルの位置を交換します。
* マップ パネルと分析パネルの相対的な幅を変更します。

```javascript
<PageContent className="dashboard-container">
  <Grid>
    <Cell className="col-1 devices-overview-cell">
      <OverviewPanel
        activeDeviceGroup={activeDeviceGroup}
        openWarningCount={openWarningCount}
        openCriticalCount={openCriticalCount}
        onlineDeviceCount={onlineDeviceCount}
        offlineDeviceCount={offlineDeviceCount}
        isPending={analyticsIsPending || devicesIsPending}
        error={deviceGroupError || devicesError || analyticsError}
        t={t} />
    </Cell>
    <Cell className="col-6">
      <TelemetryPanel
        timeSeriesExplorerUrl={timeSeriesParamUrl}
        telemetry={telemetry}
        isPending={telemetryIsPending}
        lastRefreshed={lastRefreshed}
        error={deviceGroupError || telemetryError}
        theme={theme}
        colors={chartColorObjects}
        t={t} />
    </Cell>
    <Cell className="col-3">
      <AlertsPanel
        alerts={currentActiveAlertsWithName}
        isPending={analyticsIsPending || rulesIsPending}
        error={rulesError || analyticsError}
        t={t}
        deviceGroups={deviceGroups} />
    </Cell>
    <Cell className="col-4">
      <PanelErrorBoundary msg={t('dashboard.panels.map.runtimeError')}>
        <MapPanel
          analyticsVersion={analyticsVersion}
          azureMapsKey={azureMapsKey}
          devices={devices}
          devicesInAlert={devicesInAlert}
          mapKeyIsPending={azureMapsKeyIsPending}
          isPending={devicesIsPending || analyticsIsPending}
          error={azureMapsKeyError || devicesError || analyticsError}
          t={t} />
      </PanelErrorBoundary>
    </Cell>
    <Cell className="col-6">
      <AnalyticsPanel
        timeSeriesExplorerUrl={timeSeriesParamUrl}
        topAlerts={topAlertsWithName}
        alertsPerDeviceId={alertsPerDeviceType}
        criticalAlertsChange={criticalAlertsChange}
        isPending={analyticsIsPending || rulesIsPending || devicesIsPending}
        error={devicesError || rulesError || analyticsError}
        theme={theme}
        colors={chartColorObjects}
        t={t} />
    </Cell>
    {
      Config.showWalkthroughExamples &&
      <Cell className="col-4">
        <ExamplePanel t={t} />
      </Cell>
    }
  </Grid>
</PageContent>
```

![パネル レイアウトの変更](./media/iot-accelerators-remote-monitoring-customize/layout.png)

[パネルを複製して、カスタマイズする](#duplicate-and-customize-an-existing-control)場合は、同じパネルのいくつかのインスタンスやいくつかのバージョンを追加することもできます。 次の例は、テレメトリ パネルの 2 つのインスタンスを追加する方法を示しています。 これらの変更を行うには、`src/components/pages/dashboard/dashboard.js` ファイルを編集します。

```javascript
<PageContent className="dashboard-container">
  <Grid>
    <Cell className="col-1 devices-overview-cell">
      <OverviewPanel
        activeDeviceGroup={activeDeviceGroup}
        openWarningCount={openWarningCount}
        openCriticalCount={openCriticalCount}
        onlineDeviceCount={onlineDeviceCount}
        offlineDeviceCount={offlineDeviceCount}
        isPending={analyticsIsPending || devicesIsPending}
        error={deviceGroupError || devicesError || analyticsError}
        t={t} />
    </Cell>
    <Cell className="col-3">
      <TelemetryPanel
        timeSeriesExplorerUrl={timeSeriesParamUrl}
        telemetry={telemetry}
        isPending={telemetryIsPending}
        lastRefreshed={lastRefreshed}
        error={deviceGroupError || telemetryError}
        theme={theme}
        colors={chartColorObjects}
        t={t} />
    </Cell>
    <Cell className="col-3">
      <TelemetryPanel
        timeSeriesExplorerUrl={timeSeriesParamUrl}
        telemetry={telemetry}
        isPending={telemetryIsPending}
        lastRefreshed={lastRefreshed}
        error={deviceGroupError || telemetryError}
        theme={theme}
        colors={chartColorObjects}
        t={t} />
    </Cell>
    <Cell className="col-3">
      <AlertsPanel
        alerts={currentActiveAlertsWithName}
        isPending={analyticsIsPending || rulesIsPending}
        error={rulesError || analyticsError}
        t={t}
        deviceGroups={deviceGroups} />
    </Cell>
    <Cell className="col-4">
      <PanelErrorBoundary msg={t('dashboard.panels.map.runtimeError')}>
        <MapPanel
          analyticsVersion={analyticsVersion}
          azureMapsKey={azureMapsKey}
          devices={devices}
          devicesInAlert={devicesInAlert}
          mapKeyIsPending={azureMapsKeyIsPending}
          isPending={devicesIsPending || analyticsIsPending}
          error={azureMapsKeyError || devicesError || analyticsError}
          t={t} />
      </PanelErrorBoundary>
    </Cell>
    <Cell className="col-6">
      <AnalyticsPanel
        timeSeriesExplorerUrl={timeSeriesParamUrl}
        topAlerts={topAlertsWithName}
        alertsPerDeviceId={alertsPerDeviceType}
        criticalAlertsChange={criticalAlertsChange}
        isPending={analyticsIsPending || rulesIsPending || devicesIsPending}
        error={devicesError || rulesError || analyticsError}
        theme={theme}
        colors={chartColorObjects}
        t={t} />
    </Cell>
    {
      Config.showWalkthroughExamples &&
      <Cell className="col-4">
        <ExamplePanel t={t} />
      </Cell>
    }
  </Grid>
</PageContent>
```

各パネルでさまざまなテレメトリを表示できます。

![複数のテレメトリ パネル](./media/iot-accelerators-remote-monitoring-customize/multiple-telemetry.png)

## <a name="duplicate-and-customize-an-existing-control"></a>既存のコントロールの複製とカスタマイズ

次の手順では、既存のパネルを複製し、それを変更して、変更済みのバージョンを使用する方法を説明します。 この手順では例として **alerts** パネルを使用します。

1. リポジトリのローカル コピーで、`src/components/pages/dashboard/panels` フォルダー内の **alerts** フォルダーのコピーを作成します。 新しいコピーに **cust_alerts** と名付けます。

1. **cust_alerts** フォルダー内の **alertsPanel.js** ファイルで、クラスの名前を **CustAlertsPanel** に編集します。

    ```javascript
    export class CustAlertsPanel extends Component {
    ```

1. 次の行を `src/components/pages/dashboard/panels/index.js` ファイルに追加します。

    ```javascript
    export * from './cust_alerts';
    ```

1. `src/components/pages/dashboard/dashboard.js` ファイル内の `alertsPanel` を `CustAlertsPanel` で置き換えます。

    ```javascript
    import {
      OverviewPanel,
      CustAlertsPanel,
      TelemetryPanel,
      KpisPanel,
      MapPanel,
      transformTelemetryResponse,
      chartColors
    } from './panels';

    ...

    <Cell className="col-3">
      <CustAlertsPanel
        alerts={currentActivealertsWithName}
        isPending={kpisIsPending || rulesIsPending}
        error={rulesError || kpisError}
        t={t} />
    </Cell>
    ```

これで、元の **alerts** パネルを **CustAlerts** というコピーで置き換えました。 このコピーは元のものと同じです。 これでコピーを変更できます。 たとえば、**alerts** パネルの列の順序を変更するには、次の手順を実行します。

1. `src/components/pages/dashboard/panels/cust_alerts/alertsPanel.js` ファイルを開きます。

1. 列定義を次のコード スニペットに示すように変更します。

    ```javascript
    this.columnDefs = [
      rulesColumnDefs.severity,
      {
        headerName: 'rules.grid.count',
        field: 'count'
      },
      {
        ...rulesColumnDefs.ruleName,
        minWidth: 200
      },
      rulesColumnDefs.explore
    ];
    ```

次のスクリーン ショットは、**alerts** パネルの新しいバージョンを示しています。

![更新済みの alerts パネル](./media/iot-accelerators-remote-monitoring-customize/reorder-columns.png)

## <a name="customize-the-telemetry-chart"></a>テレメトリ グラフのカスタマイズ

**ダッシュ ボード** ページのテレメトリ グラフは、`src/components/pages/dashboard/panels/telemtry` フォルダー内のファイルによって定義されます。 UI は、`src/services/telemetryService.js` ファイル内のソリューション バックエンドから、テレメトリを取得します。 次の手順では、テレメトリ グラフに表示される期間を 15 分から 5 分に変更する方法を示します。

1. `src/services/telemetryService.js` ファイルで、**getTelemetryByDeviceIdP15M** という関数を見つけます。 この関数のコピーを作成し、コピーを次のように変更します。

    ```javascript
    static getTelemetryByDeviceIdP5M(devices = []) {
      return TelemetryService.getTelemetryByMessages({
        from: 'NOW-PT5M',
        to: 'NOW',
        order: 'desc',
        devices
      });
    }
    ```

1. この新しい関数を使用して、テレメトリ グラフに入力するには、`src/components/pages/dashboard/dashboard.js` ファイルを開きます。 テレメトリ ストリームを初期化する行を見つけて、それを次のように変更します。

    ```javascript
    const getTelemetryStream = ({ deviceIds = [] }) => TelemetryService.getTelemetryByDeviceIdP5M(deviceIds)
    ```

テレメトリ グラフに、5 分間のテレメトリ データが表示されるようになりました。

![1 日を示すテレメトリ グラフ](./media/iot-accelerators-remote-monitoring-customize/telemetry-period.png)

## <a name="add-a-new-kpi"></a>新しい KPI の追加

**ダッシュボード** ページには、**Analytics** パネルの KPI が表示されます。 これらの KPI は、`src/components/pages/dashboard/dashboard.js` ファイルで計算されます。 KPI は `src/components/pages/dashboard/panels/analytics/analyticsPanel.js` ファイルによってレンダリングされます。 次の手順で、新しい KPI 値を計算して、**ダッシュ ボード** ページにレンダリングする方法について説明します。 示している例では、警告アラート KPI に新しいパーセンテージの変更を追加しています。

1. `src/components/pages/dashboard/dashboard.js` ファイルを開きます。 **initialState** オブジェクトを次のように **warningAlertsChange** プロパティを含めるように変更します。

    ```javascript
    const initialState = {
      ...

      // Analytics data
      analyticsVersion: 0,
      currentActiveAlerts: [],
      topAlerts: [],
      alertsPerDeviceId: {},
      criticalAlertsChange: 0,
      warningAlertsChange: 0,
      analyticsIsPending: true,
      analyticsError: null

      ...
    };
    ```

1. **currentAlarmsStats** オブジェクトを **totalWarningCount** をプロパティとして含めるように変更します。

    ```javascript
    return {
      openWarningCount: (acc.openWarningCount || 0) + (isWarning && isOpen ? 1 : 0),
      openCriticalCount: (acc.openCriticalCount || 0) + (isCritical && isOpen ? 1 : 0),
      totalWarningCount: (acc.totalWarningCount || 0) + (isWarning ? 1 : 0),
      totalCriticalCount: (acc.totalCriticalCount || 0) + (isCritical ? 1 : 0),
      alertsPerDeviceId: updatedAlertsPerDeviceId
    };
    ```

1. 新しい KPI を計算します。 重大なアラート数の計算を見つけます。 コードをコピーし、次のようにコピーを変更します。

    ```javascript
    // ================== Warning Alerts Count - START
    const currentWarningAlerts = currentAlertsStats.totalWarningCount;
    const previousWarningAlerts = previousAlerts.reduce(
      (cnt, { severity }) => severity === Config.ruleSeverity.warning ? cnt + 1 : cnt,
      0
    );
    const warningAlertsChange = ((currentWarningAlerts - previousWarningAlerts) / currentWarningAlerts * 100).toFixed(2);
    // ================== Warning Alerts Count - END
    ```

1. KPI ストリームに新しい **warningAlertsChange** を含めます。

    ```javascript
    return ({
      analyticsIsPending: false,
      analyticsVersion: this.state.analyticsVersion + 1,

      // Analytics data
      currentActiveAlerts,
      topAlerts,
      criticalAlertsChange,
      warningAlertsChange,
      alertsPerDeviceId: currentAlertsStats.alertsPerDeviceId,

      ...
    });
    ```

1. UI のレンダリングに使用される状態データに新しい **warningAlertsChange** を含めます。

    ```javascript
    const {
      ...

      analyticsVersion,
      currentActiveAlerts,
      topAlerts,
      alertsPerDeviceId,
      criticalAlertsChange,
      warningAlertsChange,
      analyticsIsPending,
      analyticsError,

      ...
    } = this.state;
    ```

1. KPI パネルに渡されるデータを更新します。

    ```javascript
    <AnalyticsPanel
      timeSeriesExplorerUrl={timeSeriesParamUrl}
      topAlerts={topAlertsWithName}
      alertsPerDeviceId={alertsPerDeviceType}
      criticalAlertsChange={criticalAlertsChange}
      warningAlertsChange={warningAlertsChange}
      isPending={analyticsIsPending || rulesIsPending || devicesIsPending}
      error={devicesError || rulesError || analyticsError}
      theme={theme}
      colors={chartColorObjects}
      t={t} />
    ```

これで、`src/components/pages/dashboard/dashboard.js` ファイルの変更が完了しました。 次の手順では、新しい KPI を表示するために、`src/components/pages/dashboard/panels/analytics/analyticsPanel.js` ファイルに行う変更について説明します。

1. 次のように新しい KPI 値を取得するために、次のコードの行を変更します。

    ```javascript
    const { t, isPending, criticalAlertsChange, warningAlertsChange, alertsPerDeviceId, topAlerts, timeSeriesExplorerUrl, error } = this.props;
    ```

1. 次のように、新しい KPI 値を表示するために、マークアップを変更します。

    ```javascript
    <div className="analytics-cell">
      <div className="analytics-header">{t('dashboard.panels.analytics.criticalAlerts')}</div>
      <div className="critical-alerts">
        {
          !showOverlay &&
            <div className="analytics-percentage-container">
              <div className="analytics-value">{ !isNaN(criticalAlertsChange) ? criticalAlertsChange : 0 }</div>
              <div className="analytics-percentage-sign">%</div>
            </div>
        }
      </div>
      <div className="critical-alerts">
        {
          !showOverlay &&
            <div className="analytics-percentage-container">
              <div className="analytics-value">{ !isNaN(warningAlertsChange) ? warningAlertsChange : 0 }</div>
              <div className="analytics-percentage-sign">%</div>
            </div>
        }
      </div>
    </div>
    ```

これで、**ダッシュボード** ページに、新しい KPI 値が表示されるようになります。

![警告 KPI](./media/iot-accelerators-remote-monitoring-customize/new-kpi.png)

## <a name="customize-the-map"></a>マップをカスタマイズする

ソリューションのマップ コンポーネントの詳細については、GitHub の[マップのカスタマイズ](https://github.com/Azure/azure-iot-pcs-remote-monitoring-dotnet/wiki/Developer-Reference-Guide#upgrade-map-key-to-see-devices-on-a-dynamic-map)に関するページをご覧ください。

<!--
### Connect an external visualization tool

See the [Connect an external visualization tool](https://github.com/Azure/azure-iot-pcs-remote-monitoring-dotnet/) page in GitHub for details of how to connect an external visualization tool.

-->

## <a name="other-customization-options"></a>その他のカスタマイズのオプション

リモート監視ソリューションのプレゼンテーションおよび視覚化レイヤーをさらに変更するために、コードを編集します。 関連する GitHub リポジトリは次のとおりです。

* [Azure IoT ソリューションの構成マイクロサービス (.NET)](https://github.com/Azure/remote-monitoring-services-dotnet/tree/master/config)
* [Azure IoT ソリューションの構成マイクロサービス (Java)](https://github.com/Azure/remote-monitoring-services-java/tree/master/config)
* [Azure IoT PCS リモート監視 Web UI](https://github.com/Azure/pcs-remote-monitoring-webui)

## <a name="next-steps"></a>次の手順

この記事では、リモート監視ソリューション アクセラレータの Web UI のカスタマイズに役立つように使用できるリソースについて説明します。 UI のカスタマイズに関する詳細については、次の記事を参照してください。

* [リモート監視ソリューション アクセラレータの Web UI にカスタム ページを追加する](iot-accelerators-remote-monitoring-customize-page.md)
* [リモート監視ソリューション アクセラレータの Web UI にカスタム サービスを追加する](iot-accelerators-remote-monitoring-customize-service.md)
* [リモート監視ソリューション アクセラレータの Web UI にカスタム グリッドを追加する](iot-accelerators-remote-monitoring-customize-grid.md)
* [リモート監視ソリューション アクセラレータの Web UI にカスタム ポップアップを追加する](iot-accelerators-remote-monitoring-customize-flyout.md)
* [リモート監視ソリューション アクセラレータの Web UI のダッシュボードにカスタム パネルを追加する](iot-accelerators-remote-monitoring-customize-panel.md)

リモート監視ソリューション アクセラレータの概念に関する詳細については、[リモート監視のアーキテクチャ](iot-accelerators-remote-monitoring-sample-walkthrough.md)に関するページをご覧ください

リモート監視ソリューション マイクロサービスのカスタマイズの詳細については、「[マイクロサービスをカスタマイズして再展開する](iot-accelerators-microservices-example.md)」をご覧ください。
<!-- Next tutorials in the sequence -->
