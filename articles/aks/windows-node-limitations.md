---
title: Azure Kubernetes Service (AKS) での Windows Server ノード プールの制限事項
description: Windows Server ノード プールとアプリケーション ワークロードを Azure Kubernetes Service (AKS) 内で実行するときの既知の制限事項について説明します
services: container-service
author: mlearned
ms.service: container-service
ms.topic: article
ms.date: 05/31/2019
ms.author: mlearned
ms.openlocfilehash: a0fe65428a3329d4843ec913e934fb7a91b13759
ms.sourcegitcommit: e97a0b4ffcb529691942fc75e7de919bc02b06ff
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/15/2019
ms.locfileid: "71000217"
---
# <a name="current-limitations-for-windows-server-node-pools-and-application-workloads-in-azure-kubernetes-service-aks"></a>Azure Kubernetes Service (AKS) での Windows Server ノード プールとアプリケーション ワークロードについての現在の制限事項

Azure Kubernetes Service (AKS) では、Windows Server をゲスト OS としてノード上で実行するノード プールを作成することができます。 これらのノードは、.NET Framework 上に構築されたものなど、ネイティブの Windows コンテナー アプリケーションを実行できます。 Linux と Windows OS がコンテナー サポートを提供する方法に大きな相違点があるため、いくつかの一般的な Kubernetes と Pod 関連の機能は Windows ノード プールで現在使用できません。

この記事では、AKS 内の Windows Server ノードの制限事項および OS の概念について説明します。 Windows Server 用のノード プールは現在プレビューの段階です。

> [!IMPORTANT]
> AKS のプレビュー機能は、セルフサービスのオプトインです。 プレビューは、"現状有姿のまま" および "利用可能な限度" で提供され、サービス レベル契約および限定保証から除外されるものとします。 AKS プレビューは、カスタマー サポートによってベスト エフォートで部分的にカバーされます。 そのため、これらの機能は、運用環境での使用を意図していません。 詳細については、次のサポートに関する記事を参照してください。
>
> * [AKS のサポート ポリシー][aks-support-policies]
> * [Azure サポートに関する FAQ][aks-faq]

## <a name="which-windows-operating-systems-are-supported"></a>どの Windows オペレーティング システムがサポートされていますか?

AKS では、ホスト OS のバージョンとして Windows Server 2019 が使用され、プロセス分離のみがサポートされます。 他のバージョンの Windows Server を使用してビルドされたコンテナー イメージはサポートされていません。 「[Windows コンテナー バージョンの互換性][windows-container-compat]」を参照してください。

## <a name="is-kubernetes-different-on-windows-and-linux"></a>Windows と Linux では Kubernetes に違いはありますか?

Window Server ノード プールのサポートには、Kubernetes プロジェクトの上流 Windows Server の一部であるいくつかの制限が含まれます。 これらの制限は、AKS 固有ではありません。 Kubernetes での Windows Server のこのアップストリーム サポートの詳細については、Kubernetes プロジェクトの「[Kubernetes での Windows のサポートの概要][intro-windows]」ドキュメントの「[サポートされている機能と制限事項][upstream-limitations]」を参照してください。

Kubernetes は従来より Linux が中心となっています。 アップストリームの [Kubernetes.io][kubernetes] Web サイトで使用されている多くの例は、Linux ノードでの使用を意図したものです。 Windows Server コンテナーを使用したデプロイを作成する場合、OS レベルの次の考慮事項が該当します。

- **ID** - Linux では、整数のユーザー識別子 (UID) によってユーザーが識別されます。 ユーザーは、ログオンに使用する英数字のユーザー名も持っています。これは、Linux によって、ユーザーの UID に変換されます。 同様に、Linux では、整数のグループ識別子 (GID) によってユーザー グループが識別され、グループ名が対応する GID に変換されます。
    - Windows Server では、Windows Security Access Manager (SAM) データベースに格納されている大きいバイナリ セキュリティ識別子 (SID) を使用します。 このデータベースは、ホストとコンテナー、またはコンテナー間で共有されません。
- **ファイルのアクセス許可** - Windows Server では、アクセス許可および UID と GID のビットマスクではなく、SID に基づくアクセス制御リストを使用します
- **ファイル パス** - Windows Server の規則では、/ の代わりに \ が使用されます。
    - ボリュームをマウントするポッド仕様では、Windows Server コンテナー用にパスを正しく指定してください。 たとえば、Linux コンテナーの */mnt/volume* というマウント ポイントではなく、*K:* ドライブとしてマウントするために、 */K/Volume* のようにドライブ文字と場所を指定してください。

## <a name="what-kind-of-disks-are-supported-for-windows"></a>Windows ではどのような種類のディスクがサポートされていますか?

Azure Disk と Azure Files はサポートされているボリュームの種類であり、Windows Server コンテナー内で NTFS ボリュームとしてアクセスされます。

## <a name="can-i-run-windows-only-clusters-in-aks"></a>Windows のみのクラスターを AKS で実行できますか?

AKS クラスターのマスター ノード (コントロール プレーン) は、AKS サービスによってホストされています。マスター コンポーネントがホストされているノードのオペレーティング システムはユーザーには公開されていません。 すべての AKS クラスターは既定の最初のノード プールで作成され、これは Linux ベースです。 このノード プールには、クラスターが機能するために必要なシステム サービスが含まれています。 クラスターの信頼性を確保し、クラスター操作を実行できるようにするため、最初のノード プールで少なくとも 2 つのノードを実行することをお勧めします。 最初の Linux ベースのノード プールは、AKS クラスター自体を削除しない限り削除できません。

## <a name="what-network-plug-ins-are-supported"></a>どのネットワーク プラグインがサポートされていますか?

Windows ノード プールの AKS クラスターでは、Azure CNI (高度) ネットワーク モデルを使用する必要があります。 Kubenet (基本) ネットワークはサポートされていません。 ネットワーク モデルの違いの詳細については、[AKS のアプリケーションにおけるネットワークの概念][azure-network-models]に関する記事を参照してください。 - Azure CNI ネットワーク モデルでは、IP アドレス管理に関する追加の計画と考慮事項が必要です。 Azure CNI を計画して実装する方法の詳細については、[AKS での Azure CNI ネットワークの構成][configure-azure-cni]に関するページを参照してください。

## <a name="can-i-change-the-min--of-pods-per-node"></a>ノードあたりのポッドの最小数を変更できますか?

現時点では、クラスターの信頼性を確保するため、最小値を 30 ポッドに設定する必要があります。

## <a name="how-do-patch-my-windows-nodes"></a>Windows ノードに修正プログラムを適用するにはどうすればいいですか?

最新の修正プログラムと更新プログラムを取得するには、AKS の Windows Server ノードを "*アップグレード*" する必要があります。 AKS のノードでは Windows Update は有効になっていません。 AKS では、修正プログラムが利用可能になるとすぐに新しいノード プール イメージがリリースされます。更新プログラムや修正プログラムを最新の状態に維持するために、ノード プールをアップグレードするのは、お客様の責任です。 これは、使用されている Kubernetes のバージョンにも当てはまります。 新しいバージョンが利用可能になると、AKS のリリース ノートで示されます。 Windows Server ノード プールのアップグレードの詳細については、[AKS でのノード プールのアップグレード][nodepool-upgrade]に関するページを参照してください。

> [!NOTE]
> 更新された Windows Server イメージは、ノード プールをアップグレードする前にクラスターのアップグレード (コントロール プレーンのアップグレード) が実行された場合にのみ使用されます。
>

## <a name="how-many-node-pools-can-i-create"></a>ノード プールはいくつ作成できますか?

AKS クラスターでは、最大で 8 つのノード プールを作成できます。 それらのノード プール全体で最大 400 個のノードを使用できます。 [ノード プールの制限][nodepool-limitations]に関するページを参照してください。

## <a name="what-can-i-name-my-windows-node-pools"></a>Windows ノード プールにはどのような名前を指定できますか?

名前は 6 文字以下にする必要があります。 これは AKS の現在の制限です。

## <a name="are-all-features-supported-with-windows-nodes"></a>Windows ノードではすべての機能がサポートされていますか?

現在、Windows ノードでは、ネットワーク ポリシーと kubernet はサポートされていません。 

## <a name="can-i-run-ingress-controllers-on-windows-nodes"></a>Windows ノードでイングレス コントローラーを実行できますか?

はい。Windows Server コンテナーがサポートされているイングレス コントローラーは、AKS の Windows ノードで実行できます。

## <a name="can-i-use-azure-dev-spaces-with-windows-nodes"></a>Windows ノードで Azure Dev Spaces を使用できますか?

Azure Dev Spaces は現在、Linux ベースのノード プールに対してのみ使用できます。

## <a name="can-my-windows-server-containers-use-gmsa"></a>Windows Server コンテナーで gMSA を使用できますか?

グループの管理されたサービス アカウント (gMSA) のサポートは、現在 AKS では使用できません。

## <a name="what-if-i-need-a-feature-which-is-not-supported"></a>サポートされていない機能が必要な場合はどうすればよいですか?

AKS での Windows に必要なすべての機能を組み込む作業が行われていますが、ギャップに気付かれた場合は、オープンソースのアップストリーム [aks-engine][aks-engine] プロジェクトで、Azure で Kubernetes を実行する簡単で完全にカスタマイズ可能な方法が提供されており、Windows のサポートが含まれます。 [AKS のロードマップ][aks-roadmap]に関するページで今後の機能のロードマップを確認してください。

## <a name="next-steps"></a>次の手順

AKS で Windows Server コンテナーの使用を開始するには、[AKS で Windows Server を実行するノード プールを作成][windows-node-cli]します。

<!-- LINKS - external -->
[kubernetes]: https://kubernetes.io
[aks-engine]: https://github.com/azure/aks-engine
[upstream-limitations]: https://kubernetes.io/docs/setup/production-environment/windows/intro-windows-in-kubernetes/#supported-functionality-and-limitations
[intro-windows]: https://kubernetes.io/docs/setup/production-environment/windows/intro-windows-in-kubernetes/
[aks-roadmap]: https://github.com/Azure/AKS/projects/1

<!-- LINKS - internal -->
[azure-network-models]: concepts-network.md#azure-virtual-networks
[configure-azure-cni]: configure-azure-cni.md
[nodepool-upgrade]: use-multiple-node-pools.md#upgrade-a-node-pool
[windows-node-cli]: windows-container-cli.md
[aks-support-policies]: support-policies.md
[aks-faq]: faq.md
[azure-outbound-traffic]: ../load-balancer/load-balancer-outbound-connections.md#defaultsnat
[nodepool-limitations]: use-multiple-node-pools.md#limitations
[preview-support]: support-policies.md#preview-features-or-feature-flags
[windows-container-compat]: https://docs.microsoft.com/virtualization/windowscontainers/deploy-containers/version-compatibility#windows-server-2019-host-os-compatibility
