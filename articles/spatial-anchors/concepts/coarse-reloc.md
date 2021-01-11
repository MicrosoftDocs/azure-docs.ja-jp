---
title: 粗い再局在化
description: 粗い再局在化を使用して、近くにあるアンカーを見つける方法について説明します。
author: bucurb
manager: dacoghl
services: azure-spatial-anchors
ms.author: bobuc
ms.date: 09/18/2019
ms.topic: conceptual
ms.service: azure-spatial-anchors
ms.custom: devx-track-csharp
ms.openlocfilehash: 02f5c570b7400266ac648eaa257fb76d26819a40
ms.sourcegitcommit: 419cf179f9597936378ed5098ef77437dbf16295
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/27/2020
ms.locfileid: "89003186"
---
# <a name="coarse-relocalization"></a>粗い再局在化

粗い再局在化は、次の質問に対する最初の回答を提供する機能です。*自分のデバイスが今どこにあるか/監視するべきコンテンツは何か* 応答は正確ではありませんが、代わりに次の形式になります。*これらのアンカーに近づいています。いずれかを検索してみてください*。

粗い再局在化は、さまざまなデバイス上のセンサーの読み取りを、アンカーの作成とクエリの両方に関連付けることによって機能します。 屋外のシナリオでは、センサー データは通常、デバイスの GPS (Global Positioning System) 位置です。 GPS が使用できないか、信頼性が低い (屋内など) 場合、センサー データは範囲内の WiFi アクセス ポイントと Bluetooth ビーコンで構成されます。 すべての収集されたセンサー データは、空間インデックスを維持するために役立ちます。空間インデックスは、ご自分のデバイスの約 100 m 以内にあるアンカーをすばやく特定するために Azure Spatial Anchors によって使用されます。

粗い再局在化によって有効になったアンカーのすばやい検索により、(何百万という地理的に分散した) アンカーの世界規模の収集によってサポートされるアプリケーションの開発が簡略化されます。 アンカー管理の複雑さはすべて隠されているため、ご自分の優れたアプリケーション ロジックに専念することができます。 アンカーの最も手間のかかる部分はすべて、Azure Spatial Anchors によってバックグラウンドで自動的に実行されます。

## <a name="collected-sensor-data"></a>収集されたセンサー データ

アンカー サービスに送信できるセンサー データは、次のいずれかになります。

* GPS の位置: 緯度、経度、高度。
* 範囲内の WiFi アクセス ポイントの信号強度。
* 範囲内の Bluetooth ビーコンの信号強度。

一般に、アプリケーションが GPS、WiFi、または BLE データにアクセスするには、デバイス固有のアクセス許可を取得する必要があります。 また、上記のセンサー データの中には、仕様上、特定のプラットフォームでは使用できないものもあります。 このような状況を考慮して、センサー データの収集はオプションで、既定ではオフになっています。

## <a name="set-up-the-sensor-data-collection"></a>センサー データの収集を設定する

まず、センサー フィンガープリント プロバイダーを作成し、セッションにそれを認識させるようにしましょう。

# <a name="c"></a>[C#](#tab/csharp)

```csharp
// Create the sensor fingerprint provider
sensorProvider = new PlatformLocationProvider();

// Create and configure the session
cloudSpatialAnchorSession = new CloudSpatialAnchorSession();

// Inform the session it can access sensor data from your provider
cloudSpatialAnchorSession.LocationProvider = sensorProvider;
```

# <a name="objc"></a>[ObjC](#tab/objc)

```objc
// Create the sensor fingerprint provider
ASAPlatformLocationProvider *sensorProvider;
sensorProvider = [[ASAPlatformLocationProvider alloc] init];

// Create and configure the session
cloudSpatialAnchorSession = [[ASACloudSpatialAnchorSession alloc] init];

// Inform the session it can access sensor data from your provider
cloudSpatialAnchorSession.locationProvider = sensorProvider;
```

# <a name="swift"></a>[Swift](#tab/swift)

```swift
// Create the sensor fingerprint provider
var sensorProvider: ASAPlatformLocationProvider?
sensorProvider = ASAPlatformLocationProvider()

// Create and configure the session
cloudSpatialAnchorSession = ASACloudSpatialAnchorSession()

// Inform the session it can access sensor data from your provider
cloudSpatialAnchorSession!.locationProvider = sensorProvider
```

# <a name="java"></a>[Java](#tab/java)

```java
// Create the sensor fingerprint provider
PlatformLocationProvider sensorProvider = new PlatformLocationProvider();

// Create and configure the session
cloudSpatialAnchorSession = new CloudSpatialAnchorSession();

// Inform the session it can access sensor data from your provider
cloudSpatialAnchorSession.setLocationProvider(sensorProvider);
```

# <a name="c-ndk"></a>[C++ NDK](#tab/cpp)

```cpp
// Create the sensor fingerprint provider
std::shared_ptr<PlatformLocationProvider> sensorProvider;
sensorProvider = std::make_shared<PlatformLocationProvider>();

// Create and configure the session
cloudSpatialAnchorSession = std::make_shared<CloudSpatialAnchorSession>();

// Inform the session it can access sensor data from your provider
cloudSpatialAnchorSession->LocationProvider(sensorProvider);
```

# <a name="c-winrt"></a>[C++ WinRT](#tab/cppwinrt)
```cpp
// Create the sensor fingerprint provider
PlatformLocationProvider sensorProvider = PlatformLocationProvider();

// Create and configure the session
cloudSpatialAnchorSession = CloudSpatialAnchorSession();

// Inform the session it can access sensor data from your provider
cloudSpatialAnchorSession.LocationProvider(sensorProvider);
```
---

次に、粗い再局在化に使用するセンサーを決定する必要があります。 この決定は、開発中のアプリケーションに固有のものですが、次の表に示す推奨事項を参考にしてください。

|                 | 屋内 | 屋外 |
|-----------------|---------|----------|
| **GPS**         | Off | On |
| **WiFi**        | On | オン (オプション) |
| **BLE ビーコン** | On (オプションで省略可能、以下を参照) | Off |

### <a name="enabling-gps"></a>GPS を有効化する

アプリケーションが既にデバイスの GPS 位置にアクセスするアクセス許可を持っている場合は、そのアプリケーションを使用するように Azure Spatial Anchors を構成できます。

# <a name="c"></a>[C#](#tab/csharp)

```csharp
sensorProvider.Sensors.GeoLocationEnabled = true;
```

# <a name="objc"></a>[ObjC](#tab/objc)

```objc
ASASensorCapabilities *sensors = locationProvider.sensors;
sensors.geoLocationEnabled = true;
```

# <a name="swift"></a>[Swift](#tab/swift)

```swift
let sensors = locationProvider?.sensors
sensors.geoLocationEnabled = true
```

# <a name="java"></a>[Java](#tab/java)

```java
SensorCapabilities sensors = sensorProvider.getSensors();
sensors.setGeoLocationEnabled(true);
```

# <a name="c-ndk"></a>[C++ NDK](#tab/cpp)

```cpp
const std::shared_ptr<SensorCapabilities>& sensors = sensorProvider->Sensors();
sensors->GeoLocationEnabled(true);
```

# <a name="c-winrt"></a>[C++ WinRT](#tab/cppwinrt)

```cpp
SensorCapabilities sensors = sensorProvider.Sensors()
sensors.GeoLocationEnabled(true);
```

---

アプリケーションで GPS を使用する場合、ハードウェアによって提供される測定値は、一般的に次のものになることに注意してください。

* 非同期で低周波数 (1 Hz 未満)。
* 信頼性が低いまたはノイズが多い (平均 7 m の標準偏差)。

一般に、デバイス OS と Azure Spatial Anchors ではどちらも、これらの問題を軽減するために、未加工の GPS 信号で何らかのフィルター処理と外挿が実行されます。 この追加処理には収束に時間がかかるため、最適な結果を得るには、次のことを試してみる必要があります。

* アプリケーションでできるだけ早くセンサー フィンガープリント プロバイダーを 1 つ作成する
* そのセンサー フィンガープリント プロバイダーを複数のセッション間でキープ アライブする
* そのセンサー フィンガープリント プロバイダーを複数のセッション間で共有する

アンカー セッション外でセンサー フィンガープリント プロバイダーを使用することを計画している場合は、センサーの推定を要求する前に開始するようにしてください。 たとえば、次のコードでは、マップ上のデバイスの位置がリアルタイムで更新されます。

# <a name="c"></a>[C#](#tab/csharp)

```csharp
// Game about to start, start tracking the sensors
sensorProvider.Start();

// Game loop
while (m_isRunning)
{
    // Get the GPS estimate
    GeoLocation geoPose = sensorProvider.GetLocationEstimate();

    // Paint it on the map
    drawCircle(
        x: geoPose.Longitude,
        y: geoPose.Latitude,
        radius: geoPose.HorizontalError);
}

// Game ended, no need to track the sensors anymore
sensorProvider.Stop();
```

# <a name="objc"></a>[ObjC](#tab/objc)

```objc
// Game about to start, start tracking the sensors
[sensorProvider start];

// Game loop
while (m_isRunning)
{
    // Get the GPS estimate
    ASAGeoLocation *geoPose = [sensorProvider getLocationEstimate];

    // Paint it on the map
    drawCircle(geoPose.longitude, geoPose.latitude, geoPose.horizontalError);
}

// Game ended, no need to track the sensors anymore
[sensorProvider stop];
```

# <a name="swift"></a>[Swift](#tab/swift)

```swift
// Game about to start, start tracking the sensors
sensorProvider?.start()

// Game loop
while m_isRunning
{
    // Get the GPS estimate
    var geoPose: ASAGeoLocation?
    geoPose = sensorProvider?.getLocationEstimate()

    // Paint it on the map
    drawCircle(geoPose.longitude, geoPose.latitude, geoPose.horizontalError)
}

// Game ended, no need to track the sensors anymore
sensorProvider?.stop()
```

# <a name="java"></a>[Java](#tab/java)

```java
// Game about to start, start tracking the sensors
sensorProvider.start();

// Game loop
while (m_isRunning)
{
    // Get the GPS estimate
    GeoLocation geoPose = sensorProvider.getLocationEstimate();

    // Paint it on the map
    drawCircle(geoPose.getLongitude(), geoPose.getLatitude(), geoPose.getHorizontalError());
}

// Game ended, no need to track the sensors anymore
sensorProvider.stop();
```

# <a name="c-ndk"></a>[C++ NDK](#tab/cpp)

```cpp
// Game about to start, start tracking the sensors
sensorProvider->Start();

// Game loop
while (m_isRunning)
{
    // Get the GPS estimate
    std::shared_ptr<GeoLocation> geoPose = sensorProvider->GetLocationEstimate();

    // Paint it on the map
    drawCircle(geoPose->Longitude(), geoPose->Latitude(), geoPose->HorizontalError());
}

// Game ended, no need to track the sensors anymore
sensorProvider->Stop();
```

# <a name="c-winrt"></a>[C++ WinRT](#tab/cppwinrt)

```cpp
// Game about to start, start tracking the sensors
sensorProvider.Start();

// Game loop
while (m_isRunning)
{
    // Get the GPS estimate
    GeoLocation geoPose = sensorProvider.GetLocationEstimate();

    // Paint it on the map
    drawCircle(geoPose.Longitude(), geoPose.Latitude(), geoPose.HorizontalError());
}

// Game ended, no need to track the sensors anymore
sensorProvider.Stop();
```

---

### <a name="enabling-wifi"></a>WiFi を有効化する

アプリケーションが既にデバイスの WiFi の状態にアクセスするアクセス許可を持っている場合は、そのアプリケーションを使用するように Azure Spatial Anchors を構成できます。

# <a name="c"></a>[C#](#tab/csharp)

```csharp
sensorProvider.Sensors.WifiEnabled = true;
```

# <a name="objc"></a>[ObjC](#tab/objc)

```objc
ASASensorCapabilities *sensors = locationProvider.sensors;
sensors.wifiEnabled = true;
```

# <a name="swift"></a>[Swift](#tab/swift)

```swift
let sensors = locationProvider?.sensors
sensors.wifiEnabled = true
```

# <a name="java"></a>[Java](#tab/java)

```java
SensorCapabilities sensors = sensorProvider.getSensors();
sensors.setWifiEnabled(true);
```

# <a name="c-ndk"></a>[C++ NDK](#tab/cpp)

```cpp
const std::shared_ptr<SensorCapabilities>& sensors = sensorProvider->Sensors();
sensors->WifiEnabled(true);
```

# <a name="c-winrt"></a>[C++ WinRT](#tab/cppwinrt)

```cpp
SensorCapabilities sensors = sensorProvider.Sensors()
sensors.WifiEnabled(true);
```

---

アプリケーションで WiFi を使用する場合、ハードウェアによって提供される測定値は、一般的に次のものになることに注意してください。

* 非同期で低周波数 (0.1 Hz 未満)。
* OS レベルで調整される可能性がある。
* 信頼性が低いまたはノイズが多い (平均 3 dBm の標準偏差)。

Azure Spatial Anchors では、このような問題を軽減するために、セッション中にフィルター処理された WiFi 信号強度マップの作成が試行されます。 最適な結果を得るには、次を試す必要があります。

* 最初のアンカーを配置する前に、セッションを適切に作成する。
* セッションを可能な限り長くキープ アライブする (つまり、すべてのアンカーとクエリを 1 つのセッションで作成する)。

### <a name="enabling-bluetooth-beacons"></a>Bluetooth ビーコンを有効化する

アプリケーションが既にデバイスの Bluetooth の状態にアクセスするアクセス許可を持っている場合は、そのアプリケーションを使用するように Azure Spatial Anchors を構成できます。

# <a name="c"></a>[C#](#tab/csharp)

```csharp
sensorProvider.Sensors.BluetoothEnabled = true;
```

# <a name="objc"></a>[ObjC](#tab/objc)

```objc
ASASensorCapabilities *sensors = locationProvider.sensors;
sensors.bluetoothEnabled = true;
```

# <a name="swift"></a>[Swift](#tab/swift)

```swift
let sensors = locationProvider?.sensors
sensors.bluetoothEnabled = true
```

# <a name="java"></a>[Java](#tab/java)

```java
SensorCapabilities sensors = sensorProvider.getSensors();
sensors.setBluetoothEnabled(true);
```

# <a name="c-ndk"></a>[C++ NDK](#tab/cpp)

```cpp
const std::shared_ptr<SensorCapabilities>& sensors = sensorProvider->Sensors();
sensors->BluetoothEnabled(true);
```

# <a name="c-winrt"></a>[C++ WinRT](#tab/cppwinrt)

```cpp
SensorCapabilities sensors = sensorProvider.Sensors();
sensors.BluetoothEnabled(true);
```

---

ビーコンは通常、UUID や MAC アドレスを含む、すべてのものを構成できる多用途デバイスです。 Azure Spatial Anchors では、ビーコンがその UUID によって一意に識別されると見なされるため、この柔軟性が問題になる可能性があります。 この一意性の確保に失敗すると、空間のワームホールが発生する可能性が高くなります。 最適な結果を得るには、次を行う必要があります。

* ビーコンに一意の UUID を割り当てる。
* それらを (通常は、グリッドなどの通常のパターンで) デプロイする。
* 一意のビーコン UUID の一覧をセンサー フィンガープリント プロバイダーに渡す。

# <a name="c"></a>[C#](#tab/csharp)

```csharp
sensorProvider.Sensors.KnownBeaconProximityUuids = new[]
{
    "22e38f1a-c1b3-452b-b5ce-fdb0f39535c1",
    "a63819b9-8b7b-436d-88ec-ea5d8db2acb0",
    . . .
};
```

# <a name="objc"></a>[ObjC](#tab/objc)

```objc
NSArray *uuids = @[@"22e38f1a-c1b3-452b-b5ce-fdb0f39535c1", @"a63819b9-8b7b-436d-88ec-ea5d8db2acb0"];

ASASensorCapabilities *sensors = locationProvider.sensors;
sensors.knownBeaconProximityUuids = uuids;
```

# <a name="swift"></a>[Swift](#tab/swift)

```swift
let uuids = [String]()
uuids.append("22e38f1a-c1b3-452b-b5ce-fdb0f39535c1")
uuids.append("a63819b9-8b7b-436d-88ec-ea5d8db2acb0")

let sensors = locationProvider?.sensors
sensors.knownBeaconProximityUuids = uuids
```

# <a name="java"></a>[Java](#tab/java)

```java
String uuids[] = new String[2];
uuids[0] = "22e38f1a-c1b3-452b-b5ce-fdb0f39535c1";
uuids[1] = "a63819b9-8b7b-436d-88ec-ea5d8db2acb0";

SensorCapabilities sensors = sensorProvider.getSensors();
sensors.setKnownBeaconProximityUuids(uuids);
```

# <a name="c-ndk"></a>[C++ NDK](#tab/cpp)

```cpp
std::vector<std::string> uuids;
uuids.push_back("22e38f1a-c1b3-452b-b5ce-fdb0f39535c1");
uuids.push_back("a63819b9-8b7b-436d-88ec-ea5d8db2acb0");

const std::shared_ptr<SensorCapabilities>& sensors = sensorProvider->Sensors();
sensors->KnownBeaconProximityUuids(uuids);
```

# <a name="c-winrt"></a>[C++ WinRT](#tab/cppwinrt)

```cpp
std::vector<winrt::hstring> uuids;
uuids.emplace_back("22e38f1a-c1b3-452b-b5ce-fdb0f39535c1");
uuids.emplace_back("a63819b9-8b7b-436d-88ec-ea5d8db2acb0");

SensorCapabilities sensors = sensorProvider.Sensors();
sensors.KnownBeaconProximityUuids(uuids);
```

---

Azure Spatial Anchors では、既知のビーコン近接 UUID の一覧にある Bluetooth ビーコンのみが追跡されます。 許可リストに登録された UUID を取得するようにプログラミングされた悪意のあるビーコンは、サービスの品質に悪影響を及ぼす可能性があります。 そのため、ビーコンは、デプロイを制御できるキュレートされた空間でのみ使用する必要があります。

## <a name="querying-with-sensor-data"></a>センサー データを使用してクエリを実行する

センサー データに関連付けられたアンカーを作成したら、デバイスから報告されたセンサーの測定値を使用してそれらの取得を開始できます。 見つかることを想定している既知のアンカーのリストをサービスに提供する必要がなくなりました。その代わりに、デバイスのオンボード センサーによって報告されたデバイスの場所をサービスに知らせるだけです。 次に、Azure Spatial Anchors によって、デバイスに近いアンカーのセットが判別され、それらの視覚的な一致が試行されます。

クエリでセンサー データを使用するには、まず "near device" (デバイス付近) 条件を作成します。

# <a name="c"></a>[C#](#tab/csharp)

```csharp
NearDeviceCriteria nearDeviceCriteria = new NearDeviceCriteria();

// Choose a maximum exploration distance between your device and the returned anchors
nearDeviceCriteria.DistanceInMeters = 5;

// Cap the number of anchors returned
nearDeviceCriteria.MaxResultCount = 25;

anchorLocateCriteria = new AnchorLocateCriteria();
anchorLocateCriteria.NearDevice = nearDeviceCriteria;
```

# <a name="objc"></a>[ObjC](#tab/objc)

```objc
ASANearDeviceCriteria *nearDeviceCriteria = [[ASANearDeviceCriteria alloc] init];

// Choose a maximum exploration distance between your device and the returned anchors
nearDeviceCriteria.distanceInMeters = 5.0f;

// Cap the number of anchors returned
nearDeviceCriteria.maxResultCount = 25;

ASAAnchorLocateCriteria *anchorLocateCriteria = [[ASAAnchorLocateCriteria alloc] init];
anchorLocateCriteria.nearDevice = nearDeviceCriteria;
```

# <a name="swift"></a>[Swift](#tab/swift)

```swift
let nearDeviceCriteria = ASANearDeviceCriteria()!

// Choose a maximum exploration distance between your device and the returned anchors
nearDeviceCriteria.distanceInMeters = 5.0

// Cap the number of anchors returned
nearDeviceCriteria.maxResultCount = 25

let anchorLocateCriteria = ASAAnchorLocateCriteria()!
anchorLocateCriteria.nearDevice = nearDeviceCriteria
```

# <a name="java"></a>[Java](#tab/java)

```java
NearDeviceCriteria nearDeviceCriteria = new NearDeviceCriteria();

// Choose a maximum exploration distance between your device and the returned anchors
nearDeviceCriteria.setDistanceInMeters(5.0f);

// Cap the number of anchors returned
nearDeviceCriteria.setMaxResultCount(25);

AnchorLocateCriteria anchorLocateCriteria = new AnchorLocateCriteria();
anchorLocateCriteria.setNearDevice(nearDeviceCriteria);
```

# <a name="c-ndk"></a>[C++ NDK](#tab/cpp)

```cpp
auto nearDeviceCriteria = std::make_shared<NearDeviceCriteria>();

// Choose a maximum exploration distance between your device and the returned anchors
nearDeviceCriteria->DistanceInMeters(5.0f);

// Cap the number of anchors returned
nearDeviceCriteria->MaxResultCount(25);

auto anchorLocateCriteria = std::make_shared<AnchorLocateCriteria>();
anchorLocateCriteria->NearDevice(nearDeviceCriteria);
```

# <a name="c-winrt"></a>[C++ WinRT](#tab/cppwinrt)

```cpp
NearDeviceCriteria nearDeviceCriteria = NearDeviceCriteria();

// Choose a maximum exploration distance between your device and the returned anchors
nearDeviceCriteria.DistanceInMeters(5.0f);

// Cap the number of anchors returned
nearDeviceCriteria.MaxResultCount(25);

// Set the session's locate criteria
anchorLocateCriteria = AnchorLocateCriteria();
anchorLocateCriteria.NearDevice(nearDeviceCriteria);
```

---

`DistanceInMeters` パラメーターは、コンテンツを取得するためにアンカー グラフを探索する距離を制御します。 たとえば、1 メートルごとに 2 つという一定の密度でアンカーを使用して空間を設定したとします。 さらに、デバイスのカメラが 1 つのアンカーを観察し、サービスによってその検出に成功したとします。 多くの場合、現在観察している単一のアンカーではなく、近くに配置されているすべてのアンカーを取得することの方に関心があるでしょう。 配置したアンカーがグラフに接続されている場合、サービスでグラフの端に従って、近くにあるすべてのアンカーを取得できます。 グラフ走査の実行量は、`DistanceInMeters` によって制御されます。検出されたアンカーに接続されているアンカー (`DistanceInMeters` よりも近い) がすべて取得されます。

`MaxResultCount` の値が大きいと、パフォーマンスに悪影響を及ぼす可能性があることに注意してください。 アプリケーションに適切な値に設定してください。

最後に、センサーベースの検索を使用するようにセッションに指示する必要があります。

# <a name="c"></a>[C#](#tab/csharp)

```csharp
cloudSpatialAnchorSession.CreateWatcher(anchorLocateCriteria);
```

# <a name="objc"></a>[ObjC](#tab/objc)

```objc
[cloudSpatialAnchorSession createWatcher:anchorLocateCriteria];
```

# <a name="swift"></a>[Swift](#tab/swift)

```swift
cloudSpatialAnchorSession!.createWatcher(anchorLocateCriteria)
```

# <a name="java"></a>[Java](#tab/java)

```java
cloudSpatialAnchorSession.createWatcher(anchorLocateCriteria);
```

# <a name="c-ndk"></a>[C++ NDK](#tab/cpp)

```cpp
cloudSpatialAnchorSession->CreateWatcher(anchorLocateCriteria);
```

# <a name="c-winrt"></a>[C++ WinRT](#tab/cppwinrt)

```cpp
cloudSpatialAnchorSession.CreateWatcher(anchorLocateCriteria);
```

---

## <a name="expected-results"></a>予想される結果

一般に、コンシューマー グレードの GPS デバイスはかなり不正確です。 [ZandenGen と Barbeau (2011)][6] による調査では、補助 GPS (A-GPS) を使用した携帯電話の精度の中央値が約 7 メートルであることが報告されています。これは無視するには大きすぎる値です。 これらの測定エラーを考慮するため、サービスではアンカーが GPS 空間での確率分布として扱われます。 そのため、アンカーは、実際の不明な GPS 位置を含む可能性が最も高い (つまり、95% の信頼度) 空間の領域になりました。

GPS を使用してクエリを実行する場合も、同じ理由が適用されます。 デバイスは、実際の不明な GPS 位置を中心とする別の空間信頼領域として表されます。 近くのアンカーの検出は、次の図に示すように、デバイスの信頼領域に*十分近い*信頼領域のアンカーを単純に見つけることと解釈されます。

![GPS を使用したアンカー候補の選択](media/coarse-reloc-gps-separation-distance.png)

GPS 信号の精度は、アンカーの作成時とクエリ実行時の両方において、返されたアンカーのセットに大きな影響を及ぼします。 これに対し、WiFi/ビーコンに基づくクエリでは、クエリと共通するアクセス ポイント/ビーコンが少なくとも 1 つあるすべてのアンカーが考慮されます。 この意味では、WiFi/ビーコンに基づくクエリの結果は、ほとんどの場合、アクセス ポイント/ビーコンの物理的な範囲、および環境障害物によって決まります。

次の表は、センサーの種類ごとに予想される検索空間を推定したものです。

| センサー      | 検索空間の半径 (概算) | 詳細 |
|-------------|:-------:|---------|
| GPS         | 20 m - 30 m | いくつか要因の中でも、特に GPS の不確実性によって決まります。 報告された数値は、A-GPS を使用した携帯電話の GPS 精度の中央値 (7 メートル) に対して推定されたものです。 |
| WiFi        | 50 m - 100 m | ワイヤレス アクセス ポイントの範囲によって決まります。 周波数、伝送器の強さ、物理的な障害物、干渉などによって異なります。 |
| BLE ビーコン |  70 m | ビーコンの範囲によって決まります。 周波数、伝送の強さ、物理的な障害物、干渉などによって異なります。 |

## <a name="per-platform-support"></a>プラットフォームごとのサポート

次の表は、サポートされているプラットフォームごとに収集されるセンサー データと、プラットフォーム固有の注意事項をまとめたものです。

|                 | HoloLens | Android | iOS |
|-----------------|----------|---------|-----|
| **GPS**         | 該当なし | [LocationManager][3] API (GPS とネットワークの両方) でサポートされます | [CLLocationManager][4] API でサポートされます |
| **WiFi**        | 3 秒ごとに約 1 回のスキャンの割合でサポートされます。 | サポートされています。 API レベル 28 以降では、WiFi スキャンは 2 分ごとに 4 回の呼び出しに調整されます。 Android 10 からは、開発者の設定メニューから調整を無効にすることができます。 詳細については、[Android のドキュメント][5]を参照してください。 | なし - パブリック API がありません |
| **BLE ビーコン** | [Eddystone][1] と [iBeacon][2] に制限されています | [Eddystone][1] と [iBeacon][2] に制限されています | [Eddystone][1] と [iBeacon][2] に制限されています |

## <a name="next-steps"></a>次のステップ

アプリで粗い再局在化を使用します。

> [!div class="nextstepaction"]
> [Unity](../how-tos/set-up-coarse-reloc-unity.md)

> [!div class="nextstepaction"]
> [Objective-C](../how-tos/set-up-coarse-reloc-objc.md)

> [!div class="nextstepaction"]
> [Swift](../how-tos/set-up-coarse-reloc-swift.md)

> [!div class="nextstepaction"]
> [Java](../how-tos/set-up-coarse-reloc-java.md)

> [!div class="nextstepaction"]
> [C++/NDK](../how-tos/set-up-coarse-reloc-cpp-ndk.md)

> [!div class="nextstepaction"]
> [C++/WinRT](../how-tos/set-up-coarse-reloc-cpp-winrt.md)

<!-- Reference links in article -->
[1]: https://developers.google.com/beacons/eddystone
[2]: https://developer.apple.com/ibeacon/
[3]: https://developer.android.com/reference/android/location/LocationManager
[4]: https://developer.apple.com/documentation/corelocation/cllocationmanager?language=objc
[5]: https://developer.android.com/guide/topics/connectivity/wifi-scan
[6]: https://www.cambridge.org/core/journals/journal-of-navigation/article/positional-accuracy-of-assisted-gps-data-from-highsensitivity-gpsenabled-mobile-phones/E1EE20CD1A301C537BEE8EC66766B0A9
