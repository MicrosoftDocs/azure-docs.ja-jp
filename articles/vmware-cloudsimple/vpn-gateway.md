---
title: Azure VMware Solution by CloudSimple - VPN ゲートウェイを設定する
description: ポイント対サイト VPN ゲートウェイとサイト間 VPN ゲートウェイを設定し、オンプレミス ネットワークと CloudSimple プライベート クラウドの間に接続を作成する方法について説明します
author: Ajayan1008
ms.author: v-hborys
ms.date: 08/14/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 78f78bfbf885cbae7708a75cb54ce2d41e7b526c
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "97899101"
---
# <a name="set-up-vpn-gateways-on-cloudsimple-network"></a>CloudSimple ネットワーク上の VPN ゲートウェイを設定する

VPN ゲートウェイを使用すると、オンプレミス ネットワークおよびクライアント コンピューターからリモートで CloudSimple ネットワークに接続できます。 オンプレミス ネットワークと CloudSimple ネットワーク間の VPN 接続により、プライベート クラウド上の vCenter とワークロードにアクセスできます。 CloudSimple は、サイト間 VPN ゲートウェイとポイント対サイト VPN ゲートウェイの両方をサポートしています。

## <a name="vpn-gateway-types"></a>VPN ゲートウェイの種類

* **サイト対サイト VPN** 接続を使用すると、オンプレミスのサービスにアクセスするようにプライベート クラウドのワークロードを設定できます。 プライベート クラウドの vCenter に対して認証を行うための ID ソースとして、オンプレミスの Active Directory を使用することもできます。  現時点では、**ポリシーベースの VPN** の種類のみがサポートされています。
* **ポイント対サイト VPN** 接続は、お使いのコンピューターから自分のプライベート クラウドに接続する最も簡単な方法です。 リモートからプライベート クラウドに接続するには、ポイント対サイト VPN 接続を使用します。 ポイント対サイト VPN 接続用のクライアントのインストールの詳細については、[プライベート クラウドへの VPN 接続の構成](set-up-vpn.md)に関する記事を参照してください。

リージョンでは、1 つのポイント対サイト VPN ゲートウェイと 1 つのサイト間 VPN ゲートウェイを作成できます。

## <a name="automatic-addition-of-vlansubnets"></a>VLAN/サブネットの自動追加

CloudSimple VPN ゲートウェイは、VPN ゲートウェイに VLAN/サブネットを追加するためのポリシーを提供します。  ポリシーを使用すると、管理 VLAN/サブネットとユーザー定義の VLAN/サブネットに対して異なる規則を指定できます。  管理 VLAN/サブネットの規則は、新しく作成するすべてのプライベート クラウドに適用されます。  ユーザー定義の VLAN/サブネットの規則を使用すると、既存または新規のプライベート クラウドに新しい VLAN/サブネットを自動的に追加できます。サイト間 VPN ゲートウェイの場合、各接続のポリシーを定義します。

VPN ゲートウェイに VLAN/サブネットを追加するためのポリシーは、サイト間 VPN ゲートウェイとポイント対サイト VPN ゲートウェイの両方に適用されます。

## <a name="automatic-addition-of-users"></a>ユーザーの自動追加

ポイント対サイト VPN ゲートウェイを使用すると、新しいユーザーの自動追加ポリシーを定義できます。 既定では、サブスクリプションのすべての所有者と共同作成者に、CloudSimple ポータルへのアクセス権があります。  ユーザーは CloudSimple ポータルの初回起動時にのみ作成されます。  **[Automatically add]\(自動的に追加する\)** 規則を選択すると、新しいユーザーがポイント対サイト VPN 接続を使用して CloudSimple ネットワークにアクセスできるようになります。

## <a name="set-up-a-site-to-site-vpn-gateway"></a>サイト間 VPN ゲートウェイのセットアップ

1. [CloudSimple ポータルにアクセス](access-cloudsimple-portal.md)して **[ネットワーク]** を選択します。
2. **[VPN Gateway]\(VPN ゲートウェイ\)** を選択します。
3. **[New VPN Gateway]\(新しい VPN ゲートウェイ\)** をクリックします。

    ![VPN ゲートウェイを作成する](media/create-vpn-gateway.png)

4. **[Gateway configuration]\(ゲートウェイの構成\)** で次の設定を指定して、 **[次へ]** をクリックします。

    * ゲートウェイの種類として **[サイト間 VPN]** を選択します。
    * ゲートウェイを識別する名前を入力します。
    * CloudSimple サービスをデプロイする Azure の場所を選択します。
    * 必要に応じて、高可用性を有効化します。

    ![サイト間 VPN ゲートウェイの作成](media/create-vpn-gateway-s2s.png)

    > [!WARNING]
    > 高可用性を有効にするには、オンプレミス VPN デバイスが 2 つの IP アドレスへの接続をサポートする必要があります。 VPN ゲートウェイをデプロイした後は、このオプションを無効にすることはできません。

5. オンプレミス ネットワークから最初の接続を作成して **[次へ]** をクリックします。

    * 接続を識別する名前を入力します。
    * ピア IP には、オンプレミスの VPN ゲートウェイのパブリック IP アドレスを入力します。
    * オンプレミスの VPN ゲートウェイのピア識別子を入力します。  ピア識別子は通常、オンプレミスの VPN ゲートウェイのパブリック IP アドレスです。  ゲートウェイで特定の識別子を構成済みの場合は、その識別子を入力します。
    * オンプレミスの VPN ゲートウェイからの接続に使用する共有キーをコピーします。  既定の共有キーを変更して新しい共有キーを指定するには、編集アイコンをクリックします。
    * **[On-Premises Prefixes]\(オンプレミスのプレフィックス\)** には、CloudSimple ネットワークにアクセスするオンプレミスの CIDR プレフィックスを入力します。  接続を作成するときに、複数の CIDR プレフィックスを追加できます。

    ![サイト間 VPN ゲートウェイ接続の作成](media/create-vpn-gateway-s2s-connection.png)

6. オンプレミス ネットワークからアクセスされるプライベート クラウド ネットワーク上の VLAN/サブネットを有効にして、**[次へ]** をクリックします。

    * 管理 VLAN/サブネットを追加するには、**[Add management VLANs/Subnets of Private Clouds]\(プライベート クラウドの管理 VLAN/サブネットを追加する\)** を有効にします。  管理サブネットは VMotion および vSAN サブネットに必要です。
    * vMotion サブネットを追加するには、**[Add vMotion network of Private Clouds]\(プライベート クラウドの vMotion ネットワークを追加する\)** を有効にします。
    * vSAN サブネットを追加するには、**[Add vMotion network of Private Clouds]\(プライベート クラウドの vSAN サブネットを追加する\)** を有効にします。
    * 特定の VLAN を選択または選択解除します。

    ![接続を作成する](media/create-vpn-gateway-s2s-connection-vlans.png)

7. 設定を確認して **[Submit]\(送信\)** をクリックします。

    ![サイト間 VPN ゲートウェイの確認と作成](media/create-vpn-gateway-s2s-review.png)

## <a name="create-point-to-site-vpn-gateway"></a>ポイント対サイト VPN ゲートウェイの作成

1. [CloudSimple ポータルにアクセス](access-cloudsimple-portal.md)して **[ネットワーク]** を選択します。
2. **[VPN Gateway]\(VPN ゲートウェイ\)** を選択します。
3. **[New VPN Gateway]\(新しい VPN ゲートウェイ\)** をクリックします。

    ![VPN ゲートウェイを作成する](media/create-vpn-gateway.png)

4. **[Gateway configuration]\(ゲートウェイの構成\)** で次の設定を指定して、 **[次へ]** をクリックします。

    * ゲートウェイの種類として **[Point-to-Site VPN]\(ポイント対サイト VPN\)** を選択します。
    * ゲートウェイを識別する名前を入力します。
    * CloudSimple サービスをデプロイする Azure の場所を選択します。
    * ポイント対サイト ゲートウェイ用のクライアント サブネットを指定します。  接続するときは、クライアント サブネットから DHCP アドレスが提供されます。

5. **[Connection/User]\(接続/ユーザー\)** で次の設定を指定して、 **[次へ]** をクリックします。

    * 現在および将来のすべてのユーザーがポイント対サイト ゲートウェイ経由でプライベート クラウドにアクセスするのを自動的に許可するには、**[Automatically add all users]\(すべてのユーザーを自動的に追加する\)** を選択します。 このオプションを選択すると、ユーザーの一覧のすべてのユーザーが自動的に選択されます。 一覧で個々のユーザーの選択を解除して、自動オプションをオーバーライドできます。
    * 個々のユーザーを選択するには、ユーザーの一覧でチェック ボックスをオンにします。

6. [VLANs/Subnets]\(VLAN/サブネット\) セクションでは、ゲートウェイと接続に対する管理およびユーザーの VLAN/サブネットを指定できます。

    * **[Automatically add]\(自動的に追加する\)** オプションでは、ゲートウェイに対するグローバル ポリシーが設定されます。 現在のゲートウェイに設定が適用されます。 **[Select]\(選択\)** 領域で設定をオーバーライドできます。
    * **[Add management VLANs/Subnets of Private Clouds]\(プライベート クラウドの管理 VLAN/サブネットを追加する\)** を選択します。 
    * ユーザー定義のすべての VLAN/サブネットを追加するには、 **[Add user-defined VLANs/Subnets]\(ユーザー定義の VLAN/サブネットを追加する\)** をクリックします。
    * **[Automatically add]\(自動的に追加する\)** のグローバル設定は、 **[Select]\(選択\)** の設定によってオーバーライドされます。

7. **[次へ]** をクリックして、設定を確認します。 変更するには [編集] アイコンをクリックします。
8. **[作成]** をクリックして、VPN ゲートウェイを作成します。

### <a name="client-subnet-and-protocols-for-point-to-site-vpn-gateway"></a>ポイント対サイト VPN ゲートウェイのクライアント サブネットとプロトコル

ポイント対サイト VPN ゲートウェイでは、TCP 接続と UDP 接続が許可されます。  TCP または UDP 構成を選択して、コンピューターから接続するときに使用するプロトコルを選択します。

構成されたクライアント サブネットは、TCP と UDP の両方のクライアントに使用されます。  CIDR プレフィックスは、TCP クライアント用と UDP クライアント用の 2 つのサブネットに分割されます。 同時に接続する VPN ユーザーの数に基づいて、プレフィックス マスクを選択します。  

次の表は、プレフィックス マスクの同時クライアント接続数の一覧です。

| プレフィックス マスク | /24 | /25 | /26 | /27 | /28 |
|-------------|-----|-----|-----|-----|-----|
| コンカレント TCP 接続数 | 124 | 60 | 28 | 12 | 4 |
| コンカレント UDP 接続数 | 124 | 60 | 28 | 12 | 4 |

ポイント対サイト VPN を使用して接続するには、「[ポイント対サイト VPN を使用して CloudSimple に接続する](set-up-vpn.md#connect-to-cloudsimple-using-point-to-site-vpn)」を参照してください。
