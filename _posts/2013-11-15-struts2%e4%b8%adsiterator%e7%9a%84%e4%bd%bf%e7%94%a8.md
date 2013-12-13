---
title: struts2中S:iterator的使用
author: gsh199449
layout: post
permalink: /?p=30
categories:
  - Java
format: aside
---
1.MapAction.java

import java.util.ArrayList;  
import java.util.HashMap;  
import java.util.List;  
import java.util.Map;

import com.opensymphony.xwork2.ActionSupport  
import com.model.Student  
public class MapAction extends ActionSupport  
{

private Map<String,String> map;

private Map<String,Student> studentMap;

private Map<String,String[]> arrayMap;

private Map<String,List<Student>> listMap;

public String testMap()  
{  
map=new HashMap<String,String>();  
map.put(&#8220;1&#8243;, &#8220;one&#8221;);  
map.put(&#8220;2&#8243;, &#8220;two&#8221;);

studentMap=new HashMap<String,Student>();  
studentMap.put(&#8220;student1&#8243;,new Student(new Long(1),&#8221;20034140201&#8243;,&#8221;张三1&#8243;,&#8221;男&#8221;,25));  
studentMap.put(&#8220;student2&#8243;,new Student(new Long(2),&#8221;20034140202&#8243;,&#8221;张三2&#8243;,&#8221;女&#8221;,26));  
studentMap.put(&#8220;student3&#8243;,new Student(new Long(3),&#8221;20034140202&#8243;,&#8221;张三3&#8243;,&#8221;男&#8221;,27));

arrayMap=new HashMap<String,String[]>();  
arrayMap.put(&#8220;arr1&#8243;, new String[]{&#8220;1&#8243;,&#8221;2003401&#8243;,&#8221;leejie&#8221;,&#8221;male&#8221;,&#8221;20&#8243;});  
arrayMap.put(&#8220;arr2&#8243;, new String[]{&#8220;2&#8243;,&#8221;2003402&#8243;,&#8221;huanglie&#8221;,&#8221;male&#8221;,&#8221;25&#8243;});  
arrayMap.put(&#8220;arr3&#8243;, new String[]{&#8220;3&#8243;,&#8221;2003403&#8243;,&#8221;lixiaoning&#8221;,&#8221;male&#8221;,&#8221;21&#8243;});

listMap=new HashMap<String,List<Student>>();

List<Student> list1=new ArrayList<Student>();  
list1.add(new Student(new Long(1),&#8221;20034140201&#8243;,&#8221;张三1&#8243;,&#8221;男&#8221;,25));  
list1.add(new Student(new Long(2),&#8221;20034140202&#8243;,&#8221;张三2&#8243;,&#8221;男&#8221;,25));  
list1.add(new Student(new Long(3),&#8221;20034140203&#8243;,&#8221;张三3&#8243;,&#8221;男&#8221;,25));  
listMap.put(&#8220;class1&#8243;, list1);

List<Student> list2=new ArrayList<Student>();  
list2.add(new Student(new Long(1),&#8221;20034140301&#8243;,&#8221;李四1&#8243;,&#8221;男&#8221;,20));  
list2.add(new Student(new Long(2),&#8221;20034140302&#8243;,&#8221;李四2&#8243;,&#8221;男&#8221;,21));  
list2.add(new Student(new Long(3),&#8221;20034140303&#8243;,&#8221;李四3&#8243;,&#8221;男&#8221;,22));  
list2.add(new Student(new Long(4),&#8221;20034140304&#8243;,&#8221;李四4&#8243;,&#8221;男&#8221;,23));  
listMap.put(&#8220;class2&#8243;, list2);

return SUCCESS;

}

public Map<String, String> getMap() {  
return map;  
}

public void setMap(Map<String, String> map) {  
this.map = map;  
}

public Map<String, Student> getStudentMap() {  
return studentMap;  
}

public void setStudentMap(Map<String, Student> studentMap) {  
this.studentMap = studentMap;  
}

public Map<String, String[]> getArrayMap() {  
return arrayMap;  
}

public void setArrayMap(Map<String, String[]> arrayMap) {  
this.arrayMap = arrayMap;  
}

public Map<String, List<Student>> getListMap() {  
return listMap;  
}

public void setListMap(Map<String, List<Student>> listMap) {  
this.listMap = listMap;  
}

}

2.testMap.jsp  
<%@ page contentType=&#8221;text/html;charset=UTF-8&#8243; %>  
<%@ taglib prefix=&#8221;s&#8221; uri=&#8221;/struts-tags&#8221; %>  
<html>  
<head>  
<title>struts2中的map遍历总结</title>  
</head>  
<body>  
<b>1.map中的value为String字符串</b><br>  
<s:iterator value=&#8221;map&#8221; id=&#8221;column&#8221;>  
<s:property value=&#8221;#column&#8221;/><br>  
key: <s:property value=&#8221;key&#8221;/><br>  
value:<s:property value=&#8221;value&#8221;/><br>  
\***\***\***\***\***\***\***\***\***\***\***\***\***\***<br>  
</s:iterator>

<b>2.map中的value为Student对象</b>  
<table border=&#8221;1&#8243; width=&#8221;50%&#8221;  cellspacing=&#8221;0&#8243; cellpadding=&#8221;0&#8243;>  
<tr>  
<td>key=value</td>  
<td>ID</td>  
<td>num</td>  
<td>name</td>  
<td>sex</td>  
<td>age</td>  
</tr>  
<s:iterator value=&#8221;studentMap&#8221; id=&#8221;column&#8221;>  
<tr>  
<td><s:property value=&#8221;#column&#8221;/></td>  
<td><s:property value=&#8221;value.id&#8221;/></td>  
<td><s:property value=&#8221;value.num&#8221;/></td>  
<td><s:property value=&#8221;value.name&#8221;/></td>  
<td><s:property value=&#8221;value.sex&#8221;/></td>  
<td><s:property value=&#8221;value.age&#8221;/></td>  
</tr>  
</s:iterator>  
</table>  
<p>

<b>3.map中的value为String数组</b>  
<table border=&#8221;1&#8243; width=&#8221;50%&#8221; cellspacing=&#8221;0&#8243; cellpadding=&#8221;0&#8243;>  
<tr>  
<td>key=value</td>  
<td>ID</td>  
<td>num</td>  
<td>name</td>  
<td>sex</td>  
<td>age</td>  
</tr>  
<s:iterator value=&#8221;arrayMap&#8221; id=&#8221;column&#8221;>  
<tr>  
<td><s:property value=&#8221;#column&#8221;/></td>  
<td><s:property value=&#8221;value[0]&#8220;/></td>  
<td><s:property value=&#8221;value[1]&#8220;/></td>  
<td><s:property value=&#8221;value[2]&#8220;/></td>  
<td><s:property value=&#8221;value[3]&#8220;/></td>  
<td><s:property value=&#8221;value[4]&#8220;/></td>  
</tr>  
</s:iterator>  
</table>  
<p>  
<b>4.map中的value为list集合</b>  
<table border=&#8221;1&#8243; width=&#8221;50%&#8221;  cellspacing=&#8221;0&#8243; cellpadding=&#8221;0&#8243;>  
<tr>  
<td>class</td>  
<td>ID</td>  
<td>num</td>  
<td>name</td>  
<td>sex</td>  
<td>age</td>  
</tr>

<1.<s:iterator value=&#8221;listHashMap&#8221; id=&#8221;listid&#8221;>  
<s:iterator value=&#8221;#listid.value&#8221; id=&#8221;listidsub&#8221;>  
<tr>  
<td><s:property value=&#8221;key&#8221;/></td>  
<td><s:property value=&#8221;id&#8221;/></td>  
<td><s:property value=&#8221;num&#8221;/></td>  
<td><s:property value=&#8221;name&#8221;/></td>  
<td><s:property value=&#8221;sex&#8221;/></td>  
<td><s:property value=&#8221;age&#8221;/></td>  
</tr>  
</s:iterator>  
</s:iterator>  
</table>

</body>  
</html>

转自：<http://fangzi370307.iteye.com/blog/1307526>