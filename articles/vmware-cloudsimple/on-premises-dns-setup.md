---
title: CloudSimple による Azure VMware ソリューション - CloudSimple プライベート クラウド用の DNS を構成する
description: オンプレミスのワークステーションから、CloudSimple プライベート クラウド上の vCenter サーバーにアクセスするための DNS 名前解決を設定する方法について説明します
author: sharaths-cs
ms.author: b-shsury
ms.date: 08/14/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 929c923fda8c1c2bddd96d0b4d4e4b6ce6d29de9
ms.sourcegitcommit: adc1072b3858b84b2d6e4b639ee803b1dda5336a
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/10/2019
ms.locfileid: "70845314"
---
# <a name="configure-dns-for-name-resolution-for-private-cloud-vcenter-access-from-on-premises-workstations"></a>オンプレミスのワークステーションからのプライベート クラウドの vCenter にアクセスするための名前解決用に DNS を構成する

オンプレミスのワークステーションから CloudSimple プライベート クラウド上の vCenter サーバーにアクセスするには、IP アドレスだけでなくホスト名でも vCenter サーバーのアドレスを指定できるよう、DNS アドレス解決を構成する必要があります。

## <a name="obtain-the-ip-address-of-the-dns-server-for-your-private-cloud"></a>プライベート クラウドの DNS サーバーの IP アドレスを取得する

1. [CloudSimple ポータル](access-cloudsimple-portal.md)にサインインします。

2. **[リソース]**  >  **[プライベート クラウド]** に移動し、接続先のプライベート クラウドを選択します。

3. プライベート クラウドの **[Summary]\(概要\)** ページの **[Basic Info]\(基本情報\)** で、プライベート クラウドの DNS サーバーの IP アドレスをコピーします。

    ![プライベート クラウドの DNS サーバー](media/private-cloud-dns-server.png)


DNS の構成には、次のどちらかのオプションを使用します。

* [*.cloudsimple.io に対応するゾーンを DNS サーバー上に作成する](#create-a-zone-on-a-microsoft-windows-dns-server)
* [条件付きフォワーダーをオンプレミスの DNS サーバー上に作成して *.cloudsimple.io を解決する](#create-a-conditional-forwarder)

## <a name="create-a-zone-on-the-dns-server-for-cloudsimpleio"></a>*.cloudsimple.io に対応するゾーンを DNS サーバー上に作成する

スタブ ゾーンとしてゾーンをセットアップし、名前解決のためにプライベート クラウド上の DNS サーバーをポイントすることができます。 ここでは、BIND DNS サーバーまたは Microsoft Windows DNS サーバーの使用に関する情報を提供します。

### <a name="create-a-zone-on-a-bind-dns-server"></a>BIND DNS サーバー上にゾーンを作成する

構成する具体的なファイルおよびパラメーターは、個々の DNS セットアップによって異なる場合があります。

たとえば、既定の BIND サーバー構成の場合、DNS サーバー上の /etc/named.conf ファイルを編集し、次のゾーン情報を追加します。

```
zone “cloudsimple.io”
{
    type stub;
    masters { IP address of DNS servers; };
    file “slaves/cloudsimple.io.db”;
};
```

### <a name="create-a-zone-on-a-microsoft-windows-dns-server"></a>Microsoft Windows DNS サーバー上にゾーンを作成する

1. DNS サーバーを右クリックして **[新しいゾーン]** を選択します。 
  
    ![新しいゾーン](media/DNS01.png)
2. **[スタブ ゾーン]** を選択して **[次へ]** をクリックします。

    ![新しいゾーン](media/DNS02.png)
3. 環境に応じて適切なオプションを選択し、 **[次へ]** をクリックします。

    ![新しいゾーン](media/DNS03.png)
4. **[前方参照ゾーン]**   を選択して **[次へ]** をクリックします。

    ![新しいゾーン](media/DNS01.png)
5. ゾーン名を入力して **[次へ]** をクリックします。

    ![新しいゾーン](media/DNS05.png)
6. CloudSimple ポータルから取得した、プライベート クラウドの DNS サーバーの IP アドレスを入力します。

    ![新しいゾーン](media/DNS06.png)
7. 必要に応じて **[次へ]** をクリックして、ウィザードのセットアップを完了します。

## <a name="create-a-conditional-forwarder"></a>条件付きフォワーダーの作成

条件付きフォワーダーは、すべての DNS 名前解決要求を指定されたサーバーに転送します。 このセットアップでは、*.cloudsimple.io への要求は、プライベート クラウド上に位置する DNS サーバーに転送されます。 次の例は、さまざまな種類の DNS サーバー上でフォワーダーを設定する方法を示しています。

### <a name="create-a-conditional-forwarded-on-a-bind-dns-server"></a>BIND DNS サーバー上に条件付きフォワーダーを作成する

構成する具体的なファイルおよびパラメーターは、個々の DNS セットアップによって異なる場合があります。

たとえば、既定の BIND サーバー構成の場合、DNS サーバー上の /etc/named.conf ファイルを編集し、次の条件付き転送情報を追加します。

```
zone “cloudsimple.io” {
    type forward;
    forwarders { IP address of DNS servers; };
};
```

### <a name="create-a-conditional-forwarder-on-a-microsoft-windows-dns-server"></a>Microsoft Windows DNS サーバー上に条件付きフォワーダーを作成する

1. DNS サーバー上で DNS マネージャーを開きます。
2. **[条件付きフォワーダー]** を右クリックし、新しい条件付きフォワーダーを追加するためのオプションを選択します。

    ![条件付きフォワーダー 1 の Windows DNS](media/DNS08.png)
3. プライベート クラウド内の DNS サーバーの DNS ドメインと IP アドレスを入力して **[OK]** をクリックします。
