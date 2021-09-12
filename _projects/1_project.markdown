---
layout: page
title: Quadrotor with a cable suspended load
description: transverse feedback linearization
img: /assets/img/quad-load.png
importance: 2
category: research
---


Aerial robots and drone-based payload delivery isin the spotlight and is considered revolutionary for the logisticsand transportation sectors.In most use cases, the payload isrequired to precisely follow a given path for safe and secureoperation in a cluttered environment.This work addresses theproblem of designing a path following controller for a point-mass payload tethered to a quadrotor. Specifically, we design asmooth dynamic feedback controller that forces the suspendedload to converge and follow a large class of both closed andnon-closed embedded curves. Using a local representation ofthe dynamical system consisting of a quadrotor and the cable-suspended payload, we show that the system has a well-definedvector relative degree. We treat the given curve as a smoothmanifold and then use set stabilization to find the maximalcontrol invariant manifold. The resulting controller guaranteesthat once the system reaches the path, it stays on the pathindefinitely. We demonstrate the performance of the proposedcontroller through extensive simulations with practical sensornoise and parametric uncertainties.Moreover, successful real-world experimental validation of the proposed controller isdemonstrated on a Quanser QDrone UAV platform with a cable-suspended payload.


<iframe width="560" height="315" src="https://www.youtube.com/embed/Znwy7Jd7dtg" title="YouTube video player" frameborder="0" allow="accelerometer; autoplay; clipboard-write; encrypted-media; gyroscope; picture-in-picture" allowfullscreen></iframe>


