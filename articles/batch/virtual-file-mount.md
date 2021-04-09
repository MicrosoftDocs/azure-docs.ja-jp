---
title: 仮想ファイル システムをプールにマウントする
description: 仮想ファイル システムを Batch プールにマウントする方法について説明します。
ms.topic: how-to
ms.custom: devx-track-csharp
ms.date: 08/13/2019
ms.openlocfilehash: df03275fdeea88df1a2f2b6e2cda55021497cdf7
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "89145486"
---
# <a name="mount-a-virtual-file-system-on-a-batch-pool"></a>仮想ファイル システムを Batch プールにマウントする

Azure Batch は、Batch プール内の Windows または Linux 計算ノードへのクラウド ストレージまたは外部ファイル システムのマウントをサポートするようになりました。 計算ノードがプールに参加すると、仮想ファイル システムがマウントされ、そのノードのローカル ドライブとして扱われます。 Azure Files、Azure Blob Storage、ネットワーク ファイル システム (NFS) ([Avere vFXT キャッシュ](../avere-vfxt/avere-vfxt-overview.md)を含む)、Common Internet File System (CIFS) などのファイル システムをマウントできます。

この記事では、[Batch Management Library for .NET](/dotnet/api/overview/azure/batch) を使用して、計算ノードのプールに仮想ファイル システムをマウントする方法について説明します。

> [!NOTE]
> 仮想ファイル システムのマウントは、2019-08-19 以降に作成された Batch プールでサポートされています。 2019-08-19 より前に作成された Batch プールでは、この機能はサポートされていません。
> 
> 計算ノードにファイル システムをマウントする API は、[Batch .NET](/dotnet/api/microsoft.azure.batch) ライブラリに含まれています。

## <a name="benefits-of-mounting-on-a-pool"></a>プールにマウントする利点

ファイル システムをプールにマウントすると、タスクが大規模なデータ セットからタスクのデータを取得するのではなく、タスクが必要なデータに簡単かつ効率的にアクセスできるようになります。

ムービーのレンダリングなど、一般的なデータ セットへのアクセスを必要とするタスクが複数あるシナリオについて考えてみます。 各タスクは、シーン ファイルから一度に 1 つ以上のフレームをレンダリングします。 シーン ファイルが格納されているドライブをマウントすることで、計算ノードは共有データに簡単にアクセスできるようになります。 さらに、基になるファイル システムは、データに同時にアクセスする計算ノードに必要なパフォーマンスとスケール (スループットと IOPS) に基づいて、個別に選択し、スケーリングすることができます。 たとえば、[Avere vFXT](../avere-vfxt/avere-vfxt-overview.md) の分散型メモリ内キャッシュを使用すると、オンプレミスに存在するソース データにアクセスして、数千単位の同時レンダリング ノードを使用する大規模な映画規模のレンダリングをサポートできます。 また、クラウドベースの BLOB ストレージに既に存在するデータの場合、[blobfuse](../storage/blobs/storage-how-to-mount-container-linux.md) を使用して、このデータをローカル ファイル システムとしてマウントすることもできます。 blobfuse は Linux ノード上でのみ使用できますが、[Azure Files](https://azure.microsoft.com/blog/a-new-era-for-azure-files-bigger-faster-better/) には同様のワークフローが用意されており、Windows と Linux の両方で使用できます。

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

もう 1 つの選択肢は、[blobfuse](../storage/blobs/storage-how-to-mount-container-linux.md) を介して Azure Blob ストレージを使用することです。 BLOB ファイル システムをマウントするには、ストレージ アカウントに `AccountKey` または `SasKey` が必要です。 これらのキーを取得する方法の詳細については、[ストレージ アカウント アクセス キーの管理](../storage/common/storage-account-keys-manage.md)または[共有アクセス署名 (SAS) の使用](../storage/common/storage-sas-overview.md)に関するページを参照してください。 blobfuse の使用方法の詳細については、[blobfuse のトラブルシューティングの FAQ](https://github.com/Azure/azure-storage-fuse/wiki/3.-Troubleshoot-FAQ) ページを参照してください。 blobfuse でマウントされたディレクトリへの既定のアクセスを取得するには、**Administrator** としてタスクを実行します。 blobfuse によってディレクトリはユーザー空間にマウントされ、プールの作成時にルートとしてマウントされます。 Linux では、すべての **Administrator** タスクがルートになります。 FUSE モジュールのすべてのオプションについては、[FUSE のリファレンス ページ](https://manpages.ubuntu.com/manpages/xenial/man8/mount.fuse.8.html)で説明されています。

現在の blobfuse の問題と解決策を確認するには、トラブルシューティング ガイドだけでなく、GitHub の blobfuse リポジトリの問題も役立ちます。 詳細については、[blobfuse の問題](https://github.com/Azure/azure-storage-fuse/issues)のページを参照してください。

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

ネットワーク ファイル システム (NFS) をプール ノードにマウントして、Azure Batch ノードが従来のファイル システムに簡単にアクセスできるようにすることもできます。 これは、クラウドにデプロイされる単一の NFS サーバー、または仮想ネットワーク経由でアクセスされるオンプレミスの NFS サーバーの場合があります。 または、[Avere vFXT](../avere-vfxt/avere-vfxt-overview.md) 分散型メモリ内キャッシュ ソリューションを活用してオンプレミス ストレージへのシームレスな接続を提供し、オンデマンドでデータをキャッシュに読み込み、クラウドベースの計算ノードに対してハイ パフォーマンスとスケールを実現します。

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

共通ネットワーク ファイル システム (CIFS) をプール ノードにマウントして、Azure Batch ノードが従来のファイル システムに簡単にアクセスできるようにすることもできます。 CIFS は、ネットワーク サーバーのファイルとサービスを要求するためのオープンなクロスプラットフォーム メカニズムを提供するファイル共有プロトコルです。 CIFS は、インターネットおよびイントラネット ファイル共有のための Microsoft のサーバー メッセージ ブロック (SMB) プロトコルの拡張バージョンに基づいており、Windows ノードに外部ファイル システムをマウントするために使用されます。 SMB の詳細については、[ファイル サーバーと SMB](/windows-server/storage/file-server/file-server-smb-overview) に関するページを参照してください。

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

マウント構成が失敗した場合、プール内の計算ノードは失敗し、ノードの状態は使用不可になります。 マウント構成エラーを診断するには、[`ComputeNodeError`](/rest/api/batchservice/computenode/get#computenodeerror) プロパティでエラーの詳細を調べます。

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

## <a name="next-steps"></a>次のステップ

- [Windows](../storage/files/storage-how-to-use-files-windows.md) または [Linux](../storage/files/storage-how-to-use-files-linux.md) で Azure Files 共有をマウントする方法の詳細について説明します。
- [blobfuse](https://github.com/Azure/azure-storage-fuse) 仮想ファイル システムの使用とマウントについて説明します。
- NFS とそのアプリケーションの詳細については、「[ネットワーク ファイル システムの概要](/windows-server/storage/nfs/nfs-overview)」を参照してください。
- CIFS の詳細については、「[Microsoft SMB プロトコルと CIFS プロトコルの概要](/windows/desktop/fileio/microsoft-smb-protocol-and-cifs-protocol-overview)」を参照してください。
