---
title: モジュールによるデバイス上でのロジックの実行方法について - Azure IoT Edge | Microsoft Docs
description: Azure IoT Edge モジュールは、IoT Edge デバイス上でビジネス ロジックを実行できるように、リモートでのデプロイと管理が可能なコンテナー化されたロジックの単位です。
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 03/21/2019
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.custom: seodec18
ms.openlocfilehash: d1e2e35dafd90c16e9d0dbf38afb1e981653d1fe
ms.sourcegitcommit: 90dcc3d427af1264d6ac2b9bde6cdad364ceefcc
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/21/2019
ms.locfileid: "58311103"
---
# <a name="understand-azure-iot-edge-modules"></a>Azure IoT Edge モジュールについて

Azure IoT Edge では、ビジネス ロジックを*モジュール*形式でエッジに展開および管理できます。 Azure IoT Edge モジュールは、IoT Edge によって管理される計算の最小単位であり、Azure Stream Analytics などの Azure サービスまたは独自ソリューション固有のコードを含めることができます。 モジュールを開発、デプロイ、および管理する方法を理解するには、モジュールの 4 つの概念的要素を考えると理解が深まります。

* **モジュール イメージ**。モジュールを定義するソフトウェアを含むパッケージです。
* **モジュール インスタンス**。IoT Edge デバイスでモジュール イメージを実行している計算の特定の単位です。 モジュール インスタンスは、IoT Edge ランタイムによって開始されます。
* **モジュール ID**。IoT Hub に格納されている情報 (セキュリティ資格情報を含む) の一部で、各モジュール インスタンスに関連付けられています。
* **モジュール ツイン**。IoT Hub に格納されている JSON ドキュメントで、メタデータ、構成、条件などのモジュール インスタンスの状態情報が含まれています。 

## <a name="module-images-and-instances"></a>モジュール イメージとインスタンス

IoT Edge モジュール イメージには、IoT Edge ランタイムの管理、セキュリティ、および通信機能を活用するアプリケーションが含まれています。 独自のモジュール イメージを開発したり、Azure Stream Analytics などのサポートされている Azure サービスからモジュール イメージをエクスポートしたりすることもできます。
モジュール イメージはクラウドに存在し、さまざまなソリューションで更新、変更、展開できます。 たとえば、生産ラインの出力を予測する機械学習を使用するモジュールは、コンピューター ビジョンを使用してドローンを制御するモジュールとは別のイメージとして存在します。 

モジュール イメージがデバイスに展開され、IoT Edge ランタイムによって開始されるたびに、該当モジュールの新しいインスタンスが作成されます。 世界の異なる地域にある 2 つのデバイスが同じモジュール イメージを使用できます。 ただし、各デバイスは、モジュールの起動時には、それぞれ独自のモジュール インスタンスを持ちます。 

![図 - クラウド内のモジュール イメージ - デバイス上のモジュール インスタンス](./media/iot-edge-modules/image_instance.png)

実装では、モジュール イメージはリポジトリ内のコンテナー イメージとして存在し、モジュール インスタンスはデバイス上のコンテナーです。 

<!--
As use cases for Azure IoT Edge grow, new types of module images and instances will be created. For example, resource constrained devices cannot run containers so may require module images that exist as dynamic link libraries and instances that are executables. 
-->

## <a name="module-identities"></a>モジュール ID

新しいモジュール インスタンスが IoT Edge ランタイムによって作成されると、作成されたインスタンスには対応するモジュール ID が関連付けられます。 モジュール ID は IoT Hub に格納され、その特定のモジュール インスタンスのすべてのローカルおよびクラウド通信のアドレス指定とセキュリティ スコープとして採用されます。

モジュール インスタンスに関連付けられる ID は、インスタンスが実行されているデバイスの ID と、ソリューション内のモジュールに付けた名前によって決まります。 たとえば、Azure Stream Analytics を使用するモジュール `insight` を呼び出し、`Hannover01` というデバイスに展開した場合、IoT Edge ランタイムは `/devices/Hannover01/modules/insight` という対応するモジュール ID を作成します。

明らかに、1 つのモジュール イメージを同じデバイスに複数回展開する必要があるシナリオで、同じイメージを異なる名前で複数回展開することができます。

![図 - デバイス内およびデバイス間でモジュール ID が一意になっている](./media/iot-edge-modules/identity.png)

## <a name="module-twins"></a>モジュール ツイン

各モジュール インスタンスは、モジュール インスタンスの構成に使用できる、対応するモジュール ツインも持ちます。 インスタンスとツインは、モジュール ID によってお互いに関連付けられます。 

モジュール ツインは、モジュールの情報と構成プロパティを格納する JSON ドキュメントです。 この概念は、IoT Hub の[デバイス ツイン](../iot-hub/iot-hub-devguide-device-twins.md)の概念に似ています。 モジュール ツインの構造は、デバイス ツインと同じです。 両方の種類のツインと対話するために使用する API も同じです。 2 つの唯一の違いは、クライアント SDK をインスタンス化するために使用する ID です。 

```csharp
// Create a ModuleClient object. This ModuleClient will act on behalf of a 
// module since it is created with a module’s connection string instead 
// of a device connection string. 
ModuleClient client = new ModuleClient.CreateFromEnvironmentAsync(settings); 
await client.OpenAsync(); 
 
// Get the module twin 
Twin twin = await client.GetTwinAsync(); 
```

## <a name="offline-capabilities"></a>オフライン機能

Azure IoT Edge は、IoT Edge デバイス上でのオフライン操作をサポートしています。 これらの機能は現在は制限されています。 その他のオフライン機能はパブリック プレビューで利用できます。 詳細については、「[IoT Edge デバイス、モジュール、子デバイスの拡張オフライン機能について](offline-capabilities.md)」を参照してください。

IoT Edge モジュールは、次の要件を満たしている場合、既定の時間よりも長くオフライン状態を継続できます。 

* **メッセージ Time to Live (TTL) が有効期限切れになっていない**。 メッセージ TTL の既定値は 2 時間ですが、IoT Edge ハブのストア アンド フォワード構成で増減することができます。 
* **オフライン時にモジュールを IoT Edge ハブに対して再認証する必要がない**。 モジュールは、IoT ハブとのアクティブな接続がある IoT Edge ハブに対してのみ認証を実行できます。 何らかの理由で再起動された場合には、モジュールを再認証する必要があります。 SAS トークンの有効期限が切れた後でも、モジュールから IoT Edge ハブにメッセージを送信することはできます。 接続が再開されると、IoT Edge ハブはモジュールからの新しいトークンを要求し、それを IoT ハブに対して検証します。 検証が成功した場合、IoT Edge ハブは、保存されているモジュール メッセージを転送します (モジュールのトークンが期限切れになっている間に送信されたメッセージも転送します)。 
* **オフライン時にメッセージを送信したモジュールが、接続の再開時にまだ機能している**。 IoT ハブに再接続する場合、IoT Edge ハブは、モジュール メッセージを転送する前に、新しいモジュール トークンを検証する必要があります (以前のトークンが期限切れの場合)。 モジュールが新しいトークンを提供できない場合、IoT Edge ハブは、モジュールの保存済みメッセージに対してアクションを実行できません。 
* **IoT Edge ハブに、メッセージを保存できるだけのディスク領域がある**。 既定では、メッセージは IoT Edge ハブ コンテナーのファイルシステムに保存されます。 なお、メッセージを保存するためのマウント済みボリュームを指定する構成オプションもあります。 いずれの場合も、IoT ハブへの遅延配信を行うには、メッセージを保存できるだけの容量が必要になります。  


## <a name="next-steps"></a>次の手順
 - [IoT Edge モジュールを開発するための要件とツールについて理解する](module-development.md)
 - [Azure IoT Edge ランタイムとそのアーキテクチャの概要](iot-edge-runtime.md)

