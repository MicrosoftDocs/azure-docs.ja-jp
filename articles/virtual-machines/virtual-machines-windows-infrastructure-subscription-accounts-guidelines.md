---
title: "Azure の Windows VM 用サブスクリプションとアカウント | Microsoft Docs"
description: "Azure サブスクリプションとアカウントに関する主要な設計と実装のガイドラインについて説明します。"
documentationcenter: 
services: virtual-machines-windows
author: iainfoulds
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: 761fa847-78b0-4078-a33a-d95d198d1029
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.topic: article
ms.date: 03/17/2017
ms.author: iainfou
ms.custom: H1Hack27Feb2017
translationtype: Human Translation
ms.sourcegitcommit: cea53acc33347b9e6178645f225770936788f807
ms.openlocfilehash: 5b4b039c8f706a4b113cbe76c01e8330f5cebf3a
ms.lasthandoff: 03/03/2017


---
# <a name="azure-subscription-and-accounts-guidelines-for-windows-vms"></a>Windows VM 用の Azure サブスクリプションとアカウントのガイドライン

[!INCLUDE [virtual-machines-windows-infrastructure-guidelines-intro](../../includes/virtual-machines-windows-infrastructure-guidelines-intro.md)]

この記事は、環境とユーザー ベースの拡大に合わせたサブスクリプションとアカウント管理の方法について説明します。

## <a name="implementation-guidelines-for-subscriptions-and-accounts"></a>サブスクリプションとアカウントに関する実装ガイドライン
決めること:

* IT ワークロードやインフラストラクチャをホストするために必要なサブスクリプションとアカウントのセット
* 階層を組織に合わせて分類する方法

タスク:

* サブスクリプション レベルから管理する論理的な組織階層を定義する。
* この論理階層に合わせて必要なアカウントを定義し、さらにアカウントごとにサブスクリプションを定義する。
* 名前付け規則を使用して、サブスクリプションとアカウントのセットを作成する。

## <a name="subscriptions-and-accounts"></a>サブスクリプションとアカウント
Azure を使用するには、1 つまたは複数の Azure サブスクリプションが必要です。 仮想マシン (VM) や仮想ネットワークなどのリソースは、サブスクリプションに存在します。

* 企業のお客様は、通常、エンタープライズ加入契約を使用します。これは、階層内の最上位のリソースであり、1 つまたは複数のアカウントに関連付けられています。
* エンタープライズ加入契約を持たないコンシューマーおよび顧客の最上位のリソースはアカウントです。
* サブスクリプションはアカウントに関連付けられており、アカウントごとに&1; つまたは複数のサブスクリプションを使用できます。 Azure はサブスクリプション レベルで課金情報を記録します。

アカウント/サブスクリプションの関係での&2; つの階層レベルの制限のため、アカウントとサブスクリプションの名前付け規則を課金のニーズに合わせることが重要です。 たとえば、世界規模の企業が Azure を使用する場合、リージョンごとに&1; つのアカウントを使用し、リージョン レベルでサブスクリプションを管理できます。

![](./media/virtual-machines-common-infrastructure-service-guidelines/sub01.png)

たとえば、次のような構造を使用できます。

![](./media/virtual-machines-common-infrastructure-service-guidelines/sub02.png)

リージョンで特定のグループに複数のサブスクリプションを関連付ける場合は、名前付け規則に、アカウントまたはサブスクリプション名で追加データをエンコードする仕組みが組み込まれている必要があります。 次の組織では、請求レポートの間にメッセージ請求データで新しいレベルの階層を生成できます。

![](./media/virtual-machines-common-infrastructure-service-guidelines/sub03.png)

組織は次の例のようになります。

![](./media/virtual-machines-common-infrastructure-service-guidelines/sub04.png)

単一アカウントまたはエンタープライズ アグリーメントのすべてのアカウントについて、詳細な課金情報が記載されたファイルをダウンロードできます。

## <a name="next-steps"></a>次のステップ
[!INCLUDE [virtual-machines-windows-infrastructure-guidelines-next-steps](../../includes/virtual-machines-windows-infrastructure-guidelines-next-steps.md)]


