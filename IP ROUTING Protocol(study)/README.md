# ELK

- [ELK](https://www.elastic.co/kr/what-is/elk-stack) 는 Elastic+Logstash+Kibana 의 약어.
- [ELK 기본 설치 및 강의](https://edu.goorm.io/learn/lecture/4013/elk-%EC%8A%A4%ED%83%9D-elasticsearch-logstash-kibana-%EC%9C%BC%EB%A1%9C-%EB%8D%B0%EC%9D%B4%ED%84%B0-%EB%B6%84%EC%84%9D) 참조
- Logstash : Logstash는 여러 소스에서 동시에 데이터를 수집하여 변환한 후 Elasticsearch 같은 “stash”로 전송하는 서버 사이드 데이터 처리 파이프라인
- Elasticsearch : Elasticsearch는 검색 및 분석 엔진
- Kibana : Kibana는 사용자가 Elasticsearch에서 차트와 그래프를 이용해 데이터를 시각화

## How to Run

- 개발환경
    - Centos 7.0
    - Elastic Search
    - Logstash 5.1.1
    - Kibana 5.0.2
    - fileBeat

### ELK 기본 자료

- CRUD : Type : Class, id=1 , -d = Document ,@oneclass.json 삽입

    ~~~
    Curl -XPOST http://localhost:9200/classess/class/1/ -d @oneclass.json
    ~~~

- 엘라스틱서치 데이터 업데이트

    ~~~
    Curl -XPOST http://localhost:9200/classes/class/1/ -d '{"title":"Algorithm","Professor":"John"}'
    ~~~

- 엘라스틱서치 데이터 Json 형태 View

    ~~~
    Curl -XGET http://localhos:9200/classess/class/1/?pretty
    ~~~

- 엘라스틱서치 데이터 수정

    ~~~
    Curl -XPOST http://localhost:9200/classes/class/1/_update -d '{"doc":{"unit":1}}'
    ~~~

- 엘라스틱서치 연산

    ~~~
    Curl -XPOST http://localhost:9200/classes/class/1/_update -d '{"script":ctx._source.unit += 5"}'
    ~~~

- 벌크

    ~~~
    Curl -XPOST http://localhost:9200/ bulk?pretty --data-binary @classes.json
    ~~~

- 맵핑 : Data Type
  - Properties : 칼럼별 Type을 맵핑(str,geo_print,date 등)

    ~~~
    Curl -XPUT http://localhost:9200/classes
    > Classes
    Curl -XPUT http://localhost:9200/classes/class/_mapping -d @classesRating_mapping.json
    > Colmumn 별 Data Type Mapping
    Curl -XPOST http://localhost:9200/ bulk?pretty --data-binary @classes.json
    > Column Data Insery
    ~~~

- 서치 : 엘라스틱 서치

    ~~~
    Curl -XGET localhost:9200/basketball/record_search?q=points:30&pretty
    Curl -XGET localhost:9200/basketball/record_search? -d '{
        "query" : {
        "term" : {"points" : 30}
        }
    }'
    ~~~

- 메트릭 어그리게이션 : 가지고 있는 데이터의 산술 어그리게이션  

    ~~~
    curl -XPOST 'localhost:9200/_bulk' --data-binary @ simple_basketball.json
    vi avg_points_aggs.json
    > 평균값 구하는 어그리게이션.JSON 생성(size:0/agg:aggregation/avg:평균,max:최대,min:최소,sum:합,stats:count,avg,max,min,sum 종합적으로 보여줌)
    {
        "size" : 0,
        "aggs" : {
            "avg_score" : {
                "avg" {
                    "fields" : "points"
                }
            }
        }
    }
    curl -XGET localhost:9200/_search?pretty --data-binary @avg_points_aggs.json
    > DOC1:30/DOC2:20 인 경우 25
    ~~~

- 버켓 어그리게이션 : 가지고 있는 데이터의 group by

    ~~~
    Curl -XPUT localhost:9200/basket
    > Index 생성
    vi basketball_mapping.json
    > Mapping JSON 파일 생성
    curl -XPUT 'localhost:9200/basketball/record/_mapping' -d @basketball_mapping.json
    > Mapping JSON Insert
    vi twoteam_basketball.json
    > DATA 생성
    ![Data_aggreation](/img/Data_aggreation.PNG)
    curl -XPOST 'localhost:9200/_bulk' --data-binary @twoteam_basketball.json
    > DATA Insert
    vi terms_aggrs.json
    > TERMS AGGS(GROUP BY TEAM)
    {
        "size" : 0,
        "aggs" : {
            "players" : {
                "terms" : {
                    "fields" : "team"
                }
            }
        }
    }
    > TERMS AGGS(GROUP BY TEAM)
    curl -XGET localhost:9200/_search?pretty --data-binary @teams_aggs.json
    > Group By
    vi stats_by_team.json
    {
        "size" : 0,
        "aggs" : {
            " team_stats" : {
                "terms" : {
                    "fields" : "team"
                },
                "aggs" : {
                    "stats_score" : {
                        "stats" : {
                            "fields" : "points"
                        }
                    }
                }
            }
        }
    }
    > AGGREGATION JSON 생성
    curl -XGET localhost:9200/_search?pretty --data-binary @stats_by_team.json
    > AGGREGATION SEARCH
    ~~~

### Kibana

- 기본설치 및 설정

~~~
Download DEB File from [Kibana 설치](https://www.elastic.co/downloads/kibana)
~~~

1. Kibana 설치

~~~
sudo dpkg -i kibana-5.0.2-amd64.deb
~~~

2. Kibana Config 설정

~~~
elasticsearch.url: YOUR_ELASTICSEARCH_URL (http://localhost:9200)
server.host : the address to which the kibana server will bond (localhost)
~~~

3. Kibana 실행

~~~
/USR/SHARE/KIBANA/BIN/KIBANA
~~~

4. Kibana 접속

~~~
http://localhost:5601
~~~

### Kibana 기본 자료

- Kibana Management

~~~
curl -XPUT 'localhost:9200/basketball/record/_mappin' -d @basketball_mapping.json
> Mapping Data Insert

vi bulk_basketballl.json
> Data Edit

curl -XPUTE 'localhost:9200/_bulk --data-binary @bulk_basketball.json
> Data Insert

htpp://localhost:5601
> Kibana 접속

Management => Index name or Pattern : baseketball => Time Field name : submit_date(Column Name)
> Data 및 Time Field 지정
~~~

- Kibana Discover

~~~
Discover => Time Filter 설정(Relative/Absolute) => Data Filter 기능(Column 별 Group By/Sort By 등)
~~~

- Kibana Visualize : BarChart,PieChart

~~~
Visualize => Chart Menu 선택 => X/Y축 Metric/Bucket Aggregation 설정  
~~~

- Kibana Tile Map

~~~
curl -XPUT 'http://localhost:9200/classes/class/_mapping' -d @classesRating_mapping.json
> Data Mapping

Curl -XPUT 'http://localhost:9200/_bulk?pretty --data-binary @classes.json
> Data Insert

Curl -XGET 'http://localhost:9200/classes/class/1/?pretty
> Data 확인

htpp://localhost:5601
> Kibana 접속

Management => Index name or Pattern : classes 

Visualize => Tile MAP => Aggregation/Field 설정
~~~

- Kibana Dashboard

~~~
Visualize => Chart,MAP 등 Data Save 

Dashboard => Visualize Select
~~~

### Logstash : MySQL/MongoDB/CSV/XML Etc Data Transform

- 기본설치 및 설정

~~~
Download DEB File from [Logstash 설치](https://www.elastic.co/downloads/logstash)
~~~

1. Logstash 설치

~~~
sudo dpkg -i logstash.5.0.2.deb
~~~

### ELK Application

- Project1 : People By Country Analysize

~~~
Download Data File from [실습 Data Download](https://catalog.data.gov/dataset)
> 실습에서 사용할 Data Download

ps -ef | grep "Kibana"
> ELK 작동여부 확인

vi -/Documents/git-repo/BigData/ch06/logstash.conf
> Data Input / Output(Elasticsearch[Host:localhost/index:population]) 설정

sudo ./logstash -f -/Documents/git-repo/BigData/ch06/logstash.conf
> Logstash 실행

Management => Add Index(Population) 

Discover => Data 확인 => Toggle을 통한 Filter Setting

Visualize => Chart or MAP Select => Group by/Sort by 설정

Dashboard => Chart Select
~~~

### ELK Stack - Cloud Log Analysis System Architecture

- FileBeat 를 통해 Log(Tomcat logs,db Logs)에 대한 변화를 감지하고, Logstash 전송

- Curator : Data Preserve 기능 및 Delete(Backup / restore S3)

### FileBeat

- [Filebeat 설치 및 가이드](https://www.elastic.co/guide/en/beats/filebeat/current/filebeat-installation.html)

- FileBeat 기본 설정
    - update /etc/filebeat/filebeat.yml(ch07 자료 참조)
    - create /etc/logstash/conf.d/logstash.conf\
~~~
systemctl restart logstash
> Logstash 재시작
systemctl restart filebeat
> Filebeat 재시작
~~~

### CURATOR

- CURATOR 설치

~~~
pip install elasticsearch-curator
~~~

- CURATOR Config 설정

~~~
creat curator.yml
~~~

- CURATOR -TIME BASE DELETE

~~~
Time 기준으로 Delete(ch07 파일 참조)
~~~

- CURATOR - DATA BASE DELETE

~~~
Disk 용량 기준으로 Delete(ch07 파일 참조)
~~~

- CURATOR - RUN CURATOR

~~~
CRON을 통해 SYSTEM 자동화
~~~

- CURATOR - How To backup

~~~
cd /usr/share/elasticsearch

sudo bin/elasticsearch-plugin install repositary-s3

sudo service elasticsearch restart
~~~

- Configure BACKUP

~~~
curl -XPUT 'http://localhost:9200/_snapshot/s3_elk_backup' -d '{
    "type": "s3",
    "settings": {
        "access_key": "[YOUR_ACCESS_KEY]",
        "secret_key": "YOUR_SECRET_KEY",
        "bucket": "YOUR_BUCKET",
        "region": "YOUR_REGION",
        "base_path": "elasticsearch",
        "max_retries": 3
    }
}'
~~~

- CRONTAB - DAILY BACKUP

~~~
TODAY=$(date +'%Y.%m.%d')
YESTERDAY=$(date --date="1 days ago" +'%Y.%m.%d')
echo Today is $TODAY
echo Yesterday $YESTERDAY indices will be stored in S3

INDEX_PREFIXES=''
INDEX_PREFIXES+='tomcat- '
#INDEX_PREFIXES+='filebeat- '
#INDEX_PREFIXES+='database- '

for prefix in $INDEX_PREFIXES;
do
	INDEX_NAME=${prefix}$YESTERDAY
	SNAPSHOT_NAME=$INDEX_NAME"-snapshot"
	echo Start Snapshot $INDEX_NAME
	curl -XPUT "http://localhost:9200/_snapshot/s3_elk_backup/$SNAPSHOT_NAME?wait_for_completion=true" -d '{
		"indices": "'"$INDEX_NAME"'",
		"ignore_unavailable": "true",
		"include_global_state": false
	}'
	echo Successfully completed storing "$INDEX_NAME" in S3
done
~~~

- Restore

~~~
sh elk_restore.sh tomcat-2018.08.06

if [[ $# -ne 1 ]] ; then
    echo "Missing argument. Please provide index name"
    echo # ELK

- [ELK](https://www.elastic.co/kr/what-is/elk-stack) 는 Elastic+Logstash+Kibana 의 약어.
- [ELK 기본 설치 및 강의](https://edu.goorm.io/learn/lecture/4013/elk-%EC%8A%A4%ED%83%9D-elasticsearch-logstash-kibana-%EC%9C%BC%EB%A1%9C-%EB%8D%B0%EC%9D%B4%ED%84%B0-%EB%B6%84%EC%84%9D) 참조
- Logstash : Logstash는 여러 소스에서 동시에 데이터를 수집하여 변환한 후 Elasticsearch 같은 “stash”로 전송하는 서버 사이드 데이터 처리 파이프라인
- Elasticsearch : Elasticsearch는 검색 및 분석 엔진
- Kibana : Kibana는 사용자가 Elasticsearch에서 차트와 그래프를 이용해 데이터를 시각화

## How to Run

- 개발환경
    - Centos 7.0
    - Elastic Search
    - Logstash 5.1.1
    - Kibana 5.0.2
    - fileBeat

### ELK 기본 자료

- CRUD : Type : Class, id=1 , -d = Document ,@oneclass.json 삽입

    ~~~
    Curl -XPOST http://localhost:9200/classess/class/1/ -d @oneclass.json
    ~~~

- 엘라스틱서치 데이터 업데이트

    ~~~
    Curl -XPOST http://localhost:9200/classes/class/1/ -d '{"title":"Algorithm","Professor":"John"}'
    ~~~

- 엘라스틱서치 데이터 Json 형태 View

    ~~~
    Curl -XGET http://localhos:9200/classess/class/1/?pretty
    ~~~

- 엘라스틱서치 데이터 수정

    ~~~
    Curl -XPOST http://localhost:9200/classes/class/1/_update -d '{"doc":{"unit":1}}'
    ~~~

- 엘라스틱서치 연산

    ~~~
    Curl -XPOST http://localhost:9200/classes/class/1/_update -d '{"script":ctx._source.unit += 5"}'
    ~~~

- 벌크

    ~~~
    Curl -XPOST http://localhost:9200/ bulk?pretty --data-binary @classes.json
    ~~~

- 맵핑 : Data Type
  - Properties : 칼럼별 Type을 맵핑(str,geo_print,date 등)

    ~~~
    Curl -XPUT http://localhost:9200/classes
    > Classes
    Curl -XPUT http://localhost:9200/classes/class/_mapping -d @classesRating_mapping.json
    > Colmumn 별 Data Type Mapping
    Curl -XPOST http://localhost:9200/ bulk?pretty --data-binary @classes.json
    > Column Data Insery
    ~~~

- 서치 : 엘라스틱 서치

    ~~~
    Curl -XGET localhost:9200/basketball/record_search?q=points:30&pretty
    Curl -XGET localhost:9200/basketball/record_search? -d '{
        "query" : {
        "term" : {"points" : 30}
        }
    }'
    ~~~

- 메트릭 어그리게이션 : 가지고 있는 데이터의 산술 어그리게이션  

    ~~~
    curl -XPOST 'localhost:9200/_bulk' --data-binary @ simple_basketball.json
    vi avg_points_aggs.json
    > 평균값 구하는 어그리게이션.JSON 생성(size:0/agg:aggregation/avg:평균,max:최대,min:최소,sum:합,stats:count,avg,max,min,sum 종합적으로 보여줌)
    {
        "size" : 0,
        "aggs" : {
            "avg_score" : {
                "avg" {
                    "fields" : "points"
                }
            }
        }
    }
    curl -XGET localhost:9200/_search?pretty --data-binary @avg_points_aggs.json
    > DOC1:30/DOC2:20 인 경우 25
    ~~~

- 버켓 어그리게이션 : 가지고 있는 데이터의 group by

    ~~~
    Curl -XPUT localhost:9200/basket
    > Index 생성
    vi basketball_mapping.json
    > Mapping JSON 파일 생성
    curl -XPUT 'localhost:9200/basketball/record/_mapping' -d @basketball_mapping.json
    > Mapping JSON Insert
    vi twoteam_basketball.json
    > DATA 생성
    ![Data_aggreation](/img/Data_aggreation.PNG)
    curl -XPOST 'localhost:9200/_bulk' --data-binary @twoteam_basketball.json
    > DATA Insert
    vi terms_aggrs.json
    > TERMS AGGS(GROUP BY TEAM)
    {
        "size" : 0,
        "aggs" : {
            "players" : {
                "terms" : {
                    "fields" : "team"
                }
            }
        }
    }
    > TERMS AGGS(GROUP BY TEAM)
    curl -XGET localhost:9200/_search?pretty --data-binary @teams_aggs.json
    > Group By
    vi stats_by_team.json
    {
        "size" : 0,
        "aggs" : {
            " team_stats" : {
                "terms" : {
                    "fields" : "team"
                },
                "aggs" : {
                    "stats_score" : {
                        "stats" : {
                            "fields" : "points"
                        }
                    }
                }
            }
        }
    }
    > AGGREGATION JSON 생성
    curl -XGET localhost:9200/_search?pretty --data-binary @stats_by_team.json
    > AGGREGATION SEARCH
    ~~~

### Kibana

- 기본설치 및 설정

~~~
Download DEB File from [Kibana 설치](https://www.elastic.co/downloads/kibana)
~~~

1. Kibana 설치

~~~
sudo dpkg -i kibana-5.0.2-amd64.deb
~~~

2. Kibana Config 설정

~~~
elasticsearch.url: YOUR_ELASTICSEARCH_URL (http://localhost:9200)
server.host : the address to which the kibana server will bond (localhost)
~~~

3. Kibana 실행

~~~
/USR/SHARE/KIBANA/BIN/KIBANA
~~~

4. Kibana 접속

~~~
http://localhost:5601
~~~

### Kibana 기본 자료

- Kibana Management

~~~
curl -XPUT 'localhost:9200/basketball/record/_mappin' -d @basketball_mapping.json
> Mapping Data Insert

vi bulk_basketballl.json
> Data Edit

curl -XPUTE 'localhost:9200/_bulk --data-binary @bulk_basketball.json
> Data Insert

htpp://localhost:5601
> Kibana 접속

Management => Index name or Pattern : baseketball => Time Field name : submit_date(Column Name)
> Data 및 Time Field 지정
~~~

- Kibana Discover

~~~
Discover => Time Filter 설정(Relative/Absolute) => Data Filter 기능(Column 별 Group By/Sort By 등)
~~~

- Kibana Visualize : BarChart,PieChart

~~~
Visualize => Chart Menu 선택 => X/Y축 Metric/Bucket Aggregation 설정  
~~~

- Kibana Tile Map

~~~
curl -XPUT 'http://localhost:9200/classes/class/_mapping' -d @classesRating_mapping.json
> Data Mapping

Curl -XPUT 'http://localhost:9200/_bulk?pretty --data-binary @classes.json
> Data Insert

Curl -XGET 'http://localhost:9200/classes/class/1/?pretty
> Data 확인

htpp://localhost:5601
> Kibana 접속

Management => Index name or Pattern : classes 

Visualize => Tile MAP => Aggregation/Field 설정
~~~

- Kibana Dashboard

~~~
Visualize => Chart,MAP 등 Data Save 

Dashboard => Visualize Select
~~~

### Logstash : MySQL/MongoDB/CSV/XML Etc Data Transform

- 기본설치 및 설정

~~~
Download DEB File from [Logstash 설치](https://www.elastic.co/downloads/logstash)
~~~

1. Logstash 설치

~~~
sudo dpkg -i logstash.5.0.2.deb
~~~

### ELK Application

- Project1 : People By Country Analysize

~~~
Download Data File from [실습 Data Download](https://catalog.data.gov/dataset)
> 실습에서 사용할 Data Download

ps -ef | grep "Kibana"
> ELK 작동여부 확인

vi -/Documents/git-repo/BigData/ch06/logstash.conf
> Data Input / Output(Elasticsearch[Host:localhost/index:population]) 설정

sudo ./logstash -f -/Documents/git-repo/BigData/ch06/logstash.conf
> Logstash 실행

Management => Add Index(Population) 

Discover => Data 확인 => Toggle을 통한 Filter Setting

Visualize => Chart or MAP Select => Group by/Sort by 설정

Dashboard => Chart Select
~~~

### ELK Stack - Cloud Log Analysis System Architecture

- FileBeat 를 통해 Log(Tomcat logs,db Logs)에 대한 변화를 감지하고, Logstash 전송

- Curator : Data Preserve 기능 및 Delete(Backup / restore S3)

### FileBeat

- [Filebeat 설치 및 가이드](https://www.elastic.co/guide/en/beats/filebeat/current/filebeat-installation.html)

- FileBeat 기본 설정
    - update /etc/filebeat/filebeat.yml(ch07 자료 참조)
    - create /etc/logstash/conf.d/logstash.conf\
~~~
systemctl restart logstash
> Logstash 재시작
systemctl restart filebeat
> Filebeat 재시작
~~~

### CURATOR

- CURATOR 설치

~~~
pip install elasticsearch-curator
~~~

- CURATOR Config 설정

~~~
creat curator.yml
~~~

- CURATOR -TIME BASE DELETE

~~~
Time 기준으로 Delete(ch07 파일 참조)
~~~

- CURATOR - DATA BASE DELETE

~~~
Disk 용량 기준으로 Delete(ch07 파일 참조)
~~~

- CURATOR - RUN CURATOR

~~~
CRON을 통해 SYSTEM 자동화
~~~

- CURATOR - How To backup

~~~
cd /usr/share/elasticsearch

sudo bin/elasticsearch-plugin install repositary-s3

sudo service elasticsearch restart
~~~

- Configure BACKUP

~~~
curl -XPUT 'http://localhost:9200/_snapshot/s3_elk_backup' -d '{
    "type": "s3",
    "settings": {
        "access_key": "[YOUR_ACCESS_KEY]",
        "secret_key": "YOUR_SECRET_KEY",
        "bucket": "YOUR_BUCKET",
        "region": "YOUR_REGION",
        "base_path": "elasticsearch",
        "max_retries": 3
    }
}'
~~~

- CRONTAB - DAILY BACKUP

~~~
TODAY=$(date +'%Y.%m.%d')
YESTERDAY=$(date --date="1 days ago" +'%Y.%m.%d')
echo Today is $TODAY
echo Yesterday $YESTERDAY indices will be stored in S3

INDEX_PREFIXES=''
INDEX_PREFIXES+='tomcat- '
#INDEX_PREFIXES+='filebeat- '
#INDEX_PREFIXES+='database- '

for prefix in $INDEX_PREFIXES;
do
	INDEX_NAME=${prefix}$YESTERDAY
	SNAPSHOT_NAME=$INDEX_NAME"-snapshot"
	echo Start Snapshot $INDEX_NAME
	curl -XPUT "http://localhost:9200/_snapshot/s3_elk_backup/$SNAPSHOT_NAME?wait_for_completion=true" -d '{
		"indices": "'"$INDEX_NAME"'",
		"ignore_unavailable": "true",
		"include_global_state": false
	}'
	echo Successfully completed storing "$INDEX_NAME" in S3
done
~~~

- Restore

~~~
sh elk_restore.sh tomcat-2018.08.06

if [[ $# -ne 1 ]] ; then
    echo "Missing argument. Please provide index name"
    echo "Usage: elk_restore.sh tomcat-2017.08.05"
    exit 1
fi
# Store environment from command argument which will be used for S3 location
INDEX_NAME=$1
echo "INDEX_NAME: $1"

curl -XPOST "localhost:9200/_snapshot/s3_elk_backup/$INDEX_NAME-snapshot/_restore" -d '{
    "indices": "'"$INDEX_NAME"'",
    "ignore_unavailable": "true",
    "include_global_state": false
}'
~~~"Usage: elk_restore.sh tomcat-2017.08.05"
    exit 1
fi
# Store environment from command argument which will be used for S3 location
INDEX_NAME=$1
echo "INDEX_NAME: $1"

curl -XPOST "localhost:9200/_snapshot/s3_elk_backup/$INDEX_NAME-snapshot/_restore" -d '{
    "indices": "'"$INDEX_NAME"'",
    "ignore_unavailable": "true",
    "include_global_state": false
}'
~~~