---
title: 管理操作
titleSuffix: Azure SQL Managed Instance
description: Azure SQL Managed Instance 管理操作の所要時間とベスト プラクティスについて説明します。
services: sql-database
ms.service: sql-managed-instance
ms.subservice: operations
ms.custom: sqldbrb=1
ms.devlang: ''
ms.topic: conceptual
author: urosmil
ms.author: urmilano
ms.reviewer: sstein, carlrab, MashaMSFT
ms.date: 07/10/2020
ms.openlocfilehash: 5cff54b1f71d30f7932c4ead722d1dda0a7aec57
ms.sourcegitcommit: 3543d3b4f6c6f496d22ea5f97d8cd2700ac9a481
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/20/2020
ms.locfileid: "86528075"
---
# <a name="overview-of-azure-sql-managed-instance-management-operations"></a>Azure SQL Managed Instance の管理操作の概要
[!INCLUDE[appliesto-sqlmi](../includes/appliesto-sqlmi.md)]

## <a name="what-are-management-operations"></a>管理操作とは
Azure SQL Managed Instance には、新しいマネージド インスタンスを自動的にデプロイしたり、インスタンスのプロパティを更新したり、不要になったインスタンスを削除したりする際に使用できる管理操作が用意されています。

[Azure 仮想ネットワーク内のデプロイ](../../virtual-network/virtual-network-for-azure-services.md)をサポートし、顧客に分離とセキュリティを提供するため、SQL Managed Instance では[仮想クラスター](connectivity-architecture-overview.md#high-level-connectivity-architecture)に依存しています。 仮想クラスターは、お客様の仮想ネットワーク サブネット内にデプロイされている分離された仮想マシンの専用セットを表します。 基本的に、空のサブネットにマネージド インスタンスをデプロイするとその都度、新しい仮想クラスターが構築されます。

それ以降、デプロイ済みのマネージド インスタンスに対して実行された操作は、基盤となる仮想クラスターにも影響する可能性があります。 これらの操作は管理操作の所要時間に影響を及ぼします。新たな仮想マシンのデプロイにはオーバーヘッドが伴うためです。新たなデプロイや既存のマネージド インスタンスに対する更新を計画する際には、このオーバーヘッドを考慮する必要があります。

すべての管理操作は次のように分類できます。

- インスタンスのデプロイ (新しいインスタンスの作成)。
- インスタンスの更新 (仮想コアや予約済み記憶域などの、インスタンスのプロパティの変更)。
- インスタンスの削除。

## <a name="management-operations-duration"></a>管理操作の所要時間
通常、最も時間がかかるのは仮想クラスターに対する操作です。 仮想クラスターに対する操作の所要時間はさまざまです。既存のサービス利用統計情報に基づいて一般的に見込まれる値を以下に示します。

- **仮想クラスターの作成**:作成はインスタンス管理操作で同期的に実行されるステップです。 **操作の 90% は 4 時間以内に完了します**。
- **仮想クラスターのサイズ変更 (拡張または縮小)** :拡張は同期的に実行されるステップです。一方、縮小は非同期的に実行されます (インスタンス管理操作の所要時間には影響しません)。 **クラスター拡張の 90% は 2.5 時間未満で終了します**。
- **仮想クラスターの削除**:削除は非同期で実行されるステップですが、空の仮想クラスターに対して[手動で開始](virtual-cluster-delete.md)することもでき、その場合は同期的に実行されます。 **仮想クラスターの削除の 90% は 1.5 時間以内に完了します**。

加えて、インスタンスの管理には、ホストされたデータベースに対するいずれかの操作が伴うこともあり、その場合、所要時間はさらに長くなります。

- **Azure Storage からのデータベース ファイルのアタッチ**:General Purpose サービス レベルにおけるコンピューティング (仮想コア) やストレージのスケールアップやスケールダウンなどのような同期的に実行されるステップです。 **これらの操作の 90% は 5 分以内に完了します**。
- **Always On 可用性グループのシード処理**:Business Critical サービス レベルにおけるコンピューティング (仮想コア) やストレージのスケーリング、General Purpose から Business Critical (またはその逆) へのサービス レベルの変更などのような同期的に実行されるステップです。 この操作の所要時間は、合計データベース サイズおよび現在のデータベース アクティビティ (アクティブなトランザクションの数) に比例します。 インスタンスを更新しているときのデータベース アクティビティによって、総所要時間は大きく変動する場合があります。 **これらの操作の 90% は毎時 220 GB 以上で実行されます**。

次の表は、操作と標準的な総所要時間をまとめたものです。

|カテゴリ  |操作  |実行時間の長いセグメント  |推定所要時間  |
|---------|---------|---------|---------|
|**デプロイ** |空のサブネットへの最初のインスタンス|仮想クラスターの作成|操作の 90% は 4 時間以内に完了。|
|デプロイ |空ではないサブネットへの別のハードウェア世代の最初のインスタンス (Gen 4 インスタンスを含んだサブネットへの初の Gen 5 インスタンスなど)|仮想クラスターの作成*|操作の 90% は 4 時間以内に完了。|
|デプロイ |空のサブネットまたは空ではないサブネットに 4 仮想コアの最初のインスタンスを作成|仮想クラスターの作成**|操作の 90% は 4 時間以内に完了。|
|デプロイ |空ではないサブネットに後続のインスタンス (第 2、第 3 のインスタンスなど) を作成|仮想クラスターのサイズ変更|操作の 90% は 2.5 時間以内に完了。|
|**アップデート** |インスタンスのプロパティ変更 (管理者パスワード、Azure AD ログイン、Azure ハイブリッド特典フラグ)|該当なし|最大 1 分。|
|更新 |インスタンスのストレージのスケールアップとスケールダウン (General Purpose サービス レベル)|データベース ファイルのアタッチ|操作の 90% は 5 分以内に完了。|
|更新 |インスタンスのストレージのスケールアップとスケールダウン (Business Critical サービス レベル)|- 仮想クラスターのサイズ変更<br>- Always On 可用性グループのシード処理|操作の 90% は 2.5 時間以内に完了。それに加えて、すべてのデータベースにシード処理する時間 (毎時 220 GB)。|
|更新 |インスタンスのコンピューティング (仮想コア) のスケールアップとスケールダウン (General Purpose)|- 仮想クラスターのサイズ変更<br>- データベース ファイルのアタッチ|操作の 90% は 2.5 時間以内に完了。|
|更新 |インスタンスのコンピューティング (仮想コア) のスケールアップとスケールダウン (Business Critical)|- 仮想クラスターのサイズ変更<br>- Always On 可用性グループのシード処理|操作の 90% は 2.5 時間以内に完了。それに加えて、すべてのデータベースにシード処理する時間 (毎時 220 GB)。|
|更新 |インスタンスのサービス レベルの変更 (General Purpose から Business Critical、またはその逆へ)|- 仮想クラスターのサイズ変更<br>- Always On 可用性グループのシード処理|操作の 90% は 2.5 時間以内に完了。それに加えて、すべてのデータベースにシード処理する時間 (毎時 220 GB)。|
|**削除**|インスタンスの削除|すべてのデータベースに対するログ テールのバックアップ|操作の 90% は最長でも 1 分以内に完了。<br>メモ: サブネットの最後のインスタンスが削除された場合、この操作により 12 時間後に仮想クラスターが削除されるようにスケジュールされます。**|
|削除|(ユーザーによって開始された操作としての) 仮想クラスターの削除|仮想クラスターの削除|操作の 90% は最長でも 1.5 時間以内に完了。|

\* 仮想クラスターは、ハードウェアの世代ごとに構築されます。

\*\* 12 時間は現在の構成です。将来は変更される可能性があるので、この時間に強く依存することは避けてください。 もっと早く仮想クラスターを削除する必要がある場合は (サブネットを解放するためなど)、[マネージド インスタンスの削除後のサブネットの削除](virtual-cluster-delete.md)に関するページを参照してください。

## <a name="instance-availability-during-management-operations"></a>管理操作中のインスタンス可用性

SQL Managed Instance は、**更新操作中の利用が可能です**。ただし、更新の最後に実行されるフェールオーバーによって短いダウンタイムが発生します。 [高速データベース復旧](../accelerated-database-recovery.md)のおかげで、実行時間の長いトランザクションが中断された場合でも、通常は最大で 10 秒です。

> [!IMPORTANT]
> 実行時間の長いトランザクション (データのインポート、データ処理ジョブ、インデックスの再構築など) と同時に、Azure SQL Managed Instance のコンピューティングやストレージをスケーリングしたり、サービス レベルを変更したりすることは推奨されません。 操作の最後に実行されるデータベースのフェールオーバーによって、実行中のトランザクションはすべてキャンセルされます。

デプロイおよび削除の操作中は、SQL Managed Instance はクライアント アプリケーションに対して利用不可能になります。

## <a name="management-operations-cross-impact"></a>管理操作の相互影響

マネージド インスタンスでの管理操作は、同じ仮想クラスター内に配置されたインスタンスの他の管理操作に影響を及ぼす場合があります。

- 仮想クラスター内の**実行時間の長い復元操作**により、同じサブネット内での他のインスタンスの作成やスケーリング操作が保留されます。<br/>**例:** 実行時間の長い復元操作があり、同じサブネットに作成またはスケーリングの要求がある場合、この要求は復元操作の終了を待って続行されるため、完了までに通常より長い時間がかかります。
    
- **後続のインスタンスの作成またはスケーリング**の操作は、以前に開始されたインスタンスの作成、または仮想クラスターのサイズ変更を開始したインスタンスのスケールによって保留されます。<br/>**例:** 同じ仮想クラスターの同じサブネット内に複数の作成またはスケーリングの要求がある場合に、そのうちの 1 つで仮想クラスターのサイズ変更が開始されると、仮想クラスターのサイズ変更を要求した操作から 5 分以上経過した後で送信された要求はすべて、予想よりも長く時間がかかります。これらの要求は必ず、サイズ変更の完了を待って再開されるためです。

- **5 分以内に送信された作成/スケーリング操作**は、バッチ処理されて並列で実行されます。<br/>**例:** 5 分以内 (最初の操作要求を実行した時点から測定されます) に送信されたすべての操作に対して、仮想クラスターのサイズ変更は 1 回だけ実行されます。 最初の要求が送信されてから 5 分以上経過した後に別の要求が送信された場合、仮想クラスターのサイズ変更が完了するのを待って、実行が開始されます。

> [!IMPORTANT]
> 進行中の別の操作が原因で保留になっている管理操作は、続行の条件が満たされると、自動的に再開されます。 一時停止された管理操作を再開するために必要なユーザー操作はありません。

## <a name="canceling-management-operations"></a>管理操作のキャンセル

次の表は、特定の管理操作のキャンセルが可能かどうかと、標準的な総所要時間をまとめたものです。

カテゴリ  |操作  |キャンセル可能  |推定キャンセル時間  |
|---------|---------|---------|---------|
|デプロイ |インスタンスの作成 |いいえ |  |
|更新 |インスタンスのストレージのスケールアップとスケールダウン (General Purpose) |いいえ |  |
|更新 |インスタンスのストレージのスケールアップとスケールダウン (Business Critical) |はい |操作の 90% は 5 分以内に完了。 |
|更新 |インスタンスのコンピューティング (仮想コア) のスケールアップとスケールダウン (General Purpose) |はい |操作の 90% は 5 分以内に完了。 |
|更新 |インスタンスのコンピューティング (仮想コア) のスケールアップとスケールダウン (Business Critical) |はい |操作の 90% は 5 分以内に完了。 |
|更新 |インスタンスのサービス レベルの変更 (General Purpose から Business Critical、またはその逆へ) |はい |操作の 90% は 5 分以内に完了。 |
|削除 |インスタンスの削除 |いいえ |  |
|削除 |(ユーザーによって開始された操作としての) 仮想クラスターの削除 |いいえ |  |

# <a name="portal"></a>[ポータル](#tab/azure-portal)

管理操作をキャンセルするには、[概要] ブレードにアクセスし、進行中の操作の通知ボックスをクリックします。 右側から、進行中の操作が表示された画面が出現します。そこには、操作をキャンセルするためのボタンがあります。 最初のクリックの後、もう一度クリックして操作をキャンセルするかどうかを確認するように求められます。

[![操作を取り消す](./media/management-operations-overview/canceling-operation.png)](./media/management-operations-overview/canceling-operation.png#lightbox)

キャンセル要求が送信および処理された後に、キャンセルの送信が成功したかどうかの通知を取得します。

送信したキャンセル要求が成功した場合、管理操作は数分後にキャンセルされ、エラーが発生します。

![キャンセル操作の結果](./media/management-operations-overview/canceling-operation-result.png)

キャンセル要求が失敗するか、[キャンセル] ボタンがアクティブでない場合は、管理操作がキャンセルできない状態に入っており、数分以内に完了することを意味します。 管理操作は、完了するまで実行が継続されます。

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Azure PowerShell がまだインストールされていない場合は、「[Azure PowerShell モジュールのインストール](https://docs.microsoft.com/powershell/azure/install-az-ps)」を参照してください。

管理操作を取り消すには、管理操作名を指定する必要があります。 したがって、最初に get コマンドを使用して操作一覧を取得し、次に特定の操作をキャンセルします。

```powershell-interactive
$managedInstance = "yourInstanceName"
$resourceGroup = "yourResourceGroupName"

$managementOperations = Get-AzSqlInstanceOperation -ManagedInstanceName $managedInstance  -ResourceGroupName $resourceGroup

foreach ($mo in $managementOperations ) {
    if($mo.State -eq "InProgress" -and $mo.IsCancellable){
        $cancelRequest = Stop-AzSqlInstanceOperation -ResourceGroupName $resourceGroup -ManagedInstanceName $managedInstance -Name $mo.Name
        Get-AzSqlInstanceOperation -ManagedInstanceName $managedInstance  -ResourceGroupName $resourceGroup -Name $mo.Name
    }
}
```

コマンドの詳細については、[Get-AzSqlInstanceOperation](https://docs.microsoft.com/powershell/module/az.sql/get-azsqlinstanceoperation) と [Stop-AzSqlInstanceOperation](https://docs.microsoft.com/powershell/module/az.sql/stop-azsqlinstanceoperation) を参照してください。

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Azure CLI をまだインストールしていない場合は、「[Azure CLI のインストール](/cli/azure/install-azure-cli?view=azure-cli-latest)」を参照してください。

管理操作を取り消すには、管理操作名を指定する必要があります。 したがって、最初に get コマンドを使用して操作一覧を取得し、次に特定の操作をキャンセルします。

```azurecli-interactive
az sql mi op list -g yourResourceGroupName --mi yourInstanceName --query "[?state=='InProgress' && isCancellable].{Name: name}" -o tsv |
while read -r operationName; do
    az sql mi op cancel -g yourResourceGroupName --mi yourInstanceName -n $operationName
done
```

コマンドの詳細については、[az sql mi op](https://docs.microsoft.com/cli/azure/sql/mi/op?view=azure-cli-latest) を参照してください。

---

## <a name="next-steps"></a>次のステップ
- 最初のマネージド インスタンスを作成する方法については、[クイック スタート ガイド](instance-create-quickstart.md)を参照してください。
- 機能比較一覧については、[SQL 共通機能](../database/features-comparison.md)に関する記事をご覧ください。
- VNet の構成の詳細については、[SQL Managed Instance VNet の構成](connectivity-architecture-overview.md)に関するページを参照してください。
- マネージド インスタンスを作成し、バックアップ ファイルからデータベースを復元するためのクイック スタートについては、[マネージド インスタンスの作成](instance-create-quickstart.md)に関するページを参照してください。
- Azure Database Migration Service を使用して移行する方法のチュートリアルについては、[Database Migration Service を使用した SQL Managed Instance の移行](../../dms/tutorial-sql-server-to-managed-instance.md)に関するページを参照してください。
