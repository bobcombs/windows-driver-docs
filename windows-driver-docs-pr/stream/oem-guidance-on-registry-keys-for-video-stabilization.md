---
title: Video stabilization registry settings
author: windows-driver-content
description: The OEM-set MaxPixelsPerSecond registry key pertains only to applying video stabilization to a video at capture-time.
MSHAttr:
- 'PreferredSiteName:MSDN'
- 'PreferredLib:/library/windows/hardware'
ms.assetid: F0F7A705-0F39-4A62-A110-A2E47DFB7B42
---

# Video stabilization registry settings


The OEM-set **MaxPixelsPerSecond** registry key pertains only to applying video stabilization to a video at capture-time. It allows OEMs to configure video stabilization settings on a device. The configuration takes into account the device’s recording resolution, along with its hardware and software capabilities.

## Overview


Video stabilization registry key values are used to specify the maximum capabilities of video stabilization on a device, under optimal circumstances. All apps can read the registry key and avoid attempting unreasonable usage of video stabilization.

The value entered in the **MaxPixelsPerSecond** registry key sets the limit beyond which the MFT will not try to turn on video stabilization, even if an app enables it. The registry key needs to indicate the greatest resolution and frame rate at which a device can run video stabilization. If the **MaxPixelsPerSecond** registry key is not set, the video stabilization MFT will use a fallback value. Finally, if that fails as well, video stabilization will use its internal logic to switch off in order to prevent a sub-optimal user experience.

## Video stabilization requirements


A device is considered capable of running video stabilization when all of the following can happen:

-   Video stabilization is turned on and is not in pass-through mode

-   Recording is turned on

-   Preview is active

-   No noise or dropped frames are seen in the preview

-   No noise or dropped frames are seen in the recorded video

## Set the video stabilization registry key


**Registry key format:**

-   OEMs should set a QWORD registry key that defines the cutoff value for number of pixels per second (PPS) beyond which video stabilization will be forced to run in pass-through mode, even if it is enabled by an app.

-   PPS is defined as follows:

    ```
    PPS = width * height * frame-rate
    ```

    For example, for 1080p resolution at 30 fps, PPS would be defined as 1920 \* 1080 \* 30 = 62208000

**Registry key location:**

-   OEMs should set registry keys for video stabilization in the following location:

    **HKEY\_LOCAL\_MACHINE\\SOFTWARE\\Microsoft\\Windows Media Foundation\\Platform\\VideoStabilization\\MaxPixelsPerSecond**

-   On 64-bit machines, OEMs should also set the same key on the Wow6432Node path:

    **HKEY\_LOCAL\_MACHINE\\SOFTWARE\\Wow6432Node\\Microsoft\\Windows Media Foundation\\Platform\\VideoStabilization\\MaxPixelsPerSecond**

When set, the video stabilization registry key will be visible to the video stabilization MFT and first and third party apps.

If the **MaxPixelsPerSecond** registry key is set, the video stabilization MFT will never try to stabilize frame rates or resolutions above the limit. Instead, it will go into pass-through mode even if the app requests video stabilization. The video stabilization MFT has a mechanism to recommend frame rate and resolution to the app for a given device. Apps can choose the recommendation to avoid such a pass-through on those devices that have the registry key populated.

If the **MaxPixelsPerSecond** registry key is not set, the video stabilization MFT will attempt to stabilize up to the default value but no higher.

The default value is 62208000 PPS, which is 1920 pixels x 1080 pixels x 30 fps. When video stabilization attempts to stabilize but cannot maintain real time stabilization of the video frames, the internal logic will switch video stabilization to pass-through mode (turning off video stabilization) without dropping any frames.

If video stabilization switched off in the previous session, the MFT will attempt to start video stabilization in regular mode for every new session, before deciding to switch to pass-through mode. This is because it can not rely on the previous mode to make future decisions, since the device may have been under stress when it was last operated.

## Video stabilization test requirements


OEMs need to verify end-to-end capabilities of their devices with video stabilization working. They need to verify an acceptable experience at the given largest PPS resolution.

OEMs must verify the following:

-   The video stabilization internal logic is disabled at the registry key location provided by Microsoft. Disabling the internal logic guarantees that video stabilization will not go into pass-through mode during testing if it encounters a stressful situation.

-   Video stabilization can run alone, without background tasks or other features

-   Smooth preview rendering with video stabilization enabled and the internal logic disabled

-   Smooth video recording with video stabilization enabled and the internal logic disabled

-   Desired PPS count achieved in stabilized recording

-   No overheating

**Note**  Retail systems should not have the registry key to disable the video stabilization internal logic described in this section. However, retail systems should have the **MaxPixelsPerSecond** registry key with a value determined through this test process.

 

**Note**  The **MaxPixelsPerSecond** registry key functions only when attribute [MF\_LOW\_LATENCY](https://msdn.microsoft.com/library/windows/desktop/hh162832) is set on the effect. When the provided video stabilization effect is added to the MediaCapture pipeline, the attribute is automatically set. However, if the video stabilization effect is inserted into a custom pipeline or a pipeline that doesn’t set the **MF\_LOW\_LATENCY** attribute, the registry key has no effect.

 

 

 


--------------------
[Send comments about this topic to Microsoft](mailto:wsddocfb@microsoft.com?subject=Documentation%20feedback%20%5Bstream\stream%5D:%20Video%20stabilization%20registry%20settings%20%20RELEASE:%20%288/23/2016%29&body=%0A%0APRIVACY%20STATEMENT%0A%0AWe%20use%20your%20feedback%20to%20improve%20the%20documentation.%20We%20don't%20use%20your%20email%20address%20for%20any%20other%20purpose,%20and%20we'll%20remove%20your%20email%20address%20from%20our%20system%20after%20the%20issue%20that%20you're%20reporting%20is%20fixed.%20While%20we're%20working%20to%20fix%20this%20issue,%20we%20might%20send%20you%20an%20email%20message%20to%20ask%20for%20more%20info.%20Later,%20we%20might%20also%20send%20you%20an%20email%20message%20to%20let%20you%20know%20that%20we've%20addressed%20your%20feedback.%0A%0AFor%20more%20info%20about%20Microsoft's%20privacy%20policy,%20see%20http://privacy.microsoft.com/default.aspx. "Send comments about this topic to Microsoft")


