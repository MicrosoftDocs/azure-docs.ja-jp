---
title: オペレーターのベスト プラクティス - Azure Kubernetes Services (AKS) のストレージ
description: Azure Kubernetes Service (AKS) のストレージ、データの暗号化、およびバックアップに関するクラスター オペレーターのベスト プラクティスについて説明します
services: container-service
author: mlearned
ms.service: container-service
ms.topic: conceptual
ms.date: 5/6/2019
ms.author: mlearned
ms.openlocfilehash: b42cdae634a6c2d8d994225d4cb6b440a99918e5
ms.sourcegitcommit: 6a42dd4b746f3e6de69f7ad0107cc7ad654e39ae
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/07/2019
ms.locfileid: "67614592"
---
# <a name="best-practices-for-storage-and-backups-in-azure-kubernetes-service-aks"></a>Azure Kubernetes Services (AKS) のストレージとバックアップに関するベスト プラクティス

Azure Kubernetes Service (AKS) でクラスターを作成して管理する際に、多くの場合、アプリケーションにはストレージが必要になります。 アプリケーションに適切なストレージを提供できるように、パフォーマンスのニーズとポッドのアクセス方法を理解することが重要です。 AKS ノードのサイズが、これらのストレージの選択に影響する可能性があります。 接続されたストレージをバックアップし、その復元プロセスをテストする方法を計画する必要もあります。

このベスト プラクティスの記事では、クラスター オペレーターを対象としたストレージに関する考慮事項に重点を置いています。 この記事では、次のことについて説明します。

> [!div class="checklist"]
> * 利用可能なストレージの種類
> * ストレージ パフォーマンスのために AKS ノードのサイズを適切に設定する方法
> * ボリュームの動的なプロビジョニングと静的なプロビジョニングの違い
> * データ ボリュームをバックアップしてセキュリティで保護する方法

## <a name="choose-the-appropriate-storage-type"></a>適切なストレージの種類を選択する

**ベスト プラクティス ガイダンス** - 適切なストレージを選択するためにアプリケーションのニーズを理解します。 運用環境のワークロードには、高パフォーマンスの SSD を基盤とするストレージを使用します。 複数のコンカレント接続が必要な場合は、ネットワークベースのストレージを計画します。

多くの場合、アプリケーションではさまざまな種類と速度のストレージが必要です。 ご利用のアプリケーションでは、個々のポッドに接続するストレージが必要ですか、それとも複数のポッドで共有されるストレージが必要ですか。 ストレージは、データへの読み取り専用アクセスのためのものですか、それとも大量の構造化データを書き込むためのものですか。 これらのストレージでは、使用するストレージの最も適した種類を決定する必要があります。

次の表に、使用可能なストレージの種類とその機能の概要を示します。

| ユース ケース | ボリューム プラグイン | 読み取り/書き込み (1 回のみ) | 読み取り専用 (複数回) | 読み取り/書き込み (複数回) | Windows Server コンテナーのサポート |
|----------|---------------|-----------------|----------------|-----------------|--------------------|
| 共有構成       | Azure Files   | はい | はい | はい | はい |
| 構造化されたアプリ データ        | Azure ディスク   | はい | いいえ  | いいえ  | はい |
| 非構造化データ、ファイル システム操作 | [BlobFuse (プレビュー)][blobfuse] | はい | はい | はい | いいえ |

AKS のボリュームに対して提供される、2 つの主な種類のストレージが、Azure ディスクまたは Azure ファイルでサポートされます。 セキュリティを向上させるため、両方の種類のストレージでは既定で Azure Storage Service Encryption (SSE) が使用され、保存データが暗号化されます。 ディスクは現在、AKS ノード レベルで Azure Disk Encryption を使用して暗号化することはできません。

Azure ファイルは現在、Standard パフォーマンス レベルで使用可能です。 Azure ディスクは、以下の Standard と Premium のパフォーマンス レベルで使用できます。

- *Premium*。このディスクは、高パフォーマンスのソリッドステート ディスク (SSD) でサポートされます。 すべての運用環境のワークロードに Premium ディスクをお勧めします。
- *Standard*。このディスクは標準のスピニング ディスク (HDD) でサポートされ、アーカイブまたはアクセス頻度の少ないデータに適しています。

アプリケーション パフォーマンスのニーズとアクセス パターンを理解して、適切なストレージ レベルを選択してください。 マネージド ディスクのサイズとパフォーマンス レベルの詳細については、[Azure マネージド ディスクの概要][managed-disks]に関するページを参照してください

### <a name="create-and-use-storage-classes-to-define-application-needs"></a>アプリケーションのニーズを定義するためにストレージ クラスを作成して使用する

使用するストレージの種類は、Kubernetes の*ストレージ クラス*を使って定義します。 その後、ストレージ クラスはポッドまたはデプロイの仕様で参照されます。 これらの定義を組み合わせて適切なストレージを作成し、それをポッドに接続します。 詳細については、[AKS のストレージ クラス][aks-concepts-storage-classes]に関するページを参照してください。

## <a name="size-the-nodes-for-storage-needs"></a>ストレージのニーズに合わせてノードのサイズを設定する

**ベスト プラクティス ガイダンス** - 各ノード サイズでは、最大数のディスクがサポートされます。 また、さまざまなノード サイズで、さまざまな量のローカル ストレージやネットワーク帯域幅が提供されます。 適切なサイズのノードをデプロイするために、アプリケーション要求について計画します。

AKS ノードは Azure VM として実行されます。 さまざまな種類とサイズの VM を使用できます。 各 VM サイズでは、CPU やメモリなど、異なる量のコア リソースが提供されます。 これらの VM サイズには、接続できる最大数のディスクがあります。 ストレージのパフォーマンスは、最大のローカルおよび接続されるディスクの IOPS (1 秒あたりの入出力操作) に対する VM サイズによっても異なります。

ご利用のアプリケーションでストレージ ソリューションとして Azure ディスクが必要な場合は、適切なノードの VM サイズを計画して選択します。 VM サイズを選ぶ場合の要因は、CPU とメモリの量だけではありません。 ストレージの機能も重要です。 たとえば、*Standard_B2ms* と *Standard_DS2_v2* の両方の VM サイズには、同じような量の CPU とメモリ リソースが含まれます。 次の表に示すように、その潜在的なストレージのパフォーマンスは異なります。

| ノードの種類とサイズ | vCPU | メモリ (GiB) | 最大データ ディスク数 | キャッシュされていないディスクの最大 IOPS | キャッシュされていない場合のスループット (MBps) |
|--------------------|------|--------------|----------------|------------------------|--------------------------------|
| Standard_B2ms      | 2    | 8            | 4              | 1,920                  | 22.5                           |
| Standard_DS2_v2    | 2    | 7            | 8              | 6,400                  | 96                             |

ここでは、*Standard_DS2_v2* で、2 倍の数の接続ディスクが許可され、3 倍から 4 倍の量の IOPS とディスク スループットが提供されます。 コア コンピューティング リソースだけを見て、コストを比較した場合、*Standard_B2ms* VM サイズを選択する可能性があります。その場合、ストレージのパフォーマンスは低く、制限されます。 アプリケーション開発チームと協力し、ストレージ容量とパフォーマンスのニーズを理解してください。 パフォーマンスのニーズを満たすため、あるいはニーズを上回るように、AKS ノードに適した VM サイズを選択します。 定期的にアプリケーションのベースラインを設定し、必要に応じて、VM サイズを調整します。

使用可能な VM サイズの詳細については、「[Azure の Linux 仮想マシンのサイズ][vm-sizes]」を参照してください。

## <a name="dynamically-provision-volumes"></a>ボリュームを動的にプロビジョニングする

**ベスト プラクティス ガイダンス** - 管理オーバーヘッドを減らし、スケーリングできるようにするために、永続ボリュームを静的に作成して割り当てないでください。 動的プロビジョニングを使用します。 ストレージ クラスでは、ポッドが削除された後、不要なストレージ コストを最小限に抑えるために適切な解放ポリシーを定義します。

ポッドにストレージを接続する必要がある場合は、永続ボリュームを使用します。 これらの永続ボリュームは、手動でまたは動的に作成できます。 永続ボリュームを手動で作成する場合、管理オーバーヘッドが増え、スケーリング機能が制限されます。 永続ボリュームの動的プロビジョニングを使用すると、ストレージの管理が簡略化され、必要に応じてアプリケーションを拡張し、スケーリングすることができます。

![Azure Kubernetes Services (AKS) クラスターでの永続ボリューム要求](media/concepts-storage/persistent-volume-claims.png)

永続ボリューム要求 (PVC) を使用すれば、必要に応じて、ストレージを動的に作成することができます。 ポッドで要求されたときに、基になる Azure ディスクが生成されます。 ポッド定義で、ボリュームを作成し、設計されたマウント パスに接続するよう要求します

ボリュームを動的に作成して使用する方法の概念については、「[永続ボリューム要求][aks-concepts-storage-pvcs]」を参照してください。

これらのボリュームの動作を確認する場合は、[Azure ディスク][dynamic-disks] or [Azure Files][dynamic-files]を含む永続ボリュームを動的に作成して使用する方法を参照してください。

ストレージ クラス定義の一部として、適切な *reclaimPolicy* を設定します。 この reclaimPolicy が、ポッドが削除されて永続ボリュームが不要になる可能性がある場合の、基礎となる Azure Storage リソースの動作を制御します。 基礎となるストレージ リソースは削除することも、将来のポッドで使用するために保持しておくこともできます。 reclaimPolicy は、*retain* または *delete* に設定できます。 アプリケーションのニーズを理解し、使用および課金される未使用ストレージの量を最小限に抑えるために保持されるストレージの定期チェックを実装します。

ストレージ クラス オプションの詳細については、[ストレージの解放ポリシー][reclaim-policy]に関するページを参照してください。

## <a name="secure-and-back-up-your-data"></a>データをセキュリティで保護してバックアップする

**ベスト プラクティス ガイダンス** - Velero や Azure Site Recovery など、ご利用のストレージの種類に適したツールを使って、データをバックアップします。 それらのバックアップの整合性とセキュリティを確認します。

アプリケーションでディスク上またはファイル内に保持されているデータを格納して使用する場合、定期的にそのデータのバックアップまたはスナップショットを作成する必要があります。 Azure ディスクでは、組み込みのスナップショット テクノロジを使用できます。 スナップショット操作を実行する前に、アプリケーションでディスクにフラッシュ書き込みを行うためのフックが必要になる場合があります。 [Velero][velero] can back up persistent volumes along with additional cluster resources and configurations. If you can't [remove state from your applications][remove-state]では、永続ボリュームからデータをバックアップし、復元操作を定期的にテストしてデータの整合性と必要なプロセスが確認されます。

データ バックアップのさまざまな方法の制限事項、またスナップショットを作成する前にデータを静止する必要がある場合の制限事項を理解します。 データ バックアップで、クラスター デプロイのアプリケーション環境を必ずしも復元できるとは限りません。 このようなシナリオの詳細については、「[Azure Kubernetes Service (AKS) での事業継続とディザスター リカバリーに関するベスト プラクティス][best-practices-multi-region]」を参照してください。

## <a name="next-steps"></a>次の手順

この記事では、AKS のストレージのベスト プラクティスに重点を置きました。 Kubernetes のストレージの基本の詳細については、[AKS におけるアプリケーションのストレージの概念][aks-concepts-storage]に関するページを参照してください。

<!-- LINKS - External -->
[velero]: https://github.com/heptio/velero
[blobfuse]: https://github.com/Azure/azure-storage-fuse

<!-- LINKS - Internal -->
[aks-concepts-storage]: concepts-storage.md
[vm-sizes]: ../virtual-machines/linux/sizes.md
[dynamic-disks]: azure-disks-dynamic-pv.md
[dynamic-files]: azure-files-dynamic-pv.md
[reclaim-policy]: concepts-storage.md#storage-classes
[aks-concepts-storage-pvcs]: concepts-storage.md#persistent-volume-claims
[aks-concepts-storage-classes]: concepts-storage.md#storage-classes
[managed-disks]: ../virtual-machines/linux/managed-disks-overview.md
[best-practices-multi-region]: operator-best-practices-multi-region.md
[remove-state]: operator-best-practices-multi-region.md#remove-service-state-from-inside-containers
