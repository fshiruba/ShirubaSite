---
layout: single
title:  "Algumas curiosidades sobre propriedades em C#"
date:   2020-11-11 09:13:00 -0300
categories: C# Portuguese
tags: C#
lang: pt
excerpt: "Diferença entre => e { get; } = para propriedades"
ref: csharppropen1
---

Está é uma **tradução livre/resumo** do artigo de Jiří Činčura encontrado [aqui](https://www.tabsoverspaces.com/233844-back-to-csharp-basics-difference-between-and-get-for-properties)

Nesse artigo Jiří menciona que embora produzindo resultados similares os usos de "=>" e "{ get; } =" são bem diferentes por baixo dos panos.

Veja o exemplo que ele passa:

{% highlight c# linenos %}
public class C
{
	public Foo A { get; } = new Foo();
	public Foo B => new Foo();
}
{% endhighlight %}

Essas duas propriedades **não** são a mesma coisa!

O que acontece "de baixo dos panos" é:

```C#
public class C
{
	readonly Foo _a = new Foo();
	
	public Foo A
	{
		get { return _a; }
	}

	public Foo B
	{
		get { return new Foo(); }
	}
}
```

O artigo original vai um pouco além do meu resumo e é uma leitura de aproximadamente 1 minuto se você entende inglês.


