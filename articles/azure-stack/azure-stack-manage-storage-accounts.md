---
title: "Azure Stack のストレージ アカウントを管理する | Microsoft Docs"
description: "Azure Stack のストレージ アカウントを検索、管理、回復、および回収する方法について説明します。"
services: azure-stack
documentationcenter: 
author: AniAnirudh
manager: darmour
editor: 
ms.assetid: 627d355b-4812-45cb-bc1e-ce62476dab34
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 4/6/2017
ms.author: anirudha
ms.openlocfilehash: 6e14bd6312135b45984a82099e68a934ec2a4a70
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/11/2017
---
# <a name="manage-storage-accounts-in-azure-stack"></a>Azure Stack でのストレージ アカウントの管理
ビジネスのニーズに基づいてストレージ容量を検索、回復、および再利用するために、Azure Stack でストレージ アカウントを管理する方法について説明します。

## <a name="find"></a>ストレージ アカウントの検索
リージョン内のストレージ アカウントのリストは、次の方法を使って Azure Stack で表示できます。

1. インターネット ブラウザーで、https://adminportal.local.azurestack.external に移動します。
2. クラウド オペレーターとして Azure Stack の管理ポータルにサインインします (デプロイ時に指定した資格情報を使用)。
3. 既定のダッシュボードで、**[Region management]\(リージョンの管理\)** リストを見つけ、調べたいリージョンをクリックします。 例: **(local**)。
   
   ![](media/azure-stack-manage-storage-accounts/image1.png)
4. **[リソース プロバイダー]** リストから **[ストレージ]** を選びます。
   
   ![](media/azure-stack-manage-storage-accounts/image2.png)
5. ここで、ストレージ リソース プロバイダーの管理者ブレードで、下にスクロールして、**[ストレージ アカウント]** タブをクリックします。
   
   ![](media/azure-stack-manage-storage-accounts/image3.png)
   
   結果のページでは、そのリージョンのストレージ アカウントのリストが示されます。
   
   ![](media/azure-stack-manage-storage-accounts/image4.png)

既定では、最初の 10 個のアカウントが表示されます。 リストの下部にある **[さらに読み込む]** リンクをクリックすると、さらに多くのアカウントをフェッチすることができます。

または

特定のストレージ アカウントに興味がある場合は、**フィルター処理して関連するアカウントのみをフェッチ**することができます。


**アカウントをフィルター処理するには:**

1. ブレードの上部にある **[フィルター]** をクリックします。
2. [フィルター] ブレードでは、**アカウント名**、**サブスクリプション ID**、または**ステータス**を指定して、表示するストレージ アカウントのリストを細かく調整できます。 必要に応じてこれらを使用します。
3. **[Update]**をクリックします。 リストが更新されます。
   
    ![](media/azure-stack-manage-storage-accounts/image5.png)
4. フィルターをリセットするには、**[フィルター]** をクリックし、選択をオフにして更新します。

(ストレージ アカウントのリスト ブレードの上部の) 検索テキスト ボックスを使用すると、アカウントのリスト内で選択したテキストを強調表示することができます。 これは、フルネームや ID が簡単に使用できない場合にとても便利です。

ここでフリー テキストを使用すると、興味のあるアカウントが見つけやすくなります。

![](media/azure-stack-manage-storage-accounts/image6.png)

## <a name="look-at-account-details"></a>アカウントの詳細の確認
表示で興味のあるアカウントが見つかったら、そのアカウントをクリックして特定の詳細を表示できます。 新しいブレードが開き、アカウントの種類、作成時、場所などのアカウントの詳細が表示されます。

![](media/azure-stack-manage-storage-accounts/image7.png)

## <a name="recover-a-deleted-account"></a>削除されたキーの回復
削除したアカウントを回復する必要がある場合があります。

Azure Stack では、これを行う非常に簡単な方法があります。

1. ストレージ アカウント リストを参照します。 詳細については、このトピックの「[ストレージ アカウントの検索](#find)」を参照してください。
2. リストでそのアカウントを見つけます。 場合によっては、フィルターを使用する必要があります。
3. アカウントの*状態*を確認します。 **[削除済み]** になっているはずです。
4. アカウントをクリックして、アカウントの詳細ブレードを開きます。
5. このブレードの上部にある **[回復]** ボタンをクリックします。
6. **[はい]** をクリックして確定します。
   
   ![](media/azure-stack-manage-storage-accounts/image8.png)
7. 回復が正常に完了したことが示される間、*[process…wait]\(処理中…お待ちください\)* と表示されます。
   ポータルの上部にある “ベル” のアイコンをクリックしても進行状況インジケーターを表示できます。
   
   ![](media/azure-stack-manage-storage-accounts/image9.png)
   
   回復したアカウントが正常に同期されたら、もう一度使用できます。

### <a name="some-gotchas"></a>注意事項
* 削除されたアカウントの状態が、**保有期間外**として表示される。
  
  これは、削除されたアカウントが保有期間を超過しており、回復できない可能性があることを意味します。
* 削除されたアカウントがアカウント リストに表示されない。
  
  これは、削除されたアカウントが既にガベージ コレクトされた可能性があります。 この場合、回復はできません。 このトピックの「[容量の回収](#reclaim)」を参照してください。

## <a name="set-the-retention-period"></a>保有期間の設定
保有期間の設定では、クラウド オペレーターは削除されたアカウントが回復できる可能性がある期間を日数で指定できます (0 から 9999 日の間)。 既定の保有期間は 15 日に設定されています。 値を “0” に設定すると、削除されたすべてのアカウントがすぐに保有期間外になり、定期的なガベージ コレクションの対象としてマークされます。

**保有期間を変更するには:**

1. インターネット ブラウザーで、https://adminportal.local.azurestack.external に移動します。
2. クラウド オペレーターとして Azure Stack の管理ポータルにサインインします (デプロイ時に指定した資格情報を使用)。
3. 既定のダッシュボードで、**[リージョンの管理]** リストを見つけ、調べたいリージョンをクリックします。例: **(local**)。
4. **[リソース プロバイダー]** リストから **[ストレージ]** を選びます。
5. 上部の **[設定]** をクリックして [設定] ブレードを開きます。
6. **[構成]** をクリックして保有期間の値を編集します。

   日数を設定して保存します。
   
   この値はすぐに有効になり、リージョン全体に設定されます。

   ![](media/azure-stack-manage-storage-accounts/image10.png)

## <a name="reclaim"></a>容量の回収
保有期間を設定することの副作用の 1 つは、削除されたアカウントが、保有期間外になるまで、容量を消費し続けることです。 クラウド オペレーターには、削除されたアカウントの保有期間が終了していなくても、その領域を回収する方法が必要な場合があります。

ポータルまたは PowerShell のいずれかを使用して、容量の回収ができます。

**ポータルを使用して容量を回収するには:**
1. [ストレージ アカウント] ブレードに移動します。 「[ストレージ アカウントの検索](#find)」を参照してください。
2. ブレードの上部の **[Reclaim space]\(領域の回収\)** をクリックします。
3. メッセージを確認し、**[OK]** をクリックします。

    ![](media/azure-stack-manage-storage-accounts/image11.png)
4. 成功を通知するベルのアイコンがポータルに表示されるまで待ちます。

    ![](media/azure-stack-manage-storage-accounts/image12.png)
5. [ストレージ アカウント] ページを更新します。 削除されたアカウントは消去されているため、リストに表示されません。

PowerShell を使用して保有期間を明示的に上書きし、すぐに容量を回収することもできます。

**PowerShell を使用して容量を回収するには:**   

1. Azure PowerShell がインストールおよび構成されていることを確認します。 それ以外の場合は、次の手順を実行します。 
   * Azure PowerShell の最新バージョンをインストールして、Azure サブスクリプションに関連付けるには、[Azure PowerShell のインストールおよび構成方法](http://azure.microsoft.com/documentation/articles/powershell-install-configure/)に関するページをご覧ください。
   Azure Resource Manager コマンドレットの詳細については、[Azure Resource Manager での Azure PowerShell の使用](http://go.microsoft.com/fwlink/?LinkId=394767)に関するページを参照してください。
2. 次のコマンドレットを実行します。

> [!NOTE]
> このコマンドレットを実行すると、アカウントとその内容が完全に削除されます。 これは復旧できません。 慎重に使用してください。


        Clear-ACSStorageAccount -ResourceGroupName system.local -FarmName <farm ID>


詳細については、[Azure Stack PowerShell のドキュメント](https://msdn.microsoft.com/library/mt637964.aspx)を参照してください。
 

## <a name="migrate-a-container"></a>コンテナーの移行
テナントが使用するストレージにはばらつきがあるため、クラウド オペレーターは、他よりも多くの領域を使用しているテナントの共有が 1 つまたは複数あることに気付く場合があります。 このような場合、クラウド オペレーターは、一部の BLOB コンテナーを別の共有に手動で移行して、高負荷の共有での領域の解放を試みることができます。 

PowerShell を使用して、コンテナーを移行する必要があります。
> [!NOTE]
>BLOB コンテナーの移行では、ライブ マイグレーションをサポートしておらず、現在はオフライン操作です。 移行中および完了するまで、そのコンテナー内の基になる BLOB は使用できず、"オフライン" です。 

**PowerShell を使用してコンテナーを移行するには:**

1. Azure PowerShell がインストールおよび構成されていることを確認します。 それ以外の場合は、次の手順を実行します。
    * Azure PowerShell の最新バージョンをインストールして、Azure サブスクリプションに関連付けるには、[Azure PowerShell のインストールおよび構成方法](http://azure.microsoft.com/documentation/articles/powershell-install-configure/)に関するページをご覧ください。 Azure Resource Manager コマンドレットの詳細については、[Azure Resource Manager での Azure PowerShell の使用](http://go.microsoft.com/fwlink/?LinkId=394767)に関するページを参照してください。
2. ファームの名前を取得します。 
      
      `$farm = Get-ACSFarm -ResourceGroupName system.local`
3. 共有を取得します。 

   `$shares = Get-ACSShare -ResourceGroupName system.local -FarmName $farm.FarmName`

4. 指定された共有のコンテナーを取得します。 count と intent は省略可能なパラメーターであることに注意してください。
            
   `$containers = Get-ACSContainer -ResourceGroupName system.local -FarmName $farm.FarmName -ShareName $shares[0].ShareName -Count 4 -Intent Migration`  

   $containers を確認します。

   `$containers`

    ![](media/azure-stack-manage-storage-accounts/image13.png)
5. コンテナーの移行先の最適な共有を取得します。

    `$destinationshares= Get-ACSSharesForMigration  -ResourceGroupName system.local -FarmName $farm.farmname -SourceShareName $shares[0].ShareName`

    $destinationshares を確認します。

    `$destinationshares`

    ![](media/azure-stack-manage-storage-accounts/image14.png)
6. コンテナーの移行を開始します。これは非同期の実装であるため、1 つが共有内のすべてのコンテナーをループでき、返されるジョブの ID を使用して状態を追跡できることに注目してください。

    `$jobId = Start-ACSContainerMigration -ResourceGroupName system.local -FarmName $farm.farmname -ContainerToMigrate $containers[1] -DestinationShareUncPath $destinationshares.UncPath`

    $jobId を確認します。

   ```
   $jobId
   d1d5277f-6b8d-4923-9db3-8bb00fa61b65
   ```
7. ジョブ ID で、移行ジョブの状態を確認します。コンテナーの移行が完了すると、MigrationStatus が “Completed” に設定されます。

    `Get-ACSContainerMigrationStatus -ResourceGroupName system.local -FarmName $farm.farmname -JobId $jobId`

    ![](media/azure-stack-manage-storage-accounts/image15.png)

8. 実行中の移行ジョブを取り消すことができます。 これも、非同期操作であるため、$jobid を使用して追跡することができます。

    `Stop-ACSContainerMigration-ResourceGroupName system.local -FarmName $farm.farmname -JobId $jobId-Verbose`

    ![](media/azure-stack-manage-storage-accounts/image16.png)

    移行取り消しの状態をもう一度確認することができます。

    `Get-ACSContainerMigrationStatus-ResourceGroupName system.local -FarmName $farm.farmname -JobId $jobId`

    ![](media/azure-stack-manage-storage-accounts/image17.png)




  
  