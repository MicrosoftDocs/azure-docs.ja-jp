---
title: 予定メンテナンス - Azure Database for MySQL - フレキシブル サーバー
description: この記事では、Azure Database for MySQL - フレキシブル サーバーでの予定メンテナンス機能について説明します。
author: niklarin
ms.author: nlarin
ms.service: mysql
ms.topic: conceptual
ms.date: 09/21/2020
ms.openlocfilehash: a2e99440a7c8f33eee9d3c9fe2276ac3868ff4b6
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "91331762"
---
# <a name="scheduled-maintenance-in-azure-database-for-mysql--flexible-server"></a>Azure Database for MySQL での予定メンテナンス - フレキシブル サーバー

Azure Database for MySQL - フレキシブル サーバーでは、管理対象のデータベースを安全で安定した最新の状態に保つために定期的なメンテナンスが実行されます。 メンテナンス中に、サーバーでは新しい機能、更新プログラム、パッチが取得されます。

> [!IMPORTANT]
> Azure Database for MySQL - フレキシブル サーバーはプレビュー段階です。

## <a name="select-a-maintenance-window"></a>メンテナンス期間を選択する

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

まれに、メンテナンス イベントがシステムによって取り消されたり、正常に完了しなかったりすることがあります。 この場合、取り消されたメンテナンス イベントまたは失敗したメンテナンス イベントについての通知がそれぞれ作成されます。 次にメンテナンスを実行しようとすると、現在のスケジュール設定に従ってスケジュールが設定され、5 日前に通知が届きます。

## <a name="next-steps"></a>次のステップ

* [メンテナンス スケジュールを変更する](how-to-maintenance-portal.md)方法を確認する
* Azure Service Health を使用して[今後のメンテナンスに関する通知を受け取る](../../service-health/service-notifications.md)方法を確認する
* [今後の予定メンテナンス イベントに関する通知を設定する](../../service-health/resource-health-alert-monitor-guide.md)方法を確認する
