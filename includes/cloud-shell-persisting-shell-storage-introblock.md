# <a name="persist-files-in-azure-cloud-shell"></a>Azure Cloud Shell でファイルを永続化する
Cloud Shell では Azure File ストレージを使用してセッション間でファイルを維持します。

## <a name="set-up-a-clouddrive-file-share"></a>Clouddrive ファイル共有を設定する
Cloud Shell の初回起動時に、セッション間でファイルを維持するために新しいまたは既存のファイル共有を関連付けることを求められます。

> [!NOTE]
> Bash と PowerShell は、同じファイル共有を共有します。 Cloud Shell で自動マウントと関連付けることができるのは、1 つのファイル共有のみです。

### <a name="create-new-storage"></a>新しいストレージの作成

基本設定を使用し、サブスクリプションのみを選択すると、Cloud Shell では、最寄りのサポートされるリージョンに 3 つのリソースが自動的に作成されます。
* リソース グループ: `cloud-shell-storage-<region>`
* ストレージ アカウント: `cs<uniqueGuid>`
* ファイル共有: `cs-<user>-<domain>-com-<uniqueGuid>`

![サブスクリプション設定](../articles/cloud-shell/media/persisting-shell-storage/basic-storage.png)

ファイル共有は `clouddrive` ディレクトリに `$Home` という名前でマウントされます。 これは 1 度限りのアクションであり、ファイル共有は以降のセッションで自動的にマウントされます。 

Bash では、同様に 5 GB のイメージを含むファイル共有が作成され、これによって `$Home` ディレクトリに自動的にデータが維持されます。 

### <a name="use-existing-resources"></a>既存のリソースの使用

詳細設定オプションを使用すると、既存のリソースを関連付けることができます。 ストレージのセットアップを促す画面が表示されたら、**[詳細設定の表示]** を選択して、追加オプションを表示します。 ドロップダウン メニューは、割り当てられている Cloud Shell リージョン、ローカル冗長ストレージ アカウント、geo 冗長ストレージ アカウントに合わせて絞り込まれます。

Bash 内の既存のファイル共有には、`$Home` ディレクトリの永続化に使用できる 5 GB のイメージが割り当てられます。

![リソース グループ設定](../articles/cloud-shell/media/persisting-shell-storage/advanced-storage.png)

### <a name="restrict-resource-creation-with-an-azure-resource-policy"></a>Azure リソース ポリシーによるリソース作成の制限
Cloud Shell で作成したストレージ アカウントは `ms-resource-usage:azure-cloud-shell` でタグ付けされます。 Cloud Shell からストレージ アカウントを作成できないようにするには、この固有のタグによってトリガーされる[タグの Azure リソース ポリシー](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-policy-tags)を作成します。

## <a name="supported-storage-regions"></a>サポートされているストレージ リージョン
マウント先の Cloud Shell マシンと同じリージョンに、関連付けられた Azure ストレージ アカウントが存在する必要があります。

割り当てられているリージョンは、次の手順で検索できます。
* [Advanced storage settings]\(ストレージの詳細設定\) ダイアログ ボックスに関する注記を参照します。
* 作成されたストレージ アカウントの名前を参照します (例: `cloud-shell-storage-westus`)。
* `env` を実行し、変数を特定します`ACC_LOCATION`

Cloud Shell マシンは、次のリージョンに存在します。
|領域|リージョン|
|---|---|
|アメリカ|米国東部、米国中南部、米国西部|
|ヨーロッパ|北ヨーロッパ、西ヨーロッパ|
|アジア太平洋|インド中部、東南アジア|

