---
layout: post
title:  "2019-03-06-DynamoDB Local 설치"
date:   2019-03-06 11:24:10
author: negan k
categories: Infra
tags: aws
---

# DynamoDB local 설치

## 1) AWS CLI 설치 (사전준비)

>***윈도우용 MSI 설치***

https://docs.aws.amazon.com/ko_kr/cli/latest/userguide/install-windows.html

***설치 확인***
```python
C:\> aws --version
aws-cli/1.16.111 Python/3.6.0 Windows/10 botocore/1.12.101
```

## 2) AWS AccessKeyID, Secret Access Key 발급 (사전준비)

>***AWS IAM 메뉴에서 발급 ID/KEY 발급***
```java
C:\> aws configure
AWS Access Key ID [None]:<Access Key> //발급받은 ID 입력
AWS Secret ccess Key ID [None]:<Secret Key> //발급받은 Key 입력
Defauly region name [] : ap-norteast-2 //리젼 입력
```

## 3) DynamoDB local 다운로드

>***ASW 에서 다운로드 후 압축해제***

https://docs.aws.amazon.com/ko_kr/amazondynamodb/latest/developerguide/DynamoDBLocal.html

## 4) DynamoDB 설치

>***StartDynamoDB.bat 파일 생성***
```python
java -D"java.library.path=./DynamoDBLocal_lib" -jar DynamoDBLocal.jar -dbPath ./datasource -sharedDb
```
※ ./datasource에 db를 저장하고 공유DB로 생성
기본 포트는 8000포트로 생성됨****


## 5) DynamoDB 테이블 생성 테스트
>***테이블 생성***
```python
C:\> aws dynamodb create-table --table-name orders-dev --attribute-definitions AttributeName=id,AttributeType=S AttributeName=userId,AttributeType=S --key-schema AttributeName=id,KeyType=HASH AttributeName=userId,KeyType=RANGE --provisioned-throughput ReadCapacityUnits=1,WriteCapacityUnits=1 --endpoint-url http://localhost:8000
```

****테이블 확인****
```python
C:\> aws dynamodb list-tables --endpoint-url http://localhost:8000
{
    "TableNames": [
        "orders-dev"
    ]
}
```
End
