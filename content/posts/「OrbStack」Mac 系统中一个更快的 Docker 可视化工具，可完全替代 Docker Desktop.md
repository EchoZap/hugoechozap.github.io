---
title: "「OrbStack」Mac 系统中一个更快的 Docker 可视化工具，可完全替代 Docker Desktop"
date: 2024-10-22T13:29:13+08:00
categories: ['Docs']
author: "Ronan"
---
我之前用的是 Docker Desktop，每次准备启动容器的时候，我都会把能关的应用都关闭，省的电脑卡， Docker Desktop 确实有点儿耗性能。

如果你也觉得 Docker Desktop 太慢了，或者之前没有可视化工具，不妨试试 OrbStack。

![img](https://imgs.ronan.us.kg/orbstack.png)

OrbStack 是一款针对 macOS 操作系统的定制的，它的目的就是提供一个高效、轻量级的 Docker 和虚拟机（VM）管理平台。

官网地址： https://orbstack.dev/

它不仅仅支持 Docker、 Kubernetes，而且支持Linux子系统，类似于 Windows 系统下的 WSL。

它的功能如下：

- 轻量级虚拟化：OrbStack 使用 macOS 原生的虚拟化框架，确保了高效的资源利用率和优秀的性能表现。
- 简易管理：提供了对Docker容器和虚拟机的统一管理界面，使用户能够方便地启动、停止、配置和监视容器与虚拟机。
- 无缝集成：与 Docker 和 Kubernetes 无缝集成，支持容器编排和微服务架构的轻松管理。
- 强大工具支持：支持 SSH、文件同步、端口转发等功能，方便开发者进行远程调试和开发。
- 用户友好：提供图形化用户界面和命令行工具，开发者可以根据需求选择最适合的操作方式。

OrbStack 和 Docker Desktop 的优势对比
虽然 OrbStack 和 Docker Desktop 都是用于管理Docker环境的工具，但在具体特性和用户体验上有一些显著的区别和优势：

1. 资源利用率与性能

- OrbStack：利用 macOS 原生虚拟化框架，因此更轻量级，启动更快，占用的系统资源更少。
- Docker Desktop：依赖于第三方虚拟化解决方案如 HyperKit 或 VirtualBox，在资源消耗上可能稍高，尤其是在大量使用 Docker 容器的场景下。

2. 系统整合

- OrbStack：深度集成于 macOS，具有更高的系统兼容性和稳定性。对于使用 macOS 系统的开发者，OrbStack 能提供更流畅和无缝的体验。
- Docker Desktop：尽管支持多种操作系统，但在各个平台上的整合程度和性能表现可能会有所不同。

3. 用户界面和易用性

- OrbStack：提供简洁直观的用户界面，同时保留强大的命令行工具。适合那些希望快速上手，并且需要高效管理容器和虚拟机的用户。
- Docker Desktop：虽然功能全面，但其界面对某些用户尤其是新手用户可能显得有些复杂，学习和配置的时间成本稍高。

4. 特殊功能

- OrbStack：提供了特有的功能如文件同步、端口转发和高级网络配置等，使得远程开发和调试更加便捷。
- Docker Desktop：功能依赖于 Docker Engine，具有全面的 Docker 支持，但某些高级配置功能可能需要额外的学习成本。

如果你正在使用 Docker Desktop ，可以在 OrbStack 中一键将原本的 Docker 配置和资源导入到 OrbStack，几乎零成本切换。
