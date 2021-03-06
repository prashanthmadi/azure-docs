---
title: Azure Service Fabric Application Level Monitoring | Microsoft Docs
description: Learn about application and service level events and logs used to monitor and diagnose Azure Service Fabric clusters.
services: service-fabric
documentationcenter: .net
author: dkkapur
manager: timlt
editor: ''

ms.assetid:
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 03/20/2018
ms.author: dekapur

---

# Application and service level logging

Instrumenting the code is the basis for most other aspects of monitoring your services. Instrumentation is the only way you can know that something is wrong, and to diagnose what needs to be fixed. Although technically it's possible to connect a debugger to a production service, it's not a common practice. So, having detailed instrumentation data is important.

Some products automatically instrument your code. Although these solutions can work well, manual instrumentation is almost always required. In the end, you must have enough information to forensically debug the application. This document describes different approaches to instrumenting your code, and when to choose one approach over another.

## EventSource

When you create a Service Fabric solution from a template in Visual Studio, an **EventSource**-derived class (**ServiceEventSource** or **ActorEventSource**) is generated. A template is created, in which you can add events for your application or service. The **EventSource** name **must** be unique, and should be renamed from the default template string MyCompany-&lt;solution&gt;-&lt;project&gt;. Having multiple **EventSource** definitions that use the same name causes an issue at run time. Each defined event must have a unique identifier. If an identifier is not unique, a runtime failure occurs. Some organizations preassign ranges of values for identifiers to avoid conflicts between separate development teams. For more information, see [Vance's blog](https://blogs.msdn.microsoft.com/vancem/2012/07/09/introduction-tutorial-logging-etw-events-in-c-system-diagnostics-tracing-eventsource/) or the [MSDN documentation](https://msdn.microsoft.com/library/dn774985(v=pandp.20).aspx).

## ASP.NET Core logging

It's important to carefully plan how you will instrument your code. The right instrumentation plan can help you avoid potentially destabilizing your code base, and then needing to reinstrument the code. To reduce risk, you can choose an instrumentation library like [Microsoft.Extensions.Logging](https://www.nuget.org/packages/Microsoft.Extensions.Logging/), which is part of Microsoft ASP.NET Core. ASP.NET Core has an [ILogger](https://docs.microsoft.com/aspnet/core/api/microsoft.extensions.logging.ilogger) interface that you can use with the provider of your choice, while minimizing the effect on existing code. You can use the code in ASP.NET Core on Windows and Linux, and in the full .NET Framework, so your instrumentation code is standardized.

## Choosing a logging provider

If your application relies on high performance, **EventSource** is usually a good approach. **EventSource** *generally* uses fewer resources and performs better than ASP.NET Core logging or any of the available third-party solutions.  This isn't an issue for many services, but if your service is performance-oriented, using **EventSource** might be a better choice. However, to get these benefits of structured logging, **EventSource** requires a larger investment from your engineering team. If possible, do a quick prototype of a few logging options, and then choose the one that best meets your needs.

## Next steps

Once you have chosen your logging provider to instrument your applications and services, your logs and events need to be aggregated before they can be sent to any analysis platform. Read about [EventFlow](service-fabric-diagnostics-event-aggregation-eventflow.md) and [WAD](service-fabric-diagnostics-event-aggregation-wad.md) to better understand some of the recommended options.
