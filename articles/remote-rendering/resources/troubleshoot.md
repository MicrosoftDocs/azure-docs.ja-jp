---
title: トラブルシューティング
description: Azure Remote Rendering のトラブルシューティング情報
author: florianborn71
ms.author: flborn
ms.date: 02/25/2020
ms.topic: troubleshooting
ms.openlocfilehash: 4990f0d0a10709f2c1c5a17806020cd685f999fc
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "99593335"
---
# <a name="troubleshoot"></a>トラブルシューティング

このページでは、Azure Remote Rendering に影響を及ぼす一般的な問題とその解決方法の一覧を示します。

## <a name="cant-link-storage-account-to-arr-account"></a>ストレージアカウントを ARR アカウントにリンクできない

[ストレージ アカウントのリンク](../how-tos/create-an-account.md#link-storage-accounts)中に、Remote Rendering アカウントが一覧に表示されない場合があります。 この問題を解決するには、Azure portal の ARR アカウントに移動し、左側の **[設定]** グループの下にある **[ID]** を選択します。 **[状態]** が **[オン]** に設定されていることを確認してください。
![Unity のフレーム デバッガー](./media/troubleshoot-portal-identity.png)

## <a name="client-cant-connect-to-server"></a>クライアントがサーバーに接続できない

(デバイス上やルーター内などの) ファイアウォールが[システム要件](../overview/system-requirements.md#network-firewall)で指定されているポートをブロックしていないことを確認します。

## <a name="error-disconnected-videoformatnotavailable"></a>エラー '`Disconnected: VideoFormatNotAvailable`'

GPU でハードウェアによる動画のデコードがサポートされていることを確認します。 「[開発用 PC](../overview/system-requirements.md#development-pc)」をご覧ください。

GPU を 2 基搭載したノート パソコンで作業している場合、既定で実行されている方の GPU がハードウェアによる動画のデコード機能を提供していない可能性があります。 その場合は、アプリで強制的にもう一方の GPU を使用するようにしてください。 多くの場合、この操作は GPU ドライバーの設定で実行できます。

## <a name="retrieve-sessionconversion-status-fails"></a>セッション/変換状態の取得の失敗

REST API コマンドを頻繁に送信しすぎるとサーバーでスロットルが発生し、最終的にエラーが返されます。 スロットリングが発生した場合の http 状態コードは 429 ("要求が多すぎます") になります。 経験則として、**次の呼び出しとの間に 5 秒から 10 秒** の間隔が必要です。

この制限は、直接呼び出した場合に REST API の呼び出しに影響するだけでなく、`Session.GetPropertiesAsync`、`Session.RenewAsync`、または `Frontend.GetAssetConversionStatusAsync` などの C#/C++ の対応するものにも影響します。

サーバー側のスロットリングが発生する場合は、呼び出しの頻度を減らすようにコードを変更してください。 サーバーによって 1 分ごとにスロットリングの状態がリセットされるため、1 分後に安全にコードを再実行できます。

## <a name="h265-codec-not-available"></a>H265 コーデックが使用できない

`codec not available` というエラーでサーバーが接続を拒否する可能性のある理由は 2 つあります。

**H265 コーデックがインストールされていない:**

最初に、システム要件の「[ソフトウェア](../overview/system-requirements.md#software)」セクションに記載されている **HEVC Video Extensions** を必ずインストールしてください。

それでも問題が発生する場合は、使用しているグラフィックス カードで H265 がサポートされていて、最新のグラフィックス ドライバーがインストールされていることを確認してください。 ベンダー固有の情報については、システム必要条件の「[開発用 PC](../overview/system-requirements.md#development-pc)」セクションをご覧ください。

**H265 コーデックがインストールされているが、使用できない:**

この問題の原因は、DLL のセキュリティ設定が正しくないことです。 H265 でエンコードされた動画を視聴しようとした場合、この問題は発生しません。 コーデックを再インストールしても問題は解決されません。 代わりに、次の手順を実行してください。

1. **管理者権限を使用して PowerShell** を開き、次のコマンドを実行します

    ```PowerShell
    Get-AppxPackage -Name Microsoft.HEVCVideoExtension
    ```
  
    このコマンドによって、次のようなコーデックの `InstallLocation` が出力されます。
  
    ```cmd
    InstallLocation   : C:\Program Files\WindowsApps\Microsoft.HEVCVideoExtension_1.0.23254.0_x64__5wasdgertewe
    ```

1. Windows エクスプローラーでそのフォルダーを開きます
1. **x86** と **x64** のサブフォルダーがあります。 いずれかのフォルダーを右クリックして、 **[プロパティ]** を選択します
    1. **[セキュリティ]** タブを選択し、 **[詳細設定]** ボタンをクリックします
    1. **[所有者]** で **[変更]** をクリックします
    1. テキスト フィールドに「**Administrators**」と入力します
    1. **[名前の確認]** 、 **[OK]** の順にクリックします
1. もう一方のフォルダーについて、上記の手順を繰り返します
1. 同様に、両方のフォルダー内の DLL ファイルごとに上記の手順を繰り返します。 全部で 4 つの DLL があります。

設定が正しいことを検証するには、4 つの DLL ごとに次の手順を実行します。

1. **[プロパティ] > [セキュリティ] > [編集]** の順に選択します
1. すべての **グループまたはユーザー** の一覧を 1 行ずつ確認し、それぞれに **[読み取りと実行]** 権限が設定されていることを確認します ( **[許可]** 列のチェックマークがオンになっている必要があります)

## <a name="low-video-quality"></a>動画の画質が低下している

動画の画質は、ネットワーク品質または H265 動画コーデックの欠落によって低下する可能性があります。

* [ネットワークに関する問題を特定する](#unstable-holograms)ための手順をご覧ください。
* 最新のグラフィックス ドライバーのインストールについては、「[システム要件](../overview/system-requirements.md#development-pc)」をご覧ください。

## <a name="video-recorded-with-mrc-does-not-reflect-the-quality-of-the-live-experience"></a>MRC で記録された動画にライブ エクスペリエンスの品質が反映されていない

動画は [Mixed Reality Capture (MRC)](/windows/mixed-reality/mixed-reality-capture-for-developers) を介して HoloLens に記録できます。 ただし、結果として得られる動画の画質は、次の 2 つの理由により、ライブ エクスペリエンスよりも低くなります。
* 動画のフレームレートが 60 Hz ではなく 30 Hz に制限されている。
* 動画の画像に [Late Stage Reprojection](../overview/features/late-stage-reprojection.md) の処理ステップが実行されないため、動画が途切れているように見える。

どちらも記録技法に固有の制限事項です。

## <a name="black-screen-after-successful-model-loading"></a>モデルの読み込みが成功した後にブラック スクリーンが発生する

レンダリング ランタイムに接続し、モデルが正常に読み込まれましたが、その後、ブラック スクリーンしか表示されない場合は、いくつかの明確な原因が考えられます。

詳細な分析を行う前に、次の点をテストすることをお勧めします。

* H265 コーデックがインストールされているか確認してください。 H264 コーデックへのフォールバックは用意されていますが、このフォールバックが正しく機能しなかったケースも生じています。 最新のグラフィックス ドライバーのインストールについては、「[システム要件](../overview/system-requirements.md#development-pc)」をご覧ください。
* Unity プロジェクトを使用している場合は、Unity を閉じて、プロジェクト ディレクトリ内の一時 *ライブラリ* と *obj* フォルダーを削除してから、プロジェクトを再度読み込むかビルドしてください。 場合によっては、明確な理由もなく、キャッシュ データによってサンプルが正しく機能しなくなることがあります。

これらの 2 つの手順で問題が解決しなかった場合は、ビデオ フレームがクライアントで受信されているかどうかを確認する必要があります。 これは、「[サーバー側のパフォーマンス クエリ」](../overview/features/performance-queries.md)の章で説明しているように、プログラムでクエリできます。 `FrameStatistics struct` には、受信したビデオ フレームの数を示すメンバーがあります。 この数が 0 より大きく、時間の経過と共に増加している場合、クライアントは実際のビデオ フレームをサーバーから受信しています。 したがって、これはクライアント側の問題です。

### <a name="common-client-side-issues"></a>クライアント側の一般的な問題

**モデルで選択されている VM の制限、具体的には、ポリゴンの最大数を超えている:**

特定の[サーバー サイズの制限](../reference/limits.md#overall-number-of-polygons)を確認してください。

**モデルがカメラの視錐台の外側にある:**

多くの場合、そのモデルは正しく表示されますが、カメラの視錐台の外側にあります。 一般的な理由の 1 つは、そのモデルが中心から外れた遠くのピボットを使用してエクスポートされたために、カメラの遠クリップ面で切り取られてしまうことです。 これは、モデルの境界ボックスに対してプログラムでクエリを実行し、Unity でそのボックスを線のボックスとして視覚化したり、その値をデバッグ ログに出力したりするのに役立ちます。

さらに、変換処理では、変換されたモデルと共に[出力 json ファイル](../how-tos/conversion/get-information.md)が生成されます。 モデル ポジショニングの問題をデバッグするには、[outputStatistics セクション](../how-tos/conversion/get-information.md#the-outputstatistics-section)の `boundingBox` エントリを参照してください。

```JSON
{
    ...
    "outputStatistics": {
        ...
        "boundingBox": {
            "min": [
                -43.52,
                -61.775,
                -79.6416
            ],
            "max": [
                43.52,
                61.775,
                79.6416
            ]
        }
    }
}
```

境界ボックスは、3D 空間での `min` と `max` の位置として、メートル単位で示されます。 したがって、座標が 1000.0 の場合は、原点から 1 キロメートル離れていることを意味します。

この境界ボックスには、ジオメトリの非表示につながる 2 つの問題があります。
* **ボックスが中心から遠く離れている場合がある**。このため、遠方平面クリッピングによりオブジェクトが完全に切り取られる。 この場合の `boundingBox` 値は、`min = [-2000, -5,-5], max = [-1990, 5,5]` のようになります。ここでは、例として、x 軸上の大きなオフセットを使用します。 この種類の問題を解決するには、[モデル変換構成](../how-tos/conversion/configure-model-conversion.md)で `recenterToOrigin` オプションを有効にします。
* **ボックスは中央に配置できるが、スケールが大きすぎる**。 つまり、カメラがモデルの中心から開始されても、そのジオメトリはすべての方向で切り取られます。 この場合の典型的な `boundingBox` 値は、`min = [-1000,-1000,-1000], max = [1000,1000,1000]` のようになります。 この種類の問題の原因は、通常、単位スケールの不一致です。 補正するには、[変換時のスケーリング値](../how-tos/conversion/configure-model-conversion.md#geometry-parameters)を指定するか、ソース モデルを正しい単位でマークアップします。 スケーリングは、実行時にモデルを読み込むときにルート ノードに適用することもできます。

**Unity のレンダリング パイプラインにレンダリング フックが含まれていない:**

Azure Remote Rendering では、動画を使用してフレーム合成を行ったり、再投影を行ったりするために、Unity のレンダリング パイプラインにフックします。 これらのフックが存在することを確認するには、メニュー *:::no-loc text="Window > Analysis > Frame debugger":::* を開きます。 これを有効にしてから、パイプライン内に `HolographicRemotingCallbackPass` のエントリが 2 つあることを確認します。

![Unity のレンダリング パイプライン](./media/troubleshoot-unity-pipeline.png)

## <a name="checkerboard-pattern-is-rendered-after-model-loading"></a>モデルの読み込み後にチェッカーボード パターンがレンダリングされる

レンダリングされたイメージが次のように表示される場合:![黒と白の正方形のグリッドと [ツール] メニューを示すスクリーンショット。](../reference/media/checkerboard.png)
レンダラーが[標準の構成サイズのポリゴンの制限](../reference/vm-sizes.md)に達しています。 解消するには、**Premium**  の構成サイズに切り替えるか、表示されるポリゴンの数を減らします。

## <a name="the-rendered-image-in-unity-is-upside-down"></a>Unity でレンダリングされるイメージが上下反転している

[Unity チュートリアル:リモート モデルを表示する](../tutorials/unity/view-remote-models/view-remote-models.md)に関するページの説明に正確に従っていることを確認します。 イメージが上下反転するのは、Unity がオフスクリーン レンダー ターゲットを作成する必要があることを示しています。 この動作は現在サポートされていないため、HoloLens 2 のパフォーマンスに大きな影響を与えます。

この問題の原因は、MSAA、HDR、または後処理の有効化です。 低品質のプロファイルが選択されていることを確認し、Unity で既定値として設定します。 これを行うには、 *[Edit] > [Project Settings] > [Quality]* に移動します。

## <a name="unity-code-using-the-remote-rendering-api-doesnt-compile"></a>Remote Rendering API を使用する Unity コードがコンパイルされない

### <a name="use-debug-when-compiling-for-unity-editor"></a>Unity エディター用にコンパイルするときにデバッグを使用する

Unity ソリューションの *ビルドの種類* を **[デバッグ]** に切り替えます。 Unity エディターで ARR をテストする場合、`UNITY_EDITOR` の定義は 'デバッグ' ビルドでのみ使用できます。 これは、[デプロイされたアプリケーション](../quickstarts/deploy-to-hololens.md)に使用されるビルドの種類とは関係がないので注意してください。デプロイされたアプリケーションでは、'リリース' ビルドが推奨されます。

### <a name="compile-failures-when-compiling-unity-samples-for-hololens-2"></a>HoloLens 2 用の Unity サンプルをコンパイルするときにコンパイル エラーが発生する

HoloLens 2 の Unity サンプル (quickstart、ShowCaseApp など) をコンパイルしようとすると、偽のエラーが発生することがあります。 Visual Studio には、ファイルが存在するにもかかわらず、ファイルがコピーできないという警告が表示されます。 この問題が発生した場合は、次を行います。
* すべての一時 Unity ファイルをプロジェクトから削除してから、やり直してください。 つまり、Unity を閉じて、プロジェクト ディレクトリ内の一時 *ライブラリ* と *obj* フォルダーを削除してから、プロジェクトを再度読み込むかビルドしてください。
* このコピー手順の問題はファイル名が長い場合に発生することがあるため、比較的パスが短いディスク上のディレクトリにプロジェクトが格納されていることを確認してください。
* それでも問題が解決しない場合は、MS Sense によりコピー手順が妨げられている可能性があります。 例外を設定するには、コマンド ラインから次のレジストリ コマンドを実行します (管理者権限が必要です)。
    ```cmd
    reg.exe ADD "HKLM\SOFTWARE\Policies\Microsoft\Windows Advanced Threat Protection" /v groupIds /t REG_SZ /d "Unity”
    ```
    
### <a name="arm64-builds-for-unity-projects-fail-because-audiopluginmshrtfdll-is-missing"></a>AudioPluginMsHRTF.dll が見つからないために Unity プロジェクトで Arm64 のビルドが失敗する

Arm64 の `AudioPluginMsHRTF.dll` は、バージョン 3.0.1 の *Windows Mixed Reality* パッケージ *(com.unity.xr.windowsmr.metro)* に追加されました。 Unity パッケージ マネージャーを使用して、バージョン 3.0.1 以降がインストールされていることを確認します。 Unity のメニュー バーで、 *[Window] > [Package Manager]* に移動し、 *[Windows Mixed Reality]* パッケージを見つけます。

## <a name="native-c-based-application-does-not-compile"></a>ネイティブ C++ ベースのアプリケーションがコンパイルされない

### <a name="library-not-found-error-for-uwp-application-or-dll"></a>UWP アプリケーションまたは Dll の 'ライブラリが見つかりません' エラー

C++ NuGet パッケージ内には、使用するバイナリ フレーバーを定義する `microsoft.azure.remoterendering.Cpp.targets` ファイルがあります。 `UWP` を識別するため、ファイル内の条件によって `ApplicationType == 'Windows Store'` が確認されます。 このため、この種類がプロジェクトで設定されていることを確認する必要があります。 これは、Visual Studio のプロジェクト ウィザードを使用して UWP アプリケーションまたは Dll を作成する場合に当てはまります。

## <a name="unstable-holograms"></a>ホログラムが不安定である

レンダリングされたオブジェクトが頭部の動きに連動して動くように見える場合は、*Late Stage Reprojection* (LSR) に関する問題が発生している可能性があります。 このような状況への対処方法のガイダンスについては、[Late Stage Reprojection](../overview/features/late-stage-reprojection.md) に関するセクションをご覧ください。

不安定なホログラム (揺れ、ゆがみ、ジッター、ジャンプなどが発生しているホログラム) のもう 1 つの理由として、ネットワーク接続の問題、特にネットワーク帯域幅が不足していたり、待機時間が長すぎたりすることがあります。 ネットワーク接続の品質を示す適切な指標の 1 つは、[パフォーマンス統計](../overview/features/performance-queries.md)値 `ServiceStatistics.VideoFramesReused` です。 再利用されたフレームは、クライアント側で (パケットの損失やネットワーク待機時間のばらつきなどにより) 新しいビデオ フレームが利用できなかったために、前のビデオ フレームを再利用する必要があったという状況を示しています。 `ServiceStatistics.VideoFramesReused` が 0 より大きいことが多い場合は、ネットワークの問題を示しています。

確認すべきもう 1 つの値は `ServiceStatistics.LatencyPoseToReceiveAvg` です。 この値は、常に 100 ミリ秒未満である必要があります。 値がそれよりも大きい場合は、接続されているデータ センターが離れすぎていることを示しています。

考えられる軽減策の一覧については、[ネットワーク接続のガイドライン](../reference/network-requirements.md#guidelines-for-network-connectivity)をご覧ください。

## <a name="z-fighting"></a>Z ファイティング

ARR には [Z ファイティングの軽減機能](../overview/features/z-fighting-mitigation.md)が用意されていますが、シーンでは Z ファイティングが引き続き表示されます。 このガイドは、これらの残りの問題のトラブルシューティングを目的としています。

### <a name="recommended-steps"></a>推奨される手順

次のワークフローを使用して、Z ファイティングを軽減します。

1. ARR の既定の設定を使用してシーンをテストします (Z ファイティングの軽減が有効)

1. [API](../overview/features/z-fighting-mitigation.md) を使用して Z ファイティングの軽減を無効にします 

1. カメラの近くと遠くの面をより近い範囲に変更します

1. 次の項でシーンのトラブルシューティングを行います

### <a name="investigating-remaining-z-fighting"></a>残りの Z ファイティングを調査する

上記の手順を実行しても、残りの Z ファイティングを許容できない場合は、Z ファイティングの根底にある原因を調査する必要があります。 [Z ファイティングの軽減機能](../overview/features/z-fighting-mitigation.md)に関するページに記載されているように、Z ファイティングには主に 2 つの原因があります。深度範囲の遠い側での深さの精度の損失と、同一平面で交差するサーフェスです。 深さの精度の損失は数学的な不測の事態であり、上記の手順 3 に従った場合にのみ軽減できます。 同一平面のサーフェスは、ソース アセットの欠陥を示し、ソース データではより適切に修正されています。

ARR には、サーフェスが Z ファイティングになるかどうかを判断するための[チェッカーボードの強調表示](../overview/features/z-fighting-mitigation.md)という機能があります。 また、Z ファイティングの原因を視覚的に判断することもできます。 次の最初のアニメーションは、距離における深さの精度の損失の例を示しています。2 番目のアニメーションは、ほぼ同一平面のサーフェスの例を示しています。

![アニメーションは、距離における深さの精度の損失の例を示しています。](./media/depth-precision-z-fighting.gif)  ![アニメーションは、ほぼ同一平面のサーフェスの例を示しています。](./media/coplanar-z-fighting.gif)

これらの例を実際の Z ファイティングと比較して原因を特定するか、必要に応じて次のステップバイステップのワークフローに従ってください。

1. カメラを Z ファイティングのサーフェイスの上に配置して、サーフェイスを直接見ます。
1. カメラをゆっくりと後ろに移動させて、サーフェイスから離します。
1. Z ファイティングが常に表示される場合、サーフェスは完全に同一平面にあります。 
1. Z ファイティングがほとんどの位置で表示される場合、サーフェスはほぼ同一平面にあります。
1. Z ファイティングが遠くでのみ表示される場合は、原因は深さの精度の損失です。

同一平面のサーフェスの場合は、次のようなさまざまな原因が考えられます。

* エラーまたは異なるワークフロー アプローチが原因で、エクスポートしたアプリケーションによってオブジェクトが重複している。

    それぞれのアプリケーションとアプリケーション サポートで、これらの問題を確認してください。

* 表面または裏面のカリングを使用するレンダラーでは、サーフェスが重複し、反転して両面が表示される。

    [モデル変換](../how-tos/conversion/model-conversion.md) を使用してインポートすると、モデルの基本の面が決まります。 既定値は両面です。 サーフェスは、両側からの物理的に正しい光源で、薄い壁としてレンダリングされます。 片面は、ソース アセットのフラグによって暗黙的に指定することも、[モデル変換](../how-tos/conversion/model-conversion.md)中に明示的に強制することもできます。 また、必要に応じて[片面モード](../overview/features/single-sided-rendering.md) を "標準" に設定することもできます。

* ソース アセットでオブジェクトが交差している。

     サーフェスの一部が重なり合うように変換されたオブジェクトでは、Z ファイティングも作成されます。 ARR にインポートされたシーンでシーン ツリーの一部を変換した場合も、この問題が発生することがあります。

* サーフェスが、壁の図柄やテキストのように、タッチするために意図的に作成されている。

## <a name="graphics-artifacts-using-multi-pass-stereo-rendering-in-native-c-apps"></a>ネイティブの C++ アプリでのマルチパス ステレオ レンダリングを使用したグラフィックス成果物

場合によっては、[**BlitRemoteFrame**](../concepts/graphics-bindings.md#render-remote-image) を呼び出した後にローカル コンテンツに対してマルチパス ステレオ レンダリング モードを使用する C++ のカスタム ネイティブ アプリ (別個のパスに左と右の目がレンダリングされる) で、ドライバーのバグが発生することがあります。 このバグによって、不明確なラスタライズによるエラーが発生し、ローカル コンテンツの個々の三角形や三角形の一部がランダムに非表示になります。 パフォーマンス上の理由から、**SV_RenderTargetArrayIndex** を使用するなど、より新しいシングルパス ステレオ レンダリング手法でローカル コンテンツをレンダリングすることをお勧めします。

## <a name="next-steps"></a>次のステップ

* [システム要件](../overview/system-requirements.md)
* [ネットワークの要件](../reference/network-requirements.md)