---
title: Azure VM のゲスト OS のファイアウォールが正しく構成されていない | Microsoft Docs
description: ''
services: virtual-machines-windows
documentationcenter: ''
author: deland-han
manager: willchen
editor: ''
tags: ''
ms.service: virtual-machines
ms.topic: troubleshooting
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.devlang: azurecli
ms.date: 11/22/2018
ms.author: delhan
ms.openlocfilehash: fcea5e4e6bb108f1a8d8036e51a5dae8a9e6431b
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/13/2019
ms.locfileid: "60711018"
---
# <a name="azure-vm-guest-os-firewall-is-misconfigured"></a>Azure VM のゲスト OS のファイアウォールが正しく構成されていない

この記事では、Azure VM 上の正しく構成されていないゲスト オペレーティング システムのファイアウォールを修正する方法について説明します。

## <a name="symptoms"></a>現象

1.  仮想マシン (VM) の [ようこそ] 画面には、VM が完全に読み込まれていると表示されます。

2.  ゲスト オペレーティング システムの構成方法によっては、ネットワーク トラフィックの一部または全部が VM に到達しない可能性があります。

## <a name="cause"></a>原因

ゲスト システムのファイアウォールが正しく構成されていないと、VM に対する一部またはすべての種類のネットワーク トラフィックがブロックされる可能性があります。

## <a name="solution"></a>解決策

これらの手順を行う前に、バックアップとして、影響を受ける VM のシステム ディスクのスナップショットを取得します。 詳細については、[ディスクのスナップショット](../windows/snapshot-copy-managed-disk.md)に関する記事を参照してください。

この問題のトラブルシューティングを行うには、シリアル コンソールを使用するか、VM のシステム ディスクを復旧 VM にアタッチして [VM をオフライン修復](troubleshoot-rdp-internal-error.md#repair-the-vm-offline)します。

## <a name="online-mitigations"></a>オンラインの軽減策

[シリアル コンソールに接続し、PowerShell インスタンスを開きます](serial-console-windows.md#use-cmd-or-powershell-in-serial-console)。 VM 上でシリアル コンソールが有効になっていない場合は、次の Azure の記事の「VM をオフライン修復する」セクションに進みます。

 [リモート デスクトップを介して Azure VM に接続しようとするときに、内部エラーが発生する](troubleshoot-rdp-internal-error.md#repair-the-vm-offline)

次の規則は編集することができ (RDP を介した) VM へのアクセスを有効にしたり、簡単なトラブルシューティング エクスペリエンスを提供したりすることができます。

*   リモート デスクトップ (TCP 受信):Azure 内で RDP を許可することで VM へのプライマリ アクセスを提供する標準規則です。

*   Windows リモート管理 (HTTP 受信):この規則を使用すると、PowerShell を使用して VM に接続することができます。Azure では、このような種類のアクセスを介してリモート スクリプティングとトラブルシューティングのスクリプトの側面を使用できます。

*   ファイルとプリンターの共有 (SMB 受信):この規則で、トラブルシューティング オプションとしてネットワーク共有アクセスが有効になります。

*   ファイルとプリンターの共有 (エコー要求 - ICMPv4 受信):この規則を使用すると、VM に対して ping を実行できます。

シリアル コンソール アクセス インスタンスでは、ファイアウォール規則の現在の状態を照会できます。

*   表示名をパラメーターとして使用して照会します。

    ```cmd
    netsh advfirewall firewall show rule dir=in name=all | select-string -pattern "(DisplayName.*<FIREWALL RULE NAME>)" -context 9,4 | more
    ```

*   アプリケーションで使用されているローカル ポートを使用して照会します。

    ```cmd
    netsh advfirewall firewall show rule dir=in name=all | select-string -pattern "(LocalPort.*<APPLICATION PORT>)" -context 9,4 | more
    ```

*   アプリケーションで使用されているローカル IP アドレスを使用して照会します。

    ```cmd
    netsh advfirewall firewall show rule dir=in name=all | select-string -pattern "(LocalIP.*<CUSTOM IP>)" -context 9,4 | more
    ```

*   規則が無効であることがわかっている場合は、次のコマンドを実行して規則を有効にすることができます。

    ```cmd
    netsh advfirewall firewall set rule name="<RULE NAME>" new enable=yes
    ```

*   トラブルシューティングのために、ファイアウォール プロファイルをオフにすることができます。

    ```cmd
    netsh advfirewall set allprofiles state off
    ```

    ファイアウォールを正しく設定するためにこの操作を行う場合は、トラブルシューティングを完了した後でファイアウォールを再び有効にしてください。

    > [!Note]
    > この変更を適用するために VM を再起動する必要はありません。

*   RDP を介して VM への接続を再試行します。

### <a name="offline-mitigations"></a>オフラインの対応策

1.  ファイアウォール規則を有効または無効にする方法については、「[Azure VM のゲスト OS 上のファイアウォール規則を有効または無効にする](enable-disable-firewall-rule-guest-os.md)」を参照してください。

2.  [ゲスト OS のファイアウォールによって受信トラフィックがブロックされるシナリオ](guest-os-firewall-blocking-inbound-traffic.md)に該当するかどうかを確認します。

3.  それでもファイアウォールによってアクセスがブロックされているかどうかがわからない場合は、「[Disable the guest OS Firewall in Azure VM](disable-guest-os-firewall-windows.md)」(Azure VM でゲスト OS のファイアウォールを無効にする) を参照してから、正しい規則を使用してゲスト システムのファイアウォールを再び有効にします。

