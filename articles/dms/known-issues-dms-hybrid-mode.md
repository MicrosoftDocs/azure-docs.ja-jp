---
title: ハイブリッド モードの使用に関する既知の問題と移行の制限事項
description: ハイブリッド モードでの Azure Database Migration Service の使用に関する既知の問題と移行の制限事項について説明します。
services: database-migration
author: pochiraju
ms.author: rajpo
manager: craigg
ms.reviewer: craigg
ms.service: dms
ms.workload: data-services
ms.custom: mvc
ms.topic: troubleshooting
ms.date: 02/20/2020
ms.openlocfilehash: 819fe5ced6c91819c817065305a31fca456ea5c0
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "91291845"
---
# <a name="known-issuesmigration-limitations-with-using-hybrid-mode"></a>ハイブリッド モードの使用に関する既知の問題と移行の制限事項

以下のセクションでは、ハイブリッド モードでの Azure Database Migration Service の使用に関連する既知の問題と制限事項について説明します。

## <a name="installer-fails-to-authenticate"></a>インストーラーで認証に失敗する

証明書を AdApp にアップロードした後、Azure で認証できるようになるまでに数分の待機時間があります。 インストーラーではしばらくして再試行が試みられますが、伝達の待機時間が再試行よりも長くなる可能性があり、**FailedToGetAccessTokenException** メッセージが表示されます。 証明書が適切な AdApp にアップロードされ、適切な AppId が dmsSettings.json に指定されている場合は、インストール コマンドをもう一度実行してみてください。

## <a name="service-offline-after-successful-installation"></a>インストールが正常に完了した後のサービスの "オフライン"

インストール プロセスが正常に完了した後にサービスがオフラインと表示される場合は、次の手順を試してください。

1. Azure portal の Azure Database Migration Service のインスタンスで、 **[ハイブリッド]** 設定タブに移動し、登録されている worker のグリッドを見て、worker が登録されていることを確認します。

    この worker の状態は "**オンライン**" のはずですが、問題がある場合は "**オフライン**" と表示されることがあります。

2. worker コンピューターで、次の PowerShell コマンドを実行してサービスの状態を確認します。

    ```
    Get-Service Scenario*
    ```

    このコマンドを実行すると、worker を実行している Windows サービスの状態がわかります。 結果は 1 つのみです。 worker が停止している場合は、次の PowerShell コマンドを使用して worker を再開することができます。

    ```
    Start-Service Scenario*
    ```

    また、Windows サービス UI でサービスを確認することもできます。

3. Windows サービスの "実行中" と "停止" が交互に表示される場合、worker の起動中に問題が発生しています。 問題を特定するには、Azure Database Migration Service ハイブリッド worker ログを確認してください。

    - インストール プロセスのログは、インストーラーの実行可能ファイルが実行されたフォルダー内の "logs" フォルダーに格納されます。
    - Azure Database Migration Service ハイブリッド worker ログは、worker がインストールされているフォルダー内の **WorkerLogs** フォルダーに格納されます。 ハイブリッド worker ログ ファイルの既定の場所は **C:\Program Files\DatabaseMigrationServiceHybrid\WorkerLogs** です。

## <a name="using-your-own-signed-certificate"></a>独自の署名証明書の使用

アクション GenerateCert によって生成される証明書は自己署名証明書ですが、内部セキュリティ ポリシーによっては許容されない場合があります。 この証明書を使用する代わりに、独自の証明書を用意し、dmsSettings.json にその拇印を指定することができます。 この証明書を AdApp にアップロードし、Azure Database Migration Service ハイブリッド worker をインストールするコンピューターにインストールする必要があります。 次に、この証明書を秘密キーと共にローカル コンピューターの証明書ストアにインストールします。

## <a name="running-the-worker-service-as-a-low-privilege-account"></a>低い特権のアカウントとして worker サービスを実行する

既定では、Azure Database Migration Service ハイブリッド worker サービスはローカル システム アカウントとして実行されます。 使用するアカウントにネットワーク アクセス許可がある限り、このサービスに使用するアカウントを変更できます。 サービスの "実行" アカウントを変更するには、次の手順を使用します。

1. Windows サービスを使用するか、PowerShell で Stop-Service コマンドを使用してサービスを停止します。

2. 別のログオン アカウントを使用するようにサービスを更新します。

3. ローカル コンピューター証明書の certmgr で、**DMS Hybrid App Key** および **DMS Scenario Engine Key Pair** 証明書の新しいアカウントに秘密キーのアクセス許可を付与します。

    a. certmgr を開き、次のキーを表示します。

    - DMS Hybrid App Key
    - DMS Hybrid Worker Setup Key
    - DMS Scenario Engine Key Pair

    b. **[DMS Hybrid App Key]** エントリを右クリックし、 **[すべてのタスク]** をポイントして、 **[秘密キーの管理]** を選択します。

    c. **[セキュリティ]** タブで **[追加]** を選択し、アカウントの名前を入力します。

    d. 同じ手順を使用して、新しいアカウントの秘密キーのアクセス許可を **DMS Scenario Engine Key Pair** 証明書に付与します。

## <a name="unregistering-the-worker-manually"></a>手動で worker の登録を解除する

worker コンピューターへのアクセス権がなくなった場合は、次の手順を実行して、worker の登録を解除し、Azure Database Migration Service インスタンスを再利用することができます。

1. Azure portal で Azure Database Migration Service インスタンスに移動し、 **[ハイブリッド]** 設定ページに移動します。

   worker エントリが一覧に表示され、状態が "**オフライン**" と表示されます。

2. worker エントリの一覧の右端にある省略記号を選択し、 **[登録解除]** を選択します。

## <a name="addressing-issues-for-specific-migration-scenarios"></a>特定の移行シナリオに関する問題の解決

以下のセクションでは、Azure Database Migration Service ハイブリッド モードを使用してオンライン移行を実行する場合に関連するシナリオ固有の問題について説明します。

### <a name="online-migrations-to-azure-sql-managed-instance"></a>Azure SQL Managed Instance へのオンライン移行

**CPU 使用率が高い**

**問題点**: SQL Managed Instance へのオンライン移行の場合、バックアップの数が多すぎるか、バックアップのサイズが大きすぎると、ハイブリッド worker を実行しているコンピューターの CPU 使用率が高くなります。

**対応策**:この問題を軽減するには、圧縮されたバックアップを使用するか、複数の共有を使用するように移行を分割するか、ハイブリッド worker を実行しているコンピューターをスケールアップします。
