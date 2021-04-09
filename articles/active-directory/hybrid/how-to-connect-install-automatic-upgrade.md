---
title: Azure AD Connect:自動アップグレード | Microsoft Docs
description: このトピックでは、Azure AD Connect Sync の組み込みの自動アップグレード機能について説明します。
services: active-directory
documentationcenter: ''
author: billmath
manager: daveba
editor: ''
ms.assetid: 6b395e8f-fa3c-4e55-be54-392dd303c472
ms.service: active-directory
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 06/09/2020
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 9c8dcc8766b21551f3cd62289805fe735ef0f333
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "91317618"
---
# <a name="azure-ad-connect-automatic-upgrade"></a>Azure AD Connect:自動アップグレード
この機能は、ビルド [ 1.1.105.0 (2016 年 2 月リリース) で導入されました](reference-connect-version-history.md)。  この機能は[ビルド 1.1.561](reference-connect-version-history.md) で更新され、以前サポートされていなかった追加のシナリオがサポートされています。

## <a name="overview"></a>概要
Azure AD Connect のインストールを常に最新の状態に保つことは、 **自動アップグレード** 機能によって、これまでよりも簡単になりました。 この機能は、高速インストールと DirSync のアップグレード用に既定で有効になっています。 インストールは新しいバージョンのリリース時に自動的にアップグレードされます。
自動アップグレードは、次の場合に既定で有効です。

* 簡単設定インストールと DirSync のアップグレード。
* SQL Express LocalDB を使用している (簡単設定で常に使用されます)。 SQL Express を使用した DirSync でも、LocalDB が使用されます。
* AD アカウントが、簡単設定と DirSync によって作成される既定の MSOL_ アカウントである場合。
* メタバース内のオブジェクト数が 100,000 未満。

自動アップグレードの現在の状態は、PowerShell コマンドレット `Get-ADSyncAutoUpgrade`で表示できます。 次のような状態があります。

| State | 解説 |
| --- | --- |
| Enabled |自動アップグレードが有効です。 |
| Suspended |システムによる設定だけが可能です。 システムは、自動アップグレードを **現在受け付けることができません**。 |
| 無効 |自動アップグレードが無効です。 |

`Set-ADSyncAutoUpgrade` を使用して、**有効** と **無効** を切り替えることができます。 システムだけが、状態を **保留** に設定することができます。  1\.1.750.0 より前は、自動アップグレードの状態が一時停止に設定されている場合に、Set-ADSyncAutoUpgrade コマンドレットによって Autoupgrade がブロックされていました。 この機能は変更されたため、AutoUpgrade はブロックされません。

自動アップグレードでは、アップグレード インフラストラクチャに Azure AD Connect Health を使用しています。 自動アップグレードを動作させるには、「 **Office 365 URL および IP アドレス範囲** 」に記載されているように、 [Azure AD Connect Health](https://support.office.com/article/Office-365-URLs-and-IP-address-ranges-8548a211-3fe7-47cb-abb1-355ea5aa88a2)用にプロキシ サーバーで URL を開いておく必要があります。


**Synchronization Service Manager** UI がサーバーで実行されている場合は、UI が閉じられるまで、アップグレードが中断されます。

## <a name="troubleshooting"></a>トラブルシューティング
インストール済みの Connect が想定されているとおりに自動的にアップグレードしない場合は、次の手順を実行して問題の原因を特定してください。

注意点として、自動アップグレードは新しいバージョンがリリースされた当日に試行されるわけではありません。 アップグレードを試行する前に意図的にランダムな時間をおくため、アップグレードがすぐに実行されなくても心配する必要はありません。

問題が生じていると思われる場合は、最初に `Get-ADSyncAutoUpgrade` を実行して、自動アップグレードが有効になっていることを確認してください。

状態が中断になっている場合は、`Get-ADSyncAutoUpgrade -Detail` を使用して理由を表示できます。  中断の理由には任意の文字列値を含めることができますが、通常は UpgradeResult の文字列値 (つまり、`UpgradeNotSupportedNonLocalDbInstall` または `UpgradeAbortedAdSyncExeInUse`) が格納されます。  `UpgradeFailedRollbackSuccess-GetPasswordHashSyncStateFailed` などの複合値が返される場合もあります。

また、UpgradeResult ではない結果 (つまり、"AADHealthEndpointNotDefined" または "DirSyncInPlaceUpgradeNonLocalDb") を取得する場合もあります。

その後、プロキシまたはファイアウォールで必要な URL を開いていることを確認してください。 自動更新では、「 [概要](#overview)」で説明されているように、Azure AD Connect Health が使用されています。 プロキシを使用する場合は、 [プロキシ サーバー](how-to-connect-health-agent-install.md#configure-azure-ad-connect-health-agents-to-use-http-proxy)を使用するよう Health が構成されていることを確認します。 また、Azure AD に対する [Health の接続](how-to-connect-health-agent-install.md#test-connectivity-to-azure-ad-connect-health-service) もテストします。

Azure AD への接続が確認されたら、イベント ログを調査します。 イベント ビューアーを起動し、 **[アプリケーション]** イベントログを確認します。 ソースとして **[Azure AD Connect Upgrade (Azure AD Connect のアップグレード)]** を選択し、イベント ID 範囲に「**300-399**」を指定したイベント ログ フィルターを追加します。  
![[現在のログをフィルター] ウィンドウで、[Event sources]\(イベント ソース\) および [Include/Exclude Event IDs]\(含める/除外するイベント ID\) が強調表示されているスクリーンショット。](./media/how-to-connect-install-automatic-upgrade/eventlogfilter.png)  

これで、自動アップグレードの状態に関連するイベント ログが表示されます。  
![自動アップグレードのイベントログ フィルター](./media/how-to-connect-install-automatic-upgrade/eventlogresult.png)  

結果コードには、状態についての概要を含むプレフィックスが付加されています。

| 結果コードのプレフィックス | 説明 |
| --- | --- |
| Success |正常にアップグレードしました。 |
| UpgradeAborted |一時的な状況により、アップグレードが停止しました。 後でもう一度試すと、成功することが予想されます。 |
| UpgradeNotSupported |システム内に、自動アップグレードをブロックしている構成があります。 状態が変更中であるかどうかを確認するためにもう一度試行されますが、システムを手動でアップグレードする必要があると考えられます。 |

特によく表示されるメッセージの一覧を次に示します。 これですべてではありませんが、結果メッセージを見ると、何が問題となっているかが把握しやすくなります。

| 結果メッセージ | 説明 |
| --- | --- |
| **UpgradeAborted** | |
| UpgradeAbortedCouldNotSetUpgradeMarker |レジストリに書き込むことができません。 |
| UpgradeAbortedInsufficientDatabasePermissions |組み込みの Administrators グループにデータベースへのアクセス許可がありません。 これを解決するには、最新バージョンの Azure AD Connect に手動でアップグレードしてください。 |
| UpgradeAbortedInsufficientDiskSpace |アップグレードをサポートするのに十分なディスク領域がありません。 |
| UpgradeAbortedSecurityGroupsNotPresent |同期エンジンで使用されるすべてのセキュリティ グループが見つからず、解決できません。 |
| UpgradeAbortedServiceCanNotBeStarted |NT サービス **Microsoft Azure AD Sync** を起動できませんでした。 |
| UpgradeAbortedServiceCanNotBeStopped |NT サービス **Microsoft Azure AD Sync** を停止できませんでした。 |
| UpgradeAbortedServiceIsNotRunning |NT サービス **Microsoft Azure AD Sync** が実行されていません。 |
| UpgradeAbortedSyncCycleDisabled |[スケジューラ](how-to-connect-sync-feature-scheduler.md) の SyncCycle オプションが無効になっています。 |
| UpgradeAbortedSyncExeInUse |サーバーで [Sychronization Service Manager UI](how-to-connect-sync-service-manager-ui.md) が開いています。 |
| UpgradeAbortedSyncOrConfigurationInProgress |インストール ウィザードが実行されているか、同期がスケジューラ以外の場所でスケジュールされました。 |
| **UpgradeNotSupported** | |
| UpgradeNotSupportedCustomizedSyncRules |ユーザーが構成に独自のカスタム ルールを追加しました。 |
| UpgradeNotSupportedInvalidPersistedState |インストールが簡単設定でも DirSync のアップグレードでもありません。 |
| UpgradeNotSupportedNonLocalDbInstall |SQL Server Express LocalDB データベースが使用されていません。 |
|UpgradeNotSupportedLocalDbSizeExceeded|ローカル DB のサイズが 8 GB 以上です。|
|UpgradeNotSupportedAADHealthUploadDisabled|正常性データのアップロードがポータルから無効になっています。|

## <a name="next-steps"></a>次のステップ
「 [オンプレミス ID と Azure Active Directory の統合](whatis-hybrid-identity.md)」をご覧ください。
