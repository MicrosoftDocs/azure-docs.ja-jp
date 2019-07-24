---
title: DevTest Labs のコンセプト | Microsoft Docs
description: DevTest Labs の基本概念と、DevTest Labs を Azure Virtual Machines を簡単に作成、管理、監視するために使用する方法について説明します。
services: devtest-lab,virtual-machines,lab-services
documentationcenter: na
author: spelluru
manager: femila
editor: ''
ms.assetid: 105919e8-3617-4ce3-a29f-a289fa608fb2
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/05/2018
ms.author: spelluru
ms.openlocfilehash: 1e35513d5a5a799b1f5e45cf9a5aa97c083e2087
ms.sourcegitcommit: 549070d281bb2b5bf282bc7d46f6feab337ef248
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/21/2018
ms.locfileid: "53725203"
---
# <a name="devtest-labs-concepts"></a>DevTest ラボの概念
## <a name="overview"></a>概要
次の一覧には、DevTest ラボの主要な概念と定義が含まれています。

## <a name="labs"></a>ラボ
ラボとは、Virtual Machines (VM) などのリソース グループを包含するインフラストラクチャで、制限とクォータを指定することでこれらのリソースをより適切に管理することができます。

## <a name="virtual-machine"></a>仮想マシン
Azure VM は、Azure が提供する数種類の[スケーラブルなオンデマンド コンピューティング リソース](https://docs.microsoft.com/azure/app-service/overview-compare)の 1 つです。 Azure VM は、その VM を実行する物理的なハードウェアを購入して維持する手間を省き、仮想化がもたらす柔軟性を提供します。ただし、VM を維持するために、そこで実行するソフトウェアの構成、修正、インストールなど、引き続き特定のタスクを実行する必要があります。

「[Azure における Windows 仮想マシンの概要](https://docs.microsoft.com/azure/virtual-machines/virtual-machines-windows-overview)」では、VM を作成する前に検討する必要のある事項、VM の作成方法、VM の管理方法に関する情報を提供します。

## <a name="claimable-vm"></a>要求可能 VM
Azure の要求可能 VM は、アクセス許可を持つあらゆるラボ ユーザーが使用できる仮想マシンです。 ラボ管理者は、特定の基本イメージとアーティファクトで VM を準備し、これらを共有プールに保存できます。 次に、ラボ ユーザーは、その特定の構成の 1 つの作業用 VM を必要なときにプールから要求できます。

要求可能な VM は最初、特定のユーザーに割り当てられず、"要求可能な仮想マシン" の下のすべてのユーザーの一覧に表示されます。 ユーザーが VM を要求すると、その VM は、"マイ仮想マシン" 領域に挿入され、他のユーザーが要求できなくなります。

## <a name="environment"></a>環境
DevTest ラボでは、環境とは、ラボ内の Azure リソースのコレクションを指します。 [このブログ投稿](https://blogs.msdn.microsoft.com/devtestlab/2016/11/16/connect-2016-news-for-azure-devtest-labs-azure-resource-manager-template-based-environments-vm-auto-shutdown-and-more/)では、Azure Resource Manager テンプレートから複数 VM の環境を作成する方法について説明します。

## <a name="base-images"></a>基本イメージ
基本イメージは、VM をすばやく作成するためのすべてのツールと設定がプレインストールされ、構成されている VM イメージです。 既存のベースを選択し、テスト エージェントをインストールするためのアーティファクトを追加することで、VM をプロビジョニングできます。 次に、プロビジョニング済みの VM をベースとして保存することで、テスト エージェントを VM の各プロビジョニングに再インストールすることなく、ベースを使用できます。

## <a name="artifacts"></a>アーティファクト
アーティファクトは、VM のプロビジョニング後にアプリケーションをデプロイして構成するために使用します。 次に示すアーティファクトが対象です。

* VM にインストールするツール (エージェント、Fiddler、Visual Studio など)。
* VM 上で実行するアクション (リポジトリの複製など)。
* テスト対象のアプリケーション。

アーティファクトは、デプロイメントの実行と構成の適用の指示が含まれる [Azure Resource Manager](../azure-resource-manager/resource-group-overview.md) の JSON ファイルです。

## <a name="artifact-repositories"></a>アーティファクト リポジトリ
アーティファクト リポジトリは、アーティファクトがチェックインされる git リポジトリです。 アーティファクト リポジトリを組織内の複数のラボに追加することで、再利用と共有を有効にできます。

## <a name="formulas"></a>数式
式は、基本イメージに加え、VM を短時間でプロビジョニングするための手法です。 DevTestラボにおける式とは、ラボ VM の作成に使用できる既定のプロパティ値の一覧です。
式を使用することで、ベース イメージ、VM サイズ、仮想ネットワーク、およびアーティファクトなど、同じ一連のプロパティで VM を作成できるため、作成するたびにプロパティを指定する必要がありません。 数式から VM を作成する際に、この既定値をそのまま使用することも変更することもできます。

## <a name="policies"></a>ポリシー
ポリシーは、ラボでのコスト管理をサポートします。 たとえば、定義されたスケジュールに基づいて、VM を自動的にシャット ダウンするポリシーを作成できます。

## <a name="caps"></a>キャップ
キャップは、ラボにおける無駄を最小限に抑えるメカニズムです。 たとえば、ユーザーごと、またはラボで作成できるVM の数を制限するためのキャップを設定できます。

## <a name="security-levels"></a>セキュリティ レベル
セキュリティ アクセスは、Azure のロール ベースのアクセス制御 (RBAC) によって決定されます。 アクセスのしくみを理解するには、RBAC によって定義されているアクセス許可、ロール、およびスコープの違いを理解することが有用です。

* アクセス許可 - アクセス許可とは、特定のアクションへのアクセスを定義したもの (たとえば、すべての仮想マシンへの読み取りアクセス) です。
* ロール - ロールとは、グループ化してユーザーに割り当てることができる一連のアクセス許可です。 たとえば、" *サブスクリプション所有者* " ロールは、サブスクリプション内のすべてのリソースにアクセスできます。
* スコープ - スコープは、Azure リソースの階層内のレベル (単一のリソース グループ、単一のラボ、サブスクリプション全体など) です。

DevTest Labs のスコープ内では、ユーザーのアクセス許可を定義する 2 種類のロールがあります。ラボ所有者とラボ ユーザーです。

* ラボ所有者 - ラボ所有者は、ラボ内のすべてのリソースにアクセスできます。 そのため、ポリシーの変更、任意の VM の読み取りと書き込み、仮想ネットワークの変更などを行うことができます。
* ラボ ユーザー: ラボ ユーザーは VM、ポリシー、仮想ネットワークなど、すべてのラボ リソースを表示できますが、他のユーザーが作成したポリシーまたは VM を変更することはできません。

DevTest Labs にカスタム ロールを作成する方法については、記事「 [特定のラボ ポリシーに対するアクセス許可をユーザーに付与する](devtest-lab-grant-user-permissions-to-specific-lab-policies.md)」をご覧ください。

スコープは階層構造を持つため、ユーザーが特定のスコープのアクセス許可を持つ場合は、含まれているすべての下位のスコープでそのアクセス許可が自動的に付与されます。 たとえば、ユーザーにサブスクリプション所有者のロールが割り当てられている場合、ユーザーはサブスクリプションのすべてのリソースにアクセスできます。これには、すべての仮想マシン、すべての仮想ネットワーク、およびすべてのラボが含まれます。 このため、サブスクリプション所有者は、ラボ所有者のロールを自動的に継承します。 ただし、その逆は真ではありません。 ラボ所有者はラボにアクセスできます。これはサブスクリプション レベルよりも下位のスコープです。 そのため、ラボ所有者はラボの外にある仮想マシン、仮想ネットワーク、またはいずれのリソースも表示できません。

## <a name="azure-resource-manager-templates"></a>Azure Resource Manager のテンプレート
この記事で取り扱う概念はすべて Azure Resource Manager テンプレートを利用して構成できます。Azure Resource Manager テンプレートを利用すると、Azure ソリューションのインフラストラクチャ/構成を定義し、一貫性のある状態で繰り返しデプロイできます。

「[Azure Resource Manager テンプレートの構造と構文の詳細](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-authoring-templates#template-format)」では、Azure Resource Manager テンプレートの構造とテンプレートのさまざまなセクションで利用できるプロパティについて説明しています。

[!INCLUDE [devtest-lab-try-it-out](../../includes/devtest-lab-try-it-out.md)]

## <a name="next-steps"></a>次の手順
[DevTest Labs でラボを作成します。](devtest-lab-create-lab.md)
