---
title: "Stream Analytics を使って IoT ソリューションを構築する | Microsoft Docs"
description: "料金所ブースを例に Stream Analytics を使った基本的な IoT ソリューションを紹介します。"
keywords: "IOT ソリューション, ウィンドウ関数"
documentationcenter: 
services: stream-analytics
author: jeffstokes72
manager: jhubbard
editor: cgronlun
ms.assetid: a473ea0a-3eaa-4e5b-aaa1-fec7e9069f20
ms.service: stream-analytics
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: data-services
ms.date: 03/28/2017
ms.author: jeffstok
translationtype: Human Translation
ms.sourcegitcommit: d9dad6cff80c1f6ac206e7fa3184ce037900fc6b
ms.openlocfilehash: 01850cbf8967fd5f79db64103fb18c6ec01babea
ms.lasthandoff: 03/06/2017


---
# <a name="build-an-iot-solution-by-using-stream-analytics"></a>Stream Analytics を使って IoT ソリューションを構築する
## <a name="introduction"></a>はじめに
このチュートリアルでは、データに隠された知見を Azure Stream Analytics を使ってリアルタイムで突き止める方法を説明します。 開発者は、データのストリーム (クリック ストリーム、ログ、デバイスによって生成されたイベントなど) に履歴レコードや参照データを簡単に組み合わせて、ビジネスに関する知見を導き出すことができます。 Azure Stream Analytics は、Microsoft Azure の徹底した管理によってホストされたリアルタイム ストリーム計算処理サービスであるため、高い障害回復力とスケーラビリティ、低遅延が実現され、短時間での立ち上げが可能となっています。

このチュートリアルを読むと、次のことができるようになります。

* Azure Stream Analytics ポータルについて理解を深める。
* ストリーミング ジョブを構成、デプロイする。
* 現実世界の問題を明確化し、Stream Analytics クエリ言語を使って解決する。
* Stream Analytics を使って顧客向けのストリーミング ソリューションを開発できるという確信が得られる。
* 監視機能とログ機能の使用経験を問題のトラブルシューティングに活かす。

## <a name="prerequisites"></a>前提条件
このチュートリアルの前提条件は次のとおりです。

* [Azure PowerShell](/powershell/azureps-cmdlets-docs)
* Visual Studio 2017、2015、または無料の [Visual Studio Community](https://www.visualstudio.com/products/visual-studio-community-vs.aspx)
* [Azure サブスクリプション](https://azure.microsoft.com/pricing/free-trial/)
* コンピューターの管理特権
* [TollApp.zip](https://github.com/Azure/azure-stream-analytics/blob/master/Samples/TollApp/TollApp.zip) (Microsoft ダウンロード センターからダウンロード)
* 省略可能: [GitHub](https://aka.ms/azure-stream-analytics-toll-source)

## <a name="scenario-introduction-hello-toll"></a>シナリオの説明 - "料金所"
有料道路の料金所は一般的な事象です。 世界中の多くの高速道路、橋、トンネルでそれらを目にします。 各料金所には複数の料金所ブースがあります。 手動式の料金所ブースでは、車を停めて通行料金を支払います。 自動式の料金所ブースでは、通過する車両のフロントガラスに貼り付けられた RFID カードを、各ブースの上方に設置されているセンサーがスキャンします。 車両が料金所を通過した事実は、イベント ストリームとして簡単に可視化でき、そのストリームを介して必要な処理を実行することができます。

![Picture of cars at toll booths](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image1.jpg)

## <a name="incoming-data"></a>受信データ
このチュートリアルでは、2 つのデータ ストリームを扱います。 1 つ目のストリームは、料金所の入口と出口に設置されたセンサーによって生成されます。 2 つ目のストリームは、車両登録データを格納した静的なルックアップ データセットです。

### <a name="entry-data-stream"></a>入口データ ストリーム
入口データ ストリームは、料金所に入る車両の情報を含んでいます。

| TollID | EntryTime | LicensePlate | 状態 | 保存する | モデル | VehicleType | VehicleWeight | Toll | タグ |
| --- | --- | --- | --- | --- | --- | --- | --- | --- | --- |
| 1 |2014-09-10 12:01:00.000 |JNB 7001 |NY |Honda |CRV |1 |0 |7 | |
| 1 |2014-09-10 12:02:00.000 |YXZ 1001 |NY |Toyota |Camry |1 |0 |4 |123456789 |
| 3 |2014-09-10 12:02:00.000 |ABC 1004 |CT |Ford |Taurus |1 |0 |5 |456789123 |
| 2 |2014-09-10 12:03:00.000 |XYZ 1003 |CT |Toyota |Corolla |1 |0 |4 | |
| 1 |2014-09-10 12:03:00.000 |BNJ 1007 |NY |Honda |CRV |1 |0 |5 |789123456 |
| 2 |2014-09-10 12:05:00.000 |CDE 1007 |NJ |Toyota |4x4 |1 |0 |6 |321987654 |

次に、それぞれの列について簡単に説明します。

| 分割 | Description |
| --- | --- |
| TollID |料金所を一意に識別する料金所 ID |
| EntryTime |車両が料金所ブースに入った日時 (UTC) |
| LicensePlate |車両のナンバー プレートの番号 |
| 状態 |米国の州 |
| 保存する |自動車の製造元 |
| モデル |自動車の型式 |
| VehicleType |1 (乗用車) または 2 (商用車) |
| WeightType |車両重量 (トン)。乗用車の場合は 0 |
| Toll |通行料金の値 (米ドル) |
| タグ |決済を自動化するために自動車に設置された電子タグ (支払いが手動で行われた場合は空) |

### <a name="exit-data-stream"></a>出口データ ストリーム
出口データ ストリームは、料金所から出る車両の情報を含んでいます。

| **TollId** | **ExitTime** | **LicensePlate** |
| --- | --- | --- |
| 1 |2014-09-10T12:03:00.0000000Z |JNB 7001 |
| 1 |2014-09-10T12:03:00.0000000Z |YXZ 1001 |
| 3 |2014-09-10T12:04:00.0000000Z |ABC 1004 |
| 2 |2014-09-10T12:07:00.0000000Z |XYZ 1003 |
| 1 |2014-09-10T12:08:00.0000000Z |BNJ 1007 |
| 2 |2014-09-10T12:07:00.0000000Z |CDE 1007 |

次に、それぞれの列について簡単に説明します。

| 分割 | Description |
| --- | --- |
| TollID |料金所を一意に識別する料金所 ID |
| ExitTime |車両が料金所ブースから出た日時 (UTC) |
| LicensePlate |車両のナンバー プレートの番号 |

### <a name="commercial-vehicle-registration-data"></a>商用車の登録データ
このチュートリアルでは、商用車登録データベースを使用しています。特定の時点の静的なデータであり変化しません。

| LicensePlate | RegistrationId | 有効期限切れ |
| --- | --- | --- |
| SVT 6023 |285429838 |1 |
| XLZ 3463 |362715656 |0 |
| BAC 1005 |876133137 |1 |
| RIV 8632 |992711956 |0 |
| SNY 7188 |592133890 |0 |
| ELH 9896 |678427724 |1 |

次に、それぞれの列について簡単に説明します。

| 分割 | Description |
| --- | --- |
| LicensePlate |車両のナンバー プレートの番号 |
| RegistrationId |車両の登録 ID |
| 有効期限切れ |車両登録のステータス: 車両登録が有効期間内である場合は 0、期限切れである場合は 1 |

## <a name="set-up-the-environment-for-azure-stream-analytics"></a>Azure Stream Analytics の環境の設定
このチュートリアルを完了するには、Microsoft Azure サブスクリプションが必要です。 Microsoft Azure サービスの無料試用版がマイクロソフトから提供されています。

Azure アカウントをお持ちでない場合は、 [無料試用版にサインアップ](http://azure.microsoft.com/pricing/free-trial/)してください。

> [!NOTE]
> 無料試用版にサインアップするためには、テキスト メッセージを受信できるモバイル デバイスと有効なクレジット カードが必要となります。
> 
> 

$200 相当の Azure クレジットを利用できるよう、この記事の最後にある「Azure アカウントのクリーンアップ」セクションの手順を忘れずに実行してください。

## <a name="provision-azure-resources-required-for-the-tutorial"></a>チュートリアルに必要な Azure リソースのプロビジョニング
このチュートリアルでは、"*入口*" と "*出口*" のデータ ストリームを受信するために、2 つのイベント ハブが必要となります。 Stream Analytics ジョブの結果は Azure SQL Database で出力します。 また、車両登録に関する参照データは Azure Storage に格納します。

必要なリソースはすべて、GitHub の TollApp フォルダーにある Setup.ps1 スクリプトを使って作成できます。 時間を節約するために、このスクリプトを実行することをお勧めします。 Azure Portal でこれらのリソースを構成する方法については、Azure Portal でチュートリアル リソースを構成する方法について書かれた付録を参照してください。

関連する [TollApp](https://github.com/Azure/azure-stream-analytics/blob/master/Samples/TollApp/TollApp.zip) フォルダーとファイルをダウンロードして保存します。

**[Microsoft Azure PowerShell]** ウィンドウを " *管理者として*" 開きます。 まだ Azure PowerShell をお持ちでない場合は、「 [Azure PowerShell のインストールおよび構成方法](/powershell/azureps-cmdlets-docs) 」の手順に従ってインストールしてください。

ps1 ファイル、.dll ファイル、.exe ファイルは Windows によって自動的にブロックされるため、スクリプトを実行する前に、実行ポリシーを設定しておく必要があります。 Azure PowerShell ウィンドウが*管理者権限*で実行されていることを確認してください。 **Set-ExecutionPolicy unrestricted** を実行し、 確認を求められたら「**Y**」と入力します。

![Screenshot of "Set-ExecutionPolicy unrestricted" running in Azure PowerShell window](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image2.png)

**Get-ExecutionPolicy** を実行して、コマンドが正しく実行されたことを確認します。

![Screenshot of "Get-ExecutionPolicy" running in Azure PowerShell window](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image3.png)

スクリプトとジェネレーター アプリケーションが置かれているディレクトリに移動します。

![Screenshot of "cd .\TollApp\TollApp" running in the Azure PowerShell window](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image4.png)

「**.\\\Setup.ps1**」と入力して Azure アカウントを設定します。必要なリソースをすべて作成および構成して、イベントの生成を開始します。 対象リソースの作成先となるリージョンは、スクリプトによってランダムに選択されます。 リージョンを明示的に指定するには、次の例のように **-location** パラメーターを指定してください。

**.\\Setup.ps1 -location “Central US”**

![Screenshot of the Azure sign-in page](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image5.png)

このスクリプトを実行すると、Microsoft Azure の " **サインイン** " ページが表示されます。 ご利用のアカウントの資格情報を入力してください。

> [!NOTE]
> ご利用のアカウントで複数のサブスクリプションを利用できる場合は、チュートリアルに使用するサブスクリプション名を入力するように求められます。
> 
> 

このスクリプトは完了までに数分かかる場合があります。 完了すると、次のスクリーンショットのような結果が出力されます。

![Screenshot of output of the script in the Azure PowerShell window](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image6.PNG)

他にも、以下のスクリーンショットに示したようなウィンドウが表示されます。 これは、Azure Event Hubs にイベントを送信するアプリケーションで、チュートリアルを実行するために必要となります。 そのため、チュートリアルが完了するまで、アプリケーションを停止したり、このウィンドウを閉じたりしないでください。

![Screenshot of "Sending event hub data"](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image7.png)

この時点で Azure Portal に、リソースが表示されます。 <https://portal.azure.com> にアクセスし、アカウントの資格情報でサインインします。 現在、一部の機能ではクラシック ポータルを利用しています。 その手順を以下に示します。

### <a name="azure-event-hubs"></a>Azure Event Hubs
Azure Portal で、左側の管理ウィンドウの下部にある **[その他のサービス]** をクリックします。 表示されたフィールドに「**Event hubs**」と入力し、**[イベント ハブ]** をクリックします。 新しいブラウザー ウィンドウが起動され、**クラシック ポータル**に **[Service Bus]** 領域が表示されます。 ここで、Setup.ps1 スクリプトによって作成されたイベント ハブを確認できます。

![Service Bus](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image8.png)

*tolldata* で始まるものをクリックします。 **[イベント ハブ]** タブをクリックします。 この名前空間に作成された *entry* と *exit* という&2; つのイベント ハブが表示されます。

![クラシック ポータルの [イベント ハブ] タブ](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image9.png)

### <a name="azure-storage-container"></a>Azure Storage コンテナー
1. Azure Portal が表示されている、ブラウザーの先ほどのタブに戻ります。 Azure Portal の左側にある **[ストレージ]** をクリックすると、チュートリアルで使用する Azure Storage コンテナーが表示されます。
   
    ![Storage menu item](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image11.png)
2. *tolldata* で始まるものをクリックします。 **[コンテナー]** タブをクリックすると、作成されたコンテナーが表示されます。
   
    ![Containers tab in the Azure portal](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image10.png)
3. **[tolldata]** コンテナーをクリックし、車両の登録データが含まれる、アップロード済みの JSON ファイルを表示します。
   
    ![Screenshot of the registration.json file in the container](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image12.png)

### <a name="azure-sql-database"></a>Azure SQL Database
1. ブラウザーで表示されている最初のタブで Azure Portal に戻ります。 Azure Portal の左側にある **[SQL データベース]** をクリックしてチュートリアルで使う SQL データベースを表示し、**tolldatadb** をクリックします。
   
    ![Screenshot of the created SQL database](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image15.png)
2. ポート番号を除いたサーバー名をコピーします (例:*servername*.database.windows.net)。
    ![作成した SQL データベースのスクリーンショット](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image15a.png)

## <a name="connect-to-the-database-from-visual-studio"></a>Visual Studio からデータベースに接続する
出力データベースに格納されているクエリの結果に、Visual Studio を使用してアクセスします。

次の手順に従って、SQL データベース (接続先) に Visual Studio から接続します。

1. Visual Studio を起動し、**[ツール]**  >  **[データベースへの接続]** をクリックします。
2. データ ソースの確認を求められたら、 **[Microsoft SQL Server]** をクリックします。
   
    ![Change Data Source dialog box](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image16.png)
3. **[サーバー名]** フィールドで、前のセクションで Azure Portal からコピーした名前を貼り付けます ( *servername*.database.windows.net)。
4. **[SQL Server 認証を使用する]**をクリックします。
5. **[ユーザー名]** フィールドに「**tolladmin**」、**[パスワード]** フィールドに **123toll!** 」と入力します。
6. **[データベース名の選択または入力]** をクリックし、データベースとして **[TollDataDB]** を選択します。
   
    ![Add Connection dialog box](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image17.jpg)
7. **[OK]**をクリックします。
8. [サーバー エクスプローラー] を開きます。
   
    ![Server Explorer](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image18.png)
9. TollDataDB データベースの&4; つのテーブルが表示されます。
   
    ![Tables in the TollDataDB database](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image19.jpg)

## <a name="event-generator-tollapp-sample-project"></a>イベント ジェネレーター: TollApp サンプル プロジェクト
PowerShell スクリプトは、TollApp というサンプル アプリケーション プログラムを使用して、イベントの送信を自動的に開始します。 別途処理を実行する必要はありません。

ただし、詳しい実装内容に関心がある方は、GitHub の [samples/TollApp](https://aka.ms/azure-stream-analytics-toll-source)で、TollApp アプリケーションのソース コードをご覧いただけます。

![Screenshot of sample code displayed in Visual Studio](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image20.png)

## <a name="create-a-stream-analytics-job"></a>Stream Analytics のジョブの作成
1. Azure Portal で、ページの左上隅にある緑色の正符号をクリックして、新しい Stream Analytics のジョブを作成します。 **[インテリジェンス + 分析]** を選択し、**[Stream Analytics ジョブ]** をクリックします。
   
    ![[新規] ボタン](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image21.png)
2. ジョブ名を指定し、サブスクリプションが正しいことを確認してから、イベント ハブのストレージと同じリージョンで新しいリソース グループを作成します (スクリプトの既定値は米国中南部です)。
3. **[ダッシュボードにピン留めする]** をクリックしてから、ページの下部にある **[作成]** をクリックします。
   
    ![Create Stream Analytics Job option](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image22.png)

## <a name="define-input-sources"></a>入力ソースの定義
1. ジョブが作成され、ジョブのページが開きます。 また、ポータルのダッシュボードで、作成された分析ジョブをクリックすることもできます。

2. **[入力]** タブをクリックしてソース データを定義します。
   
    ![The Inputs tab](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image24.png)
3. **[入力の追加]**をクリックします。
   
    ![The Add an Input option](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image25.png)
4. **[入力のエイリアス]** として「**EntryStream**」と入力します。
5. [ソースの種類] は **[データ ストリーム]** です。
6. [ソース] は **[イベント ハブ]** です。
7. **[Service Bus 名前空間]** は、ドロップダウンにある TollData の名前空間を指定します。
8. **[イベント ハブの名前]** は **[entry (入力)]** に設定する必要があります。
9. **[イベント ハブ ポリシー名]*は **RootManageSharedAccessKey** です (既定値)。
10. **[イベントのシリアル化の形式]** に **[JSON]** を、**[エンコード]** に **[UTF8]** を選択します。
   
    設定は次のようになります。
   
    ![Event hub settings](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image28.png)

10. ページの下部にある **[作成]** をクリックしてウィザードを終了します。
    
    入口のストリームを作成したら、同じ手順で出口のストリームを作成します。 次のスクリーンショットに従って値を入力してください。
    
    ![Settings for the exit stream](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image31.png)
    
    以下のように、2 つの入力ストリームを定義しました。
    
    ![Defined input streams in the Azure portal](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image32.png)
    
    次に、車両登録データの BLOB ファイルに対する参照データ入力を追加します。
11. **[追加]** をクリックして、ストリーム入力と同じ手順に従います。ただし、**[データ ストリーム]** の代わりに **[参照データ]** を選択し、**[入力のエイリアス]** に **[登録]** を指定します。

12. [ストレージ アカウント] には、**tolldata** で始まるものを選択します。 コンテナー名は **tolldata**、**[パス パターン]** は **registration.json** とする必要があります。 このファイル名は大文字と小文字が区別されるため、**小文字**で入力してください。
    
    ![Blog storage settings](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image34.png)
13. **[作成]** をクリックしてウィザードを終了します。

これで、すべての入力が定義されました。

## <a name="define-output"></a>出力の定義
1. [Stream Analytics job (Stream Analytics ジョブの概要)] ウィンドウで、**[出力]** を選択します。
   
    ![The Output tab and "Add an output" option](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image37.png)
2. **[追加]**をクリックします。
3. **[出力のエイリアス]** を [出力] に設定し **[シンク]** を **[SQL データベース]** に設定します。
3. この記事の「Visual Studio からデータベースに接続する」セクションで使用したサーバー名を選択します。 データベース名は **TollDataDB**としてください。
4. **[ユーザー名]** フィールドに「**tolladmin**」、**[パスワード]** フィールドに 「**123toll!**」、および **[テーブル]** フィールドに「**TollDataRefJoin**」と入力します。
   
    ![SQL Database settings](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image38.png)
5. **[作成]**をクリックします。

## <a name="azure-stream-analytics-query"></a>Azure Stream Analytics クエリ
**[クエリ]** タブには、入力データを変換する SQL クエリが表示されます。

![A query added to the Query tab](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image39.png)

このチュートリアルでは、通行料金データに伴う実務上の課題を解決していきたいと思います。必要な答えを導くために、Azure Stream Analytics で使用できる Stream Analytics クエリを構築します。

初めての Stream Analytics ジョブを作成する前に、いくつかのシナリオとクエリ構文について詳しく見ていきましょう。

## <a name="introduction-to-azure-stream-analytics-query-language"></a>Azure Stream Analytics クエリ言語について
- - -
料金所ブースに入る車両の台数をカウントしなければならなくなったとします。 絶えず発生するイベントであるため、"期間" の定義が必要となります。 そこで目的を少し変更し、"3 分ごとに料金所ブースに入る車両の台数" を調べることにします。 これを一般に "タンブリング カウント" といいます。

以下に示したのは、それを実現する Azure Stream Analytics クエリです。

    SELECT TollId, System.Timestamp AS WindowEnd, COUNT(*) AS Count
    FROM EntryStream TIMESTAMP BY EntryTime
    GROUP BY TUMBLINGWINDOW(minute, 3), TollId

ご覧のとおり、Azure Stream Analytics には SQL に似たクエリ言語が使用されており、さらに、時間に関連したクエリ要素を指定するための拡張機能がいくつか追加されています。

詳細については、クエリにおける[時間管理](https://msdn.microsoft.com/library/azure/mt582045.aspx)と[時間枠](https://msdn.microsoft.com/library/azure/dn835019.aspx)の概念についての記事を MSDN でご覧ください。

## <a name="testing-azure-stream-analytics-queries"></a>Azure Stream Analytics クエリのテスト
初めての Azure Stream Analytics クエリを作成したら、TollApp フォルダーの以下のパスに格納されているサンプル データ ファイルを使ってクエリをテストします。

**..\\TollApp\\TollApp\\Data**

このフォルダーには次のファイルが格納されています。

* Entry.json
* Exit.json
* registration.json

## <a name="question-1-number-of-vehicles-entering-a-toll-booth"></a>テスト 1: 料金所ブースに入る車両の台数
1. Azure Portal を開き、先ほど作成した Azure Stream Analytics ジョブに移動します。 **[クエリ]** タブをクリックし、前のセクションからクエリをコピーして貼り付けます。

2. サンプル データに対してこのクエリを検証するには、[...] 記号をクリックし、**[ファイルからサンプル データをアップロードする]** を選択して、データを EntryStream 入力にアップロードします。

    ![Screenshot of the Entry.json file](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image41.png)
3. 表示されたウィンドウで、ローカル コンピューター上のファイル (Entry.json) を選択し、**[OK]** をクリックします。 ここで **[テスト]** アイコンが点灯し、クリック可能になります。
   
    ![Screenshot of the Entry.json file](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image42.png)
3. クエリの出力結果が想定どおりであることを確認します。
   
    ![Results of the test](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image43.png)

## <a name="question-2-report-total-time-for-each-car-to-pass-through-the-toll-booth"></a>テスト 2: 各車両が料金所ブースを通過するのにかかる総時間を調べる
処理効率と顧客満足を評価できるよう、車両が料金所を通過するのにかかる平均時間を調べるとします。

総時間を調べるためには、EntryTime ストリームと ExitTime ストリームを結合する必要があります。 ストリームの結合条件には、TollId 列と LicencePlate 列を指定することにします。 結合したイベントどうしの間隔として許容される時間差を **JOIN** 演算子で指定する必要があります。 **DATEDIFF** 関数を使用し、発生間隔が 15 分以内のイベントに限定する条件を指定します。 また、通行料金の徴収に費やされた実際の時間を計算するために、ExitTime と EntryTime にも **DATEDIFF** 関数を適用します。 **DATEDIFF** を **SELECT** ステートメントで使用する場合と **JOIN** 条件で使用する場合の使い方の違いに注目してください。

    SELECT EntryStream.TollId, EntryStream.EntryTime, ExitStream.ExitTime, EntryStream.LicensePlate, DATEDIFF (minute , EntryStream.EntryTime, ExitStream.ExitTime) AS DurationInMinutes
    FROM EntryStream TIMESTAMP BY EntryTime
    JOIN ExitStream TIMESTAMP BY ExitTime
    ON (EntryStream.TollId= ExitStream.TollId AND EntryStream.LicensePlate = ExitStream.LicensePlate)
    AND DATEDIFF (minute, EntryStream, ExitStream ) BETWEEN 0 AND 15

1. このクエリをテストするには、ジョブの **[クエリ]** でクエリを更新します。 先ほど **EntryStream** を入力した要領で、**ExitStream** のテスト ファイルを追加します。
   
2. **[Test]**をクリックします。

3. チェック ボックスをオンにしてクエリをテストし、出力結果を確認します。
   
    ![Output of the test](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image45.png)

## <a name="question-3-report-all-commercial-vehicles-with-expired-registration"></a>テスト 3: 登録が期限切れとなっているすべての商用車を調べる
Azure Stream Analytics では、特定の時点の静的データを使用して経時的に発生するデータ ストリームを結合することができます。 この機能のデモとして、次のテストを行います。

料金徴収会社に登録されている商用車は、検査のために停車しなくても料金所ブースを通過することができます。 商用車登録参照テーブルを使用して、登録期限切れとなっているすべての商用車を特定することにしましょう。

```
SELECT EntryStream.EntryTime, EntryStream.LicensePlate, EntryStream.TollId, Registration.RegistrationId
FROM EntryStream TIMESTAMP BY EntryTime
JOIN Registration
ON EntryStream.LicensePlate = Registration.LicensePlate
WHERE Registration.Expired = '1'
```

参照データを使ってクエリをテストするためには、その参照データの入力ソースを定義する必要がありますが、その作業は既に完了しています。

このクエリをテストするには、**[クエリ]** タブにクエリを貼り付けて **[テスト]** をクリックし、2 つの入力ソースと登録サンプル データを指定してから、**[テスト]** をクリックします。  
   
![Output of the test](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image46.png)

## <a name="start-the-stream-analytics-job"></a>Stream Analytics ジョブの開始
では、構成を終了してジョブを開始してみましょう。 テスト 3 で作成したクエリを保存してください。出力テーブル **TollDataRefJoin** のスキーマに適合した出力結果が、このクエリによって生成されます。

ジョブの**ダッシュボード**に移動して **[開始]** をクリックします。

![Screenshot of the Start button in the job dashboard](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image48.png)

表示されたダイアログ ボックスで、**[出力の開始]** の時刻を **[ユーザー設定時刻]** に変更します。 時間 (時) を編集し、現在時刻の&1; 時間前の時刻に設定します。 この変更により、チュートリアルの最初にイベントの生成を開始したとき以降、イベント ハブから受け取ったすべてのイベントが確実に処理されます。 ここで、**[開始]** をクリックしてジョブを開始します。

![Selection of custom time](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image49.png)

ジョブが開始されるまでに数分かかる場合があります。 そのステータスは、Stream Analytics の最上位のページで確認できます。

![Screenshot of the status of the job](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image50.png)

## <a name="check-results-in-visual-studio"></a>Visual Studio での結果の確認
1. Visual Studio のサーバー エクスプローラーを開いて **TollDataRefJoin** テーブルを右クリックします。
2. **[テーブル データの表示]** をクリックすると、ジョブの出力結果が表示されます。
   
    ![Selection of "Show Table Data" in Server Explorer](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image51.jpg)

## <a name="scale-out-azure-stream-analytics-jobs"></a>Azure Stream Analytics ジョブのスケールアウト
Azure Stream Analytics は、大量のデータ処理に対応できるよう、状況の変化に応じてスケール変更できるように設計されています。 Azure Stream Analytics クエリで **PARTITION BY** 句を使用すると、そのステップをスケールアウトして処理するようシステムに命令することができます。 **PartitionId** は、システムによって追加される特殊な列で、入力 (イベント ハブ) のパーティション ID と一致します。

    SELECT TollId, System.Timestamp AS WindowEnd, COUNT(*)AS Count
    FROM EntryStream TIMESTAMP BY EntryTime PARTITION BY PartitionId
    GROUP BY TUMBLINGWINDOW(minute,3), TollId, PartitionId

1. 現在のジョブを停止し、**[クエリ]** タブでクエリを更新して、ジョブのダッシュボードで **[設定]** の歯車アイコンを開きます。 **[スケール]**をクリックします。
   
    **ストリーミング ユニット** によって定義されます。
2. ドロップ ダウンを 1 から 6 に変更します。
   
    ![Screenshot of selecting 6 streaming units](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image52.png)
3. **[出力]** タブに移動して、SQL テーブルの名前を **TollDataTumblingCountPartitioned** に変更してください。

これでジョブを開始すると、より多くのコンピューティング リソースに処理が分散され、スループットが向上します。 TollApp アプリケーションから送信されるイベントも TollId でパーティション分割されることに注目してください。

## <a name="monitor"></a>監視
**[監視]** 領域には、実行中のジョブに関する統計情報が表示されます。 ストレージ アカウントを同じリージョン内で使用する場合は、初回の構成 が必要です (このドキュメントの他の部分と同様に toll という名前を付けます)。   

![監視のスクリーンショット](media/stream-analytics-build-an-iot-solution-using-stream-analytics/monitoring.png)

**アクティビティ ログ**には、ジョブのダッシュボードの **[設定]** 領域からもアクセスできます。


## <a name="conclusion"></a>まとめ
このチュートリアルでは、Azure Stream Analytics サービスについて取り上げました。 デモンストレーションでは、Stream Analytics ジョブの入力と出力の構成方法を説明しました。 イベント データをリアルタイムで扱ううえでの一般的な課題と、Azure Stream Analytics の SQL に似た単純なクエリを使ってそれを解決する方法について、通行料金データを例に説明しました。 その中で触れた SQL 拡張機能の概念は、経時的に発生するデータを扱う際に必要となります。 データ ストリームを結合する方法や、データ ストリームの利用価値を静的な参照データで高める方法、クエリをスケールアウトしてスループットを高める方法についても紹介しました。

このチュートリアルで取り上げた内容はあくまで入門的な位置付けであり、ここでは紹介しきれない情報も数多くあります。 SAQL 言語を使ったその他のクエリ パターンについては、「 [一般的 Stream Analytics 使用状況パターンのクエリ例](stream-analytics-stream-analytics-query-patterns.md)」をご覧ください。
Azure Stream Analytics の詳細については、 [オンライン ドキュメント](https://azure.microsoft.com/documentation/services/stream-analytics/) を参照してください。

## <a name="clean-up-your-azure-account"></a>Azure アカウントのクリーンアップ
1. Azure Portal で Stream Analytics ジョブを停止します。
   
    Setup.ps1 スクリプトでは、イベント ハブが&2; つと SQL データベースが&1; つ作成されます。 チュートリアルの最後に、次の手順に従ってリソースをクリーンアップしてください。
2. PowerShell ウィンドウで「**\\Cleanup.ps1**」と入力します。これにより、チュートリアルで使用したリソースを削除するスクリプトが開始されます。
   
   > [!NOTE]
   > リソースを表す名前が表示されます。 それぞれ慎重に確認してから削除してください。
   > 
   > 



