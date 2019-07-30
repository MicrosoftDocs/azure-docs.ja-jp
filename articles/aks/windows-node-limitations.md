---
title: Azure Kubernetes Service (AKS) での Windows Server ノード プールの制限事項
description: Windows Server ノード プールとアプリケーション ワークロードを Azure Kubernetes Service (AKS) 内で実行するときの既知の制限事項について説明します
services: container-service
author: mlearned
ms.service: container-service
ms.topic: article
ms.date: 05/31/2019
ms.author: mlearned
ms.openlocfilehash: 0d79b4d76249bd4a79f8adbd5df0cfa1ae133760
ms.sourcegitcommit: 6a42dd4b746f3e6de69f7ad0107cc7ad654e39ae
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/07/2019
ms.locfileid: "67613731"
---
# <a name="current-limitations-for-windows-server-node-pools-and-application-workloads-in-azure-kubernetes-service-aks"></a>Azure Kubernetes Service (AKS) での Windows Server ノード プールとアプリケーション ワークロードについての現在の制限事項

Azure Kubernetes Service (AKS) では、Windows Server をゲスト OS としてノード上で実行するノード プールを作成することができます。 これらのノードは、.NET Framework 上に構築されたものなど、ネイティブの Windows コンテナー アプリケーションを実行できます。 Linux と Windows OS がコンテナー サポートを提供する方法に大きな相違点があるため、いくつかの一般的な Kubernetes と Pod 関連の機能は Windows ノード プールで現在使用できません。

この記事では、AKS 内の Windows Server ノードの制限事項および OS の概念について説明します。 Windows Server 用のノード プールは現在プレビューの段階です。

> [!IMPORTANT]
> AKS のプレビュー機能は、セルフサービスのオプトインです。 これらは、コミュニティからフィードバックやバグを収集するために提供されています。 これらの機能はプレビュー段階であり、運用環境での使用を意図していません。 パブリック プレビュー段階の機能は、"ベスト エフォート" のサポートに該当します。 AKS テクニカル サポート チームによるサポートは、太平洋タイム ゾーン (PST) での営業時間内のみで利用できます。 詳細については、次のサポートに関する記事を参照してください。
>
> * [AKS のサポート ポリシー][aks-support-policies]
> * [Azure サポートに関する FAQ][aks-faq]

## <a name="limitations-for-windows-server-in-kubernetes"></a>Kubernetes での Windows Server の制限事項

Windows Server コンテナーは、Windows ベースのコンテナー ホスト上で実行する必要があります。 AKS で Windows Server コンテナーを実行するには、ゲスト OS として [Windows Server を実行するノード プールを作成][windows-node-cli]します。 Window Server ノード プールのサポートには、Kubernetes プロジェクトの上流 Windows Server の一部であるいくつかの制限が含まれます。 これらの制限は、AKS 固有ではありません。 Kubernetes での Windows Server 向けのこのアップストリーム サポートについて詳しくは、[Kubernetes での Windows Server コンテナーの制限事項](https://docs.microsoft.com/azure/aks/windows-node-limitations)に関するページを参照してください。

Kubernetes での Windows Server コンテナーに対する次のアップストリーム制限は、AKS に関連しています。

- Windows Server コンテナーは、基になる Windows Server ノード OS に対応する Windows Server 2019 のみ使用できます。
    - 基本 OS として Windows Server 2016 を使用して構築されたコンテナー イメージはサポートされません。
- 特権を持つコンテナーは、使用できません。
- RunAsUser、SELinux、AppArmor、または POSIX 機能などの Linux 固有の機能は、Windows Server コンテナーでは使用できません。
    - UUI/GUID やユーザーごとの権限などの Linux に固有のファイル システム制限も、Windows Server コンテナーでは使用できません。
- Azure Disk と Azure Files はサポートされているボリュームの種類であり、Windows Server コンテナー内で NTFS ボリュームとしてアクセスされます。
    - NFS ベースの記憶域/ボリュームはサポートされません。

## <a name="aks-limitations-for-windows-server-node-pools"></a>Windows Server ノード プールに対する AKS の制限事項

次の追加の制限事項は AKS の Windows Server ノード プールのサポートに該当します。

- AKS クラスターには、Linux ノード プールが最初のノード プールとして常に含まれています。 この最初の Linux ベースのノード プールは、AKS クラスター自体を削除しない限り削除できません。
- AKS クラスターは、Azure CNI (高度) ネットワーク モデルを使用する必要があります。
    - Kubenet (基本) ネットワークはサポートされていません。 Kubenet を使用する AKS クラスターを作成することはできません。 ネットワーク モデルの違いの詳細については、[AKS のアプリケーションにおけるネットワークの概念][azure-network-models]に関する記事を参照してください。
    - Azure CNI ネットワーク モデルでは、IP アドレス管理に関する追加の計画と考慮事項が必要です。 Azure CNI を計画して実装する方法の詳細については、[AKS での Azure CNI ネットワークの構成][configure-azure-cni]に関するページを参照してください。
- AKS の Windows サーバー ノードは、最新の修正プログラムと更新プログラムを維持するために、最新の Windows Server 2019 リリースに*アップグレード*する必要があります。 Windows Update は AKS の基本ノード イメージでは有効になっていません。 Windows Update のリリース サイクルと独自の検証プロセス周辺の定期的スケジュールで、AKS クラスター内の Windows Server ノード プールでアップグレードを実行する必要があります。 Windows Server ノード プールのアップグレードの詳細については、[AKS でのノード プールのアップグレード][nodepool-upgrade]に関するページを参照してください。
    - これらの Windows Server ノードのアップグレードでは、古いノードが削除されるまで、新しいノードがデプロイされるときに仮想ネットワーク サブネットの追加の IP アドレスを一時的に消費します。
    - 新しいノードをデプロイするときに、サブスクリプション内で vCPU クォータも一時的に消費され、その後で古いノードが削除されます。
    - AKS 内の Linux ノードのように、`kured` を使用して自動的に更新したり再起動を管理したりすることはできません。
- AKS クラスターは、最大で 8 つノード プールを持つことができます。
    - この 8 つのノード プール全体で最大 400 のノードを持つことができます。
- Windows Server ノード プール名は 6 文字に制限されています。
- ネットワーク ポリシーやクラスター オートスケーラーなどの AKS のプレビュー機能は Windows Server ノードに対して動作が保証されていません。
- イングレス コントローラーは、NodeSelector を使用して Linux ノード上でのみスケジュールする必要があります。
- Azure Dev Spaces は現在、Linux ベースのノード プールに対してのみ使用できます。
- Windows サーバー ノードが Active Directory ドメインに参加していない場合のグループ管理サービス アカウント (gMSA) のサポートは、現在、AKS では使用できません。
    - この機能を使用する必要がある場合は、オープン ソースのアップストリーム [aks-engine][aks-engine] プロジェクトで、現在、gMSA サポートが提供されています。

## <a name="os-concepts-that-are-different"></a>異なる OS の概念

Kubernetes は従来より Linux が中心となっています。 アップストリームの [Kubernetes.io][kubernetes] Web サイトで使用されている多くの例は、Linux ノードでの使用を意図したものです。 Windows Server コンテナーを使用したデプロイを作成する場合、OS レベルの次の考慮事項が該当します。

- **ID** -Linux ではユーザー ID (UID) とグループ ID (GID) が使用され、整数型として表されます。 ユーザー名とグループ名は正規名ではなく、これらは UID と GID に由来する */etc/groups* または */etc/passwd* 内の別名にすぎません。
    - Windows Server では、Windows Security Access Manager (SAM) データベースに格納されている大きいバイナリ セキュリティ識別子 (SID) を使用します。 このデータベースは、ホストとコンテナー、またはコンテナー間で共有されません。
- **ファイルのアクセス許可** - Windows Server では、アクセス許可および UID と GID のビットマスクではなく、SID に基づくアクセス制御リストを使用します
- **ファイル パス** - Windows Server の規則では、/ の代わりに \ が使用されます。
    - ボリュームをマウントするポッド仕様では、Windows Server コンテナー用にパスを正しく指定してください。 たとえば、Linux コンテナーの */mnt/volume* というマウント ポイントではなく、*K:* ドライブとしてマウントするために、 */K/Volume* のようにドライブ文字と場所を指定してください。

## <a name="next-steps"></a>次の手順

AKS で Windows Server コンテナーの使用を開始するには、[AKS で Windows Server を実行するノード プールを作成][windows-node-cli]します。

<!-- LINKS - external -->
[upstream-limitations]: https://kubernetes.io/docs/setup/windows/#limitations
[kubernetes]: https://kubernetes.io
[aks-engine]: https://github.com/azure/aks-engine

<!-- LINKS - internal -->
[azure-network-models]: concepts-network.md#azure-virtual-networks
[configure-azure-cni]: configure-azure-cni.md
[nodepool-upgrade]: use-multiple-node-pools.md#upgrade-a-node-pool
[windows-node-cli]: windows-container-cli.md
[aks-support-policies]: support-policies.md
[aks-faq]: faq.md
[azure-outbound-traffic]: ../load-balancer/load-balancer-outbound-connections.md#defaultsnat
