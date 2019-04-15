---
title: Azure Stack のストレージ容量を管理する | Microsoft Docs
description: Azure Stack の Azure Stack ストレージ容量と可用性ストレージ容量を監視および管理します。
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
editor: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: PowerShell
ms.topic: conceptual
ms.date: 03/29/2019
ms.author: mabrigg
ms.reviewer: xiaofmao
ms.lastreviewed: 03/19/2019
ms.openlocfilehash: e5188a7f7a1ce889c8f4340f100cfe767ff2dff8
ms.sourcegitcommit: 956749f17569a55bcafba95aef9abcbb345eb929
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2019
ms.locfileid: "58629392"
---
# <a name="manage-storage-capacity-for-azure-stack"></a>Azure Stack のストレージ容量を管理する 

*適用対象:Azure Stack 統合システムと Azure Stack Development Kit*

この記事の情報は、Azure Stack クラウド オペレーターが、Azure Stack 展開のストレージ容量を監視して管理するために役立ちます。 Azure Stack ストレージ インフラストラクチャは、Azure Stack 展開の合計ストレージ容量のサブセットを、**ストレージ サービス**で使用されるストレージ容量として割り当てます。 ストレージ サービスは、展開のノードに対応するボリューム上の共有にテナント データを格納します。

クラウド オペレーターとして操作できるストレージの量には制限があります。 ストレージの量は、実装するソリューションによって定義されます。 ソリューションは、マルチノード ソリューションを使用する場合は OEM ベンダーによって提供され、Azure Stack Development Kit をインストールする場合はインストール先のハードウェアによって提供されます。

Azure Stack ではストレージ容量の拡張はサポートされないため、使用可能なストレージを[監視](#monitor-shares)して、効率的な操作が維持されることを保証することが重要です。  

共有の残りの空き容量が少なくなった場合は、共有が容量不足になることを防ぐために[領域の管理](#manage-available-space)を計画します。

容量を管理する方法は次のとおりです。
- 容量の回収
- コンテナーの移行

共有が 100% 使用されると、その共有に対するストレージ サービスは機能しなくなります。 共有に対する操作を復元するための支援を得るには、Microsoft サポートに問い合わせてください。

## <a name="understand-volumes-and-shares-containers-and-disks"></a>ボリュームと共有、コンテナー、およびディスクを理解する
### <a name="volumes-and-shares"></a>ボリュームと共有
*ストレージ サービス*は、使用可能なストレージを、テナント データを保持するための複数の同量のボリュームにパーティション分割します。 ボリュームの数は、Azure Stack 展開内のノードの数と同じになります。

- 4 ノード展開では、4 つのボリュームがあります。 各ボリュームには、1 つの共有があります。 マルチノード展開では、ノードが削除されたり正常に機能しなくなったりした場合でも、共有の数が減ることはありません。
- Azure Stack Developer Kit を使用している場合は、1 つの共有を持つ 1 つのボリュームがあります。

ストレージ サービスの共有はストレージ サービスが排他的に使用するため、共有上のファイルを直接変更、追加、または削除しないでください。 これらのボリュームに格納されているファイルは、ストレージ サービスのみが操作する必要があります。

ボリューム上の共有がテナント データを保持します。 テナント データには、ページ BLOB、ブロック BLOB、追加 BLOB、テーブル、キュー、データベース、および関連するメタデータ ストアが含まれます。 ストレージ オブジェクト (BLOB など) は、1 つの共有内に個別に格納されるため、各オブジェクトの最大サイズは、共有のサイズを超えることはできません。 新しいオブジェクトの最大サイズは、新しいオブジェクトの作成時に共有内に未使用の領域として残っている容量によって決まります。

共有の空き領域が少ないときに、領域を[回収](#reclaim-capacity)するためのアクションが成功しないか使用できない場合、Azure Stack クラウド オペレーターは、ある共有から別の共有に BLOB コンテナーを移行できます。

- テナント ユーザーによる Azure Stack 内での BLOB ストレージの操作方法については、「[Azure Stack Storage サービス](/azure/azure-stack/user/azure-stack-storage-overview#azure-stack-storage-services)」を参照してください。


### <a name="containers"></a>Containers
テナント ユーザーは、BLOB データを格納するために使用されるコンテナーを作成します。 BLOB を配置するコンテナーはユーザーが決定しますが、コンテナーを配置するボリュームは、ストレージ サービスがアルゴリズムを使用して決定します。 このアルゴリズムでは、通常は、使用可能な領域が最も多いボリュームが選択されます。  

コンテナーに BLOB が配置された後、その BLOB が増加してより多くの領域を使用する可能性があります。 新しい BLOB を追加するときに既存の BLOB が増加していると、そのコンテナーを保持しているボリュームの空き領域が少なくなります。  

コンテナーは、1 つの共有に限定されません。 コンテナー内の結合された BLOB データが増加して使用可能な領域の 80% 以上を使用すると、コンテナーは*オーバーフロー* モードに入ります。 オーバーフロー モードに入ると、そのコンテナー内に作成される新しい BLOB は、十分な領域がある別のボリュームに割り当てられます。 時間の経過と共に、オーバーフロー モードのコンテナーは、複数のボリュームに分散された BLOB を持つ可能性があります。

ボリュームの空き領域の 80% が使用された時点、および 90% が使用された時点で、システムは Azure Stack 管理者ポータルにアラートを生成します。 クラウド オペレーターは、使用可能なストレージ容量を確認し、コンテンツを再調整することを計画します。 ディスクが 100% 使用されると、ストレージ サービスは動作を停止しますが、この時点で他のアラートが生成されることはありません。

### <a name="disks"></a>ディスク
VM ディスクは、テナントによってコンテナーに追加され、オペレーティング システム ディスクが含まれます。 VM は、1 つ以上のデータ ディスクも保持できます。 どちらの種類のディスクも、ページ BLOB として格納されます。 テナントへのガイダンスは、VM のパフォーマンスを向上させるために各ディスクを別のコンテナーに配置することです。
- VM のディスク (ページ BLOB) を保持する各コンテナーは、ディスクを所有している VM に接続されているコンテナーであるとみなされます。
- VM の任意のディスクを保持していないコンテナーは、空のコンテナーとみなされます。

接続されているコンテナーの領域を解放するためのオプションは[制限されています](#move-vm-disks)。
> [!TIP]  
> クラウド オペレーターは、テナントによってコンテナーに追加された仮想マシン (VM) に接続されているディスクを直接管理しません。 ただし、ストレージの共有の領域に対する管理を計画するときに、ディスクがコンテナーと共有にどのように関連しているかを理解しておくと役に立つ可能性があります。

## <a name="monitor-shares"></a>共有を監視する
PowerShell または管理者ポータルを使用して共有を監視することで、空き領域が少なくなった場合にそれを把握できます。 ポータルを使用しているときは、領域が少なくなっている共有に関するアラートを受信します。    

### <a name="use-powershell"></a>PowerShell の使用
クラウド オペレーターは、PowerShell の **Get AzsStorageShare** コマンドレットを使用して、共有のストレージ容量を監視できます。 Get AzsStorageShare コマンドレットは、各共有の合計領域、割り当て済み領域、および空き領域をバイト単位で返します。   
![例:共有の空き領域を返す](media/azure-stack-manage-storage-shares/free-space.png)

- **合計容量**は、共有で使用できるバイト単位の合計領域です。 この領域は、ストレージ サービスによって保持されるデータとメタデータ用に使用されます。
- **使用済み容量**は、テナント データとそれに関連付けられているメタデータを格納するファイルのすべてのエクステントで使用されるバイト単位のデータの量です。

### <a name="use-the-administrator-portal"></a>管理者ポータルを使用する
クラウド オペレーターは、管理者ポータルを使用して、すべての共有のストレージ容量を確認できます。

1. [管理ポータル](https://adminportal.local.azurestack.external)にサインインします。
2. **[すべてのサービス]** > **[ストレージ]** > **[ファイル共有]** を選択してファイル共有の一覧を開きます。そこで使用状況情報を確認できます。 

    ![例:ストレージ ファイル共有](media/azure-stack-manage-storage-shares/storage-file-shares.png)

   - **[合計]** は、共有で使用できるバイト単位の合計領域です。 この領域は、ストレージ サービスによって保持されるデータとメタデータ用に使用されます。
   - **[使用済み]** は、テナント データとそれに関連付けられているメタデータを格納するファイルのすべてのエクステントで使用されるバイト単位のデータの量です。

### <a name="storage-space-alerts"></a>記憶域スペースのアラート
管理者ポータルを使用しているときは、領域が少なくなっている共有に関するアラートを受信します。

> [!IMPORTANT]
> クラウド オペレーターは、共有が完全に使用されないようにする必要があります。 共有が 100% 使用されると、その共有に対するストレージ サービスは機能しなくなります。 使用率が 100% である共有で空き領域を復旧して操作を復元するには、Microsoft サポートに問い合わせる必要があります。

**警告**:ファイル共有の使用率が 80% を超えると、管理者ポータルで "*警告*" アラートを受信します。![例: 警告アラート](media/azure-stack-manage-storage-shares/alert-warning.png)


**[高]**:ファイル共有の使用率が 90% を超えると、管理者ポータルで "*重大*" アラートを受信します。![例: 重大アラート](media/azure-stack-manage-storage-shares/alert-critical.png)

**詳細の表示**: 管理者ポータルでアラートの詳細を開いて、軽減策を確認できます。![例: アラートの詳細の表示](media/azure-stack-manage-storage-shares/alert-details.png)


## <a name="manage-available-space"></a>使用可能な領域を管理する
共有の領域を開放する必要がある場合は、最も影響が少ない方法を先に使用してください。 たとえば、コンテナーを移行する前に領域の回収を試してください。  

### <a name="reclaim-capacity"></a>容量の回収
"*このオプションは、マルチノード展開と Azure Stack Development Kit の両方に適用されます。*"

削除されているテナント アカウントによって使用されている容量を回収できます。 この容量は、データ[保有期間](azure-stack-manage-storage-accounts.md#set-the-retention-period)に達すると自動的に回収されますが、今すぐ回収する操作を実行できます。

詳細については、ストレージ リソースの管理に関する記事の「[容量の回収](azure-stack-manage-storage-accounts.md#reclaim)」を参照してください。

### <a name="migrate-a-container-between-volumes"></a>ボリューム間でコンテナーを移行する
"*このオプションは、マルチノード展開のみに適用されます。*"

テナントの使用パターンによっては、一部のテナントの共有が他の共有よりも多くの領域を使用することがあります。 その結果、比較的使用されていない共有よりも前に領域が不足する可能性があります。

一部の BLOB コンテナーを別の共有に手動で移行することで、過剰使用されている共有の領域の解放を試行できます。 複数の小さいコンテナーを、それらすべてを保持できる容量がある 1 つの共有に移行できます。 移行を使用して、"*空の*" コンテナーを移動できます。 空のコンテナーとは、VM 用のディスクが存在しないコンテナーです。   

移行すると、すべてのコンテナー BLOB が新しい共有で統合されます。

- コンテナーがオーバーフロー モードに入り、BLOB が別のボリュームに配置されている場合、新しい共有には、移行するコンテナーのすべての BLOB を保持するのに十分な容量が必要です。 これには、別の共有に配置されている BLOB も含まれます。

- PowerShell コマンドレット *Get AzsStorageContainer* は、コンテナーの初期ボリュームで使用されている領域のみを識別します。 このコマンドレットは、別のボリューム上に配置されている BLOB によって使用されている領域は識別しません。 そのため、コンテナーのフル サイズがはっきりしないことがあります。 新しい共有でのコンテナーの統合によって、新しい共有がオーバーフロー状態になり、データが別の共有に配置される可能性があります。 その結果、共有を再び再調整する必要が生じる場合があります。

- リソース グループへのアクセス許可がないために、PowerShell を使用してオーバーフロー データが配置されている別のボリュームをクエリできない場合は、それらのリソース グループとコンテナーの所有者と協力して、移行するデータの合計サイズを把握したうえでデータを移行します。  

> [!IMPORTANT]
> コンテナーの BLOB の移行は、PowerShell の使用を要求するオフライン操作です。 移行が完了するまで、移行するコンテナーのすべての BLOB はオフラインのままであり、使用することはできません。 また、進行中の移行作業がすべて完了するまで、Azure Stack をアップグレードすることは避けてください。

#### <a name="to-migrate-containers-using-powershell"></a>PowerShell を使用してコンテナーを移行するには
1. [Azure PowerShell のインストールと構成](https://azure.microsoft.com/documentation/articles/powershell-install-configure/)が行われていることを確認します。 詳細については、 [リソース マネージャーでの Azure PowerShell の使用](https://go.microsoft.com/fwlink/?LinkId=394767)をご覧ください。
2. コンテナーを調べて、移行する予定の共有にどのようなデータがあるかを把握します。 ボリューム内の移行に最適な候補コンテナーを識別するには、**Get AzsStorageContainer** コマンドレットを使用します。

   ```powershell  
   $farm_name = (Get-AzsStorageFarm)[0].name
   $shares = Get-AzsStorageShare -FarmName $farm_name
   $containers = Get-AzsStorageContainer -ShareName $shares[0].ShareName -FarmName $farm_name
   ```
   $containers を確認します。

   ```powershell
   $containers
   ```

   ![例: $Containers](media/azure-stack-manage-storage-shares/containers.png)

3. 移行するコンテナーを保持できる最善の共有を識別します。

   ```powershell
   $destinationshares = Get-AzsStorageShare -SourceShareName
   $shares[0].ShareName -Intent ContainerMigration
   ```

   $destinationshares を確認します。

   ```powershell 
   $destinationshares
   ```

   ![例: $destination 共有](media/azure-stack-manage-storage-shares/examine-destinationshares.png)

4. コンテナーの移行を開始します。 移行は非同期です。 最初の移行が完了する前に、別のコンテナーの移行を開始する場合は、ジョブ ID を使用してそれぞれの状態を追跡します。

   ```powershell
   $job_id = Start-AzsStorageContainerMigration -StorageAccountName $containers[0].Accountname -ContainerName $containers[0].Containername -ShareName $containers[0].Sharename -DestinationShareUncPath $destinationshares[0].UncPath -FarmName $farm_name
   ```

   $jobId を調べます。 次の例の *d62f8f7a-8b46-4f59-a8aa-5db96db4ebb0* を、調べるジョブ ID に置き換えてください。

   ```powershell
   $jobId
   d62f8f7a-8b46-4f59-a8aa-5db96db4ebb0
   ```

5. ジョブ ID を使用して、移行ジョブの状態を確認します。 コンテナーの移行が完了すると、**MigrationStatus** が **Completed** に設定されます。

   ```powershell 
   Get-AzsStorageContainerMigrationStatus -JobId $job_id -FarmName $farm_name
   ```

   ![例:移行の状態](media/azure-stack-manage-storage-shares/migration-status1.png)

6. 実行中の移行ジョブを取り消すことができます。 取り消された移行ジョブは、非同期的に処理されます。 $Jobid を使用して、取り消しを追跡できます。

   ```powershell
   Stop-AzsStorageContainerMigration -JobId $job_id -FarmName $farm_name
   ```

   ![例:ロールバック状態](media/azure-stack-manage-storage-shares/rollback.png)

7. 移行ジョブの状態が **Canceled**になるまで、手順 6 のコマンドをもう一度実行できます。  

    ![例:取り消し済みの状態](media/azure-stack-manage-storage-shares/cancelled.png)

### <a name="move-vm-disks"></a>VM ディスクを移動する
"*このオプションは、マルチノード展開のみに適用されます。*"

領域を管理する最も極端な方法には、仮想マシンのディスクの移動が伴います。 接続されているコンテナー (VM ディスクを含むコンテナー) の移動は複雑であるため、この操作を実行する場合は Microsoft サポートに問い合わせてください。

## <a name="next-steps"></a>次の手順
[ユーザーに対する仮想マシンの提供](azure-stack-tutorial-tenant-vm.md)についての詳細を確認します。
