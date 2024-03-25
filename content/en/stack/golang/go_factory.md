---
author: "wangjinbao"
title: "Golang的工厂模式"
date: 2021-01-01 00:00:08
description: "工厂模式将对象的创建封装在一个类中，并提供一个公共接口来创建对象。在Go语言中，可以使用接口来实现工厂模式"
draft: false
hideToc: false
enableToc: true
enableTocContent: false
author: wangjinbao
authorEmoji: 👻
tags: 
- golang
categories:
- golang



---
通过工厂方法创建对象，而无需指定创建对象的具体类
如下：
```go
package main

import "fmt"

type Product interface {
	GetInfo() string
}

type ConcreteProduct1 struct {
}

func (p *ConcreteProduct1) GetInfo() string {
	return "Product 1"
}

type ConcreteProduct2 struct {
}

func (p *ConcreteProduct2) GetInfo() string {
	return "Product 2"
}

type Factory struct {
}

func (f *Factory) CreateProduct(productType int) Product {
	switch productType {
	case 1:
		return &ConcreteProduct1{}
	case 2:
		return &ConcreteProduct2{}
	default:
		return nil
	}
}

func main() {
	factory := &Factory{}
	product1 := factory.CreateProduct(1)
	product2 := factory.CreateProduct(2)
	fmt.Println(product1.GetInfo())
	fmt.Println(product2.GetInfo())
}
```

结果：
```shell
$go run main.go
Product 1
Product 2
```