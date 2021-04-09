---
title: Azure NetApp Files の SMB またはデュアルプロトコル ボリュームのトラブルシューティング | Microsoft Docs
description: Azure NetApp Files の SMB またはデュアルプロトコルに関する問題のトラブルシューティングに役立つエラー メッセージと解決策について説明します。
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
ms.date: 02/19/2021
ms.author: b-juche
ms.openlocfilehash: ee2d77bf5e1d4af9b78345d55dee8050b84622e9
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/30/2021
ms.locfileid: "104955535"
---
# <a name="troubleshoot-smb-or-dual-protocol-volumes"></a>SMB またはデュアルプロトコル ボリュームのトラブルシューティング

この記事では、デュアルプロトコル ボリュームの作成または管理時に発生する可能性のあるエラー状態の解決方法について説明します。

## <a name="errors-for-dual-protocol-volumes"></a>デュアルプロトコル ボリュームのエラー

|     エラー条件    |     解決策    |
|-|-|
| LDAP over TLS が有効になっていて、デュアルプロトコル ボリュームの作成に失敗し、エラー `This Active Directory has no Server root CA Certificate` が発生します。    |     このエラーがデュアルプロトコル ボリュームの作成中に発生した場合は、お使いの NetApp アカウントでルート CA 証明書が アップロードされていることを確認してください。    |
| デュアルプロトコル ボリュームの作成に失敗し、エラー `Failed to validate LDAP configuration, try again after correcting LDAP configuration` が発生します。    |  AD ホスト マシンのポインター (PTR) レコードが DNS サーバーに存在しない可能性があります。 DNS サーバーに逆引き参照ゾーンを作成してから、その逆引き参照ゾーンに AD ホストマシンの PTR レコードを追加する必要があります。 <br> たとえば、AD マシンの IP アドレスが `10.x.x.x` であり、AD マシンのホスト名 (`hostname` コマンドを使用して確認されたもの) が `AD1` であり、ドメイン名が `contoso.com` であるとします。  逆引き参照ゾーンに追加される PTR レコードは `10.x.x.x` -> `contoso.com` になります。   |
| デュアルプロトコル ボリュームの作成に失敗し、エラー `Failed to create the Active Directory machine account \\\"TESTAD-C8DD\\\". Reason: Kerberos Error: Pre-authentication information was invalid Details: Error: Machine account creation procedure failed\\n [ 434] Loaded the preliminary configuration.\\n [ 537] Successfully connected to ip 10.x.x.x, port 88 using TCP\\n**[ 950] FAILURE` が発生します。 |     このエラーは、NetApp アカウントで Active Directory に参加するときの AD パスワードが正しくないことを示しています。 正しいパスワードで AD 接続を更新してから、もう一度お試しください。 |
| デュアルプロトコル ボリュームの作成に失敗し、エラー `Could not query DNS server. Verify that the network configuration is correct and that DNS servers are available` が発生します。 |   このエラーは、DNS に到達できないことを示しています。 DNS IP が正しくないか、ネットワークに関する問題が発生していることが原因である可能性があります。 AD 接続で入力された DNS IP を調べて、その IP が正しいことを確認します。 <br> また、AD とボリュームが同じリージョンにあり、さらに同じ VNet 内にあることを確認します。 それらが異なる VNet 内にある場合は、必ずその 2 つの VNet 間に VNet ピアリングが確立されているようにします。|
| デュアルプロトコル ボリュームのマウント時に、アクセス許可が拒否されます。 | デュアルプロトコル ボリュームでは、NFS プロトコルと SMB プロトコルの両方がサポートされます。  UNIX システムのマウントされたボリュームにアクセスしようとすると、システムは使用している UNIX ユーザーを Windows ユーザーにマップしようとします。 マッピングが見つからない場合は、"アクセス許可が拒否されました" というエラーが発生します。 <br> この状況は、アクセスに ‘root’ ユーザーを使用する場合にも当てはまります。 <br> "アクセス許可が拒否されました" の問題を回避するには、マウント ポイントにアクセスする前に、Windows Active Directory に `pcuser` が含まれていることを確認します。 "アクセス許可が拒否されました" の問題が発生した後に `pcuser` を追加する場合は、キャッシュ エントリがクリアされるまで 24 時間待機してからアクセスを再試行してください。 |

## <a name="common-errors-for-smb-and-dual-protocol-volumes"></a>SMB またはデュアルプロトコル ボリュームの一般的なエラー

|     エラー条件    |     解決策    |
|-|-|
| SMB またはデュアルプロトコル ボリュームの作成に失敗し、次のエラーが発生します。 <br> `{"code":"DeploymentFailed","message":"At least one resource deployment operation failed. Please list deployment operations for details. Please see https://aka.ms/DeployOperations for usage details.","details":[{"code":"InternalServerError", "message":"Error when creating - Could not query DNS server. Verify that the network configuration is correct and that DNS servers are available."}]}` | このエラーは、DNS に到達できないことを示しています。 <br> 次の解決策を検討してください。 <ul><li>ADDS およびこのボリュームが同じリージョンにデプロイされているか確認します。</li> <li>ADDS およびこのボリュームが同じ VNet を使用しているか確認します。 異なる VNet を使用している場合は、これらの VNet が相互にピアリングしていることを確認します。 「[Azure NetApp Files のネットワーク計画のガイドライン](azure-netapp-files-network-topologies.md)」を参照してください。 </li> <li>DNS サーバーにネットワーク セキュリティ グループ (NSG) が適用されている可能性があります。  そのため、トラフィック フローが許可されません。 この場合は、この NSG を、各種ポートに接続するために DNS または AD に対して開きます。 ポート要件については、「[Active Directory 接続の要件](create-active-directory-connections.md#requirements-for-active-directory-connections)」を参照してください。 </li></ul> <br>Azure ADDS についても同じ解決策が適用されます。 Azure ADDS は同じリージョンにデプロイする必要があります。 VNet は同じリージョン内に存在するか、ボリュームで使用される VNet とピアリングしている必要があります。 | 
| SMB またはデュアルプロトコル ボリュームの作成に失敗し、次のエラーが発生します。 <br> `{"code":"DeploymentFailed","message":"At least one resource deployment operation failed. Please list deployment operations for details. Please see https://aka.ms/DeployOperations for usage details.","details":[{"code":"InternalServerError", "message":"Error when creating - Failed to create the Active Directory machine account \"SMBTESTAD-C1C8\". Reason: Kerberos Error: Invalid credentials were given Details: Error: Machine account creation procedure failed\n [ 563] Loaded the preliminary configuration.\n**[ 670] FAILURE: Could not authenticate as 'test@contoso.com':\n** Unknown user (KRB5KDC_ERR_C_PRINCIPAL_UNKNOWN)\n. "}]}`  |  <ul><li>入力したユーザー名が正しいことを確認します。 </li> <li>ユーザーが Administrator グループのメンバーであり、マシンのアカウントを作成する権限が付与されていることを確認します。 </li> <li> Azure ADDS を使用している場合は、ユーザーが Azure AD グループ `Azure AD DC Administrators` のメンバーであることを確認します。 </li></ul> | 
| SMB またはデュアルプロトコル ボリュームの作成に失敗し、次のエラーが発生します。 <br> `{"code":"DeploymentFailed","message":"At least one resource deployment operation failed. Please list deployment operations for details. Please see https://aka.ms/DeployOperations for usage details.","details":[{"code":"InternalServerError", "message":"Error when creating - Failed to create the Active Directory machine account \"SMBTESTAD-A452\". Reason: Kerberos Error: Pre-authentication information was invalid Details: Error: Machine account creation procedure failed\n [ 567] Loaded the preliminary configuration.\n [ 671] Successfully connected to ip 10.x.x.x, port 88 using TCP\n**[ 1099] FAILURE: Could not authenticate as\n** 'user@contoso.com': CIFS server account password does\n** not match password stored in Active Directory\n** (KRB5KDC_ERR_PREAUTH_FAILED)\n. "}]}` | AD 接続への参加時に入力したパスワードが正しいことを確認します。 |
| SMB またはデュアルプロトコル ボリュームの作成に失敗し、次のエラーが発生します。 <br> `{"code":"DeploymentFailed","message":"At least one resource deployment operation failed. Please list deployment operations for details. Please see https://aka.ms/DeployOperations for usage details.","details":[{"code":"InternalServerError","message":"Error when creating - Failed to create the Active Directory machine account \"SMBTESTAD-D9A2\". Reason: SecD Error: ou not found Details: Error: Machine account creation procedure failed\n [ 561] Loaded the preliminary configuration.\n [ 665] Successfully connected to ip 10.x.x.x, port 88 using TCP\n [ 1039] Successfully connected to ip 10.x.x.x, port 389 using TCP\n**[ 1147] FAILURE: Specifed OU 'OU=AADDC Com' does not exist in\n** contoso.com\n. "}]}` | AD 接続への参加時に指定した組織単位パスが正しいことを確認します。 Azure ADDS を使用している場合は、組織単位パスが `OU=AADDC Computers` であることを確認します。 |
| SMB またはデュアルプロトコル ボリュームの作成に失敗し、次のエラーが発生します。 <br> `Failed to create the Active Directory machine account \"SMB-ANF-VOL. Reason: LDAP Error: Local error occurred Details: Error: Machine account creation procedure failed. [nnn] Loaded the preliminary configuration. [nnn] Successfully connected to ip 10.x.x.x, port 88 using TCP [nnn] Successfully connected to ip 10.x.x.x, port 389 using [nnn] Entry for host-address: 10.x.x.x not found in the current source: FILES. Ignoring and trying next available source [nnn] Source: DNS unavailable. Entry for host-address:10.x.x.x found in any of the available sources\n*[nnn] FAILURE: Unable to SASL bind to LDAP server using GSSAPI: local error [nnn] Additional info: SASL(-1): generic failure: GSSAPI Error: Unspecified GSS failure. Minor code may provide more information (Cannot determine realm for numeric host address) [nnn] Unable to connect to LDAP (Active Directory) service on contoso.com (Error: Local error) [nnn] Unable to make a connection (LDAP (Active Directory):contosa.com, result: 7643. `  | AD ホスト マシンのポインター (PTR) レコードが DNS サーバーに存在しない可能性があります。 DNS サーバーに逆引き参照ゾーンを作成してから、その逆引き参照ゾーンに AD ホストマシンの PTR レコードを追加する必要があります。 <br> たとえば、AD マシンの IP アドレスが `10.x.x.x` であり、AD マシンのホスト名 (`hostname` コマンドを使用して確認されたもの) が `AD1` であり、ドメイン名が `contoso.com` であるとします。  逆引き参照ゾーンに追加される PTR レコードは `10.x.x.x` -> `contoso.com` になります。   | 
| SMB またはデュアルプロトコル ボリュームの作成に失敗し、次のエラーが発生します。 <br> `Failed to create the Active Directory machine account \"SMB-ANF-VOL\". Reason: Kerberos Error: KDC has no support for encryption type Details: Error: Machine account creation procedure failed [nnn]Loaded the preliminary configuration. [nnn]Successfully connected to ip 10.x.x.x, port 88 using TCP [nnn]FAILURE: Could not authenticate as 'contosa.com': KDC has no support for encryption type  (KRB5KDC_ERR_ETYPE_NOSUPP) ` | Active Directory 接続とサービス アカウントの両方で [AES 暗号化](/azure/azure-netapp-files/create-active-directory-connections#create-an-active-directory-connection)が有効になっていることを確認します。 |
| SMB またはデュアルプロトコル ボリュームの作成に失敗し、次のエラーが発生します。 <br> `Failed to create the Active Directory machine account \"SMB-NTAP-VOL\". Reason: LDAP Error: Strong authentication is required Details: Error: Machine account creation procedure failed\n [ 338] Loaded the preliminary configuration.\n [ nnn] Successfully connected to ip 10.x.x.x, port 88 using TCP\n [ nnn ] Successfully connected to ip 10.x.x.x, port 389 using TCP\n [ 765] Unable to connect to LDAP (Active Directory) service on\n dc51.area51.com (Error: Strong(er) authentication\n required)\n*[ nnn] FAILURE: Unable to make a connection (LDAP (Active\n* Directory):contoso.com), result: 7609\n. "` | LDAP 署名オプションは選択されていませんが、AD クライアントは LDAP 署名を持っています。  [LDAP 署名を有効化](create-active-directory-connections.md#create-an-active-directory-connection)してから、再試行してください。 | 

## <a name="next-steps"></a>次のステップ  

* [SMB ボリュームを作成する](azure-netapp-files-create-volumes-smb.md)
* [デュアルプロトコル ボリュームを作成する](create-volumes-dual-protocol.md)
* [Azure NetApp Files 用に NFS クライアントを構成する](configure-nfs-clients.md)
