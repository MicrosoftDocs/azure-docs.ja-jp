---
title: "Windows セキュリティを使用して Windows 上で実行されるクラスターを保護する | Microsoft Docs"
description: "Windows セキュリティを使用して、Windows 上で実行されるスタンドアロン クラスターでノード間またはクライアントとノード間のセキュリティを構成する方法について説明します。"
services: service-fabric
documentationcenter: .net
author: rwike77
manager: timlt
editor: 
ms.assetid: ce3bf686-ffc4-452f-b15a-3c812aa9e672
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 01/17/2017
ms.author: ryanwi
translationtype: Human Translation
ms.sourcegitcommit: b4802009a8512cb4dcb49602545c7a31969e0a25
ms.openlocfilehash: b8842047351da6fdb547e0b09492b89fad962bee
ms.lasthandoff: 03/29/2017


---
# <a name="secure-a-standalone-cluster-on-windows-by-using-windows-security"></a>Windows セキュリティを使用して Windows 上のスタンドアロン クラスターを保護する
Service Fabric クラスターへの未承認のアクセスを防ぐには、クラスターをセキュリティで保護する必要があります。 クラスターで運用環境のワークロードが実行されている場合は、セキュリティが特に重要となります。 この記事では、*ClusterConfig.JSON* ファイルで Windows セキュリティを使用して、ノード間およびクライアントとノード間のセキュリティを構成する方法について説明します。  このプロセスは、[Windows 上で実行されるスタンドアロン クラスターの作成](service-fabric-cluster-creation-for-windows-server.md)に関する記事のセキュリティの構成手順に対応しています。 Service Fabric における Windows セキュリティの使用の詳細については、[クラスターのセキュリティ シナリオ](service-fabric-cluster-security.md)に関する記事をご覧ください。

> [!NOTE]
> セキュリティを選択した後で別のセキュリティにクラスターをアップグレードすることはできないため、ノード間のセキュリティの選択は慎重に検討してください。 セキュリティの選択を変更するには、クラスター全体を再構築する必要があります。
>
>

## <a name="configure-windows-security-using-gmsa"></a>gMSA を使用して Windows セキュリティを構成する  
<seg>
  [Microsoft.Azure.ServiceFabric.WindowsServer<version>.zip](http://go.microsoft.com/fwlink/?LinkId=730690) スタンドアロン クラスター パッケージと共にダウンロードされるサンプルの構成ファイル *ClusterConfig.gMSA.Windows.MultiMachine.JSON* には、[グループ管理サービス アカウント (gMSA)](https://technet.microsoft.com/library/hh831782.aspx) を使用して Windows セキュリティを構成するためのテンプレートが含まれています。</seg>  

```  
"security": {  
            "ServerCredentialType": "Windows",  
            "WindowsIdentities": {  
                "ClustergMSAIdentity": "accountname@fqdn"  
                "ClusterSPN": "fqdn"  
                "ClientIdentities": [  
                    {  
                        "Identity": "domain\\username",  
                        "IsAdmin": true  
                    }  
                ]  
            }  
        }  
```  
  
| **構成設定** | **説明** |  
| --- | --- |  
| WindowsIdentities |クラスターとクライアントの ID が含まれます。 |  
| ClustergMSAIdentity |ノード間のセキュリティを構成します。 グループ管理サービス アカウント。 |  
| ClusterSPN |gMSA アカウントの完全修飾ドメイン SPN。|  
| ClientIdentities |クライアントとノードの間のセキュリティを構成します。 クライアントのユーザー アカウントの配列です。 |  
| ID |ドメイン ユーザーであるクライアントの ID。 |  
| IsAdmin |true の場合は、ドメイン ユーザーが管理者クライアント アクセスを持つことを示し、false の場合は、ユーザー クライアント アクセスを持つことを示します。 |  
  
[ノード間のセキュリティ](service-fabric-cluster-security.md#node-to-node-security)は、Service Fabric が gMSA で実行する必要があるときに、**ClustergMSAIdentity** を設定することによって構成されます。 ノード間の信頼関係を構築するには、各ノードが互いを認識する必要があります。 これを行うには 2 つの方法があります。クラスター内のすべてのノードを含むグループ管理サービス アカウントを指定する方法と、クラスター内のすべてのノードのドメイン コンピューター グループを指定する方法です。 強くお勧めするのは、[グループ管理サービス アカウント (gMSA)](https://technet.microsoft.com/library/hh831782.aspx) を使用する方法です。特に、クラスターが大きい場合 (ノードが 10 個以上) またはクラスターの拡大と縮小が予想される場合には、この方法が推奨されます。  
この方法なら、メンバーの追加と削除に必要なアクセス権がクラスター管理者から付与されたドメイン グループを作成する必要がありません。 これらのアカウントは、パスワードの自動管理でも役立ちます。 詳細については、「[グループ管理サービス アカウントの概要](http://technet.microsoft.com/library/jj128431.aspx)」を参照してください。  
 
[クライアントとノードの間のセキュリティ](service-fabric-cluster-security.md#client-to-node-security)は **ClientIdentities** を使用して構成します。 クライアントとクラスターの間の信頼を確立するためには、どのクライアントの ID なら信頼できるのかを認識できるようにクラスターを構成する必要があります。 これを行うには 2 つの方法があります。接続可能なドメイン グループ ユーザーを指定する方法と、接続可能なドメイン ノード ユーザーを指定する方法です。 Service Fabric では、Service Fabric クラスターに接続されるクライアントのために、管理者用とユーザー用の 2 つの異なるアクセス コントロールの種類がサポートされています。 アクセス制御を使用すると、クラスター管理者は、ユーザーのグループごとに特定の種類のクラスター操作へのアクセスを制限し、クラスターのセキュリティを強化できます。  管理者には、管理機能へのフル アクセス権 (読み取り/書き込み機能など) があります。 ユーザーには、既定で管理機能 (クエリ機能など) と、アプリケーションとサービスを解決する機能への読み取りアクセス権のみがあります。 アクセス コントロールの詳細については、「[ロールベースのアクセス制御 (Service Fabric クライアント用)](service-fabric-cluster-security-roles.md)」を参照してください。  
 
次の **security** セクションの例では、gMSA を使用して Windows セキュリティを構成し、*ServiceFabric.clusterA.contoso.com* 内のコンピューターがクラスターに属することと、*CONTOSO\usera* が管理者クライアント アクセスを持つことを指定しています。  
  
```  
"security": {  
    "WindowsIdentities": {  
        "ClustergMSAIdentity" : "ServiceFabric.clusterA.contoso.com",  
        "ClusterSPN" : "clusterA.contoso.com",  
        "ClientIdentities": [{  
            "Identity": "CONTOSO\\usera",  
            "IsAdmin": true  
        }]  
    }  
}  
```  
  
## <a name="configure-windows-security-using-a-machine-group"></a>コンピューター グループを使用して Windows セキュリティを構成する  
<seg>
  [Microsoft.Azure.ServiceFabric.WindowsServer<version>.zip](http://go.microsoft.com/fwlink/?LinkId=730690) スタンドアロン クラスター パッケージと共にダウンロードされるサンプルの構成ファイル *ClusterConfig.Windows.MultiMachine.JSON* には、Windows セキュリティを構成するためのテンプレートが含まれています。</seg>  Windows セキュリティは **Properties** セクション内で構成します。 

```
"security": {
            "ClusterCredentialType": "Windows",
            "ServerCredentialType": "Windows",
            "WindowsIdentities": {
                "ClusterIdentity" : "[domain\machinegroup]",
                "ClientIdentities": [{
                    "Identity": "[domain\username]",
                    "IsAdmin": true
                }]
            }
        }
```

| **構成設定** | **説明** |
| --- | --- |
| ClusterCredentialType |ClusterIdentity が Active Directory コンピューター グループ名を指定している場合、**ClusterCredentialType** は*Windows* に設定されます。 |  
| ServerCredentialType |クライアントの Windows セキュリティを有効にするには、*Windows* に設定します。<br /><br />これは、クラスターのクライアントとクラスター自体が Active Directory ドメイン内で実行されていることを示します。 |  
| WindowsIdentities |クラスターとクライアントの ID が含まれます。 |  
| ClusterIdentity |コンピューター グループ名 (domain\machinegroup) を使用して、ノード間のセキュリティを構成します。 |  
| ClientIdentities |クライアントとノードの間のセキュリティを構成します。 クライアントのユーザー アカウントの配列です。 |  
| ID |クライアント ID のドメイン ユーザー (domain\username) を追加します。 |  
| IsAdmin |true に設定すると、ドメイン ユーザーが管理者クライアント アクセスを持つことを示し、false に設定すると、ユーザー クライアント アクセスを持つことを示します。 |  

Active Directory ドメイン内のコンピューター グループを使用する場合、[ノード間セキュリティ](service-fabric-cluster-security.md#node-to-node-security)は、**ClusterIdentity** を設定することで構成されます。 詳細については、[Active Directory でのグループの作成](https://msdn.microsoft.com/library/aa545347(v=cs.70).aspx)に関する記事を参照してください。

[クライアントとノード間のセキュリティ](service-fabric-cluster-security.md#client-to-node-security)は、**ClientIdentities** を使用して構成します。 クライアントとクラスター間の信頼を確立するには、クラスターが信頼できるクライアント ID を認識できるようにクラスターを構成する必要があります。 次の 2 つの方法で信頼を確立できます。

- 接続可能なドメイン グループ ユーザーを指定する。
- 接続可能なドメイン ノード ユーザーを指定する。

Service Fabric では、Service Fabric クラスターに接続されるクライアントのために、管理者用とユーザー用の 2 つの異なるアクセス コントロールの種類がサポートされています。 アクセス制御を使用すると、クラスター管理者は、ユーザーのグループごとに特定の種類のクラスター操作へのアクセスを制限して、クラスターのセキュリティを強化できます。  管理者には、管理機能へのフル アクセス権 (読み取り/書き込み機能など) があります。 ユーザーには、既定で管理機能 (クエリ機能など) と、アプリケーションとサービスを解決する機能への読み取りアクセス権のみがあります。  

次の **security** セクションの例では、Windows セキュリティを構成し、*ServiceFabric/clusterA.contoso.com* 内のコンピューターがクラスターに属することと、*CONTOSO\usera* が管理者クライアント アクセスを持つことを指定しています。

```
"security": {
    "ClusterCredentialType": "Windows",
    "ServerCredentialType": "Windows",
    "WindowsIdentities": {
        "ClusterIdentity" : "ServiceFabric/clusterA.contoso.com",
        "ClientIdentities": [{
            "Identity": "CONTOSO\\usera",
            "IsAdmin": true
        }]
    }
},
```

> [!NOTE]
> Service Fabric は、ドメイン コントローラー上にデプロイしないでください。 コンピューター グループまたは管理されたサービス アカウント (gMSA) グループを使用する場合は、ClusterConfig.json にドメイン コントローラーの IP アドレスが含まれていないことを確認します。
>
>

## <a name="next-steps"></a>次のステップ
*ClusterConfig.JSON* ファイルで Windows セキュリティを構成したら、 [Windows 上で実行されるスタンドアロン クラスターの作成](service-fabric-cluster-creation-for-windows-server.md)に関する記事で説明されているクラスター作成処理を再開します。

ノード間のセキュリティ、クライアントとノード間のセキュリティ、ロールベースのアクセス制御の詳細については、[クラスターのセキュリティ シナリオ](service-fabric-cluster-security.md)に関する記事をご覧ください。

PowerShell または FabricClient を使用した接続の例については、「[セキュリティ保護されたクラスターに接続する](service-fabric-connect-to-secure-cluster.md)」をご覧ください。

