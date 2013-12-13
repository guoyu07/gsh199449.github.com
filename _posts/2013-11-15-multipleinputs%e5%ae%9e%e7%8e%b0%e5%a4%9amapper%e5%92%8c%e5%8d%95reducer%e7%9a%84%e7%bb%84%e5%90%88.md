---
title: MultipleInputs实现多Mapper和单Reducer的组合
author: gsh199449
layout: post
permalink: /?p=10
categories:
  - Hadoop
format: aside
---
在MapReduce架构中，有时候需要处理一种特殊情况：

现在存在多个结构不同的数据文件，Job需要在这些数据文件中提取一些数据，并交给一个Reducer进一步处理。这种操作类似于关系数据库中的连接操作。在一个Mapper中根据输入文件名( 使用 Job.get(&#8220;map.input.file&#8221;) 获取 )来区分数据来源并分别处理，是一个解决办法，但有时需要一个更加彻底的办法，那就是MultipleInputs.

MultipleInputs:  
This class supports MapReduce jobs that have multiple input paths with a different InputFormat and Mapper for each path.  
支持多个Mapper的输出混合到一个shuffle, 一个reducer, 其中每个Mapper拥有不同的InputFormat和Mapper处理类

&nbsp;

0. Hadoop版本  
1.0.3是可以的。最新版本(1.0.4)未测试

使用MultipleInputs类实现MapReduce任务的步骤如下：  
1. 首先根据不同的输入文件编写Mapper class  
不同文件结构和含义不同，Mapper class处理不同。  
所有Mapper需要输出相同的数据类型。  
对于输出value，需要标记该value来源，以便Reducer识别  
2. Reducer class根据输入以及tag标记进一步处理数据  
Reducer接受数据为 key-value(包含tag)  
根据这些数据进一步处理。得到最终结果

&nbsp;

示例：<a href="http://download.csdn.net/detail/king_on/5187769" target="_blank">示例-MapReduce-MultipleInputs用法</a>

“readme.txt&#8221;

> 1.输入  
> file_1.txt  
> 编号tab国家名  
> file_2.txt  
> 国家名tab首都名
> 
> 2.处理过程  
> MapA.class 处理file_1.txt  
> MapB.class 处理file_2.txt  
> Reduce.class处理最后结果，将国家名、编号和首都格式化为：&#8221;ID=%s\tcountry=%s\tcapital=%s&#8221;
> 
> 3.输出结果：  
> &#8212;&#8212;&#8212;&#8212;&#8212;&#8212;&#8212;&#8212;&#8212;&#8212;  
> Bhutan ID=2 name=Bhutan capital=Thimphu  
> Burma ID=3 name=Burma capital=Rangoon  
> China ID=1 name=China capital=BeiJing  
> India ID=4 name=India capital=New Delhi  
> Laos ID=5 name=Laos capital=Vientiane  
> &#8212;&#8212;&#8212;&#8212;&#8212;&#8212;&#8212;&#8212;&#8212;&#8212;&#8212;&#8212;&#8212;&#8212;&#8212;-

&#8220;Example_1.java&#8221;

<pre lang="java" line="1" file="download.txt" colla="+">package org.forward.example.hadoop.multipleinputs;

import java.io.IOException;
import java.util.Iterator;

import org.apache.hadoop.fs.Path;
import org.apache.hadoop.io.LongWritable;
import org.apache.hadoop.io.Text;
import org.apache.hadoop.mapred.FileOutputFormat;
import org.apache.hadoop.mapred.JobClient;
import org.apache.hadoop.mapred.JobConf;
import org.apache.hadoop.mapred.MapReduceBase;
import org.apache.hadoop.mapred.Mapper;
import org.apache.hadoop.mapred.OutputCollector;
import org.apache.hadoop.mapred.Reducer;
import org.apache.hadoop.mapred.Reporter;
import org.apache.hadoop.mapred.TextInputFormat;
import org.apache.hadoop.mapred.TextOutputFormat;
import org.apache.hadoop.mapred.lib.MultipleInputs;

public class Example_1
{
	/**
	 * 处理File_1.txt 输入: 行号,行内容&lt;br/&gt;
	 * 输出: key=国家名, value=编号
	 * 
	 * @author Jim
	 * 
	 */
	public static class MapA extends MapReduceBase implements Mapper&lt;LongWritable, Text, Text, Capital_or_ID&gt;
	{

		@Override
		public void map(LongWritable lineN, Text content, OutputCollector&lt;Text, Capital_or_ID&gt; collect, Reporter rp)
				throws IOException
		{
			// TODO Auto-generated method stub
			String part[] = content.toString().split("\\t");
			if (part.length == 2)
			{
				Capital_or_ID coi = new Capital_or_ID(part[0], "file_1");
				collect.collect(new Text(part[1]), coi);

			}
			System.out.println("in MapA: content="+content);
			for(String s:part)
			{
				System.out.println("part[idx]="+s);
			}
		}

	}

	/**
	 * 处理File_2.txt 输入: 行号,行内容&lt;br/&gt;
	 * 输出: key=国家名,value=首都名
	 * 
	 * @author Jim
	 * 
	 */
	public static class MapB extends MapReduceBase implements Mapper&lt;LongWritable, Text, Text, Capital_or_ID&gt;
	{

		@Override
		public void map(LongWritable lineN, Text content, OutputCollector&lt;Text, Capital_or_ID&gt; collect, Reporter rp)
				throws IOException
		{
			// TODO Auto-generated method stub
			String part[] = content.toString().split("\\t");
			if (part.length == 2)
			{
				Capital_or_ID coi = new Capital_or_ID(part[1], "file_2");
				collect.collect(new Text(part[0]), coi);

			}
			System.out.println("in MapB: content="+content);
			for(String s:part)
			{
				System.out.println("part[idx]="+s);
			}
		}

	}

	/**
	 * Reduce.class处理最后结果，将国家名、编号和首都格式化为："ID=%s\tcountry=%s\tcapital=%s"
	 * 
	 * ID=1 country=China capital=BeiJing
	 * 
	 * @author Jim
	 * 
	 */
	public static class Reduce extends MapReduceBase implements Reducer&lt;Text, Capital_or_ID, Text, Text&gt;
	{

		@Override
		public void reduce(Text countryName, Iterator&lt;Capital_or_ID&gt; values, OutputCollector&lt;Text, Text&gt; collect,
				Reporter rp) throws IOException
		{
			// TODO Auto-generated method stub
			String capitalName = null, ID = null;
			while (values.hasNext())
			{
				Capital_or_ID coi = values.next();
				if (coi.getTag().equals("file_1"))
				{
					ID = coi.getValue();
				} else if (coi.getTag().equals("file_2"))
				{
					capitalName = coi.getValue();
				}
			}

			String result = String.format("ID=%s\tname=%s\tcapital=%s", ID, countryName, capitalName);

			collect.collect(countryName, new Text(result));
		}
	}

	public static void main(String args[]) throws IOException
	{
		// args[0] file1 for MapA
		String file_1 = args[0];
		// args[1] file2 for MapB
		String file_2 = args[1];
		// args[2] outPath
		String outPath = args[2];

		JobConf conf = new JobConf(Example_1.class);
		conf.setJobName("example-MultipleInputs");

		conf.setMapOutputKeyClass(Text.class);
		conf.setMapOutputValueClass(Capital_or_ID.class);

		conf.setOutputKeyClass(Text.class);
		conf.setOutputValueClass(Text.class);

		conf.setReducerClass(Reduce.class);

		conf.setOutputFormat(TextOutputFormat.class);
		FileOutputFormat.setOutputPath(conf, new Path(outPath));

		MultipleInputs.addInputPath(conf, new Path(file_1), TextInputFormat.class, MapA.class);
		MultipleInputs.addInputPath(conf, new Path(file_2), TextInputFormat.class, MapB.class);

		JobClient.runJob(conf);
	}
}</pre>

另外，需要实现一个自定义数据类型，用于区分两个Mapper的输出。

&nbsp;

&nbsp;

<pre lang="java" line="1" file="download.txt" colla="+">package org.forward.example.hadoop.multipleinputs;

import java.io.DataInput;
import java.io.DataOutput;
import java.io.IOException;

import org.apache.hadoop.io.Writable;
/**
 * @author Jim
 * 该类为自定义数据类型. 其目的是为了在多个输入文件多个Mapper的情况下，标记数据.从而Reduce可以辨认value的来源
 */
public class Capital_or_ID implements Writable
{
	/** 相同来源的value应当具有相同的tag */
	private String tag=null;

	private String value=null;

	public Capital_or_ID()
	{

	}
	public Capital_or_ID(String value,String tag)
	{
		this.value=value;
		this.tag=tag;
	}
	public String getTag()
	{
		return tag;
	}
	public void setTag(String tag)
	{
		this.tag=tag;
	}
	public String getValue()
	{
		return value;
	}
	@Override
	public void readFields(DataInput in) throws IOException
	{
		// TODO Auto-generated method stub
		tag=in.readUTF();
		value=in.readUTF();
	}

	@Override
	public void write(DataOutput out) throws IOException
	{
		// TODO Auto-generated method stub
		out.writeUTF(tag);
		out.writeUTF(value);
	}

}</pre>