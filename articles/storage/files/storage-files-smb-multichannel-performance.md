---
title: SMB マルチチャネルのパフォーマンス - Azure Files
description: SMB マルチチャネルのパフォーマンスについて説明します。
author: gunjanj
ms.service: storage
ms.topic: conceptual
ms.date: 11/16/2020
ms.author: gunjanj
ms.subservice: files
ms.openlocfilehash: a9edd93aa265622732be4a7582cce9900959bf6d
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "100374985"
---
# <a name="smb-multichannel-performance"></a>SMB マルチチャネルのパフォーマンス

Azure Files SMB マルチチャネル (プレビュー) を使用すると、SMB 3.x クライアントから、FileStorage アカウント内の Premium ファイル共有への複数のネットワーク接続を確立できます。 Windows Server 2012 および Windows 8 クライアントで、SMB 3.0 プロトコルに SMB マルチチャネル機能が導入されました。 これにより、SMB マルチチャネルをサポートしている Azure Files SMB 3.x クライアントから、Azure Premium ファイル共有の機能を利用できます。 ストレージ アカウントで SMB マルチチャネルを有効にするための追加料金は発生しません。

## <a name="benefits"></a>利点

Azure Files SMB マルチチャネルを使用すると、クライアントから、複数のネットワーク接続を使用して、パフォーマンスを向上させることができ、所有コストも削減します。 複数の NIC に帯域幅を集約し、NIC の Receive Side Scaling (RSS) サポートを利用して複数の CPU に IO 負荷を分散させることで、パフォーマンスの向上が達成されます。

- **スループットの向上**:複数接続により、複数のパスで並行してデータを転送できるため、サイズが大きく、IO サイズが大きいファイルを使用し、単一の VM またはより小さな VM セットからの高スループットを必要とするワークロードに大きなメリットがあります。 これらのワークロードには、コンテンツ作成やコード変換のためのメディアやエンターテインメント、ゲノミクス、金融サービスのリスク分析などがあります。
- **高 IOPS**:NIC RSS 機能を使用すると、複数の CPU で複数の接続がある場合に効果的な負荷分散を行うことができます。 これは、より高い IOPS スケールと VM CPU の有効活用に役立ちます。 これは、データベース アプリケーションなど、IO サイズが小さいワークロードに便利です。
- **ネットワーク フォールト トレランス**: 複数接続により、クライアントが単一の接続に依存しなくなるため、中断のリスクが軽減されます。
- **自動構成**: クライアントとストレージ アカウントで SMB マルチチャネルを有効にすると、既存の接続を動的に検出できるようになり、必要に応じて追加の接続パスを作成できます。
- **コストの最適化**:ワークロードは、Premium 共有に接続しながら、単一の VM または小さな VM セットからスケールアップできます。 これにより、ワークロードを実行および管理するために必要な VM の数が減り、総保有コストを削減できます。

SMB マルチチャネルの詳細については、[Windows のドキュメント](/azure-stack/hci/manage/manage-smb-multichannel)を参照してください。

この機能により、マルチスレッド プリケーションのパフォーマンスが向上するメリットが得られますが、通常シングルスレッド アプリケーションには役立ちません。 詳細については、「[パフォーマンスの比較](#performance-comparison)」セクションを参照してください。

## <a name="limitations"></a>制限事項

[!INCLUDE [storage-files-smb-multi-channel-restrictions](../../../includes/storage-files-smb-multi-channel-restrictions.md)]

### <a name="regional-availability"></a>リージョン別の提供状況

[!INCLUDE [storage-files-smb-multi-channel-regions](../../../includes/storage-files-smb-multi-channel-regions.md)]

## <a name="configuration"></a>構成

SMB マルチチャネルは、機能がクライアント側 (お使いのクライアント) とサービス側 (お使いの Azure ストレージ アカウント) の両方で有効になっている場合にのみ機能します。

Windows クライアントでは、SMB マルチチャネルが既定で有効になっています。 次の PowerShell コマンドを実行して、構成を確認できます。 

`Get-smbClientConfiguration | select EnableMultichannel`.
 
Azure ストレージ アカウントで、SMB マルチチャネルを有効にする必要があります。 「[SMB マルチチャネルを有効にする (プレビュー)](storage-files-enable-smb-multichannel.md)」を参照してください。

### <a name="disable-smb-multichannel"></a>SMB マルチチャネルを無効にする
ほとんどのシナリオ (特にマルチスレッドのワークロード) では、クライアントのパフォーマンスは SMB マルチチャネルにより向上するはずです。 しかしながら、シングルスレッドのワークロードやテスト目的など、特定のシナリオでは、SMB マルチチャネルを無効にした方がよい場合があります。 詳細については、「[パフォーマンスの比較](#performance-comparison)」を参照してください。

## <a name="verify-smb-multichannel-is-configured-correctly"></a>SMB マルチチャネルが正しく構成されていることを確認する

1. Premium ファイル共有を作成するか、既存のファイル共有を使用します。
1. クライアントが SMB マルチチャネルをサポートしている (1 つまたは複数のネットワーク アダプターの Receive Side Scaling が有効になっている) ことを保証します。 詳細については、[Windows のドキュメント](/azure-stack/hci/manage/manage-smb-multichannel)を参照してください。
1. クライアントにファイル共有をマウントします。
1. アプリケーションで負荷を生成します。
    robocopy /MT などのコピー ツールや Diskspd などのパフォーマンス ツールを使用してファイルの読み取り/書き込みを行うと、負荷を生じさせることができます。
1. 管理者として PowerShell を開き、次のコマンドを使用します: `Get-SmbMultichannelConnection |fl`
1. **MaxChannels** および **CurrentChannels** プロパティを検索します

:::image type="content" source="media/storage-files-smb-multichannel-performance/files-smb-multi-channel-connection.PNG" alt-text="Get-SMBMultichannelConnection の結果のスクリーンショット。" lightbox="media/storage-files-smb-multichannel-performance/files-smb-multi-channel-connection.PNG":::

## <a name="performance-comparison"></a>パフォーマンスの比較

読み取り/書き込みのワークロード パターンには、シングルスレッドとマルチスレッドの 2 つのカテゴリがあります。 ほとんどのワークロードで複数のファイルが使用されますが、共有内の 1 つのファイルを使ってワークロードが動作する特殊なユース ケースがあります。 このセクションでは、それぞれのユース ケースとパフォーマンスへの影響について説明します。 一般に、ほとんどのワークロードはマルチスレッドであり、ワークロードが複数のファイルに分散されるため、SMB マルチチャネルを使用することでパフォーマンスの大幅な向上が見られるはずです。

- **マルチスレッド、複数ファイル**:ワークロード パターンによって、複数のチャネルでの IO の読み取りと書き込みのパフォーマンスが大幅に向上するはずです。 IOPS、スループット、待ち時間について、パフォーマンスが 2 から 4 倍向上します。 このカテゴリでは、最適なパフォーマンスを実現するために SMB マルチチャネルを有効にする必要があります。
- **マルチスレッド、単一ファイル**:このカテゴリのほとんどのユース ケースでは、特にワークロードの平均 IO サイズが約 16k を超える場合、ワークロードは SMB マルチチャネルを有効にすることでメリットが得られます。 SMB マルチチャネルのメリットが得られるシナリオ例としては、大きな単一ファイルのバックアップや回復があります。 例外として SMB マルチチャネルを無効にした方がよいのは、ワークロードが小さく、IO が重い場合です。 この場合、10% ほどのわずかなパフォーマンス低下が見られる可能性があります。 ユース ケースに応じて、複数のファイルに負荷を分散させるか、機能を無効にすることを検討してください。 詳細については、「[構成](#configuration)」セクションを参照してください。
- **シングルスレッド、複数ファイルまたは単一ファイル**:ほとんどのシングルスレッド ワークロードでは、並列処理がないため、パフォーマンス上の利点は最小限に抑えられます。SMB マルチチャネルが有効になっている場合、通常はパフォーマンス低下はわずかで 10% ほどです。 この場合は、SMB マルチチャネルを無効にすることをお勧めします。ただし、例外が 1 つあります。 シングルスレッドのワークロードで複数のファイルに負荷を分散し、平均的な大きな IO サイズ (約 16k 超) で使用する場合は、SMB マルチチャネルから得られるパフォーマンス上の利点はわずかです。

### <a name="performance-test-configuration"></a>パフォーマンス テストの構成

この記事のグラフで使用されている構成は、単一の標準 D32s v3 VM で、単一の RSS 対応 NIC を備え、チャネルは 4 つです。 負荷は、diskspd.exe を使用し、IO 深度 10 のマルチスレッドと、IO サイズが異なるランダム IO を使用して生成されました。

| サイズ | vCPU | メモリ:GiB | 一時ストレージ (SSD) GiB | 最大データ ディスク数 | キャッシュが有効な場合および一時ストレージの最大スループットIOPS/MBps (キャッシュ サイズは GiB 単位) | キャッシュが無効な場合の最大ディスク スループット: IOPS/MBps | 最大 NIC 数|必要なネットワーク帯域幅 (Mbps) |
|---|---|---|---|---|---|---|---|---|
| [Standard_D32s_v3](../../virtual-machines/dv3-dsv3-series.md) | 32 | 128 | 256 | 32 | 64000/512 (800)    | 51200/768  | 8|16000 |

:::image type="content" source="media/storage-files-smb-multichannel-performance/files-smb-multi-channel-nic-settings-all-nics.PNG" alt-text="パフォーマンス テストの構成を示すスクリーンショット。" lightbox="media/storage-files-smb-multichannel-performance/files-smb-multi-channel-nic-settings-all-nics.PNG":::

### <a name="mutli-threadedmultiple-files-with-smb-multichannel"></a>SMB マルチチャネルを使用した、マルチスレッド、複数ファイル

負荷は、IO サイズが異なる 10 個のファイルに対して生成されました。 スケールアップ テストの結果には、SMB マルチチャネルを有効にした IOPS とスループットのテスト結果の両方で大幅な改善が示されました。 それぞれの結果を以下の図に示します。

:::image type="content" source="media/storage-files-smb-multichannel-performance/diagram-smb-multi-channel-multiple-files-compared-to-single-channel-iops-performance.png" alt-text="パフォーマンスの図" lightbox="media/storage-files-smb-multichannel-performance/diagram-smb-multi-channel-multiple-files-compared-to-single-channel-iops-performance.png":::

:::image type="content" source="media/storage-files-smb-multichannel-performance/diagram-smb-multi-channel-multiple-files-compared-to-single-channel-throughput-performance.png" alt-text="スループットのパフォーマンスの図。" lightbox="media/storage-files-smb-multichannel-performance/diagram-smb-multi-channel-multiple-files-compared-to-single-channel-throughput-performance.png":::

- 単一の NIC で、読み取りの場合は 2 から 3 倍のパフォーマンス向上、書き込みの場合は IOPS とスループットの両方で 3 から 4 倍の向上が見られました。
- NIC が 1 つ、チャネルが 4 つという制限があっても、SMB マルチチャネルにより、IOPS とスループットが VM の制限に達することができました。
- エグレス (またはストレージへの読み取り) は測定されないため、読み取りスループットが、VM の公開上限である 16,000 Mbps (2 GiB/s) を超えることができました。 テストは 2.7 GiB/s 超を達成しました。 イングレス (またはストレージへの書き込み) は引き続き VM の制限が適用されます。
- 複数のファイルに負荷を分散することにより、大幅な改善が可能になりました。

このテストで使用されたコマンドの例を次に示します。 

`diskspd.exe -W300 -C5 -r -w100 -b4k -t8 -o8 -Sh -d60 -L -c2G -Z1G z:\write0.dat z:\write1.dat z:\write2.dat z:\write3.dat z:\write4.dat z:\write5.dat z:\write6.dat z:\write7.dat z:\write8.dat z:\write9.dat `.

### <a name="multi-threadedsingle-file-workloads-with-smb-multichannel"></a>SMB マルチチャネルを使用したマルチスレッド、単一ファイルのワークロード

この負荷は、128 GiB の単一ファイルに対して生成されました。 SMB マルチチャネルを有効にすると、マルチスレッド、単一ファイルを使用したスケールアップ テストのほとんどの場合に改善が見られました。 それぞれの結果を以下の図に示します。

:::image type="content" source="media/storage-files-smb-multichannel-performance/diagram-smb-multi-channel-single-file-compared-to-single-channel-iops-performance.png" alt-text="IOPS パフォーマンスの図。" lightbox="media/storage-files-smb-multichannel-performance/diagram-smb-multi-channel-single-file-compared-to-single-channel-iops-performance.png":::

:::image type="content" source="media/storage-files-smb-multichannel-performance/diagram-smb-multi-channel-single-file-compared-to-single-channel-throughput-performance.png" alt-text="単一ファイルのスループット パフォーマンスの図。" lightbox="media/storage-files-smb-multichannel-performance/diagram-smb-multi-channel-single-file-compared-to-single-channel-throughput-performance.png":::

- 平均的な IO サイズが大きい (約 16k 超) の単一の NIC では、読み取りと書き込みの両方が大幅に改善しました。
- IO サイズが小さい場合は、SMB マルチチャネルが有効になっていると、パフォーマンスに与える影響はわずかで 10% ほどでした。 これは、複数のファイルに負荷を分散するか、機能を無効にすることで軽減できます。
- パフォーマンスは、[単一ファイルの制限](storage-files-scale-targets.md#file-scale-targets)に引き続き縛られます。

## <a name="optimizing-performance"></a>パフォーマンスの最適化

次のヒントを参考にして、パフォーマンスを最適化することができます。

- ストレージ アカウントとクライアントが同じ Azure リージョンに確実に共存しているようにして、ネットワーク待ち時間を短縮します。
- マルチスレッド アプリケーションを使用し、複数のファイルに負荷を分散します。
- SMB マルチチャネルのパフォーマンス上の利点は、負荷を分散するファイルの数と共に増えます。
- Premium 共有のパフォーマンスは、プロビジョニングされた共有サイズ (IOPS、エグレス、イングレス) と単一ファイルの制限に縛られます。 詳細については、「[Premium ファイル共有のプロビジョニングについて](understanding-billing.md#provisioned-model)」を参照してください。
- 1 つの VM クライアントの最大パフォーマンスは VM の制限に引き続き縛られます。 たとえば [Standard_D32s_v3](../../virtual-machines/dv3-dsv3-series.md) で最大帯域幅 16,000 MBps (または 2 GBps) をサポートでき、VM からのエグレス (ストレージへの書き込み) は測定され、イングレス (ストレージからの読み取り) は測定されません。 ファイル共有のパフォーマンスは、マシン ネットワーク制限、CPU、内部ストレージの使用可能なネットワーク帯域幅、IO サイズ、並列処理、その他の要因の影響を受けます。
- 初期テストは通常、ウォームアップであり、その結果は破棄して、テストを繰り返します。
- パフォーマンスが 1 つのクライアントによって制限されていて、ワークロードがプロビジョニングされた共有制限をまだ下回っている場合は、複数のクライアントに負荷を分散することでパフォーマンスを向上させることができます。

### <a name="the-relationship-between-iops-throughput-and-io-sizes"></a>IOPS、スループット、IO サイズの関係

**スループット = IO サイズ * IOPS**

IO サイズが大きくなるとスループットが向上して、待ち時間が多くなり、その結果、正味の IOPS 数が少なくなります。 IO サイズが小さくなると IOPS が向上しますが、その結果、正味のスループットと待ち時間は少なくなります。

## <a name="next-steps"></a>次のステップ

- [FileStorage アカウントで SMB マルチチャネルを有効にする (プレビュー)](storage-files-enable-smb-multichannel.md)
- SMB マルチチャネルの詳細については、[Windows のドキュメント](/azure-stack/hci/manage/manage-smb-multichannel)を参照してください。
