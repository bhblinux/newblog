---
title: XML
author: Bhblinux
tags:
  - java
categories:
  - 编程
date: 2018-04-22 22:14:00
---
## xml解析
### 解析工具

Dom解析工具

1. jaxp
2. JDOM工具
3. DOM4J工具

SAX 解析

1. SAX解析工具,

```java
节点信息:  Node
	节点名称
   节点类型
 nodeIterator
 
 标签节点: Element
 	标签名
 方法
 1. getRootElement
 2. elementIterator
 3. elements
 
 属性节点:Attribute
 	属性名称
    属性值
    
 方法:
 1. contactEle.attributeValue("id");
 2. contactEle.attribute("id");
 3. contactEle.attributes();
 4. contactEle.attributeIterator()
		
 
 文本节点: Text
 	 文本内容
 方法
 1.doc.getRootElement().element("contact").element("name");
 2. doc.getRootElement().element("contact").elementText("name");
```

### DOM4J解析

1. 导包

dom4j-1.6.1.jar

2. 获取Document文档

```java
/*new File(".").getAbsolutePath() D:\Workspaces\MyEclipse 8.5\day0422\.*/
/*
 * 读取xml内容
 */
package com.yonyou.demo01;

import java.io.File;
import java.util.Iterator;

import org.dom4j.Document;
import org.dom4j.DocumentException;
import org.dom4j.Element;
import org.dom4j.Node;
import org.dom4j.io.SAXReader;
import org.junit.Test;

public class test02 {
	 
	
	/*
	 * 得到节点信息
	 */
	@Test
	public void test01() throws Exception{
		//1.创建xml解析器
		SAXReader saxReader = new SAXReader();
		//2.读取xml文档,返回Document对象
		Document doc = saxReader.read(new File("src/contact.xml"));
		//3nodeIterator 得到当前节点下所有子节点对象(不包含孙或以下节点)
		Iterator<Node> it = doc.nodeIterator();
		while(it.hasNext()){ //判断是否有下一个元素
			Node node = it.next(); //取出元素
			System.out.println(node.getName()); //只有一个即为根节点
			//继续去出去下面的节点
			//只有标签节点才有子节点
			if(node instanceof Element){
				Element elem=(Element)node;
				Iterator<Node> nodeIterator = elem.nodeIterator();
				while(nodeIterator.hasNext()){
					Node next = nodeIterator.next();
					System.out.println(next.getName());
				}
			}
		}
		
		
	}
	
	@Test
	public void test02() throws Exception{
		//1.创建xml解析器
		SAXReader saxReader = new SAXReader();
		//2.读取xml文档,返回Document对象
		Document doc = saxReader.read(new File("src/contact.xml"));
		
		//获取根标签
		Element rootElement = doc.getRootElement();
		getChileNode(rootElement);
		
	}
	/*
	 * 获取传入的标签下的所有子节点
	 */
	public  void getChileNode(Element elem){
		System.out.println(elem.getName());
		//得到子节点
		Iterator<Node> it = elem.nodeIterator();
		while(it.hasNext()){
			Node node=it.next();
			if(node instanceof Element){
				getChileNode((Element)node);
			}
			
		}
	}
}


```


```java

/*
 * 读取xml内容
 */
package com.yonyou.demo01;

import java.io.File;
import java.util.Iterator;
import java.util.List;

import org.dom4j.Attribute;
import org.dom4j.Document;
import org.dom4j.DocumentException;
import org.dom4j.Element;
import org.dom4j.Node;
import org.dom4j.io.SAXReader;
import org.junit.Test;

public class test02 {
	 
	
	/*
	 * 得到节点信息
	 */
	@Test
	public void test01() throws Exception{
		//1.创建xml解析器
		SAXReader saxReader = new SAXReader();
		//2.读取xml文档,返回Document对象
		Document doc = saxReader.read(new File("src/contact.xml"));
		//3nodeIterator 得到当前节点下所有子节点对象(不包含孙或以下节点)
		Iterator<Node> it = doc.nodeIterator();
		while(it.hasNext()){ //判断是否有下一个元素
			Node node = it.next(); //取出元素
			System.out.println(node.getName()); //只有一个即为根节点
			//继续去出去下面的节点
			//只有标签节点才有子节点
			if(node instanceof Element){
				Element elem=(Element)node;
				Iterator<Node> nodeIterator = elem.nodeIterator();
				while(nodeIterator.hasNext()){
					Node next = nodeIterator.next();
					System.out.println(next.getName());
				}
			}
		}
		
		
	}
	
	@Test
	public void test02() throws Exception{
		//1.创建xml解析器
		SAXReader saxReader = new SAXReader();
		//2.读取xml文档,返回Document对象
		Document doc = saxReader.read(new File("src/contact.xml"));
		
		//获取根标签
		Element rootElement = doc.getRootElement();
		getChileNode(rootElement);
		
	}
	/*
	 * 获取传入的标签下的所有子节点
	 */
	public  void getChileNode(Element elem){
		System.out.println(elem.getName());
		//得到子节点
		Iterator<Node> it = elem.nodeIterator();
		while(it.hasNext()){
			Node node=it.next();
			if(node instanceof Element){
				getChileNode((Element)node);
			}
			
		}
	}
	
	/*
	 * 获取标签
	 */
	@Test
	public void test3() throws Exception{
		//1.创建xml解析器
		SAXReader saxReader = new SAXReader();
		//2.读取xml文档,返回Document对象
		Document doc = saxReader.read(new File("src/contact.xml"));
		//获取根标签
		Element rootElement = doc.getRootElement();
		
		//获取标签的名称
		String name = rootElement.getName();
		System.out.println(name);
		
//		//获取第一个子标签
//		Element element = rootElement.element("contact");
//		System.out.println("第一个 "+element.getName());
		
		//得到当前标签下指定名称所有子标签
		Iterator<Element> it = rootElement.elementIterator("contact");
		while(it.hasNext()){
			Element next = it.next();
			System.out.println(next.getName());
		}
		System.out.println("----------------------------");
		
		//获取当前标签下所有子标签
		List<Element> elements = rootElement.elements();
		for (Element element : elements) {
			System.out.println(element.getName());
		}
		
		
		
	}
	
	/*
	 * 获取属性
	 */
	@Test
	public void test4() throws Exception{
		//1.创建xml解析器
		SAXReader saxReader = new SAXReader();
		//2.读取xml文档,返回Document对象
		Document doc = saxReader.read(new File("src/contact.xml"));
		//获取根标签
		Element contactEle = doc.getRootElement().element("contact");
		//获取属性
		String attributeValue = contactEle.attributeValue("id");
		System.out.println(attributeValue);
		
		System.out.println("0-------------------------0");
		//获取指定属性名称的属性对象
		Attribute attribute = contactEle.attribute("id");
		System.out.println(attribute.getName()+" = "+attribute.getValue());
		System.out.println("0-------------------------0");
		//得到所有属性对象
		List<Attribute> attributes = contactEle.attributes();
		for (Attribute attribute2 : attributes) {
			System.out.println(attribute2.getName()+" = "+attribute2.getValue());
			
		}
		
	}
	
	/*
	 * 获取文本
	 */
	
	@Test
	public void test5() throws Exception{
		//1.创建xml解析器
		SAXReader saxReader = new SAXReader();
		//2.读取xml文档,返回Document对象
		Document doc = saxReader.read(new File("src/contact.xml"));
		//先获取标签,再去标签上的文本
		Element element = doc.getRootElement().element("contact").element("name");
		String text = element.getText();
		System.out.println(text);
		System.out.println("---------------------");
		//方法二
		String elementText = doc.getRootElement().element("contact").elementText("name");
		System.out.println(elementText);
	}
}
```
```java
package com.yonyou.demo01;

import java.io.File;
import java.util.Iterator;
import java.util.List;

import org.dom4j.Attribute;
import org.dom4j.Document;
import org.dom4j.DocumentException;
import org.dom4j.Element;
import org.dom4j.Node;
import org.dom4j.Text;
import org.dom4j.io.SAXReader;
import org.junit.Test;

/*
 * 获取完整xml内容
 */
public class test03 {

	
	@Test
	public void test() throws Exception{
		//1.创建xml解析器
		SAXReader saxReader = new SAXReader();
		//2.读取xml文档,返回Document对象
		Document doc = saxReader.read(new File("src/contact.xml"));
		StringBuffer sb=new StringBuffer();
		getChileElement(doc.getRootElement(),sb);
		System.out.println(sb);
	}
	
	
	/*
	 * 获取当前标签的子标签
	 */
	public void getChileElement(Element elem,StringBuffer sb){
		//System.out.println(elem.getName());
		//开始标签
		sb.append("<"+elem.getName());
		//得到标签的属性列表
		List<Attribute> attributes = elem.attributes();
		if(attributes!=null){
			for (Attribute attribute : attributes) {
				//System.out.println("属性 --- " + attribute.getName());
				sb.append(" "+attribute.getName()+"=\""+attribute.getValue());
			}
			sb.append(">");
			//获取文本
			//String text = elem.getText();
			//System.out.println(text);
			//sb.append(text);
		}
		Iterator<Node> nodeIterator = elem.nodeIterator();
		
		while(nodeIterator.hasNext()){
			Node next = nodeIterator.next();
			//标签
			if(next instanceof Element){
				getChileElement((Element)next,sb);
			}
			//文本
			if(next instanceof Text){
				Text text=(Text)next;
				sb.append(text.getText());
			}
		}
		sb.append("</"+elem.getName()+">");
	}
	
}


```

## 保存xml文档

```java
/*
 * 写xml
 */
package com.yonyou.demo01;

import java.io.File;
import java.io.FileNotFoundException;
import java.io.FileOutputStream;

import org.dom4j.Document;
import org.dom4j.io.OutputFormat;
import org.dom4j.io.SAXReader;
import org.dom4j.io.XMLWriter;

public class test04 {
	public static void main(String[] args) throws Exception {
		//1.创建xml解析器
		SAXReader saxReader = new SAXReader();
		//2.读取xml文档,返回Document对象
		Document doc = saxReader.read(new File("src/contact.xml"));
		FileOutputStream fileOutputStream = new FileOutputStream("src/text.xml");
		//制定一个写出的模式
		//紧凑的格式,去除空格 和换行
		OutputFormat outputFormat = OutputFormat.createCompactFormat();
		//漂亮的格式
		OutputFormat createPrettyPrint = OutputFormat.createPrettyPrint();
        createPrettyPrint.setEncoding("utf-8");//设置生成文件的编码
		//2创建写出对象
		XMLWriter xmlWriter = new XMLWriter(fileOutputStream,createPrettyPrint);
		xmlWriter.write(doc);
		xmlWriter.close();
		
	}
}


```

### 修改

```java
/*
 * 
 * 修改内容
 * 增加:标签,属性
 * 修改: 属性值,文本
 * 删除 标签,属性
 */
package com.yonyou.demo01;

import java.io.FileNotFoundException;
import java.io.FileOutputStream;

import org.dom4j.Document;
import org.dom4j.DocumentHelper;
import org.dom4j.Element;
import org.dom4j.io.OutputFormat;
import org.dom4j.io.XMLWriter;
import org.junit.Test;

public class demo05 {

	@Test
	public void test1() throws Exception{
		//创建文档
		Document doc = DocumentHelper.createDocument();
		//增加跟标签
		Element root = doc.addElement("ContactList");
		Element addElement = root.addElement("contact");
		//添加属性
		addElement.addAttribute("id", "001");
		Element addElement2 = addElement.addElement("name");
		addElement2.addText("wangzuliang");
		
		//保存
		FileOutputStream out=new FileOutputStream("src/text2.xml");
		OutputFormat createPrettyPrint = OutputFormat.createPrettyPrint();
		createPrettyPrint.setEncoding("utf-8");
		XMLWriter xmlWriter = new XMLWriter(out,createPrettyPrint);
		xmlWriter.write(doc);
		xmlWriter.close();
	}

	
}

```

### 修改属性值

```java
@Test
	public void test2() throws Exception{
		Document doc = new SAXReader().read(new File("src/contact.xml"));
		Element contactElem = doc.getRootElement().element("contact");
		//获取属性 方法一
		Attribute attribute = contactElem.attribute("id");
		attribute.setValue("001");
		
		//方法二
		//添加一个同名的属性值,如果该属性存在会覆盖
		contactElem.addAttribute("a", "abc");
		
		//保存
		FileOutputStream out=new FileOutputStream("src/contact.xml");
		OutputFormat createPrettyPrint = OutputFormat.createPrettyPrint();
		createPrettyPrint.setEncoding("utf-8");
		XMLWriter xmlWriter = new XMLWriter(out,createPrettyPrint);
		xmlWriter.write(doc);
		xmlWriter.close();
	}

```

### 修改文本

```java
@Test
	public void test2() throws Exception{
		Document doc = new SAXReader().read(new File("src/contact.xml"));
		Element contactElem = doc.getRootElement().element("contact");
		//获取属性 方法一
		Attribute attribute = contactElem.attribute("id");
		attribute.setValue("001");
		
		//方法二
		//添加一个同名的属性值,如果该属性存在会覆盖
		contactElem.addAttribute("a", "abc");
		
		//保存
		FileOutputStream out=new FileOutputStream("src/contact.xml");
		OutputFormat createPrettyPrint = OutputFormat.createPrettyPrint();
		createPrettyPrint.setEncoding("utf-8");
		XMLWriter xmlWriter = new XMLWriter(out,createPrettyPrint);
		xmlWriter.write(doc);
		xmlWriter.close();
	}
```
### 删除标签

```java
/*
	 * 删除
	 */
	@Test
	public void test4() throws Exception{
		Document doc = new SAXReader().read(new File("src/contact.xml"));
		Element contactElem = doc.getRootElement().element("contact");
		//1.获取标签对象,2.删除标签
		
		Element element = contactElem.element("name");
		element.detach();
        //方法二
		element.getParent().remove(element);
		//保存
		FileOutputStream out=new FileOutputStream("src/contact.xml");
		OutputFormat createPrettyPrint = OutputFormat.createPrettyPrint();
		createPrettyPrint.setEncoding("utf-8");
		XMLWriter xmlWriter = new XMLWriter(out,createPrettyPrint);
		xmlWriter.write(doc);
		xmlWriter.close();
	}
```

### 删除属性

```java
//删除属性
		List elements = doc.getRootElement().elements();
		Element contactElem1 =(Element) elements.get(1);
		Attribute attribute = contactElem1.attribute("id");
		attribute.detach();
```

## XPATH

1. 导包 jaxen-1.1-beta-6.jar

2. 使用xpath方法

	- List list selectNodes( xpath表达式 ); 获取多个
     
   - Node node selectSingleNode(  xpath表达式 ); 获取指定
   
### xpath语法

1. / 绝对路径, 表示从xml的根位置或子元素(一个层次结构不包含第二层)

2. // 所有满足双斜线//之后规则的元素(无论层级关系)

3. * 选择所有由星号之前的路径所定位的元素

4. [] 可以进一步的指定元素, 其中数字表示元素在选择集里的位置, 而last()函数则表示选择集中的最后一个元素.

5. 属性通过前缀 @ 来指定 //BBB[not(@*)]取反

6. = 属性的值可以被用来作为选择的准则, normalize-space函数删除了前部和尾部的空格, 并且把连续的空格串替换为一个单一的空格 //BBB[normalize-space(@name)='bbb'] 去掉空格

7. count()函数可以计数所选元素的个数

8. name()函数返回元素的名称, start-with()函数在该函数的第一个参数字符串是以第二个参数字符开始的情况返回true, contains()函数当其第一个字符串参数包含有第二个字符串参数时返回true. //*[name()='BBB'] //*[starts-with(name(),'B')] //*[contains(name(),'C')]

9. string-length函数返回字符串的字符数,你应该用&lt;替代<, 用&gt;代替>

10. 多个路径可以用分隔符 | 合并在一起

## SAX解析

加载一点,读取一点,处理一点,对内存要求比较低

- DefaultHandler

```java

 void startDocument()  接收文档开始的通知 
 void endDocument() 接收文档结束的通知。 
 
 startElement(String uri, String localName, String qName, Attributes attributes) 接收元素开始的通知。
 
 endElement(String uri, String localName, String qName)  接收元素结束的通知。
 
 characters(char[] ch, int start, int length) 接收元素中字符数据的通知。
 
 
 /*
 * sax解析
 */
package com.yonyou.demo01;

import java.io.File;

import javax.xml.parsers.ParserConfigurationException;
import javax.xml.parsers.SAXParser;
import javax.xml.parsers.SAXParserFactory;

import org.xml.sax.Attributes;
import org.xml.sax.SAXException;
import org.xml.sax.helpers.DefaultHandler;

public class demo6 {

	
	public static void main(String[] args) throws Exception {
		//1.创建saxparser对象
		SAXParser newSAXParser = SAXParserFactory.newInstance().newSAXParser();
		//2. 调用parse 方法
		/*
		 * 参数一 xml文件
		 * 参数二 defalutHandler的子类
		 */
		myDefalutHandler myDefalutHandler = new myDefalutHandler() ;
		newSAXParser.parse(new File("src/contact.xml"),myDefalutHandler);
		StringBuffer sb = myDefalutHandler.getSb();
		System.out.println(sb);
	}
}


class myDefalutHandler extends DefaultHandler{
	StringBuffer sb=null;
	myDefalutHandler(){
		sb=new StringBuffer();
	}
	
	public StringBuffer getSb() {
		return sb;
	}

	public void setSb(StringBuffer sb) {
		this.sb = sb;
	}

	//开始文档
	@Override
	public void startDocument() throws SAXException {
	}
	
	//开始读取标签
	/*
	 * @param qname :表示开始标签的标签名
	 */
	@Override
	public void startElement(String uri, String localName, String qName,
			Attributes attributes) throws SAXException {
		sb.append("<"+qName);
		int length = attributes.getLength();
		for(int i=0;i<length;i++){
			sb.append(" "+attributes.getQName(i)+"=");
			sb.append("\""+attributes.getValue(i)+"\"");
		}
		sb.append(">");
	}
	
	//文本
	@Override
	public void characters(char[] ch, int start, int length)
			throws SAXException {
		sb.append(new String(ch,start,length));
	}
	
	
	//结束标签
	@Override
	public void endElement(String uri, String localName, String qName)
			throws SAXException {
		sb.append("</"+qName);
		
		
		
		sb.append(">");
	}
	
	//结束文档
	@Override
	public void endDocument() throws SAXException {
	}
}
```
