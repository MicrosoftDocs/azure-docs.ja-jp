---
title: Azure で入れ子になった仮想化を使用して問題のある Azure VM のトラブルシューティングを行う | Microsoft Docs
description: Azure で入れ子になった仮想化を使用して問題のある Azure VM のトラブルシューティングを行う方法
services: virtual-machines-windows
documentationcenter: ''
author: glimoli
manager: dcscontentpm
editor: ''
tags: azure-resource-manager
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.topic: article
ms.date: 11/01/2018
ms.author: genli
ms.openlocfilehash: ad359a19cb42bf115189aca7905d1908d0dc5284
ms.sourcegitcommit: c79aa93d87d4db04ecc4e3eb68a75b349448cd17
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/18/2019
ms.locfileid: "71087060"
---
# <a name="troubleshoot-a-problem-azure-vm-by-using-nested-virtualization-in-azure"></a>Azure で入れ子になった仮想化を使用して問題のある Azure VM のトラブルシューティングを行う

この記事では、入れ子になった仮想化環境を Microsoft Azure に作成し、問題のある VM のディスクを Hyper-V ホスト (復旧 VM) にマウントしてトラブルシューティングを行う方法について説明します。

## <a name="prerequisites"></a>前提条件

問題のある VM をマウントするには、復旧 VM が次の前提条件を満たしている必要があります。

-   問題のある VM と同じ場所に復旧 VM が存在している必要があります。

-   問題のある VM と同じリソース グループに復旧 VM が存在している必要があります。

-   問題のある VM と同じタイプのストレージ アカウント (Standard または Premium) が復旧 VM に使用されている必要があります。

## <a name="step-1-create-a-rescue-vm-and-install-hyper-v-role"></a>手順 1:復旧 VM を作成し、Hyper-V ロールをインストールする

1.  新しい復旧 VM を作成します。

    -  オペレーティング システム:Windows Server 2016 Datacenter

    -  サイズ:入れ子になった仮想化をサポートする少なくとも 2 つのコアを備えたすべての V3 シリーズ。 詳細については、「[Introducing the new Dv3 and Ev3 VM sizes (新しい VM サイズ Dv3 および Ev3 の発表)](https://azure.microsoft.com/blog/introducing-the-new-dv3-and-ev3-vm-sizes/)」を参照してください。

    -  問題のある VM と同じ場所、ストレージ アカウント、リソース グループ。

    -  問題のある VM と同じストレージの種類 (Standard または Premium) を選択。

2.  復旧 VM の作成後、その復旧 VM にリモート デスクトップで接続します。

3.  サーバー マネージャーで **[管理]**  >  **[役割と機能の追加]** の順に選択します。

4.  **[インストールの種類]** セクションで **[役割ベースまたは機能ベースのインストール]** を選択します。

5.  **[対象サーバーの選択]** セクションで、復旧 VM が選択されていることを確認します。

6.  **[Hyper-V ロール]**  >  **[機能の追加]** の順に選択します。

7.  **[機能]** セクションで **[次へ]** を選択します。

8.  仮想スイッチが利用可能な場合は、それを選択します。 それ以外の場合は、 **[次へ]** を選択します。

9.  **[移行]** セクションで **[次へ]** を選択します。

10. **[既定の保存場所]** セクションで **[次へ]** を選択します。

11. サーバーを自動的に再起動するためのチェック ボックスを必要に応じてオンにします。

12. **[インストール]** を選択します。

13. サーバーに Hyper-V ロールのインストールを許可します。 これには数分かかります。サーバーは自動的に再起動します。

## <a name="step-2-create-the-problem-vm-on-the-rescue-vms-hyper-v-server"></a>手順 2:復旧 VM の Hyper-V サーバー上に問題のある VM を作成する

1.  問題のある VM のディスクの名前を記録しておき、問題のある VM を削除します。 アタッチされているディスクはすべて維持してください。 

2.  問題のある VM の OS ディスクを復旧 VM のデータ ディスクとしてアタッチします。

    1.  問題のある VM の削除後、復旧 VM に移動します。

    2.  **[ディスク]** を選択してから、 **[データ ディスクの追加]** を選択します。

    3.  問題のある VM のディスクを選択してから、 **[保存]** を選択します。

3.  ディスクが正常にアタッチされたら、リモート デスクトップで復旧 VM に接続します。

4.  ディスクの管理 (diskmgmt.msc) を開きます。 問題のある VM のディスクが **[オフライン]** に設定されていることを確認します。

5.  Hyper-V マネージャーを開き、 **[サーバー マネージャー]** で **Hyper-V ロール**を選択します。 そのサーバーを右クリックし、 **[Hyper-V マネージャー]** を選択します。

6.  Hyper-V マネージャーで、復旧 VM を右クリックし、 **[新規]**  >  **[仮想マシン]**  >  **[次へ]** の順に選択します。

7.  VM の名前を入力し、 **[次へ]** を選択します。

8.  **[第 1 世代]** を選択します。

9.  起動メモリを 1024 MB 以上に設定します。

10. 該当する場合、作成済みの Hyper-V ネットワーク スイッチを選択します。 それ以外の場合、次のページに進みます。

11. **[後で仮想ハード ディスクを接続する]** を選択します。

    ![[後で仮想ハード ディスクを接続する] オプションの画像](media/troubleshoot-vm-by-use-nested-virtualization/attach-disk-later.png)

12. VM が作成されたら **[完了]** を選択します。

13. 作成した VM を右クリックし、 **[設定]** を選択します。

14. **[IDE コントローラー 0]** を選択し、 **[ハード ドライブ]** を選択して、 **[追加]** をクリックします。

    ![新しいハード ドライブの追加に関する画像](media/troubleshoot-vm-by-use-nested-virtualization/create-new-drive.png)    

15. **[物理ハード ディスク]** で、Azure VM にアタッチされている問題のある VM のディスクを選択します。 ディスクが一覧にない場合は、[ディスクの管理] でそのディスクがオフラインに設定されているかどうかを確認してください。

    ![ディスクのマウントに関する画像](media/troubleshoot-vm-by-use-nested-virtualization/mount-disk.png)  


17. **[Apply]\(適用\)** を選択し、次に **[OK]** を選択します。

18. VM をダブルクリックして起動します。

19. これでその VM をオンプレミス VM として使用することができます。 必要なトラブルシューティング手順を実施することができます。

## <a name="step-3-re-create-your-azure-vm-in-azure"></a>手順 3:Azure で Azure VM を再作成する

1.  VM をオンライン状態に復帰させた後、その VM を Hyper-V マネージャーでシャットダウンします。

2.  [Azure Portal](https://portal.azure.com) に移動し、復旧 VM の [ディスク] を選択し、そのディスクの名前をコピーします。 この名前は、次の手順で使用します。 復旧 VM から固定ディスクをデタッチします。

3.  **[すべてのリソース]** に移動して目的のディスク名を検索し、そのディスクを選択します。

     ![ディスクの検索に関する画像](media/troubleshoot-vm-by-use-nested-virtualization/search-disk.png)     

4. **[VM の作成]** をクリックします。

     ![ディスクからの VM 作成に関する画像](media/troubleshoot-vm-by-use-nested-virtualization/create-vm-from-vhd.png) 

Azure PowerShell を使用してディスクから VM を作成することもできます。 詳細については、[PowerShell を使用して既存のディスクから新しい VM を作成する方法](../windows/create-vm-specialized.md#create-the-new-vm)に関するページを参照してください。 

## <a name="next-steps"></a>次の手順

VM への接続の問題が発生した場合は、[Azure VM への RDP 接続のトラブルシューティング](troubleshoot-rdp-connection.md)に関する記事をご覧ください。 VM で実行されているアプリケーションへのアクセスに関する問題については、[Windows VM でのアプリケーションの接続の問題のトラブルシューティング](troubleshoot-app-connection.md)に関する記事をご覧ください。
