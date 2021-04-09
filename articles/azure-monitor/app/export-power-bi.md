---
title: Azure Application Insights から Power BI にエクスポートする | Microsoft Docs
description: Power BI で Analytics クエリを表示できます。
ms.topic: conceptual
ms.date: 08/10/2018
ms.openlocfilehash: 4bdae09d193d5ae9c59fe9352e07b8219a76841d
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "100584303"
---
# <a name="feed-power-bi-from-application-insights"></a>Application Insights のデータを Power BI に入力する
[Power BI](https://www.powerbi.com/) は、データを分析し、洞察を共有できる一連のビジネス ツールです。 あらゆるデバイスで機能豊富なダッシュボードを利用できます。 [Azure Application Insights](./app-insights-overview.md) の Analytics クエリなど、さまざまなソースのデータを組み合わせることができます。

Application Insights のデータを Power BI にエクスポートする場合、3 つの方法があります。

* [**Analytics クエリのエクスポート**](#export-analytics-queries)。 可能であればこの方法の使用をお勧めします。 必要なクエリを作成し、Power BI にエクスポートします。 このクエリは、他のデータと共にダッシュボードに配置できます。
* [**連続エクスポートと Azure Stream Analytics**](./export-stream-analytics.md)。 この方法は、データを長期間保存する必要がある場合に便利です。 長期にわたるデータ保有要件がない場合は、Analytics クエリのエクスポートの方法を使用します。 連続エクスポートと Stream Analytics では、多くの設定作業と追加のストレージ オーバーヘッドが必要になります。
* **Power BI アダプター**。 グラフ セットが事前定義されていますが、他のソースの独自のクエリを追加できます。

> [!NOTE]
> 現在、Power BI アダプターは **非推奨** となっています。 このソリューションの定義済みのグラフは、編集できない静的なクエリによって入力されます。 これらのクエリを編集することはできません。データの特定のプロパティによっては、Power BI への接続が成功する可能性がありますが、データは入力されません。 これは、ハードコードされたクエリ内で設定されている除外条件によるものです。 一部のお客様はこのソリューションをまだ利用できる場合がありますが、アダプターの柔軟性が不足しているため、[**Analytics クエリのエクスポート**](#export-analytics-queries)機能を使用するというソリューションをお勧めします。

## <a name="export-analytics-queries"></a>Analytics クエリのエクスポート
この方法では、必要な Analytics クエリを作成するか、使用状況フィルターからエクスポートした後、それを Power BI ダッシュボードにエクスポートできます (アダプターによって作成されたダッシュボードに追加できます)。

### <a name="one-time-install-power-bi-desktop"></a>1 回限り: Power BI Desktop のインストール
Application Insights のクエリをインポートするには、Power BI のデスクトップ バージョンを使用します。 その後、Web または Power BI クラウド ワークスペースにクエリを発行できます。 

[Power BI Desktop](https://powerbi.microsoft.com/en-us/desktop/) をインストールします。

### <a name="export-an-analytics-query"></a>Analytics クエリのエクスポート
1. [Analytics を開き、クエリを作成](../logs/log-analytics-tutorial.md)します。
2. 満足のいく結果が得られるまで、クエリをテストして改善します。 エクスポートする前に、クエリが Analytics で正しく実行されることを確認します。
3. **[エクスポート]** メニューの **[Power BI (M)]** を選択します。 テキスト ファイルを保存します。
   
    ![[エクスポート] メニューが強調表示されている Analytics のスクリーンショット](./media/export-power-bi/analytics-export-power-bi.png)
4. Power BI Desktop で、 **[データの取得]**  >  **[空のクエリ]** の順に選びます。 その後、クエリ エディターで、 **[表示]** の **[詳細エディター]** を選びます。

    エクスポートした M 言語スクリプトを詳細エディターに貼り付けます。

    ![詳細エディターが強調表示されている Power BI Desktop のスクリーンショット](./media/export-power-bi/power-bi-import-analytics-query.png)

5. Power BI が Azure にアクセスできるようにするには、資格情報の指定が必要な場合があります。 **[組織アカウント]** を使って、Microsoft アカウントでサインインします。
   
    ![Power BI の [クエリの設定] ダイアログ ボックスのスクリーンショット](./media/export-power-bi/power-bi-import-sign-in.png)

    資格情報を確認する必要がある場合は、クエリ エディターの **[データ ソース設定]** メニュー コマンドを使います。 Azure 用の資格情報を指定するように注意してください。これは Power BI 用の資格情報とは異なる可能性があります。
6. クエリの視覚エフェクトを選び、x 軸、y 軸、セグメント化ディメンションの各フィールドを選びます。
   
    ![Power BI Desktop の視覚エフェクト オプションのスクリーンショット](./media/export-power-bi/power-bi-analytics-visualize.png)
7. Power BI クラウド ワークスペースにレポートを発行します。 そこから、同期されたバージョンを他の Web ページに埋め込むことができます。
   
    ![[発行] ボタンが強調表示されている Power BI Desktop のスクリーンショット](./media/export-power-bi/publish-power-bi.png)
8. レポートを周期的に手動で更新するか、オプション ページで更新のスケジュールを設定します。

### <a name="export-a-funnel"></a>フィルターをエクスポートする
1. [フィルターを作成します](./usage-funnels.md)。
2. **[Power BI]** を選択します。

   ![Power BI ボタンのスクリーンショット](./media/export-power-bi/button.png)

3. Power BI Desktop で、 **[データの取得]**  >  **[空のクエリ]** の順に選びます。 その後、クエリ エディターで、 **[表示]** の **[詳細エディター]** を選びます。

   ![[空のクエリ] ボタンが強調表示されている Power BI Desktop のスクリーンショット](./media/export-power-bi/blankquery.png)

   エクスポートした M 言語スクリプトを詳細エディターに貼り付けます。 

   ![スクリーンショットには、詳細エディターを強調表示した Power BI Desktop が示されています](./media/export-power-bi/advancedquery.png)

4. クエリから項目を選び、[フィルター] 視覚エフェクトを選びます。

   ![スクリーンショットには、Power BI Desktop Funnel の視覚エフェクト オプションが示されています](./media/export-power-bi/selectsequence.png)

5. わかりやすいタイトルに変更し、レポートを Power BI クラウド ワークスペースに発行します。 

   ![タイトルの変更が強調表示されている Power BI Desktop のスクリーンショット](./media/export-power-bi/changetitle.png)

## <a name="troubleshooting"></a>トラブルシューティング

資格情報またはデータセットのサイズに関連するエラーが発生することがあります。 ここでは、これらのエラーへの対処方法について説明します。

### <a name="unauthorized-401-or-403"></a>権限がない (401 または 403)
これは、更新トークンが更新されていない場合に発生することがあります。 次の手順で、まだアクセス権があることを確認します。

1. Azure portal にサインインし、リソースにアクセスできることを確認します。
2. ダッシュボードの資格情報を更新します。
3. Power BI Desktop からキャッシュをクリアします。


   アクセス権があり、資格情報の更新がうまくいかない場合は、サポート チケットを開いてください。

### <a name="bad-gateway-502"></a>無効なゲートウェイ (502)
これは通常、分析クエリで大量のデータが返される場合に発生します。 もっと小さい時間の範囲をクエリに使ってみてください。 

Analytics クエリから取得するデータセットを小さくすると要件を満たせない場合は、[API](https://dev.applicationinsights.io/documentation/overview) を使ってより大きいデータセットをプルすることを検討します。 M クエリのエクスポートを変換して API を使う方法を次に示します。

1. [API キー](https://dev.applicationinsights.io/documentation/Authorization/API-key-and-App-ID)を作成します。
2. Azure Resource Manager の URL を Application Insights の API に置き換えて、Analytics からエクスポートした Power BI の M スクリプトを更新します。
   * **https:\//management.azure.com/subscriptions/...** を
   * **https:\//api.applicationinsights.io/beta/apps/...** に置き換えます
3. 最後に、資格情報を基本に更新して、API キーを使います。

**元のスクリプト**
 ```
 Source = Json.Document(Web.Contents("https://management.azure.com/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourcegroups//providers/microsoft.insights/components//api/query?api-version=2014-12-01-preview",[Query=[#"csl"="requests",#"x-ms-app"="AAPBI"],Timeout=#duration(0,0,4,0)]))
 ```
**更新されたスクリプト**
 ```
 Source = Json.Document(Web.Contents("https://api.applicationinsights.io/beta/apps/<APPLICATION_ID>/query?api-version=2014-12-01-preview",[Query=[#"csl"="requests",#"x-ms-app"="AAPBI"],Timeout=#duration(0,0,4,0)]))
 ```

## <a name="about-sampling"></a>サンプリングについて
お使いのアプリケーションで送信されるデータの量によっては、ご自身のテレメトリの一部のみを送信するアダプティブ サンプリング機能を使用できます。 これは、SDK または取り込みでサンプリングを手動で設定した場合にも当てはまります。 [サンプリングの詳細についてはこちらを参照してください](./sampling.md)。

## <a name="power-bi-adapter-deprecated"></a>Power BI アダプター (非推奨)
この方法では、テレメトリの完全なダッシュボードが作成されます。 初期データ セットが事前定義されていますが、さらに多くのデータを追加できます。

### <a name="get-the-adapter"></a>アダプターの入手
1. [Power BI](https://app.powerbi.com/) にサインインします。
2. **[データの取得]** (![左下隅の GetData アイコンのスクリーンショット](./media/export-power-bi/001.png))、 **[サービス]** の順に開きます。

    ![スクリーンショットには、[サービス] ウィンドウの [取得] ボタンが示されています。](./media/export-power-bi/002.png)

3. Application Insights で **[今すぐ入手する]** を選択します。

   ![Application Insights データ ソースから取得する手順のスクリーンショット](./media/export-power-bi/003.png)
4. Application Insights リソースの詳細を入力し、**サインイン** します。

    ![スクリーンショットには、[Application Insights に接続する] ウィンドウが示されています。](./media/export-power-bi/005.png)

     この情報は、Application Insights の概要ウィンドウにあります。

     ![Application Insights データ ソースから取得する手順のスクリーンショット](./media/export-power-bi/004.png)

5. 新しく作成した Application Insights Power BI アプリを開きます。

6. データがインポートされるまで 1 ～ 2 分待ちます。

    ![Power BI アダプターのスクリーンショット](./media/export-power-bi/010.png)

Application Insights のグラフを他のソースのグラフや Analytics クエリと組み合わせて、ダッシュボードを編集できます。 ビジュアル ギャラリーではさらに多くのグラフを入手でき、各グラフにはユーザーが設定できるパラメーターがあります。

最初のインポート後は、ダッシュボードとレポートが毎日更新されます。 データセットの更新スケジュールを管理できます。

## <a name="next-steps"></a>次のステップ
* [Power BI - 詳細](https://www.powerbi.com/learning/)
* [Analytics のチュートリアル](../logs/log-analytics-tutorial.md)