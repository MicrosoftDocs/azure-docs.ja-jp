---
title: Azure Files のパフォーマンスのトラブルシューティング ガイド
description: Azure ファイル共有のパフォーマンスに関する既知の問題のトラブルシューティングを行います。 これらの問題が発生した場合に考えられる原因と関連する回避策を確認します。
author: gunjanj
ms.service: storage
ms.topic: troubleshooting
ms.date: 08/24/2020
ms.author: gunjanj
ms.subservice: files
ms.openlocfilehash: fe1460d4353addff1b8e3095cfe06c1fcb3b7bd0
ms.sourcegitcommit: 9c3cfbe2bee467d0e6966c2bfdeddbe039cad029
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/24/2020
ms.locfileid: "88782372"
---
# <a name="troubleshoot-azure-files-performance-issues"></a>Azure Files のパフォーマンスに関する問題のトラブルシューティング

この記事では、Azure ファイル共有に関連する一般的な問題を示します。 これらの問題が発生した場合に考えられる原因と回避策を提示します。

## <a name="high-latency-low-throughput-and-general-performance-issues"></a>高待機時間、低スループット、および全般的なパフォーマンスの問題

### <a name="cause-1-share-was-throttled"></a>原因 1:共有がスロットルされていた

IOPS、ファイル共有のイングレスまたはエグレスの制限に達した場合、要求はスロットルされます。 Standard および Premium ファイル共有の制限については、「[ファイル共有とファイルのスケール ターゲット](https://docs.microsoft.com/azure/storage/files/storage-files-scale-targets#file-share-and-file-scale-targets)」を参照してください。

共有がスロットルされているかどうかを確認するには、ポータルで Azure メトリックスを利用できます。

1. [Azure portal](https://portal.azure.com) にサインインします。

1. **[すべてのサービス]** を選択して「**メトリック**」を検索します。

1. **[メトリック]** を選びます。

1. リソースとしてお使いのストレージ アカウントを選択します。

1. メトリック名前空間として **[ファイル]** を選択します。

1. メトリックとして **[トランザクション]** を選択します。

1. **ResponseType** のフィルターを追加し、要求に **SuccessWithThrottling** (SMB の場合) または **ClientThrottlingError** (REST の場合) の応答コードがあるかどうかを調べます。

![Premium ファイル共有のメトリック オプション](media/storage-troubleshooting-premium-fileshares/metrics.png)

> [!NOTE]
> ファイル共有がスロットルされた場合にアラートを受け取るには、「[ファイル共有がスロットルされた場合にアラートを作成する方法](#how-to-create-an-alert-if-a-file-share-is-throttled)」を参照してください。

### <a name="solution"></a>解決策

- Standard ファイル共有を使用している場合は、お使いのストレージ アカウントで [[大きいファイルの共有]](https://docs.microsoft.com/azure/storage/files/storage-files-how-to-create-large-file-share?tabs=azure-portal) を有効にします。 大きいファイルの共有では、共有あたり最大 10,000 IOPS がサポートされています。
- Premium ファイル共有を使用している場合は、プロビジョニングされたファイル共有のサイズを増やして IOPS の上限を上げます 詳細については、Azure Files 計画ガイドの「[Premium ファイル共有のプロビジョニングについて](https://docs.microsoft.com/azure/storage/files/storage-files-planning#understanding-provisioning-for-premium-file-shares)」セクションを参照してください。

### <a name="cause-2-metadatanamespace-heavy-workload"></a>原因 2:メタデータ/名前空間の過大なワークロード

要求の大半がメタデータ中心である場合 (createfile/openfile/closefile/queryinfo/querydirectory など)、読み取り/書き込み操作と比較すると待機時間が悪化します。

要求のほとんどがメタデータ中心であるかを確認するには、上記と同じ手順を使用できます。 ただし、**ResponseType** のフィルターを追加する代わりに、**API 名**のフィルターを追加します。

![メトリックでの API 名のフィルター](media/storage-troubleshooting-premium-fileshares/MetadataMetrics.png)

### <a name="workaround"></a>回避策

- メタデータ操作の数を減らすようにアプリケーションを変更できるかどうかを調べます。
- ファイル共有に VHD を追加し、クライアントから SMB を介して VHD をマウントして、データに対するファイル操作を実行します。 このアプローチは、単一のライターと複数のリーダーのシナリオで機能し、ローカルで直接接続されているストレージと同様のパフォーマンスを提供するメタデータ操作をローカルで行うことができます。

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

### <a name="workaround"></a>回避策

- より大きなコアの VM を取得すればスループットの向上に役立つ可能性があります。
- 複数の VM からクライアント アプリケーションを実行すると、スループットが向上します。

- 可能な場合は、REST API を使用します。

## <a name="throughput-on-linux-clients-is-significantly-lower-when-compared-to-windows-clients"></a>Linux クライアントでのスループットは、Windows クライアントと比較すると大幅に低下します。

### <a name="cause"></a>原因

これは、Linux での SMB クライアントの実装に関する既知の問題です。

### <a name="workaround"></a>回避策

- 複数の VM 間で負荷を分散します。
- 同じ VM 上で、**nosharesock** オプションで複数のマウント ポイントを使用し、これらのマウント ポイント間で負荷を分散します。
- Linux では、すべての **fsync** 呼び出しで SMB フラッシュが強制されないようにするために、**nostrictsync** オプションを使用したマウントを試してみてください。 Azure Files の場合、このオプションはデータの一貫性に干渉することはありませんが、ディレクトリの一覧に古いファイルのメタデータが表示される可能性があります (**ls -l** コマンド)。 ファイルのメタデータのクエリを直接実行する (**stat** コマンド) と、最新のファイルのメタデータが返されます。

## <a name="high-latencies-for-metadata-heavy-workloads-involving-extensive-openclose-operations"></a>多大な開く/閉じる操作に関連するメタデータの過大なワークロードの長い待機時間。

### <a name="cause"></a>原因

ディレクトリ リースのサポートの欠如。

### <a name="workaround"></a>回避策

- 可能であれば、同じディレクトリで短時間に過剰に開く/閉じる操作を行うことを避けます。
- Linux VM では、マウント オプションとして **actimeo=\<sec>** を指定することで、ディレクトリ エントリ キャッシュのタイムアウトを増やします。 既定では 1 秒になっているので、3 や 5 のような大きな値が役立つことがあります。
- Linux VM の場合、カーネルを 4.20 以上にアップグレードします。

## <a name="low-iops-on-centosrhel"></a>CentOS/RHEL での低 IOPS

### <a name="cause"></a>原因

CentOS/RHEL では、1 未満の IO の深さはサポートされていません。

### <a name="workaround"></a>回避策

- CentOS 8/RHEL 8 にアップグレードします。
- Ubuntu に変更します。

## <a name="slow-file-copying-to-and-from-azure-files-in-linux"></a>Linux で Azure Files との間でのファイルのコピーが遅い

Azure Files との間で低速なファイルのコピーが発生する場合は、Linux トラブルシューティング ガイドの「[Linux で Azure Files との間でのファイルのコピーが遅い](storage-troubleshoot-linux-file-connection-problems.md#slow-file-copying-to-and-from-azure-files-in-linux)」というセクションに目を通してください。

## <a name="jitterysaw-tooth-pattern-for-iops"></a>IOPS のジッター/のこぎり歯パターン

### <a name="cause"></a>原因

クライアント アプリケーションが絶えずベースライン IOPS を超えています。 現在、サービス側で要求負荷を平滑化することはないので、クライアントはベースライン IOPS を超えた場合、サービスによってスロットルされます。 このスロットリングの結果、クライアントにジッター/のこぎり歯 IOPS パターンが発生することがあります。 この場合、クライアントによって達成される平均 IOPS はベースライン IOPS を下回る可能性があります。

### <a name="workaround"></a>回避策

- クライアント アプリケーションから要求負荷を軽減して、共有がスロットルされないようにします。
- 共有がスロットルされないように、共有のクォータを増やします。

## <a name="excessive-directoryopendirectoryclose-calls"></a>DirectoryOpen/DirectoryClose の過剰な呼び出し

### <a name="cause"></a>原因

DirectoryOpen/DirectoryClose の呼び出し数が上位の API 呼び出しに入っており、そのような多数の呼び出しをクライアントにさせるつもりがない場合、Azure クライアント VM にインストールされているウイルス対策に問題がある可能性があります。

### <a name="workaround"></a>回避策

- この問題の修正プログラムは [Windows の 4 月のプラットフォーム更新プログラム](https://support.microsoft.com/help/4052623/update-for-windows-defender-antimalware-platform)で入手できます。

## <a name="file-creation-is-slower-than-expected"></a>ファイルの作成が予想より遅い

### <a name="cause"></a>原因

多数のファイルの作成に基づくワークロードの場合、Premium ファイル共有と標準ファイル共有の間で大きなパフォーマンスの差は見られません。

### <a name="workaround"></a>回避策

- [なし] :

## <a name="slow-performance-from-windows-81-or-server-2012-r2"></a>Windows 8.1 または Server 2012 R2 からのパフォーマンスの低下

### <a name="cause"></a>原因

IO 集中型ワークロードのため、Azure Files へのアクセスの待機時間が予想よりも長くなります。

### <a name="workaround"></a>回避策

- 使用可能な[修正プログラム](https://support.microsoft.com/help/3114025/slow-performance-when-you-access-azure-files-storage-from-windows-8-1)をインストールします。

## <a name="how-to-create-an-alert-if-a-file-share-is-throttled"></a>ファイル共有がスロットルされた場合にアラートを作成する方法

1. **Azure portal** で目的の**ストレージ アカウント**に移動します。
2. [監視] セクションで **[アラート]** をクリックしてから、 **[+ 新しいアラート ルール]** をクリックします。
3. **[リソースの編集]** をクリックし、ストレージ アカウントの**ファイル リソースの種類**を選択してから、 **[完了]** をクリックします。 たとえば、ストレージ アカウント名が contoso の場合は、contoso/ファイル リソースを選択します。
4. **[条件の選択]** をクリックして条件を追加します。
5. ストレージ アカウントでサポートされているシグナルの一覧が表示されたら、 **[トランザクション]** メトリックを選択します。
6. **[シグナルロジックの構成]** ブレードで、 **[ディメンション名]** ドロップダウンをクリックし、 **[応答の種類]** を選択します。
7. **[ディメンション値]** ドロップダウンをクリックして、 **[SuccessWithThrottling]** (SMB の場合) または **[ClientThrottlingError]** (REST の場合) を選択します。

  > [!NOTE]
  > [SuccessWithThrottling] または [ClientThrottlingError] のディメンション値が一覧にない場合は、リソースがスロットルされていないことを意味します。 ディメンション値を追加するには、 **[ディメンション値]** ドロップダウンの横の **[カスタム値を追加]** をクリックし、「**SuccessWithThrottling**」または「**ClientThrottlingError**」と入力し、 **[OK]** をクリックしてから、手順 7. を繰り返します。

8. **[ディメンション名]** ドロップダウンをクリックし、 **[ファイル共有]** を選択します。
9. **[ディメンション値]** ドロップダウンをクリックして、アラートの対象とするファイル共有を選択します。

  > [!NOTE]
  > ファイル共有が標準のファイル共有の場合は、 **[現在と将来のすべての値]** を選択します。 ディメンション値のドロップダウンには、ファイル共有が一覧表示されません。これは、標準ファイル共有では、共有ごとのメトリックを使用できないためです。 ストレージ アカウント内のいずれかのファイル共有がスロットルされている場合は、標準ファイル共有のスロットリング アラートがトリガーされ、どのファイル共有がスロットルされたかはアラートによって識別されません。 標準ファイル共有では共有ごとのメトリックを使用できないため、ストレージ アカウントごとに 1 つのファイル共有を使用することをお勧めします。

10. **アラート パラメーター** (しきい値、演算子、集計粒度、評価の頻度) を定義し、 **[完了]** をクリックします。

  > [!TIP]
  > 静的しきい値を使おうとしている場合は、ファイル共有が現在スロットルされているかどうかの妥当なしきい値を判断するのにメトリック グラフが役立つことがあります。 動的しきい値を使用している場合は、メトリック グラフに最新のデータに基づいて計算されたしきい値が表示されます。

11. **[アクション グループの選択]** をクリックし、既存のアクション グループを選択するか、新しいアクション グループを作成して、**アクション グループ** (メール、SMS など) をアラートに追加します。
12. **アラート ルール名**、**説明**、および**重大度**などの**アラートの詳細**を指定します。
13. **[アラート ルールの作成]** をクリックして、アラートを作成します。

Azure Monitor でのアラートの構成の詳細については、「[Microsoft Azure のアラートの概要]( https://docs.microsoft.com/azure/azure-monitor/platform/alerts-overview)」をご覧ください。

## <a name="see-also"></a>関連項目
* [Windows での Azure Files に関する問題のトラブルシューティング](storage-troubleshoot-windows-file-connection-problems.md)
* [Linux での Azure Files に関する問題のトラブルシューティング](storage-troubleshoot-linux-file-connection-problems.md)
* [Azure Files に関してよく寄せられる質問 (FAQ)](storage-files-faq.md)
