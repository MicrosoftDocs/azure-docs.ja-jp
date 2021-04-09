---
title: Avere vFXT for Azure のディザスター リカバリーに関するガイダンス
description: 偶発的な削除や障害から Avere vFXT for Azure のデータを保護する方法
author: ekpgh
ms.service: avere-vfxt
ms.topic: conceptual
ms.date: 12/10/2019
ms.author: rohogue
ms.openlocfilehash: 5cc4678b082aa5a4a3f90518ff8fac448f414f1a
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "92342248"
---
# <a name="disaster-recovery-guidance-for-avere-vfxt-for-azure"></a>Avere vFXT for Azure のディザスター リカバリーに関するガイダンス

この記事では、Avere vFXT for Azure ワークフローを保護するための戦略について概説し、事故や障害から復旧できるようにデータのバックアップに関するガイダンスを提供します。

Avere vFXT for Azure は、一時的にデータをそのキャッシュに格納します。 データは、バックエンド ストレージ システム (オンプレミスのハードウェア システム、Azure Blob Storage コンテナー、またはその両方) に長期間保存されます。

障害やデータ損失の可能性を防ぐために、次の 4 つの分野を検討してください。

* Avere vFXT for Azure システムが使用できなくなった場合のダウンタイムに対する保護
* クラスター キャッシュ内のデータの保護
* バックエンドの NAS ハードウェア ストレージ内のデータの保護
* バックエンドの Azure BLOB クラウド ストレージ内のデータの保護

Avere vFXT for Azure のすべてのお客様は、これらの項目の計画を含む独自の包括的なディザスター リカバリー プランを作成する必要があります。 また、vFXT クラスターで使用するアプリケーションに回復性を組み込むこともできます。 ヘルプについては、「[次の手順](#next-steps)」のリンク先を参照してください。

## <a name="protect-against-downtime"></a>ダウンタイムから保護する

Avere vFXT for Azure 製品には、冗長性が組み込まれています。

* クラスターは可用性が高く、個々のクラスター ノードは中断を最小限に抑えてフェールオーバーできます。
* キャッシュ内で変更されたデータは、長期保存のためにバックエンドのコア ファイラー (ハードウェア NAS または Azure BLOB) に定期的に書き込まれます。

各 Avere vFXT for Azure クラスターは、単一の可用性ゾーンに配置する必要があります。ただし、リージョンの障害が発生した場合に迅速にアクセスを提供するために、別のゾーンまたは別のリージョンにある冗長クラスターを使用できます。

また、データにアクセスできなくなることが懸念される場合は、複数のリージョンにストレージ コンテナーを配置することもできます。 ただし、リージョン間のトランザクションは、1 つのリージョン内に収まるトランザクションより待ち時間が長く、コストが高くなることに注意してください。

## <a name="protect-data-in-the-cluster-cache"></a>クラスター キャッシュ内のデータを保護する

キャッシュ データは常に、通常のシャットダウンの前にコア ファイラーに書き込まれますが、制御されていないシャットダウンでは、キャッシュ内の変更されたデータは失われる可能性があります。

クラスターを使用してファイルの読み取りのみを最適化している場合、失われる変更はありません。 また、クラスターを使用してファイルの変更 (書き込み) をキャッシュする場合は、コア ファイラーの[キャッシュ ポリシー](https://azure.github.io/Avere/legacy/ops_guide/4_7/html/gui_manage_cache_policies.html)を調整して<!-- link to legacy doc --> 長期ストレージにデータを書き込む頻度をカスタマイズするかどうかを検討してください。

一般に、復旧計画では、より多くのデータを保持し、通常、障害発生後にワークフローを再確立するためにより重要であるバックエンド ストレージ システムのバックアップに焦点を当てる必要があります。

## <a name="protect-data-in-nas-core-filers"></a>NAS コア ファイラー内のデータを保護する

容認された方法を使用して、オンプレミスの NAS ハードウェア コア ファイラーに格納されているデータを保護します。これには、NAS プロバイダーが推奨するスナップショットや完全バックアップが含まれます。 これらのコア ファイラーのディザスター リカバリーについては、この記事では取り扱いません。

## <a name="protect-data-in-azure-blob-storage"></a>Azure Blob Storage 内のデータを保護する

Avere vFXT for Azure では、Azure BLOB コア ファイラーにローカル冗長ストレージ (LRS) を使用します。 これは、データセンター内での一時的なハードウェア障害から保護するために、BLOB コンテナー内のデータが自動的にコピーされることを意味します。

このセクションでは、Blob Storage 内のデータを、まれに発生するリージョン全体の障害や誤削除からさらに保護する方法に関するヒントを示します。

Azure Blob Storage 内のデータを保護するためのベスト プラクティスを次に示します。

* 重要なデータを別のリージョンにある別のストレージ アカウントに頻繁に (ディザスター リカバリー プランで決定された頻度で) コピーします。
* 偶発的な削除または破損を防ぐために、すべてのターゲット システム上のデータへのアクセスを制御します。 データ ストレージでの[リソース ロック](../azure-resource-manager/management/lock-resources.md)の使用を検討してください。
* BLOB コア ファイラーに対して Avere vFXT for Azure の[クラウド スナップショット](<https://azure.github.io/Avere/legacy/ops_guide/4_7/html/gui_cloud_snapshot_policies.html>)機能を有効にします。

### <a name="copy-avere-vfxt-core-filer-data-to-a-backup-account"></a>Avere vFXT コア ファイラーのデータをバックアップ アカウントにコピーする

別のアカウントでデータ バックアップを確立するには、次の手順に従います。

1. 必要に応じて、新しい暗号化キーを生成し、影響を受けるシステムの外部に安全に格納します。

   データが Avere vFXT for Azure クラスターによって暗号化される場合は、そのデータを別のストレージ アカウントにコピーする前に、新しい暗号化キーを生成する必要があります。 そのキーとパスワードは、リージョンの障害による影響を受けない安全な施設に安全に保管します。

   このキーは、コンテナーをクラスターに追加するときに (元のクラスターに再追加する場合でも) 指定する必要があります。

   [クラウドの暗号化の設定](<https://azure.github.io/Avere/legacy/ops_guide/4_7/html/gui_cloud_encryption_settings.html>)に関する記事で<!-- link to legacy doc site --> 詳細を確認してください。

   お使いのコンテナーで Azure の組み込みの暗号化のみを使用している場合は、この手順をスキップできます。

1. システムからコア ファイラーを削除します。 これにより、クラスターは、変更されたすべてのデータをバックエンド ストレージに書き込みます。

   バックアップ後にコア ファイラーを再度追加する必要がありますが、これを削除することは、すべてのデータがバックエンドに完全に書き込まれることを保証する最良の方法です ("中断" オプションの場合は、変更されたデータがキャッシュに残ることがあります)。 <!-- xxx true? or just metadata? -->

   バックアップ後にコンテナーを再度追加したときにレプリケートできるように、(コントロール パネルの **[名前空間]** ページに表示されている) コア ファイラーの名前とジャンクション情報を書き留めておきます。

   クラスターのコントロール パネルを使用して、コア ファイラーを削除します。 [クラスターのコントロール パネルを開き](avere-vfxt-cluster-gui.md)、 **[Core filer]\(コア ファイラー\)**  >  **[Manage core filers]\(コア ファイラーの管理\)** の順に選択します。 バックアップするストレージ システムを見つけ、 **[削除]** ボタンを使用してクラスターから削除します。

1. 別のリージョンにある別のストレージ アカウントに新しい空の Blob Storage コンテナーを作成します。

1. 任意の使いやすいコピー ツールを使用して、コア ファイラーのデータを新しいコンテナーにコピーします。 このコピー操作では、変更を加えることなく、また Avere vFXT for Azure で使用される独自のクラウド ファイルシステム形式を損ねることなく、データをレプリケートする必要があります。 Azure ベースのツールには、[AzCopy](../storage/common/storage-use-azcopy-v10.md)、[Azure PowerShell](../data-lake-store/data-lake-store-get-started-powershell.md)、および [Azure Data Factory](../data-factory/connector-azure-data-lake-store.md) があります。

1. データをバックアップ コンテナーにコピーした後、「[ストレージの構成](avere-vfxt-add-storage.md)」の説明に従って、元のコンテナーを再びクラスターに追加します。

   * クライアント ワークフローを変更する必要がないように、同じコア ファイラー名とジャンクション情報を使用します。
   * **[Bucket contents]\(バケットの内容\)** の値を既存のデータ オプションに設定します。
   * コンテナーがクラスターによって暗号化された場合、そのコンテンツの最新の暗号化キーを入力する必要があります (これは、手順 1 で更新したキーです)。

初回後のバックアップでは、新しいストレージ コンテナーを作成する必要はありません。 ただし、自分が覚えている場所に必ず最新のキーが保管されるように、バックアップを実行するたびに新しい暗号化キーを生成することを検討してください。

### <a name="access-a-backup-data-source-during-an-outage"></a>障害発生時にバックアップ データ ソースにアクセスする

Avere vFXT for Azure クラスターからバックアップ コンテナーにアクセスするには、次の手順に従います。

1. 必要に応じて、影響を受けていないリージョンに新しい Avere vFXT for Azure クラスターを作成します。

   > [!TIP]
   > Avere vFXT for Azure クラスターを作成するとき、その作成テンプレートとパラメーターのコピーを保存できます。 プライマリ クラスターの作成時にこの情報を保存すると、これを使用して、同じプロパティを持つ代替クラスターを作成できます。 [[概要]](avere-vfxt-deploy.md#validation-and-purchase) ページで、 **[テンプレートとパラメーターのダウンロード]** リンクをクリックします。 クラスターを作成する前に、情報をファイルに保存します。

1. 複製の BLOB コンテナーを指す新しいクラウド コア ファイラーを追加します。

   コア ファイラー作成ウィザードの **[Bucket contents]\(バケットの内容\)** 設定で、ターゲット コンテナーに既にデータが含まれていることを必ず指定します (この設定を誤って **[Empty]\(空\)** のままにした場合は、システムによりアラートが表示されます)。  <!-- you can't add a populated volume at cluster creation time via template, only create a fresh one -->

1. 必要に応じて、元のものではなく新しいクラスターまたは新しいコア ファイラーをマウントするようにクライアントを更新します (元のコンテナーと同じ名前とジャンクション パスを使用した代替コア ファイラーを追加した場合、新しいクラスターを新しい IP アドレスでマウントする必要がない限り、クライアント プロセスの更新は不要です)。

## <a name="next-steps"></a>次のステップ

* Avere vFXT for Azure の設定のカスタマイズの詳細については、「[クラスター チューニング](avere-vfxt-tuning.md)」を参照してください。
* Azure でのディザスター リカバリーと回復性があるアプリケーションの構築の詳細を参照してください。

  * [Azure の回復性技術ガイダンス](/azure/architecture/framework/resiliency/overview)
  * [リージョン全体でのサービスの中断から回復する](/azure/architecture/resiliency/recovery-loss-azure-region)
  * [Azure アプリケーションのディザスター リカバリーと高可用性](/azure/architecture/framework/resiliency/backup-and-recovery)
  <!-- can't find these in the source tree to use relative links -->