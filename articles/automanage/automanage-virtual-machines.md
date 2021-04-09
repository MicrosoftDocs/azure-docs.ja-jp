---
title: Azure Automanage for virtual machines
description: Azure Automanage for virtual machines について説明します。
author: deanwe
ms.service: virtual-machines
ms.subservice: automanage
ms.workload: infrastructure
ms.topic: conceptual
ms.date: 02/23/2021
ms.author: deanwe
ms.custom: references_regions
ms.openlocfilehash: 1d3b2174df5dd83852ce120ec6693ae187a3e795
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "101643538"
---
# <a name="azure-automanage-for-virtual-machines"></a>Azure Automanage for virtual machines

この記事では、Azure Automanage for virtual machines について説明します。これには次のような利点があります。

- 仮想マシンをインテリジェントにオンボードして、ベスト プラクティスの Azure サービスを選択する
- Azure のベスト プラクティスに従って、各サービスを自動的に構成する
- ドリフトを監視し、検出されたら修正する
- 簡単なエクスペリエンス (ポイント、クリック、設定、消去) を提供する


## <a name="overview"></a>概要

Azure Automanage for virtual machines サービスを使用すると、Azure で仮想マシンに役に立つ特定のサービスを検出し、オンボード方法を知り、構成方法を知る必要がなくなります。 これらのサービスは、Azure のベスト プラクティス サービスと見なされ、仮想マシンの信頼性、セキュリティ、管理を強化するのに役立ちます。 サービスの例としては、[Azure Update Management](../automation/update-management/overview.md) や [Azure Backup](../backup/backup-overview.md) などがあります。

ご利用の仮想マシンを Azure Automanage にオンボードすると、各ベスト プラクティス サービスが推奨される設定に自動的に構成されます。 ベスト プラクティスは、サービスごとに異なります。 たとえば、Azure Backup の場合、仮想マシンを 1 日に 1 回バックアップして 6 か月保持する、といったベスト プラクティスが考えられます。

また、Azure Automanage を使用すると、ドリフトが自動的に監視され、検出されると修正されます。 つまり、仮想マシンが Azure Automanage にオンボードされた場合、Azure のベスト プラクティスに従って自動的に構成されるだけでなく、ライフサイクル全体を通してそれらのベスト プラクティスに準拠し続けるように、マシンが自動的に監視されます。 仮想マシンがそれらのプラクティスからドリフトつまり逸脱した場合 (たとえば、サービスがオフボードされた場合) は、修正が行われ、マシンは望ましい状態に戻されます。

## <a name="prerequisites"></a>前提条件

仮想マシンで Azure Automanage を有効にする前に、いくつかの前提条件を考慮する必要があります。

- サポートされている [Windows Server のバージョン](automanage-windows-server.md#supported-windows-server-versions) と [Linux ディストリビューション](automanage-linux.md#supported-linux-distributions-and-versions)
- VM はサポート対象のリージョンに存在する必要があります (下記参照)
- ユーザーは正しいアクセス許可を持っている必要があります (下記参照)
- 現時点では、サンドボックス サブスクリプションは Automanage ではサポートされていません

### <a name="supported-regions"></a>サポートされているリージョン
Automanage では、次のリージョンにある VM のみがサポートされます。
* 西ヨーロッパ
* 北ヨーロッパ
* 米国中部
* 米国東部
* 米国東部 2
* 米国西部
* 米国西部 2
* カナダ中部
* 米国中西部
* 米国中南部
* 東日本
* 英国南部
* オーストラリア東部
* オーストラリア南東部

### <a name="required-rbac-permissions"></a>必要な RBAC アクセス許可
新しい Automanage アカウントで Automanage を有効にするかどうかによって、ご利用のアカウントに必要な RBAC ロールは若干異なります。

新しい Automanage アカウントで Automanage を有効にする場合は、次のようになります。
* ご利用の VM を含むサブスクリプションの **[所有者]** ロール、"_**または**_"
* ご利用の VM を含むサブスクリプションの **[共同作成者]** および **[ユーザー アクセス管理者]** ロール

既存の Automanage アカウントで Automanage を有効にする場合は、次のようになります。
* ご利用の VM を含むリソース グループの **[共同作成者]** ロール。

> [!NOTE]
> 別のサブスクリプションのワークスペースに接続されている VM で Automanage を使用する場合は、サブスクリプションごとに上記に記載されているアクセス許可を持っている必要があります。

## <a name="participating-services"></a>対象となるサービス

:::image type="content" source="media\automanage-virtual-machines\intelligently-onboard-services.png" alt-text="サービスをインテリジェントにオンボードする。":::

対象となる Azure サービスの完全な一覧と、サポートされている環境については、以下をご覧ください。
- [Linux 用 Automanage](automanage-linux.md)
- [Windows Server 用 Automanage](automanage-windows-server.md)

 これらの対象サービスは、自動的にオンボードされます。 それらはベスト プラクティスのホワイト ペーパーに不可欠なものであり、[クラウド導入フレームワーク](/azure/cloud-adoption-framework/manage/azure-server-management)を参照してください。

これらサービスすべてについて、自動オンボード、自動構成、ドリフトの監視、ドリフトが検出された場合の修正が行われます。


## <a name="enabling-automanage-for-vms-in-azure-portal"></a>Azure portal で Azure Automanage for VMs を有効にする

Azure portal では、既存の仮想マシンに対して、または新しい仮想マシンを作成するときに、Automanage を有効にできます。 このプロセスの簡単な手順については、[Azure Automanage for virtual machines のクイックスタート](quick-create-virtual-machines-portal.md)に関するページを参照してください。

VM に対して Automanage を初めて有効にする場合は、Azure portal で **[Automanage – Azure virtual machine best practices]\(Automanage – Azure 仮想マシンのベスト プラクティス\)** を検索します。 **[Enable on existing VM]\(既存の VM で有効にする\)** をクリックし、オンボードする VM を選択し、 **[選択]** をクリックし、 **[有効]** をクリックして終わりです。

これらのサービスを管理するためにこの VM との対話が必要になるのは、VM の自動修正が試みられて失敗した場合だけです。 VM の自動修正が正常に行われた場合は、コンプライアンス状態に戻り、お客様には何も通知されません。 詳細については、[VM の状態](#status-of-vms)に関するページをご覧ください。


## <a name="environment-configuration"></a>環境の構成

ご利用の仮想マシンに対して Automanage を有効にする場合は、環境が必要です。 環境は、このサービスの基礎となります。 それらには、ご利用のマシンがオンボードされるサービスと、それらのサービスのある程度の構成が定義されています。

### <a name="default-environments"></a>既定の環境

現在、2 つの環境が使用できます。

- **Dev/Test** 環境は、Dev/Test 用のマシン向けに設計されています。
- **運用** 環境は、実稼働向けです。

このような違いの理由は、実行されるワークロードに基づいて特定のサービスが推奨されるためです。 たとえば、運用マシンでは、Azure Backup に自動的にオンボードされます。 一方、Dev/Test マシンの場合は通常、ビジネスへの影響が少ないため、バックアップ サービスは不要なコストになります。

### <a name="customizing-an-environment-using-preferences"></a>基本設定を使用した環境のカスタマイズ

自動的にオンボードされる標準サービスに加えて、ユーザーは基本設定の特定のサブセットを構成することもできます。 これらの基本設定は、構成オプションの範囲内で許可されます。 たとえば、Azure Backup の場合、ユーザーによる定義が許可されているのは、バックアップの頻度と実行する曜日です。

> [!NOTE]
> Dev/Test 環境では、VM のバックアップはまったく行われません。

既定の環境の設定は、基本設定を使用して調整できます。 基本設定の作成方法については、[こちら](virtual-machines-custom-preferences.md)を参照してください。

> [!NOTE]
> Automanage が有効になっている間は、ご利用の VM の環境構成を変更できません。 その VM の Automanage を無効にしてから、必要な環境と基本設定を使用して Automanage を再度有効にする必要があります。

対象となる Azure サービスの完全な一覧と、それらによって基本設定がサポートされているかどうかについては、次をご覧ください。
- [Linux 用 Automanage](automanage-windows-server.md)
- [Windows Server 用 Automanage](automanage-windows-server.md)


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

一覧の VM ごとに、次の詳細が表示されます: 名前、環境、構成の基本設定、状態、オペレーティング システム、アカウント、サブスクリプション、リソース グループ。

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

この記事では、Automanage for virtual machines によって、Azure サービスを理解し、それにオンボードし、そのベスト プラクティスを構成する必要がなくなる手段が提供されることを説明しました。 また、Automanage for virtual machines に対してオンボードしたマシンが、環境の設定から外れた場合は、準拠するように自動的に戻されます。

Azure portal でAzure Automanage for virtual machines を有効にしてみてください。

> [!div class="nextstepaction"]
> [Azure portal で Azure Automanage for virtual machines を有効にする](quick-create-virtual-machines-portal.md)