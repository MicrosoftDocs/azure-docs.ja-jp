---
title: Azure NetApp Files のデュアルプロトコル ボリュームのトラブルシューティング | Microsoft Docs
description: Azure NetApp Files のデュアルプロトコルに関する問題のトラブルシューティングに役立つエラー メッセージと解決策について説明します。
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
ms.date: 01/22/2021
ms.author: b-juche
ms.openlocfilehash: fb4233a87231dddb1e3cb2777ac2ef53a61f833e
ms.sourcegitcommit: 78ecfbc831405e8d0f932c9aafcdf59589f81978
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 01/23/2021
ms.locfileid: "98726617"
---
# <a name="troubleshoot-dual-protocol-volumes"></a>デュアルプロトコル ボリュームのトラブルシューティング

この記事では、デュアルプロトコル ボリュームの作成または管理時に発生する可能性のあるエラー状態の解決方法について説明します。

## <a name="error-conditions-and-resolutions"></a>エラーの状態と解決策

|     エラー条件    |     解像度    |
|-|-|
| LDAP over TLS が有効になっていて、デュアルプロトコル ボリュームの作成に失敗し、エラー `This Active Directory has no Server root CA Certificate` が発生します。    |     このエラーがデュアルプロトコル ボリュームの作成中に発生した場合は、お使いの NetApp アカウントでルート CA 証明書が アップロードされていることを確認してください。    |
| デュアルプロトコル ボリュームの作成に失敗し、エラー `Failed to validate LDAP configuration, try again after correcting LDAP configuration` が発生します。    |  AD ホスト マシンのポインター (PTR) レコードが DNS サーバーに存在しない可能性があります。 DNS サーバーに逆引き参照ゾーンを作成してから、その逆引き参照ゾーンに AD ホストマシンの PTR レコードを追加する必要があります。 <br> たとえば、AD マシンの IP アドレスが `1.1.1.1` であり、AD マシンのホスト名 (`hostname` コマンドを使用して確認されたもの) が `AD1` であり、ドメイン名が `contoso.com` であるとします。  逆引き参照ゾーンに追加される PTR レコードは `1.1.1.1` -> `contoso.com` になります。   |
| デュアルプロトコル ボリュームの作成に失敗し、エラー `Failed to create the Active Directory machine account \\\"TESTAD-C8DD\\\". Reason: Kerberos Error: Pre-authentication information was invalid Details: Error: Machine account creation procedure failed\\n [ 434] Loaded the preliminary configuration.\\n [ 537] Successfully connected to ip 1.1.1.1, port 88 using TCP\\n**[ 950] FAILURE` が発生します。 |  このエラーは、NetApp アカウントで Active Directory に参加するときの AD パスワードが正しくないことを示しています。 正しいパスワードで AD 接続を更新してから、もう一度お試しください。 |
| デュアルプロトコル ボリュームの作成に失敗し、エラー `Could not query DNS server. Verify that the network configuration is correct and that DNS servers are available` が発生します。 |   このエラーは、DNS に到達できないことを示しています。 DNS IP が正しくないか、ネットワークに関する問題が発生していることが原因である可能性があります。 AD 接続で入力された DNS IP を調べて、その IP が正しいことを確認します。 <br> また、AD とボリュームが同じリージョンにあり、さらに同じ VNet 内にあることを確認します。 それらが異なる VNet 内にある場合は、必ずその 2 つの VNet 間に VNet ピアリングが確立されているようにします。|
| デュアルプロトコル ボリュームのマウント時に、アクセス許可が拒否されます。 | デュアルプロトコル ボリュームでは、NFS プロトコルと SMB プロトコルの両方がサポートされます。  UNIX システムのマウントされたボリュームにアクセスしようとすると、システムは使用している UNIX ユーザーを Windows ユーザーにマップしようとします。 マッピングが見つからない場合は、"アクセス許可が拒否されました" というエラーが発生します。 <br> この状況は、アクセスに ‘root’ ユーザーを使用する場合にも当てはまります。 <br> "アクセス許可が拒否されました" の問題を回避するには、マウント ポイントにアクセスする前に、Windows Active Directory に `pcuser` が含まれていることを確認します。 "アクセス許可が拒否されました" の問題が発生した後に `pcuser` を追加する場合は、キャッシュ エントリがクリアされるまで 24 時間待機してからアクセスを再試行してください。 |

## <a name="next-steps"></a>次の手順  

* [デュアルプロトコル ボリュームを作成する](create-volumes-dual-protocol.md)
* [Azure NetApp Files 用に NFS クライアントを構成する](configure-nfs-clients.md)
