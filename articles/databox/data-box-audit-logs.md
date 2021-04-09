---
title: Azure Data Box、Azure Data Box Heavy イベントの監査ログ | Microsoft Docs
description: Azure Data Box と Azure Data Box Heavy の注文のさまざまな段階で収集される、Data Box の完全な監査ログについて説明します。
services: databox
author: alkohli
ms.service: databox
ms.subservice: pod
ms.topic: article
ms.date: 07/10/2020
ms.author: alkohli
ms.openlocfilehash: 50dbe9ab649a708fb36b1c9e4fe89bccadc7ea90
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "86209177"
---
# <a name="audit-logs-for-your-azure-data-box-and-azure-data-box-heavy"></a>Azure Data Box と Azure Data Box Heavy の監査ログ

ログは、時間の経過と共に発生した個別のイベントの変更できないタイムスタンプ付きのレコードです。 ログには、デバイスからの診断、監査、セキュリティ情報が含まれています。  

Data Box または Data Box Heavy の注文では、その操作中に、注文、設定、データのコピー、返却、Azure へのアップロードと確認、およびデータの消去という手順を行います。 これらの各手順では、すべてのイベントが監査され、ログに記録されます。

この記事には、ログの種類、収集される情報およびログの場所など、Data Box 監査ログに関する情報が含まれています。 

この記事の情報は、Data Box と Data Box Heavy の両方に適用されます。 後続のセクションにおいて、Data Box への言及はすべて、Data Box Heavy にも適用されます。 この記事では、Azure で実行されている Data Box サービスから収集されるログについては説明しません。 


## <a name="about-audit-logs"></a>監査ログについて 

Data Box では、次のログが収集されます。

- **システム ログ** - Data Box は Windows ベースのデバイスであるため、すべてのハードウェア、ソフトウェア、システム イベントがログに記録されます。 これらの一連のイベントが収集され、システム監査ログに記録されます。 

- **セキュリティ** - Data Box は Windows ベースのデバイスであるため、すべてのセキュリティ イベントがログに記録されます。 これらの一連のイベントが収集され、セキュリティ監査ログに記録されます。 

- **アプリケーション** - これらのログは Data Box のみに固有です。 これらのログには、実行中の Data Box サービスへの応答として、デバイス上で生成されたすべてのイベントが含まれます。

これらの各ログについては、以下のセクションで説明します。

### <a name="system-logs"></a>システム ログ

次のシステム ログのイベント ID は、Data Box でシステム監査ログとして収集されます。

|イベント プロバイダー名     |収集されるイベント ID   |イベントの説明   |
|-------------------|----------|----------------|
|Microsoft-Windows-Kernel-General|12  |OS が再起動された UTC 時間。   |
|                                |13  |OS がシャットダウンされた UTC 時間。 |
|    |                              |
|Microsoft-Windows-Kernel-Power  |41  |システムはクリーン シャットダウンなしで再起動されました。| 
|    |                              |
|Microsoft-Windows-BitLocker-Driver|All|    |

### <a name="security-logs"></a>セキュリティ ログ

次のセキュリティ ログのイベント ID は、Data Box のセキュリティ監査ログとして収集されます。

|イベント プロバイダー名                   |収集されるイベント ID    |イベントの説明       |
|--------------------------------------|------------|----------|
|Microsoft-Windows-Security-Auditing   |4624        |ログオンに成功しました。 |
|                                      |4625        |アカウントのログオンに失敗しました。 ユーザー名が不明であるか、パスワードが間違っています。 |
|                                     

### <a name="application-logs"></a>アプリケーション ログ

次のアプリケーション ログのイベント ID は、Data Box でパッケージ監査ログの一部として収集されます。     

- **Microsoft-Azure-DataBox-OOBE-Auditing** - ローカル UI で発生するイベントが含まれます。 
- **Microsoft-Azure-DataBox-Reprovision-Audit** - Data Box デバイスの再プロビジョニングに関するイベントが含まれます。 Data Box の再プロビジョニングは、ローカル UI を使用してデバイスがリセットされたときに発生します。 このオプションは、既存の共有を削除し、再プロビジョニングまたはデバイスのリセットの一環として共有を再作成することによって、コピーしたデータを消去する場合に選択します。
- **Microsoft-Azure-DataBox-HcsMgmt-Audit** - デバイスが Azure データセンターに返送される前の、**発送準備** の手順のみに関するイベントが含まれます。 
- **Microsoft-Azure-DataBox-IfxAudit** - ジョブに関する製品のさまざまなエンティティによってログに記録されたメッセージが含まれます。このログには、一部のフローで発生している内容の詳しい情報が示されます。

次の表は、さまざまなイベント プロバイダーと、それぞれのケースで収集される対応するイベント ID をまとめたものです。

|イベント プロバイダー名    |イベント ID    | Notes |
|-----------------|-----------------|-------------------|
|Microsoft-Azure-DataBox-OOBE-Auditing |4624        |ログオンに成功しました。|
|                                      |4625        |アカウントのログオンに失敗しました。 ユーザー名が不明であるか、パスワードが間違っています。|
|                                     |4634        |ログ オフ イベント。|
|                                   |  | |
|Microsoft-Azure-DataBox-Reprovision-Audit    |65001       |イベントの再プロビジョニングに成功しました。|
|                                                  |65002       |イベントを再プロビジョニングできませんでした。|
|                                                  |                 |         |
|Microsoft-Azure-DataBox-HcsMgmt-Audit        |65003       |発送準備の状態イベント、NotStarted、InProgress、Failed、Canceled、Succeeded、ScanCompletedWithIssues、SucceededWithWarnings          |
|                                                  |                 |     |
|Microsoft-Azure-DataBox-IfxAudit    |All |すべてのイベントは、コードの監査ログ API を使用してログに記録されます |

インストルメンテーション フレームワーク (IFX) 監査ログの例を以下に示します。

|     タスク/ジョブ/API                              |     ログに記録されるイベント                                                                                                              | 
|-----------------------------------------------|------------------------------------------------------------------------------------------------------------------------------|
|     クリーンアップ                                   |     クリーンアップ ジョブの開始、完了、または失敗に関するイベントがログに記録されます。 |                                              
|     顧客への出荷用にデバイスを準備する    |     出荷用にデバイスを準備するジョブの開始、完了、または失敗に関するイベントがログに記録されます。 |
|     プロビジョニング                                 |     デバイス プロビジョニング ジョブの開始、完了、または失敗に関するイベントがログに記録されます。|
|     監査パッケージ ジョブ                       |     生産物流管理ログを作成する監査パッケージ ジョブの開始、完了、または失敗に関するイベントがログに記録されます。|
|     ディスクの上書き                          |     ディスクの上書きの失敗がログに記録されます。|
|     リモート PowerShell を有効または無効にする     |     デバイスでのリモート PowerShell の有効化または無効化に関するイベントがログに記録されます。 |
|     インストール フェーズの詳細を取得する               |     段階的なデバイスへのソフトウェアのインストールに関するイベントは、Azure データセンターでログに記録されます。|
|     BitLocker ボリュームのロックを解除またはロックする           |     *basevolume* と *hcsdata* ボリュームの BitLocker 状態を示すイベントがログに記録されます。|
|     ディスクをサニタイズする                              |     消去できなかった物理ディスクの失敗に関するイベントと、デバイス上のすべての物理ディスクが正常に消去されたときのイベントがログに記録されます。 |
|     ローカル ユーザーを有効または無効にする               |     StorSimpleAdmin と PodSupportAdminUser のローカル ユーザー アカウントの有効化または無効化に関するイベントがログに記録されます。| 
|     パスワードのリセット                          |     ローカル StorSimpleAdmin ユーザーのパスワード リセットの成功または失敗に関するイベントがログに記録されます。 |


IFX 監査ログとは別に、Data Box のために一連の生産物流管理監査ログも収集されます。 これらのログは、リアル タイムで表示することはできませんが、ジョブが完了し、Data Box ディスクからデータが消去された後であれば表示できます。 これらのログには、IFX 監査ログに含まれる情報のサブセットが含まれます。

生産物流管理監査ログの詳細については、「[データの消去後に生産物流管理ログを取得する](data-box-logs.md#get-chain-of-custody-logs-after-data-erasure)」を参照してください。

<!-- write a few lines about order history and link out to the detailed section on order history-->

## <a name="access-audit-logs"></a>監査ログにアクセスする

これらのログは Azure に格納されており、直接アクセスすることはできません。 これらのログにアクセスする必要がある場合は、サポート チケットを提出してください。 詳細については、「 [Microsoft サポートに問い合わせる](data-box-disk-contact-microsoft-support.md)」を参照してください。 

サポート チケットが提出されると、Microsoft でこれらのログをダウンロードしてアクセスできるようにします。


## <a name="next-steps"></a>次のステップ

- [Data Box および Data Box Heavy の問題のトラブルシューティング](data-box-troubleshoot.md)方法を確認します。
