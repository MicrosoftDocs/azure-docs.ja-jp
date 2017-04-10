---
title: "構成済みソリューションの使用 | Microsoft Docs"
description: "このチュートリアルに従って、Azure IoT Suite 事前構成済みソリューションのデプロイ方法について学習します。"
services: 
suite: iot-suite
documentationcenter: 
author: dominicbetts
manager: timlt
editor: 
ms.assetid: 6ab38d1a-b564-469e-8a87-e597aa51d0f7
ms.service: iot-suite
ms.devlang: na
ms.topic: hero-article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 02/15/2017
ms.author: dobett
translationtype: Human Translation
ms.sourcegitcommit: d9dad6cff80c1f6ac206e7fa3184ce037900fc6b
ms.openlocfilehash: e68815c2dafc596c3560ad3fcb2a7bf96d29182b
ms.lasthandoff: 03/06/2017


---
# <a name="tutorial-get-started-with-the-preconfigured-solutions"></a>チュートリアル: 事前構成済みソリューションの使用
## <a name="introduction"></a>はじめに
Azure IoT Suite の[事前構成済みソリューション][lnk-preconfigured-solutions]は、さまざまな Azure IoT サービスを組み合わせて、IoT のビジネス シナリオを満たすエンド ツー エンド ソリューションを提供します。 " *リモート監視* " は、対象のデバイスに接続して監視する事前構成済みソリューションです。 このソリューションを使用して、対象デバイスから流れてくるデータのストリームを分析し、そのデータのストリームに対してプロセスから自動的に応答が得られるようにすることで、ビジネスの成果を高めることができます。

このチュートリアルでは、リモート監視の事前構成済みソリューションをプロビジョニングする方法について説明します。 また、事前構成済みソリューションの基本機能についても詳しく説明します。 こうした機能の多くには、事前構成済みソリューションの一部としてデプロイされるソリューション *ダッシュボード*からアクセスできます。

![Remote monitoring preconfigured solution dashboard][img-dashboard]

このチュートリアルを完了するには、アクティブな Azure サブスクリプションが必要になります。

> [!NOTE]
> アカウントがない場合は、無料試用版のアカウントを数分で作成することができます。 詳細については、[Azure の無料試用版サイト][lnk_free_trial]をご覧ください。
> 
> 

[!INCLUDE [iot-suite-provision-remote-monitoring](../../includes/iot-suite-provision-remote-monitoring.md)]

## <a name="scenario-overview"></a>シナリオの概要

リモート監視の構成済みソリューションをデプロイすると、代表的なリモート監視シナリオに順を追って対処できるリソースが事前に入力されています。 対象となるシナリオは、ソリューションに接続された複数のデバイスから、予期しない温度値が報告されている状況です。 以降のセクションでは、次の方法について説明します。

* 予期しない温度値を送信しているデバイスを特定する。
* 詳細なテレメトリを送信するようデバイスを構成する。
* デバイスのファームウェアを更新して問題を解決する。
* これらのアクションによって問題が解決されたことを確認する。

このシナリオの主な特徴は、ソリューション ダッシュボードからこれらのアクションをすべてリモートで実行できることです。 デバイスに物理的にアクセスする必要はありません。

## <a name="view-the-solution-dashboard"></a>ソリューション ダッシュボードの表示

ソリューションのダッシュボードでは、デプロイされたソリューションを管理できます。 テレメトリの表示、デバイスの追加、ルールの構成などを行えます。

1. プロビジョニングが完了し、事前構成済みソリューションのタイルに **[準備完了]** が表示されたら、**[起動]** を選択し、新しいタブでリモート監視ソリューション ポータルを開きます。

    ![Launch the preconfigured solution][img-launch-solution]

1. 既定では、ソリューション ポータルに "*ダッシュボード*" が表示されます。 ソリューション ポータルの他の領域に移動するには、ページの左側にあるメニューを使用します。

    ![Remote monitoring preconfigured solution dashboard][img-menu]

ダッシュボードには次の情報が表示されます。

* ソリューションに接続されている各デバイスの場所が表示されるマップ。 初めてソリューションを実行すると、シミュレートされた 25 台のデバイスが表示されます。 シミュレートされたデバイスは、Azure WebJobs として実装されています。ソリューションは Bing Maps API を使用して、マップに情報をプロットします。 マップを動的に表示する方法については、[よく寄せられる質問][lnk-faq]に関するページをご覧ください。
* 選択したデバイスからの湿度と温度のテレメトリがほぼリアルタイムでプロットされ、最大値、最小値、平均湿度などの集計データが表示される **[テレメトリ履歴]** パネル。
* テレメトリ値がしきい値を超えたときに最新のアラーム イベントが表示される **[アラーム履歴]** パネル。 構成済みソリューションによって作成された例に加えて、独自のアラームを定義することもできます。
* スケジュールされたジョブに関する情報が表示される **[ジョブ]** パネル。 自分のジョブは、**[管理ジョブ]** ページでスケジュールできます。

## <a name="view-alarms"></a>アラームの表示

[アラーム履歴] パネルに、予期されたテレメトリ値より高い値が&5; 台のデバイスから報告されていることが表示されています。

![ソリューション ダッシュボードに表示される対処の必要なアラーム履歴][img-alarms]

> [!NOTE]
> これらのアラームは、構成済みソリューションに含まれているルールに基づいて生成されています。 このルールでは、デバイスから送信された温度値が 60 を超えた場合にアラートが生成されます。 左側のメニューで [[ルール]](#add-a-rule) と [[アクション]](#add-an-action) を選択すると、独自のルールやアクションを定義できます。

## <a name="view-devices"></a>デバイスの表示

"*デバイス一覧*" には、ソリューションの登録済みデバイスがすべて表示されます。 デバイス一覧で、デバイス メタデータの表示と編集、デバイスの追加または取り外し、デバイスのメソッドの呼び出しを行うことができます。 デバイスの一覧にはフィルター処理や並べ替えを適用できます。 また、デバイス一覧に表示される列をカスタマイズすることもできます。

1. **[デバイス]** を選択して、このソリューションのデバイス一覧を表示します。

   ![ソリューション ポータルにデバイス一覧を表示][img-devicelist]

1. デバイス一覧には、まず、プロビジョニング プロセスで作成された 25 台のシミュレートされたデバイスが表示されます。 ソリューションには、シミュレートされたデバイスや物理デバイスを追加できます。

1. デバイス一覧でデバイスを選択すると、そのデバイスの詳細が表示されます。

   ![ソリューション ポータルにデバイスの詳細を表示][img-devicedetails]

**[デバイスの詳細]** パネルには、次の&6; つのセクションが含まれています。

* デバイス アイコンのカスタマイズ、デバイスの無効化、ルールの追加、メソッドの呼び出し、またはコマンドの送信を行うためのリンクのコレクション。 コマンド (device-to-cloud メッセージ) とメソッド (直接メソッド) の比較については、「[cloud-to-device 通信に関するガイダンス][lnk-c2d-guidance]」を参照してください。
* **[デバイス ツイン] - [タグ]** セクションでは、デバイスのタグ値を編集することができます。 デバイス一覧でタグ値を表示し、タグ値を使用してデバイス一覧をフィルター処理することができます。
* **[デバイス ツイン] - [必要なプロパティ]** セクションでは、デバイスに送信されるプロパティ値を設定することができます。
* **[デバイス ツイン] - [報告されたプロパティ]** セクションでは、デバイスから送信されたプロパティ値が表示されます。
* **[デバイスのプロパティ]** セクションでは、デバイス ID や認証キーなど、ID レジストリの情報が表示されます。
* **[最近のジョブ]** セクションでは、このデバイスを最近対象にしたジョブに関する情報が表示されます。

## <a name="filter-the-device-list"></a>デバイス一覧のフィルター処理

フィルターを使用すると、予期しない温度値を送信しているデバイスのみを表示できます。 リモート監視の構成済みソリューションには、平均温度の値が 60 よりも高いデバイスを表示する **[異常なデバイス]** フィルターが用意されています。 [フィルターを自分で作成する](#add-a-filter)こともできます。

1. **[保存済みのフィルターを開く]** を選択すると、使用可能なフィルターの一覧が表示されます。 **[異常なデバイス]** を選択すると、フィルターが適用されます。

    ![フィルターの一覧を表示][img-unhealthy-filter]

1. デバイス一覧に、平均温度が 60 よりも高いデバイスだけが表示されます。

    ![フィルターを適用し、異常なデバイスを示すデバイス一覧を表示][img-filtered-unhealthy-list]

## <a name="update-desired-properties"></a>必要なプロパティを更新する

修復しなければならない可能性のあるデバイスを特定できました。 しかし、問題を明確に診断するには、15 秒というデータ頻度では不十分だと判断したとしましょう。 テレメトリ頻度を&5; 秒に変更すれば、データ ポイントの数が増え、問題を的確に診断できます。 この構成変更は、ソリューション ポータルからリモートのデバイスにプッシュできます。 1 回変更を加えるだけで、影響を評価し、結果に対してアクションを実施できます。

次の手順に従うと、該当するデバイスに対して必要なプロパティ **TelemetryInterval** を変更するジョブを実行できます。 デバイスは **TelemetryInterval** プロパティの新しい値を受け取ると、テレメトリを 15 秒ごとではなく 5 秒ごとに送信するよう構成を変更します。

1. デバイス一覧に異常なデバイスが一覧表示されているときに、**[ジョブ スケジューラ]**、**[デバイス ツインの編集]** の順に選択します。

1. **[Change telemetry interval (テレメトリ間隔の変更)]** ジョブを呼び出します。

1. **[必要なプロパティ名]** にある **desired.Config.TelemetryInterval** の値を&5; 秒に変更します。

1. **[スケジュール]** を選択します。

    ![TelemetryInterval プロパティを&5; 秒に変更][img-change-interval]

1. ジョブの進行状況は、ポータルの **[管理ジョブ]** ページで監視できます。

> [!NOTE]
> 特定のデバイスで必要なプロパティの値を変更したい場合は、ジョブを実行する代わりに、**[デバイスの詳細]** パネルの **[必要なプロパティ]** セクションを使用してください。

このジョブを実行すると、フィルターで選択したすべてのデバイスのデバイス ツインに、必要なプロパティ **TelemetryInterval** の値が設定されます。 デバイスはデバイス ツインからこの値を取得して、動作を更新します。 デバイス ツインから取得した必要なプロパティを処理した後、デバイスは対応する [報告されたプロパティ] に値を設定します。

## <a name="invoke-methods"></a>メソッドを呼び出す

ジョブの実行中に、異常なデバイスの一覧を見ると、対象のすべてのデバイスでファームウェアのバージョンが古い (バージョン 1.6 より前である) ことがわかりました。

![異常なデバイスについて報告されたファームウェアのバージョンを表示][img-old-firmware]

他の正常なデバイスでは最近ファームウェア バージョンが 2.0 に更新されたことがわかっていますので、このファームウェア バージョンが予期しない温度値の根本原因である可能性があります。 組み込みの **[Old firmware devices (ファームウェアが古いデバイス)]** フィルターを使用して、ファームウェアのバージョンが古いデバイスを特定できます。 まだ以前のファームウェア バージョンが実行されているデバイスを特定できたら、ポータルからリモートで該当するすべてのデバイスを更新できます。

1. **[保存済みのフィルターを開く]** を選択すると、使用可能なフィルターの一覧が表示されます。 **[Old firmware devices (ファームウェアが古いデバイス)]** を選択すると、フィルターが適用されます。

    ![フィルターの一覧を表示][img-old-filter]

1. デバイス一覧に、ファームウェアのバージョンが古いデバイスだけが表示されます。 この一覧には、**[異常なデバイス]** フィルターで特定された&５; 台のデバイスに加えて&３; 台のデバイスが表示されています。

    ![フィルターを適用し、古いデバイスを示すデバイス一覧を表示][img-filtered-old-list]

1. **[ジョブ スケジューラ]**、**[メソッドの呼び出し]** の順に選択します。

1. **[ジョブ名]** を「**Firmware update to version 2.0**」に設定します。

1. **[メソッド]** で **[InitiateFirmwareUpdate]** を選択します。

1. **FwPackageUri** パラメーターを **https://iotrmassets.blob.core.windows.net/firmwares/FW20.bin** に設定します。

1. **[スケジュール]** を選択します。 既定では、ジョブがすぐに実行されます。

    ![選択したデバイスのファームウェアを更新するジョブを作成][img-method-update]

> [!NOTE]
> 特定のデバイスでメソッドを呼び出したい場合は、ジョブを実行する代わりに、**[デバイスの詳細]** パネルで **[メソッド]** セクションを選択してください。

このジョブを実行すると、フィルターで選択したすべてのデバイスで **InitiateFirmwareUpdate** ダイレクト メソッドが呼び出されます。 デバイスはすぐに IoT Hub に応答し、ファームウェアの更新プロセスを非同期で開始します。 次のスクリーンショットに示すように、ファームウェアの更新プロセスに関する状態情報が [報告されたプロパティ] の値を通じて提供されます。 **[最新の情報に更新]** アイコンを選択すると、デバイス一覧とジョブ一覧の情報が更新されます。

![実行中のファームウェア更新を一覧表示するジョブ一覧][img-update-1]
![ファームウェアの更新状態を表示するデバイス一覧][img-update-2]
![完了したファームウェア更新を一覧表示するジョブ一覧][img-update-3]

> [!NOTE]
> 運用環境では、指定したメンテナンス ウィンドウ中に実行されるジョブをスケジュールできます。

## <a name="scenario-review"></a>シナリオのまとめ

このシナリオでは、ダッシュボードのアラーム履歴とフィルターを使用して、リモート デバイスの一部で発生した潜在的な問題を特定しました。 そして、フィルターとジョブを使用して、問題の診断に役立つ詳細情報を提供するようにデバイスをリモートで構成しました。 最後に、フィルターとジョブを使用して、該当するデバイスに対してメンテナンスのスケジュールを設定しました。 ダッシュボードに戻ると、ソリューション内のデバイスからアラームが通知されなくなったことを確認できます。 フィルターを使用して、ソリューション内のすべてのデバイスでファームウェアが最新であることと、異常なデバイスがなくなっていることを確認できます。

![すべてのデバイスのファームウェアが最新であることを示すフィルター][img-updated]

![すべてのデバイスが正常であることを示すフィルター][img-healthy]

## <a name="other-features"></a>その他の機能

以降のセクションでは、上記のシナリオで扱わなかった、リモート監視の構成済みソリューションが備える他の機能をいくつか紹介します。

### <a name="customize-columns"></a>列のカスタマイズ

デバイス一覧に表示される情報をカスタマイズするには、**[列エディター]** をクリックします。 報告されたプロパティとタグの値を表示する列を追加したり削除したりすることができます。 また、列を並べ替えたり、列の名前を変更したりすることもできます。

   ![デバイス一覧の列エディター][img-columneditor]

### <a name="customize-the-device-icon"></a>デバイス アイコンのカスタマイズ

デバイス一覧に表示されるデバイス アイコンは、**[デバイスの詳細]** パネルで、次のようにカスタマイズできます。

1. デバイスの **[イメージの編集]** パネルを開くには、鉛筆アイコンを選択します。

   ![デバイスのイメージ エディターを開く][img-startimageedit]

1. 新しいイメージをアップロードするか、既存のイメージのいずれかを使用して、**[保存]** を選択します。

   ![デバイスのイメージ エディターで編集する][img-imageedit]

1. 選択したイメージが、デバイスの **[アイコン]** 列に表示されるようになりました。

> [!NOTE]
> イメージは、BLOB ストレージに格納されます。 デバイス ツインのタグには、BLOB ストレージ内のイメージへのリンクが含まれています。

### <a name="add-a-device"></a>デバイスを追加する

事前構成済みソリューションをデプロイすると、25 台のサンプル デバイスが自動的にプロビジョニングされ、デバイス一覧に表示されます。 これらは "*シミュレートされたデバイス*" であり、Azure WebJob で実行されています。 シミュレーション デバイスを使用することで、事前構成済みソリューションを簡単にテストすることができます。実際に物理デバイスをデプロイする必要はありません。 実際のデバイスをソリューションに接続する方法については、チュートリアル「[デバイスをリモート監視構成済みソリューションに接続する][lnk-connect-rm]」をご覧ください。

次の手順は、シミュレートされたデバイスをソリューションに追加する方法を示しています。

1. デバイス一覧に戻ります。

1. デバイスを追加するには、左下隅の **[+ デバイスの追加]** を選択します。

   ![Add a device to the preconfigured solution][img-adddevice]

1. **[シミュレートされたデバイス]** タイルで **[新規追加]** を選択します。

   ![Set new device details in dashboard][img-addnew]

   新しいシミュレートされたデバイスを作成することに加えて、 **カスタム デバイス**を作成することを選択した場合、物理デバイスを追加することもできます。 ソリューションへの物理デバイスの接続に関する詳細については、[IoT Suite リモート監視構成済みソリューションへのデバイスの接続][lnk-connect-rm]に関する記事をご覧ください。

1. **[Let me define my own Device ID (自分で自分のデバイス ID を定義する)]** を選択し、**mydevice_01** など一意のデバイス ID 名を入力します。

1. **[作成]**を選択します。

   ![Save a new device][img-definedevice]

1. 「**新しいシミュレートされたデバイスの追加**」の手順 3. で **[完了]** を選択すると、デバイス一覧に戻ります。

1. デバイス一覧でデバイスが **[実行中]** であることを確認できます。

    ![View new device in device list][img-runningnew]

1. 新しいデバイスからシミュレートされたテレメトリをダッシュボードに表示することもできます。

    ![View telemetry from new device][img-runningnew-2]

### <a name="disable-and-delete-a-device"></a>デバイスの無効化と削除

デバイスを無効にし、無効になったデバイスを削除することができます。

![Disable and remove a device][img-disable]

### <a name="add-a-rule"></a>規則を追加する

追加した新しいデバイスにはルールがありません。 このセクションでは、新しいデバイスによって報告された温度が 47 度を超えるとアラームをトリガーするルールを追加します。 始める前に、ダッシュボードで新しいデバイスのテレメトリ履歴に、デバイスの温度が 45 度を超えたことがないと示されていることに注意します。

1. デバイス一覧に戻ります。

1. デバイスのルールを追加するには、**デバイス一覧**で新しいデバイスを選択し、**[ルールの追加]** を選択します。

1. データ フィールドとして **[Temperature (温度)]** を使用し、温度が 47 度を超えたときの出力として **[AlarmTemp]** を使用するルールを作成します。

    ![Add a device rule][img-adddevicerule]

1. 変更を保存するには、**[ルールを保存して表示]** を選択します。

1. 新しいデバイスの [デバイスの詳細] ペインで **[コマンド]** を選択します。

    ![Add a device rule][img-adddevicerule2]

1. コマンド リストから **[ChangeSetPointTemp]** を選択し、**[SetPointTemp]** を 45 に設定します。 **[コマンドの送信]** を選択します。

    ![Add a device rule][img-adddevicerule3]

1. ダッシュボードに移動します。 しばらくして、新しいデバイスによって報告される温度が 47 度のしきい値を超えると、 **[アラーム履歴]** ペインに新しいエントリが表示されます。

    ![Add a device rule][img-adddevicerule4]

1. ダッシュボードの **[ルール]** ページですべてのルールを確認して編集できます。

    ![List device rules][img-rules]

1. ダッシュボードの **[アクション]** ページで、ルールへの対応として実行可能なすべてのアクションを確認して編集できます。

    ![List device actions][img-actions]

> [!NOTE]
> ルールへの対応として電子メール メッセージや SMS を送信したり、[ロジック アプリ][lnk-logic-apps]経由で基幹業務システムに統合したりするアクションを定義できます。 詳細については、[Azure IoT Suite リモート監視構成済みソリューションへのロジック アプリの接続][lnk-logicapptutorial]に関するページをご覧ください。

### <a name="manage-filters"></a>フィルターを管理する

デバイス一覧では、ハブに接続されているデバイスのカスタマイズされた一覧を表示するために、フィルターの作成、保存、再読み込みを行うことができます。 フィルターを作成する手順は、次のとおりです。

1. デバイスの一覧の上にあるフィルター編集アイコンを選択します。

    ![フィルター エディターを開く][img-editfiltericon]

1. **[エディターのフィルター]** で、デバイス一覧をフィルター処理するためのフィールド、演算子、値を追加します。 フィルターの精度を上げるために、複数の句を追加することができます。 **[フィルター]** を選択して、フィルターを適用します。

    ![フィルターを作成する][img-filtereditor]

1. この例では、一覧が製造元とモデル番号でフィルター処理されます。

    ![フィルター処理された一覧][img-filterelist]

1. カスタム名でフィルターを保存するには、**[保存]** アイコンを選択します。

    ![フィルターを保存する][img-savefilter]

1. 前に保存したフィルターを再適用するには、**[保存済みのフィルターを開く]** アイコンを選択します。

    ![フィルターを開く][img-openfilter]

デバイス ID、デバイスの状態、必要なプロパティ、報告されるプロパティ、タグに基づいて、フィルターを作成することができます。 独自のカスタム タグをデバイスに追加するには、**[デバイスの詳細]** ペインの **[タグ]** セクションを使用します。複数のデバイスのタグを更新するジョブを実行することもできます。

> [!NOTE]
> **[エディターのフィルター]** では、**[詳細ビュー]** を使用して、クエリ テキストを直接編集することができます。

### <a name="commands"></a>コマンド

**[デバイスの詳細]** パネルで、デバイスにコマンドを送信することができます。 デバイスは初めて起動したときに、ソリューションに対してサポートしているコマンドに関する情報を送信します。 コマンドとメソッドの違いの詳細については、[Azure IoT Hub の cloud-to-device オプション][lnk-c2d-guidance]に関するページをご覧ください。

1. 選択したデバイスの **[デバイスの詳細]** パネルで **[コマンド]** を選択します。

   ![Device commands in dashboard][img-devicecommands]

1. コマンドの一覧から **[PingDevice]** を選択します。

1. **[コマンドの送信]** を選択します。

1. [コマンドの履歴] にコマンドの状態が表示されます。

   ![Command status in dashboard][img-pingcommand]

ソリューションは、送信した各コマンドの状態を追跡します。 最初は結果が **[保留中]**になっています。 コマンドを実行したことがデバイスから報告されると、結果が **[成功]**に設定されます。

## <a name="behind-the-scenes"></a>バックグラウンド処理

事前構成済みソリューションをデプロイすると、デプロイ プロセスにより、選択した Azure サブスクリプションに複数のリソースが作成されます。 これらのリソースを [Azure ポータル][lnk-portal]で表示できます。 デプロイ プロセスにより、事前構成済みソリューション用に選択した名前に基づいた名前で、 **リソース グループ** が作成されます。

![Preconfigured solution in the Azure portal][img-portal]

各リソースの設定を表示するには、リソース グループ内のリソースのリストでそれを選択します。

また、事前構成済みソリューションのソース コードを表示することもできます。 リモート監視の事前構成済みソリューションのソース コードは、[azure-iot-remote-monitoring][lnk-rmgithub] GitHub リポジトリにあります。

* **DeviceAdministration** フォルダーには、ダッシュボードのソース コードが格納されています。
* **Simulator** フォルダーには、シミュレートされたデバイスのソース コードが格納されています。
* **EventProcessor** フォルダーには、受信テレメトリを処理するバックエンド プロセスのソース コードが格納されています。

完了したら、[azureiotsuite.com][lnk-azureiotsuite] で事前構成済みソリューションを Azure サブスクリプションから削除できます。 このサイトでは、構成済みのソリューションを作成したときにプロビジョニングされていたすべてのリソースを簡単に削除することができます。

> [!NOTE]
> 事前構成済みソリューションに関連するすべてのものを確実に削除するには、ポータルでリソース グループを削除するのではなく、[azureiotsuite.com][lnk-azureiotsuite] サイトで事前構成済みソリューションを削除してください。

## <a name="next-steps"></a>次のステップ

これで、事前構成済みの実際のソリューションをデプロイできました。引き続き IoT Suite の概要について学習するには、次の記事を参照してください。

* [リモート監視の事前構成済みソリューションのチュートリアル][lnk-rm-walkthrough]
* [デバイスをリモート監視の事前構成済みソリューションに接続する][lnk-connect-rm]
* [azureiotsuite.com サイトでのアクセス許可][lnk-permissions]

[img-launch-solution]: media/iot-suite-getstarted-preconfigured-solutions/launch.png
[img-dashboard]: media/iot-suite-getstarted-preconfigured-solutions/dashboard.png
[img-menu]: media/iot-suite-getstarted-preconfigured-solutions/menu.png
[img-devicelist]: media/iot-suite-getstarted-preconfigured-solutions/devicelist.png
[img-alarms]: media/iot-suite-getstarted-preconfigured-solutions/alarms.png
[img-devicedetails]: media/iot-suite-getstarted-preconfigured-solutions/devicedetails.png
[img-devicecommands]: media/iot-suite-getstarted-preconfigured-solutions/devicecommands.png
[img-pingcommand]: media/iot-suite-getstarted-preconfigured-solutions/pingcommand.png
[img-adddevice]: media/iot-suite-getstarted-preconfigured-solutions/adddevice.png
[img-addnew]: media/iot-suite-getstarted-preconfigured-solutions/addnew.png
[img-definedevice]: media/iot-suite-getstarted-preconfigured-solutions/definedevice.png
[img-runningnew]: media/iot-suite-getstarted-preconfigured-solutions/runningnew.png
[img-runningnew-2]: media/iot-suite-getstarted-preconfigured-solutions/runningnew2.png
[img-rules]: media/iot-suite-getstarted-preconfigured-solutions/rules.png
[img-adddevicerule]: media/iot-suite-getstarted-preconfigured-solutions/addrule.png
[img-adddevicerule2]: media/iot-suite-getstarted-preconfigured-solutions/addrule2.png
[img-adddevicerule3]: media/iot-suite-getstarted-preconfigured-solutions/addrule3.png
[img-adddevicerule4]: media/iot-suite-getstarted-preconfigured-solutions/addrule4.png
[img-actions]: media/iot-suite-getstarted-preconfigured-solutions/actions.png
[img-portal]: media/iot-suite-getstarted-preconfigured-solutions/portal.png
[img-disable]: media/iot-suite-getstarted-preconfigured-solutions/solutionportal_08.png
[img-columneditor]: media/iot-suite-getstarted-preconfigured-solutions/columneditor.png
[img-startimageedit]: media/iot-suite-getstarted-preconfigured-solutions/imagedit1.png
[img-imageedit]: media/iot-suite-getstarted-preconfigured-solutions/imagedit2.png
[img-editfiltericon]: media/iot-suite-getstarted-preconfigured-solutions/editfiltericon.png
[img-filtereditor]: media/iot-suite-getstarted-preconfigured-solutions/filtereditor.png
[img-filterelist]: media/iot-suite-getstarted-preconfigured-solutions/filteredlist.png
[img-savefilter]: media/iot-suite-getstarted-preconfigured-solutions/savefilter.png
[img-openfilter]:  media/iot-suite-getstarted-preconfigured-solutions/openfilter.png
[img-unhealthy-filter]: media/iot-suite-getstarted-preconfigured-solutions/unhealthyfilter.png
[img-filtered-unhealthy-list]: media/iot-suite-getstarted-preconfigured-solutions/unhealthylist.png
[img-change-interval]: media/iot-suite-getstarted-preconfigured-solutions/changeinterval.png
[img-old-firmware]: media/iot-suite-getstarted-preconfigured-solutions/noticeold.png
[img-old-filter]: media/iot-suite-getstarted-preconfigured-solutions/oldfilter.png
[img-filtered-old-list]: media/iot-suite-getstarted-preconfigured-solutions/oldlist.png
[img-method-update]: media/iot-suite-getstarted-preconfigured-solutions/methodupdate.png
[img-update-1]: media/iot-suite-getstarted-preconfigured-solutions/jobupdate1.png
[img-update-2]: media/iot-suite-getstarted-preconfigured-solutions/jobupdate2.png
[img-update-3]: media/iot-suite-getstarted-preconfigured-solutions/jobupdate3.png
[img-updated]: media/iot-suite-getstarted-preconfigured-solutions/updated.png
[img-healthy]: media/iot-suite-getstarted-preconfigured-solutions/healthy.png

[lnk_free_trial]: http://azure.microsoft.com/pricing/free-trial/
[lnk-preconfigured-solutions]: iot-suite-what-are-preconfigured-solutions.md
[lnk-azureiotsuite]: https://www.azureiotsuite.com
[lnk-logic-apps]: https://azure.microsoft.com/documentation/services/app-service/logic/
[lnk-portal]: http://portal.azure.com/
[lnk-rmgithub]: https://github.com/Azure/azure-iot-remote-monitoring
[lnk-logicapptutorial]: iot-suite-logic-apps-tutorial.md
[lnk-rm-walkthrough]: iot-suite-remote-monitoring-sample-walkthrough.md
[lnk-connect-rm]: iot-suite-connecting-devices.md
[lnk-permissions]: iot-suite-permissions.md
[lnk-c2d-guidance]: ../iot-hub/iot-hub-devguide-c2d-guidance.md
[lnk-faq]: iot-suite-faq.md
