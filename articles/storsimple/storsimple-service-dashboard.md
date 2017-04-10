---
title: "StorSimple Manager サービスのダッシュボード | Microsoft Docs"
description: "StorSimple Manager サービス ダッシュボードについて説明すると共に、サービス ダッシュボードを使用して StorSimple ソリューションの状態を監視する方法を説明します。"
services: storsimple
documentationcenter: 
author: SharS
manager: carmonm
editor: 
ms.assetid: fb0f131d-d60b-45d7-ace2-56d0502e6627
ms.service: storsimple
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 05/24/2016
ms.author: v-sharos
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: 596431b7279b753ca4da838eb028cdde2022ce02


---
# <a name="use-the-storsimple-manager-service-dashboard"></a>StorSimple Manager サービスのダッシュボードを使用する
## <a name="overview"></a>Overview
StorSimple Manager サービスのダッシュボード ページには、StorSimple Manager サービスに接続されているすべてのデバイスの概要が表示されます。システム管理者が注意を必要とする内容は、強調表示されます。 このチュートリアルでは、ダッシュボード ページを紹介してから、ダッシュボードの内容と機能について、さらに、このページで実行できるタスクについて説明します。

![サービスのダッシュボード](./media/storsimple-service-dashboard/HCS_ServiceDashboard.png)

StorSimple Manager サービスのダッシュボードには、次の情報が表示されます。

* **グラフ** の領域には、デバイスに関連するメトリック グラフが表示されます。 ここでは、すべてのデバイスで使用されるプライマリ ストレージ (ローカル固定ストレージと階層化ストレージ) と、一定期間デバイスで使用されるクラウド ストレージを確認できます。 グラフの右上隅にあるコントロールを使用して、1 週間、1 か月、3 か月、または 1 年間のタイム スケールを指定できます。
* **[使用状況の概要]** では、すべてのデバイスで使用可能な合計ストレージに対して、プロビジョニングされているプライマリ ストレージ、およびすべてのデバイスで使用されているプライマリ ストレージの割合を示します。 **[プロビジョニング済み]** は、使用のための準備および割り当てが完了しているストレージ容量を示し、**[使用済み]** は、デバイスに接続されているイニシエーターで確認された、ボリュームの使用状況を示します。
* **[アラート]** 領域には、すべてのデバイスでアクティブになっているアラートを重大度別に分類したスナップショットが表示されます。 重大度レベルをクリックすると、 **[アラート]** ページが開き、該当する重大度レベルのアラートが表示対象となります。 **[アラート]** ページでは、個々のアラートをクリックすることで、それぞれのアラートに関する詳細情報 (推奨される対応など) を確認できます。 問題が解決した場合は、アラートをクリアすることもできます。
* **[ジョブ]** 領域には、サービスに接続されているすべてのデバイスにおいて最近行われたジョブのスナップショットが表示されます。 ジョブ確認用のリンクとして、現在進行中のジョブ、過去 24 時間に失敗したジョブ、または次の 24 時間で実行するようにスケジュールされているジョブを確認できるリンクがあります。
* **[概要]** 領域には、サービスの状態、サービスに接続されているデバイスの数、サービスの場所、サービスに関連付けられているサブスクリプションの詳細など、役に立つ情報が表示されます。 操作ログへのリンクもあります。 このリンクをクリックすると、完了したすべての StorSimple Manager サービス操作が一覧表示されます。

StorSimple Manager サービスのダッシュボード ページを使用して、次のタスクを実行できます。

* サービス登録キーの表示または再生成
* サービス データ暗号化キーの変更
* 操作ログの表示

## <a name="view-or-regenerate-the-service-registration-key"></a>サービス登録キーの表示または再生成
サービス登録キーを使用して、Microsoft Azure StorSimple デバイスを StorSimple Manager サービスに登録します。これにより、Azure クラシック ポータルにデバイスが表示され、管理操作の対象となります。 キーは最初のデバイスで作成され、残りのすべてのデバイスと共有されます。

**[登録キー]** (ページの下部にある) をクリックすると、**[サービス登録キー]** ダイアログ ボックスが表示されます。ここでは、現在のサービス登録キーをクリップボードにコピーすることも、サービス登録キーを再生成することもできます。

キーを再生成しても、以前に登録したデバイスには影響ありません。キーを再生成した後でサービスに登録されたデバイスのみが影響を受けます。

サービス登録キーの表示と再生成に関する詳細は、「 [サービス登録キーを取得する](storsimple-manage-service.md#get-the-service-registration-key)」をご覧ください。

## <a name="change-the-service-data-encryption-key"></a>サービス データ暗号化キーの変更
サービス データ暗号化キーを使用して、StorSimple Manager サービスから StorSimple デバイスに送信される、顧客の機密データ (ストレージ アカウントの資格情報など) を暗号化します。 IT 組織にストレージ デバイスに関するキー ローテーション ポリシーがある場合は、これらのキーを定期的に変更する必要があります。 キーの変更プロセスは、StorSimple Manager サービスが 1 つのデバイスを管理しているか、または複数のデバイスを管理しているかによって多少異なります。

サービス データ暗号化キーの変更は、次の 3 つの手順からなるプロセスです。

1. Azure クラシック ポータルを使用して、サービス データ暗号化キーを変更できるようにデバイスを承認します。
2. StorSimple 用 Windows PowerShell を使用して、サービス データ暗号化キーの変更を開始します。
3. StorSimple デバイスを複数使用している場合は、他のすべてのデバイスでサービス データ暗号化キーを更新します。

次の手順では、サービス データ暗号化キーのロール オーバー プロセスについて説明します。

[!INCLUDE [storsimple-change-data-encryption-key](../../includes/storsimple-change-data-encryption-key.md)]

## <a name="view-the-operations-logs"></a>操作ログを表示する
操作ログを表示するには、ダッシュボードの **[概要]** ウィンドウにある操作ログ リンクをクリックします。 これにより、管理サービス ページが表示されます。このページでは、フィルター機能を使用して、特定の StorSimple Manager サービスに限定したログを表示することができます。

## <a name="next-steps"></a>次のステップ
* [StorSimple デバイスのトラブルシューティング](storsimple-troubleshoot-operational-device.md)を行う方法
* [StorSimple Manager サービスを使用した StorSimple デバイスの管理方法](storsimple-manager-service-administration.md)




<!--HONumber=Nov16_HO3-->


