---
title: "StorSimple Manager サービス管理 | Microsoft Docs"
description: "Azure クラシック ポータルで StorSimple Manager サービスを使用して、StorSimple デバイスを管理する方法を説明します。"
services: storsimple
documentationcenter: 
author: alkohli
manager: carmonm
editor: 
ms.assetid: 2586582e-d85c-42e1-afb3-be734c1c0461
ms.service: storsimple
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/21/2016
ms.author: alkohli
translationtype: Human Translation
ms.sourcegitcommit: 219dcbfdca145bedb570eb9ef747ee00cc0342eb
ms.openlocfilehash: d5d5aa67d14d2344c58e67ee78ea3f2b5d8fd415


---
# <a name="use-the-storsimple-manager-service-to-administer-your-storsimple-device"></a>StorSimple Manager サービスを使用した StorSimple デバイスの管理
## <a name="overview"></a>Overview
この記事では、接続方法、用意されている各種オプション、この UI を通じて実行できる特定のワークフローへのリンクを含め、StorSimple Manager サービス インターフェイスについて説明します。 このガイドは、StorSimple 物理デバイスと仮想デバイスの両方に適用されます。

この記事を読むと、次のことを習得できます。

* StorSimple Manager サービスへの接続
* StorSimple Manager UI のナビゲーション
* StorSimple Manager サービスを使用した StorSimple デバイスの管理

## <a name="connect-to-storsimple-manager-service"></a>StorSimple Manager サービスへの接続
StorSimple Manager サービスは Microsoft Azure で実行され、複数の StorSimple デバイスに接続します。 これらのデバイスを管理するには、中央の Microsoft Azure クラシック ポータルをブラウザーで実行して使用します。 StorSimple Manager サービスに接続するには、次の手順を実行します。

#### <a name="to-connect-to-the-service"></a>サービスに接続するには
1. [https://manage.windowsazure.com/](https://manage.windowsazure.com/)に移動します。
2. Microsoft アカウントの資格情報を使用して、ウィンドウの右上にある Microsoft Azure クラシック ポータルにログオンします。
3. 左側のナビゲーション ウィンドウで下へスクロールして、StorSimple Manager サービスにアクセスします。

## <a name="navigate-storsimple-manager-service-ui"></a>StorSimple Manager サービス UI のナビゲーション
StorSimple Manager サービス UI のナビゲーション階層を次の表に示します。

* **StorSimple Manager** のランディング ページからは、サービス内のすべてのデバイスに適用可能な UI サービス レベルのページに移動できます。
* **[デバイス]** ページからは、特定のデバイスに適用可能なデバイス レベルの UI ページに移動できます。
* **[ボリューム コンテナー]** ページからは、デバイスに関連付けられているすべてのボリュームを表示するボリューム ページに移動できます。

#### <a name="storsimple-manager-service-navigational-hierarchy"></a>StorSimple Manager サービスのナビゲーション階層
| ランディング ページ | サービス レベルのページ | デバイス レベルのページ | デバイス レベルのページ |
| --- | --- | --- | --- |
| StorSimple Manager サービス |サービスのダッシュボード |デバイス ダッシュボード | |
| デバイス → |監視 | | |
| バックアップ カタログ |ボリューム コンテナー → |ボリューム | |
| 構成 (サービス) |バックアップ ポリシー | | |
| ジョブ |構成 (デバイス) | | |
| Alerts |メンテナンス | | |

![ビデオ](./media/storsimple-manager-service-administration/Video_icon.png) **ビデオ**

StorSimple Manager サービスのユーザー インターフェイスのチュートリアル ビデオについては、 [こちら](https://azure.microsoft.com/documentation/videos/storsimple-manager-service-overview/)を参照してください。

## <a name="administer-storsimple-device-using-storsimple-manager-service"></a>StorSimple Manager サービスを使用した StorSimple デバイスの管理
次の表に、StorSimple Manager サービス UI 内で実行できるすべての一般的な管理タスクと複雑なワークフローの概要を示します。 これらのタスクは、タスクが開始される UI ページに基づいてまとめられています。

各ワークフローの詳細については、表内の適切な手順をクリックしてください。

#### <a name="storsimple-manager-workflows"></a>StorSimple Manager のワークフロー
| 目的の操作 | 移動先の UI ページ | 実行する手順 |
| --- | --- | --- |
| サービスを作成する</br>サービスを削除する</br>サービス登録キーを取得する</br>サービス登録キーを再生成する |StorSimple Manager サービス |[StorSimple Manager サービスをデプロイする](storsimple-manage-service.md) |
| サービス データ暗号化キーを変更する</br>操作ログを表示する |StorSimple Manager サービス → [ダッシュボード] |[StorSimple Manager サービスのダッシュボードを使用する](storsimple-service-dashboard.md) |
| デバイスを非アクティブ化する</br>デバイスを削除する |StorSimple Manager サービス → [デバイス] |[デバイスを非アクティブ化または削除する](storsimple-deactivate-and-delete-device.md) |
| 障害復旧とデバイスのフェールオーバーについて学習する</br>物理デバイスへのフェイルオーバー</br>仮想デバイスへのフェールオーバー</br>ビジネス継続性障害復旧 (BCDR) |StorSimple Manager サービス → [デバイス] |[StorSimple デバイスのフェールオーバーと障害復旧](storsimple-device-failover-disaster-recovery.md) |
| ボリュームのバックアップを一覧表示する</br>バックアップ セットの選択</br>バックアップ セットを削除する |StorSimple Manager サービス → [バックアップ カタログ] |[バックアップを管理する](storsimple-manage-backup-catalog.md) |
| ボリュームを複製する |StorSimple Manager サービス → [バックアップ カタログ] |[ボリュームを複製する](storsimple-clone-volume.md) |
| バックアップ セットを復元する |StorSimple Manager サービス → [バックアップ カタログ] |[バックアップ セットを復元する](storsimple-restore-from-backup-set.md) |
| ストレージ アカウントについて</br>ストレージ アカウントの追加</br>ストレージ アカウントの編集</br>ストレージ アカウントの削除</br>ストレージ アカウントのキー ローテーション |StorSimple Manager サービス → [構成] |[ストレージ アカウントを管理する](storsimple-manage-storage-accounts.md) |
| 帯域幅テンプレートについて</br>帯域幅テンプレートを追加する</br>帯域幅テンプレートを編集する</br>帯域幅テンプレートを削除する</br>既定の帯域幅テンプレートを使用する</br>指定した時刻に開始される終日の帯域幅テンプレートを作成する |StorSimple Manager サービス → [構成] |[帯域幅テンプレートを管理する](storsimple-manage-bandwidth-templates.md) |
| アクセス制御レコードについて</br>アクセス制御レコードの作成</br>アクセス制御レコードの編集</br>アクセス制御レコードの削除 |StorSimple Manager サービス → [構成] |[アクセス制御レコードを管理する](storsimple-manage-acrs.md) |
| ジョブの詳細を表示する</br>ジョブを取り消す |StorSimple Manager サービス → [ジョブ] |[ジョブの管理](storsimple-manage-jobs.md) |
| アラート通知を受け取る</br>Manage alerts</br>アラートを確認する |StorSimple Manager サービス → [アラート] |[StorSimple アラートを表示および管理する](storsimple-manage-alerts.md) |
| 接続されているイニシエーターを表示する</br>デバイスのシリアル番号の検索</br>ターゲット IQN を調べる |StorSimple Manager サービス → [デバイス] → [ダッシュボード] |[StorSimple デバイス ダッシュボードを使用する](storsimple-device-dashboard.md) |
| 監視グラフを作成する |StorSimple Manager サービス → [デバイス] → [監視] |[StorSimple デバイスを監視する](storsimple-monitor-device.md) |
| ボリューム コンテナーを追加する</br>ボリューム コンテナーを変更する</br>ボリューム コンテナーを削除する |StorSimple Manager サービス → [デバイス] → [ボリューム コンテナー] |[ボリューム コンテナーを管理する](storsimple-manage-volume-containers.md) |
| ボリュームを追加する</br>ボリュームを変更する</br>ボリュームをオフラインにする</br>ボリュームを削除する</br>ボリュームを監視する |StorSimple Manager サービス → [デバイス] → [ボリューム コンテナー] → [ボリューム] |[ボリュームを管理する](storsimple-manage-volumes.md) |
| デバイスの設定の変更</br>時刻の設定の変更</br>DNS.md 設定の変更</br>ネットワーク インターフェイスを構成する |StorSimple Manager サービス → [デバイス] → [構成] |[StorSimple デバイスのデバイス構成を変更する](storsimple-modify-device-config.md) |
| Web プロキシ設定を表示する |StorSimple Manager サービス → [デバイス] → [構成] |[デバイスの Web プロキシを構成する](storsimple-configure-web-proxy.md) |
| デバイス管理者のパスワードを変更する</br>StorSimple Snapshot Manager のパスワードを変更する |StorSimple Manager サービス → [デバイス] → [構成] |[StorSimple のパスワードを変更する](storsimple-change-passwords.md) |
| リモート管理の構成 |StorSimple Manager サービス → [デバイス] → [構成] |[StorSimple デバイスにリモート接続する](storsimple-remote-connect.md) |
| アラート設定を構成する |StorSimple Manager サービス → [デバイス] → [構成] |[StorSimple アラートを表示および管理する](storsimple-manage-alerts.md) |
| StorSimple デバイスの CHAP を構成する |StorSimple Manager サービス → [デバイス] → [構成] |[StorSimple デバイスの CHAP を構成する](storsimple-configure-chap.md) |
| バックアップ ポリシーを追加する</br>スケジュールの追加または変更</br>バックアップ ポリシーの削除</br>手動バックアップの取得</br>複数のボリュームとスケジュールを含むカスタム バックアップ ポリシーを作成する |StorSimple Manager サービス → [デバイス] → [バックアップ ポリシー] |[バックアップ ポリシーを管理する](storsimple-manage-backup-policies.md) |
| デバイス コントローラーを停止する</br>デバイス コントローラーを再起動する</br>デバイス コントローラーをシャット ダウンする</br>デバイスを工場出荷時の既定値にリセットする</br>(これらはオンプレミスのデバイスのみに適用されます) |StorSimple Manager サービス → [デバイス] → [メンテナンス] |[StorSimple デバイス コントローラーを管理する](storsimple-manage-device-controller.md) |
| StorSimple のハードウェア コンポーネントについて学習する</br>ハードウェアの状態を監視する</br>(これらはオンプレミスのデバイスのみに適用されます) |StorSimple Manager サービス → [デバイス] → [メンテナンス] |[ハードウェア コンポーネントを監視する](storsimple-monitor-hardware-status.md) |
| サポート パッケージを作成する |StorSimple Manager サービス → [デバイス] → [メンテナンス] |[サポート パッケージを作成および管理する](storsimple-create-manage-support-package.md) |
| ソフトウェアの更新プログラムをインストールする |StorSimple Manager サービス → [デバイス] → [メンテナンス] |[デバイスを更新する](storsimple-update-device.md) |

## <a name="next-steps"></a>次のステップ
StorSimple デバイスの日常的な操作または StorSimple デバイスのハードウェア コンポーネントのいずれかで問題が発生した場合は、次のトピックを参照してください。

* [運用デバイスのトラブルシューティング](storsimple-troubleshoot-operational-device.md)
* [StorSimple 監視インジケーター LED の使用](storsimple-monitoring-indicators.md)

問題を解決できず、サービス要求の作成が必要な場合は、｢ [Microsoft サポートに問い合わせる](storsimple-contact-microsoft-support.md)」を参照してください。




<!--HONumber=Nov16_HO3-->


