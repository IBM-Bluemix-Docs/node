---

copyright:
  years: 2017-2018
lastupdated: "2018-10-01"

---
{:new_window: target="_blank"}
{:shortdesc: .shortdesc}
{:screen: .screen}
{:codeblock: .codeblock}
{:pre: .pre}
{:tip: .tip}

# {{site.data.keyword.cloud_notm}}에 문서 저장
{: #cloudant}

{{site.data.keyword.cloudantfull}}는 문서 중심 DBaaS(Database as a Service)입니다. 데이터를 JSON 형식의 문서로 저장합니다. {{site.data.keyword.cloudant_short_notm}}는 확장성, 고가용성 및 내구성을 중시하여 빌드되었으며, Node.js 애플리케이션에서 사용할 수 있도록 쉽게 구성할 수 있습니다. 이는 MapReduce, {{site.data.keyword.cloudant_short_notm}} Query, 전체 텍스트 색인 작성, 지리공간 색인 작성을 포함한 다양한 색인 작성 선택사항과 함께 제공됩니다. 복제 기능은 데이터베이스 클러스터, 데스크탑 PC 및 모바일 디바이스 간에 데이터 동기화 상태를 쉽게 유지할 수 있게 해 줍니다.
{:shortdesc}

자세한 정보는 [{{site.data.keyword.cloudant_short_notm}} 기본 사항](/docs/services/Cloudant/basics/index.html#cloudant-nosql-db-basics){:new_window}을 참조하십시오. 

## 시작하기 전에
{: #before}

다음 전제조건이 준비되어 있어야 합니다. 
 * [Nodejs-cloudant ![외부 링크 아이콘](../icons/launch-glyph.svg "외부 링크 아이콘")](https://github.com/cloudant/nodejs-cloudant){:new_window} 2.3.0+ 클라이언트 라이브러리. 
 * [{{site.data.keyword.cloud}} 계정 ![외부 링크 아이콘](../icons/launch-glyph.svg "외부 링크 아이콘")](https://console.bluemix.net/registration/?target=%2Fdeveloper%2Fappservice%2Fcreate-app){: new_window}이 있어야 합니다. 
 * {{site.data.keyword.cloudant_short_notm}}에 액세스하려면 [{{site.data.keyword.cloud_notm}} 대시보드 ![외부 링크 아이콘](../icons/launch-glyph.svg "외부 링크 아이콘")](https://console.bluemix.net/dashboard/apps){: new_window}에서 서비스를 작성한 후 해당 서비스 인스턴스에서 {{site.data.keyword.cloudant_short_notm}} 대시보드를 실행해야 합니다. 
 * 이 지시사항의 코드 스니펫에서는 IAM 인증을 사용합니다. 
 
### IAM을 {{site.data.keyword.cloudant_short_notm}}와 함께 사용할 수 있도록 설정
{: #enable_IAM}

새 {{site.data.keyword.cloudant_short_notm}} 서비스 인스턴스만 {{site.data.keyword.cloud_notm}} IAM과 함께 사용할 수 있습니다. 

모든 새 {{site.data.keyword.cloudant_short_notm}} 서비스는 프로비저닝될 때 {{site.data.keyword.cloud_notm}} Identity and Access Management(IAM)를 사용할 수 있도록 설정됩니다. {{site.data.keyword.cloud_notm}} 카탈로그에서 새 인스턴스를 프로비저닝할 때 **IAM만 사용** 인증 방법을 선택하십시오. 이 모드는 서비스 바인딩 및 인증 정보 생성에서 IAM 인증 정보만이 제공됨을 의미합니다. 자세한 정보는 [{{site.data.keyword.cloud_notm}} Identity and Access Management(IAM)](/docs/services/Cloudant/guides/iam.html)에서 찾을 수 있습니다. 

## 1단계. {{site.data.keyword.cloudant_short_notm}}의 인스턴스 작성
{: #create-instance}

{{site.data.keyword.cloudant_short_notm}}의 인스턴스를 작성할 때는 데이터베이스 또한 작성합니다. 

1. {{site.data.keyword.cloud_notm}} 계정에 로그인하십시오. 
2. [{{site.data.keyword.cloud_notm}} 대시보드 ![외부 링크 아이콘](../icons/launch-glyph.svg "외부 링크 아이콘")](https://console.bluemix.net/dashboard/apps){: new_window}에서 **리소스 작성**을 클릭하십시오. {{site.data.keyword.cloud_notm}} 카탈로그가 열립니다. 
3. [{{site.data.keyword.cloud_notm}} 카탈로그](https://console.bluemix.net/catalog/)에서 **데이터베이스** 카테고리를 선택한 후 {{site.data.keyword.cloudant_short_notm}}를 클릭하십시오. 서비스 구성 페이지가 열립니다. 
4. 다음 필드에 정보를 채우십시오. 
  * **서비스 이름** - 서비스 인스턴스의 이름을 입력하거나 사전 설정된 이름을 사용하십시오. 
  * **배치할 지역/위치 선택** - 서비스를 배치할 지역을 선택하십시오. 
  * **리소스 그룹 선택** - 리소스 그룹을 선택하거나 기본값을 수락하십시오. 
  * **사용 가능한 인증 방법** - **IAM만 사용**을 인증 방법으로 선택하십시오. 
5. 가격 플랜을 선택한 후 **작성**을 클릭하십시오. 서비스 인스턴스의 페이지가 열립니다. 
6. 서비스 인증 정보를 작성하려면 다음 단계를 완료하십시오. 
  1. 탐색 메뉴에서 **서비스 인증 정보**를 선택하십시오. 
  2. **새 인증 정보**를 클릭하십시오. 새 인증 정보 추가 페이지가 열립니다. 
  3. 새 인증 정보 추가 페이지에서 필드를 채운 후 **추가**를 클릭하십시오. 새 서비스 인증 정보가 서비스 인스턴스에 추가됩니다. 
  4. 서비스 인증 정보 세부사항을 보려면 새 인증 정보의 **조치** 열에서 **인증 정보 보기**를 클릭하십시오. 
7. 탐색 메뉴에서 **관리**를 선택한 후 **Cloudant 대시보드 실행**을 클릭하십시오. 
8. 탐색 메뉴에서 **데이터베이스** 아이콘을 클릭하십시오. 
9. **데이터베이스 작성**을 클릭하고 데이터베이스 이름을 제공한 후 **작성**을 클릭하십시오. 데이터베이스 페이지가 열립니다. 

{{site.data.keyword.cloud_notm}} 서비스의 인스턴스를 프로비저닝하는 것과 관련된 정보를 보려면 [Creating an IBM Cloudant instance on IBM Cloud 튜토리얼 ![외부 링크 아이콘](../icons/launch-glyph.svg "외부 링크 아이콘")](https://console.bluemix.net/docs/services/Cloudant/tutorials/create_service.html#creating-a-cloudant-nosql-db-instance-on-ibm-cloud){: new_window}을 참조하십시오. 

## 2단계. SDK 설치
{: #install}

<!--From github.com/cloudant/nodejs-cloudant#installation-and-usage-->

고유 Node.js 프로젝트를 시작한 후 이 작업을 종속 항목으로 정의하십시오. 즉, package.json 종속 항목에 {{site.data.keyword.cloudant_short_notm}}를 배치하십시오. 명령행에서 [npm](https://nodejs.org/) 패키지 관리자를 사용하여 SDK를 설치하십시오. 
```
npm install --save @cloudant/cloudant
```
{: codeblock}

`package.json` 파일이 이제 Cloudant 패키지를 표시하는 것을 볼 수 있습니다. 

## 3단계. SDK 초기화
{: #initialize}

앱에서 SDK를 초기화한 후에는 {{site.data.keyword.cloudant_short_notm}}를 사용하여 데이터를 저장할 수 있습니다. 연결을 초기화하려면, 인증 정보를 입력하고 모든 준비가 완료되면 실행할 콜백 함수를 제공하십시오. 

1. 다음 `require` 정의를 `server.js` 파일에 추가하여 클라이언트 라이브러리를 로드하십시오. 
  ```js
  var Cloudant = require('@cloudant/cloudant');
  ```
  {: codeblock}

2. 인증 정보를 제공하여 클라이언트 라이브러리를 초기화하십시오. `iamauth` 플러그인을 사용하여 IAM API 키로 데이터베이스 클라이언트를 작성하십시오.  
  ```js
  var cloudant = new Cloudant({ url: 'https://examples.cloudant.com', plugins: { iamauth: { iamApiKey: 'xxxxxxxxxx' } } });
  ```
  {: codeblock}

3. 다음 코드를 `server.js` 파일에 추가하여 데이터베이스를 나열하십시오. 
  ```javascript
  cloudant.db.list(function(err, body) {
  body.forEach(function(db) {
    console.log(db);
    });
  });
  ```
  {: codeblock}

대문자 `Cloudant`는 `require()`를 사용하여 로드하는 패키지입니다. 소문자 `cloudant`는 {{site.data.keyword.cloudant_short_notm}} 서비스에 대한 인증된 연결입니다.
{: tip}

### 기본 오퍼레이션을 사용한 데이터 관리
{: #basic_operations}
<!--Borrowed from https://github.com/cloudant/nodejs-cloudant/blob/master/example/crud.js-->

다음 기본 오퍼레이션은 초기화된 클라이언트를 사용하여 문서를 작성하고, 읽고, 업데이트하고, 삭제하는 조치를 보여줍니다. 

#### 문서 작성
```js
var createDocument = function(callback) {
  console.log("Creating document 'mydoc'");
  // specify the id of the document so you can update and delete it later
  db.insert({ _id: 'mydoc', a: 1, b: 'two' }, function(err, data) {
    console.log('Error:', err);
    console.log('Data:', data);
    callback(err, data);
  });
};
```
{: codeblock}

#### 문서 읽기
```js
var readDocument = function(callback) {
  console.log("Reading document 'mydoc'");
  db.get('mydoc', function(err, data) {
    console.log('Error:', err);
    console.log('Data:', data);
    // keep a copy of the doc so you know its revision token
    doc = data;
    callback(err, data);
  });
};
```
{: codeblock}

#### 문서 업데이트
```js
var updateDocument = function(callback) {
  console.log("Updating document 'mydoc'");
  // make a change to the document, using the copy we kept from reading it back
  doc.c = true;
  db.insert(doc, function(err, data) {
    console.log('Error:', err);
    console.log('Data:', data);
    // keep the revision of the update so we can delete it
    doc._rev = data.rev;
    callback(err, data);
  });
};
```
{: codeblock}

#### 문서 삭제
```js
var deleteDocument = function(callback) {
  console.log("Deleting document 'mydoc'");
  // supply the id and revision to be deleted
  db.destroy(doc._id, doc._rev, function(err, data) {
    console.log('Error:', err);
    console.log('Data:', data);
    callback(err, data);
  });
};
```
{: codeblock}

## 4단계. 앱 테스트
{: #test}

모든 항목이 올바르게 설정되었습니까? 테스트를 통해 확인해 보십시오. 

1. 초기화 및 각 오퍼레이션(문서 작성 등)이 시작되는지 확인하면서 애플리케이션을 실행하십시오. 
2. [{{site.data.keyword.cloud_notm}} 대시보드 ![외부 링크 아이콘](../icons/launch-glyph.svg "외부 링크 아이콘")](https://console.bluemix.net/dashboard/apps){: new_window}에서 이전에 작성한 {{site.data.keyword.cloudant_short_notm}} 서비스 인스턴스를 클릭하십시오. 해당 서비스 인스턴스가 열리면 **Cloudant 대시보드 실행**을 클릭하십시오. 
3. {{site.data.keyword.cloudant_short_notm}} 대시보드에서 새 문서를 작성한 데이터베이스를 선택하십시오. 

문제가 있습니까? [{{site.data.keyword.cloudant_short_notm}} API 참조](/docs/services/Cloudant/api/index.html#api-reference-overview){:new_window}를 확인하십시오. 

## 다음 단계
{: #next notoc}

수고하셨습니다! 앱에 보안 지속성 레벨이 추가되었습니다. 다음 선택사항 중 하나를 따라 작업을 계속 진행하십시오. 

* Node.js용 [{{site.data.keyword.cloudant_short_notm}} SDK ![외부 링크 아이콘](../icons/launch-glyph.svg "외부 링크 아이콘")](https://github.com/cloudant/nodejs-cloudant){: new_window} 소스 코드를 보십시오. 
* [데이터베이스 및 문서 오퍼레이션에 대한 코드 예 ![외부 링크 아이콘](../icons/launch-glyph.svg "외부 링크 아이콘")](https://github.com/cloudant/nodejs-cloudant/tree/master/example){: new_window}를 참조하십시오. 
* 스타터 킷은 {{site.data.keyword.cloud}}의 기능을 사용하는 가장 빠른 방법 중 하나입니다. [모바일 개발자 대시보드 ![외부 링크 아이콘](../icons/launch-glyph.svg "외부 링크 아이콘")](https://console.bluemix.net/developer/mobile/dashboard){: new_window}에서 사용 가능한 스타터 킷을 보십시오. 코드를 다운로드하십시오. 앱을 실행하십시오. 
* {{site.data.keyword.cloudant_short_notm}}에서 제공하는 모든 기능에 대해 자세히 알아보고 이를 이용하려면 [문서를 참조하십시오](/docs/services/Cloudant/getting-started.html)! 
