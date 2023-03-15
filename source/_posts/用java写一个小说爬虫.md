---
title: 用java写一个小说爬虫
date: 2023-03-15 10:18:54
tags: 
  - 工具类
categories: 后端技术
cover: false
---
前言：很多网站都不提供小说下载，提供小说下载的网站又不一定有想要的小说，所以就想着写个爬虫程序，不过通用性很差，每个网站都要单独做一些修改，去掉一些页面格式，适配性问题以后有心情再说吧
```JAVA
package txt;

import java.io.BufferedReader;
import java.io.BufferedWriter;
import java.io.File;
import java.io.FileInputStream;
import java.io.FileOutputStream;
import java.io.FileWriter;
import java.io.IOException;
import java.io.InputStreamReader;
import org.apache.http.client.ClientProtocolException;
import org.apache.http.client.methods.CloseableHttpResponse;
import org.apache.http.client.methods.HttpGet;
import org.apache.http.client.utils.HttpClientUtils;
import org.apache.http.impl.client.CloseableHttpClient;
import org.apache.http.impl.client.HttpClients;
import org.apache.http.util.EntityUtils;

public class CreateNovelTxt {
	public static String BDPATH = "D:/临时存储";
	public static String XSNAME = "";

	public static void main(String[] args) {
		//第一步：获取小说的全部章节目录
		createMl("http://www.swsk.org/xs/0/7/","目录");
		//第二步，通过章节目录网址获取内容并分章节生成txt文档
		//readMl("D:/临时存储","目录");
	}
	
	public static void createMl(String url,String wzKey){
		//1.创建httpclient（相当于打开一个浏览器）
	    CloseableHttpClient httpClient = HttpClients.createDefault();
	    //2.创建get请求（相当于浏览器输入网址）
	    HttpGet request = new HttpGet(url);
	    ///html/0/318/737543541.html
	    CloseableHttpResponse response = null;
	    try {
	        //3.执行get请求（相当于输入网址后敲回车键）
	        response = httpClient.execute(request);
	        //4.判断响应状态是否为200
	        if(response.getStatusLine().getStatusCode() == org.apache.http.HttpStatus.SC_OK){
	            //5.获取响应内容即页面内容
	            org.apache.http.HttpEntity httpEntity = response.getEntity();
	            String html = org.apache.http.util.EntityUtils.toString(httpEntity, "utf-8");
	            //打印出来
	            writeTXT(BDPATH,wzKey,html);
	        } else {
	            //如果返回状态不是200，比如404（页面不存在）等
	            System.out.println("返回状态不是200");                
	            System.out.println(EntityUtils.toString(response.getEntity(), "utf-8"));
	        }
	    } catch (ClientProtocolException e) {
	        e.printStackTrace();
	    } catch (IOException e) {
	        e.printStackTrace();
	    } finally {
	        //6.关闭
	        HttpClientUtils.closeQuietly(response);
	        HttpClientUtils.closeQuietly(httpClient);
	    }
	}
	
	public static void readMl(String path,String title){
		try {
			String fileName = path+"\\"+title+".txt";
			InputStreamReader in = new InputStreamReader(new FileInputStream(new File(fileName)),"utf-8");
			BufferedReader br = new BufferedReader(in);
			String line = "";
			while((line=br.readLine())!=null){
				//因为每个网站的网址都不一样，所以需要进行重新拼接
				String res = line.replace("<dd> <a style=\"\" href=\"/", "");
				//文章网址 示例：http://www.swsk.org/xs/0/7/45761919.html
				String url = "http://www.swsk.org/"+res.split("\">第")[0].trim();
				//文章标题 示例：第247章
				String wzKey = res.split("\">")[1].replace("</a></dd>", "");
				//System.out.println(wzKey);
				//将网页内容写入到txt中
				getHtmlY(url,wzKey);
			}
			br.close();
		} catch (Exception e) {
			e.printStackTrace();
		}
	}
	
	public static void getHtmlY(String url,String wzKey){
		//url示例"https://www.123wx.cc/html/0/318/56111081.html"
		//1.创建httpclient（相当于打开一个浏览器）
	    CloseableHttpClient httpClient = HttpClients.createDefault();
	    //2.创建get请求（相当于浏览器输入网址）
	    HttpGet request = new HttpGet(url);
	    ///html/0/318/737543541.html
	    CloseableHttpResponse response = null;
	    try {
	        //3.执行get请求（相当于输入网址后敲回车键）
	        response = httpClient.execute(request);
	        //4.判断响应状态是否为200
	        if(response.getStatusLine().getStatusCode() == org.apache.http.HttpStatus.SC_OK){
	            //5.获取响应内容即页面内容
	            org.apache.http.HttpEntity httpEntity = response.getEntity();
	            String html = org.apache.http.util.EntityUtils.toString(httpEntity, "utf-8");
	            //打印出来
	            writeTXT(BDPATH,wzKey,html);
	            readTxt(BDPATH,wzKey);
	        } else {
	            //如果返回状态不是200，比如404（页面不存在）等
	            System.out.println("返回状态不是200");                
	            System.out.println(EntityUtils.toString(response.getEntity(), "utf-8"));
	        }
	    } catch (ClientProtocolException e) {
	        e.printStackTrace();
	    } catch (IOException e) {
	        e.printStackTrace();
	    } finally {
	        //6.关闭
	        HttpClientUtils.closeQuietly(response);
	        HttpClientUtils.closeQuietly(httpClient);
	    }
	}
	
	//读取目标txt并向txt中写入文章内容
	public static void readTxt(String path,String title){
		BufferedReader br = null;
		try {
			String fileName = path+"\\"+title+".txt";
			File file = new File(fileName);
			InputStreamReader in = new InputStreamReader(new FileInputStream(file),"utf-8");
			br = new BufferedReader(in);
			String line = "";
			boolean iszj = false;
			boolean isContent = false;
			while((line=br.readLine())!=null){
				//输出标题
				if(line.contains("<div class=\"bookname\">")){
					iszj = true;
				}
				if(iszj){
					//根据获取的文章页面内容进行重新格式化拼接
					if(line.contains("<h1>")){
						line = line.replace("<h1>", "");
						line = line.replace("</h1>", "");
						System.out.println("开始读取-----"+line);
						line = line + "\n\n";
						fileOutputStream(BDPATH,XSNAME,line);
						iszj = false;
					}
				}
				//输出正文
				if(line.contains("<div id=\"content\"")){
					isContent = true;
				}
				if(isContent){
					if(line.contains("</div>")){
						return;
					}
					String content = line;
					int index = content.indexOf("&emsp;");//获取第一个"&emsp;"的位置3
					String str1 = content.substring(index+6);
					str1 = str1.replaceAll("<br />", "");
					str1 = str1.replace(">", "");
					str1 = str1 + "\n";
					//System.out.println(str1);
					fileOutputStream(BDPATH,XSNAME,str1);
				}
			}
			file.delete();
		} catch (Exception e) {
			e.printStackTrace();
		}finally{
			if(br!=null){
				try {
					br.close();
				} catch (IOException e) {
					e.printStackTrace();
				}
			}
		}
	}
	
	public static void fileOutputStream(String path,String title,String content) throws IOException{  
		content = content +"\n";
		String filePath = path+"\\"+title+".txt";
        FileOutputStream fos = new FileOutputStream(filePath,true);//true表示在文件末尾追加  
        fos.write(content.getBytes());  
        fos.close();//流要及时关闭  
    }  
	
	public static void writeTXT(String path,String title,String content){
	    try {
	        // 防止文件建立或读取失败，用catch捕捉错误并打印，也可以throw
	        /* 写入Txt文件 */
	        File writename = new File(path);// 相对路径，如果没有则要建立一个新的output。txt文件
	        if(!writename.exists()){
	            writename.mkdirs();
	        }
	        writename = new File(path+"\\"+title+".txt");// 相对路径，如果没有则要建立一个新的output。txt文件
	        writename.createNewFile(); // 创建新文件
	        BufferedWriter out = new BufferedWriter(new FileWriter(writename));
	        out.write(content); // \r\n即为换行
	        out.flush(); // 把缓存区内容压入文件
	        out.close(); // 最后记得关闭文件

	    } catch (Exception e) {
	        e.printStackTrace();
	    }
	}

}

```
