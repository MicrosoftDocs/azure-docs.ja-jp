---
author: jluk
ms.service: cloud-shell
ms.topic: persist-storage
ms.date: 9/7/2018
ms.author: juluk
ms.openlocfilehash: c28441b6fe25b3480a55b79682d5067b19e3023a
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2019
ms.locfileid: "58052225"
---
# <a name="persist-files-in-azure-cloud-shell"></a>Azure Cloud Shell でファイルを永続化する
Cloud Shell では Azure File ストレージを使用してセッション間でファイルを維持します。 Cloud Shell の初回起動時に、セッション間でファイルを維持するために新しいまたは既存のファイル共有を関連付けることを求められます。

> [!NOTE]
> Bash と PowerShell は、同じファイル共有を共有します。 Cloud Shell で自動マウントと関連付けることができるのは、1 つのファイル共有のみです。

## <a name="create-new-storage"></a>新しいストレージの作成

基本設定を使用し、サブスクリプションのみを選択すると、Cloud Shell では、最寄りのサポートされるリージョンに 3 つのリソースが自動的に作成されます。
* リソース グループ: `cloud-shell-storage-<region>`
* ストレージ アカウント: `cs<uniqueGuid>`
* ファイル共有: `cs-<user>-<domain>-com-<uniqueGuid>`

![サブスクリプション設定](../articles/cloud-shell/media/persisting-shell-storage/basic-storage.png)

ファイル共有は `clouddrive` ディレクトリに `$Home` という名前でマウントされます。 これは 1 度限りのアクションであり、ファイル共有は以降のセッションで自動的にマウントされます。 

> [!NOTE]
> セキュリティのために、各ユーザーが自分のストレージ アカウントをプロビジョニングする必要があります。  ロールベースのアクセス制御 (RBAC) では、ユーザーはストレージ アカウント レベルで共同作成者以上のアクセス権を持つ必要があります。

また、5 GB のイメージを含むファイル共有が作成され、これによって `$Home` ディレクトリに自動的にデータが維持されます。 これは、Bash と PowerShell の両方に適用されます。

## <a name="use-existing-resources"></a>既存のリソースの使用

詳細設定オプションを使用すると、既存のリソースを関連付けることができます。 Cloud Shell リージョンを選択する場合、同じリージョンに併置されたバッキング ストレージ アカウントを選択する必要があります。 たとえば、割り当てリージョンが米国西部である場合、同様に米国西部に存在するファイル共有を関連付ける必要があります。

ストレージのセットアップを促す画面が表示されたら、**[詳細設定の表示]** を選択して、追加オプションを表示します。 設定されているストレージ オプションは、ローカル冗長ストレージ (LRS) アカウント、 geo 冗長ストレージ (GRS) アカウント、およびゾーン冗長ストレージ (ZRS) アカウントをフィルター処理します。 

> [!NOTE]
> バッキング ファイル共有の回復性を強化するには、GRS または ZRS のストレージ アカウントの使用をお勧めします。 どちらの種類の冗長性を選ぶかは、実際の目標と価格の希望により異なります。 [詳細については、Azure Storage アカウントのレプリケーションのオプションを参照してください](https://docs.microsoft.com/azure/storage/common/storage-redundancy)。

![リソース グループ設定](../articles/cloud-shell/media/persisting-shell-storage/advanced-storage.png)

### <a name="supported-storage-regions"></a>サポートされているストレージ リージョン
マウント先の Cloud Shell マシンと同じリージョンに、関連付けられた Azure ストレージ アカウントが存在する必要があります。 現在のリージョンを確認するには、Bash で `env` を実行し、変数 `ACC_LOCATION` を見つけます。 ファイル共有には、`$Home` ディレクトリの永続化に使用できる 5 GB のイメージが割り当てられます。

Cloud Shell マシンは、次の各リージョンに存在します。

|領域|リージョン|
|---|---|
|アメリカ合衆国|米国東部、米国中南部、米国西部|
|ヨーロッパ|北ヨーロッパ、西ヨーロッパ|
|アジア太平洋|インド中部、東南アジア|

## <a name="restrict-resource-creation-with-an-azure-resource-policy"></a>Azure リソース ポリシーによるリソース作成の制限
Cloud Shell で作成したストレージ アカウントは `ms-resource-usage:azure-cloud-shell` でタグ付けされます。 Cloud Shell からストレージ アカウントを作成できないようにするには、この固有のタグによってトリガーされる[タグの Azure リソース ポリシー](../articles/azure-policy/json-samples.md)を作成します。
