---
title: StorSimple Snapshot Manager 管理 | Microsoft Docs
description: StorSimple Snapshot Manager ソリューションの管理タスクとワークフローの概要とこれらに関する詳細情報へのリンクを示します。
services: storsimple
documentationcenter: NA
author: alkohli
manager: timlt
editor: ''
ms.assetid: 1cdbb61d-bd16-4be4-ade2-ceab11508acb
ms.service: storsimple
ms.devlang: NA
ms.topic: how-to
ms.tgt_pltfrm: NA
ms.workload: TBD
ms.date: 06/05/2016
ms.author: alkohli
ms.openlocfilehash: 16d782c6a25d8d2d8e1323b7cb683f41d8c99649
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "90054978"
---
# <a name="use-storsimple-snapshot-manager-to-administer-your-storsimple-solution"></a>StorSimple Snapshot Manager を使用した StorSimple ソリューションの管理

## <a name="overview"></a>概要
StorSimple Snapshot Manager は、Microsoft Azure StorSimple 環境でのデータ保護とバックアップ管理を簡素化する、Microsoft 管理コンソール (MMC) スナップインです。 StorSimple Snapshot Manager では、データ センター内やクラウド内の Microsoft Azure StorSimple データを 1 つの統合ストレージ ソリューションとして管理できるため、バックアップ プロセスが簡素化され、コストが削減されます。

StorSimple Snapshot Manager の中央管理コンソールを使用すると、ローカルおよびクラウドのデータについて、一貫性のある、特定時点のバックアップ コピーを作成することができます。 たとえば、このコンソールを使用して次の操作を実行できます。

* ボリュームを構成、バックアップ、削除する。
* ボリューム グループを構成してバックアップ データのアプリケーション整合性を確保する。
* 事前に指定されたスケジュールでデータがバックアップされるようにバックアップ ポリシーを管理する。
* クラウドに保存して障害復旧に使用できる、データの独立したコピーを作成する。

この記事では、StorSimple Snapshot Manager の概要と、これを使用してシステム管理タスクとワークフローの完了する方法を説明するためのチュートリアルへのリンクを示します。

* StorSimple Snapshot Manager のコンポーネントとアーキテクチャの詳細については、「 [What is StorSimple Snapshot Manager? (StorSimple Snapshot Manager について)](storsimple-what-is-snapshot-manager.md) 
* StorSimple Snapshot Manager をダウンロードするには、 [StorSimple Snapshot Manager のダウンロード ページ](https://www.microsoft.com/download/details.aspx?id=44220)にアクセスしてください。
* StorSimple Snapshot Manager のデプロイ手順については、「 [Deploy StorSimple Snapshot Manager (StorSimple Snapshot Manager のデプロイ)](storsimple-snapshot-manager-deployment.md)」をご覧ください。

> [!NOTE]
> StorSimple Snapshot Manager を使用して、Microsoft Azure StorSimple Virtual Array (別名 StorSimple オンプレミス仮想デバイス) を管理することはできません。


## <a name="storsimple-snapshot-manager-tasks-and-workflows"></a>StorSimple Snapshot Manager のタスクとワークフロー
StorSimple Snapshot Manager を使用すると、現在のバックアップ ジョブ、スケジュールされているバックアップ ジョブ、および完了したバックアップ ジョブを監視し、管理できます。 さらに、StorSimple Snapshot Manager では、最大 64 個の完了したバックアップのカタログが提供されます。 このカタログを使用して、ボリュームまたは個々のファイルを検索し、復元できます。 

| 目的の操作 | 使用するチュートリアル |
|:--- |:--- |
| StorSimple Snapshot Manager について知りたい。 |[StorSimple Snapshot Manager について](storsimple-what-is-snapshot-manager.md) |
| StorSimple Snapshot Manager のインストール<br>StorSimple Snapshot Manager の再インストール<br>StorSimple Snapshot Manager の削除 |[「Deploy StorSimple Snapshot Manager (StorSimple Snapshot Manager のデプロイ)」](storsimple-snapshot-manager-deployment.md) |
| StorSimple Snapshot Manager メニューと機能の使用方法 : <ul><li>メニュー バー</li><li>ツール バー</li><li>スコープ ウィンドウ</li><li>結果ウィンドウ</li><li>[操作] ウィンドウ</li><li>キーボード ナビゲーションとショートカット</li></ul> |[「StorSimple Snapshot Manager user interface (StorSimple Snapshot Manager のユーザー インターフェイス)」](storsimple-use-snapshot-manager.md) |
| StorSimple Snapshot Manager に含まれている一般的な MMC 機能の使用方法: <ul><li>表示</li><li>ここから新しいウィンドウ</li><li>更新</li><li>リストのエクスポート</li><li>Help</li></ul> |[「Use the MMC menu actions in StorSimple Snapshot Manager (StorSimple Snapshot Manager での MMC メニュー操作の使用)」](storsimple-snapshot-manager-mmc-menu.md) |
| デバイスの追加または置き換え<br>デバイスの接続<br>インポートされたボリューム グループの確認<br>接続されたデバイスの更新<br>デバイスの認証<br>デバイスの詳細の表示<br>デバイスの構成の削除<br>デバイスのパスワードの変更<br>障害が発生したデバイスの置き換え<br> |[StorSimple Snapshot Manager を使用した StorSimple デバイスの接続と管理](storsimple-snapshot-manager-manage-devices.md) |
| ボリュームのマウント<br>ボリュームに関する情報を表示する<br>ボリュームを削除する<br>ボリュームを再スキャンする<br>ベーシック ボリュームの構成とバックアップ<br>ダイナミック ミラー ボリュームの構成とバックアップ |[StorSimple Snapshot Manager を使用したボリュームの表示と管理](storsimple-snapshot-manager-manage-volumes.md) |
| ボリューム グループの表示<br>ボリューム グループの作成<br>ボリューム グループのバックアップ<br>ボリューム グループの編集<br>ボリューム グループの削除 |[StorSimple Snapshot Manager を使用したボリューム グループの作成と管理](storsimple-snapshot-manager-manage-volume-groups.md) |
| バックアップ ポリシーの作成 <br>バックアップ ポリシーの編集<br>バックアップ ポリシーの削除 |[「Use StorSimple Snapshot Manager to create and manage backup policies (StorSimple Snapshot Manager を使用したバックアップ ポリシーの作成と管理)」](storsimple-snapshot-manager-manage-backup-policies.md) |
| スケジュールされたバックアップ ジョブの表示と管理<br>最近実行されたバックアップ ジョブの表示と管理<br>現在実行中のバックアップ ジョブの表示と管理 |[「StorSimple Snapshot Manager を使用したバックアップ ジョブの表示と管理」](storsimple-snapshot-manager-manage-backup-jobs.md) |
| ボリュームの復元<br>ボリュームまたはボリューム グループの複製<br>バックアップの削除<br>ファイルの復元<br>Storsimple Snapshot Manager データベースの復元 |[「Use StorSimple Snapshot Manager to manage the backup catalog (StorSimple Snapshot Manager を使用したバックアップ カタログの管理)」](storsimple-snapshot-manager-manage-backup-catalog.md) |

## <a name="next-steps"></a>次のステップ
[StorSimple Snapshot Manager のダウンロード](https://www.microsoft.com/download/details.aspx?id=44220)。

