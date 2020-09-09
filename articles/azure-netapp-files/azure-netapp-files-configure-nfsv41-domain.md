---
title: Azure NetApp Files 用に NFSv4.1 の既定のドメインを構成する | Microsoft Docs
description: Azure NetApp Files で NFSv4.1 を使用するように NFS クライアントを構成する方法について説明します。
documentationcenter: ''
author: b-juche
manager: ''
editor: ''
ms.assetid: ''
ms.service: azure-netapp-files
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.date: 11/08/2019
ms.author: b-juche
ms.openlocfilehash: dda911add42568e76160e4233502a1f4f550520d
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/02/2020
ms.locfileid: "85483722"
---
# <a name="configure-nfsv41-default-domain-for-azure-netapp-files"></a>Azure NetApp Files 用に NFSv4.1 の既定のドメインを構成する

NFSv4 では、認証ドメインの概念が導入されています。 Azure NetApp Files は、現在、サービスから NFS クライアントへのルート専用ユーザー マッピングをサポートしています。 Azure NetApp Files で NFSv4.1 機能を使用するには、NFS クライアントを更新する必要があります。

## <a name="default-behavior-of-usergroup-mapping"></a>ユーザー/グループ マッピングの既定の動作

NFSv4 ドメインは `localdomain` に設定されているため、ルート マッピングの既定値は `nobody` ユーザーになります。 Azure NetApp Files NFSv4.1 ボリュームをルートとしてマウントすると、次のようなファイルのアクセス許可が表示されます。  

![NFSv4.1 のユーザー/グループ マッピングの既定の動作](../media/azure-netapp-files/azure-netapp-files-nfsv41-default-behavior-user-group-mapping.png)

上の例に示ように、`file1` のユーザーは `root` であるべきですが、既定では `nobody` にマップされています。  この記事では、`file1` ユーザーを `root` に設定する方法について説明します。  

## <a name="steps"></a>手順 

1. NFS クライアントで `/etc/idmapd.conf` ファイルを編集します。   
    行 `#Domain` をコメント解除し (つまり、行から `#` を削除する)、値 `localdomain` を `defaultv4iddomain.com` に変更します。 

    初期構成: 
    
    ![NFSv4.1 の初期構成](../media/azure-netapp-files/azure-netapp-files-nfsv41-initial-config.png)

    更新された構成:
    
    ![NFSv4.1 の更新された構成](../media/azure-netapp-files/azure-netapp-files-nfsv41-updated-config.png)

2. 現在マウントされている NFS ボリュームをマウント解除します。
3. `/etc/idmapd.conf` ファイルを更新します。
4. ホストで `rpcbind` サービスを再起動する (`service rpcbind restart`) か、単にホストを再起動します。
5. 必要に応じて NFS ボリュームをマウントします。   

    「[Windows または Linux 仮想マシンのボリュームをマウント/マウント解除する](azure-netapp-files-mount-unmount-volumes-for-virtual-machines.md)」を参照してください。 

次の例は、結果として得られるユーザー/グループの変更を示しています。 

![NFSv4.1 の結果の構成](../media/azure-netapp-files/azure-netapp-files-nfsv41-resulting-config.png)

この例に示すように、ユーザー/グループは `nobody` から `root` に変更されました。

## <a name="behavior-of-other-non-root-users-and-groups"></a>他の (ルート以外の) ユーザーとグループの動作

Azure NetApp Files は、NFSv4.1 ボリューム内のファイルまたはフォルダーに関連付けられたアクセス許可を持つローカル ユーザー (ホスト上でローカルに作成されたユーザー) をサポートします。 ただし、このサービスでは、複数のノード間でのユーザー/グループのマッピングは現時点ではサポートされていません。 したがって、あるホスト上に作成されたユーザーは、既定で別のホスト上に作成されたユーザーにマップされません。 

次の例で、`Host1` には 3 つの既存のテスト ユーザー アカウント (`testuser01`、`testuser02`、`testuser03`) があります。 

![NFSv4.1 の結果の構成](../media/azure-netapp-files/azure-netapp-files-nfsv41-host1-users.png)

`Host2` で、テスト ユーザー アカウントは作成されていないが、同じボリュームが両方のホストにマウントされていることに注意してください。

![NFSv4.1 の結果の構成](../media/azure-netapp-files/azure-netapp-files-nfsv41-host2-users.png)

## <a name="next-step"></a>次のステップ 

[Windows または Linux 仮想マシンのボリュームをマウント/マウント解除する](azure-netapp-files-mount-unmount-volumes-for-virtual-machines.md)

