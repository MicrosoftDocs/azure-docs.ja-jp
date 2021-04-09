---
title: Service Management API の使用 (Python) - 機能ガイド
description: Python から一般的なサービス管理タスクをプログラムで実行する方法について説明します。
ms.topic: article
ms.service: cloud-services
ms.date: 10/14/2020
ms.author: tagore
author: tanmaygore
ms.reviewer: mimckitt
ms.custom: ''
ms.openlocfilehash: 02993f2b79e37e5e50c20c4ee07220bcbd36edb8
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "98741402"
---
# <a name="use-service-management-from-python"></a>Python からサービス管理を使用する

> [!IMPORTANT]
> [Azure Cloud Services (延長サポート)](../cloud-services-extended-support/overview.md) は、Azure Cloud Services 製品向けの新しい Azure Resource Manager ベースのデプロイ モデルです。 この変更により、Azure Service Manager ベースのデプロイ モデルで実行されている Azure Cloud Services は Cloud Services (クラシック) という名前に変更されました。そして、すべての新しいデプロイでは [Cloud Services (延長サポート)](../cloud-services-extended-support/overview.md) を使用する必要があります。

このガイドでは、Python から一般的なサービス管理タスクをプログラムで実行する方法について説明します。 [Azure SDK for Python](https://github.com/Azure/azure-sdk-for-python) の **ServiceManagementService** クラスは、[Azure Portal][management-portal] で使用できるサービス管理関連の機能の多くへのプログラムによるアクセスをサポートしています。 この機能を使用して、クラウド サービス、デプロイ、データ管理サービス、および仮想マシンの作成、更新、および削除を行うことができます。 この機能は、サービス管理へのプログラムによるアクセスが必要なアプリケーションをビルドするために役立つ場合があります。

## <a name="what-is-service-management"></a><a name="WhatIs"> </a>サービス管理とは
Azure Service Management API を使うと、[Azure Portal][management-portal] を通じて使用できるサービス管理機能の多くにプログラムでアクセスできます。 Azure SDK for Python を使用すると、クラウド サービスとストレージ アカウントを管理できます。

Service Management API を使用するには、 [Azure アカウントを作成する](https://azure.microsoft.com/pricing/free-trial/)必要があります。

## <a name="concepts"></a><a name="Concepts"> </a>概念
Azure SDK for Python は、REST API である [Service Management API][svc-mgmt-rest-api] をラップします。 すべての API 操作は TLS 経由で実行され、X.509 v3 証明書を使用して相互認証されます。 管理サービスへのアクセスは、Azure で実行されているサービス内から行うことができます。 また、HTTPS 要求の送信と HTTPS 応答の受信の機能を持つ任意のアプリケーションからインターネット経由で直接行うこともできます。

## <a name="installation"></a><a name="Installation"> </a>インストール
`azure-servicemanagement-legacy` パッケージには、この記事で説明されているすべての機能が用意されています。このパッケージは pip を使用してインストールできます。 インストールの詳細については (たとえば、Python を初めて使用する場合)、[Python と Azure SDK のインストール](/azure/developer/python/azure-sdk-install)に関する記事を参照してください。

## <a name="connect-to-service-management"></a><a name="Connect"> </a>サービス管理に接続する
サービス管理エンドポイントに接続するには、Azure サブスクリプション ID、および有効な管理証明書が必要です。 サブスクリプション ID は [Azure Portal][management-portal] から入手できます。

> [!NOTE]
> Windows で実行中の OpenSSL で作成した証明書を使用できるようになりました。 Python 2.7.4 以降が必要です。 .pfx 証明書のサポートは今後削除される可能性があるため、.pfx の代わりに OpenSSL を使用することをお勧めします。
>
>

### <a name="management-certificates-on-windowsmaclinux-openssl"></a>Windows、Mac または Linux での管理証明書 (OpenSSL)
[OpenSSL](https://www.openssl.org/) を使用して管理証明書を作成できます。 2 つの証明書を作成する必要があります。1 つはサーバー用 (`.cer` ファイル)、もう 1 つはクライアント用 (`.pem` ファイル) です。 `.pem` ファイルを作成するには、次のコマンドを実行します。

```console
openssl req -x509 -nodes -days 365 -newkey rsa:1024 -keyout mycert.pem -out mycert.pem
```

`.cer` 証明書を作成するには、次のコマンドを実行します。

```console
openssl x509 -inform pem -in mycert.pem -outform der -out mycert.cer
```

Azure 証明書の詳細については、「[Azure Cloud Services の証明書の概要](cloud-services-certs-create.md)」を参照してください。 OpenSSL のパラメーターの詳細については、[https://www.openssl.org/docs/apps/openssl.html](https://www.openssl.org/docs/apps/openssl.html) のドキュメントを参照してください。

これらのファイルを作成したら、`.cer` ファイルを Azure にアップロードします。 [Azure portal][management-portal] の **[設定]** タブで、 **[アップロード]** を選択します。 `.pem` ファイルを保存した場所をメモします。

サブスクリプション ID を取得した後、証明書を作成し、`.cer` ファイルを Azure にアップロードして、Azure 管理エンドポイントに接続します。 サブスクリプション ID と `.pem` ファイルのパスを **ServiceManagementService** に渡すことで、接続します。

```python
from azure import *
from azure.servicemanagement import *

subscription_id = '<your_subscription_id>'
certificate_path = '<path_to_.pem_certificate>'

sms = ServiceManagementService(subscription_id, certificate_path)
```

前の例では、 `sms` は **ServiceManagementService** オブジェクトです。 **ServiceManagementService** クラスは、Azure サービスを管理するときに使用する主要なクラスです。

### <a name="management-certificates-on-windows-makecert"></a>Windows での管理証明書 (MakeCert)
`makecert.exe` を使用して、マシン上で自己署名管理証明書を作成できます。 **管理者** として **Visual Studio コマンド プロンプト** を開き、次のコマンドを使用して、*AzureCertificate* を、使用する証明書の名前に置き換えます。

```console
makecert -sky exchange -r -n "CN=AzureCertificate" -pe -a sha1 -len 2048 -ss My "AzureCertificate.cer"
```

このコマンドにより、`.cer` ファイルが作成され、**個人用** 証明書ストアにインストールされます。 詳細については、「[Azure Cloud Services の証明書の概要](cloud-services-certs-create.md)」を参照してください。

証明書を作成したら、`.cer` ファイルを Azure にアップロードします。 [Azure portal][management-portal] の **[設定]** タブで、 **[アップロード]** を選択します。

サブスクリプション ID を取得した後、証明書を作成し、`.cer` ファイルを Azure にアップロードして、Azure 管理エンドポイントに接続します。 サブスクリプション ID と **個人用** 証明書ストア内の証明書の場所を **ServiceManagementService** に渡すことで、接続します (再度、*AzureCertificate* を証明書の名前に置き換えます)。

```python
from azure import *
from azure.servicemanagement import *

subscription_id = '<your_subscription_id>'
certificate_path = 'CURRENT_USER\\my\\AzureCertificate'

sms = ServiceManagementService(subscription_id, certificate_path)
```

前の例では、 `sms` は **ServiceManagementService** オブジェクトです。 **ServiceManagementService** クラスは、Azure サービスを管理するときに使用する主要なクラスです。

## <a name="list-available-locations"></a><a name="ListAvailableLocations"> </a>利用可能な場所を列挙する
ホスティング サービスに利用できる場所を列挙するには、**list\_locations** メソッドを使用します。

```python
from azure import *
from azure.servicemanagement import *

sms = ServiceManagementService(subscription_id, certificate_path)

result = sms.list_locations()
for location in result:
    print(location.name)
```

クラウド サービスやストレージ サービスを作成するときは、有効な場所を指定する必要があります。 **list\_locations** メソッドでは常に、現在利用可能な場所の最新のリストが返されます。 この記事の執筆時点で利用可能な場所は次のとおりです。

* 西ヨーロッパ
* 北ヨーロッパ
* 東南アジア
* 東アジア
* 米国中部
* 米国中北部
* 米国中南部
* 米国西部
* 米国東部
* 東日本
* 西日本
* ブラジル南部
* オーストラリア東部
* オーストラリア南東部

## <a name="create-a-cloud-service"></a><a name="CreateCloudService"> </a>クラウド サービスを作成する
Azure でアプリケーションを作成して実行する場合、そのコードと構成は、総称して [Azure クラウド サービス][cloud service]と呼ばれます。 (以前の Azure リリースでは、*ホステッド サービス* と呼ばれていました)。**create\_hosted\_service** メソッドを使用して、新しいホステッド サービスを作成できます。 サービスを作成するには、ホステッド サービス名 (Azure 上で一意の名前)、ラベル (Base64 に自動的にエンコードされます)、説明、場所を渡します。

```python
from azure import *
from azure.servicemanagement import *

sms = ServiceManagementService(subscription_id, certificate_path)

name = 'myhostedservice'
label = 'myhostedservice'
desc = 'my hosted service'
location = 'West US'

sms.create_hosted_service(name, label, desc, location)
```

**list\_hosted\_services** メソッドを使用して、サブスクリプションのすべてのホステッド サービスを列挙できます。

```python
result = sms.list_hosted_services()

for hosted_service in result:
    print('Service name: ' + hosted_service.service_name)
    print('Management URL: ' + hosted_service.url)
    print('Location: ' + hosted_service.hosted_service_properties.location)
    print('')
```

特定のホステッド サービスに関する情報を取得するには、**get\_hosted\_service\_properties** メソッドにホステッド サービス名を渡します。

```python
hosted_service = sms.get_hosted_service_properties('myhostedservice')

print('Service name: ' + hosted_service.service_name)
print('Management URL: ' + hosted_service.url)
print('Location: ' + hosted_service.hosted_service_properties.location)
```

クラウド サービスを作成した後、**create\_deployment** メソッドを使用してコードをサービスにデプロイします。

## <a name="delete-a-cloud-service"></a><a name="DeleteCloudService"> </a>クラウド サービスを削除する
クラウド サービスを削除するには、そのサービス名を **delete\_hosted\_service** メソッドに渡します。

```python
sms.delete_hosted_service('myhostedservice')
```

サービスを削除する前に、そのサービスのすべてのデプロイを最初に削除する必要があります。 詳細については、「[デプロイの削除](#DeleteDeployment)」を参照してください。

## <a name="delete-a-deployment"></a><a name="DeleteDeployment"> </a>デプロイを削除する
デプロイを削除するには、**delete\_deployment** メソッドを使用します。 次の例では、`v1` という名前のデプロイを削除する方法を示しています。

```python
from azure import *
from azure.servicemanagement import *

sms = ServiceManagementService(subscription_id, certificate_path)

sms.delete_deployment('myhostedservice', 'v1')
```

## <a name="create-a-storage-service"></a><a name="CreateStorageService"> </a>ストレージ サービスを作成する
[ストレージ サービス](../storage/common/storage-account-create.md)を使用すると、Azure の [BLOB](../storage/blobs/storage-quickstart-blobs-python.md)、[テーブル](../cosmos-db/table-storage-how-to-use-python.md)、[キュー](../storage/queues/storage-python-how-to-use-queue-storage.md)にアクセスできます。 ストレージ サービスを作成するには、サービスの名前 (Azure 内で一意の 3 から 24 文字の小文字) が必要です。 また、説明、ラベル (最大 100 文字、Base64 に自動的にエンコードされます)、場所も必要です。 次の例では、場所を指定してストレージ サービスを作成する方法を示しています。

```python
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
```

前の例では、**create\_storage\_account** 処理のステータスを取得するため、**create\_storage\_account** から返された結果を **get\_operation\_status** メソッドに渡しています。 

ストレージ アカウントとそのプロパティを列挙するには、**list\_storage\_accounts** メソッドを使用します。

```python
from azure import *
from azure.servicemanagement import *

sms = ServiceManagementService(subscription_id, certificate_path)

result = sms.list_storage_accounts()
for account in result:
    print('Service name: ' + account.service_name)
    print('Location: ' + account.storage_service_properties.location)
    print('')
```

## <a name="delete-a-storage-service"></a><a name="DeleteStorageService"> </a>ストレージ サービスを削除する
ストレージ サービスを削除するには、そのサービス名を **delete\_storage\_account** メソッドに渡します。 ストレージ サービスを削除すると、サービスに格納されているすべてのデータ (BLOB、テーブル、キュー) が削除されます。

```python
from azure import *
from azure.servicemanagement import *

sms = ServiceManagementService(subscription_id, certificate_path)

sms.delete_storage_account('mystorageaccount')
```

## <a name="list-available-operating-systems"></a><a name="ListOperatingSystems"> </a>利用可能なオペレーティング システムを列挙する
ホスティング サービスに利用できるオペレーティング システムを列挙するには、**list\_operating\_systems** メソッドを使用します。

```python
from azure import *
from azure.servicemanagement import *

sms = ServiceManagementService(subscription_id, certificate_path)

result = sms.list_operating_systems()

for os in result:
    print('OS: ' + os.label)
    print('Family: ' + os.family_label)
    print('Active: ' + str(os.is_active))
```

または、**list\_operating\_system\_families** メソッドを使用することもできます。このメソッドでは、オペレーティング システムがファミリ別にグループ化されます。

```python
result = sms.list_operating_system_families()

for family in result:
    print('Family: ' + family.label)
    for os in family.operating_systems:
        if os.is_active:
            print('OS: ' + os.label)
            print('Version: ' + os.version)
    print('')
```

## <a name="create-an-operating-system-image"></a><a name="CreateVMImage"> </a>オペレーティング システム イメージを作成する
オペレーティング システム イメージをイメージ リポジトリに追加するには、**add\_os\_image** メソッドを使用します。

```python
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
```

利用できるオペレーティング システム イメージを列挙するには、**list\_os\_images** メソッドを使用します。 このメソッドでは、すべてのプラットフォーム イメージとユーザー イメージが対象となります。

```python
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
```

## <a name="delete-an-operating-system-image"></a><a name="DeleteVMImage"> </a>オペレーティング システム イメージを削除する
ユーザー イメージを削除するには、**delete\_os\_image** メソッドを使用します。

```python
from azure import *
from azure.servicemanagement import *

sms = ServiceManagementService(subscription_id, certificate_path)

result = sms.delete_os_image('mycentos')

operation_result = sms.get_operation_status(result.request_id)
print('Operation status: ' + operation_result.status)
```

## <a name="create-a-virtual-machine"></a><a name="CreateVM"></a>仮想マシンを作成する
仮想マシンを作成するには、最初に [クラウド サービス](#CreateCloudService)を作成する必要があります。 その後で、**create\_virtual\_machine\_deployment** メソッドを使用して、仮想マシンのデプロイを作成します。

```python
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
```

## <a name="delete-a-virtual-machine"></a><a name="DeleteVM"> </a>仮想マシンを削除する
仮想マシンを削除するには、最初に **delete\_deployment** メソッドを使用してデプロイを削除します。

```python
from azure import *
from azure.servicemanagement import *

sms = ServiceManagementService(subscription_id, certificate_path)

sms.delete_deployment(service_name='myvm',
    deployment_name='myvm')
```

その後で、**delete\_hosted\_service** メソッドを使用して、クラウド サービスを削除することができます。

```python
sms.delete_hosted_service(service_name='myvm')
```

## <a name="create-a-virtual-machine-from-a-captured-virtual-machine-image"></a>キャプチャした仮想マシン イメージからの仮想マシンの作成
VM イメージをキャプチャするには、まず、**capture\_vm\_image** メソッドを呼び出します。

```python
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
```

イメージが正常にキャプチャされたことを確認するには、**list\_vm\_images** API を使用します。 イメージが結果に表示されていることを確認します。

```python
images = sms.list_vm_images()
```

キャプチャしたイメージを使用して仮想マシンを作成するために、前の手順と同様に、**create\_virtual\_machine\_deployment** メソッドを使用しますが、今回は vm_image_name を渡します。

```python
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
```

クラシック デプロイ モデルで Linux 仮想マシンをキャプチャする方法について詳しくは、[Linux 仮想マシンのキャプチャ](/previous-versions/azure/virtual-machines/linux/classic/capture-image-classic)に関するページをご覧ください。

クラシック デプロイ モデルで Windows 仮想マシンをキャプチャする方法について詳しくは、[Windows 仮想マシンのキャプチャ](/previous-versions/azure/virtual-machines/windows/classic/capture-image-classic)に関するページをご覧ください。

## <a name="next-steps"></a><a name="What's Next"> </a>次の手順
これで、サービス管理の基本を学習できました。[Azure Python SDK の完全な API のリファレンス ドキュメント](https://azure-sdk-for-python.readthedocs.org/)にアクセスして、複雑なタスクを簡単に実行することにより、Python アプリケーションを管理できます。

詳細については、 [Python デベロッパー センター](https://azure.microsoft.com/develop/python/)を参照してください。

[What is service management?]: #WhatIs
[Concepts]: #Concepts
[Connect to service management]: #Connect
[List available locations]: #ListAvailableLocations
[Create a cloud service]: #CreateCloudService
[Delete a cloud service]: #DeleteCloudService
[Create a deployment]: #CreateDeployment
[Update a deployment]: #UpdateDeployment
[Move deployments between staging and production]: #MoveDeployments
[Delete a deployment]: #DeleteDeployment
[Create a storage service]: #CreateStorageService
[Delete a storage service]: #DeleteStorageService
[List available operating systems]: #ListOperatingSystems
[Create an operating system image]: #CreateVMImage
[Delete an operating system image]: #DeleteVMImage
[Create a virtual machine]: #CreateVM
[Delete a virtual machine]: #DeleteVM
[Next steps]: #NextSteps
[management-portal]: https://portal.azure.com/
[svc-mgmt-rest-api]: /previous-versions/azure/ee460799(v=azure.100)


[cloud service]:/azure/cloud-services/