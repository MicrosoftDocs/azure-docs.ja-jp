---
title: エージェントレスの VMware VM 移行におけるレプリケーションの問題のトラブルシューティング
description: レプリケーション サイクルの失敗に関するヘルプを表示する
author: anvar-ms
ms.author: anvar
ms.manager: bsiva
ms.topic: troubleshooting
ms.date: 08/17/2020
ms.openlocfilehash: 33e2bf641b75a5dd360498478f1ea70c7614fb38
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "98071376"
---
# <a name="troubleshooting-replication-issues-in-agentless-vmware-vm-migration"></a>エージェントレスの VMware VM 移行におけるレプリケーションの問題のトラブルシューティング

この記事では、オンプレミス VMware VM をレプリケートする場合に発生する可能性がある、いくつかの一般的な問題と特定のエラーについて説明します。レプリケートには、Azure Migrate:サーバー移行のエージェントレス方式を使用します。

エージェントレス レプリケーション方式を使用して VMware 仮想マシンをレプリケートする場合、仮想マシン ディスク (vmdks) のデータは、Azure サブスクリプションのレプリカ マネージド ディスクにレプリケートされます。 VM のレプリケーションが開始されると、初期レプリケーション サイクルが発生し、ディスクの完全なコピーがレプリケートされます。 初期レプリケーションが完了した後、前回のレプリケーション サイクル以降に発生したすべての変更を転送するために、増分レプリケーション サイクルが定期的にスケジュールされます。

場合によっては、VM のレプリケーション サイクルが失敗することがあります。 これらのエラーは、オンプレミスのネットワーク構成の問題から、Azure Migrate クラウド サービスのバックエンドの問題まで、さまざまな理由により発生します。 この記事では、次のことを行います。

 - レプリケーションの状態を監視し、エラーを解決する方法について説明します。
 - 一般的に発生するレプリケーション エラーの一部を一覧表示し、それらを修復するための追加の手順を提案します。

## <a name="monitor-replication-status-using-the-azure-portal"></a>Azure portal を使用してレプリケーションの状態を監視する

仮想マシンのレプリケーションの状態を監視するには、次の手順に従います。

  1. Azure portal で Azure Migrate の [サーバー] ページにアクセスします。
  ![画像 1](./media/troubleshoot-changed-block-tracking-replication/image0.png)
  1. [サーバー移行] タイルの [サーバーをレプリケートしています] をクリックして、[マシンのレプリケート] ページに移動します。
  ![画像 2](./media/troubleshoot-changed-block-tracking-replication/image1.png)
  1. レプリケートしているサーバーの一覧と、状態、正常性、最終同期時刻などの追加情報が表示されます。[正常性] 列には、VM の現在のレプリケーションの正常性が示されます。 [正常性] 列の 'クリティカル' または '警告' 値は、通常、その VM で以前のレプリケーション サイクルが失敗したことを示します。 詳細を表示するには、VM を右クリックし、[エラーの詳細] を選択します。 [エラーの詳細] ページには、エラーの情報と、トラブルシューティングの方法に関する追加情報が表示されます。 また、VM のイベント ページに移動するために使用できる [最近のイベント] リンクも表示されます。
  ![画像 3](./media/troubleshoot-changed-block-tracking-replication/image2.png)
  1. [最近のイベント] をクリックして、VM の以前のレプリケーション サイクル エラーを確認します。 イベントページで、その VM の "レプリケーション サイクルが失敗しました" または "ディスクのレプリケーション サイクルが失敗しました" という種類の最新のイベントを探します。
  ![画像 4](./media/troubleshoot-changed-block-tracking-replication/image3.png)
  1. イベントをクリックすると、エラーの考えられる原因と推奨される修復手順がわかります。 表示された情報を使用して、エラーのトラブルシューティングと修復を行います。
 ![画像 5](./media/troubleshoot-changed-block-tracking-replication/image4.png)

## <a name="common-replication-errors"></a>一般的なレプリケーション エラー

ここでは、いくつかの一般的なエラーとそのトラブルシューティング方法について説明します。

## <a name="key-vault-operation-failed-error-when-trying-to-replicate-vms"></a>VM をレプリケートしようとしたときにエラーが発生して Key Vault の操作が失敗した

**エラー:** “Key Vault の操作が失敗しました。 操作:マネージド ストレージ アカウントの構成、Key Vault:Key-vault-name、ストレージ アカウント: エラーで失敗したストレージ アカウント名:"

**エラー:** “Key Vault の操作が失敗しました。 操作:Shared Access Signature 定義の生成、Key Vault:Key-vault-name、ストレージ アカウント: エラーで失敗したストレージ アカウント名:"

![Key Vault](./media/troubleshoot-changed-block-tracking-replication/key-vault.png)

一般的に、このエラーが発生するのは、Key Vault のユーザー アクセス ポリシーによって、現在ログインしているユーザーに、Key Vault で管理されるストレージ アカウントを構成するために必要なアクセス許可が付与されていないためです。 Key Vault のユーザー アクセス ポリシーを確認するには、Key Vault のポータルの [Key Vault] ページにアクセスして、[アクセス ポリシー] を選択します。 

ポータルでキー コンテナーが作成されると、ユーザー アクセス ポリシーも追加され、現在ログインしているユーザーに Key Vault で管理されるストレージ アカウントを構成するためのアクセス許可が付与されます。 これは、2 つの理由で失敗する可能性があります

- ログインしたユーザーが、顧客の Azure テナント (CSP サブスクリプション - ログインしているユーザーはパートナー管理者) のリモート プリンシパルである場合です。 この場合の回避策は、キー コンテナーを削除し、ポータルからログアウトしてから、(リモート プリンシパルではなく) 顧客テナントからのユーザー アカウントでログインして、操作を再試行することです。 CSP パートナーには、通常、使用できるユーザー アカウントが顧客の Azure Active Directory テナントにあります。 ない場合は、顧客の Azure Active Directory テナントに自分用の新しいユーザー アカウントを作成し、その新しいユーザーとしてポータルにログインしてから、レプリケート操作を再試行できます。 使用するアカウントには、リソース グループ (Migrate プロジェクト リソース グループ) のアカウントに付与された所有者または共同作成者のアクセス許可とユーザー アクセス管理者のアクセス許可が必要です。

- この問題が起こるその他のケースとして、あるユーザー (user1) が最初にレプリケーションをセットアップしようとしてエラーが発生したが、キー コンテナーが既に作成されている (およびユーザー アクセス ポリシーがこのユーザーに適切に割り当てられている) 場合があります。 後で、別のユーザー (user2) がレプリケーションのセットアップを試みますが、キー コンテナー内の user2 に対応するユーザー アクセス ポリシーがないため、マネージド ストレージ アカウントの構成操作または SAS 定義の生成操作は失敗します。

**解決方法**:この問題を回避するには、キー コンテナーに user2 のユーザー アクセス ポリシーを作成し、user2 にマネージド ストレージ アカウントの構成と SAS 定義の生成を行うためのアクセス許可を付与します。 user2 は、Azure PowerShell から次のコマンドレットを使用してこれを行うことができます。

$userPrincipalId = $(Get-AzureRmADUser -UserPrincipalName "user2_email_address").Id

Set-AzureRmKeyVaultAccessPolicy -VaultName "keyvaultname" -ObjectId $userPrincipalId -PermissionsToStorage get, list, delete, set, update, regeneratekey, getsas, listsas, deletesas, setsas, recover, backup, restore, purge


## <a name="disposeartefactstimedout"></a>DisposeArtefactsTimedOut

**エラー ID:** 181008

**エラー メッセージ:** VM:VMName。 エラー:Encountered timeout event 'DisposeArtefactsTimeout' in the state &'['Gateway.Service.StateMachine.SnapshotReplication.SnapshotReplicationEngine+WaitingForArtefactsDisposalPreCycle' ('WaitingForArtefactsDisposalPreCycle')]'. (状態 &'['Gateway.Service.StateMachine.SnapshotReplication.SnapshotReplicationEngine+WaitingForArtefactsDisposalPreCycle' ('WaitingForArtefactsDisposalPreCycle')]' でタイムアウト イベント 'DisposeArtefactsTimeout' が発生しました。)

**考えられる原因:**

Azure にデータをレプリケートしようとしているコンポーネントがダウンしているか、応答していません。 次の原因が考えられます。

- Azure Migrate アプライアンスで実行されているゲートウェイ サービスがダウンしています。
- ゲートウェイ サービスで、Service Bus、イベント ハブ、アプライアンス ストレージ アカウントに対する接続の問題が発生しています。

**DisposeArtefactsTimedOut の正確な原因と、対応する解決策を特定する:**

1. Azure Migrate アプライアンスが稼働していることを確認してください。
2. アプライアンスでゲートウェイ サービスが実行されているかどうかを確認します。
   1.  リモート デスクトップを使用して Azure Migrate アプライアンスにログインし、次の操作を行います。

   2.  Microsoft サービス MMC スナップインを開き ([ファイル名を指定して実行] > services.msc)、"Microsoft Azure ゲートウェイ サービス" が実行されているかどうかを確認します。 サービスが停止しているか、実行されていない場合は、サービスを開始します。 または、コマンド プロンプトまたは PowerShell を開いて、次を実行できます:"Net Start asrgwy"

3. Azure Migrate アプライアンスとアプライアンス ストレージ アカウントの間の接続に問題がないか確認します。 

    Azure Migrate アプライアンスに azcopy をダウンロードした後、次のコマンドを実行します。
    
    _azcopy bench https://[account].blob.core.windows.net/[container]?SAS_
    
    **パフォーマンス ベンチマーク テストを実行する手順:**
    
      1. azcopy を[ダウンロード](../storage/common/storage-use-azcopy-v10.md)します
        
      2. リソース グループでアプライアンス ストレージ アカウントを探します。 ストレージ アカウントは、migrategwsa\*\*\*\*\*\*\*\*\*\* のような名前です。 これは、上記のコマンドのパラメーター [account] の値です。
        
      3. Azure portal で、お使いのストレージ アカウントを検索します。 検索に使用するサブスクリプションが、ストレージ アカウントが作成されるサブスクリプション (ターゲット サブスクリプション) と同じであることを確認します。 [Blob service] セクションの [コンテナー] にアクセスします。 [+ コンテナー] をクリックし、コンテナーを作成します。 パブリック アクセス レベルを既定の選択された値のままにします。
        
      4. [設定] の [Shared Access Signature] にアクセスします。 [Allowed Resource Type]\(許可されるリソースの種類\) で [コンテナー] を選択します。 [SAS と接続文字列を生成する] をクリックします。 SAS 値をコピーします。
        
      5. アカウント、コンテナー、SAS をそれぞれ手順 2、3、および 4 で取得した値に置き換えて、コマンド プロンプトで上記のコマンドを実行します。
        
      または、Azure Storage Explore をアプライアンスに[ダウンロード](https://go.microsoft.com/fwlink/?linkid=2138967)し、最大 64 MB の 10 BLOB をストレージ アカウントにアップロードします。 問題がなければ、アップロードは正常に完了します。
        
    **解決策:** このテストが失敗した場合は、ネットワークに問題があります。 接続の問題を確認するには、ローカル ネットワーク チームに問い合わせてください。 通常、障害の原因となっているファイアウォールの設定がいくつかあります。
    
4.  Azure Migrate アプライアンスと Service Bus の間の接続に問題がないか確認します。

    このテストでは、Azure Migrate アプライアンスが Azure Migrate クラウド サービス バックエンドと通信できるかどうかを確認します。 アプライアンスは、Service Bus とイベント ハブ メッセージ キューを介してサービス バックエンドと通信します。 アプライアンスから Service Bus への接続を検証するには、Service Bus Explorer を[ダウンロード](https://go.microsoft.com/fwlink/?linkid=2139104)し、Service Bus アプライアンスへの接続を試行し、メッセージの送受信を実行します。 問題がなければ、正常に完了します。

    **テストを実行する手順:**

    1. Migrate プロジェクトで作成された Service Bus から接続文字列をコピーします
    2. Service Bus Explorer を開きます
    3. [ファイル]、[接続] の順に選択します
    4. 接続文字列を貼り付け、[接続] を選択します
    5. Service Bus 名前空間が開きます
    6. トピックの [Snapshot Manager] を選択します。 Snapshot Manager を右クリックし、[メッセージの受信] を選択して、[ピーク] を選択し、[OK] をクリックします
    7. 接続に成功すると、コンソールの出力に "[x] 件のメッセージを受信しました" と表示されます。 接続が成功しなかった場合は、接続に失敗したことを示すメッセージが表示されます
    
    **解決策:** このテストが失敗した場合は、ネットワークに問題があります。 接続の問題を確認するには、ローカル ネットワーク チームに問い合わせてください。 通常、障害の原因となっているファイアウォールの設定がいくつかあります。

5. Azure Migrate アプライアンスと Azure Key Vault 間の接続の問題:

    このテストでは、Azure Migrate アプライアンスと Azure Key Vault 間の接続に問題がないかどうかを確認します。 Key Vault は、レプリケーションに使用されるストレージ アカウントのアクセスを管理するために使用されます。
    
    **接続を確認する手順:**
    
    1. Azure Migrate プロジェクトに対応するリソース グループ内のリソースの一覧から Key Vault URI をフェッチします。
    
    1. Azure Migrate アプライアンスで PowerShell を開き、次のコマンドを実行します。
    
    _test-netconnection Key Vault URI-P 443_
    
    このコマンドを実行すると、TCP 接続が試行され、出力が返されます。
    
     - 出力で、"_TcpTestSucceeded_" フィールドを確認します。 値が "_True_" の場合、Azure Migrate アプライアンスと Azure Key Vault の間に接続の問題はありません。 値が "False" の場合、接続の問題があります。
    
    **解決策:** このテストが失敗した場合、Azure Migrate アプライアンスと Azure Key Vault の間に接続の問題があります。 接続の問題を確認するには、ローカル ネットワーク チームに問い合わせてください。 通常、障害の原因となっているファイアウォールの設定がいくつかあります。
    
## <a name="diskuploadtimedout"></a>DiskUploadTimedOut

**エラー ID:** 1011

**エラー メッセージ:** The upload of data for disk DiskPath, DiskId of virtual machine VMName; VMId did not complete within the expected time. (仮想マシン VMName のディスク DiskPath、DiskId のデータのアップロード。VMId が予想時間内に完了しませんでした。)

このエラーは通常、レプリケーションを実行している Azure Migrate アプライアンスが Azure Cloud Services に接続できないか、レプリケーションの進行が遅いため、レプリケーション サイクルがタイムアウトしたことを示しています。

次の原因が考えられます。

- Azure Migrate アプライアンスがダウンしています。
- アプライアンス上のレプリケーション ゲートウェイ サービスが実行されていません。
- レプリケーション ゲートウェイ サービスで、レプリケーションに使用される次のいずれかの Azure サービス コンポーネントに接続の問題が発生しています:Service Bus、イベント ハブ、Azure キャッシュ ストレージ アカウント、Azure Key Vault。
- ディスクを読み取ろうとしているときに、ゲートウェイ サービスが vCenter レベルでスロットルされています。

**根本原因を特定し、問題を解決する:**

1. Azure Migrate アプライアンスが稼働していることを確認してください。
2. アプライアンスでゲートウェイ サービスが実行されているかどうかを確認します。
   1.  リモート デスクトップを使用して Azure Migrate アプライアンスにログインし、次の操作を行います。

   2.  Microsoft サービス MMC スナップインを開き ([ファイル名を指定して実行] > services.msc)、"Microsoft Azure ゲートウェイ サービス" が実行されているかどうかを確認します。 サービスが停止しているか、実行されていない場合は、サービスを開始します。 または、コマンド プロンプトまたは PowerShell を開いて、次を実行できます:"Net Start asrgwy"。


3. **Azure Migrate アプライアンスとキャッシュ ストレージ アカウントの間の接続に問題がないか確認します。** 

    Azure Migrate アプライアンスに azcopy をダウンロードした後、次のコマンドを実行します。
    
    _azcopy bench https://[account].blob.core.windows.net/[container]?SAS_
    
    **パフォーマンス ベンチマーク テストを実行する手順:**
    
      1. azcopy を[ダウンロード](../storage/common/storage-use-azcopy-v10.md)します
        
      2. リソース グループでアプライアンス ストレージ アカウントを探します。 ストレージ アカウントは、migratelsa\*\*\*\*\*\*\*\*\*\* のような名前です。 これは、上記のコマンドのパラメーター [account] の値です。
        
      3. Azure portal で、お使いのストレージ アカウントを検索します。 検索に使用するサブスクリプションが、ストレージ アカウントが作成されるサブスクリプション (ターゲット サブスクリプション) と同じであることを確認します。 [Blob service] セクションの [コンテナー] にアクセスします。 [+ コンテナー] をクリックし、コンテナーを作成します。 パブリック アクセス レベルを既定の選択された値のままにします。
        
      4. [設定] の [Shared Access Signature] にアクセスします。 [Allowed Resource Type]\(許可されるリソースの種類\) で [コンテナー] を選択します。 [SAS と接続文字列を生成する] をクリックします。 SAS 値をコピーします。
        
      5. アカウント、コンテナー、SAS をそれぞれ手順 2、3、および 4 で取得した値に置き換えて、コマンド プロンプトで上記のコマンドを実行します。
        
      または、Azure Storage Explore をアプライアンスに[ダウンロード](https://go.microsoft.com/fwlink/?linkid=2138967)し、最大 64 MB の 10 BLOB をストレージ アカウントにアップロードします。 問題がなければ、アップロードは正常に完了します。
        
    **解決策:** このテストが失敗した場合は、ネットワークに問題があります。 接続の問題を確認するには、ローカル ネットワーク チームに問い合わせてください。 通常、障害の原因となっているファイアウォールの設定がいくつかあります。
                
4.  **Azure Migrate アプライアンスと Azure Service Bus 間の接続の問題:**

    このテストでは、Azure Migrate アプライアンスが Azure Migrate クラウド サービス バックエンドと通信できるかどうかを確認します。 アプライアンスは、Service Bus とイベント ハブ メッセージ キューを介してサービス バックエンドと通信します。 アプライアンスから Service Bus への接続を検証するには、Service Bus Explorer を[ダウンロード](https://go.microsoft.com/fwlink/?linkid=2139104)し、Service Bus アプライアンスへの接続を試行し、メッセージの送受信を実行します。 問題がなければ、正常に完了します。

    **テストを実行する手順:**
    
    1. Azure Migrate プロジェクトに対応するリソース グループに作成された Service Bus から接続文字列をコピーします
    
    1. Service Bus Explorer を開きます
    
    1. [ファイル] > [接続] にアクセスします
    
    1. 手順 1 でコピーした接続文字列を貼り付け、[接続] をクリックします
    
    1. Service Bus 名前空間が開きます。
    
    1. 名前空間で、トピックの [Snapshot Manager] を選択します。 Snapshot Manager を右クリックし、[メッセージの受信] を選択して、[ピーク] を選択し、[OK] をクリックします。
    
    接続に成功すると、コンソールの出力に "[x] 件のメッセージを受信しました" と表示されます。 接続が成功しなかった場合は、接続に失敗したことを示すメッセージが表示されます。
    
    **解決策:** このテストが失敗した場合、Azure Migrate アプライアンスと Service Bus の間に接続の問題があります。 これらの接続の問題を確認するには、ローカル ネットワーク チームに問い合わせてください。 通常、障害の原因となっているファイアウォールの設定がいくつかあります。
    
 5. **Azure Migrate アプライアンスと Azure Key Vault 間の接続の問題:**

    このテストでは、Azure Migrate アプライアンスと Azure Key Vault 間の接続に問題がないかどうかを確認します。 Key Vault は、レプリケーションに使用されるストレージ アカウントのアクセスを管理するために使用されます。
    
    **接続を確認する手順:**
    
    1. Azure Migrate プロジェクトに対応するリソース グループ内のリソースの一覧から Key Vault URI をフェッチします。
    
    1. Azure Migrate アプライアンスで PowerShell を開き、次のコマンドを実行します。
    
    _test-netconnection Key Vault URI-P 443_
    
    このコマンドを実行すると、TCP 接続が試行され、出力が返されます。
    
    1. 出力で、"_TcpTestSucceeded_" フィールドを確認します。 値が "_True_" の場合、Azure Migrate アプライアンスと Azure Key Vault の間に接続の問題はありません。 値が "False" の場合、接続の問題があります。
    
    **解決策:** このテストが失敗した場合、Azure Migrate アプライアンスと Azure Key Vault の間に接続の問題があります。 接続の問題を確認するには、ローカル ネットワーク チームに問い合わせてください。 通常、障害の原因となっているファイアウォールの設定がいくつかあります。
    
## <a name="encountered-an-error-while-trying-to-fetch-changed-blocks"></a>変更されたブロックを取得しようとしているときにエラーが発生しました

エラー メッセージ:'Encountered an error while trying to fetch change blocks' ('変更ブロックを取得しようとしているときにエラーが発生しました')

エージェントレス レプリケーション方法では、VMware の変更ブロック追跡テクノロジ (CBT) を使って Azure にデータをレプリケートします。 CBT により、サーバー移行ツールでは、最後のレプリケーション サイクル以降に変更されたブロックのみを追跡してレプリケートすることができます。 このエラーは、仮想マシン レプリケート用の変更ブロック追跡がリセットされている場合、または変更ブロック追跡ファイルが壊れている場合に発生します。

このエラーは、次の 2 つの方法で解決できます。

- VM のレプリケーションをトリガーしたときに [はい] を選択して [レプリケーションの自動修復] を選択した場合は、ツールによって修復が試行されます。 VM を右クリックし、[レプリケーションの修復] を選択します。
- [レプリケーションの自動修復] を選択しなかった場合、または上記の手順が機能しなかった場合は、仮想マシンのレプリケーションを停止し、仮想マシン上の[変更されたブロック追跡をリセット](https://go.microsoft.com/fwlink/?linkid=2139203)して、レプリケーションを再構成します。

VMware vSphere 5.5 で仮想マシンの CBT のリセットが発生する可能性のある既知の問題の 1 つについて、「[VMware KB 2048201: Changed Block Tracking](https://go.microsoft.com/fwlink/?linkid=2138888) が vSphere 5.x でのストレージ vMotion 操作の後で変更ブロック追跡がリセットされる」で説明されています。 VMware vSphere 5.5 を使用している場合は、この KB で説明されている更新プログラムを適用してください。

または、VMware PowerCLI を使用して、仮想マシンで VMware の変更ブロック追跡をリセットすることもできます。

## <a name="an-internal-error-occurred"></a>内部エラーが発生しました

場合によっては、VMware 環境または API の問題によってエラーが発生することがあります。 VMware 環境関連のエラーとして、次の一連のエラーを特定しました。 これらのエラーの形式は固定されています。

_エラー メッセージ:An internal error occurred. [Error message] (内部エラーが発生しました。[エラー メッセージ])_

次に例を示します。エラー メッセージ:An internal error occurred. [An Invalid snapshot configuration was detected]. (内部エラーが発生しました。[無効なスナップショット構成が検出されました]。)

次のセクションでは、よく見られる VMware のエラーとその対処法を示します。

### <a name="error-message-an-internal-error-occurred-server-refused-connection"></a>エラー メッセージ:An internal error occurred. [Server Refused Connection] (内部エラーが発生しました。[サーバーが接続を拒否しました])

この問題は、VMware の既知の問題であり、VDDK 6.7 で発生します。 Azure Migrate アプライアンスで実行されているゲートウェイ サービスを停止し、[VMware KB から更新プログラムをダウンロード](https://go.microsoft.com/fwlink/?linkid=2138889)し、ゲートウェイ サービスを再起動する必要があります。

ゲートウェイ サービスを停止する手順:

1. Windows + R キーを押し、services.msc を開きます。 "Microsoft Azure ゲートウェイ サービス" をクリックして停止します。
2. または、コマンド プロンプトまたは PowerShell を開いて、次を実行できます:Net Stop asrgwy。 サービスが実行されなくなったことを示すメッセージが表示されるまで必ず待機してください。

ゲートウェイ サービスを開始する手順:

1. Windows + R キーを押し、services.msc を開きます。 "Microsoft Azure ゲートウェイ サービス" を右クリックして開始します。
2. または、コマンド プロンプトまたは PowerShell を開いて、次を実行できます:Net Start asrgwy。

### <a name="error-message-an-internal-error-occurred-an-invalid-snapshot-configuration-was-detected"></a>エラー メッセージ:An internal error occurred. ['An Invalid snapshot configuration was detected.'] (内部エラーが発生しました。['無効なスナップショット構成が検出されました。'])

複数のディスクを持つ仮想マシンがある場合、仮想マシンからディスクを削除すると、このエラーが発生することがあります。 この問題を修復するには、[こちらの VMware の記事](https://go.microsoft.com/fwlink/?linkid=2138890)の手順を参照してください。

### <a name="error-message-an-internal-error-occurred-generate-snapshot-hung"></a>エラー メッセージ:An internal error occurred. [Generate Snapshot Hung] (内部エラーが発生しました。[スナップショットの生成が応答停止しました])

この問題は、スナップショットの生成が応答停止した場合に発生します。 この問題が発生した場合は、スナップショットの作成タスクが 95% または 99% で停止していることを確認できます。 この問題を解決するには、こちらの [VMware KB](https://go.microsoft.com/fwlink/?linkid=2138969) を参照してください。

### <a name="error-message-an-internal-error-occurred-failed-to-consolidate-the-disks-on-vm-_reasons_"></a>エラー メッセージ:An internal error occurred. [Failed to consolidate the disks on VM _[Reasons]_ ] (内部エラーが発生しました。[VM のディスクを統合できませんでした "[理由]"])

レプリケーション サイクルの最後にディスクを統合するとき、操作が失敗します。 問題を解決するには、該当する "_理由_" を選択して、[VMware KB](https://go.microsoft.com/fwlink/?linkid=2138970) の指示に従ってください。

次のエラーは、VMware スナップショット関連の操作 (ディスクの作成、削除、または統合) が失敗したときに発生します。 次のセクションのガイダンスに従って、エラーを修復してください。

### <a name="error-message-an-internal-error-occurred-another-task-is-already-in-progress"></a>エラー メッセージ:An internal error occurred. [Another task is already in progress] (内部エラーが発生しました。[このタスクは既に実行中です])

この問題は、バックグラウンドで実行されている仮想マシン タスクが競合している場合、または vCenter Server 内のタスクがタイムアウトした場合に発生します。次の [VMware KB](https://go.microsoft.com/fwlink/?linkid=2138891) に示されている解決方法に従ってください。

### <a name="error-message-an-internal-error-occurred-operation-not-allowed-in-current-state"></a>エラー メッセージ:An internal error occurred. [Operation not allowed in current state] (内部エラーが発生しました。[現在の状態では操作は許可されていません])

この問題は、vCenter Server 管理エージェントが動作を停止した場合に発生します。 この問題を解決するには、次の [VMware KB](https://go.microsoft.com/fwlink/?linkid=2138971) の解決策を参照してください。

### <a name="error-message-an-internal-error-occurred-snapshot-disk-size-invalid"></a>エラー メッセージ:An internal error occurred. [Snapshot Disk size invalid] (内部エラーが発生しました。[スナップショット ディスクのサイズが無効です])

これは、スナップショットによって示されたディスク サイズがゼロになる VMware の既知の問題です。 [VMware KB](https://go.microsoft.com/fwlink/?linkid=2138972) に示されている解決方法に従ってください。

### <a name="error-message-an-internal-error-occurred-memory-allocation-failed-out-of-memory"></a>エラー メッセージ:An internal error occurred. [Memory allocation failed. Out of memory.] (内部エラーが発生しました。[メモリの割り当てが失敗しました。 メモリ不足です。])

これは、NFC ホスト バッファーのメモリが不足している場合に発生します。 この問題を解決するには、空きリソースがある別のホストに VM (コンピューティング vMotion) を移動する必要があります。

## <a name="replication-cycle-failed"></a>レプリケーション サイクルが失敗した

**エラー ID:** 181008

**エラー メッセージ:** VM:"VMName"。 エラー:[No disksnapshots were found for the snapshot replication with snapshot Id :'SnapshotID'.]\(スナップショット ID が "SnapshotID" のスナップショット レプリケーションで、ディスク スナップショットが見つかりません。\)

**考えられる原因:**

次のような原因が考えられます。
1. ストレージ vMotion が原因で、含まれている 1 つ以上のディスクのパスが変更されています。
2. 含まれている 1 つ以上のディスクが VM に接続されていません。
      
**推奨事項:**

推奨事項を次に示します
1. ストレージ vMotion を使用して、含まれているディスクを元のパスに復元し、その後ストレージ vMotion を無効にします。
2. ストレージ vMotion が有効になっている場合は無効にし、仮想マシンのレプリケーションを停止し、その後、仮想マシンをもう一度レプリケートします。 問題が解決しない場合は、サポートにお問い合わせください。

## <a name="next-steps"></a>次の手順

VM レプリケーションを続行し、[テスト移行](./tutorial-migrate-vmware.md#run-a-test-migration)を実行します。
