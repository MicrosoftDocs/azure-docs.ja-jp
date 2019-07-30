---
title: Azure Files のパフォーマンスのトラブルシューティング ガイド
description: Azure ファイル共のパフォーマンスに関する既知の問題と、関連する回避策。
services: storage
author: gunjanj
ms.service: storage
ms.topic: article
ms.date: 04/25/2019
ms.author: gunjanj
ms.subservice: files
ms.openlocfilehash: 8c35501f3afbeed519fb5304229f25be1cbd5f9b
ms.sourcegitcommit: f56b267b11f23ac8f6284bb662b38c7a8336e99b
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/28/2019
ms.locfileid: "67445670"
---
# <a name="troubleshoot-azure-files-performance-issues"></a>Azure Files のパフォーマンスに関する問題のトラブルシューティング

この記事では、Azure ファイル共有に関連する一般的な問題を示します。 これらの問題が発生した場合に考えられる原因と回避策を提示します。

## <a name="high-latency-low-throughput-and-general-performance-issues"></a>高待機時間、低スループット、および全般的なパフォーマンスの問題

### <a name="cause-1-share-experiencing-throttling"></a>原因 1:共有でのスロットルの発生

Premium 共有の既定のクォータは 100 GiB であり、これは 100 ベースライン IOPS (1 時間に最高 300 のバーストの可能性あり) を提供します。 プロビジョニングとその IOPS との関係の詳細については、プランニング ガイドの[プロビジョニング済みの共有](storage-files-planning.md#provisioned-shares)に関するセクションを参照してください。

共有がスロットルされているかどうかを確認するには、ポータルで Azure メトリックスを利用できます。

1. [Azure Portal](https://portal.azure.com) にサインインします。

1. **[すべてのサービス]** を選択して「**メトリック**」を検索します。

1. **[メトリック]** を選びます。

1. リソースとしてお使いのストレージ アカウントを選択します。

1. メトリック名前空間として **[ファイル]** を選択します。

1. メトリックとして **[トランザクション]** を選択します。

1. **ResponseType** のフィルターを追加し、要求に **SuccessWithThrottling** (SMB の場合) または **ClientThrottlingError** (REST の場合) の応答コードがあるかどうかを調べます。

![Premium ファイル共有のメトリック オプション](media/storage-troubleshooting-premium-fileshares/metrics.png)

### <a name="solution"></a>解決策

- 共有のクォータを高くすることで、共有のプロビジョニング容量を増やします。

### <a name="cause-2-metadatanamespace-heavy-workload"></a>原因 2:メタデータ/名前空間の過大なワークロード

要求の大半がメタデータ中心である場合 (createfile/openfile/closefile/queryinfo/querydirectory など)、読み取り/書き込み操作と比較すると待機時間が悪化します。

要求のほとんどがメタデータ中心であるかを確認するには、上記と同じ手順を使用できます。 ただし、**ResponseType** のフィルターを追加する代わりに、**API 名**のフィルターを追加します。

![メトリックでの API 名のフィルター](media/storage-troubleshooting-premium-fileshares/MetadataMetrics.png)

### <a name="workaround"></a>対処法

- メタデータ操作の数を減らすようにアプリケーションを変更できるかどうかを調べます。

### <a name="cause-3-single-threaded-application"></a>原因 3:シングル スレッド アプリケーション

顧客が使用しているアプリケーションがシングルスレッドで処理されている場合、これは、プロビジョニングされた共有サイズに基づく可能な最大値よりも IOPS/スループットが大幅に低下する結果になる可能性があります。

### <a name="solution"></a>解決策

- スレッドの数を増やすことで、アプリケーションの並列処理を増やします。
- 並列処理が可能なアプリケーションに切り替えます。 たとえば、コピー操作には、顧客は Windows クライアントから AzCopy または RoboCopy を使用でき、Linux クライアントでは **parallel** コマンドを使用できます。

## <a name="very-high-latency-for-requests"></a>非常に長い要求の待機時間

### <a name="cause"></a>原因

クライアント VM がファイル共有とは異なるリージョンに置かれている可能性があります。

### <a name="solution"></a>解決策

- ファイル共有と同じリージョンに配置されている VM からアプリケーションを実行します。

## <a name="client-unable-to-achieve-maximum-throughput-supported-by-the-network"></a>ネットワークでサポートされている最大スループットを達成できないクライアント

この問題の考えられる原因の 1 つは、SMB マルチチャネルがサポートされていないことです。 Azure ファイル共有は現在、シングル チャネルだけをサポートしているため、クライアント VM からサーバーへの接続は 1 つしかありません。 この単一の接続は、クライアント VM の単一のコアに固定されているため、VM から達成可能な最大スループットは単一コアにバインドされます。

### <a name="workaround"></a>対処法

- より大きなコアの VM を取得すればスループットの向上に役立つ可能性があります。
- 複数の VM からクライアント アプリケーションを実行すると、スループットが向上します。
- 可能な場合は、REST API を使用します。

## <a name="throughput-on-linux-clients-is-significantly-lower-when-compared-to-windows-clients"></a>Linux クライアントでのスループットは、Windows クライアントと比較すると大幅に低下します。

### <a name="cause"></a>原因

これは、Linux での SMB クライアントの実装に関する既知の問題です。

### <a name="workaround"></a>対処法

- 複数の VM 間で負荷を分散します
- 同じ VM 上で、**nosharesock** オプションで複数のマウント ポイントを使用し、これらのマウント ポイント間で負荷を分散します。

## <a name="high-latencies-for-metadata-heavy-workloads-involving-extensive-openclose-operations"></a>多大な開く/閉じる操作に関連するメタデータの過大なワークロードの長い待機時間。

### <a name="cause"></a>原因

ディレクトリ リースのサポートの欠如。

### <a name="workaround"></a>対処法

- 可能であれば、同じディレクトリで短時間に過剰に開く/閉じる操作を行うことを避けます。
- Linux VM では、マウント オプションとして **actimeo=<sec>** を指定することで、ディレクトリ エントリ キャッシュのタイムアウトを増やします。 既定では 1 秒になっているので、3 や 5 のような大きな値が役立つことがあります。
- Linux VM の場合、カーネルを 4.20 以上にアップグレードします。

## <a name="low-iops-on-centosrhel"></a>CentOS/RHEL での低 IOPS

### <a name="cause"></a>原因

CentOS/RHEL では、1 未満の IO の深さはサポートされていません。

### <a name="workaround"></a>対処法

- CentOS 8/RHEL 8 にアップグレードします。
- Ubuntu に変更します。

## <a name="slow-file-copying-to-and-from-azure-files-in-linux"></a>Linux で Azure Files との間でのファイルのコピーが遅い

Azure Files との間で低速なファイルのコピーが発生する場合は、Linux トラブルシューティング ガイドの「[Linux で Azure Files との間でのファイルのコピーが遅い](storage-troubleshoot-linux-file-connection-problems.md#slow-file-copying-to-and-from-azure-files-in-linux)」というセクションに目を通してください。

## <a name="jitterysaw-tooth-pattern-for-iops"></a>IOPS のジッター/のこぎり歯パターン

### <a name="cause"></a>原因

クライアント アプリケーションが絶えずベースライン IOPS を超えています。 現在、サービス側で要求負荷を平滑化することはないので、クライアントはベースライン IOPS を超えた場合、サービスによってスロットルされます。 このスロットリングの結果、クライアントにジッター/のこぎり歯 IOPS パターンが発生することがあります。 この場合、クライアントによって達成される平均 IOPS はベースライン IOPS を下回る可能性があります。

### <a name="workaround"></a>対処法

- クライアント アプリケーションから要求負荷を軽減して、共有がスロットルされないようにします。
- 共有がスロットルされないように、共有のクォータを増やします。

## <a name="excessive-directoryopendirectoryclose-calls"></a>DirectoryOpen/DirectoryClose の過剰な呼び出し

### <a name="cause"></a>原因

DirectoryOpen/DirectoryClose の呼び出し数が上位の API 呼び出しに入っており、そのような多数の呼び出しをクライアントにさせるつもりがない場合、Azure クライアント VM にインストールされているウイルス対策に問題がある可能性があります。

### <a name="workaround"></a>対処法

- この問題の修正プログラムは [Windows の 4 月のプラットフォーム更新プログラム](https://support.microsoft.com/help/4052623/update-for-windows-defender-antimalware-platform)で入手できます。

## <a name="file-creation-is-slower-than-expected"></a>ファイルの作成が予想より遅い

### <a name="cause"></a>原因

多数のファイルの作成に基づくワークロードの場合、Premium ファイル共有と標準ファイル共有の間で大きなパフォーマンスの差は見られません。

### <a name="workaround"></a>対処法

- なし。

## <a name="slow-performance-from-windows-81-or-server-2012-r2"></a>Windows 8.1 または Server 2012 R2 からのパフォーマンスの低下

### <a name="cause"></a>原因

IO 集中型ワークロードのため、Azure Files へのアクセスの待機時間が予想よりも長くなります。

### <a name="workaround"></a>対処法

- 使用可能な[修正プログラム](https://support.microsoft.com/help/3114025/slow-performance-when-you-access-azure-files-storage-from-windows-8-1)をインストールします。