---
title: Azure AD Connect Health - アラート "Health サービス データが最新ではありません" | Microsoft Docs
description: このドキュメントでは、アラート "Health サービス データが最新ではありません" の原因と、そのトラブルシューティングを実行する方法について説明します。
services: active-directory
documentationcenter: ''
author: billmath
manager: daveba
editor: ''
ms.service: active-directory
ms.subservice: hybrid
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.date: 02/26/2018
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 00518eb91e57efaacb7abc63b6ad4531619be2ce
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "98012871"
---
# <a name="health-service-data-is-not-up-to-date-alert"></a>アラート "Health サービス データが最新ではありません"

## <a name="overview"></a>概要

Azure AD Connect Health で監視されているオンプレミスのマシン上のエージェントによって、Azure AD Connect Health サービスにデータが定期的にアップロードされます。 サービスでエージェントからのデータが受信されない場合、ポータルに表示される情報が古くなります。 問題を強調するため、サービスで "**Health サービス データが最新ではありません**" アラートが発生します。 これのアラートは、サービスで過去 2 時間以内に完全なデータが受信されていない場合に生成されます。  

- Health サービスで、過去 2 時間以内にサーバーから送信された **部分的な** データ型のみを受信した場合、**警告** ステータス アラートが発生します。 警告状態通知では、構成されている受信者への電子メール通知はトリガーされません。 
- Health サービスで、過去 2 時間以内にサーバーからいずれのデータ型も受信しなかった場合、**エラー** ステータス アラートが発生します。 エラー状態通知では、構成されている受信者への電子メール通知がトリガーされます。

サービスでは、サービスの種類によって、オンプレミス マシンで実行されているエージェントからデータが取得されます。 次の表に、マシンで実行されるエージェント、行われる処理、サービスによって生成されるデータ型を示します。 場合によっては、複数のサービスがプロセスに関与するため、いずれかのサービスが原因になることがあります。 

## <a name="understanding-the-alert"></a>アラートの概要

アラートが発生し、過去に検出されていた場合、**アラートの詳細** ブレードが表示されます。 2 時間おきに実行されるバックグラウンド プロセスで、アラートが生成され、再評価されます。 次の例では、最初のアラートが 3 月 10 日午前 9 時 59 分に発生しました。 アラートが再評価されたとき、アラートは 3 月 12 日午前 10 時 00 分にまだ存在していました。 ブレードには、Health サービスで特定のデータ型を前回受信した時刻も詳述されます。 
 
 ![Azure AD Connect Health のアラート詳細](./media/how-to-connect-health-data-freshness/data-freshness-details.png)
 
次の表で、サービスの種類を、対応する必須データ型にマップします。

| サービスの種類 | エージェント (Windows サービス名) | 目的 | 生成されるデータ型  |
| --- | --- | --- | --- |  
| Azure AD Connect (同期) | Azure AD Connect Health Sync 分析サービス | AAD Connect 固有の情報を収集する (コネクタ、同期規則など) | - AadSyncService-SynchronizationRules <br />  - AadSyncService-Connectors <br /> - AadSyncService-GlobalConfigurations  <br />  - AadSyncService-RunProfileResults <br /> - AadSyncService-ServiceConfigurations <br /> - AadSyncService-ServiceStatus   |
|  | Azure AD Connect Health Sync 監視サービス | AAD Connect 固有の Perf counters、ETW トレース、ファイルを収集する | パフォーマンス カウンター |
| AD DS | Azure AD Connect Health AD DS Insights Service | 合成テストを実行し、トポロジ情報、レプリケーション メタデータを収集する |  - Adds-TopologyInfo-Json <br /> - Common-TestData-Json (テスト結果を作成する)   | 
|  | Azure AD Connect Health AD DS Monitoring Service | ADDS 固有の Perf counters、ETW トレース、ファイルを収集する | - パフォーマンス カウンター  <br /> - Common-TestData-Json (テスト結果をアップロードする)  |
| AD FS | Azure AD Connect Health AD FS Diagnostics Service | 合成テストを実行する | TestResult (テスト結果を作成する) | 
| | Azure AD Connect Health AD FS Insights Service  | ADFS の使用状況メトリックを収集する | Adfs-UsageMetrics |
| | Azure AD Connect Health AD FS Monitoring Service | ADFS 固有の Perf counters、ETW トレース、ファイルを収集する | TestResult (テスト結果をアップロードする) |

## <a name="troubleshooting-steps"></a>トラブルシューティングの手順 

問題の診断に必要な手順は次のとおりです。 最初に、すべてのサービスの種類に共通する一連の基本的なチェックを行います。 

> [!IMPORTANT] 
> このアラートは、Connect Health [データ リテンション期間ポリシー](reference-connect-health-user-privacy.md#data-retention-policy)に従います。

* 最新バージョンのエージェントがインストールされていることを確認します。 [リリース履歴](reference-connect-health-version-history.md)を表示します。 
* Azure AD Connect Health エージェント サービスが、マシンで **実行されている** ことを確認します。 たとえば、Connect Health for AD FS には 3 つのサービスが必要です。
  ![Azure AD Connect Health の確認](./media/how-to-connect-health-agent-install/install5.png)

* [要件セクション](how-to-connect-health-agent-install.md#requirements)を確認して、必ず要件に対応してください。
* [テスト接続ツール](how-to-connect-health-agent-install.md#test-connectivity-to-azure-ad-connect-health-service)を使用して接続の問題を検出します。
* HTTP プロキシを使用する場合は、こちらの[構成手順](how-to-connect-health-agent-install.md#configure-azure-ad-connect-health-agents-to-use-http-proxy)に従います。 


## <a name="next-steps"></a>次のステップ
上記の手順のいずれかで問題が見つかった場合、問題を修正し、アラートが解決するまで待ちます。 アラートのバック グラウンド プロセスは 2 時間ごとに実行されるため、アラートを解決するには最大 2 時間かかります。 

* [Azure AD Connect Health のデータ リテンション期間ポリシー](reference-connect-health-user-privacy.md#data-retention-policy)
* [Azure AD Connect Health の FAQ](reference-connect-health-faq.md)
