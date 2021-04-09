---
title: Azure ファイル共有のパフォーマンスのトラブルシューティング ガイド
description: Azure ファイル共有のパフォーマンスに関する既知の問題のトラブルシューティングを行います。 これらの問題が発生した場合に考えられる原因と関連する回避策を確認します。
author: gunjanj
ms.service: storage
ms.topic: troubleshooting
ms.date: 11/16/2020
ms.author: gunjanj
ms.subservice: files
ms.openlocfilehash: 9f858549f36d196c6412aec549d0ab2e2d864145
ms.sourcegitcommit: df1930c9fa3d8f6592f812c42ec611043e817b3b
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/13/2021
ms.locfileid: "103417673"
---
# <a name="troubleshoot-azure-file-shares-performance-issues"></a>Azure ファイル共有のパフォーマンスに関する問題のトラブルシューティング

この記事では、Azure ファイル共有に関連する一般的な問題を示します。 これらの問題が発生した場合に考えられる原因と回避策を提示します。

## <a name="high-latency-low-throughput-and-general-performance-issues"></a>高待機時間、低スループット、および全般的なパフォーマンスの問題

### <a name="cause-1-share-was-throttled"></a>原因 1:共有がスロットルされていた

1 秒あたりの操作回数 (IOPS)、ファイル共有のイングレスまたはエグレスの制限に達すると、要求がスロットルされます。 Standard および Premium ファイル共有の制限については、「[ファイル共有とファイルのスケール ターゲット](./storage-files-scale-targets.md#azure-file-share-scale-targets)」を参照してください。

共有がスロットルされているかどうかを確認するには、ポータルで Azure メトリックスにアクセスして使用します。

1. Azure portal で、ストレージ アカウントに移動します。

1. 左側のペインの **[監視]** で **[メトリック]** を選択します。

1. お使いのストレージ アカウント スコープのメトリック名前空間として、 **[ファイル]** を選択します。

1. メトリックとして **[トランザクション]** を選択します。

1. **[応答の種類]** に対してフィルターを追加し、その後、スロットルされた要求があるかどうかを確認します。 

    Standard ファイル共有の場合、要求がスロットルされると、次の応答の種類がログに記録されます。

    - SuccessWithThrottling
    - SuccessWithShareIopsThrottling
    - ClientShareIopsThrottlingError

    Premium ファイル共有の場合、要求がスロットルされると、次の応答の種類がログに記録されます。

    - SuccessWithShareEgressThrottling
    - SuccessWithShareIngressThrottling
    - SuccessWithShareIopsThrottling
    - ClientShareEgressThrottlingError
    - ClientShareIngressThrottlingError
    - ClientShareIopsThrottlingError

    各応答の種類の詳細については、「[メトリック ディメンション](./storage-files-monitoring-reference.md#metrics-dimensions)」を参照してください。

    !["応答の種類" プロパティ フィルターを示す Premium ファイル共有のメトリック オプションのスクリーンショット。](media/storage-troubleshooting-premium-fileshares/metrics.png)

    > [!NOTE]
    > アラートを受信するには、この記事の後半で示す「[ファイル共有がスロットルされた場合にアラートを作成する方法](#how-to-create-an-alert-if-a-file-share-is-throttled)」セクションをご覧ください。

### <a name="solution"></a>解決策

- Standard ファイル共有を使用している場合は、ストレージ アカウントで [[大きいファイルの共有]](./storage-files-how-to-create-large-file-share.md?tabs=azure-portal) を有効にします。 大きいファイルの共有では、共有あたり最大 10,000 IOPS がサポートされています。
- Premium ファイル共有を使用している場合は、プロビジョニングされたファイル共有のサイズを増やして IOPS の上限を上げます。 詳細については、「[Premium ファイル共有のプロビジョニングについて](./understanding-billing.md#provisioned-model)」をご覧ください。

### <a name="cause-2-metadata-or-namespace-heavy-workload"></a>原因 2:メタデータまたは名前空間の過大なワークロード

要求の大半がメタデータ中心 (createfile、openfile、closefile、queryinfo、querydirectory など) である場合は、読み取り/書き込み操作よりも待機時間が悪化します。

要求の大半がメタデータ中心であるかどうかを確認するには、前述の「原因 1」で説明した手順 1-4 に従ってください。 手順 5 では、 **[応答の種類]** のフィルターを追加する代わりに、 **[API 名]** にプロパティ フィルターを追加します。

!["API 名" プロパティ フィルターを示す Premium ファイル共有のメトリック オプションのスクリーンショット。](media/storage-troubleshooting-premium-fileshares/MetadataMetrics.png)

### <a name="workaround"></a>回避策

- メタデータ操作の数を減らすようにアプリケーションを変更できるかどうかを確認します。
- ファイル共有に仮想ハード ディスク (VHD) を追加し、クライアントから SMB を介して VHD をマウントして、データに対するファイル操作を実行します。 このアプローチは、単一のライターと複数のリーダーのシナリオで機能し、メタデータ操作をローカルで行うことができます。 セットアップすると、ローカルに直接アタッチされているストレージの場合と同様のパフォーマンスが実現されます。

### <a name="cause-3-single-threaded-application"></a>原因 3:シングル スレッド アプリケーション

使用しているアプリケーションがシングルスレッドで処理されている場合、これをセットアップすると、プロビジョニングされた共有サイズに応じて、最大限のスループットよりも IOPS スループットが大幅に低下する結果になる可能性があります。

### <a name="solution"></a>解決策

- スレッドの数を増やすことで、アプリケーションの並列処理を増やします。
- 並列処理が可能なアプリケーションに切り替えます。 たとえば、コピー操作では、Windows クライアントから AzCopy または RoboCopy を使用でき、Linux クライアントから **parallel** コマンドを使用できます。

## <a name="very-high-latency-for-requests"></a>非常に長い要求の待機時間

### <a name="cause"></a>原因

クライアント仮想マシン (VM) がファイル共有とは異なるリージョンに配置されている可能性があります。 待機時間が長いその他の理由として、クライアントまたはネットワークに起因する待機時間が原因として考えられます。

### <a name="solution"></a>解決策

- ファイル共有と同じリージョンに配置されている VM からアプリケーションを実行します。
- ストレージ アカウントについては、Azure portal で **Azure Monitor** 介したトランザクション メトリック **SuccessE2ELatency** および **SuccessServerLatency** を確認してください。 SuccessE2ELatency と SuccessServerLatency のメトリック値が大きく異なる場合は、ネットワークやクライアントに起因する待機時間が原因である可能性があります。 「Azure Files 監視データのリファレンス」の「[トランザクション メトリック](storage-files-monitoring-reference.md#transaction-metrics)」を参照してください。

## <a name="client-unable-to-achieve-maximum-throughput-supported-by-the-network"></a>ネットワークでサポートされている最大スループットを達成できないクライアント

### <a name="cause"></a>原因
考えられる原因の 1 つは、Standard ファイル共有で SMB マルチチャネルがサポートされていないことです。 現在、Azure ファイル共有ではシングル チャネルのみがサポートされているため、クライアント VM からサーバーへの接続は 1 つしかありません。 この単一の接続は、クライアント VM の単一のコアに固定されているため、VM から達成可能な最大スループットは単一コアにバインドされます。

### <a name="workaround"></a>回避策

- Premium ファイル共有の場合は、[FileStorage アカウントで SMB マルチチャネルを有効にします](storage-files-enable-smb-multichannel.md)。
- より大きなコアの VM を取得すれば、スループットの向上に役立つ可能性があります。
- 複数の VM からクライアント アプリケーションを実行すると、スループットが向上します。
- 可能な場合は、REST API を使用します。

## <a name="throughput-on-linux-clients-is-significantly-lower-than-that-of-windows-clients"></a>Linux クライアントでのスループットは、Windows クライアントよりも大幅に低下します。

### <a name="cause"></a>原因

これは、Linux での SMB クライアントの実装に関する既知の問題です。

### <a name="workaround"></a>回避策

- 複数の VM 間で負荷を分散します。
- 同じ VM 上で、**nosharesock** オプションで複数のマウント ポイントを使用し、これらのマウント ポイント間で負荷を分散します。
- Linux では、すべての **fsync** 呼び出しで SMB フラッシュが強制されないようにするために、**nostrictsync** オプションを使用したマウントを試してみてください。 Azure Files の場合、このオプションはデータの一貫性に干渉することはありませんが、ディレクトリのリストに古いファイルのメタデータが表示される可能性があります (**ls -l** コマンド)。 **stat** コマンドを使用してファイル メタデータのクエリを直接実行すると、最新のファイルのメタデータが返されます。

## <a name="high-latencies-for-metadata-heavy-workloads-involving-extensive-openclose-operations"></a>多大な開く/閉じる操作に関連するメタデータの過大なワークロードの長い待機時間

### <a name="cause"></a>原因

ディレクトリ リースのサポートの欠如。

### <a name="workaround"></a>回避策

- 可能であれば、同じディレクトリで短時間に過剰に開く/閉じる操作を使用することを避けます。
- Linux VM では、マウント オプションとして **actimeo=\<sec>** を指定することで、ディレクトリ エントリ キャッシュのタイムアウトを増やします。 既定では、タイムアウトが 1 秒であるため、3 秒や 5 秒などの大きな値が役立つ可能性があります。
- CentOS Linux または Red Hat Enterprise Linux (RHEL) VM の場合は、システムを CentOS Linux 8.2 または RHEL 8.2 にアップグレードします。 その他の Linux VM の場合は、カーネルを 5.0 以上にアップグレードします。

## <a name="low-iops-on-centos-linux-or-rhel"></a>CentOS Linux または RHEL 上の IOPS が低い

### <a name="cause"></a>原因

CentOS Linux または RHEL では、1 よりも大きい I/O の深さはサポートされていません。

### <a name="workaround"></a>回避策

- CentOS Linux 8 または RHEL 8 にアップグレードします。
- Ubuntu に変更します。

## <a name="slow-file-copying-to-and-from-azure-file-shares-in-linux"></a>Linux で Azure ファイル共有間でのファイルのコピーが遅い

低速なファイルのコピーが発生する場合は、[Linux トラブルシューティング ガイド](storage-troubleshoot-linux-file-connection-problems.md#slow-file-copying-to-and-from-azure-files-in-linux)の「Linux で Azure ファイル共有間でのファイルのコピーが遅い」というセクションをご覧ください。

## <a name="jittery-or-sawtooth-pattern-for-iops"></a>IOPS のジッターまたはのこぎり歯パターン

### <a name="cause"></a>原因

クライアント アプリケーションが絶えずベースライン IOPS を超えています。 現在、要求の負荷はサービス側で平準化されていません。 クライアントがベースライン IOPS を上回ると、サービスでスロットルされます。 スロットリングの結果、クライアントにジッターまたはのこぎり歯 IOPS パターンが発生することがあります。 この場合、クライアントによって達成される平均 IOPS はベースライン IOPS を下回る可能性があります。

### <a name="workaround"></a>回避策
- クライアント アプリケーションから要求負荷を軽減して、共有がスロットルされないようにします。
- 共有がスロットルされないように、共有のクォータを増やします。

## <a name="excessive-directoryopendirectoryclose-calls"></a>DirectoryOpen/DirectoryClose の過剰な呼び出し

### <a name="cause"></a>原因

**DirectoryOpen/DirectoryClose** の呼び出し数が上位の API 呼び出しに入っており、そのような多数の呼び出しをクライアントにさせるつもりがない場合、Azure クライアント VM にインストールされているウイルス対策が原因で問題が発生する可能性があります。

### <a name="workaround"></a>回避策

- この問題の修正プログラムは [Windows の 4 月のプラットフォーム更新プログラム](https://support.microsoft.com/help/4052623/update-for-windows-defender-antimalware-platform)で入手できます。

## <a name="file-creation-is-slower-than-expected"></a>ファイルの作成が予想より遅い

### <a name="cause"></a>原因

多数のファイルの作成に基づくワークロードの場合、Premium ファイル共有と Standard ファイル共有の間で大きなパフォーマンスの差異は見られません。

### <a name="workaround"></a>回避策

- [なし] :

## <a name="slow-performance-from-windows-81-or-server-2012-r2"></a>Windows 8.1 または Server 2012 R2 からのパフォーマンスの低下

### <a name="cause"></a>原因

I/O 集中型ワークロードのために、Azure ファイル共有へのアクセスの待機時間が予想よりも長くなります。

### <a name="workaround"></a>回避策

- 使用可能な[修正プログラム](https://support.microsoft.com/help/3114025/slow-performance-when-you-access-azure-files-storage-from-windows-8-1)をインストールします。

## <a name="smb-multichannel-option-not-visible-under-file-share-settings"></a>SMB マルチチャネル オプションは、ファイル共有の設定に表示されません。 

### <a name="cause"></a>原因

サブスクリプションが機能に登録されていないか、リージョンとアカウントの種類がサポートされていません。

### <a name="solution"></a>解決策

サブスクリプションが SMB マルチチャネル機能に登録されていることを確認してください。 [[概要]](storage-files-enable-smb-multichannel.md#getting-started) を参照してください。アカウントの概要ページで、アカウントの種類が FileStorage (Premium ファイル アカウント) であることを確認してください。 

## <a name="smb-multichannel-is-not-being-triggered"></a>SMB マルチチャネルはトリガーされません。

### <a name="cause"></a>原因

再マウントせずに、SMB マルチチャネル構成の設定が最近変更されました。

### <a name="solution"></a>解決策
 
-   Windows SMB クライアントまたはアカウント SMB マルチチャネル構成の設定が変更されたら、共有のマウントを解除し、60 秒間待機してから、共有を再マウントしてマルチチャネルをトリガーする必要があります。
-   Windows クライアント OS の場合は、たとえば、ファイルをコピーして SMB マルチチャネルをトリガーして、キューの深さが大きい IO 負荷 (QD=8 など) を生成します。  サーバー OS の場合は、SMB マルチチャネルが QD = 1 でトリガーされます。つまり、共有への IO を開始した直後に発生します。

## <a name="high-latency-on-web-sites-hosted-on-file-shares"></a>ファイル共有でホストされている Web サイトの待機時間が長い 

### <a name="cause"></a>原因  

ファイル共有に関するファイル変更通知の数が多くなると、待機時間が大幅に長くなる可能性があります。 一般に、この問題は、ディレクトリ構造が深い入れ子になっているファイル共有でホストされている Web サイトで発生します。 一般的なシナリオは、IIS でホストされている Web アプリケーションです。既定の構成では、ファイル変更通知はディレクトリごとにセットアップされます。 SMB クライアントが登録されている共有で変更 ([ReadDirectoryChangesW](/windows/win32/api/winbase/nf-winbase-readdirectorychangesw)) が行われるたびに、ファイル サービスからクライアントに変更通知がプッシュされます。そのため、システム リソースが消費され、変更の数に伴い問題が悪化します。 これにより、共有のスロットリングが発生し、クライアント側の待機時間が長くなります。 

確認するには、ポータルで Azure メトリックを使用します。 

1. Azure portal で、ストレージ アカウントに移動します。 
1. 左側のメニューの [監視] で [メトリック] を選択します。 
1. ストレージ アカウント スコープのメトリック名前空間として、[ファイル] を選択します。 
1. メトリックとして [トランザクション] を選択します。 
1. ResponseType のフィルターを追加し、要求に SuccessWithThrottling (SMB の場合) または ClientThrottlingError (REST の場合) の応答コードがあるかどうかを確認します。

### <a name="solution"></a>解決策 

- ファイル変更通知が使用されていない場合は、ファイル変更通知を無効にします (推奨)。
    - FCNMode を更新して、[ファイル変更通知を無効にします](https://support.microsoft.com/help/911272/fix-asp-net-2-0-connected-applications-on-a-web-site-may-appear-to-sto)。 
    - レジストリで `HKEY_LOCAL_MACHINE\System\CurrentControlSet\Services\W3SVC\Parameters\ConfigPollMilliSeconds ` を設定し、W3WP プロセスを再起動して、IIS ワーカー プロセス (W3WP) のポーリング間隔を 0 に更新します。 この設定の詳細については、[IIS の多くの部分で使用される共通のレジストリ キー](/troubleshoot/iis/use-registry-keys#registry-keys-that-apply-to-iis-worker-process-w3wp)に関するページをご覧ください。
- ファイル変更通知のポーリング間隔を長くして、ボリュームを減らします。
    - 要件に基づいて、W3WP ワーカー プロセスのポーリング間隔を大きい値 (10 分や 30 分など) に更新します。 [レジストリで](/troubleshoot/iis/use-registry-keys#registry-keys-that-apply-to-iis-worker-process-w3wp) `HKEY_LOCAL_MACHINE\System\CurrentControlSet\Services\W3SVC\Parameters\ConfigPollMilliSeconds ` を設定し、W3WP プロセスを再起動します。
- Web サイトのマップされた物理ディレクトリのディレクトリ構造が入れ子になっている場合は、ファイル変更通知のスコープを制限してみると、通知のボリュームが減る可能性があります。 既定では、仮想ディレクトリのマップ先となる物理ディレクトリ内の Web.config ファイルの構成と、その物理ディレクトリ内のすべての子ディレクトリ内の構成が IIS で使用されます。 子ディレクトリ内の Web.config ファイルを使用しない場合は、仮想ディレクトリで allowSubDirConfig 属性に false を指定します。 詳細については、[こちら](/iis/get-started/planning-your-iis-architecture/understanding-sites-applications-and-virtual-directories-on-iis#virtual-directories)をご覧ください。 
    - Web.Config で IIS 仮想ディレクトリ "allowSubDirConfig" 設定を *false* に設定して、マップされた物理子ディレクトリをスコープから除外します。  

## <a name="how-to-create-an-alert-if-a-file-share-is-throttled"></a>ファイル共有がスロットルされた場合にアラートを作成する方法

1. **Azure portal** で目的の **ストレージ アカウント** に移動します。
2. **[監視]** セクションで **[アラート]** をクリックしてから、 **[+ 新しいアラート ルール]** をクリックします。
3. **[リソースの編集]** をクリックし、ストレージ アカウントの **ファイル リソースの種類** を選択してから、 **[完了]** をクリックします。 たとえば、ストレージ アカウント名が `contoso` である場合、`contoso/file` リソースを選択します。
4. **[条件の追加]** をクリックして条件を追加します。
5. ストレージ アカウントでサポートされているシグナルの一覧が表示されたら、 **[トランザクション]** メトリックを選択します。
6. **[シグナルロジックの構成]** ブレードで、 **[ディメンション名]** ドロップダウンをクリックし、 **[応答の種類]** を選択します。
7. **[ディメンション値]** ドロップダウンをクリックし、ファイル共有に適した応答の種類を選択します。

    Standard ファイル共有の場合、次の応答の種類を選択します。

    - SuccessWithThrottling
    - SuccessWithShareIopsThrottling
    - ClientShareIopsThrottlingError

    Premium ファイル共有の場合、次の応答の種類を選択します。

    - SuccessWithShareEgressThrottling
    - SuccessWithShareIngressThrottling
    - SuccessWithShareIopsThrottling
    - ClientShareEgressThrottlingError
    - ClientShareIngressThrottlingError
    - ClientShareIopsThrottlingError

   > [!NOTE]
   > 応答の種類が **[ディメンション値]** ドロップダウンに一覧表示されない場合は、リソースがスロットルされていないことを意味します。 ディメンション値を追加するには、 **[ディメンション値]** ドロップダウン リストの横にある **[カスタム値を追加]** を選択し、応答の種類 (**SuccessWithThrottling** など) を入力し、 **[OK]** を選択します。その後、これらの手順を繰り返して、自分のファイル共有に適用できるすべての応答の種類を追加します。

8. **Premium ファイル共有** の場合は、 **[ディメンション名]** ドロップダウンをクリックし、 **[ファイル共有]** を選択します。 **Standard ファイル共有** の場合は、**手順 #10** に進みます。

   > [!NOTE]
   > ファイル共有が Standard ファイル共有の場合、 **[ファイル共有]** ディメンションにはファイル共有が一覧に表示されません。これは、Standard ファイル共有では、共有ごとのメトリックを使用できないためです。 ストレージ アカウント内のいずれかのファイル共有がスロットルされている場合は、標準ファイル共有のスロットリング アラートがトリガーされ、どのファイル共有がスロットルされたかはアラートによって識別されません。 標準ファイル共有では共有ごとのメトリックを使用できないため、ストレージ アカウントごとに 1 つのファイル共有を使用することをお勧めします。

9. **[ディメンション値]** ドロップダウンをクリックして、アラートの対象とするファイル共有を選択します。
10. **アラート パラメーター** (しきい値、演算子、集計粒度、評価の頻度) を定義し、 **[完了]** をクリックします。

    > [!TIP]
    > 静的しきい値を使おうとしている場合は、ファイル共有が現在スロットルされているかどうかの妥当なしきい値を判断するのにメトリック グラフが役立つことがあります。 動的しきい値を使用している場合は、メトリック グラフに最新のデータに基づいて計算されたしきい値が表示されます。

11. **[Add action groups]\(アクション グループの追加\)** をクリックし、既存のアクション グループを選択するか、新しいアクション グループを作成して、**アクション グループ** (電子メール、SMS など) をアラートに追加します。
12. **[アラート ルール名]** 、 **[説明]** 、 **[重大度]** などの **[アラートの詳細]** を指定します。
13. **[アラート ルールの作成]** をクリックして、アラートを作成します。

Azure Monitor でのアラートの構成の詳細については、「[Microsoft Azure のアラートの概要]( https://docs.microsoft.com/azure/azure-monitor/platform/alerts-overview)」をご覧ください。

## <a name="how-to-create-alerts-if-a-premium-file-share-is-trending-toward-being-throttled"></a>Premium ファイル共有がスロットルされる傾向にある場合にアラートを作成する方法

1. Azure portal で、ストレージ アカウントに移動します。
2. **[監視]** セクションで **[アラート]** を選択し、 **[新しいアラート ルール]** を選択します。
3. **[リソースの編集]** を選択し、ストレージ アカウントの **ファイル リソースの種類** を選択してから、 **[完了]** を選択します。 たとえば、ストレージ アカウント名が *contoso* の場合は、contoso/ファイル リソースを選択します。
4. **[条件の選択]** を選択して条件を追加します。
5. ストレージ アカウントでサポートされているシグナルのリストで、 **[エグレス]** メトリックを選択します。

   > [!NOTE]
   > イングレス、エグレス、またはトランザクションの値が設定したしきい値を超えたときに発行される 3 つの個別のアラートを作成する必要があります。 これは、すべての条件を満たす場合にのみアラートがトリガーされるためです。 たとえば、すべての条件を 1 つのアラートに含めた場合、イングレス、エグレス、およびトランザクションがそれぞれのしきい値を超えた場合にのみアラートが発行されます。

6. 下へスクロールします。 **[ディメンション名]** ドロップダウン リストで、 **[ファイル共有]** を選択します。
7. **[ディメンション値]** ドロップダウンで、アラートの対象とする 1 つ以上のファイル共有を選択します。
8. **[演算子]** 、 **[しきい値]** 、 **[集計粒度]** 、 **[評価の頻度]** で値を選択してアラート パラメーターを定義してから、 **[完了]** をクリックします。

   秒あたりのエグレス、イングレス、および I/O がプロビジョニングされていますが、エグレス、イングレス、およびトランザクションのメトリックは分あたりで表されています。 したがって、たとえば、プロビジョニングされたエグレスの 1 秒あたりのメビバイトが 90&nbsp;(MiB/秒) であり、しきい値をプロビジョニングされたエグレスの 80&nbsp;% に設定する場合は、次のようにアラート パラメーターを選択します。 
   - **[しきい値]** : *75497472* 
   - **[演算子]** : *次の値以上*
   - **[集計の種類]** : *平均*
   
   アラートのノイズをどれだけにするかに応じて、 **[集計粒度]** と **[評価の頻度]** の値を選択できます。 たとえば、アラートで 1 時間の平均イングレスを確認し、1 時間ごとにアラート ルールが実行されるようにする場合は、次のように選択します。
   - **[集計粒度]** : *1 時間*
   - **[評価の頻度]** : *1 時間*

9. **[Add action groups]\(アクション グループの追加\)** を選択し、既存のアクション グループを選択するか、新しいものを作成して、アクション グループ (電子メールや SMS など) をアラートに追加します。
10. アラートの詳細 ( **[アラート ルール名]** 、 **[説明]** 、 **[重大度]** など) を入力します。
11. **[アラート ルールの作成]** を選択して、アラートを作成します。

    > [!NOTE]
    > - *プロビジョニングされたイングレスが原因で*、Premium ファイル共有がまもなくスロットルされることを通知するには、前の手順に従います。ただし、次のように変更してください。
    >    - 手順 5 で、 **[エグレス]** ではなく、 **[イングレス]** メトリックを選択します。
    >
    > - *プロビジョニングされた IOPS が原因で*、Premium ファイル共有がまもなくスロットルされることを通知するには、前の手順に従います。ただし、次のように変更してください。
    >    - 手順 5 で、 **[エグレス]** ではなく、 **[トランザクション]** メトリックを選択します。
    >    - 手順 10 では、 **[集計の種類]** の唯一のオプションは *[合計]* です。 したがって、しきい値は、選択した集計粒度によって異なります。 たとえば、プロビジョニングされたベースライン IOPS の 80&nbsp;% にしきい値を設定し、 **[集計粒度]** に *1 時間* を選択した場合は、 **[しきい値]** がベースライン IOPS (バイト単位) &times;&nbsp;0.8 &times;&nbsp;3600 になります。 

Azure Monitor でのアラートの構成の詳細については、「[Microsoft Azure のアラートの概要]( https://docs.microsoft.com/azure/azure-monitor/platform/alerts-overview)」をご覧ください。

## <a name="see-also"></a>関連項目
- [Windows での Azure Files に関する問題のトラブルシューティング](storage-troubleshoot-windows-file-connection-problems.md)  
- [Linux での Azure Files に関する問題のトラブルシューティング](storage-troubleshoot-linux-file-connection-problems.md)  
- [Azure Files に関する FAQ](storage-files-faq.md)
