# RenderingThreadingModeSelecter

## Summary

[RenderingThreadingMode](https://docs.unity3d.com/ScriptReference/Rendering.RenderingThreadingMode.html)needs to be set by a combination of: [PlayerSettings.MTRendering](https://docs.unity3d.com/ScriptReference/PlayerSettings.MTRendering.html)、[PlayerSettings.graphicsJobs](https://docs.unity3d.com/ScriptReference/PlayerSettings-graphicsJobs.html)、and PlayerSettings.graphicsJobsMode. This Editor extension will allow you to easily specify the setting from the menu.

</br></br></br>

## How to use

</br>

![2021-03-15 211806](https://user-images.githubusercontent.com/29646672/111251681-0e953880-8653-11eb-9e65-67ac1a62f5f9.png)

Select [Direct](https://docs.unity3d.com/ScriptReference/Rendering.RenderingThreadingMode.Direct.html)、[MultiThreaded](https://docs.unity3d.com/ScriptReference/Rendering.RenderingThreadingMode.MultiThreaded.html)、[LegacyJobified](https://docs.unity3d.com/ScriptReference/Rendering.RenderingThreadingMode.LegacyJobified.html)、[NativeGraphiceJobs](https://docs.unity3d.com/ScriptReference/Rendering.RenderingThreadingMode.NativeGraphicsJobs.html)、[NativeGraphiceJobsWithoutRenderThread](https://docs.unity3d.com/ScriptReference/Rendering.RenderingThreadingMode.NativeGraphicsJobsWithoutRenderThread.html) from Editor> RenderingThreadingMode.

※RenderingThreadingMode can only be set at build time.
The settings cannot be changed when the Runtime is executed.
When Runtime is executed, you can check which mode it's running by referencing [SystemInfo.renderingThreadingModeRenderingThreadingMode](https://docs.unity3d.com/ScriptReference/SystemInfo-renderingThreadingMode.html)

The Graphic API cannot be used with OpenELGS2/3.
You need to change the settings to use Metal and Vulkan.
</br></br></br>

## About RenderingThreadingMode

Explaining what RenderingThreadingMode is.
Please check the official document for better understanding.

### [Direct](https://docs.unity3d.com/ScriptReference/Rendering.RenderingThreadingMode.Direct.html)

![Direct](https://connect-prd-cdn.unity.com/20190130/566f943b-7cbe-4d8c-9f7f-3429af605392_singlethreaded_rendering_0.png.2000x0x1.webp)

Going run the Graphic API directly from the Main Thread.
It has stability since it operates with Single Thread, but the length of processing the main thread is directly linked to the frame rate.
If you run this mode and experiencing processing failures due to a CPU bottleneck, you should consider trying other modes.
It's a state where neither multi-threaded rendering nor Graphic Jobs.
If the device has 1 core, it will operate in this mode regardless of the setting.
</br></br></br>

### [MultiThreaded](https://docs.unity3d.com/ScriptReference/Rendering.RenderingThreadingMode.MultiThreaded.html)

![MultiThreaded](https://connect-prd-cdn.unity.com/20190130/2dc1a536-4f58-4065-bc91-3abbd049f881_multithreaded_rendering_0.png.2000x0x1.webp)

Create MainThreadでrenderingCommands(RCMD) and write to shared buffer that can be referecned by other threads.
Renderthread executes the Graphic APO (GCMD) from the contents of shared buffer.
It's so-called the multithreaded rendering.
It tends to give good results on terminals with relatively small numbers of cores (around 2 cores or so) such as iPhoe 7 and earlier models.
</br></br></br>

### [LegacyJobified](https://docs.unity3d.com/ScriptReference/Rendering.RenderingThreadingMode.LegacyJobified.html)

![LegacyJobified](https://connect-prd-cdn.unity.com/20190130/e3f8bcbd-3fc8-47fd-8db1-ddb236ebef7b_jobified_rendering_0.png.2000x0x1.webp)

Multiple Workerthreads create intermediate graphics commands (IGCMD) based on RCDM on the shared buffer to create in M shared buffer. 
Then the Renderingthread reads and transfers it to the internal Class GfxDevice and converts it to GCMD.
This mode is the operation of the GraphicJob system during Unity 5 era.
This mode does not work on Android platforms(Fall back to NativeGraphicsJobs).
</br></br></br>

### [NativeGraphiceJobs](https://docs.unity3d.com/ScriptReference/Rendering.RenderingThreadingMode.NativeGraphicsJobs.html)

![NativeGraphiceJobs](https://connect-prd-cdn.unity.com/20190130/b07d0d7a-c328-4ecc-b3c6-5437e4f769e8_graphics_jobs.png.2000x0x1.webp)

Dispatches the processing of the Renderingthread in MultiThreaded mode to the Workerthread.
If processing load of Renderingthread is high in MultiThreaded mode, this tool could make an improvement.
</br></br></br>

[NativeGraphiceJobsWithoutRenderThread](https://docs.unity3d.com/ScriptReference/Rendering.RenderingThreadingMode.NativeGraphicsJobsWithoutRenderThread.html)

![NativeGraphiceJobsWithoutRenderThread](https://cdn-ak.f.st-hatena.com/images/fotolife/k/kimukats/20200601/20200601164055.png)

Dispatch the Mainthread processing that was done in Direct mode to Workerthread.

---

That'll be all.
