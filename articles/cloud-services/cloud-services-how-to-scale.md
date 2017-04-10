---
title: "クラシック ポータルでのクラウド サービスの自動スケール | Microsoft Docs"
description: "(クラシック) クラシック ポータルを使用して、Azure でクラウド サービスの Web ロールおよび worker ロールの自動スケール ルールを構成する方法について説明します。"
services: cloud-services
documentationcenter: 
author: Thraka
manager: timlt
editor: 
ms.assetid: eb167d70-4eba-42a4-b157-d8d0688abf4b
ms.service: cloud-services
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/27/2017
ms.author: adegeo
translationtype: Human Translation
ms.sourcegitcommit: 4f2230ea0cc5b3e258a1a26a39e99433b04ffe18
ms.openlocfilehash: 488b16623300d80a53a61badee420de106c418b0
ms.lasthandoff: 03/25/2017

---

# <a name="how-to-configure-auto-scaling-for-a-cloud-service-in-the-classic-portal"></a>クラシック ポータルでクラウド サービスの自動スケールを構成する方法
> [!div class="op_single_selector"]
> * [Azure ポータル](cloud-services-how-to-scale-portal.md)
> * [Azure クラシック ポータル](cloud-services-how-to-scale.md)

Azure クラシック ポータルの [スケール] ページでは、Web ロールまたは worker ロールの自動スケール設定を構成できます。 また、ルールベースの自動スケールの代わりに、手動スケーリングを構成することもできます。

> [!NOTE]
> この記事では、クラウド サービスの Web ロールと worker ロールについて説明します。 仮想マシンを直接作成した場合 (クラシック)、その仮想マシンはクラウド サービスでホストされます。 この情報の一部は、このような種類の仮想マシンに適用されます。 仮想マシンの可用性セットのスケーリングは、構成したスケール ルールに基づいて仮想マシンをアップダウンしているだけです。 Virtual Machines と可用性セットの詳細については、[Virtual Machines の可用性の管理](../virtual-machines/windows/classic/configure-availability.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json)に関するページを参照してください。

アプリケーションの規模の設定を構成する前に、以下に示す情報を考慮してください。

* 規模設定はコア使用量の影響を受けます。

    ロール インスタンスが大きいと、使用するコアも多くなります。 アプリケーションの規模の設定は、サブスクリプションのコア数の制限内でのみ行うことができます。 たとえば、サブスクリプションのコア数が 20 に制限されているとします。 アプリケーションを 2 つの中規模のクラウド サービスで実行している場合 (合計 4 コア)、サブスクリプション内の他のクラウド サービスのデプロイは残りの 16 コアまでしかスケールアップできません。 サイズの詳細については、「[Cloud Service のサイズ](cloud-services-sizes-specs.md)」を参照してください。

* メッセージのしきい値に基づいてアプリケーションの規模を設定する前に、キューを作成してロールに関連付ける必要があります。 詳細については、「 [How to use the Queue Storage Service ( Queue Storage サービスの使用方法)](../storage/storage-dotnet-how-to-use-queues.md)」を参照してください。

* クラウド サービスにリンクされたリソースの規模を設定できます。 リソースのリンクの詳細については、「 [方法: クラウド サービスに対するリソースのリンク](cloud-services-how-to-manage.md#how-to-link-a-resource-to-a-cloud-service)」を参照してください。

* アプリケーションの高可用性を可能にするには、アプリケーションを 2 つ以上のロール インスタンスと共にデプロイする必要があります。 詳細については、「 [サービス レベル アグリーメント](https://azure.microsoft.com/support/legal/sla/)」を参照してください。

> [!WARNING]
> 自動スケーリングは、従来の Azure Storage アカウントのみで動作します。 Azure Resource Manager ストレージ アカウントでは動作しません。

## <a name="schedule-scaling"></a>スケールのスケジュール
既定では、すべてのロールが特定のスケジュールに従っていません。 そのため、変更した設定はすべての時間およびすべての日に年間を通じて適用されます。 必要に応じて、次のいずれかのモードに対して手動スケールまたは自動スケールを設定できます。

* 平日の日中
* 週末
* 平日の夜間
* 平日の午前
* 特定の日付
* 特定の日付範囲

スケジュールの設定は、[Azure クラシック ポータル](https://manage.windowsazure.com/) の  
**[Cloud Services]** > **\[クラウド サービス名\]** > **[スケール]** > **\[[Production or Staging (運用またはステージング)]\]** ページで構成できます。

変更する各ロールの **[スケジュール時間の設定]** ボタンをクリックします。

![スケジュールに基づくクラウド サービスの自動スケール][scale_schedules]

## <a name="manual-scale"></a>手動でのスケーリング
**[スケール]** ページで、クラウド サービスで実行中のインスタンスの数を手動で増減できます。 この設定は、作成した各スケジュールまたはスケジュールを作成していない場合はすべての時間に対して構成されます。

1. [Azure クラシック ポータル](https://manage.windowsazure.com/)で、 **[Cloud Services]**をクリックし、クラウド サービスの名前をクリックしてダッシュボードを開きます。
   
   > [!TIP]
   > クラウド サービスが表示されない場合、**[運用]** から **[ステージング]**、またはその逆に変更する必要があります。

2. **[スケール]**をクリックします。
3. スケール オプションを変更するスケジュールを選択します。 スケジュールを定義していない場合、*[スケジュールされた時間なし]* が既定値です。
4. **[メトリックによるスケール]** セクションを探し、**[なし]** を選択します。 この設定は、すべてのロールの既定値です。
5. クラウド サービス内の各ロールには、使用するインスタンスの数を変更するスライダーがあります。
   
    ![クラウド サービス ロールの手動スケール][manual_scale]
   
    追加のインスタンスが必要な場合、 [クラウド サービスの仮想マシン サイズ](cloud-services-sizes-specs.md)を変更しなければならない場合があります。
6. **[保存]**をクリックします。  
   選択に応じて、ロール インスタンスが追加または削除されます。

> [!TIP]
> ![][tip_icon] が表示される場合は、マウスをそこに移動すると、特定の設定の動作に関するヘルプを入手することができます。

## <a name="automatic-scale---cpu"></a>自動スケール - CPU
このモードでは、平均 CPU 使用率 (%) が所定のしきい値を上回った場合、または下回った場合にスケーリングを行います。 このような場合、ロール インスタンスが作成または削除されます。

1. [Azure クラシック ポータル](https://manage.windowsazure.com/)で、 **[Cloud Services]**をクリックし、クラウド サービスの名前をクリックしてダッシュボードを開きます。
   
   > [!TIP]
   > クラウド サービスが表示されない場合、**[運用]** から **[ステージング]**、またはその逆に変更する必要があります。

2. **[スケール]**をクリックします。
3. スケール オプションを変更するスケジュールを選択します。 スケジュールを定義していない場合、*[スケジュールされた時間なし]* が既定値です。
4. **[メトリックによるスケール]** セクションを探し、**[CPU]** を選択します。
5. ここで、ロール インスタンス、(スケール アップをトリガーするための) ターゲット CPU の使用率、およびスケール アップ/ダウンするインスタンスの数の最小範囲と最大範囲を構成できます。

![CPU 負荷によるクラウド サービス ロールのスケール][cpu_scale]

> [!TIP]
> ![][tip_icon] が表示される場合は、マウスをそこに移動すると、特定の設定の動作に関するヘルプを入手することができます。

## <a name="automatic-scale---queue"></a>自動スケール - キュー
このモードでは、キュー内のメッセージの数が所定のしきい値を上回った場合または下回った場合に、自動的にスケーリングが行われます。 このような場合、ロール インスタンスが作成または削除されます。

1. [Azure クラシック ポータル](https://manage.windowsazure.com/)で、 **[Cloud Services]**をクリックし、クラウド サービスの名前をクリックしてダッシュボードを開きます。
   
   > [!TIP]
   > クラウド サービスが表示されない場合、**[運用]** から **[ステージング]**、またはその逆に変更する必要があります。

2. **[スケール]**をクリックします。
3. **[メトリックによるスケール]** セクションを探し、**[キュー]** を選択します。
4. ここで、ロール インスタンスの最小値と最大値の範囲、各インスタンスに対して処理するキューおよびキュー メッセージの数、およびスケールアップ/ダウンするインスタンスの数を構成できます。

![メッセージ キューによるクラウド サービス ロールのスケール][queue_scale]

> [!TIP]
> ![][tip_icon] が表示される場合は、マウスをそこに移動すると、特定の設定の動作に関するヘルプを入手することができます。

## <a name="scale-linked-resources"></a>リンク済みリソースの規模の設定
多くの場合、ロールのスケールを設定するときに、アプリケーションが使用しているデータベースのスケールもあわせて変更するのが得策です。 クラウド サービスにデータベースをリンクしている場合は、該当するリンクをクリックして、そのリソースのスケール設定を表示することができます。

1. [Azure クラシック ポータル](https://manage.windowsazure.com/)で、 **[Cloud Services]**をクリックし、クラウド サービスの名前をクリックしてダッシュボードを開きます。
   
   > [!TIP]
   > クラウド サービスが表示されない場合、**[運用]** から **[ステージング]**、またはその逆に変更する必要があります。

2. **[スケール]**をクリックします。
3. **[リンク済みリソース]** セクションを探し、**[このデータベースのスケールを管理する]** をクリックします。
   
   > [!NOTE]
   > **[リンク済みリソース]** セクションが表示されない場合、リンク済みリソースがない可能性があります。

![][linked_resource]

[manual_scale]: ./media/cloud-services-how-to-scale/manual-scale.png
[queue_scale]: ./media/cloud-services-how-to-scale/queue-scale.png
[cpu_scale]: ./media/cloud-services-how-to-scale/cpu-scale.png
[tip_icon]: ./media/cloud-services-how-to-scale/tip.png
[scale_schedules]: ./media/cloud-services-how-to-scale/schedules.png
[scale_popup]: ./media/cloud-services-how-to-scale/schedules-dialog.png
[linked_resource]: ./media/cloud-services-how-to-scale/linked-resources.png

