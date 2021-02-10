---
title: Azure Automanage for virtual machines
description: Azure Automanage for virtual machines について説明します。
author: deanwe
ms.service: virtual-machines
ms.subservice: automanage
ms.workload: infrastructure
ms.topic: conceptual
ms.date: 09/04/2020
ms.author: deanwe
ms.custom: references_regions
ms.openlocfilehash: 0d8ce501b951f3543e1baf54c8a52648b13f6e66
ms.sourcegitcommit: 77afc94755db65a3ec107640069067172f55da67
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 01/22/2021
ms.locfileid: "98695672"
---
# <a name="azure-automanage-for-virtual-machines"></a>Azure Automanage for virtual machines

この記事では、Azure Automanage for virtual machines について説明します。これには次のような利点があります。

- 仮想マシンをインテリジェントにオンボードして、ベスト プラクティスの Azure サービスを選択する
- Azure のベスト プラクティスに従って、各サービスを自動的に構成する
- ドリフトを監視し、検出されたら修正する
- 簡単なエクスペリエンス (ポイント、クリック、設定、消去) を提供する


## <a name="overview"></a>概要

Azure Automanage for virtual machines サービスを使用すると、Azure で仮想マシンに役に立つ特定のサービスを検出し、オンボード方法を知り、構成方法を知る必要がなくなります。 これらのサービスは、仮想マシンの信頼性、セキュリティ、管理を強化するためのものであり、Azure のベスト プラクティス サービスと見なされます ([Azure Update Management](../automation/update-management/overview.md)、[Azure Backup](../backup/backup-overview.md) など多数)。

Azure Automanage に仮想マシンをオンボードすると、各ベスト プラクティス サービスが推奨される設定に自動的に構成されます。 ベスト プラクティスは、サービスごとに異なります。 たとえば、Azure Backup の場合、仮想マシンを 1 日に 1 回バックアップして 6 か月保持する、といったベスト プラクティスが考えられます。

また、Azure Automanage を使用すると、ドリフトが自動的に監視され、検出されると修正されます。 つまり、仮想マシンが Azure Automanage にオンボードされた場合、Azure のベスト プラクティスに従って自動的に構成されるだけでなく、ライフサイクル全体を通してそれらのベスト プラクティスに準拠し続けるように、マシンが自動的に監視されます。 仮想マシンがそれらのプラクティスからドリフトつまり逸脱した場合は、修正が行われ、マシンは望ましい状態に戻されます。

最後に、エクスペリエンスは非常に簡素です。


## <a name="prerequisites"></a>前提条件

仮想マシンで Azure Automanage を有効にする前に、いくつかの前提条件を考慮する必要があります。

- Windows Server VM のみ
- VM が実行されている必要があります
- VM はサポートされているリージョン内に存在する必要があります (次の段落を参照)
- ユーザーは正しいアクセス許可を持っている必要があります (次の段落を参照)
- 現時点では、サンドボックス サブスクリプションは Automanage ではサポートされていません

また、Automanage では次のリージョンにある Windows VM のみがサポートされることにも注意してください: 西ヨーロッパ、米国東部、米国西部 2、カナダ中部、米国中西部、東日本。

既存の Automanage アカウントを使用して VM で Automanage を有効にするには、VM を含むリソース グループの **共同作成者** ロールが必要です。 新しい Automanage アカウントで Automanage を有効にする場合は、サブスクリプションに対する次のアクセス許可が必要です。**所有者** ロール、または **共同作成者** ロールと **ユーザー アクセス管理者** ロールの併用。

> [!NOTE]
> 別のサブスクリプションのワークスペースに接続されている VM で Automanage を使用する場合は、サブスクリプションごとに上記に記載されているアクセス許可を持っている必要があります。

## <a name="participating-services"></a>対象となるサービス

:::image type="content" source="media\automanage-virtual-machines\intelligently-onboard-services.png" alt-text="サービスをインテリジェントにオンボードする。":::

対象となる Azure サービスの完全な一覧と、サポートされている構成プロファイルについては、[Azure Automanage for virtual machines のベスト プラクティス](virtual-machines-best-practices.md)に関するページを参照してください。

 これらの対象サービスは、自動的にオンボードされます。 それらはベスト プラクティスのホワイト ペーパーに不可欠なものであり、[クラウド導入フレームワーク](/azure/cloud-adoption-framework/manage/azure-server-management)を参照してください。

これらサービスすべてについて、自動オンボード、自動構成、ドリフトの監視、ドリフトが検出された場合の修正が行われます。


## <a name="enabling-automanage-for-vms-in-azure-portal"></a>Azure portal で Azure Automanage for VMs を有効にする

Azure portal では、既存の仮想マシンに対して、または新しい仮想マシンを作成するときに、Automanage を有効にできます。 このプロセスの簡単な手順については、[Azure Automanage for virtual machines のクイックスタート](quick-create-virtual-machines-portal.md)に関するページを参照してください。

VM に対して Automanage を初めて有効にする場合は、Azure portal で **[Automanage – Azure virtual machine best practices]\(Automanage – Azure 仮想マシンのベスト プラクティス\)** を検索します。 **[Enable on existing VM]\(既存の VM で有効にする\)** をクリックし、オンボードする VM を選択し、 **[選択]** をクリックし、 **[有効]** をクリックして終わりです。

これらのサービスを管理するためにこの VM との対話が必要になるのは、VM の自動修正が試みられて失敗した場合だけです。 VM の自動修正が正常に行われた場合は、コンプライアンス状態に戻り、お客様には何も通知されません。


## <a name="configuration-profiles"></a>構成プロファイル

仮想マシンに対して Automanage を有効にするときは、構成プロファイルが必要です。 構成プロファイルは、このサービスの基礎となります。 それらでは、マシンがオンボードされるサービスと、それらのサービスのある程度の構成が、正確に定義されています。

### <a name="default-configuration-profiles"></a>既定の構成プロファイル

現在、2 つの構成プロファイルを使用できます。

- **Azure 仮想マシンのベスト プラクティス - Dev/Test** 構成プロファイルは、Dev/Test 用のマシン向けに設計されています。
- **Azure 仮想マシンのベスト プラクティス - 運用** 構成プロファイルは、運用環境向けです。

このような違いの理由は、実行されるワークロードに基づいて特定のサービスが推奨されるためです。 たとえば、運用マシンでは、Azure Backup に自動的にオンボードされます。 一方、Dev/Test マシンの場合は通常、ビジネスへの影響が少ないため、バックアップ サービスは不要なコストになります。

### <a name="customizing-a-configuration-profile-using-preferences"></a>基本設定を使用した構成プロファイルのカスタマイズ

自動的にオンボードされる標準サービスに加えて、ユーザーは基本設定の特定のサブセットを構成することもできます。 これらの基本設定は、ベスト プラクティスに違反しない構成オプションの範囲内で許可されます。 たとえば、Azure Backup の場合、ユーザーによる定義が許可されているのは、バックアップの頻度と実行する曜日です。 しかし、Azure Backup 完全にオフにすることは許可されて "*いません*"。

> [!NOTE]
> Dev/Test 構成プロファイルでは、VM のバックアップはまったく行われません。

既定の構成プロファイルの設定は、基本設定を使用して調整できます。 基本設定の作成方法については、[こちら](virtual-machines-custom-preferences.md)を参照してください。

> [!NOTE]
> Automanage が有効になっている間は、VM の構成プロファイルを変更できません。 その VM の Automanage を無効にしてから、必要な構成プロファイルと設定を使用して Automanage を再度有効にする必要があります。


## <a name="automanage-account"></a>Automanage アカウント

Automanage アカウントは、自動化された操作が行われるセキュリティ コンテキストまたは ID です。 通常、Automanage アカウント オプションを選択する必要はありませんが、リソースの自動化された管理を (おそらくは 2 人のシステム管理者間で) 分割したい委任シナリオがあった場合は、このオプションを使用すると、これらの各管理者の Azure ID を定義できます。

Azure portal のエクスペリエンスでは、VM で Automanage が有効になっていると、 **[Enable Azure VM best practice]\(Azure VM のベスト プラクティスを有効にする\)** ブレードの詳細ドロップダウンを使用して、Automanage アカウントを割り当てたり、手動で作成したりすることができます。

Automanage アカウントには、Automanage をオンボードするマシンを含むサブスクリプションに対する **共同作成者** および **リソース ポリシー共同作成者** ロールの両方が付与されます。 複数のサブスクリプションにわたるマシンで同じ Automanage アカウントを使用できます。これにより、その Automanage アカウントに、すべてのサブスクリプションに対する **共同作成者** および **リソース ポリシー共同作成者** アクセス許可が付与されます。

VM が別のサブスクリプションの Log Analytics ワークスペースに接続されている場合は、Automanage アカウントに、その別のサブスクリプションの **共同作成者** と **リソース ポリシー共同作成者** の両方も付与されます。

新しい Automanage アカウントで Automanage を有効にする場合は、サブスクリプションに対する次のアクセス許可が必要です。**所有者** ロール、または **共同作成者** ロールと **ユーザー アクセス管理者** ロールの併用。

既存の Automanage アカウントを使用して Automanage を有効にする場合は、VM を含むリソース グループの **共同作成者** ロールが必要です。

> [!NOTE]
> 自動管理のベスト プラクティスを無効にしても、関連付けられているすべてのサブスクリプションに対する Automanage アカウントのアクセス許可がそのままになります。 サブスクリプションの IAM ページに移動してアクセス許可を手動で削除するか、または Automanage アカウントを削除してください。 Automanage アカウントがまだいずれかのマシンを管理している場合、そのアカウントを削除することはできません。


## <a name="status-of-vms"></a>VM の状態

Azure portal で、自動管理されている VM がすべて表示されている **[Automanage – Azure virtual machine best practices]\(Automanage - Azure 仮想マシンのベスト プラクティス)** ページに移動します。 ここには、各仮想マシンの全体的な状態が表示されます。

:::image type="content" source="media\automanage-virtual-machines\configured-status.png" alt-text="構成済み仮想マシンの一覧。":::

一覧の VM ごとに、次の詳細が表示されます: 名前、構成プロファイル、構成の基本設定、状態、アカウント、サブスクリプション、リソース グループ。

**[状態]** 列には、次の状態が表示されます。
- *[進行中]* - VM は有効にされたばかりで、構成が行われています
- *[構成済み]* - VM は構成されており、ドリフトは検出されていません
- *[失敗]* - VM にはドリフトがあり、修正できませんでした
- *[保留中]* - VM は現在実行されておらず、次回実行されるとき、Automanage はその VM をオンボードまたは修復しようとします

**[状態]** が *[失敗]* と表示される場合は、VM が配置されているリソース グループを使用してデプロイのトラブルシューティングを行うことができます。 **[リソース グループ]** に移動し、リソース グループを選択し、 **[デプロイ]** をクリックして、"*失敗*" 状態とエラーの詳細を確認します。


## <a name="disabling-automanage-for-vms"></a>Automanage for VMs を無効にする

特定の VM について Automanage を無効にすることができます。 たとえば、マシンで非常に機密性の高いセキュリティで保護されたワークロードが実行されていて、Azure によって既定で行われるより厳しくロックダウンする必要があるため、Azure のベスト プラクティスの範囲外でマシンを構成する必要があるような場合です。

Azure portal でそれを行うには、自動管理されている VM がすべて表示される **[Automanage – Azure virtual machine best practices]\(Automanage - Azure 仮想マシンのベスト プラクティス)** ページに移動します。 Automanage から無効にする仮想マシンの横にあるチェック ボックスをオンにして、 **[Disable automanagment]\(自動管理を無効にする\)** ボタンをクリックし ます。

:::image type="content" source="media\automanage-virtual-machines\disable-step-1.png" alt-text="仮想マシンでの Automanage の無効化。":::

**[Disable]\(無効\)** に同意する前に、結果として表示されたポップアップのメッセージを注意深く確認します。

> [!NOTE]
> VM で自動管理を無効にすると、次の動作が発生します。
>
> - VM とそれにオンボードされているサービスの構成は変更されません。
> - それらのサービスによって発生する料金は引き続き課金されます。
> - Automanage のすべての動作は直ちに停止します。


何よりもまず、仮想マシンは、オンボードされて構成されているどのサービスからも、オフボードされることはありません。 そのため、それらのサービスによって発生する料金は引き続き課金されます。 必要な場合は、オフボードする必要があります。 Automanage のすべての動作は直ちに停止します。 たとえば、VM でドリフトは監視されなくなります。

## <a name="next-steps"></a>次の手順

この記事では、Automanage for virtual machines によって、Azure サービスを理解し、それにオンボードし、そのベスト プラクティスを構成する必要がなくなる手段が提供されることを説明しました。 また、Automanage for virtual machines に対してオンボードしたマシンが、構成プロファイルの設定から外れた場合は、準拠するように自動的に戻されます。

Azure portal でAzure Automanage for virtual machines を有効にしてみてください。

> [!div class="nextstepaction"]
> [Azure portal で Azure Automanage for virtual machines を有効にする](quick-create-virtual-machines-portal.md)