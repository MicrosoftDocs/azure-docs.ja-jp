---
title: Python を使用して Azure で Windows VM を作成および管理する |Microsoft Docs
description: Python を使って Azure で Windows VM を作成および管理する方法について説明します。
services: virtual-machines-windows
documentationcenter: ''
author: cynthn
manager: gwallace
editor: tysonn
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machines-windows
ms.workload: na
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.topic: article
ms.date: 06/22/2017
ms.author: cynthn
ms.openlocfilehash: a132cf28b0ccd30b3f7e854e46763ce99372ddfe
ms.sourcegitcommit: 4b647be06d677151eb9db7dccc2bd7a8379e5871
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/19/2019
ms.locfileid: "68361152"
---
# <a name="create-and-manage-windows-vms-in-azure-using-python"></a>Python を使用して Azure で Windows VM を作成および管理する

[Azure 仮想マシン](overview.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) (VM) には、いくつかのサポート Azure リソースが必要です。 この記事では、Python を使って VM リソースを作成、管理、削除する方法について説明します。 学習内容は次のとおりです。

> [!div class="checklist"]
> * Visual Studio プロジェクトを作成する
> * パッケージをインストールする
> * 資格情報を作成する
> * リソースを作成する
> * 管理タスクを実行する
> * リソースを削除する
> * アプリケーションの実行

これらの手順を実行するには約 20 分かかります。

## <a name="create-a-visual-studio-project"></a>Visual Studio プロジェクトを作成する

1. まだ [Visual Studio](https://docs.microsoft.com/visualstudio/install/install-visual-studio) をインストールしていない場合は、インストールを実行します。 [ワークロード] ページで **[Python 開発]** を選び、 **[インストール]** をクリックします。 概要では、**Python 3 64 ビット (3.6.0)** が自動的に選択されていることがわかります。 Visual Studio を既にインストールしてある場合は、Visual Studio 起動ツールを使って Python ワークロードを追加できます。
2. Visual Studio をインストールして起動した後、 **[ファイル]**  >  **[新規]**  >  **[プロジェクト]** の順にクリックします。
3. **[テンプレート]**  >  **[Python]**  >  **[Python アプリケーション]** の順にクリックし、プロジェクトの名前に「*myPythonProject*」と入力して、プロジェクトの場所を選んだ後、 **[OK]** をクリックします。

## <a name="install-packages"></a>パッケージをインストールする

1. ソリューション エクスプローラーで、*myPythonProject* の **[Python 環境]** を右クリックし、 **[仮想環境の追加]** を選びます。
2. [仮想環境の追加] 画面で、既定の名前 *env* をそのまま使い、ベース インタープリターとして *[Python 3.6 (64-bit)]\(Python 3.6 (64 ビット)\)* が選ばれていることを確認して、 **[作成]** をクリックします。
3. 作成した *env* 環境を右クリックして、 **[Python パッケージのインストール]** をクリックし、検索ボックスに「*azure*」と入力して Enter キーを押します。

出力ウィンドウに、Azure パッケージが正常にインストールされたことが表示されます。 

## <a name="create-credentials"></a>資格情報を作成する

この手順を始める前に、[Active Directory サービス プリンシパル](../../active-directory/develop/howto-create-service-principal-portal.md)があることを確認します。 また、後の手順で必要になるので、アプリケーション ID、認証キー、テナント ID を控えておく必要があります。

1. 作成した *myPythonProject.py* ファイルを開き、アプリケーションが実行できるようにする次のコードを追加します。

    ```python
    if __name__ == "__main__":
    ```

2. 必要なコードをインポートするには、.py ファイルの先頭に次のステートメントを追加します。

    ```python
    from azure.common.credentials import ServicePrincipalCredentials
    from azure.mgmt.resource import ResourceManagementClient
    from azure.mgmt.compute import ComputeManagementClient
    from azure.mgmt.network import NetworkManagementClient
    from azure.mgmt.compute.models import DiskCreateOption
    ```

3. 次に、.py ファイルで、インポート ステートメントの後に変数を追加して、コードで使われる共通の値を指定します。
   
    ```
    SUBSCRIPTION_ID = 'subscription-id'
    GROUP_NAME = 'myResourceGroup'
    LOCATION = 'westus'
    VM_NAME = 'myVM'
    ```

    **subscription-id** は、実際のサブスクリプション ID に置き換えます。

4. 要求を行うために必要な Active Directory 資格情報を作成するには、.py ファイルの変数の後に次の関数を追加します。

    ```python
    def get_credentials():
        credentials = ServicePrincipalCredentials(
            client_id = 'application-id',
            secret = 'authentication-key',
            tenant = 'tenant-id'
        )

        return credentials
    ```

    **application-id**、**authentication-key**、**tenant-id** は、Azure Active Directory サービス プリンシパルを作成するときに収集した値に置き換えます。

5. 前に追加した関数を呼び出すには、.py ファイルの最後にある **if** ステートメントの下に次のコードを追加します。

    ```python
    credentials = get_credentials()
    ```

## <a name="create-resources"></a>リソースを作成する
 
### <a name="initialize-management-clients"></a>管理クライアントを初期化する

Azure で Python SDK を使ってリソースを作成および管理するには、管理クライアントが必要です。 管理クライアントを作成するには、.py ファイルの最後にある **if** ステートメントの下に次のコードを追加します。

```python
resource_group_client = ResourceManagementClient(
    credentials,
    SUBSCRIPTION_ID
)
network_client = NetworkManagementClient(
    credentials,
    SUBSCRIPTION_ID
)
compute_client = ComputeManagementClient(
    credentials,
    SUBSCRIPTION_ID
)
```

### <a name="create-the-vm-and-supporting-resources"></a>VM とサポート リソースを作成する

すべてのリソースは、[リソース グループ](../../azure-resource-manager/resource-group-overview.md)に含まれる必要があります。

1. リソース グループを作成するには、.py ファイルの変数の後に次の関数を追加します。

    ```python
    def create_resource_group(resource_group_client):
        resource_group_params = { 'location':LOCATION }
        resource_group_result = resource_group_client.resource_groups.create_or_update(
            GROUP_NAME, 
            resource_group_params
        )
    ```

2. 前に追加した関数を呼び出すには、.py ファイルの最後にある **if** ステートメントの下に次のコードを追加します。

    ```python
    create_resource_group(resource_group_client)
    input('Resource group created. Press enter to continue...')
    ```

[可用性セット](tutorial-availability-sets.md)を使うと、アプリケーションで使う仮想マシンの管理が容易になります。

1. 可用性セットを作成するには、.py ファイルの変数の後に次の関数を追加します。
   
    ```python
    def create_availability_set(compute_client):
        avset_params = {
            'location': LOCATION,
            'sku': { 'name': 'Aligned' },
            'platform_fault_domain_count': 3
        }
        availability_set_result = compute_client.availability_sets.create_or_update(
            GROUP_NAME,
            'myAVSet',
            avset_params
        )
    ```

2. 前に追加した関数を呼び出すには、.py ファイルの最後にある **if** ステートメントの下に次のコードを追加します。

    ```python
    create_availability_set(compute_client)
    print("------------------------------------------------------")
    input('Availability set created. Press enter to continue...')
    ```

仮想マシンと通信するには、[パブリック IP アドレス](../../virtual-network/virtual-network-ip-addresses-overview-arm.md)が必要です。

1. 仮想マシンのパブリック IP アドレスを作成するには、.py ファイルの変数の後に次の関数を追加します。

    ```python
    def create_public_ip_address(network_client):
        public_ip_addess_params = {
            'location': LOCATION,
            'public_ip_allocation_method': 'Dynamic'
        }
        creation_result = network_client.public_ip_addresses.create_or_update(
            GROUP_NAME,
            'myIPAddress',
            public_ip_addess_params
        )

        return creation_result.result()
    ```

2. 前に追加した関数を呼び出すには、.py ファイルの最後にある **if** ステートメントの下に次のコードを追加します。

    ```python
    creation_result = create_public_ip_address(network_client)
    print("------------------------------------------------------")
    print(creation_result)
    input('Press enter to continue...')
    ```

仮想マシンは、[仮想ネットワーク](../../virtual-network/virtual-networks-overview.md)のサブネット内に存在する必要があります。

1. 仮想ネットワークを作成するには、.py ファイルの変数の後に次の関数を追加します。

    ```python
    def create_vnet(network_client):
        vnet_params = {
            'location': LOCATION,
            'address_space': {
                'address_prefixes': ['10.0.0.0/16']
            }
        }
        creation_result = network_client.virtual_networks.create_or_update(
            GROUP_NAME,
            'myVNet',
            vnet_params
        )
        return creation_result.result()
    ```

2. 前に追加した関数を呼び出すには、.py ファイルの最後にある **if** ステートメントの下に次のコードを追加します。
   
    ```python
    creation_result = create_vnet(network_client)
    print("------------------------------------------------------")
    print(creation_result)
    input('Press enter to continue...')
    ```

3. 仮想ネットワークにサブネットを追加するには、.py ファイルの変数の後に次の関数を追加します。
    
    ```python
    def create_subnet(network_client):
        subnet_params = {
            'address_prefix': '10.0.0.0/24'
        }
        creation_result = network_client.subnets.create_or_update(
            GROUP_NAME,
            'myVNet',
            'mySubnet',
            subnet_params
        )

        return creation_result.result()
    ```
        
4. 前に追加した関数を呼び出すには、.py ファイルの最後にある **if** ステートメントの下に次のコードを追加します。
   
    ```python
    creation_result = create_subnet(network_client)
    print("------------------------------------------------------")
    print(creation_result)
    input('Press enter to continue...')
    ```

仮想マシンが仮想ネットワーク上で通信するには、ネットワーク インターフェイスが必要です。

1. ネットワーク インターフェイスを作成するには、.py ファイルの変数の後に次の関数を追加します。

    ```python
    def create_nic(network_client):
        subnet_info = network_client.subnets.get(
            GROUP_NAME, 
            'myVNet', 
            'mySubnet'
        )
        publicIPAddress = network_client.public_ip_addresses.get(
            GROUP_NAME,
            'myIPAddress'
        )
        nic_params = {
            'location': LOCATION,
            'ip_configurations': [{
                'name': 'myIPConfig',
                'public_ip_address': publicIPAddress,
                'subnet': {
                    'id': subnet_info.id
                }
            }]
        }
        creation_result = network_client.network_interfaces.create_or_update(
            GROUP_NAME,
            'myNic',
            nic_params
        )

        return creation_result.result()
    ```

2. 前に追加した関数を呼び出すには、.py ファイルの最後にある **if** ステートメントの下に次のコードを追加します。

    ```python
    creation_result = create_nic(network_client)
    print("------------------------------------------------------")
    print(creation_result)
    input('Press enter to continue...')
    ```

すべての関連リソースを作成したので、仮想マシンを作成できます。

1. 仮想マシンを作成するには、.py ファイルの変数の後に次の関数を追加します。
   
    ```python
    def create_vm(network_client, compute_client):  
        nic = network_client.network_interfaces.get(
            GROUP_NAME, 
            'myNic'
        )
        avset = compute_client.availability_sets.get(
            GROUP_NAME,
            'myAVSet'
        )
        vm_parameters = {
            'location': LOCATION,
            'os_profile': {
                'computer_name': VM_NAME,
                'admin_username': 'azureuser',
                'admin_password': 'Azure12345678'
            },
            'hardware_profile': {
                'vm_size': 'Standard_DS1'
            },
            'storage_profile': {
                'image_reference': {
                    'publisher': 'MicrosoftWindowsServer',
                    'offer': 'WindowsServer',
                    'sku': '2012-R2-Datacenter',
                    'version': 'latest'
                }
            },
            'network_profile': {
                'network_interfaces': [{
                    'id': nic.id
                }]
            },
            'availability_set': {
                'id': avset.id
            }
        }
        creation_result = compute_client.virtual_machines.create_or_update(
            GROUP_NAME, 
            VM_NAME, 
            vm_parameters
        )
    
        return creation_result.result()
    ```

    > [!NOTE]
    > このチュートリアルでは、Windows Server オペレーティング システムのバージョンを実行する仮想マシンを作成します。 他のイメージの選択の詳細については、 [Windows PowerShell と Azure CLI による Azure 仮想マシン イメージのナビゲーションと選択](../linux/cli-ps-findimage.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)に関する記事をご覧ください。
    > 
    > 

2. 前に追加した関数を呼び出すには、.py ファイルの最後にある **if** ステートメントの下に次のコードを追加します。

    ```python
    creation_result = create_vm(network_client, compute_client)
    print("------------------------------------------------------")
    print(creation_result)
    input('Press enter to continue...')
    ```

## <a name="perform-management-tasks"></a>管理タスクを実行する

仮想マシンのライフサイクルで、各種の管理タスクを実行する必要がある場合があります (仮想マシンの起動、停止、削除など)。 また、何度も行う作業や複雑な作業は、コードを作成して自動化したい場合もあるでしょう。

### <a name="get-information-about-the-vm"></a>VM に関する情報を取得する

1. 仮想マシンに関する情報を取得するには、.py ファイルの変数の後に次の関数を追加します。

    ```python
    def get_vm(compute_client):
        vm = compute_client.virtual_machines.get(GROUP_NAME, VM_NAME, expand='instanceView')
        print("hardwareProfile")
        print("   vmSize: ", vm.hardware_profile.vm_size)
        print("\nstorageProfile")
        print("  imageReference")
        print("    publisher: ", vm.storage_profile.image_reference.publisher)
        print("    offer: ", vm.storage_profile.image_reference.offer)
        print("    sku: ", vm.storage_profile.image_reference.sku)
        print("    version: ", vm.storage_profile.image_reference.version)
        print("  osDisk")
        print("    osType: ", vm.storage_profile.os_disk.os_type.value)
        print("    name: ", vm.storage_profile.os_disk.name)
        print("    createOption: ", vm.storage_profile.os_disk.create_option.value)
        print("    caching: ", vm.storage_profile.os_disk.caching.value)
        print("\nosProfile")
        print("  computerName: ", vm.os_profile.computer_name)
        print("  adminUsername: ", vm.os_profile.admin_username)
        print("  provisionVMAgent: {0}".format(vm.os_profile.windows_configuration.provision_vm_agent))
        print("  enableAutomaticUpdates: {0}".format(vm.os_profile.windows_configuration.enable_automatic_updates))
        print("\nnetworkProfile")
        for nic in vm.network_profile.network_interfaces:
            print("  networkInterface id: ", nic.id)
        print("\nvmAgent")
        print("  vmAgentVersion", vm.instance_view.vm_agent.vm_agent_version)
        print("    statuses")
        for stat in vm_result.instance_view.vm_agent.statuses:
            print("    code: ", stat.code)
            print("    displayStatus: ", stat.display_status)
            print("    message: ", stat.message)
            print("    time: ", stat.time)
        print("\ndisks");
        for disk in vm.instance_view.disks:
            print("  name: ", disk.name)
            print("  statuses")
            for stat in disk.statuses:
                print("    code: ", stat.code)
                print("    displayStatus: ", stat.display_status)
                print("    time: ", stat.time)
        print("\nVM general status")
        print("  provisioningStatus: ", vm.provisioning_state)
        print("  id: ", vm.id)
        print("  name: ", vm.name)
        print("  type: ", vm.type)
        print("  location: ", vm.location)
        print("\nVM instance status")
        for stat in vm.instance_view.statuses:
            print("  code: ", stat.code)
            print("  displayStatus: ", stat.display_status)
    ```
2. 前に追加した関数を呼び出すには、.py ファイルの最後にある **if** ステートメントの下に次のコードを追加します。

    ```python
    get_vm(compute_client)
    print("------------------------------------------------------")
    input('Press enter to continue...')
    ```

### <a name="stop-the-vm"></a>VM を停止する

仮想マシンを停止してそのすべての設定を保持する (引き続き課金されます)、または仮想マシンを停止して割り当てを解除します。 仮想マシンの割り当てを解除すると、それに関連付けられているすべてのリソースの割り当ても解除され、仮想マシンに対する課金が終了します。

1. 仮想マシンを割り当て解除しないで停止するには、.py ファイルの変数の後に次の関数を追加します。

    ```python
    def stop_vm(compute_client):
        compute_client.virtual_machines.power_off(GROUP_NAME, VM_NAME)
    ```

    仮想マシンの割り当てを解除する場合は、power_off の呼び出しを次のコードに変更します。

    ```python
    compute_client.virtual_machines.deallocate(GROUP_NAME, VM_NAME)
    ```

2. 前に追加した関数を呼び出すには、.py ファイルの最後にある **if** ステートメントの下に次のコードを追加します。

    ```python
    stop_vm(compute_client)
    input('Press enter to continue...')
    ```

### <a name="start-the-vm"></a>VM を起動する

1. 仮想マシンを起動するには、.py ファイルの変数の後に次の関数を追加します。

    ```python
    def start_vm(compute_client):
        compute_client.virtual_machines.start(GROUP_NAME, VM_NAME)
    ```

2. 前に追加した関数を呼び出すには、.py ファイルの最後にある **if** ステートメントの下に次のコードを追加します。

    ```python
    start_vm(compute_client)
    input('Press enter to continue...')
    ```

### <a name="resize-the-vm"></a>VM のサイズを変更する

仮想マシンのサイズを決定するときは、デプロイのさまざまな面を考慮する必要があります。 詳しくは、[VM サイズ](sizes.md)をご覧ください。

1. 仮想マシンのサイズを変更するには、.py ファイルの変数の後に次の関数を追加します。

    ```python
    def update_vm(compute_client):
        vm = compute_client.virtual_machines.get(GROUP_NAME, VM_NAME)
        vm.hardware_profile.vm_size = 'Standard_DS3'
        update_result = compute_client.virtual_machines.create_or_update(
            GROUP_NAME, 
            VM_NAME, 
            vm
        )

    return update_result.result()
    ```

2. 前に追加した関数を呼び出すには、.py ファイルの最後にある **if** ステートメントの下に次のコードを追加します。

    ```python
    update_result = update_vm(compute_client)
    print("------------------------------------------------------")
    print(update_result)
    input('Press enter to continue...')
    ```

### <a name="add-a-data-disk-to-the-vm"></a>VM にデータ ディスクを追加する

仮想マシンでは 1 つ以上の[データ ディスク](managed-disks-overview.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)を保持することができ、それらは VHD として格納されます。

1. 仮想マシンにデータ ディスクを追加するには、.py ファイルの変数の後に次の関数を追加します。 

    ```python
    def add_datadisk(compute_client):
        disk_creation = compute_client.disks.create_or_update(
            GROUP_NAME,
            'myDataDisk1',
            {
                'location': LOCATION,
                'disk_size_gb': 1,
                'creation_data': {
                    'create_option': DiskCreateOption.empty
                }
            }
        )
        data_disk = disk_creation.result()
        vm = compute_client.virtual_machines.get(GROUP_NAME, VM_NAME)
        add_result = vm.storage_profile.data_disks.append({
            'lun': 1,
            'name': 'myDataDisk1',
            'create_option': DiskCreateOption.attach,
            'managed_disk': {
                'id': data_disk.id
            }
        })
        add_result = compute_client.virtual_machines.create_or_update(
            GROUP_NAME,
            VM_NAME,
            vm)

        return add_result.result()
    ```

2. 前に追加した関数を呼び出すには、.py ファイルの最後にある **if** ステートメントの下に次のコードを追加します。

    ```python
    add_result = add_datadisk(compute_client)
    print("------------------------------------------------------")
    print(add_result)
    input('Press enter to continue...')
    ```

## <a name="delete-resources"></a>リソースを削除する

Azure で使用されるリソースに対して課金されるため、不要になったリソースは削除することを常にお勧めします。 仮想マシンとすべての関連リソースを削除する場合、必要な操作はリソース グループの削除だけです。

1. リソース グループとすべてのリソースを削除するには、.py ファイルの変数の後に次の関数を追加します。
   
    ```python
    def delete_resources(resource_group_client):
        resource_group_client.resource_groups.delete(GROUP_NAME)
    ```

2. 前に追加した関数を呼び出すには、.py ファイルの最後にある **if** ステートメントの下に次のコードを追加します。
   
    ```python
    delete_resources(resource_group_client)
    ```

3. *myPythonProject.py* を保存します。

## <a name="run-the-application"></a>アプリケーションの実行

1. コンソール アプリケーションを実行するには、Visual Studio で **[開始]** をクリックします。

2. 各リソースの状態が返された後、**Enter** キーを押します。 状態の情報では、プロビジョニングが**成功**状態になっている必要があります。 仮想マシンが作成された後、作成したすべてのリソースを削除する機会があります。 **Enter** キーを押してリソースの削除を開始する前に、Azure Portal でリソースの作成状況を確認することもできます。 Azure Portal が開いたままになっている場合、新しいリソースを表示するにはブレードの更新が必要になる場合があります。  

    このコンソール アプリケーションが実行を開始してから完全に終了するまでには、約 5 分かかります。 アプリケーションが終了してから、すべてのリソースとリソース グループが削除されるまで、数分かかる場合があります。


## <a name="next-steps"></a>次の手順

- デプロイに問題がある場合は、次の手順として、「[Azure Portal でのリソース グループのデプロイのトラブルシューティング](../../resource-manager-troubleshoot-deployments-portal.md)」を参照してください。
- [Azure Python ライブラリ](https://docs.microsoft.com/python/api/overview/azure/?view=azure-python)の詳細をご覧ください。

