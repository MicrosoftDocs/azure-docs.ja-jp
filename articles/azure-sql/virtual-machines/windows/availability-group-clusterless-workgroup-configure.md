---
title: ドメインに依存しないワークグループ可用性グループを構成する
description: Azure の SQL Server 仮想マシンで、Active Directory ドメインに依存しないワークグループ Always On 可用性グループを構成する方法について説明します。
services: virtual-machines-windows
documentationcenter: na
author: MashaMSFT
editor: ''
tags: azure-service-management
ms.assetid: 53981f7e-8370-4979-b26a-93a5988d905f
ms.service: virtual-machines-sql
ms.subservice: hadr
ms.topic: how-to
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 01/29/2020
ms.author: mathoma
ms.openlocfilehash: 0f194101720481f71434709c467d0e3130a0f1f9
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "97359457"
---
# <a name="configure-a-workgroup-availability-group"></a>ワークグループ可用性グループを構成する 
[!INCLUDE[appliesto-sqlvm](../../includes/appliesto-sqlvm.md)]

この記事では、Always On 可用性グループを使用して、Active Directory ドメインに依存しないクラスターを作成するために必要な手順について説明します。これは、ワークグループ クラスターとも呼ばれます。 この記事では、ワークグループと可用性グループの準備と構成に関連する手順に重点を置いて説明します。クラスターの作成方法や可用性グループのデプロイ方法など、他の記事で説明されている手順については省略します。 


## <a name="prerequisites"></a>前提条件

ワークグループ可用性グループを構成するには、次のものが必要です。
- 静的 IP アドレスを使用して同じ可用性セットまたは異なる可用性ゾーンにデプロイされている、SQL Server 2016 (またはそれ以降) を実行中の 2 台以上の Windows Server 2016 (またはそれ以降) の仮想マシン。 
- サブネット上に最低 4 つの空き IP アドレスがあるローカル ネットワーク。 
- SQL Server 内で sysadmin 権限も持っている管理者グループ内の各コンピューターのアカウント。 
- 開いているポート: TCP 1433、TCP 5022、TCP 59999。 

参考のために、この記事では次のパラメーターを使用しますが、必要に応じて変更できます。 

| **名前** | **パラメーター** |
| :------ | :---------------------------------- |
| **Node1**   | AGNode1 (10.0.0.4) |
| **Node2**   | AGNode2 (10.0.0.5) |
| **[Cluster name]\(クラスター名\)** | AGWGAG (10.0.0.6) |
| **リスナー** | AGListener (10.0.0.7) | 
| **DNS サフィックス** | ag.wgcluster.example.com | 
| **ワークグループ名** | AGWorkgroup | 
| &nbsp; | &nbsp; |

## <a name="set-a-dns-suffix"></a>DNS サフィックスを設定する 

この手順では、両方のサーバーの DNS サフィックスを構成します。 たとえば、「 `ag.wgcluster.example.com` 」のように入力します。 これにより、接続するオブジェクトの名前をネットワーク内の完全修飾アドレスとして使用できます (例: `AGNode1.ag.wgcluster.example.com`)。 

DNS サフィックスを構成するには、次の手順に従います。

1. お使いの最初のノードに RDP 接続し、サーバー マネージャーを開きます。 
1. **[ローカル サーバー]** を選択した後、 **[コンピューター名]** から仮想マシンの名前を選択します。 
1. **[To rename this computer...]\(このコンピューターの名前を変更するには...\)** の下の **[変更...]** を選択します。 
1. ワークグループ名の名前を、`AGWORKGROUP` などの意味のある名前に変更します。 

   ![ワークグループ名を変更する](./media/availability-group-clusterless-workgroup-configure/1-change-workgroup-name.png)

1. **[詳細...]** を選択して、 **[DNS サフィックスと NetBIOS コンピューター名]** ダイアログ ボックスを開きます。 
1. **[このコンピュータのプライマリ DNS サフィックス]** の下に DNS サフィックスの名前 (`ag.wgcluster.example.com` など) を入力し、 **[OK]** を選択します。 

   ![スクリーンショットには、[DNS サフィックス] ダイアログ ボックスと [NetBIOS コンピュータ名] ダイアログ ボックスが表示されています。このダイアログ ボックスで値を入力できます。](./media/availability-group-clusterless-workgroup-configure/2-add-dns-suffix.png)

1. **[フル コンピューター名]** に DNS サフィックスが表示されていることを確認した後、 **[OK]** を選択して変更を保存します。 

   ![スクリーンショットは、完全なコンピューター名を表示する場所を示しています。](./media/availability-group-clusterless-workgroup-configure/3-confirm-full-computer-name.png)

1. 再起動を求めるメッセージが表示されたら、サーバーを再起動します。 
1. 可用性グループに使用する他のすべてのノードで、これらの手順を繰り返します。 

## <a name="edit-a-host-file"></a>ホスト ファイルを編集する

Active Directory がないため、Windows 接続を認証する方法はありません。 そのため、テキスト エディターでホスト ファイルを編集することで信頼を割り当てます。 

ホスト ファイルを編集するには、次の手順を実行します。

1. お使いの仮想マシンに RDP 接続します。 
1. **エクスプローラー** を使用して、`c:\windows\system32\drivers\etc` に移動します。 
1. **hosts** ファイルを右クリックし、**メモ帳** (またはその他のテキスト エディター) を使用してファイルを開きます。
1. ファイルの末尾に、次のように、各ノード、可用性グループ、およびリスナーのエントリを `IP Address, DNS Suffix #comment` の形式で追加します。 

   ```
   10.0.0.4 AGNode1.ag.wgcluster.example.com #Availability group node
   10.0.0.5 AGNode2.ag.wgcluster.example.com #Availability group node
   10.0.0.6 AGWGAG.ag.wgcluster.example.com #Cluster IP
   10.0.0.7 AGListener.ag.wgcluster.example.com #Listener IP
   ```
 
   ![IP アドレス、クラスター、およびリスナーのエントリをホスト ファイルに追加する](./media/availability-group-clusterless-workgroup-configure/4-host-file.png)

## <a name="set-permissions"></a>アクセス許可を設定する

アクセス許可を管理する Active Directory がないため、組み込まれていないローカル管理者アカウントによるクラスターの作成を手動で許可する必要があります。 

これを行うには、すべてのノードの管理 PowerShell セッションで次の PowerShell コマンドレットを実行します。 

```PowerShell

new-itemproperty -path HKLM:\SOFTWARE\Microsoft\Windows\CurrentVersion\Policies\System -Name LocalAccountTokenFilterPolicy -Value 1
```

## <a name="create-the-failover-cluster"></a>フェールオーバー クラスターを作成する

この手順では、フェールオーバー クラスターを作成します。 これらの手順に慣れていない場合は、[フェールオーバー クラスターのチュートリアル](failover-cluster-instance-storage-spaces-direct-manually-configure.md)に従うことができます。

このチュートリアルとワークグループ クラスターに対して行う必要があることの主な相違点は次のとおりです。
- クラスターの検証を実行するときに、 **[ストレージ]** と **[記憶域スペース ダイレクト]** をオフにします。 
- クラスターにノードを追加するときは、完全修飾名を追加します。次に例を示します。
   - `AGNode1.ag.wgcluster.example.com`
   - `AGNode2.ag.wgcluster.example.com`
- **[使用可能な記憶域をすべてクラスターに追加する]** をオフにします。 

クラスターが作成されたら、静的なクラスター IP アドレスを割り当てます。 これを行うには、次のステップに従います。

1. いずれかのノードで、**フェールオーバー クラスター マネージャー** を開いてクラスターを選択し、 **[クラスター コア リソース]** の下にある **[名前: \<ClusterNam>]** を右クリックしてから、 **[プロパティ]** を選択します。 

   ![クラスター名のプロパティの起動](./media/availability-group-clusterless-workgroup-configure/5-launch-cluster-name-properties.png)

1. **[IP アドレス]** から IP アドレスを選択し、 **[編集]** を選択します。 
1. **[静的を使用]** を選択し、クラスターの IP アドレスを指定した後、 **[OK]** を選択します。 

   ![クラスターの静的 IP アドレスを指定する](./media/availability-group-clusterless-workgroup-configure/6-provide-static-ip-for-cluster.png)

1. 設定が正しく表示されていることを確認し、 **[OK]** を選択して保存します。

   ![クラスターのプロパティを確認する](./media/availability-group-clusterless-workgroup-configure/7-verify-cluster-properties.png)

## <a name="create-a-cloud-witness"></a>クラウド監視を作成する 

この手順では、クラウド共有監視を構成します。 この手順がよくわからない場合は、「[フェールオーバー クラスターのクラウド監視を展開する](/windows-server/failover-clustering/deploy-cloud-witness)」参照してください。 

## <a name="enable-the-availability-group-feature"></a>可用性グループ機能を有効にする 

この手順では、可用性グループ機能を有効にします。 この手順に慣れていない場合は、[可用性グループのチュートリアル](availability-group-manually-configure-tutorial.md#enable-availability-groups)を参照してください。 

## <a name="create-keys-and-certificates"></a>キーと証明書を作成する

この手順では、SQL ログイン時に暗号化されたエンドポイントで使用される証明書を作成します。 証明書のバックアップを保持するフォルダーを各ノードに作成します (`c:\certs` など)。 

最初のノードを構成するには、次の手順に従います。 

1. **SQL Server Management Studio** を開き、最初のノード (`AGNode1` など) に接続します。 
1. **[新しいクエリ]** ウィンドウを開き、複雑でセキュリティが強化されたパスワードに更新した後、次の Transact-SQL (T-SQL) ステートメントを実行します。

   ```sql
   USE master;  
   CREATE MASTER KEY ENCRYPTION BY PASSWORD = 'PassWOrd123!';  
   GO

   --create a cert from the master key
   USE master;  
   CREATE CERTIFICATE AGNode1Cert   
      WITH SUBJECT = 'AGNode1 Certificate';  
   GO  

   --Backup the cert and transfer it to AGNode2
   BACKUP CERTIFICATE AGNode1Cert TO FILE = 'C:\certs\AGNode1Cert.crt';  
   GO  
   ```

1. 次に、HADR エンドポイントを作成し、次の Transact-SQL (T-SQL) ステートメントを実行することで、認証に証明書を使用します。

   ```sql
   --CREATE or ALTER the mirroring endpoint
   CREATE ENDPOINT hadr_endpoint  
      STATE = STARTED  
      AS TCP (  
         LISTENER_PORT=5022  
         , LISTENER_IP = ALL  
      )   
      FOR DATABASE_MIRRORING (   
         AUTHENTICATION = CERTIFICATE AGNode1Cert  
         , ENCRYPTION = REQUIRED ALGORITHM AES  
         , ROLE = ALL  
      );  
   GO  
   ```

1. **エクスプローラー** を使用して、証明書があるファイルの場所 (`c:\certs` など) に移動します。 
1. 最初のノードから証明書のコピーを手動で作成し (`AGNode1Cert.crt` など)、それを 2 番目のノードの同じ場所に転送します。 

2 番目のノードを構成するには、次の手順に従います。 

1. **SQL Server Management Studio** を使用して 2 番目のノード (`AGNode2` など) に接続します。 
1. **[新しいクエリ]** ウィンドウで、複雑でセキュリティが強化されたパスワードに更新した後、次の Transact-SQL (T-SQL) ステートメントを実行します。 

   ```sql
   USE master;  
   CREATE MASTER KEY ENCRYPTION BY PASSWORD = 'PassWOrd123!';  
   GO 
   
   --create a cert from the master key
   USE master;  
   CREATE CERTIFICATE AGNode2Cert   
      WITH SUBJECT = 'AGNode2 Certificate';  
   GO  
   --Backup the cert and transfer it to AGNode1
   BACKUP CERTIFICATE AGNode2Cert TO FILE = 'C:\certs\AGNode2Cert.crt';  
   GO
   ```

1. 次に、HADR エンドポイントを作成し、次の Transact-SQL (T-SQL) ステートメントを実行することで、認証に証明書を使用します。

   ```sql
   --CREATE or ALTER the mirroring endpoint
   CREATE ENDPOINT hadr_endpoint  
      STATE = STARTED  
      AS TCP (  
         LISTENER_PORT=5022  
         , LISTENER_IP = ALL  
      )   
      FOR DATABASE_MIRRORING (   
         AUTHENTICATION = CERTIFICATE AGNode2Cert  
         , ENCRYPTION = REQUIRED ALGORITHM AES  
         , ROLE = ALL  
      );  
   GO  
   ```

1. **エクスプローラー** を使用して、証明書があるファイルの場所 (`c:\certs` など) に移動します。 
1. 2 番目のノードからの証明書のコピーを手動で作成し (`AGNode2Cert.crt` など)、それを最初のノードと同じ場所に転送します。 

クラスターに他のノードがある場合は、それぞれの証明書の名前を変更しながら、これらの手順を繰り返します。 

## <a name="create-logins"></a>ログインを作成する

ノード間でデータを同期するために証明書認証が使用されます。 これを可能にするには、他のノード用のログインを作成し、ログイン用のユーザーを作成します。バックアップされた証明書を使用するログイン用の証明書を作成した後、ミラーリング エンドポイントでの接続を許可します。 

これを行うには、最初のノード (`AGNode1` など) で、まず次の Transact-SQL (T-SQL) クエリを実行します。 

```sql
--create a login for the AGNode2
USE master;  
CREATE LOGIN AGNode2_Login WITH PASSWORD = 'PassWord123!';  
GO  

--create a user from the login
CREATE USER AGNode2_User FOR LOGIN AGNode2_Login;  
GO  

--create a certificate that the login uses for authentication
CREATE CERTIFICATE AGNode2Cert  
   AUTHORIZATION AGNode2_User  
   FROM FILE = 'C:\certs\AGNode2Cert.crt'  
GO 

--grant connect for login
GRANT CONNECT ON ENDPOINT::hadr_endpoint TO [AGNode2_login];  
GO
```

次に、2 番目のノード (`AGNode2` など) で、次の Transact-SQL (T-SQL) クエリを実行します。 

```sql
--create a login for the AGNode1
USE master;  
CREATE LOGIN AGNode1_Login WITH PASSWORD = 'PassWord123!';  
GO  

--create a user from the login
CREATE USER AGNode1_User FOR LOGIN AGNode1_Login;  
GO  

--create a certificate that the login uses for authentication
CREATE CERTIFICATE AGNode1Cert  
   AUTHORIZATION AGNode1_User  
   FROM FILE = 'C:\certs\AGNode1Cert.crt'  
GO 

--grant connect for login
GRANT CONNECT ON ENDPOINT::hadr_endpoint TO [AGNode1_login];  
GO
```

クラスターに他のノードがある場合は、それぞれの証明書とユーザーの名前を変更しながら、これらの手順を繰り返します。 

## <a name="configure-an-availability-group"></a>可用性グループを構成する

この手順では、可用性グループを構成して、そこにデータベースを追加します。 この時点ではリスナーを作成しないでください。 この手順に慣れていない場合は、[可用性グループのチュートリアル](availability-group-manually-configure-tutorial.md#create-the-availability-group)を参照してください。 フェールオーバーとフェールバックを開始して、すべてが正常に動作していることを確認します。 

   > [!NOTE]
   > 同期プロセス中にエラーが発生した場合は、最初のノード (`AGNode1` など) 上にクラスター リソースを作成するために `NT AUTHORITY\SYSTEM` sysadmin 権限を一時的に付与することが必要になる場合があります。 

## <a name="configure-a-load-balancer"></a>ロード バランサーを構成する

この最後の手順では、[Azure portal](availability-group-load-balancer-portal-configure.md) または [PowerShell](availability-group-listener-powershell-configure.md) を使用してロード バランサーを構成します。


## <a name="next-steps"></a>次のステップ

[Az SQL VM CLI](./availability-group-az-commandline-configure.md) を使用して、可用性グループを構成することもできます。