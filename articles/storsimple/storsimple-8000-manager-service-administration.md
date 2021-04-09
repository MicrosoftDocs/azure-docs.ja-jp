---
title: StorSimple デバイス マネージャー サービスの管理 | Microsoft Docs
description: Azure Portal で StorSimple デバイス マネージャー サービスを使用して、StorSimple デバイスを管理する方法を説明します。
services: storsimple
documentationcenter: ''
author: alkohli
manager: timlt
editor: ''
ms.assetid: ''
ms.service: storsimple
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/17/2021
ms.author: alkohli
ms.openlocfilehash: 0e25d41fbf6a9683f178cbfd1b07fde1b6796509
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "104656823"
---
# <a name="use-the-storsimple-device-manager-service-to-administer-your-storsimple-device"></a>StorSimple デバイス マネージャー サービスを使用した StorSimple デバイスの管理

## <a name="overview"></a>概要

この記事では、接続方法、用意されている各種オプション、この UI を通じて実行できる特定のワークフローへのリンクを含め、StorSimple デバイス マネージャー サービスのインターフェイスについて説明します。 このガイドは、StorSimple 物理デバイスとクラウド アプライアンスの両方に適用されます。

この記事を読むと、次のことを習得できます。

* StorSimple デバイス マネージャー サービスに接続する
* StorSimple デバイス マネージャー サービスを使用した StorSimple デバイスの管理

## <a name="connect-to-storsimple-device-manager-service"></a>StorSimple デバイス マネージャー サービスに接続する

StorSimple デバイス マネージャー サービスは Microsoft Azure で実行され、複数の StorSimple デバイスに接続します。 こうしたデバイスを管理するには、中央の Microsoft Azure Portal をブラウザーで実行して使用します。 StorSimple デバイス マネージャー サービスに接続するには、次の操作を行います。

#### <a name="to-connect-to-the-service"></a>サービスに接続するには
1. [https://portal.azure.com/](https://portal.azure.com/) に移動します。
2. Microsoft アカウントの資格情報を使用して、ウィンドウの右上にある Microsoft Azure Portal にログオンします。
3. 左側のナビゲーション ウィンドウで下へスクロールして、StorSimple デバイス マネージャー サービスにアクセスします。


## <a name="administer-storsimple-device-using-storsimple-device-manager-service"></a>StorSimple デバイス マネージャー サービスを使用した StorSimple デバイスの管理

次の表に、StorSimple デバイス マネージャー サービス UI 内で実行できる一般的な管理タスクと複雑なワークフローすべての概要を示します。 これらのタスクは、タスクが開始される UI ブレードに基づいてまとめられています。

各ワークフローの詳細については、表内の適切な手順をクリックしてください。

[!INCLUDE [StorSimple software warning](../../includes/storsimple-update-software-warning.md)]

#### <a name="storsimple-device-manager-workflows"></a>StorSimple デバイス マネージャーのワークフロー

| 目的の操作 | 実行する手順 |
| --- | --- |
| サービスの作成</br>サービスの削除</br>サービス登録キーを取得する</br>サービス登録キーを再生成する |[StorSimple デバイス マネージャー サービスのデプロイ](storsimple-8000-manage-service.md) |
| アクティビティ ログを表示する |[StorSimple デバイス マネージャー サービスの概要ブレードの使用](storsimple-8000-service-dashboard.md) |
| サービス データ暗号化キーの変更</br>操作ログを表示する |[StorSimple デバイス マネージャー サービスのダッシュボードの使用](storsimple-8000-service-dashboard.md) |
| デバイスの非アクティブ化</br>デバイスの削除 |[デバイスを非アクティブ化または削除する](storsimple-8000-deactivate-and-delete-device.md) |
| ディザスター リカバリーとデバイスのフェールオーバーについて学習する</br>物理デバイスへのフェイルオーバー</br>仮想デバイスへのフェールオーバー</br>ビジネス継続性ディザスター リカバリー (BCDR) |[StorSimple デバイスのフェールオーバーとディザスター リカバリー](storsimple-8000-device-failover-disaster-recovery.md) |
| ボリュームのバックアップを一覧表示する</br>バックアップ セットの選択</br>バックアップ セットの削除 |[バックアップの管理](storsimple-8000-manage-backup-catalog.md) |
| ボリュームを複製する |[ボリュームを複製する](storsimple-8000-clone-volume-u2.md) |
| バックアップ セットを復元する |[バックアップ セットを復元する](storsimple-8000-restore-from-backup-set-u2.md) |
| ストレージ アカウントについて</br>ストレージ アカウントの追加</br>ストレージ アカウントの編集</br>ストレージ アカウントを削除する</br>ストレージ アカウントのキー ローテーション |[ストレージ アカウントを管理する](storsimple-8000-manage-storage-accounts.md) |
| 帯域幅テンプレートについて</br>帯域幅テンプレートを追加する</br>帯域幅テンプレートを編集する</br>帯域幅テンプレートを削除する</br>既定の帯域幅テンプレートを使用する</br>指定した時刻に開始する終日帯域幅テンプレートを作成する |[帯域幅テンプレートを管理する](storsimple-8000-manage-bandwidth-templates.md) |
| アクセス制御レコードについて</br>アクセス制御レコードの作成</br>アクセス制御レコードの編集</br>アクセス制御レコードの削除 |[アクセス制御レコードを管理する](storsimple-8000-manage-acrs.md) |
| ジョブの詳細を表示する</br>ジョブを取り消す |[ジョブの管理](storsimple-8000-manage-jobs-u2.md) |
| アラート通知を受け取る</br>Manage alerts</br>アラートを確認する |[StorSimple アラートを表示および管理する](storsimple-8000-manage-alerts.md) |
| 監視グラフを作成する |[StorSimple デバイスを監視する](./storsimple-8000-monitor-device.md) |
| ボリューム コンテナーを追加する</br>ボリューム コンテナーを変更する</br>ボリューム コンテナーを削除する |[ボリューム コンテナーを管理する](storsimple-8000-manage-volume-containers.md) |
| ボリュームを追加する</br>ボリュームを変更する</br>ボリュームをオフラインにする</br>ボリュームを削除する</br>ボリュームを監視する |[ボリュームを管理する](storsimple-8000-manage-volumes-u2.md) |
| デバイスの設定の変更</br>時刻の設定の変更</br>DNS.md 設定の変更</br>ネットワーク インターフェイスを構成する |[StorSimple デバイスのデバイス構成を変更する](storsimple-8000-modify-device-config.md) |
| Web プロキシ表示設定を構成する |[デバイスの Web プロキシを構成する](storsimple-8000-configure-web-proxy.md) |
| デバイス管理者のパスワードを変更する</br>StorSimple Snapshot Manager のパスワードを変更する |[StorSimple のパスワードを変更する](storsimple-8000-change-passwords.md) |
| リモート管理の構成 |[StorSimple デバイスにリモート接続する](storsimple-8000-remote-connect.md) |
| アラート設定の構成 |[StorSimple アラートを表示および管理する](storsimple-8000-manage-alerts.md) |
| StorSimple デバイスの CHAP の構成 |[StorSimple デバイスの CHAP の構成](./storsimple-8000-configure-chap.md) |
| バックアップ ポリシーを追加する</br>スケジュールの追加または変更</br>バックアップ ポリシーの削除</br>手動バックアップの取得</br>複数のボリュームとスケジュールによるカスタム バックアップ ポリシーの作成 |[バックアップ ポリシーを管理する](storsimple-8000-manage-backup-policies-u2.md) |
| デバイス コントローラーを停止する</br>デバイス コントローラーを再起動する</br>デバイス コントローラーをシャット ダウンする</br>デバイスを工場出荷時の既定値にリセットする</br>(これらはオンプレミスのデバイスのみに適用されます) |[StorSimple デバイス コントローラーを管理する](storsimple-8000-manage-device-controller.md) |
| StorSimple のハードウェア コンポーネントについて学習する</br>ハードウェアの状態を監視する</br>(これらはオンプレミスのデバイスのみに適用されます) |[ハードウェア コンポーネントを監視する](storsimple-8000-monitor-hardware-status.md) |
| サポート パッケージを作成する |[サポート パッケージを作成および管理する](storsimple-8000-contact-microsoft-support.md#start-a-support-session-in-windows-powershell-for-storsimple) |
| ソフトウェアの更新プログラムをインストールする |[デバイスの更新](storsimple-update-device.md) |

## <a name="next-steps"></a>次のステップ

StorSimple デバイスの日常的な操作または StorSimple デバイスのハードウェア コンポーネントのいずれかで問題が発生した場合は、次のトピックを参照してください。

* [診断ツールを使用したトラブルシューティング](storsimple-8000-diagnostics.md)
* [StorSimple 監視インジケーター LED の使用](storsimple-monitoring-indicators.md)

問題を解決できず、サービス要求の作成が必要な場合は、｢ [Microsoft サポートに問い合わせる](storsimple-8000-contact-microsoft-support.md)」を参照してください。