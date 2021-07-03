---
title: 仮想ファイル システムをプールにマウントする
description: 仮想ファイル システムを Batch プールにマウントする方法について説明します。
ms.topic: how-to
ms.custom: devx-track-csharp
ms.date: 03/26/2021
ms.openlocfilehash: dc5fbdf9ca0df8362a8999856c3f7163dd5e59b9
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/30/2021
ms.locfileid: "105626029"
---
# <a name="mount-a-virtual-file-system-on-a-batch-pool"></a>仮想ファイル システムを Batch プールにマウントする

Azure Batch は、Batch プール内の Windows または Linux 計算ノードへのクラウド ストレージまたは外部ファイル システムのマウントをサポートします。 計算ノードがプールに参加すると、仮想ファイル システムがマウントされ、そのノードのローカル ドライブとして扱われます。 Azure Files、Azure Blob Storage、ネットワーク ファイル システム (NFS) ([Avere vFXT キャッシュ](../avere-vfxt/avere-vfxt-overview.md)を含む)、Common Internet File System (CIFS) などのファイル システムをマウントできます。

この記事では、[Batch Management Library for .NET](/dotnet/api/overview/azure/batch) を使用して、計算ノードのプールに仮想ファイル システムをマウントする方法について説明します。

> [!NOTE]
> 仮想ファイル システムのマウントは、2019 年 8 月 8 日以降に作成された Batch プールでのみサポートされています。 この日付より前に作成された Batch プールでは、この機能はサポートされません。

## <a name="benefits-of-mounting-on-a-pool"></a>プールにマウントする利点

ファイル システムをプールにマウントすると、タスクが大規模なデータ セットからタスクのデータを取得するのではなく、タスクが必要なデータに簡単かつ効率的にアクセスできるようになります。

ムービーのレンダリングなど、一般的なデータ セットへのアクセスを必要とするタスクが複数あるシナリオについて考えてみます。 各タスクは、シーン ファイルから一度に 1 つ以上のフレームをレンダリングします。 シーン ファイルが格納されているドライブをマウントすることで、計算ノードは共有データに簡単にアクセスできるようになります。

さらに、基になるファイル システムは、データに同時にアクセスする計算ノードに必要なパフォーマンスとスケール (スループットと IOPS) に基づいて、個別に選択し、スケーリングすることができます。 たとえば、[Avere vFXT](../avere-vfxt/avere-vfxt-overview.md) の分散型メモリ内キャッシュを使用すると、オンプレミスに存在するソース データにアクセスして、数千単位の同時レンダリング ノードを使用する大規模な映画規模のレンダリングをサポートできます。 また、クラウドベースの BLOB ストレージに既に存在するデータの場合、[blobfuse](../storage/blobs/storage-how-to-mount-container-linux.md) を使用して、このデータをローカル ファイル システムとしてマウントすることもできます。 blobfuse は Linux ノード上でのみ使用できますが、[Azure Files](../storage/files/storage-files-introduction.md) に用意されている同様のワークフローは、Windows と Linux の両方で使用できます。

## <a name="mount-a-virtual-file-system-on-a-pool"></a>仮想ファイル システムをプールにマウントする  

プールに仮想ファイル システムをマウントすると、プール内のすべての計算ノードでファイル システムを使用できるようになります。 ファイル システムは、計算ノードがプールに参加するとき、またはノードが再起動または再イメージ化されるときに構成されます。

プールにファイル システムをマウントするには、`MountConfiguration` オブジェクトを作成します。 仮想ファイル システムに適したオブジェクト (`AzureBlobFileSystemConfiguration`、`AzureFileShareConfiguration`、`NfsMountConfiguration`、または `CifsMountConfiguration`) を選択します。

すべてのマウント構成オブジェクトには、次の基本パラメーターが必要です。 一部のマウント構成には、使用するファイル システムに固有のパラメーターがあります。詳細については、コード例を参照してください。

- **アカウント名またはソース**:仮想ファイル共有をマウントするには、ストレージ アカウントまたはそのソースの名前が必要です。
- **相対マウント パスまたはソース**:計算ノードにマウントされるファイル システムの場所。`AZ_BATCH_NODE_MOUNTS_DIR` を介してノード上でアクセスできる標準の `fsmounts` ディレクトリの相対的な場所です。 正確な場所は、ノード上の使用されているオペレーティング システムによって変わります。 たとえば、Ubuntu ノード上の物理的な場所は `mnt\batch\tasks\fsmounts` にマップされ、CentOS ノード上の場合は `mnt\resources\batch\tasks\fsmounts` にマップされます。
- **マウント オプションまたは blobfuse オプション**:これらのオプションは、ファイル システムをマウントするための特定のパラメーターを示します。

`MountConfiguration` オブジェクトが作成されたら、プールを作成するときにそのオブジェクトを `MountConfigurationList` プロパティに割り当てます。 ファイル システムは、ノードがプールに参加するとき、またはノードが再起動または再イメージ化されるときにマウントされます。

ファイル システムがマウントされると、マウントされたファイル システムとログ ファイル (トラブルシューティングとデバッグに役立ちます) の場所を指す環境変数 `AZ_BATCH_NODE_MOUNTS_DIR` が作成されます。 ログ ファイルの詳細については、「[マウント エラーの診断](#diagnose-mount-errors)」セクションを参照してください。  

> [!IMPORTANT]
> プールにマウントされるファイル システムの最大数は 10 です。 詳細とその他の制限については、「[Batch サービスのクォータと制限](batch-quota-limit.md#other-limits)」を参照してください。

## <a name="examples"></a>例

次のコード例は、さまざまなファイル共有を計算ノードのプールにマウントする方法を示しています。

### <a name="azure-files-share"></a>Azure Files 共有

Azure Files は、標準の Azure クラウド ファイル システム オファリングです。 マウント構成コード サンプルのパラメーターを取得する方法の詳細については、[Azure Files 共有の使用](../storage/files/storage-how-to-use-files-windows.md)に関するページを参照してください。

```csharp
new PoolAddParameter
{
    Id = poolId,
    MountConfiguration = new[]
    {
        new MountConfiguration
        {
            AzureFileShareConfiguration = new AzureFileShareConfiguration
            {
                AccountName = "{storage-account-name}",
                AzureFileUrl = "https://{storage-account-name}.file.core.windows.net/{file-share-name}",
                AccountKey = "{storage-account-key}",
                RelativeMountPath = "S",
                MountOptions = "-o vers=3.0,dir_mode=0777,file_mode=0777,sec=ntlmssp"
            },
        }
    }
}
```

### <a name="azure-blob-file-system"></a>Azure Blob ファイル システム

もう 1 つの選択肢は、[blobfuse](../storage/blobs/storage-how-to-mount-container-linux.md) を介して Azure Blob Storage を使用することです。 BLOB ファイル システムをマウントするには、ストレージ アカウントに `AccountKey` または `SasKey` が必要です。 これらのキーの取得については、「[ストレージ アカウント アクセス キーを管理する](../storage/common/storage-account-keys-manage.md)」または「[Shared Access Signatures (SAS) を使用して Azure Storage リソースへの制限付きアクセスを許可する](../storage/common/storage-sas-overview.md)」を参照してください。 blobfuse の使用に関する詳細とヒントについては、「blobfuse」を参照してください。

blobfuse でマウントされたディレクトリへの既定のアクセスを取得するには、**Administrator** としてタスクを実行します。 blobfuse によってディレクトリはユーザー空間にマウントされ、プールの作成時にルートとしてマウントされます。 Linux では、すべての **Administrator** タスクがルートになります。 FUSE モジュールのすべてのオプションについては、[FUSE のリファレンス ページ](https://manpages.ubuntu.com/manpages/xenial/man8/mount.fuse.8.html)で説明されています。

[トラブルシューティングの FAQ](https://github.com/Azure/azure-storage-fuse/wiki/3.-Troubleshoot-FAQ)に関する記事で、blobfuse の使用に関する詳細とヒントについて確認してください。 また、[blobfuse リポジトリの GitHub の問題](https://github.com/Azure/azure-storage-fuse/issues)に関する記事を参照して、現在の blobfuse の問題と解決策を確認することもできます。

```csharp
new PoolAddParameter
{
    Id = poolId,
    MountConfiguration = new[]
    {
        new MountConfiguration
        {
            AzureBlobFileSystemConfiguration = new AzureBlobFileSystemConfiguration
            {
                AccountName = "StorageAccountName",
                ContainerName = "containerName",
                AccountKey = "StorageAccountKey",
                SasKey = "",
                RelativeMountPath = "RelativeMountPath",
                BlobfuseOptions = "-o attr_timeout=240 -o entry_timeout=240 -o negative_timeout=120 "
            },
        }
    }
}
```

### <a name="network-file-system"></a>ネットワーク ファイル システム

ネットワーク ファイル システム (NFS) をプール ノードにマウントして、Azure Batch が従来のファイル システムにアクセスできるようにすることができます。 これは、クラウドにデプロイされる単一の NFS サーバー、または仮想ネットワーク経由でアクセスされるオンプレミスの NFS サーバーの場合があります。 または、[Avere vFXT](../avere-vfxt/avere-vfxt-overview.md) 分散型インメモリ キャッシュ ソリューションをデータ集中型ハイ パフォーマンス コンピューティング (HPC) タスクで使用することもできます

```csharp
new PoolAddParameter
{
    Id = poolId,
    MountConfiguration = new[]
    {
        new MountConfiguration
        {
            NfsMountConfiguration = new NFSMountConfiguration
            {
                Source = "source",
                RelativeMountPath = "RelativeMountPath",
                MountOptions = "options ver=1.0"
            },
        }
    }
}
```

### <a name="common-internet-file-system"></a>共通インターネット ファイル システム

プール ノードへの[共通インターネット ファイル システム (CIFS)](/windows/desktop/fileio/microsoft-smb-protocol-and-cifs-protocol-overview) のマウントは、従来のファイル システムへのアクセスを提供するもう 1 つの方法です。 CIFS は、ネットワーク サーバーのファイルとサービスを要求するためのオープンなクロスプラットフォーム メカニズムを提供するファイル共有プロトコルです。 CIFS は、インターネットおよびイントラネット ファイル共有のための[サーバー メッセージ ブロック (SMB)](/windows-server/storage/file-server/file-server-smb-overview) プロトコルの拡張バージョンに基づいており、Windows ノードに外部ファイル システムをマウントするために使用できます。

```csharp
new PoolAddParameter
{
    Id = poolId,
    MountConfiguration = new[]
    {
        new MountConfiguration
        {
            CifsMountConfiguration = new CIFSMountConfiguration
            {
                Username = "StorageAccountName",
                RelativeMountPath = "cifsmountpoint",
                Source = "source",
                Password = "StorageAccountKey",
                MountOptions = "-o vers=3.0,dir_mode=0777,file_mode=0777,serverino"
            },
        }
    }
}
```

## <a name="diagnose-mount-errors"></a>マウント エラーの診断

マウント構成が失敗した場合、プール内の計算ノードは失敗し、ノードの状態は `unusable` に設定されます。 マウント構成エラーを診断するには、[`ComputeNodeError`](/rest/api/batchservice/computenode/get#computenodeerror) プロパティでエラーの詳細を調べます。

デバッグのためにログ ファイルを取得するには、[OutputFiles](batch-task-output-files.md) を使用して `*.log` ファイルをアップロードします。 `*.log` ファイルには、`AZ_BATCH_NODE_MOUNTS_DIR` の場所にあるファイル システムのマウントに関する情報が含まれています。 マウント ログ ファイルの形式は、マウントごとに `<type>-<mountDirOrDrive>.log` となります。 たとえば、`test` というマウント ディレクトリの `cifs` マウントでは、`cifs-test.log` という名前のマウント ログ ファイルが作成されます。

## <a name="supported-skus"></a>サポートされている SKU

| Publisher | プラン | SKU | Azure Files 共有 | blobfuse | NFS マウント | CIFS マウント |
|---|---|---|---|---|---|---|
| batch (バッチ) | rendering-centos73 | rendering | :heavy_check_mark: <br>注:CentOS 7.7 との互換性</br>| :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| Canonical | UbuntuServer | 16.04-LTS、18.04-LTS | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| Credativ | Debian | 8| :heavy_check_mark: | :x: | :heavy_check_mark: | :heavy_check_mark: |
| Credativ | Debian | 9 | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| microsoft-ads | linux-data-science-vm | linuxdsvm | :heavy_check_mark: <br>注:CentOS 7.4 と互換性があります。 </br> | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| microsoft-azure-batch | centos-container | 7.6 | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| microsoft-azure-batch | centos-container-rdma | 7.4 | :heavy_check_mark: <br>注:A_8 または 9 ストレージをサポートします</br> | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| microsoft-azure-batch | ubuntu-server-container | 16.04 LTS | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| microsoft-dsvm | linux-data-science-vm-ubuntu | linuxdsvmubuntu | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| OpenLogic | CentOS | 7.6 | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| OpenLogic | CentOS-HPC | 7.4、7.3、7.1 | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| Oracle | Oracle-Linux | 7.6 | :x: | :x: | :x: | :x: |
| Windows | WindowsServer | 2012、2016、2019 | :heavy_check_mark: | :x: | :x: | :x: |

## <a name="networking-requirements"></a>ネットワーク要件

[仮想ネットワーク内の Azure Batch プール](batch-virtual-network.md)で仮想ファイル マウントを使用するときは、次の要件を考慮して、必要なトラフィックがブロックされないようにしてください。

- **Azure ファイル**:
  - "storage" サービス タグとの間のトラフィックに TCP ポート 445 を開く必要があります。 詳細については、[Windows での Azure ファイル共有の使用](../storage/files/storage-how-to-use-files-windows.md#prerequisites)に関する記事を参照してください。
- **blobfuse**:
  - "storage" サービス タグとの間のトラフィックに TCP ポート 443 を開く必要があります。
  - blobfuse および gpg パッケージをダウンロードするために、VM が https://packages.microsoft.com にアクセスできる必要があります。 構成によっては、追加のパッケージをダウンロードするために他の URL へのアクセスも必要な場合があります。
- **Network File System (NFS)** :
  - ポート 2049 (既定値。構成によって他の要件になる場合もあります) へのアクセスが必要です。
  - NFS 共通 (Debian または Ubuntu 用)、または NFS ユーティリティ (CentOS 用) のパッケージをダウンロードするには、VM が適切なパッケージ マネージャーにアクセスできる必要があります。 この URL は、使用している OS バージョンによって異なる場合があります。 構成によっては、追加のパッケージをダウンロードするために他の URL へのアクセスも必要な場合があります。
- **共通インターネット ファイル システム (CIFS)** :
  - TCP ポート 445 へのアクセスが必要です。
  - CIFS ユーティリティ パッケージをダウンロードするには、VM が適切なパッケージ マネージャーにアクセスできる必要があります。 この URL は、使用している OS バージョンによって異なる場合があります。

## <a name="next-steps"></a>次のステップ

- [Windows](../storage/files/storage-how-to-use-files-windows.md) または [Linux](../storage/files/storage-how-to-use-files-linux.md) で Azure Files 共有をマウントする方法の詳細について説明します。
- [blobfuse](https://github.com/Azure/azure-storage-fuse) 仮想ファイル システムの使用とマウントについて説明します。
- NFS とそのアプリケーションの詳細については、「[ネットワーク ファイル システムの概要](/windows-server/storage/nfs/nfs-overview)」を参照してください。
- CIFS の詳細については、「[Microsoft SMB プロトコルと CIFS プロトコルの概要](/windows/desktop/fileio/microsoft-smb-protocol-and-cifs-protocol-overview)」を参照してください。
