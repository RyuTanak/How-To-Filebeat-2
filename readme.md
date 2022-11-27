# Filebeatの使いかた(2章)  

[1章](https://github.com/RyuTanak/How-To-Filebeat-1)では、データ取得、データ送信についてやりました。  
2章では、データ加工の方法について説明します。  

## 目次  
[データ加工について](#content1)  

<h2 id="content1">データ加工について</h2>  

データ加工とは言っても、何をどうやって加工するのかについて説明します。  
まず、「何を」とはログデータのことです。（1章でも説明）  
機器から出ているログ、例えば  
```  
2022-11-01 12:00:00 192.168.1.1 199.20.11.44 ms0000000  
2022-11-02 12:00:00 192.168.1.1 199.20.11.44 ms0000000  
```  
のようなデータのことで、