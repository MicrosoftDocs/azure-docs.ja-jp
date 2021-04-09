---
title: Azure FarmBeats のトラブルシューティング
description: この記事では、Azure FarmBeats のトラブルシューティングの方法について説明します。
author: uhabiba04
ms.topic: article
ms.date: 11/04/2019
ms.author: v-ummehabiba
ms.openlocfilehash: c45b6196b82682b37e253a33eed3940b68b4d61e
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "102172986"
---
# <a name="troubleshoot-azure-farmbeats"></a>Azure FarmBeats のトラブルシューティング

この記事では、Azure FarmBeats の一般的な問題の解決策を示します。 さらに支援が必要な場合は、[Q&A サポート フォーラム](/answers/topics/azure-farmbeats.html)に問い合わせるか、farmbeatssupport@microsoft.com にメールでご連絡ください。

> [!NOTE]
  > 4 月中に FarmBeats をインストールし、ジョブが空のエラー メッセージで失敗する場合は、重要な保健機関や安全機関へのサポートを優先するために、インストールにバッチ クォータが割り当てられていない可能性があります。 詳細については、 [こちら](https://azure.microsoft.com/blog/update-2-on-microsoft-cloud-services-continuity/) をご覧ください。 ジョブを正常に実行するには、Batch アカウントへの VM の割り当てを要求する必要があります。

## <a name="install-issues"></a>インストールの問題

  > [!NOTE]
  > エラーが発生したためにインストールを開始し直す場合は、インストールを再トリガーする前に、必ず **リソース グループ** を削除するか、リソース グループからすべてのリソースを削除してください。

### <a name="invalid-sentinel-credentials"></a>無効な Sentinel 資格情報

インストール中に指定した Sentinel 資格情報が正しくありません。 正しい資格情報を使用してインストールを再度開始します。

### <a name="the-regional-account-quota-of-batch-accounts-for-the-specified-subscription-has-been-reached"></a>指定したサブスクリプションに対する Batch アカウントのリージョンのアカウント クォータに達した

クォータを増やすか、未使用の Batch アカウントを削除して、インストールを再度開始します。

### <a name="invalid-resource-group-location"></a>無効なリソース グループの場所

**リソース グループ** が、インストール時に指定した **リージョン** と同じ場所にあることを確認します。

### <a name="other-install-issues"></a>他のインストールの問題

以下の詳細を添えてご連絡ください。

- サブスクリプション ID
- リソース グループ名
- デプロイ エラーのログ ファイルを添付するには、次の手順のようにします。

    1. Azure portal で **[リソース グループ]** に移動します。

    2. 左側の **[設定]** セクションで **[デプロイ]** を選択します。

    3. **[失敗]** と表示されているすべてのデプロイについて、展開の詳細を選択してダウンロードします。 このファイルをメールに添付します。

## <a name="sensor-telemetry"></a>センサー テレメトリ

### <a name="cant-view-telemetry-data"></a>テレメトリ データを表示できない

**現象**:デバイスまたはセンサーをデプロイし、FarmBeats とデバイス パートナーをリンクさせましたが、FarmBeats でテレメトリ データの取得や表示ができません。

**是正措置**:

1. FarmBeats リソース グループにアクセスします。
2. **イベント ハブ** 名前空間 ("sensor-partner-eh-namespace-xxxx") を選択し、[Event Hubs] をクリックして、パートナーに割り当てられているイベント ハブの受信メッセージの数を確認します
3. 以下のいずれかを実行します。

   - "*受信メッセージがない*" 場合は、デバイス パートナーにお問い合わせください。  
   - "*受信メッセージ*" がある場合は、データハブと Accelerator のログおよびキャプチャしたテレメトリを添えてご連絡てください。

ログのダウンロード方法の詳細については、「[ログを手動で収集する](#collect-logs-manually)」セクションを参照してください。  

### <a name="cant-view-telemetry-data-after-ingesting-historicalstreaming-data-from-your-sensors"></a>センサーから履歴またはストリーミング データを取り込んだ後で、テレメトリ データを表示できない

**現象**:デバイスまたはセンサーがデプロイされており、FarmBeats 上でデバイスまたはセンサーを作成し、EventHub にテレメトリを取り込みましたが、FarmBeats 上でテレメトリ データを取得または表示することができません。

**是正措置**:

1. パートナー登録を確実に正しく完了させます。これを確認するには、ご利用の Datahub Swagger にアクセスし、/Partner API に移動し、Get を実行して、パートナーが登録されているかどうかを確認します。 そうでない場合は、こちらの[手順](get-sensor-data-from-sensor-partner.md#enable-device-integration-with-farmbeats)に従ってパートナーを追加します。

2. 次に示す、正しいテレメトリ メッセージ形式を確実に使用します。

```json
{
"deviceid": "<id of the Device created>",
"timestamp": "<timestamp in ISO 8601 format>",
"version" : "1",
"sensors": [
    {
      "id": "<id of the sensor created>",
      "sensordata": [
        {
          "timestamp": "< timestamp in ISO 8601 format >",
          "<sensor measure name (as defined in the Sensor Model)>":<value>
        },
        {
          "timestamp": "<timestamp in ISO 8601 format>",
          "<sensor measure name (as defined in the Sensor Model)>": <value>
        }
      ]
    }
 ]
}
```

### <a name="dont-have-the-azure-event-hubs-connection-string"></a>Azure Event Hubs の接続文字列がない

**是正措置**:

1. Datahub Swagger でパートナー API にアクセスします。
2. **[Get]\(取得\)**  >  **[Try it out]\(使ってみる\)**  >  **[Execute]\(実行\)** の順に選択します。

> [!NOTE]
> 関心のあるセンサー パートナーのパートナー ID。

3. パートナー API に戻り、 **[Get/\<ID>] (<ID> を取得)** を選択します。
4. 手順 3. のパートナー ID を指定して、 **[Execute]\(実行\)** を選択します。

   API の応答に、Event Hubs の接続文字列が含まれているはずです。

### <a name="device-appears-offline"></a>デバイスがオフラインとして表示される

**現象**:デバイスをインストールし、FarmBeats とデバイス パートナーをリンクしています。 デバイスはオンラインであり、テレメトリ データを送信していますが、オフラインとして表示されます。

**是正措置** このデバイスのレポート間隔が構成されていません。 レポート間隔を設定するには、お使いのデバイスの製造元に問い合わせてください。 

### <a name="error-deleting-a-device"></a>デバイスの削除エラー

デバイスの削除中に、次の一般的なエラー シナリオのいずれかが発生することがあります。  

**メッセージ**:"Device is referenced in sensors: (デバイスはセンサーで参照されています。) There are one or more sensors associated with the device. (デバイスに関連付けられているセンサーが 1 つ以上あります。) Delete the sensors and then delete the device. (センサーを削除してから、デバイスを削除してください。)"  

**意味**:デバイスが、ファームにデプロイされている複数のセンサーに関連付けられています。

**是正措置**:  

1. Accelerator を使用して、デバイスに関連付けられているセンサーを削除します。  
2. センサーを別のデバイスに関連付ける場合は、デバイス パートナーに依頼してこの操作を行ってもらいます。  
3. `DELETE API` 呼び出しを使用してデバイスを削除し、force パラメーターを *true* に設定します。  

**メッセージ**:"Device is referenced in devices as ParentDeviceId: (デバイスは、他のデバイスから ParentDeviceId として参照されています。) このデバイスには、子デバイスとして関連付けられているデバイスが 1 つ以上あります。 Delete them, and then delete this device. (これらを削除してから、このデバイスを削除してください。)"  

**意味**:自分のデバイスに他のデバイスが関連付けられています。  

**是正措置**:

1. 対象のデバイスに関連付けられているデバイスを削除します。  
2. 対象のデバイスを削除します。  

    > [!NOTE]
    > センサーが関連付けられている場合、デバイスを削除することはできません。 関連付けられているセンサーの削除方法の詳細については、「[センサー パートナーからセンサー データを取得する](get-sensor-data-from-sensor-partner.md)」の「**センサーの削除**」セクションを参照してください。
    > パートナーには、デバイスまたはセンサーを削除するためのアクセス許可がありません。 管理者だけが削除のためのアクセス許可を持っています。

## <a name="issues-with-jobs"></a>ジョブの問題

### <a name="farmbeats-internal-error"></a>FarmBeats の内部エラー

**メッセージ**:"FarmBeats internal error, see troubleshooting guide for more details. (FarmBeats の内部エラーです。詳細については、トラブルシューティング ガイドを参照してください。)"

**是正措置** この問題の原因は、データ パイプラインの一時的な障害である可能性があります。 もう一度ジョブを作成してください。 エラーが引き続き発生する場合は、エラー メッセージとログを添えてお問い合わせください。

## <a name="accelerator-troubleshooting"></a>Accelerator のトラブルシューティング

### <a name="access-control"></a>アクセス制御

**問題点**: ロールの割り当ての追加中にエラーが発生しました。

**メッセージ**:"検索条件に一致するユーザーが見つかりません。"

**是正措置** ロールの割り当てを追加しようとしている、対象のメール ID を確認します。 メール ID は、Active Directory Domain Services でそのユーザーに登録されている ID と完全に一致している必要があります。 エラーが引き続き発生する場合は、エラー メッセージとログを添えてお問い合わせください。

### <a name="unable-to-log-in-to-accelerator"></a>Accelerator にログインできない

**メッセージ**:"エラー: サービスを呼び出す権限がありません。 管理者に承認を依頼してください。"

**是正措置** FarmBeats のデプロイへのアクセスを承認するように管理者に依頼してください。 これを行うには、RoleAssignment API シリーズの POST を実行するか、Accelerator の **[設定]** ペインのアクセス制御を使用します。  

既にアクセスを付与されているにもかかわらずこのエラーが発生する場合は、ページを最新の情報に更新して再試行してください。 エラーが引き続き発生する場合は、エラー メッセージとログを添えてお問い合わせください。

![認可エラーを示すスクリーンショット。](./media/troubleshoot-azure-farmbeats/accelerator-troubleshooting-1.png)

### <a name="accelerator-issues"></a>Accelerator の問題  

**問題点**: 原因不明の Accelerator エラーが発生しました。

**メッセージ**:"エラー: 不明なエラーが発生しました。"

**是正措置** このエラーは、ページを長時間アイドル状態のままにした場合に発生します。 ページを更新します。 エラーが引き続き発生する場合は、エラー メッセージとログを添えてお問い合わせください。

**問題点**: FarmBeatsDeployment をアップグレードした後でも、FarmBeats Accelerator で最新バージョンが表示されません。

**是正措置** このエラーは、ブラウザーでサービス ワーカーが永続化されていることによって発生します。 次の操作を行います。

1. Accelerator が開いているすべてのブラウザーのタブを閉じ、ブラウザー ウィンドウを閉じます。
2. ブラウザーの新しいインスタンスを起動し、Accelerator URI を再度読み込みます。 この操作により、新バージョンの Accelerator が読み込まれます。

## <a name="sentinel-imagery-related-issues"></a>Sentinel: 画像関連の問題

### <a name="wrong-username-or-password"></a>ユーザー名またはパスワードが間違っている

**ジョブ エラー メッセージ**:"Full authentication is required to access this resource. (このリソースにアクセスするには完全な認証が必要です。)"

**是正措置**:次のいずれかの操作を行います。

- 次の手順に従い、正しいユーザー名とパスワードで FarmBeats を更新した後、ジョブを再試行します。

  **Sentinel のユーザー名を更新する**

    1. [Azure ポータル](https://portal.azure.com)にサインインします。
    2. **[検索]** ボックスで、FarmBeats Datahub リソース グループを探します。
    3. [ストレージ アカウント storage*****] > **[コンテナー]**  > **batch-prep-files** > **to_vm** > **config.ini** を選択します
    4. **[編集]** を選択します。
    5. sentinel_account セクションでユーザー名を更新します

  **Sentinel のパスワードを更新する**

    1. [Azure ポータル](https://portal.azure.com)にサインインします。
    2. **[検索]** ボックスで、FarmBeats Datahub リソース グループを探します。
    3. keyvault-***** を選択します
    4. [設定] で [アクセス ポリシー] を選択します
    5. **[アクセス ポリシーの追加]** を選択します
    6. テンプレートからの構成の **[シークレットの管理]** を使用して、自分自身をプリンシパルに追加します
    7. **[追加]** を選択し、 **[アクセス ポリシー]** ページで **[保存]** を選択します
    8. **[設定]** で、 **[シークレット]** を選択します
    9. **Sentinel-password** を選択します
    10. 新しいバージョンの値を作成して有効にします。

- 失敗したジョブを再実行するか、5 日から 7 日の範囲のサテライト インデックス ジョブを実行してから、ジョブが成功したかどうかを確認します。

### <a name="sentinel-hub-wrongurlor-site-not-accessible"></a>Sentinel ハブ: URL が間違っているか、サイトにアクセスできない

**ジョブ エラー メッセージ**:"Oops, something went wrong. (問題が発生しました。) The page you were trying to access is (temporarily) unavailable. (アクセスしようとしていたページは、(一時的に) 利用できなくなっています。)"

**是正措置**:

1. ブラウザーで [Sentinel](https://scihub.copernicus.eu/dhus/) を開き、Web サイトにアクセスできるかどうかを確認します。
2. Web サイトにアクセスできない場合は、ファイアウォール、社内ネットワーク、またはその他のアクセス制限ソフトウェアにより、Web サイトへのアクセスが妨げられていないかどうかを確認してから、Sentinel URL の許可に必要な手順を実行します。 
3. 失敗したジョブを再実行するか、5 日から 7 日の範囲のサテライト インデックス ジョブを実行してから、ジョブが成功したかどうかを確認します。  

### <a name="sentinel-server-down-for-maintenance"></a>Sentinel サーバー: メンテナンスのためのダウン

**ジョブ エラー メッセージ**:"The Copernicus Open Access Hub will be back soon! (Copernicus Open Access Hub は間もなく復旧します。) ご不便をおかけして申し訳ありません。しばらくの間、いくつかのメンテナンスを行っています。 We'll be back online shortly! (すぐにオンラインに戻ります。)" 

**是正措置**:

この問題は、Sentinel サーバーでメンテナンス作業が行われている場合に発生する可能性があります。

1. メンテナンスが実施されていることが原因でジョブまたはパイプラインが失敗した場合は、しばらくしてからジョブを再送信してください。 

   Sentinel のメンテナンス活動の詳細については、[Copernicus Open Access Hub のニュース](https://scihub.copernicus.eu/news/)に関するサイトを参照してください。  

2. 失敗したジョブを再実行するか、5 日から 7 日の範囲のサテライト インデックス ジョブを実行してから、ジョブが成功したかどうかを確認します。

### <a name="sentinel-maximum-number-of-connections-reached"></a>Sentinel: 接続数の上限に到達した

**ジョブ エラー メッセージ**:"ユーザー '\<username>' が実行できる 2 つの同時実行フローの最大数。"

**意味**:接続数の上限に到達したためにジョブが失敗した場合、同じ Sentinel アカウントが複数のジョブで使用されています。

**是正措置**:次のいずれかを試します。

* 他のジョブが完了するのを待ってから、失敗したジョブを再実行します。
* 新しい Sentinel アカウントを作成し、FarmBeats で Sentinel のユーザー名とパスワードを更新します。

### <a name="sentinel-server-refused-connection"></a>Sentinel サーバー: 接続の拒否

**ジョブ エラー メッセージ**:"Server refused connection at: "http://172.30.175.69:8983/solr/dhus". ("サーバーが次の場所で接続を拒否しました: "http://172.30.175.69:8983/solr/dhus"。)

**是正措置**:この問題は、Sentinel サーバーでメンテナンス作業が行われている場合に発生する可能性があります。

1. メンテナンスが実施されていることが原因でジョブまたはパイプラインが失敗した場合は、しばらくしてからジョブを再送信してください。

   Sentinel のメンテナンス活動の詳細については、[Copernicus Open Access Hub のニュース](https://scihub.copernicus.eu/news/)に関するサイトを参照してください。  

2. 失敗したジョブを再実行するか、5 日から 7 日の範囲のサテライト インデックス ジョブを実行してから、ジョブが成功したかどうかを確認します。

### <a name="soil-moisture-map-has-white-areas"></a>土壌水分マップに空白がある

**問題点**: **土壌水分マップ** が生成されましたが、マップのほとんどの部分が空白です。

**是正措置**:この問題は、マップが要求された時間に対して生成されたサテライト インデックスの NDVI 値が 0.3 未満の場合に発生する可能性があります。 詳細については、「[Sentinel の テクニカルガイド](https://earth.esa.int/web/sentinel/technical-guides/sentinel-2-msi/level-2a/algorithm)」を参照してください。

1. 別の日付範囲でジョブをもう一度実行し、サテライト インデックスの NDVI 値が 0.3 より大きいかどうかを確認します。

## <a name="collect-logs-manually"></a>ログを手動で収集する

[Azure Storage Explorer をインストールしてデプロイします]( https://docs.microsoft.com/azure/vs-azure-tools-storage-manage-with-storage-explorer?tabs=windows)。

### <a name="collect-azure-data-factory-job-logs-or-app-service-logs-in-datahub"></a>データハブで Azure Data Factory ジョブのログまたは App Service のログを収集する

1. [Azure portal](https://portal.azure.com) にサインインします。
2. **[検索]** ボックスで、FarmBeats Datahub リソース グループを探します。
3. **[リソース グループ]** ダッシュボードで、*datahublogs\** ストレージ アカウントを探します。 たとえば、*datahublogsmvxmq* のようになります。  
4. **[名前]** 列でストレージ アカウントを選択し、 **[ストレージ アカウント]** ダッシュボードを表示します。
5. **[datahubblogs\*] *_ ペインで _* [Explorer で開く]** を選択し、 **[Azure Storage Explorer を開く]** アプリケーションを表示します。
6. 左側のペインで **[BLOB コンテナー]** を選択した後、Azure Data Factory ログの **job-logs** または App Service ログの **appinsights-logs** を選択します。
7. **[ダウンロード]** を選択して、コンピューター上のローカル フォルダーにログをダウンロードします。

    ![ダウンロードされたログ ファイルを示すスクリーンショット。](./media/troubleshoot-azure-farmbeats/collecting-logs-manually-1.png)

### <a name="collect-azure-data-factory-job-logs-or-app-service-logs-for-accelerator"></a>Accelerator に対する Azure Data Factory ジョブのログまたは App Service のログを収集する

1. [Azure portal](https://portal.azure.com) にサインインします。
2. **[検索]** ボックスで、FarmBeats Accelerator リソース グループを探します。
3. **[リソース グループ]** ダッシュボードで、*storage\** ストレージ アカウントを探します。 たとえば、*storagedop4k\** のようになります。
4. **[名前]** 列のストレージ アカウントを選択し、**ストレージ アカウント** ダッシュボードを表示します。
5. **[storage\*] *_ ペインで _* [Explorer で開く]** を選択し、Azure Storage Explorer アプリケーションを開きます。
6. 左側のペインで **[BLOB コンテナー]** を選択した後、Azure Data Factory ログの **job-logs** または App Service ログの **appinsights-logs** を選択します。
7. **[ダウンロード]** を選択して、コンピューター上のローカル フォルダーにログをダウンロードします。

## <a name="high-cpu-usage"></a>CPU 使用率が高い

**Error**: "**高 CPU 使用率アラート**" メールを受け取りました。

**是正措置**:

1. FarmBeats Datahub リソース グループにアクセスします。
2. **App Service** を選択します。  
3. [App Service の価格のスケールアップ](https://azure.microsoft.com/pricing/details/app-service/windows/)に関するページにアクセスし、適切な価格レベルを選択します。

## <a name="weather-data-job-failures"></a>気象データ ジョブの失敗

**Error**: 気象データを取得するジョブを実行したが、ジョブが失敗する

### <a name="collect-logs-to-troubleshoot-weather-data-job-failures"></a>ログを収集して、気象データ ジョブの失敗をトラブルシューティングする

1. Azure portal で FarmBeats リソース グループに移動します。
2. リソース グループに含まれる Data Factory サービスをクリックします。 サービスには "sku:Datahub" というタグが付きます

> [!NOTE]
> リソース グループ内のサービスのタグを表示するには、[列の編集] をクリックし、リソース グループ ビューに [タグ] を追加します。

:::image type="content" source="./media/troubleshoot-Azure-farmbeats/weather-log-1.png" alt-text="Sku: Datahub タグが強調表示されているスクリーンショット。":::

3. Data Factory の [概要] ページで、 **[Author and Monitor]\(作成と監視\)** をクリックします。 ブラウザーに新しいタブが表示されます。 **[監視]** をクリックします。

:::image type="content" source="./media/troubleshoot-Azure-farmbeats/weather-log-2.png" alt-text="[監視] メニュー オプションが強調表示されているスクリーンショット。":::

4. 気象ジョブの実行の一部であるパイプライン実行の一覧が表示されます。 ログを収集するジョブをクリックします。
 
:::image type="content" source="./media/troubleshoot-Azure-farmbeats/weather-log-3.png" alt-text="[パイプラインの実行] メニュー オプションと選択されたジョブが強調表示されているスクリーンショット。":::

5. パイプラインの概要ページに、アクティビティの実行の一覧が表示されます。 ログを収集するアクティビティの実行 ID をメモします。
 
:::image type="content" source="./media/troubleshoot-Azure-farmbeats/weather-log-4.png" alt-text="アクティビティの実行の一覧を示すスクリーンショット。":::

6. Azure portal の FarmBeats リソース グループに戻り、名前が **datahublogs-XXXX** のストレージ アカウントをクリックします。
 
:::image type="content" source="./media/troubleshoot-Azure-farmbeats/weather-log-5.png" alt-text="datahublogs-XXXX という名前のストレージ アカウントが強調表示されているスクリーンショット。":::

7. **[Containers]\(コンテナー\)**  ->  **[adfjobs]** をクリックします。 検索ボックスに、上記の手順 5 でメモしたジョブの実行 ID を入力します。
 
:::image type="content" source="./media/troubleshoot-Azure-farmbeats/weather-log-6.png" alt-text="プロジェクト FarmBeats":::

8. 検索結果には、このジョブに関連するログを持つフォルダーが表示されます。 問題のデバッグの支援を受けるには、ログをダウンロードして farmbeatssupport@microsoft.com に送信してください。