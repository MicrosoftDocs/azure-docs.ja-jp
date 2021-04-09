---
title: Azure Site Recovery で VMware vCenter の検出エラーをトラブルシューティングする
description: この記事では、Azure Site Recovery で VMware vCenter の検出エラーをトラブルシューティングする方法について説明します。
author: mayurigupta13
manager: rochakm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 10/29/2019
ms.author: mayg
ms.openlocfilehash: 1a8471305af93194ccae7b0928685e10d4d64726
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "92366660"
---
# <a name="troubleshoot-vcenter-server-discovery-failures"></a>vCenter Server の検出エラーをトラブルシューティングする

この記事は、VMware vCenter の検出のエラーが原因で発生する問題を解決する上で役立ちます。

## <a name="non-numeric-values-in-the-maxsnapshots-property"></a>maxSnapShots プロパティの数値以外の値

9\.20 より前のバージョンでは、VM の `snapshot.maxSnapShots` プロパティの数値以外の値を取得するときに vCenter が切断されます。

この問題は、エラー ID 95126 によって識別されます。

```output
ERROR :: Hit an exception while fetching the required informationfrom vCenter/vSphere.Exception details:
System.FormatException: Input string was not in a correct format.
    at System.Number.StringToNumber(String str, NumberStyles options, NumberBuffer& number, NumberFormatInfo info, Boolean parseDecimal)
    at System.Number.ParseInt32(String s, NumberStyles style, NumberFormatInfo info)
    at VMware.VSphere.Management.InfraContracts.VirtualMachineInfo.get_MaxSnapshots()
```

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

[PsExec ツール](/sysinternals/downloads/psexec) をダウンロードします。 

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

## <a name="next-steps"></a>次のステップ

[VMware VM のディザスター リカバリー用の構成サーバーを管理する](./vmware-azure-manage-configuration-server.md#refresh-configuration-server)