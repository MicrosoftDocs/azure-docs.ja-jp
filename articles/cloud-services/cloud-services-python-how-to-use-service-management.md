---
title: "サービス管理 API の使用方法 (Python) - 機能ガイド"
description: "Python から一般的なサービス管理タスクをプログラムで実行する方法について説明します。"
services: cloud-services
documentationcenter: python
author: lmazuel
manager: wpickett
editor: 
ms.assetid: 61538ec0-1536-4a7e-ae89-95967fe35d73
ms.service: cloud-services
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: python
ms.topic: article
ms.date: 09/06/2016
ms.author: lmazuel
translationtype: Human Translation
ms.sourcegitcommit: 356de369ec5409e8e6e51a286a20af70a9420193
ms.openlocfilehash: 579015f419ac0ee886f8e8497760a562ab324c24
ms.lasthandoff: 03/27/2017


---
# <a name="how-to-use-service-management-from-python"></a>Python からサービス管理を使用する方法
> [!NOTE]
> サービス管理 API は、新しいリソース管理 API (現在のところ、プレビュー リリースで使用できます) によって置き換えられます。  Python から新しいリソース管理 API を使用する詳細については、「 [Azure リソース管理ドキュメント](http://azure-sdk-for-python.readthedocs.org/) 」を参照してください。
> 
> 

このガイドでは、Python から一般的なサービス管理タスクをプログラムで実行する方法について説明します。 [Azure SDK for Python](https://github.com/Azure/azure-sdk-for-python) の **ServiceManagementService** クラスは、[Azure クラシック ポータル][management-portal]で使用できるサービス管理関連の機能 (**クラウド サービス、デプロイメント、データ管理サービスおよび仮想マシンの作成、更新、削除など**) の多くへのプログラムによるアクセスをサポートしています。 この機能は、サービス管理へのプログラムによるアクセスが必要なアプリケーションをビルドするために役立つ場合があります。

## <a name="WhatIs"> </a>サービス管理とは
Service Management API を使用すると、[Azure クラシック ポータル][management-portal]を通じて使用できるサービス管理機能の多くにプログラムでアクセスできます。 Azure SDK for Python を使用すると、クラウド サービスとストレージ アカウントを管理できます。

Service Management API を使用するには、 [Azure アカウントを作成する](https://azure.microsoft.com/pricing/free-trial/)必要があります。

## <a name="Concepts"> </a>概念
Azure SDK for Python は、REST API である [Azure Service Management API][svc-mgmt-rest-api] をラップします。 すべての API 操作は SSL 上で実行され、X.509 v3 証明書を使用して相互認証されます。 管理サービスへのアクセスは、Azure で実行されているサービス内から行うことも、HTTPS 要求の送信と HTTPS 応答の受信の機能を持つ任意のアプリケーションからインターネット上で直接行うこともできます。

## <a name="Installation"> </a>インストール
`azure-servicemanagement-legacy` パッケージには、この記事で説明されているすべての機能が用意されています。このパッケージは pip を使用してインストールできます。 インストールの詳細については (たとえば、Python を初めて使用する場合)、[Python と Azure SDK のインストール](../python-how-to-install.md)に関する記事をご覧ください。

## <a name="Connect"> </a>方法: サービス管理に接続する
サービス管理エンドポイントに接続するには、Azure サブスクリプション ID、および有効な管理証明書が必要です。 サブスクリプション ID は [Azure クラシック ポータル][management-portal]から入手できます。

> [!NOTE]
> Azure SDK for Python v0.8.0 以降、Windows で実行中の OpenSSL で作成した証明書を使用できるようになりました。  これには、Python 2.7.4 以降が必要です。 .pfx 証明書のサポートは今後削除される可能性があるため、.pfx の代わりに OpenSSL を使用することをお勧めします。
> 
> 

### <a name="management-certificates-on-windowsmaclinux-openssl"></a>Windows、Mac または Linux での管理証明書 (OpenSSL)
[OpenSSL](http://www.openssl.org/) を使用して管理証明書を作成できます。  実際は 2 つの証明書を作成する必要があります。1 つはサーバー用 (`.cer` ファイル)、もう 1 つはクライアント用 (`.pem` ファイル) です。 `.pem` ファイルを作成するには、次のコマンドを実行します。

    openssl req -x509 -nodes -days 365 -newkey rsa:1024 -keyout mycert.pem -out mycert.pem

`.cer` 証明書を作成するには、次のコマンドを実行します。

    openssl x509 -inform pem -in mycert.pem -outform der -out mycert.cer

Azure 証明書の詳細については、「[Azure Cloud Services の証明書の概要](cloud-services-certs-create.md)」を参照してください。 OpenSSL のパラメーターの詳細については、 [http://www.openssl.org/docs/apps/openssl.html](http://www.openssl.org/docs/apps/openssl.html)にあるドキュメントを参照してください。

これらのファイルを作成した後、[Azure クラシック ポータル][management-portal]の [設定] タブで [アップロード] をクリックして、`.cer` ファイルを Azure にアップロードする必要があります。また、`.pem` ファイルを保存した場所を書き留めておいてください。

サブスクリプション ID を取得し、証明書を作成して、`.cer` ファイルを Azure にアップロードした後、サブスクリプション ID と `.pem` ファイルへのパスを **ServiceManagementService** に渡すことで、Azure の管理エンドポイントに接続できます。

    from azure import *
    from azure.servicemanagement import *

    subscription_id = '<your_subscription_id>'
    certificate_path = '<path_to_.pem_certificate>'

    sms = ServiceManagementService(subscription_id, certificate_path)

前の例では、 `sms` は **ServiceManagementService** オブジェクトです。 **ServiceManagementService** クラスは、Azure サービスを管理するときに使用する主要なクラスです。

### <a name="management-certificates-on-windows-makecert"></a>Windows での管理証明書 (MakeCert)
`makecert.exe`を使用して、マシン上で自己署名管理証明書を作成できます。  **管理者**として **Visual Studio コマンド プロンプト**を開き、次のコマンドを使用して、*AzureCertificate* を、使用する証明書の名前に置き換えます。

    makecert -sky exchange -r -n "CN=AzureCertificate" -pe -a sha1 -len 2048 -ss My "AzureCertificate.cer"

このコマンドにより、 `.cer` ファイルが作成され、 **個人用** 証明書ストアにインストールされます。 詳細については、「[Azure Cloud Services の証明書の概要](cloud-services-certs-create.md)」を参照してください。

証明書を作成した後、[Azure クラシック ポータル][management-portal]の [設定] タブで [アップロード] をクリックして、`.cer` ファイルを Azure にアップロードする必要があります。

サブスクリプション ID を取得し、証明書を作成して、`.cer` ファイルを Azure にアップロードした後、サブスクリプション ID と**個人用**証明書ストア内の証明書の場所を **ServiceManagementService** に渡すことで、Azure の管理エンドポイントに接続できます (再度、*AzureCertificate* を証明書の名前に置き換えます)。

    from azure import *
    from azure.servicemanagement import *

    subscription_id = '<your_subscription_id>'
    certificate_path = 'CURRENT_USER\\my\\AzureCertificate'

    sms = ServiceManagementService(subscription_id, certificate_path)

前の例では、 `sms` は **ServiceManagementService** オブジェクトです。 **ServiceManagementService** クラスは、Azure サービスを管理するときに使用する主要なクラスです。

## <a name="ListAvailableLocations"> </a>方法: 利用可能な場所を列挙する
ホスティング サービスに利用できる場所を列挙するには、**list\_locations** メソッドを使用します。

    from azure import *
    from azure.servicemanagement import *

    sms = ServiceManagementService(subscription_id, certificate_path)

    result = sms.list_locations()
    for location in result:
        print(location.name)

クラウド サービスやストレージ サービスを作成するときは、有効な場所を指定する必要があります。 **list\_locations** メソッドでは常に、現在利用可能な場所の最新のリストが返されます。 この記事の執筆時点で利用可能な場所は次のとおりです。

* 西ヨーロッパ
* 北ヨーロッパ
* 東南アジア
* 東アジア
* 米国中央部
* 米国中北部
* 米国中南部
* 米国西部
* 米国東部
* 東日本
* 西日本
* ブラジル南部
* オーストラリア東部
* オーストラリア南東部

## <a name="CreateCloudService"> </a>方法: クラウド サービスを作成する
アプリケーションを作成して、それを Azure で実行するときは、そのコードと構成をあわせて Azure [クラウド サービス][cloud service]と呼びます (以前にリリースした Azure では*ホストされるサービス*と呼ばれていました)。 **create\_hosted\_service** メソッドを使用して、新しいホストされるサービスを作成できます。そのためには、このメソッドに、ホストされるサービス名 (Azure 上で一意の名前)、ラベル (Base64 に自動的にエンコードされます)、説明、場所を渡します。

    from azure import *
    from azure.servicemanagement import *

    sms = ServiceManagementService(subscription_id, certificate_path)

    name = 'myhostedservice'
    label = 'myhostedservice'
    desc = 'my hosted service'
    location = 'West US'

    sms.create_hosted_service(name, label, desc, location)

**list\_hosted\_services** メソッドを使用して、サブスクリプションのすべてのホストされるサービスを列挙できます。

    result = sms.list_hosted_services()

    for hosted_service in result:
        print('Service name: ' + hosted_service.service_name)
        print('Management URL: ' + hosted_service.url)
        print('Location: ' + hosted_service.hosted_service_properties.location)
        print('')

特定のホストされるサービスに関する情報を取得する場合は、ホストされるサービス名を **get\_hosted\_service\_properties** メソッドに渡します。

    hosted_service = sms.get_hosted_service_properties('myhostedservice')

    print('Service name: ' + hosted_service.service_name)
    print('Management URL: ' + hosted_service.url)
    print('Location: ' + hosted_service.hosted_service_properties.location)

クラウド サービスを作成した後、**create\_deployment** メソッドを使用してコードをサービスにデプロイできます。

## <a name="DeleteCloudService"> </a>方法: クラウド サービスを削除する
クラウド サービスを削除するには、そのサービス名を **delete\_hosted\_service** メソッドに渡します。

    sms.delete_hosted_service('myhostedservice')

サービスを削除する前に、そのサービスのすべてのデプロイを最初に削除する必要があります。 (詳細については「 [方法: デプロイの削除](#DeleteDeployment) 」を参照)。

## <a name="DeleteDeployment"> </a>方法: デプロイの削除
デプロイを削除するには、**delete\_deployment** メソッドを使用します。 次の例では、 `v1`という名前のデプロイメントを削除する方法を示しています。

    from azure import *
    from azure.servicemanagement import *

    sms = ServiceManagementService(subscription_id, certificate_path)

    sms.delete_deployment('myhostedservice', 'v1')

## <a name="CreateStorageService"> </a>方法: ストレージ サービスを作成する
[ストレージ サービス](../storage/storage-create-storage-account.md)を使用すると、Azure の [BLOB](../storage/storage-python-how-to-use-blob-storage.md)、[テーブル](../storage/storage-python-how-to-use-table-storage.md)、[キュー](../storage/storage-python-how-to-use-queue-storage.md)にアクセスできます。 ストレージ サービスを作成するには、サービスの名前 (Azure 内で一意の 3 〜 24 文字の小文字)、説明、ラベル (最大 100 文字、Base64 に自動的にエンコードされます)、場所が必要です。 次の例では、場所を指定してストレージ サービスを作成する方法を示しています。

    from azure import *
    from azure.servicemanagement import *

    sms = ServiceManagementService(subscription_id, certificate_path)

    name = 'mystorageaccount'
    label = 'mystorageaccount'
    location = 'West US'
    desc = 'My storage account description.'

    result = sms.create_storage_account(name, desc, label, location=location)

    operation_result = sms.get_operation_status(result.request_id)
    print('Operation status: ' + operation_result.status)

前の例では、**create\_storage\_account** 処理のステータスを取得するため、**create\_storage\_account** から返された結果を **get\_operation\_status** メソッドに渡しています。  

ストレージ アカウントとそのプロパティを列挙するには、**list\_storage\_accounts** メソッドを使用します。

    from azure import *
    from azure.servicemanagement import *

    sms = ServiceManagementService(subscription_id, certificate_path)

    result = sms.list_storage_accounts()
    for account in result:
        print('Service name: ' + account.service_name)
        print('Location: ' + account.storage_service_properties.location)
        print('')

## <a name="DeleteStorageService"> </a>方法: ストレージ サービスを削除する
ストレージ サービスを削除するには、そのサービス名を **delete\_storage\_account** メソッドに渡します。 ストレージ サービスを削除すると、サービスに格納されているすべてのデータ (BLOB、テーブル、キュー) が削除されます。

    from azure import *
    from azure.servicemanagement import *

    sms = ServiceManagementService(subscription_id, certificate_path)

    sms.delete_storage_account('mystorageaccount')

## <a name="ListOperatingSystems"> </a>方法: 利用可能なオペレーティング システムを列挙する
ホスティング サービスに利用できるオペレーティング システムを列挙するには、**list\_operating\_systems** メソッドを使用します。

    from azure import *
    from azure.servicemanagement import *

    sms = ServiceManagementService(subscription_id, certificate_path)

    result = sms.list_operating_systems()

    for os in result:
        print('OS: ' + os.label)
        print('Family: ' + os.family_label)
        print('Active: ' + str(os.is_active))

または、**list\_operating\_system\_families** メソッドを使用することもできます。このメソッドでは、オペレーティング システムがファミリ別にグループ化されます。

    result = sms.list_operating_system_families()

    for family in result:
        print('Family: ' + family.label)
        for os in family.operating_systems:
            if os.is_active:
                print('OS: ' + os.label)
                print('Version: ' + os.version)
        print('')

## <a name="CreateVMImage"> </a>方法: オペレーティング システム イメージを作成する
オペレーティング システム イメージをイメージ リポジトリに追加するには、**add\_os\_image** メソッドを使用します。

    from azure import *
    from azure.servicemanagement import *

    sms = ServiceManagementService(subscription_id, certificate_path)

    name = 'mycentos'
    label = 'mycentos'
    os = 'Linux' # Linux or Windows
    media_link = 'url_to_storage_blob_for_source_image_vhd'

    result = sms.add_os_image(label, media_link, name, os)

    operation_result = sms.get_operation_status(result.request_id)
    print('Operation status: ' + operation_result.status)

利用できるオペレーティング システム イメージを列挙するには、**list\_os\_images** メソッドを使用します。 このメソッドでは、すべてのプラットフォーム イメージとユーザー イメージが対象となります。

    result = sms.list_os_images()

    for image in result:
        print('Name: ' + image.name)
        print('Label: ' + image.label)
        print('OS: ' + image.os)
        print('Category: ' + image.category)
        print('Description: ' + image.description)
        print('Location: ' + image.location)
        print('Media link: ' + image.media_link)
        print('')

## <a name="DeleteVMImage"> </a>方法: オペレーティング システム イメージを削除する
ユーザー イメージを削除するには、**delete\_os\_image** メソッドを使用します。

    from azure import *
    from azure.servicemanagement import *

    sms = ServiceManagementService(subscription_id, certificate_path)

    result = sms.delete_os_image('mycentos')

    operation_result = sms.get_operation_status(result.request_id)
    print('Operation status: ' + operation_result.status)

## <a name="CreateVM"> </a>方法: 仮想マシンを作成する
仮想マシンを作成するには、最初に [クラウド サービス](#CreateCloudService)を作成する必要があります。  その後で、**create\_virtual\_machine\_deployment** メソッドを使用して、仮想マシンのデプロイを作成します。

    from azure import *
    from azure.servicemanagement import *

    sms = ServiceManagementService(subscription_id, certificate_path)

    name = 'myvm'
    location = 'West US'

    #Set the location
    sms.create_hosted_service(service_name=name,
        label=name,
        location=location)

    # Name of an os image as returned by list_os_images
    image_name = 'OpenLogic__OpenLogic-CentOS-62-20120531-en-us-30GB.vhd'

    # Destination storage account container/blob where the VM disk
    # will be created
    media_link = 'url_to_target_storage_blob_for_vm_hd'

    # Linux VM configuration, you can use WindowsConfigurationSet
    # for a Windows VM instead
    linux_config = LinuxConfigurationSet('myhostname', 'myuser', 'mypassword', True)

    os_hd = OSVirtualHardDisk(image_name, media_link)

    sms.create_virtual_machine_deployment(service_name=name,
        deployment_name=name,
        deployment_slot='production',
        label=name,
        role_name=name,
        system_config=linux_config,
        os_virtual_hard_disk=os_hd,
        role_size='Small')

## <a name="DeleteVM"> </a>方法: 仮想マシンを削除する
仮想マシンを削除するには、最初に **delete\_deployment** メソッドを使用してデプロイを削除します。

    from azure import *
    from azure.servicemanagement import *

    sms = ServiceManagementService(subscription_id, certificate_path)

    sms.delete_deployment(service_name='myvm',
        deployment_name='myvm')

その後で、**delete\_hosted\_service** メソッドを使用して、クラウド サービスを削除することができます。

    sms.delete_hosted_service(service_name='myvm')

## <a name="how-to-create-a-virtual-machine-from-a-captured-virtual-machine-image"></a>方法: キャプチャした仮想マシン イメージから仮想マシンを作成する
VM イメージをキャプチャするには、まず、**capture\_vm\_image** メソッドを呼び出します。

    from azure import *
    from azure.servicemanagement import *

    sms = ServiceManagementService(subscription_id, certificate_path)

    # replace the below three parameters with actual values
    hosted_service_name = 'hs1'
    deployment_name = 'dep1'
    vm_name = 'vm1'

    image_name = vm_name + 'image'
    image = CaptureRoleAsVMImage    ('Specialized',
        image_name,
        image_name + 'label',
        image_name + 'description',
        'english',
        'mygroup')

    result = sms.capture_vm_image(
            hosted_service_name,
            deployment_name,
            vm_name,
            image
        )

次に、イメージが正常にキャプチャされたことを確認するには、**list\_vm\_images** API を使用して、イメージが結果に表示されていることを確認します。

    images = sms.list_vm_images()

キャプチャしたイメージを使用して仮想マシンを作成するために、前の手順と同様に、**create\_virtual\_machine\_deployment** メソッドを使用しますが、今回は vm_image_name を渡します。

    from azure import *
    from azure.servicemanagement import *

    sms = ServiceManagementService(subscription_id, certificate_path)

    name = 'myvm'
    location = 'West US'

    #Set the location
    sms.create_hosted_service(service_name=name,
        label=name,
        location=location)

    sms.create_virtual_machine_deployment(service_name=name,
        deployment_name=name,
        deployment_slot='production',
        label=name,
        role_name=name,
        system_config=linux_config,
        os_virtual_hard_disk=None,
        role_size='Small',
        vm_image_name = image_name)

Linux 仮想マシンをキャプチャする方法の詳細については、[Linux 仮想マシンをキャプチャする方法](../virtual-machines/linux/classic/capture-image.md?toc=%2fazure%2fvirtual-machines%2flinux%2fclassic%2ftoc.json)に関するページを参照してください。

Windows 仮想マシンをキャプチャする方法の詳細については、[Windows 仮想マシンをキャプチャする方法](../virtual-machines/windows/classic/capture-image.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json)に関するページを参照してください。

## <a name="What's Next"> </a>次のステップ
これで、サービス管理の基本を学習できました。[Azure Python SDK の完全な API のリファレンス ドキュメント](http://azure-sdk-for-python.readthedocs.org/)にアクセスして、複雑なタスクを簡単に実行することにより、Python アプリケーションを管理できます。

詳細については、 [Python デベロッパー センター](/develop/python/)を参照してください。

[What is Service Management]: #WhatIs
[Concepts]: #Concepts
[How to: Connect to service management]: #Connect
[How to: List available locations]: #ListAvailableLocations
[How to: Create a cloud service]: #CreateCloudService
[How to: Delete a cloud service]: #DeleteCloudService
[How to: Create a deployment]: #CreateDeployment
[How to: Update a deployment]: #UpdateDeployment
[How to: Move deployments between staging and production]: #MoveDeployments
[How to: Delete a deployment]: #DeleteDeployment
[How to: Create a storage service]: #CreateStorageService
[How to: Delete a storage service]: #DeleteStorageService
[How to: List available operating systems]: #ListOperatingSystems
[How to: Create an operating system image]: #CreateVMImage
[How to: Delete an operating system image]: #DeleteVMImage
[How to: Create a virtual machine]: #CreateVM
[How to: Delete a virtual machine]: #DeleteVM
[Next Steps]: #NextSteps
[management-portal]: https://manage.windowsazure.com/
[svc-mgmt-rest-api]: http://msdn.microsoft.com/library/windowsazure/ee460799.aspx


[cloud service]:/services/cloud-services/


