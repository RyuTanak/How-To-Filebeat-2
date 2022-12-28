# Filebeatの使いかた(2章)  

[1章](https://github.com/RyuTanak/How-To-Filebeat-1)では、データ取得、データ送信についてやりました。  
2章では、データ加工の方法について説明します。  

## 目次  
[データ加工について](#content1)  
[filebeatのprocessorを使ったデータ加工方法](#content2)  

<h2 id="content1">データ加工について</h2>  

データ加工とは言っても、何をどうやって加工するのかについて説明します。  
まず、「何を」とはログデータのことです。（1章でも説明）  
機器から出ているログ、例えば  
```  
2022-11-01 12:00:00 192.168.1.1 199.20.11.44 ms0000000  
2022-11-02 12:00:00 192.168.1.1 199.20.11.44 ms0000000  
・
・
・
```  
のようなデータのことです。  

「どうやって加工する」については、前提としてElasticsearchのindexにログデータを登録するとして  

|Field名|datetime|srcip|dstip|module_id|  
|-|-|-|-|-|  
|データ1|2022-11-01 12:00:00|192.168.1.1|199.20.11.44|ms0000000|  
|データ2|2022-11-02 12:00:00|192.168.1.1|199.20.11.44|ms0000000|  

のように空白で区切られている各項目に、Field名を付与することをします。  

Filebeatでは、様々な方法でデータ加工を行うことが出来ます。  
業務で得た知識から、知っているデータ加工方法を紹介していきます。  


<h2 id="content2">filebeatのprocessorを使ったデータ加工方法</h2>  

リファレンスは以下を参照  
https://www.elastic.co/guide/en/beats/filebeat/7.17/filtering-and-enhancing-data.html  

以下のログを加工してみる  
```  
2022-11-01 192.168.1.1 199.20.11.41 ms0000001  
2022-11-02 192.168.1.2 199.20.11.42 ms0000002  
2022-11-03 192.168.1.3 199.20.11.43 ms0000003  
```

filebeat.ymlに上記のprocessorを定義することでデータ加工を行うことができる。
今回は「dissect」というprocessorを使用する。  
filebeat.ymlを以下のように定義する。  

```yaml
filebeat.inputs:
- type: log
  enabled: true
  paths:
    - /var/log/test/*.log
setup.template.name: "tokyo"
setup.template.pattern: "tokyo-*"
setup.ilm.enabled: false
output.elasticsearch:
  hosts: ["localhost:9200"]
  username: "filebeat_writer"
  password: "YOUR_PASSWORD"
  index: "use-pipeline"
processors:
  - dissect:
      tokenizer: '%{date} %{srcip} %{dstip} %{module_id}'
      field: "message"
      target_prefix: "result"
```

indexのデータを見ると、ログの各項目にField名がついて登録されていることが分かる。  
![discover1](./image/discover1.png)  

