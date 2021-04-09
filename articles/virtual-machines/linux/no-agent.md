---
title: プロビジョニング エージェントを使用せずに Linux イメージを作成する
description: Azure でプロビジョニング エージェントを使用せずに一般化された Linux イメージを作成します。
author: danielsollondon
ms.service: virtual-machines
ms.subservice: imaging
ms.collection: linux
ms.topic: how-to
ms.workload: infrastructure
ms.date: 09/01/2020
ms.author: danis
ms.reviewer: cynthn
ms.openlocfilehash: c7ca147f0a5b907ee0c5c66d53a219fe75ab2179
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/30/2021
ms.locfileid: "102551710"
---
# <a name="creating-generalized-images-without-a-provisioning-agent"></a>プロビジョニング エージェントを使用せずに一般化されたイメージを作成する

Microsoft Azure は、Linux VM のプロビジョニング エージェントを [walinuxagent](https://github.com/Azure/WALinuxAgent) または [cloud init](https://github.com/canonical/cloud-init) の形式で提供します (推奨)。 ただし、次のようなプロビジョニング エージェントでこれらのアプリケーションを使用しない場合もあります。

- お使いの Linux ディストリビューション/バージョンでは、cloud-init/Linux エージェントがサポートされていない。
- ホスト名など、特定の VM プロパティを設定する必要がある。

> [!NOTE] 
>
> プロパティを設定する必要がない場合、またはあらゆる形式のプロビジョニングが必要がない場合は、特殊化されたイメージを作成することを検討してください。

この記事では、プロビジョニング エージェントをインストールせずに、Azure プラットフォームの要件を満たすように VM イメージを設定し、ホスト名を設定する方法について説明します。

## <a name="networking-and-reporting-ready"></a>ネットワークと準備完了の報告

Linux VM が Azure コンポーネントと通信できるようにするには、DHCP クライアントを使用して、仮想ネットワークからホスト IP を取得し、さらに DNS 解決とルート管理を行う必要があります。 ほとんどのディストリビューションは、これらのユーティリティをすぐに使用できます。 Linux ディストリビューション ベンダーによって Azure でテストされたツールには、`dhclient`、`network-manager`、`systemd-networkd` などがあります。

> [!NOTE]
>
> 現在、プロビジョニング エージェントを使用せずに一般化されたイメージを作成する場合は、DHCP 対応の VM のみがサポートされます。

ネットワークをセットアップして構成したら、"準備完了を報告" する必要があります。 これにより、VM が正常にプロビジョニングしたことが Azure に通知されます。

> [!IMPORTANT]
>
> Azure に準備完了の報告をしないと、VM が再起動されます。

## <a name="demosample"></a>デモ/サンプル

このデモでは、既存の Marketplace イメージ (この場合は Debian Buster VM) を取得し、Linux エージェント (walinuxagent) を削除する方法について説明します。また、VM が "準備完了" であることを Azure に報告するための最も基本的なプロセスを作成する方法についても説明します。

### <a name="create-the-resource-group-and-base-vm"></a>リソース グループとベース VM を作成する:

```bash
$ az group create --location eastus --name demo1
```

ベース VM を作成する:

```bash
$ az vm create \
    --resource-group demo1 \
    --name demo1 \
    --location eastus \
    --ssh-key-value <ssh_pub_key_path> \
    --public-ip-address-dns-name demo1 \
    --image "debian:debian-10:10:latest"
```

### <a name="remove-the-image-provisioning-agent"></a>イメージ プロビジョニング エージェントを削除する

VM がプロビジョニングしたら、SSH 接続し、Linux エージェントを削除できます。

```bash
$ sudo apt purge -y waagent
$ sudo rm -rf /var/lib/waagent /etc/waagent.conf /var/log/waagent.log
```

### <a name="add-required-code-to-the-vm"></a>VM に必要なコードを追加する

また、Azure Linux エージェントを削除したため、VM 内でも、準備完了を報告するメカニズムを提供する必要があります。 

#### <a name="python-script"></a>Python スクリプト

```python
import http.client
import sys
from xml.etree import ElementTree

wireserver_ip = '168.63.129.16'
wireserver_conn = http.client.HTTPConnection(wireserver_ip)

print('Retrieving goal state from the Wireserver')
wireserver_conn.request(
    'GET',
    '/machine?comp=goalstate',
    headers={'x-ms-version': '2012-11-30'}
)

resp = wireserver_conn.getresponse()

if resp.status != 200:
    print('Unable to connect with wireserver')
    sys.exit(1)

wireserver_goalstate = resp.read().decode('utf-8')

xml_el = ElementTree.fromstring(wireserver_goalstate)

container_id = xml_el.findtext('Container/ContainerId')
instance_id = xml_el.findtext('Container/RoleInstanceList/RoleInstance/InstanceId')
print(f'ContainerId: {container_id}')
print(f'InstanceId: {instance_id}')

# Construct the XML response we need to send to Wireserver to report ready.
health = ElementTree.Element('Health')
goalstate_incarnation = ElementTree.SubElement(health, 'GoalStateIncarnation')
goalstate_incarnation.text = '1'
container = ElementTree.SubElement(health, 'Container')
container_id_el = ElementTree.SubElement(container, 'ContainerId')
container_id_el.text = container_id
role_instance_list = ElementTree.SubElement(container, 'RoleInstanceList')
role = ElementTree.SubElement(role_instance_list, 'Role')
instance_id_el = ElementTree.SubElement(role, 'InstanceId')
instance_id_el.text = instance_id
health_second = ElementTree.SubElement(role, 'Health')
state = ElementTree.SubElement(health_second, 'State')
state.text = 'Ready'

out_xml = ElementTree.tostring(
    health,
    encoding='unicode',
    method='xml'
)
print('Sending the following data to Wireserver:')
print(out_xml)

wireserver_conn.request(
    'POST',
    '/machine?comp=health',
    headers={
        'x-ms-version': '2012-11-30',
        'Content-Type': 'text/xml;charset=utf-8',
        'x-ms-agent-name': 'custom-provisioning'
    },
    body=out_xml
)

resp = wireserver_conn.getresponse()
print(f'Response: {resp.status} {resp.reason}')

wireserver_conn.close()
```

#### <a name="generic-steps-without-using-python"></a>一般的な手順 (Python を使用しない)

VM に Python がインストールされていないか使用できない場合は、次の手順に従って、このスクリプト ロジックをプログラムで再現できます。

1. WireServer: `curl -X GET -H 'x-ms-version: 2012-11-30' http://168.63.129.16/machine?comp=goalstate` からの応答を解析することによって、`ContainerId` と `InstanceId` を取得します。

2. 次の XML データを作成し、上記の手順で解析された `ContainerId` と `InstanceId` を挿入します。
   ```xml
   <Health>
     <GoalStateIncarnation>1</GoalStateIncarnation>
     <Container>
       <ContainerId>CONTAINER_ID</ContainerId>
       <RoleInstanceList>
         <Role>
           <InstanceId>INSTANCE_ID</InstanceId>
           <Health>
             <State>Ready</State>
           </Health>
         </Role>
       </RoleInstanceList>
     </Container>
   </Health>
   ```

3. このデータを WireServer: `curl -X POST -H 'x-ms-version: 2012-11-30' -H "x-ms-agent-name: WALinuxAgent" -H "Content-Type: text/xml;charset=utf-8" -d "$REPORT_READY_XML" http://168.63.129.16/machine?comp=health` に投稿します。

### <a name="automating-running-the-code-at-first-boot"></a>最初の起動時にコードの実行を自動化する

このデモでは、最新の Linux ディストリビューションの最も一般的な init システムである systemd を使用します。 そのため、この準備完了の報告メカニズムを適切なタイミングで実行するための最も簡単でネイティブな方法は、systemd サービス ユニットを作成することです。 次のユニット ファイルを `/etc/systemd/system` に追加できます (この例では、ユニット ファイル `azure-provisioning.service`)。

```bash
[Unit]
Description=Azure Provisioning

[Service]
Type=oneshot
ExecStart=/usr/bin/python3 /usr/local/azure-provisioning.py
ExecStart=/bin/bash -c "hostnamectl set-hostname $(curl \
    -H 'metadata: true' \
    'http://169.254.169.254/metadata/instance/compute/name?api-version=2019-06-01&format=text')"
ExecStart=/usr/bin/systemctl disable azure-provisioning.service

[Install]
WantedBy=multi-user.target
```

この systemd サービスは、基本的なプロビジョニングに関して次の 3 つの処理を行います。

1. Azure に準備完了を報告する (正常に完了したことを示すため)。
1. [Azure Instance Metadata Service (IMDS)](./instance-metadata-service.md) からこのデータを取得することによって、ユーザーが指定した VM 名に基づいて VM の名前を変更する。 **注** IMDS では、SSH 公開キーなどの他の [インスタンス メタデータ](./instance-metadata-service.md#access-azure-instance-metadata-service)も提供されるため、ホスト名以外の名前を設定することができます。
1. 最初の起動時にのみ実行され、その後の再起動では実行されないように、自身を無効にする。

filesystem のユニットで、次を実行して有効にします。

```bash
$ sudo systemctl enable azure-provisioning.service
```

これで、VM を一般化し、そこからイメージを作成できるようになりました。 

#### <a name="completing-the-preparation-of-the-image"></a>イメージの準備を完了する

開発用マシンに戻り、次を実行して、ベース VM からイメージを作成できるように準備します。

```bash
$ az vm deallocate --resource-group demo1 --name demo1
$ az vm generalize --resource-group demo1 --name demo1
```

この VM からイメージを作成します。

```bash
$ az image create \
    --resource-group demo1 \
    --source demo1 \
    --location eastus \
    --name demo1img
```

これで、イメージから新しい VM (または複数の VM) を作成する準備ができました。

```bash
$ IMAGE_ID=$(az image show -g demo1 -n demo1img --query id -o tsv)
$ az vm create \
    --resource-group demo12 \
    --name demo12 \
    --location eastus \
    --ssh-key-value <ssh_pub_key_path> \
    --public-ip-address-dns-name demo12 \
    --image "$IMAGE_ID" 
    --enable-agent false
```

> [!NOTE]
>
> walinuxagent がイメージから作成される VM 上に存在しないため、`--enable-agent` を `false` に設定することが重要です。

この VM は正常にプロビジョニングするはずです。 新しくプロビジョニングする VM にログインすると、準備完了の報告を行う systemd サービスの出力を確認できるようになります。

```bash
$ sudo journalctl -u azure-provisioning.service
-- Logs begin at Thu 2020-06-11 20:28:45 UTC, end at Thu 2020-06-11 20:31:24 UTC. --
Jun 11 20:28:49 thstringnopa systemd[1]: Starting Azure Provisioning...
Jun 11 20:28:54 thstringnopa python3[320]: Retrieving goal state from the Wireserver
Jun 11 20:28:54 thstringnopa python3[320]: ContainerId: 7b324f53-983a-43bc-b919-1775d6077608
Jun 11 20:28:54 thstringnopa python3[320]: InstanceId: fbb84507-46cd-4f4e-bd78-a2edaa9d059b._thstringnopa2
Jun 11 20:28:54 thstringnopa python3[320]: Sending the following data to Wireserver:
Jun 11 20:28:54 thstringnopa python3[320]: <Health><GoalStateIncarnation>1</GoalStateIncarnation><Container><ContainerId>7b324f53-983a-43bc-b919-1775d6077608</ContainerId><RoleInstanceList><Role><InstanceId>fbb84507-46cd-4f4e-bd78-a2edaa9d059b._thstringnopa2</InstanceId><Health><State>Ready</State></Health></Role></RoleInstanceList></Container></Health>
Jun 11 20:28:54 thstringnopa python3[320]: Response: 200 OK
Jun 11 20:28:56 thstringnopa bash[472]:   % Total    % Received % Xferd  Average Speed   Time    Time     Time  Current
Jun 11 20:28:56 thstringnopa bash[472]:                                  Dload  Upload   Total   Spent    Left  Speed
Jun 11 20:28:56 thstringnopa bash[472]: [158B blob data]
Jun 11 20:28:56 thstringnopa2 systemctl[475]: Removed /etc/systemd/system/multi-user.target.wants/azure-provisioning.service.
Jun 11 20:28:56 thstringnopa2 systemd[1]: azure-provisioning.service: Succeeded.
Jun 11 20:28:56 thstringnopa2 systemd[1]: Started Azure Provisioning.
```

## <a name="support"></a>サポート

独自のプロビジョニング コード/エージェントを実装する場合、ユーザーがこのコードのサポートを行うため、Microsoft サポートはプロビジョニング インターフェイスが使用できないことに関連する問題のみを調査します。 この領域には継続的に改善と変更が加えられているため、API の変更をプロビジョニングするために、cloud-init と Azure Linux エージェントの変更を監視する必要があります。

## <a name="next-steps"></a>次のステップ

詳細については、[Linux のプロビジョニング](provisioning.md)に関するページを参照してください。
