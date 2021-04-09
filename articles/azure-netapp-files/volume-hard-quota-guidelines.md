---
title: ボリューム ハード クォータへの変更が Azure NetApp Files サービスに対して何を意味するかについて | Microsoft Docs
description: ボリューム ハード クォータ使用への変更、この変更に備えて計画する方法、容量を監視および管理する方法について説明します。
services: azure-netapp-files
documentationcenter: ''
author: b-juche
manager: ''
editor: ''
ms.assetid: ''
ms.service: azure-netapp-files
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 02/05/2021
ms.author: b-juche
ms.openlocfilehash: 12807e83f7841bc67999ce385d0cb82bf15f4c71
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "102175993"
---
# <a name="what-changing-to-volume-hard-quota-means-for-your-azure-netapp-files-service"></a>ボリューム ハード クォータへの変更が Azure NetApp Files サービスに対して何を意味するかについて

Azure NetApp Files には、サービスの開始当初から、容量プールのプロビジョニングと自動拡張のメカニズムが使用されています。 Azure NetApp Files のボリュームは、お客様がプロビジョニングした、選択されたレベルとサイズを持つ基になる容量プールに基づいて仮想プロビジョニングされます。 ボリュームのサイズ (クォータ) がパフォーマンスと容量を提供するために使用され、クォータはいつでも即時に調整できます。 この動作が意味するのは、現時点ではボリューム クォータが、ボリュームに対して帯域幅を制御するために使用されるパフォーマンス操作であるということです。 現時点では、基になる容量プールは、容量が上限いっぱいになると自動的に拡張されます。   

> [!IMPORTANT] 
> Azure NetApp Files のボリュームと容量プールのプロビジョニング動作は、"*手動*" で "*制御可能な*" メカニズムに変わります。 **2021 年 4 月 1 日以降 (更新) は、プロビジョニングされる容量だけでなく帯域幅のパフォーマンスもボリュームのサイズ (クォータ) で管理され、基になる容量プールは自動的に拡張されなくなります。** 

## <a name="reasons-for-the-change-to-volume-hard-quota"></a>ボリューム ハード クォータへの変更の理由

多くのお客様から、"*初期*" の動作に関して、3 つの主な課題が挙がっていました。
* OS スペースまたは容量監視ツールの使用時、VM クライアントには特定のボリュームの仮想プロビジョニングされた (100 TiB) 容量が表示され、クライアントまたはアプリケーション側での容量の把握が不正確になります。
* 容量プールの拡張動作は自動であるため、プロビジョニングされる容量プール領域 (および関連コスト) をアプリケーション所有者は制御できません。 "暴走プロセス" によって急速に容量が上限いっぱいになり、プロビジョニングされる容量とコストが増大する可能性がある環境においては、この状況は厄介です。
* お客様は、ボリューム サイズ (クォータ) とパフォーマンスとの間の直接的な相関関係を確認し、管理することを望んでいます。 ボリュームの (暗黙的な) 過剰サブスクライブ (容量について) およびプールの自動拡張という現在の動作では、ボリューム クォータがアクティブに設定またはリセットされるまで、お客様に直接的な相関関係はわかりません。 

多くのお客様から、プロビジョニングされる容量の直接制御の要求がありました。 ストレージの容量と使用率を制御し、バランスを取ることを望んでいます。 また、アプリケーション ボリュームの使用可能、使用済み、およびプロビジョニングされた容量とパフォーマンスをアプリケーション側およびクライアント側で確認できるだけでなく、コストを制御できることも望んでいます。 

## <a name="what-is-the-volume-hard-quota-change"></a>ボリューム ハード クォータの変更の内容   

ボリューム ハード クォータへの変更に伴い、Azure NetApp Files ボリュームが (最大) 100 TiB で仮想プロビジョニングされることはなくなります。 ボリュームは、実際に構成されたサイズ (クォータ) でプロビジョニングされます。 また、基になる容量プールは、使用量が容量いっぱいになっても自動的に拡張されなくなります。 この変更によって、Azure Managed Disks と同様の動作が反映されます。つまり、自動的な容量の増加を伴わず、そのままプロビジョニングされます。

たとえば、4 TiB の Ultra サービス レベルの容量プールに 1 TiB のサイズ (クォータ) で構成された Azure NetApp Files ボリュームについて考えてみます。 アプリケーションからボリュームにデータが継続的に書き込まれます。

"*初期*" の動作:  
* 予想される帯域幅: 128 MiB/s
* 使用可能な (そしてクライアントで確認できる) 容量の合計: 100 TiB   
    このサイズを超えてデータをボリュームに書き込むことはできません。
* 容量プール: いっぱいになると、自動的に 1 TiB ずつ増加します。
* ボリューム クォータの変更: ボリュームのパフォーマンス (帯域幅) のみを変更します。 クライアントで確認または使用可能な容量は変わりません。

"*変更後*" の動作:  
* 予想される帯域幅: 128 MiB/s
* 使用可能な (そしてクライアントで確認できる) 容量の合計: 1 TiB   このサイズを超えてデータをボリュームに書き込むことはできません。
* 容量プール: サイズは 4 TiB のままで、自動的に拡張されることはありません。 
* ボリューム クォータの変更: ボリュームのパフォーマンス (帯域幅) と、クライアントで確認または使用可能な容量を変更します。

Azure NetApp Files ボリュームと容量プールの使用率について、先を見越した監視を行う必要があります。 使用率が上限に近くなるように、ボリュームとプールの使用率を慎重に変更する必要があります。 Azure NetApp Files を使用して、[ボリュームと容量プールの即時のサイズ変更操作](azure-netapp-files-resize-capacity-pools-or-volumes.md)を引き続き行うことができます。

## <a name="how-to-operationalize-the-volume-hard-quota-change"></a>ボリューム ハード クォータの変更を運用化する方法

このセクションでは、スムーズな移行のためにボリューム ハード クォータへの変更を運用化する方法についてのガイダンスを示します。 また、現在のプロビジョニングされたボリュームと容量プールの扱い、継続的な監視、アラートと容量管理のオプションについての分析情報も提供します。

### <a name="currently-provisioned-volumes-and-capacity-pools"></a>現在のプロビジョニングされたボリュームと容量プール

ボリューム ハード クォータの変更に伴い、運用モデルを変更する必要があります。 プロビジョニングされたボリュームと容量プールには、継続的な容量管理が必要になります。  変更された動作はすぐに発生するため、Azure NetApp Files チームは、このセクションで説明するように、以前にプロビジョニングされた既存のボリュームと容量プールに対して 1 回限りの一連の修正措置をお勧めします。

#### <a name="one-time-corrective-or-preventative-measures-recommendations"></a>1 回限りの修正または予防措置に関する推奨事項  

ボリューム ハード クォータの変更により、以前にプロビジョニングされたボリュームとプールのプロビジョニングされた、および使用可能な容量が変わります。 その結果、容量割り当ての課題が発生する可能性があります。 お客様にとって短期的な領域不足の状況を回避するために、Azure NetApp Files チームは、次に示す 1 回限りの修正と予防の措置をお勧めしています。 

* **プロビジョニングされたボリューム サイズ**:   
    プロビジョニングされたすべてのボリュームのサイズを変更して、変更率とアラートに基づいて適切なバッファーを用意するか、ターンアラウンド時間のサイズを変更します (たとえば、一般的なワークロードの考慮事項に基づいて 20%)。最大値は 100 TiB ([ボリューム サイズの上限](azure-netapp-files-resource-limits.md#resource-limits)) です。 バッファー容量を含むこの新しいボリューム サイズは、次の要素に基づいている必要があります。
    * **プロビジョニングされた** ボリューム容量 (使用済み容量がプロビジョニングされたボリューム クォータより少ない場合)。
    * **使用済み** ボリューム容量 (使用済み容量がプロビジョニングされたボリューム クォータより多い場合)。  
    基になる容量プールのサイズを拡張する必要がない場合、ボリューム レベルの容量の増加に対する追加料金は発生しません。 この変更の影響として、ボリュームの帯域幅の上限の "*引き上げ*" が観察される場合があります ([自動の QoS 容量プールの種類](azure-netapp-files-understand-storage-hierarchy.md#qos_types)が使用されている場合)。

* **プロビジョニングされた容量プールのサイズ**:   
    ボリューム サイズ調整後、ボリューム サイズの合計がホスティング容量プールのサイズよりも大きくなった場合は、容量プールをボリュームの合計と同じかそれより大きいサイズに増やす必要があります。最大値は 500 TiB ([容量プールのサイズ上限](azure-netapp-files-resource-limits.md#resource-limits)) です。 追加の容量プールの容量には、通常どおり ACR 料金が適用されます。

以下のセクションで説明するように、監視またはアラートの設定に関する支援が必要な場合は、Azure NetApp Files のスペシャリストと協力して環境を検証する必要があります。

### <a name="ongoing-capacity-management"></a>継続的な容量管理  

1 回限りの修正措置を実行したら、継続的なプロセスをまとめて、容量を監視および管理する必要があります。 以下のセクションでは、容量の監視と管理に関する推奨事項と代替手段について説明します。

### <a name="monitor-capacity-utilization"></a>容量使用率の監視

さまざまなレベルで容量使用率を監視できます。 

#### <a name="vm-level-monitoring"></a>VM レベルの監視 

最上位レベルの監視 (アプリケーションに最も近い) は、アプリケーション仮想マシン内で行います。 VM クライアント OS 内からの容量レポートで動作の変化を確認します。

次の 2 つのシナリオで、Azure NetApp Files ボリュームが 4 TiB の Ultra サービス レベルの容量プールで 1 TiB のサイズ (クォータ) に構成されているとします。 

##### <a name="windows"></a>Windows

Windows クライアントの場合、ネットワーク マップ ドライブのプロパティを使用して、ボリュームの使用済みおよび使用可能な容量を確認できます。 **[エクスプローラー]**  -> **ドライブ** ->  **[プロパティ]** オプションを使用できます。  

次の例は、動作変更 "*前*" の Windows のボリューム容量レポートを示しています。

![動作変更前のボリュームのストレージ容量の例を示すスクリーンショット。](../media/azure-netapp-files/hard-quota-windows-capacity-before.png)

次に示すように、コマンド プロンプトで `dir` コマンドを使用することもできます。

![動作変更前の、コマンドを使用したボリュームのストレージ容量の表示を示すスクリーンショット。](../media/azure-netapp-files/hard-quota-command-capacity-before.png)

次の例は、動作変更 "*後*" の Windows でのボリューム容量レポートを示しています。

![動作変更後のボリュームのストレージ容量の例を示すスクリーンショット。](../media/azure-netapp-files/hard-quota-windows-capacity-after.png)

`dir` コマンドの出力例を次に示します。  

![動作変更後の、コマンドを使用したボリュームのストレージ容量の表示を示すスクリーンショット。](../media/azure-netapp-files/hard-quota-command-capacity-after.png)

##### <a name="linux"></a>Linux 

Linux クライアントの場合は、[`df` コマンド](https://linux.die.net/man/1/df)を使用して、ボリュームの使用済みおよび使用可能な容量を確認できます。 `-h` オプションを使用すると、サイズ、使用済み領域、使用可能な領域が、ユーザーが判読できる形式で、M、G、T 単位のサイズを使用して表示されます。

次の例は、動作が変更される "*前*" の、Linux でのボリューム容量レポートを示しています。  

![動作変更前の Linux を使用したボリュームのストレージ容量の表示を示すスクリーンショット。](../media/azure-netapp-files/hard-quota-linux-capacity-before.png)

次の例は、動作が変更された "*後*"の、Linux でのボリューム容量レポートを示しています。  

![動作変更後の Linux を使用したボリュームのストレージ容量の表示を示すスクリーンショット。](../media/azure-netapp-files/hard-quota-linux-capacity-after.png)


### <a name="configure-alerts-using-anfcapacitymanager"></a>ANFCapacityManager を使用してアラートを構成する

コミュニティでサポートされている Logic Apps ANFCapacityManager ツールを使用して Azure NetApp Files 容量を監視し、カスタマイズされたアラートを受け取ることができます。 ANFCapacityManager ツールは、[ANFCapacityManager の GitHub ページ](https://github.com/ANFTechTeam/ANFCapacityManager)から入手できます。

ANFCapacityManager は、容量ベースのアラート ルールを管理する Azure ロジック アプリです。 ボリューム サイズを自動的に増やし、Azure NetApp Files ボリュームの領域が不足しないようにします。 デプロイが簡単で、次の Alert Management 機能が提供されます。

* Azure NetApp Files 容量プールまたはボリュームが作成されると、ANFCapacityManager で、指定した使用率のしきい値に基づいてメトリック アラート ルールが作成されます。
* Azure NetApp Files 容量プールまたはボリュームのサイズが変更されると、ANFCapacityManager で、指定した容量使用率のしきい値に基づいてメトリック アラート ルールが変更されます。 アラート ルールが存在しない場合は作成されます。
* Azure NetApp Files 容量プールまたはボリュームが削除されると、対応するメトリック アラート ルールが削除されます。

次の重要なアラート設定を構成できます。  

* **容量プールの使用率しきい値** - 容量プールに対するアラートをトリガーする使用量のしきい値を指定します。 値が 90 の場合は、容量プールが 90% の使用率に達したときにアラートがトリガーされます。
* **ボリュームの使用率しきい値** - ボリュームに対するアラートをトリガーする使用量のしきい値を指定します。 値が 80 の場合は、ボリュームが 80% の使用率に達したときにアラートがトリガーされます。
* **容量通知のための既存アクション グループ** - この設定は、容量ベースのアラートのためにトリガーされるアクション グループです。 この設定は、自分で事前に作成しておく必要があります。 アクション グループを使用すると、電子メール、SMS、またはその他の形式での送信ができます。

次の図は、アラートの構成を示しています。  

![ANFCapacityManager を使用したアラートの構成を示す図。](../media/azure-netapp-files/hard-quota-anfcapacitymanager-configuration.png)

ANFCapacityManager のインストール後は、次の動作を期待できます。Azure NetApp Files 容量プールまたはボリュームが作成、変更、または削除されると、ロジック アプリで、`ANF_Pool_poolname` または `ANF_Volume_poolname_volname` という名前の容量ベースのメトリック アラート ルールが自動的に作成、変更、または削除されます。 

### <a name="manage-capacity"></a>キャパシティの管理

監視とアラートに加えて、Azure NetApp Files の (増加した) 容量消費を管理するために、アプリケーション容量管理プラクティスも組み込む必要があります。 Azure NetApp Files ボリュームまたは容量プールが上限いっぱいになったときに、[アプリケーションを中断することなく、追加容量を即座に提供できます](azure-netapp-files-resize-capacity-pools-or-volumes.md)。 このセクションでは、プロビジョニングされたボリュームと容量プールのスペースを必要に応じて増やすための、手動および自動化されたさまざまな方法について説明します。
 
#### <a name="manual"></a>手動 

ポータルまたは CLI を使用して、ボリュームまたは容量プールのサイズを手動で増やすことができます。 

##### <a name="portal"></a>ポータル 

必要に応じて[ボリュームのサイズを変更](azure-netapp-files-resize-capacity-pools-or-volumes.md#resize-a-volume)できます。 ボリュームの容量消費は、そのプールのプロビジョニング容量を前提としてカウントされます。

1. [Manage NetApp Account]\(NetApp アカウントの管理\) ブレードで、 **[ボリューム]** をクリックします。  
2. サイズを変更するボリュームの名前を右クリックするか、ボリュームの行の最後にある `…` アイコンをクリックして、コンテキスト メニューを表示します。 
3. コンテキスト メニューのオプションを使用して、ボリュームをサイズ変更するか、削除します。   

   ![ボリュームのコンテキスト メニュー オプションを示すスクリーンショット。](../media/azure-netapp-files/hard-quota-volume-options.png) 

   ![[ボリュームのクォータを更新] ウィンドウを示すスクリーンショット。](../media/azure-netapp-files/hard-quota-update-volume-quota.png) 

場合によっては、ホスティング容量プールにボリュームのサイズを変更するための十分な容量がないことがあります。 ただし、[容量プールのサイズ変更](azure-netapp-files-resize-capacity-pools-or-volumes.md#resize-the-capacity-pool)は、1 TiB 単位で増減して行うことができます。 容量プールのサイズは 4 TiB 未満にすることはできません。 *容量プールをサイズ変更すると、購入した Azure NetApp Files の容量が変更されます。*

1. [Manage NetApp Account]\(NetApp アカウントの管理\) ブレードで、サイズ変更する容量プールをクリックします。
2. 容量プールの名前を右クリックするか、容量プールの行の最後にある `…` アイコンをクリックして、コンテキスト メニューを表示します。
3. コンテキスト メニューのオプションを使用して、容量プールをサイズ変更するか、削除します。    

   ![容量プールのコンテキスト メニュー オプションを示すスクリーンショット。](../media/azure-netapp-files/hard-quota-pool-options.png) 

   ![[プールのサイズ変更] ウィンドウを示すスクリーンショット。](../media/azure-netapp-files/hard-quota-update-resize-pool.png) 


##### <a name="cli-or-powershell"></a>CLI または PowerShell

Azure CLI および Azure PowerShell を含む [Azure NetApp Files CLI ツール](azure-netapp-files-sdk-cli.md#cli-tools)を使用して、ボリュームまたは容量プールのサイズを手動で変更できます。  次の 2 つのコマンドを使用して、Azure NetApp Files ボリュームおよびプールのリソースを管理できます。  

* [`az netappfiles pool`](/cli/azure/netappfiles/pool)
* [`az netappfiles volume`](/cli/azure/netappfiles/volume)

Azure CLI を使用して Azure NetApp Files リソースを管理するには、Azure portal を開き、メニュー バーの先頭にある Azure **[Cloud Shell]** リンクを選択します。 

[ ![Cloud Shell リンクにアクセスする方法を示すスクリーンショット。](../media/azure-netapp-files/hard-quota-update-cloud-shell-link.png) ](../media/azure-netapp-files/hard-quota-update-cloud-shell-link.png#lightbox)

この操作により、Azure Cloud Shell が開きます。

[ ![Cloud Shell ウィンドウを示すスクリーンショット。](../media/azure-netapp-files/hard-quota-update-cloud-shell-window.png) ](../media/azure-netapp-files/hard-quota-update-cloud-shell-window.png#lightbox)

次の例では、ボリュームのサイズを[表示](/cli/azure/netappfiles/volume#az-netappfiles-volume-show)および[更新](/cli/azure/netappfiles/volume#az-netappfiles-volume-update)するコマンドを使用しています。
 
[ ![PowerShell を使用したボリューム サイズの表示を示すスクリーンショット。](../media/azure-netapp-files/hard-quota-update-powershell-volume-show.png) ](../media/azure-netapp-files/hard-quota-update-powershell-volume-show.png#lightbox)

[ ![PowerShell を使用したボリューム サイズの更新を示すスクリーンショット。](../media/azure-netapp-files/hard-quota-update-powershell-volume-update.png) ](../media/azure-netapp-files/hard-quota-update-powershell-volume-update.png#lightbox)

次の例では、容量プールのサイズを[表示](/cli/azure/netappfiles/pool#az-netappfiles-pool-show)および[更新](/cli/azure/netappfiles/pool#az-netappfiles-pool-update)するコマンドを使用しています。

[ ![PowerShell を使用した容量プール サイズの表示を示すスクリーンショット。](../media/azure-netapp-files/hard-quota-update-powershell-pool-show.png) ](../media/azure-netapp-files/hard-quota-update-powershell-pool-show.png#lightbox) 

[ ![PowerShell を使用した容量プール サイズの更新を示すスクリーンショット。](../media/azure-netapp-files/hard-quota-update-powershell-pool-update.png) ](../media/azure-netapp-files/hard-quota-update-powershell-pool-update.png#lightbox)

#### <a name="automated"></a>自動  

自動プロセスを構築して、変更後の動作を管理できます。

##### <a name="rest-api"></a>REST API   

Azure NetApp Files サービス用の REST API では、NetApp アカウント、容量プール、ボリューム、スナップショットなどのリソースに対する HTTP 操作が定義されます。 Azure NetApp Files の REST API 仕様は、[Azure NetApp Files Resource Manager の GitHub ページ](https://github.com/Azure/azure-rest-api-specs/tree/master/specification/netapp/resource-manager)に公開されています。 [REST API で使用するコード例](https://github.com/Azure/azure-rest-api-specs/tree/master/specification/netapp/resource-manager/Microsoft.NetApp/stable/2020-06-01/examples)を GitHub で見つけることができます。

「[REST API を使用して Azure NetApp Files 用の開発を行う](azure-netapp-files-develop-with-rest-api.md)」を参照してください。 

##### <a name="rest-api-using-powershell"></a>PowerShell を使用した REST API  

Azure NetApp Files サービス用の REST API では、NetApp アカウント、容量プール、ボリューム、スナップショットなどのリソースに対する HTTP 操作が定義されます。 [Azure NetApp Files の REST API 仕様](https://github.com/Azure/azure-rest-api-specs/tree/master/specification/netapp/resource-manager)は、GitHub に公開されています。

「[PowerShell を使用して REST API による Azure NetApp Files 用の開発を行う](develop-rest-api-powershell.md)」を参照してください。

##### <a name="capacity-management-using-anfcapacitymanager"></a>ANFCapacityManager を使用した容量管理

ANFCapacityManager は、容量ベースのアラート ルールを管理する Azure ロジック アプリです。 ボリューム サイズを自動的に増やし、Azure NetApp Files ボリュームの領域が不足しないようにします。 Azure NetApp Files ボリュームの領域が不足しないように、アラートを送信することに加えて、ボリュームと容量プールのサイズを自動的に増やすことが可能になります。 

* 必要に応じて、Azure NetApp Files ボリュームが指定した使用率のしきい値に達したときに、ボリューム クォータ (サイズ) が、10 から 100 の指定したパーセントだけ増加されます。  
* ボリュームのサイズを増やして、それが含まれる容量プールの容量を超えた場合、新しいボリューム サイズに合わせて容量プールのサイズも増加されます。

次に示す主要な容量管理の設定を構成できます。  

* **自動拡張の割合** - 指定した **使用率のしきい値** に達した場合にボリュームを自動的に拡張するための、既存のボリューム サイズに対する割合。 値を 0 (ゼロ) に設定すると、自動拡張機能が無効になります。 10 から 100 までの値を指定することをお勧めします。

    ![自動拡張の割合を設定するウィンドウを示すスクリーンショット。](../media/azure-netapp-files/hard-quota-volume-anfcapacitymanager-auto-grow-percent.png) 

## <a name="faq"></a>よく寄せられる質問 

このセクションでは、ボリューム ハード クォータの変更に関するいくつかの質問について、答えを示します。 

### <a name="does-snapshot-space-count-towards-the-usable-or-provisioned-capacity-of-a-volume"></a>スナップショット領域は、ボリュームの使用可能またはプロビジョニング済みの容量から減算されますか。

はい。消費されたスナップショット容量は、ボリューム内のプロビジョニングされたスペースから減算されます。 ボリュームがいっぱいになった場合は、次の 2 つの修復オプションを検討してください。

* この記事で説明されているように、ボリュームのサイズを変更します。
* 以前のスナップショットを削除して、ホスティング ボリュームのスペースを解放します。

### <a name="does-this-change-mean-the-volume-auto-grow-behavior-will-disappear-from-azure-netapp-files"></a>この変更によって、ボリュームの自動拡張動作が Azure NetApp Files からなくなるのでしょうか。

よくある誤解は、Azure NetApp Files の "*ボリューム*" が、いっぱいになったときに自動的に拡張されるという考え方です。 ボリュームは、実際に設定されたクォータに関係なく、100 TiB のサイズで仮想プロビジョニングされています。一方、基になる "*容量プール*" は、1 TiB を増分単位として自動的に拡張されます。 この変更により、(確認できる使用可能な) "*ボリューム*" サイズが設定したクォータに対応し、"*容量プール*" は自動的に拡張されなくなります。 この変更によって、一般的に必要とされるクライアント側での正確なスペースと容量のレポートが生成されます。 これにより、"制御されていない" 容量の消費が回避されます。

### <a name="does-this-change-have-any-effect-on-volumes-replicated-with-cross-region-replication-preview"></a>この変更は、リージョン間レプリケーション (プレビュー) を使用してレプリケートされたボリュームに影響しますか。 

ハード ボリューム クォータは、レプリケーション先ボリュームには適用されません。

### <a name="does-this-change-have-any-effect-on-metrics-currently-available-in-azure-monitor"></a>この変更は Azure Monitor で現在利用可能なメトリックに影響しますか。

ポータルのメトリックと Azure Monitor 統計には、割り当てと使用率の新しいモデルが厳密に反映されます。

### <a name="does-this-change-have-any-effect-on-the-resource-limits-for-azure-netapp-files"></a>この変更は、Azure NetApp Files のリソース制限に影響しますか。

Azure NetApp Files のリソース制限には、この記事で説明するクォータの変更以外の変更はありません。

### <a name="is-there-an-example-anfcapacitymanager-workflow"></a>ANFCapacityManager ワークフローの例はありますか。  

はい。 [ボリューム自動拡張ワークフローの例の GitHub ページ](https://github.com/ANFTechTeam/ANFCapacityManager/blob/master/ResizeWorkflow.md)を参照してください。

### <a name="is-anfcapacitymanager-microsoft-supported"></a>ANFCapacityManager は Microsoft によってサポートされていますか。  

[ANFCapacityManager のロジック アプリは、そのままの状態で提供され、NetApp または Microsoft によってサポートされていません](https://github.com/ANFTechTeam/ANFCapacityManager#disclaimer)。 特定の環境や要件に合わせて変更することをお勧めします。 ビジネス クリティカルまたは運用環境へのデプロイ前に、機能をテストしてください。

### <a name="how-can-i-report-a-bug-or-submit-a-feature-request-for-anfcapacitymanger"></a>ANFCapacityManger のバグを報告したり、機能要求を送信したりする方法はありますか。
バグと機能の要求を送信するには、[ANFCapacityManager の GitHub ページ](https://github.com/ANFTechTeam/ANFCapacityManager/issues)で **[New Issue]\(新しいイシュー\)** をクリックします。

## <a name="next-steps"></a>次のステップ
* [容量プールまたはボリュームをサイズ変更する](azure-netapp-files-resize-capacity-pools-or-volumes.md) 
* [Azure NetApp Files のメトリック](azure-netapp-files-metrics.md)