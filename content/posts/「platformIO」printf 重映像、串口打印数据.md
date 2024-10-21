---
title: "「platformIO」printf 重映像、串口打印数据"
date: 2024-10-21T21:56:56.554726
categories: ['Docs']
author: "Ronan"
---
在 `keil` 环境中使用 printf 通过串口打印数据：

- 在 main.c 引入`stdio.h`
- 重写`fputc`函数

  ```c
  // keil arm环境，如果你要用printf函数，就必须重写fputc函数，并且在魔术棒里面勾选使用Micro LIB
  int fputc(int ch, FILE *f)
  {
  	USART_SendData(USART1, ch);
  	while (USART_GetFlagStatus(USART1, USART_FLAG_TC) == RESET);//循环的判断串口是否发送完数据

  	return ch;
  }
  ```

  完成以上配置即可在 main 函数中使用 printf 函数通过串口打印数据。

在 `arm-***-gcc` 环境使用 printf 通过串口打印数据：

- 不需要引入`stdio.h`头文件
- 直接在 main.c 中重写`_write`方法即可

  ```c
  // gcc 编译环境重写该方法
  //这是一种写法
  int _write( int fd, char *pBuffer, int size) {
      for (int i = 0; i < size; i++) {
          while ((USART1->SR&0x40) == 0);		// 等待串口发送完毕
          USART1->DR = (uint8_t) pBuffer[i];
      }
      return size;
  }

  //还有另一种写法，根据情况按需选择
  int _write( int fd, char *pBuffer, int size) {
      for (int i = 0; i < size; i++)
  	{
          while (USART_GetFlagStatus(USART1, USART_FLAG_TC) == RESET);		// 等待串口发送完毕
          USART1->DR = (uint8_t) pBuffer[i];
      }
      return size;
  }
  ```
