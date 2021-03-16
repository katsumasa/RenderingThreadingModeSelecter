# RenderingThreadingModeSelecter

## 概要

[RenderingThreadingMode](https://docs.unity3d.com/ScriptReference/Rendering.RenderingThreadingMode.html)は [PlayerSettings.MTRendering](https://docs.unity3d.com/ScriptReference/PlayerSettings.MTRendering.html)、[PlayerSettings.graphicsJobs](https://docs.unity3d.com/ScriptReference/PlayerSettings-graphicsJobs.html)、PlayerSettings.graphicsJobsModeの組み合わせで設定する必要がありますが、その設定をメニューから簡単に指定することが出来るEditor拡張です。
</br></br></br>

## 使い方

</br>

![2021-03-15 211806](https://user-images.githubusercontent.com/29646672/111251681-0e953880-8653-11eb-9e65-67ac1a62f5f9.png)



Editor>RenderingThreadingModeから[Direct](https://docs.unity3d.com/ScriptReference/Rendering.RenderingThreadingMode.Direct.html)、[MultiThreaded](https://docs.unity3d.com/ScriptReference/Rendering.RenderingThreadingMode.MultiThreaded.html)、[LegacyJobified](https://docs.unity3d.com/ScriptReference/Rendering.RenderingThreadingMode.LegacyJobified.html)、[NativeGraphiceJobs](https://docs.unity3d.com/ScriptReference/Rendering.RenderingThreadingMode.NativeGraphicsJobs.html)、[NativeGraphiceJobsWithoutRenderThread](https://docs.unity3d.com/ScriptReference/Rendering.RenderingThreadingMode.NativeGraphicsJobsWithoutRenderThread.html)の何れかを選択するだけです。

※RenderingThreadingModeはビルド時にのみ設定可能です。
Runtime実行時には設定を変更する事は出来ません。
Runtime実行時には[SystemInfo.renderingThreadingModeRenderingThreadingMode](https://docs.unity3d.com/ScriptReference/SystemInfo-renderingThreadingMode.html)を参照することでどのモードで実行されているのかを確認することが可能です。
また、Graphic APIがOpenELGS2/3では使用することは出来ません。
Metal及びVulkanを使用するように設定を変更する必要があります。
</br></br></br>

## RenderingThreadingModeについて

RenderingThreadingModeとは何かについて説明します。
正しい内容の理解に関しては、公式ドキュメントを確認して下さい。

### [Direct](https://docs.unity3d.com/ScriptReference/Rendering.RenderingThreadingMode.Direct.html)

![Direct](https://connect-prd-cdn.unity.com/20190130/566f943b-7cbe-4d8c-9f7f-3429af605392_singlethreaded_rendering_0.png.2000x0x1.webp)

MainThreadから直接Graphic APIを実行します。
Single Threadで動作する為、もっとも安定した動作となりますが、Mainthreadの処理の長さがフレームレートに直結します。
このモードを実行してCPUネックによる処理落ちが発生している場合、他のモードを検討する必要があります。
いわゆる、マルチスレッドレンダリング、GraphicJobsどちらでもない状態です。
デバイスのコア数が1の場合、設定に関わらずこのモードで動作します。
</br></br></br>

### [MultiThreaded](https://docs.unity3d.com/ScriptReference/Rendering.RenderingThreadingMode.MultiThreaded.html)

![MultiThreaded](https://connect-prd-cdn.unity.com/20190130/2dc1a536-4f58-4065-bc91-3abbd049f881_multithreaded_rendering_0.png.2000x0x1.webp)

MainThreadでrenderingCommands(RCMD)を作成し、他のThreadから参照可能な共有バッファに書き込みます。
Renderthreadは共有バッファの内容からGraphic API (GCMD)を実行します。
いわゆる、マルチスレッドレンダリングです。
iPhone7以前のようなコア数が比較的少ない(2コア前後)端末で、良い結果を出す傾向にあります。
</br></br></br>

### [LegacyJobified](https://docs.unity3d.com/ScriptReference/Rendering.RenderingThreadingMode.LegacyJobified.html)

![LegacyJobified](https://connect-prd-cdn.unity.com/20190130/e3f8bcbd-3fc8-47fd-8db1-ddb236ebef7b_jobified_rendering_0.png.2000x0x1.webp)

複数のWorkerthreadがM共有バッファに作成に共有バッファ上のRCMDを元にintermediate graphics commands (IGCMD)を作成します。
その後、Renderingthreadが読み取り、内部ClassであるGfxDeviceに転送し、GCMDに変換します。
このモードはUnity5系時代のGraphicJobシステムの動作となります。
このモードはAndroidプラットフォームでは動作しません。(NativeGraphicsJobsへフォールバックします)
</br></br></br>

### [NativeGraphiceJobs](https://docs.unity3d.com/ScriptReference/Rendering.RenderingThreadingMode.NativeGraphicsJobs.html)

![NativeGraphiceJobs](https://connect-prd-cdn.unity.com/20190130/b07d0d7a-c328-4ecc-b3c6-5437e4f769e8_graphics_jobs.png.2000x0x1.webp)

MultiThreadedモードのRenderingthreadの処理をWorkerthreadへディスパッチします。
MultiThreadedモードでRenderingthreadの処理不可が高い場合、こちらのモードを使用すると不可が改善する可能性があります。
</br></br></br>

[NativeGraphiceJobsWithoutRenderThread](https://docs.unity3d.com/ScriptReference/Rendering.RenderingThreadingMode.NativeGraphicsJobsWithoutRenderThread.html)

![NativeGraphiceJobsWithoutRenderThread](https://cdn-ak.f.st-hatena.com/images/fotolife/k/kimukats/20200601/20200601164055.png)

Directモードで行っていたMainthreadの処理をWorkerthereadへディスパッチします。

---

以上です。
