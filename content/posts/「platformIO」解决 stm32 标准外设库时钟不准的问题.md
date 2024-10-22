---
title: "「platformIO」解决 stm32 标准外设库时钟不准的问题"
date: 2024-10-22T13:29:11+08:00
categories: ['Docs']
author: "Ronan"
---
> 以下以stm32f407系列举例

### 1.遭遇问题：

在使用[ platformIO 搭建标准外设库](https://blog.ronan.us.kg/2024/09/08/platformIO-%E5%9F%BA%E4%BA%8E-stm32-%E6%A0%87%E5%87%86%E5%A4%96%E8%AE%BE%E5%BA%93%E7%9A%84%E5%B7%A5%E7%A8%8B%E6%A8%A1%E6%9D%BF/)进行实际开发时遇到了外部时钟不准的问题。

### 2.原因分析：

这是因为 HSE 的配置有问题，platformIO 自带以及从 ST 官网下载的标准库中的 `system_stm32f4xx.c` 的`PLL_M`和`stm32f4xx.h`中的` HSE_VALUE`默认值与实际开发版的外部晶振频率不一致

### 3.解决方法

> 假设当前stm32f407的板子上外部晶振是8MHz（根据自身情况不同）

1.将 platformIO 的安装目录中的`/Users/<xxx>/.platformio/packages/framework-cmsis-stm32f4/Source/Templates/system_stm32f4xx.c`文件删除。


2.将解压得到的标准库的`.../STM32F4xx_DSP_StdPeriph_Lib_V1.9.0/Project/STM32F4xx_StdPeriph_Templates/system_stm32f4xx.c`文件复制到`/Users/<xxx>/.platformio/packages/framework-cmsis-stm32f4/Source/Templates/`目录下。


3.打开`/Users/<xxx>/.platformio/packages/framework-cmsis-stm32f4/Source/Templates/system_stm32f4xx.c`，在其中搜索到`PLL_M`

```c
#if defined(STM32F40_41xxx) || defined(STM32F427_437xx) || defined(STM32F429_439xx) || defined(STM32F401xx) || defined(STM32F469_479xx)
 /* PLL_VCO = (HSE_VALUE or HSI_VALUE / PLL_M) * PLL_N */
 #define PLL_M      25
```

将 `PLL_M` 后的`25`修改为 `8`。


4.找到`.../STM32F4xx_DSP_StdPeriph_Lib_V1.9.0/Libraries/CMSIS/Device/ST/STM32F4xx/Include/stm32f4xx.h`，在其中搜索`HSE_VALUE`，找到

```c
#if defined(STM32F40_41xxx) || defined(STM32F427_437xx)  || defined(STM32F429_439xx) || defined(STM32F401xx) || \
    defined(STM32F410xx) || defined(STM32F411xE) || defined(STM32F469_479xx)
 #if !defined  (HSE_VALUE)
  #define HSE_VALUE    ((uint32_t)25000000) /*!< Value of the External oscillator in Hz */
```

将`((uint32_t)25000000)`修改为`((uint32_t)8000000)`并强制保存。
