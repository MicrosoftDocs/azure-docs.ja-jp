---
title: 'VM の D: ドライブをデータ ディスクにする | Microsoft Docs'
description: 'Windows VM のドライブ文字を変更して、データ ディスクとして D: ドライブを使用する方法について説明します。'
services: virtual-machines-windows
documentationcenter: ''
author: cynthn
manager: gwallace
editor: ''
tags: azure-resource-manager,azure-service-management
ms.assetid: 0867a931-0055-4e31-8403-9b38a3eeb904
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.topic: article
ms.date: 01/02/2018
ms.author: cynthn
ms.openlocfilehash: 846bb7a5ea6c3f363a2811cf3feb30e37ff30504
ms.sourcegitcommit: 44e85b95baf7dfb9e92fb38f03c2a1bc31765415
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/28/2019
ms.locfileid: "70079871"
---
# <a name="use-the-d-drive-as-a-data-drive-on-a-windows-vm"></a>Windows VM のデータ ドライブとしての D: ドライブの使用
D ドライブを使用してデータを格納する必要があるアプリケーションの場合、次の手順に従って一時ディスク用の別のドライブ文字を使用します。 維持する必要があるデータは、一時ディスクに格納しないでください。

仮想マシンをサイズ変更する、または **停止 (割り当て解除)** する場合、新しいハイパーバイザーに対するその仮想マシンの配置がトリガーされる可能性があります。 計画済みまたは計画外メンテナンス イベントでも、この配置がトリガーされる可能性があります。 このシナリオで、一時ディスクに最初に利用可能なドライブ文字が再割り当てされます。 D: ドライブを使用する必要があるアプリケーションがある場合、次の手順を実行して pagefile.sys を一時的に移動し、新しいデータ ディスクをアタッチし、文字 D を割り当ててから、一時ドライブに pagefile.sys を戻す必要があります。 この手順を完了すると、VM が別のハイパーバイザーに移動した場合に Azure は D: を取り消さなくなります。

Azure が一時ディスクを使用する方法については、 [Microsoft Azure Virtual Machines の一時ドライブの概要](https://blogs.msdn.microsoft.com/mast/2013/12/06/understanding-the-temporary-drive-on-windows-azure-virtual-machines/)

## <a name="attach-the-data-disk"></a>データ ディスクのアタッチ
最初に、仮想マシンにデータ ディスクをアタッチする必要があります。 ポータルでこれを行う方法については、[Azure Portal でマネージド データ ディスクを接続する方法](attach-managed-disk-portal.md)に関する記事をご覧ください。

## <a name="temporarily-move-pagefilesys-to-c-drive"></a>一時的に pagefile.sys を C ドライブに移動します。
1. 仮想マシンへの接続 
2. **[スタート]** メニューを右クリックし、 **[システム]** を選択します。
3. 左側のメニューで、 **[システムの詳細設定]** を選択します。
4. **[パフォーマンス]** セクションで、 **[設定]** を選択します。
5. **[詳細]** タブを選択します。
6. **[仮想メモリ]** セクションで、 **[変更]** を選択します。
7. **C** ドライブを選択し、 **[システム管理サイズ]** をクリックして、 **[設定]** をクリックします。
8. **D** ドライブを選択し、 **[ページング ファイルなし]** をクリックして、 **[設定]** をクリックします。
9. [適用] をクリックします。 変更を有効にするにはコンピューターの再起動が必要であることを示す警告が表示されます。
10. 仮想マシンを再起動します。

## <a name="change-the-drive-letters"></a>ドライブ文字の変更
1. VM が再起動した後、VM に再びログオンします。
2. **[スタート]** メニューをクリックし、「**diskmgmt.msc**」と入力して、Enter キーを押します。 [ディスクの管理] が開始します。
3. 一時ストレージ ドライブの **D** を右クリックし、 **[ドライブ文字とパスの変更]** を選択します。
4. ドライブ文字として **T** などの新しいドライブを選び、 **[OK]** をクリックします。 
5. データ ディスクを右クリックして、 **[ドライブ文字とパスの変更]** を選択します。
6. ドライブ文字として **D** を選択し、 **[OK]** をクリックします。 

## <a name="move-pagefilesys-back-to-the-temporary-storage-drive"></a>pagefile.sys を一時ストレージ ドライブに戻します。
1. **[スタート]** メニューを右クリックし、 **[システム]** を選択します。
2. 左側のメニューで、 **[システムの詳細設定]** を選択します。
3. **[パフォーマンス]** セクションで、 **[設定]** を選択します。
4. **[詳細]** タブを選択します。
5. **[仮想メモリ]** セクションで、 **[変更]** を選択します。
6. OS ドライブ **C** を選択し、 **[ページング ファイルなし]** をクリックして、 **[設定]** をクリックします。
7. 一時ストレージ ドライブ **T** を選び、 **[システム管理サイズ]** をクリックして、 **[設定]** をクリックします。
8. **[Apply]** をクリックします。 変更を有効にするにはコンピューターの再起動が必要であることを示す警告が表示されます。
9. 仮想マシンを再起動します。

## <a name="next-steps"></a>次の手順
* [追加のデータ ディスクをアタッチする](attach-managed-disk-portal.md)ことで、仮想マシンに使用できるストレージを増やすことができます。

