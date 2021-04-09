---
title: Azure NetApp Files 用の NFSv4.1 Kerberos ボリュームの問題をトラブルシューティングする | Microsoft Docs
description: Azure NetApp Files 用の NFSv4.1 Kerberos ボリュームの問題をトラブルシューティングするのに役立つエラー メッセージと解決策について説明します。
services: azure-netapp-files
documentationcenter: ''
author: b-juche
manager: ''
editor: ''
ms.assetid: ''
ms.service: azure-netapp-files
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: troubleshooting
ms.date: 01/12/2021
ms.author: b-juche
ms.openlocfilehash: 638607da02b1db4842cdc04f86a4fed1860c243f
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "98134315"
---
# <a name="troubleshoot-nfsv41-kerberos-volume-issues"></a>NFSv4.1 Kerberos ボリュームの問題のトラブルシューティング 

この記事では、NFSv4.1 Kerberos ボリュームの作成または管理時に発生する可能性のあるエラー状態の解決方法について説明します。 

## <a name="error-conditions-and-resolutions"></a>エラーの状態と解決策

|     エラー条件    |     解決策    |
|-|-|
|`Error allocating volume - Export policy rules does not match kerberosEnabled flag` | Azure NetApp Files は、NFSv3 ボリュームに対して Kerberos をサポートしていません。 Kerberos は、NFSv4.1 プロトコルでのみサポートされます。  |
|`This NetApp account has no configured Active Directory   connections`  |  **[KDC IP]** および **[AD サーバー名]** の各フィールドを使用して、NetApp アカウントに対して Active Directory を構成します。 手順については、「[Azure portal を構成する](configure-kerberos-encryption.md#configure-the-azure-portal)」を参照してください。 |
|`Mismatch between KerberosEnabled flag value and ExportPolicyRule's access type parameter values.`  | Azure NetApp Files は、プレーン NFSv4.1 ボリュームから Kerberos NFSv4.1 ボリュームへの変換、およびその逆の変換をサポートしていません。 |
|`mount.nfs: access denied by server when mounting volume <SMB_SERVER_NAME-XXX.DOMAIN_NAME>/<VOLUME_NAME>` <br>  例 : `smb-test-64d9.contoso.com:/nfs41-vol101` | <ol><li> A/PTR レコードが適切に設定され、サーバー名 `smb-test-64d9.contoso.com` の Active Directory に存在することを確認します。 <br> NFS クライアントでは、`smb-test-64d9.contoso.com` の `nslookup` が IP アドレス IP1 (つまり `10.1.1.68`) に解決される場合、IP1 の `nslookup` は 1 つのレコード (つまり、`smb-test-64d9.contoso.com`) にのみ解決される必要があります。 IP1 の `nslookup` が、複数の名前に解決されないようにする "*必要があります*"。 </li>  <li>PowerShell または UI のいずれかを使用して、AD で `NFS-<Smb NETBIOS NAME>-<few random characters>` タイプの NFS マシン アカウントに対して AES-256 を設定します。 <br> コマンドの例: <ul><li>`Set-ADComputer <NFS_MACHINE_ACCOUNT_NAME> -KerberosEncryptionType AES256` </li><li>`Set-ADComputer NFS-SMB-TEST-64 -KerberosEncryptionType AES256` </li></ul> </li> <li>NFS クライアント、AD、Azure NetApp Files ストレージ ソフトウェアの時刻が相互に同期され、時刻のずれが 5 分以内であることを確認します。 </li>  <li>`kinit <administrator>` コマンドを使用して、NFS クライアントで Kerberos チケットを取得します。</li> <li>NFS クライアントのホスト名を 15 文字未満に削減し、領域の結合をもう一度実行します。 </li><li>次のように、NFS クライアントと `rpcgssd` サービスを再起動します。 このコマンドは OS によって異なる場合があります。<br> RHEL 7: <br> `service nfs restart` <br> `service rpcgssd restart` <br> CentOS 8: <br> `systemctl enable nfs-client.target && systemctl start nfs-client.target` <br> Ubuntu: <br> (`rpc-gssd` サービスを再起動します。) <br> `sudo systemctl start rpc-gssd.service` </ul>| 
|`mount.nfs: an incorrect mount option was specified`   | この問題は、NFS クライアントの問題に関連している可能性があります。 NFS クライアントを再起動します。    |
|`Hostname lookup failed`   | DNS サーバーに逆引き参照ゾーンを作成してから、その逆引き参照ゾーンに AD ホストマシンの PTR レコードを追加する必要があります。 <br> たとえば、AD マシンの IP アドレスが `10.1.1.4` であり、AD マシンのホスト名 (hostname コマンドを使用して確認されたもの) が `AD1` であり、ドメイン名が `contoso.com` であるとします。 逆引き参照ゾーンに追加される PTR レコードは `10.1.1.4 -> AD1.contoso.com` になります。 |
|`Volume creation fails due to unreachable DNS server`  | 2 つの解決策を使用できます。 <br> <ul><li> このエラーは、DNS に到達できないことを示しています。 原因として、DNS IP の誤りやネットワークの問題が考えられます。 AD 接続で入力された DNS IP を調べて、その IP が正しいことを確認します。 </li> <li> AD とボリュームが同じリージョンにあり、さらに同じ VNet 内にあることを確認します。 それらが異なる VNet 内にある場合は、その 2 つの VNet 間に VNet ピアリングが確立されていることを確認します。 </li></ul> |
|NFSv4.1 Kerberos ボリュームの作成は、次の例のようなエラーが原因で失敗します。 <br> `Failed to enable NFS Kerberos on LIF "svm_e719cde8d6d0413fbd6adac0636cdecb_7ad0b82e_73349613". Failed to bind service principal name on LIF "svm_e719cde8d6d0413fbd6adac0636cdecb_7ad0b82e_73349613". SecD Error: server create fail join user auth.` |KDC IP が誤っていて、Kerberos ボリュームが作成されています。 正しいアドレスで KDC IP を更新します。 <br> KDC IP を更新しても、エラーは解消されません。 ボリュームを再作成する必要があります。 |

## <a name="next-steps"></a>次のステップ  

* [Azure NetApp Files の NFSv4.1 の Kerberos 暗号化を構成する](configure-kerberos-encryption.md)
