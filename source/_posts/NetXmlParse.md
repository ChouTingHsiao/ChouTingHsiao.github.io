---
title: 如何解析 XML 資料
date: 2024-02-04 18:00:00
categories: .Net
tags: [.Net, XML]
---

# 宣告 XML 資料

XML 格式字串如下
```c#
string doc = @"<properties>
<property key='name'>Test</property>
<property key='age'>10</property>
</properties>";
```

# 使用 XDocument 解析 XML

## 讀取資料並解析
```c#
// XML 字串轉 XDocument
XDocument xmlDoc = XDocument.Parse(doc);

// 取得 property 列表
List<XElement> xmlElements = xmlDoc.Element("properties").Elements("property").ToList();

// 取得第一筆 key 為 name 資料
string name = xmlElements.Where(x => x.Attribute("key").Value == "name").FirstOrDefault()?.Value;
```

<!--more-->

# 使用序列化與反序列化方法解析 XML

## 建立序列化與反序列化方法
```c#
// 物件轉 XML 字串
public static string XmlSerialize<T>(T obj)
{
	XmlSerializer xsSubmit = new XmlSerializer(typeof(T));

	XmlSerializerNamespaces ns = new XmlSerializerNamespaces();
	ns.Add(string.Empty, string.Empty);

	using (var memStm = new MemoryStream())
	using (var xw = XmlWriter.Create(memStm))
	{
		xsSubmit.Serialize(xw, obj, ns);
		byte[] utf8 = memStm.ToArray();
		return Encoding.UTF8.GetString(utf8);
	}
}

// XML 字串轉物件
public static T XmlDeSerialize<T>(string xmlString)
{
	XmlSerializer xsSubmit = new XmlSerializer(typeof(T));

	byte[] byteArray = Encoding.UTF8.GetBytes(xmlString);

	using (var memStm = new MemoryStream(byteArray))
	using (var xr = XmlReader.Create(memStm))
	{
		object data = xsSubmit.Deserialize(xr);
		return (T)data;
	}
}
```

## 建立類別
```c#
[XmlRoot(ElementName = "property")]
public class Property {

  [XmlAttribute(AttributeName = "key")]
  public string Key {
    get;
    set;
  }

  [XmlText]
  public string Text {
    get;
    set;
  }
}

[XmlRoot(ElementName = "properties")]
public class Properties {

  [XmlElement(ElementName = "property")]
  public List < Property > Property {
    get;
    set;
  }
}
```

## 讀取資料並解析
```c#
// XML 字串轉物件
Properties data = XmlDeSerialize<Properties>(doc);

// 取得第一筆 key 為 name 資料
string name = data.Property.Where(x => x.Key == "name").FirstOrDefault().Text;

// 物件轉 XML 字串
string xmlFile = XmlSerialize(data);
```

{% note warning %}
📜 參考資料
1. [XDocument Class](https://learn.microsoft.com/en-us/dotnet/api/system.xml.linq.xdocument?view=net-8.0)
2. [XmlSerializer Class](https://learn.microsoft.com/en-us/dotnet/api/system.xml.serialization.xmlserializer?view=net-8.0)
{% endnote %}