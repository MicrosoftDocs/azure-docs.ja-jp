---
title: 予定メンテナンス - Azure Database for PostgreSQL - フレキシブル サーバー
description: この記事では、Azure Database for PostgreSQL - フレキシブル サーバーでの予定メンテナンス機能について説明します。
author: niklarin
ms.author: nlarin
ms.service: postgresql
ms.topic: conceptual
ms.date: 09/22/2020
ms.openlocfilehash: ffee15776a48b6495f78b6becf81c620e1dc4d69
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "91336311"
---
# <a name="scheduled-maintenance-in-azure-database-for-postgresql--flexible-server"></a>Azure Database for PostgreSQL での予定メンテナンス - フレキシブル サーバー
 
Azure Database for PostgreSQL - フレキシブル サーバーでは、管理対象のデータベースを安全で安定した最新状態に保持するために定期的なメンテナンスが実行されます。 メンテナンス中に、サーバーでは新しい機能、更新プログラム、パッチが取得されます。
 
> [!IMPORTANT]
> Azure Database for PostgreSQL - フレキシブル サーバーはプレビュー段階です
 
## <a name="selecting-a-maintenance-window"></a>メンテナンス期間の選択
 
特定の曜日とその日の時間帯の間でのメンテナンスをスケジュールできます。 また、システムで自動的に曜日と時間枠が選択されるようにすることもできます。 どちらの場合でも、メンテナンスを実行する 5 日前に通知されます。 また、メンテナンスが開始されたときと、正常に完了したときに通知されるようにすることもできます。
 
今後の予定メンテナンスについて、次のように通知できます。
 
* 特定のアドレスにメールで送信する
* Azure Resource Manager のロールにメールで送信する
* テキスト メッセージ (SMS) でモバイル デバイスに送信する
* 通知として Azure アプリにプッシュする
* 音声メッセージとして配信する
 
メンテナンス スケジュールの設定を指定する場合、曜日と時間帯を選択できます。 指定しない場合、システムでは、サーバーのリージョンの時刻で午後 11 時から午前 7 時までの時間が選択されます。 Azure サブスクリプションのフレキシブル サーバーごとに異なるスケジュールを定義できます。 
 
> [!IMPORTANT]
> 通常、サーバーの正常な予定メンテナンス イベントの間隔は 30 日間以上です。
>
> ただし、重大な脆弱性などのクリティカルな緊急更新プログラムの場合、通知期間は 5 日未満になる可能性があります。 過去 30 日間に予定メンテナンスが正常に実行された場合でも、重要な更新プログラムがサーバーに適用されることがあります。

スケジュール設定はいつでも更新できます。 フレキシブル サーバーのメンテナンスがスケジュールされている場合に、スケジュールの優先順位を更新すると、現在のイベントはスケジュールどおりに実行され、スケジュール設定の変更は正常に完了した時点で有効になります。 

メンテナンス イベントがシステムで取り消された場合や正常に完了しなかった場合は、取り消されたメンテナンス イベントまたは失敗したメンテナンス イベントについての通知がそれぞれ作成されます。 次にメンテナンスを実行しようとすると、現在のスケジュール設定に従ってスケジュールが設定され、5 日前に通知が届きます。
 
## <a name="next-steps"></a>次のステップ
 
* [メンテナンス スケジュールを変更する](how-to-maintenance-portal.md)方法を確認する
* Azure Service Health を使用して[今後のメンテナンスに関する通知を受け取る](../../service-health/service-notifications.md)方法を確認する
* [今後の予定メンテナンス イベントに関する通知を設定する](../../service-health/resource-health-alert-monitor-guide.md)方法を確認する
