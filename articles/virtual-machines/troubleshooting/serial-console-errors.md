---
title: Azure シリアル コンソールのエラー | Microsoft Docs
description: Azure シリアル コンソール内での一般的なエラー
services: virtual-machines
documentationcenter: ''
author: asinn826
manager: borisb
editor: ''
tags: azure-resource-manager
ms.service: virtual-machines
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm
ms.workload: infrastructure-services
ms.date: 8/20/2019
ms.author: alsin
ms.openlocfilehash: cad12a55332a6c7898f9709776c58d7dba8dd81a
ms.sourcegitcommit: 3543d3b4f6c6f496d22ea5f97d8cd2700ac9a481
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/20/2020
ms.locfileid: "86526437"
---
# <a name="common-errors-within-the-azure-serial-console"></a>Azure シリアル コンソール内での一般的なエラー
Azure シリアル コンソール内には、一連の既知のエラーがあります。 これらのエラーとその対応策の一覧を次に示します。

## <a name="common-errors"></a>一般的なエラー

エラー                             |   対応策
:---------------------------------|:--------------------------------------------|
"Azure シリアル コンソールでは、ブート診断を有効にする必要があります。 仮想マシンのブート診断を構成するには、ここをクリックしてください。" | VM または仮想マシン スケール セットで[ブート診断](boot-diagnostics.md)が有効になっていることを確認します。 仮想マシン スケール セットでシリアル コンソールを使用している場合は、インスタンスが最新モデルであることを確認してください。
"Azure シリアル コンソールでは、仮想マシンが実行されている必要があります。 上の [開始] ボタンを使用して、仮想マシンを起動してください。"  | シリアル コンソールにアクセスするには、VM または仮想マシン スケール セット インスタンスが起動済み状態になっている必要があります (VM を停止または再割り当てしてはいけません)。 VM または仮想マシン スケール セット インスタンスが実行されていることを確認して、もう一度やり直してください。
"このサブスクリプションの Azure シリアル コンソールが有効になっていません。有効にするには、サブスクリプション管理者に連絡してください。" | Azure シリアル コンソールは、サブスクリプション レベルで無効化できます。 サブスクリプション管理者は、[Azure シリアル コンソールを有効または無効にする](./serial-console-enable-disable.md)ことができます。 サブスクリプション管理者でない場合は、次の手順のために、サブスクリプション管理者に連絡する必要があります。
この VM のブート診断ストレージ アカウントにアクセスする際に、"許可されていません" という応答が発生しました。 | ブート診断にアカウントのファイアウォールがないことを確認してください。 シリアル コンソールが機能するには、アクセス可能なブート診断ストレージ アカウントが必要です。 シリアル コンソールは、ブート診断ストレージ アカウントで有効になっているストレージ アカウント ファイアウォールと設計上、連動できません。
You do not have the required permissions to use this VM with the serial console. (シリアル コンソールでこの VM を使用するために必要なアクセス許可がありません。) Ensure you have at least Virtual Machine Contributor role permissions. (少なくとも仮想マシン共同作成者ロールのアクセス許可があることを確認してください。)| シリアル コンソールにアクセスするには、VM または仮想マシン スケール セットに対する共同作成者レベル以上のアクセス権が必要です。 詳細については、[概要](serial-console-overview.md)に関するページを参照してください。
The storage account '' used for boot diagnostics on this VM could not be found. (この VM のブート診断に使用したストレージ アカウント '' が見つかりませんでした。) Verify that boot diagnostics is enabled for this VM, this storage account has not been deleted, and you have access to this storage account. (この VM に対してブート診断が有効になっていること、このストレージ アカウントが削除されていないこと、このストレージ アカウントへのアクセス権があることをご確認ください。) | VM または仮想マシン スケール セットのブート診断ストレージ アカウントが削除されていないことを再度確認します。
VM へのシリアル コンソール接続でエラーが発生しました:'Bad Request' (400) | これは、ブート診断の URI が正しくない場合に発生する可能性があります。 たとえば、"https://" の代わりに "http://" が使用されました。 次のコマンドを使用して、ブート診断の URI を修正できます。`az vm boot-diagnostics enable --name vmName --resource-group rgName --storage https://<storageAccountUri>.blob.core.windows.net/`
You do not have the required permissions to write to the boot diagnostics storage account for this VM. (この VM のブート診断ストレージ アカウントに書き込むために必要なアクセス許可がありません。) 少なくとも VM 共同作成者のアクセス許可があることをご確認ください。 | シリアル コンソールにアクセスするには、ブート診断ストレージ アカウントに対する共同作成者レベルのアクセス権が必要です。 詳細については、[概要](serial-console-overview.md)に関するページを参照してください。
Unable to determine the resource group for the boot diagnostics storage account *&lt;STORAGEACCOUNTNAME&gt;* . (ブート診断ストレージ アカウント STORAGEACCOUNTNAME のリソース グループを特定できません。) Verify that boot diagnostics is enabled for this VM and you have access to this storage account. (この VM のブート診断が有効になっており、このストレージ アカウントにアクセスできることを確認してください。) | シリアル コンソールにアクセスするには、ブート診断ストレージ アカウントに対する共同作成者レベルのアクセス権が必要です。 詳細については、[概要](serial-console-overview.md)に関するページを参照してください。
Provisioning for this VM has not yet succeeded. (この VM のプロビジョニングはまだ成功していません。) Please ensure the VM is fully deployed and retry the serial console connection. (VM が完全にデプロイされていることを確認して、シリアル コンソール接続をもう一度お試しください。) | VM または仮想マシン スケール セットがまだプロビジョニング中である可能性があります。 しばらく待って、もう一度お試しください。
Web ソケットが閉じているか、開けませんでした。 | `*.console.azure.com` にファイアウォール アクセスを追加することが必要になる場合があります。 より詳細であるが時間がかかるアプローチとしては、[Microsoft Azure データセンターの IP 範囲](https://www.microsoft.com/download/details.aspx?id=41653)へのファイアウォール アクセスを許可するというものがありますが、この IP 範囲はかなり定期的に変化します。
シリアル コンソールが、階層型名前空間を持つ Azure Data Lake Storage Gen2 を使用するストレージ アカウントで機能しません。 | これは階層型名前空間の既知の問題です。 緩和するには、Azure Data Lake Storage Gen2 を使用して VM のブート診断ストレージ アカウントを作成しないようにします。 このオプションは、ストレージ アカウントの作成時にのみ設定できます。 この問題を緩和するには、Azure Data Lake Storage Gen2 を有効にせずに別個のブート診断ストレージ アカウントを作成しなければならない場合があります。
VM へのシリアル コンソール接続でエラーが発生しました:'Forbidden'(SubscriptionNotEnabled) - Subscription name undefined, id \<subscription id> is in non-Enabled state undefined ('Forbidden'(SubscriptionNotEnabled) - サブスクリプション名が未定義です、ID \<subscription id> は、有効ではない状態で未定義です) | この問題は、ユーザーが Cloud Shell ストレージ アカウントを作成したサブスクリプションが無効になっている場合に発生することがあります。 軽減するには、Cloud Shell を起動し、[必要な手順を行い](../../cloud-shell/persisting-shell-storage.md#unmount-clouddrive-1)、現在のサブスクリプションの Cloud Shell にバックアップしたストレージ アカウントを再プロビジョニングします。

## <a name="next-steps"></a>次のステップ
* [Linux VM 用 Azure シリアル コンソール](./serial-console-linux.md)についての詳細を参照する
* [Windows VM 用 Azure シリアル コンソール](./serial-console-windows.md)についての詳細を参照する
