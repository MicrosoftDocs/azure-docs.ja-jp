---
title: Azure Site Recovery を使用した VMware VM の Azure へのディザスター リカバリー時のオンプレミスへのフェールバックのトラブルシューティング | Microsoft Docs
description: この記事では、Azure Site Recovery を使用して VMware VM を Azure にディザスター リカバリーするときのフェールバックと再保護の問題をトラブルシューティングする方法について説明します。
author: rayne-wiselman
manager: carmonm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 02/19/2019
ms.author: raynew
ms.openlocfilehash: c27e72333618f73b67eec9b5c0c3a70239a1c0b3
ms.sourcegitcommit: c2e7595a2966e84dc10afb9a22b74400c4b500ed
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/05/2019
ms.locfileid: "71970852"
---
# <a name="troubleshoot-vcenter-discovery-failures"></a>vCenter の検出エラーのトラブルシューティング

この記事は、VMware vCenter の検出のエラーが原因で発生する問題を解決する上で役立ちます。

## <a name="non-numeric-values-in-the-maxsnapshots-property"></a>maxSnapShots プロパティの数値以外の値

9\.20 より前のバージョンでは、VM の `snapshot.maxSnapShots` プロパティの数値以外の値を取得するときに vCenter が切断されます。

この問題は、エラー ID 95126 によって識別されます。

    ERROR :: Hit an exception while fetching the required informationfrom vCenter/vSphere.Exception details:
    System.FormatException: Input string was not in a correct format.
       at System.Number.StringToNumber(String str, NumberStyles options, NumberBuffer& number, NumberFormatInfo info, Boolean parseDecimal)
       at System.Number.ParseInt32(String s, NumberStyles style, NumberFormatInfo info)
       at VMware.VSphere.Management.InfraContracts.VirtualMachineInfo.get_MaxSnapshots()
    
この問題を解決するには:

- VM を特定し、値を数値に設定します (vCenter の VM の編集設定)。

または

- 構成サーバーを 9.20 以降のバージョンにアップグレードします。

## <a name="proxy-configuration-issues-for-vcenter-connectivity"></a>vCenter を接続するためのプロキシ構成の問題

vCenter の検出では、システム ユーザーによって構成されたシステムの既定のプロキシ設定が優先されます。 DRA サービスでは、統合セットアップ インストーラーまたは OVA テンプレートを使用して構成サーバーをインストールする際にユーザーが指定したプロキシ設定が優先されます。 

一般的に、Azure との通信など、パブリック ネットワークとの通信にプロキシが使用されます。 プロキシが構成されていて、ローカル環境に vCenter がある場合、プロキシは DRA と通信することができません。

この問題が発生すると、次のような状況になります。

- 次のエラーのため vCenter サーバー \<vCenter> に到達できません。リモート サーバーがエラー(503) Server Unavailable
- 次のエラーのため vCenter サーバー \<vCenter> に到達できません。リモート サーバーがエラーリモート サーバーに接続できません。
- vCenter/ESXi サーバーに接続できない。

この問題を解決するには:

[PsExec ツール](https://aka.ms/PsExec) をダウンロードします。 

PsExec ツールを使用して、システム ユーザーのコンテキストにアクセスし、プロキシ アドレスが構成されているかどうかを判断します。 次の手順に従って、バイパス リストに vCenter を追加できます。

Discovery プロキシの構成の場合

1. PsExec ツールを使用してシステム ユーザーのコンテキストで IE を開きます。
    
    psexec -s -i "%programfiles%\Internet Explorer\iexplore.exe"

2. vCenter の IP アドレスをバイパスするよう、Internet Explorer のプロキシ設定を変更します。
3. tmanssvc サービスを再起動します。

DRA プロキシ構成の場合

1. コマンド プロンプトを開き、Microsoft Azure Site Recovery Provider フォルダーを開きます。
 
    **cd C:\Program Files\Microsoft Azure Site Recovery Provider**

3. コマンド プロンプトで、次のコマンドを実行します。
   
   **DRCONFIGURATOR.EXE /configure /AddBypassUrls [IP アドレス/vCenter の追加時に提供される vCenter Server の FQDN]**

4. DRA プロバイダー サービスを再起動します。

## <a name="next-steps"></a>次の手順

[VMware VM のディザスター リカバリー用の構成サーバーを管理する](https://docs.microsoft.com/azure/site-recovery/vmware-azure-manage-configuration-server#refresh-configuration-server) 
