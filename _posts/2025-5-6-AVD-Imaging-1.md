---
layout: post
title: Azure Virtual Desktop Image Creation - Pt 1
date: 2025-05-06
published: false
---

Azure Virtual Desktop(AVD) is a modern Virtual Desktop Infrastructure(VDI) solution offering both Remote Desktop and Remote Apps. It brings together Microsoft 365 and Azure to provide users with multi-session and session-session Windows 11 and Windows 10 experience.

AVD utilizes Azure Virtual Machines(VMs) platform to deliver the session hosts. The necessary brokering and gateway components are managed by Microsoft as part of the offering.

You can create the session hosts using the Azure marketplace images or customize the marketplace images to create your own golden images.

## Golden Image Creation

Golden image creation is not new to VDI, and AVD is no different. The main differentiating factor is the presence of Windows multi-session client OS which can only be run in Azure Virtual Desktop.