---

copyright:
  years: 2018
lastupdated: "2018-10-08"

---
{:new_window: target="_blank"}
{:shortdesc: .shortdesc}
{:screen: .screen}
{:codeblock: .codeblock}
{:pre: .pre}

# Object Storage에 정적 컨텐츠 저장
{: #object}

<!-- Sample Code for the SDK: https://github.com/ibm/ibm-cos-sdk-js#example-code -->

<!-- More sample code: https://console.bluemix.net/docs/services/cloud-object-storage/libraries/node.html#using-node-js -->

<!-- Object storage tutorial under the Storing and sharing data topicgroup:
https://console.bluemix.net/docs/services/cloud-object-storage/about-cos.html#about-ibm-cloud-object-storage -->

{{site.data.keyword.cos_full_notm}}는 클라우드 컴퓨팅의 기본적인 구성요소이며 Apple 개발자와 이들이 개발한 애플리케이션에 강력한 기능을 제공합니다. 파일 계층 구조(블록 또는 파일 스토리지)로 정보를 저장하는 것과 달리, 오브젝트 저장소는 파일과 해당 메타데이터만으로 구성됩니다. 이러한 파일은 버킷이라는 콜렉션에 저장됩니다. 정의상 이러한 오브젝트는 불변이며, 따라서 이미지, 동영상 및 기타 정적 문서에 대해 매우 적합합니다. 자주 변경되는 데이터나 관계형 데이터에 대해서는 [{{site.data.keyword.cloudant_short_notm}}](/docs/node/cloudant.html) 데이터베이스 서비스를 사용할 수 있습니다.

{{site.data.keyword.cos_short}}(COS)는 유연하며 비용 효율적이고 확장 가능한, 구조화되지 않은 데이터를 저장하는 데 사용되는 스토리지 시스템입니다. 데이터는 SDK를 통해, 또는 IBM 사용자 인터페이스를 사용하여 액세스할 수 있습니다. 사용자는 {{site.data.keyword.cos_short}}를 사용하여, RESTful API 및 SDK에 의해 지원되는 셀프 서비스 포털을 통해 구조화되지 않은 데이터에 액세스할 수 있습니다.

## 시작하기 전에
{: #before}

다음 전제조건이 준비되어 있어야 합니다.
1. [{{site.data.keyword.cloud}} 계정 ![외부 링크 아이콘](../icons/launch-glyph.svg "외부 링크 아이콘")](https://console.bluemix.net/registration/?target=%2Fdeveloper%2Fappservice%2Fcreate-app){: new_window}이 있어야 합니다.
2. Node.js용 [{{site.data.keyword.cos_short}} SDK ![외부 링크 아이콘](../icons/launch-glyph.svg "외부 링크 아이콘")](https://github.com/ibm/ibm-cos-sdk-js){: new_window}가 있어야 합니다.
3. Node 4.x+가 있어야 합니다.
4. 나중에 SDK 초기화에 사용될 인증 정보 키 값을 찾으십시오.

    * _**endpoint**_ - Cloud Object Storage를 위한 공용 엔드포인트입니다. 이 엔드포인트는 [{{site.data.keyword.cloud_notm}} 대시보드 ![외부 링크 아이콘](../icons/launch-glyph.svg "외부 링크 아이콘")](https://console.bluemix.net/dashboard/apps){: new_window}에서 사용 가능합니다.
    * _**api-key**_ - 서비스 인증 정보가 작성될 때 생성된 API 키입니다. 작성 및 삭제 예를 위해서는 쓰기 액세스 권한이 필요합니다.
    * _**resource-instance-id**_ - Cloud Object Storage의 리소스 ID입니다. 이 리소스 ID는 [{{site.data.keyword.cloud_notm}} CLI](../cli/index.html) 또는 [{{site.data.keyword.cloud_notm}} 대시보드 ![외부 링크 아이콘](../icons/launch-glyph.svg "외부 링크 아이콘")](https://console.bluemix.net/dashboard/apps){: new_window}를 통해 사용 가능합니다.

## 1단계. {{site.data.keyword.cos_short}}의 인스턴스 작성
{: #create-instance}

1. [{{site.data.keyword.cloud_notm}} 카탈로그](https://console.bluemix.net/catalog/)에서 **스토리지** 카테고리를 선택하고 {{site.data.keyword.cos_short}}를 클릭하십시오. 서비스 구성 페이지가 열립니다.
2. 서비스 인스턴스의 이름을 지정하거나 사전 설정된 이름을 사용하십시오.
3. 가격 플랜을 선택하고 **작성**을 클릭하십시오. Object Storage 인스턴스 페이지가 열립니다.
4. 탐색 메뉴에서 **서비스 인증 정보**를 선택하십시오.
5. 서비스 인증 정보 페이지에서 **새 인증 정보**를 클릭하십시오.
6. 새 인증 정보 추가 페이지에서 역할이 **작성자**로 설정되어 있는지 확인한 후 **추가**를 클릭하십시오. 새 인증 정보가 작성되어 서비스 인증 정보 페이지에 표시됩니다.

## 2단계. SDK 설치
{: #install}

명령행에서 [npm](https://nodejs.org/) 패키지 관리자를 사용하여 Node.js용 {{site.data.keyword.cos_short}} SDK를 설치하십시오.
```
npm install ibm-cos-sdk
```
{: pre}

## 3단계. SDK 초기화
{: #initialize}

앱에서 SDK를 초기화한 후에는 {{site.data.keyword.cos_short}}를 사용하여 데이터를 저장할 수 있습니다. 인증 정보를 제공하고 모든 준비가 완료되면 실행할 콜백 함수를 제공하여 연결을 초기화하십시오.

1. 다음 `require` 정의를 `server.js` 파일에 추가하여 클라이언트 라이브러리를 로드하십시오.
  ```js
  var objectStore = require('ibm-cos-sdk');
  ```
  {: codeblock}

2. 인증 정보를 제공하여 클라이언트 라이브러리를 초기화하십시오.
  ```js
  var config = {
    endpoint: '<endpoint>',
    apiKeyId: '<api-key>',
    ibmAuthEndpoint: 'https://iam.ng.bluemix.net/oidc/token',
    serviceInstanceId: '<resource-instance-id>',
  };
  ```
  {: codeblock}

  앱의 인증 정보 키 값을 찾는 데 대해 도움이 필요한 경우에는 [시작하기 전에](object_storage.html#before) 섹션의 *4단계*에서 이를 찾을 수 있는 위치에 대한 세부사항을 확인하십시오.
  {: tip}

3. 다음 코드를 `server.js` 파일에 추가하십시오.
  ```js
  var cos = new objectStore(config);
  ```
  {: codeblock}

### 기본 오퍼레이션을 사용한 데이터 관리
{: #basic_operations}
<!--Borrowed from https://github.com/ibm/ibm-cos-sdk-js#example-code-->

#### 버킷 작성
```js
function doCreateBucket() {
    console.log('Creating bucket');
    return cos.createBucket({
        Bucket: 'my-bucket',
        CreateBucketConfiguration: {
          LocationConstraint: 'us-standard'
        },
    }).promise();
}
```
{: codeblock}

#### 오브젝트 작성/업로드 또는 겹쳐쓰기
```js
function doCreateObject() {
    console.log('Creating object');
    return cos.putObject({
        Bucket: 'my-bucket',
        Key: 'foo',
        Body: 'bar'
    }).promise();
}
```
{: codeblock}

#### 오브젝트 다운로드
<!-- Verify this snippet with Nick when he returns from vacation -->
```js
function doGetObject() {
 console.log('Getting object');
 return cos.getObject({
   Bucket: 'my-bucket',
   Key: 'foo'
 }).createReadStream().pipe(fs.createWriteStream('./MyObject'));
}
```
{: codeblock}

#### 오브젝트 삭제
```js
function doDeleteObject() {
    console.log('Deleting object');
    return cos.deleteObject({
        Bucket: 'my-bucket',
        Key: 'foo'
    }).promise();
}
```
{: codeblock}

다중 부분 업로드, 보안 기능 및 기타 오퍼레이션에 대해서는 [전체 문서](/docs/services/cloud-object-storage/libraries/node.html#using-node-js)를 참조하십시오.

## 4단계. 앱 테스트
{: #test}

모든 항목이 올바르게 설정되었습니까? 테스트를 통해 확인해 보십시오.

1. 초기화 및 각 오퍼레이션(버킷 작성 및 버킷에 데이터 추가 등)이 시작되는지 확인하면서 애플리케이션을 실행하십시오.
2. 웹 브라우저에서 이전에 작성한 {{site.data.keyword.cos_short}} 서비스 인스턴스로 돌아가 서비스 대시보드를 여십시오.
3. 사용된 버킷을 선택하면 새로 작성된 오브젝트를 대시보드에서 볼 수 있습니다.

문제가 있습니까? [{{site.data.keyword.cos_short}} API 참조](/docs/services/cloud-object-storage/api-reference/about-api.html){:new_window}를 참조하십시오.

## 다음 단계
{: #next notoc}

수고하셨습니다! 앱에 보안 지속성 레벨이 추가되었습니다. 다음 선택사항 중 하나를 따라 작업을 계속 진행하십시오.

* Node.js용 [{{site.data.keyword.cos_short}} SDK ![외부 링크 아이콘](../icons/launch-glyph.svg "외부 링크 아이콘")](https://github.com/ibm/ibm-cos-sdk-js){:new_window} 소스 코드를 보십시오.
* [버킷 및 오브젝트 오퍼레이션에 대한 코드 예 ![외부 링크 아이콘](../icons/launch-glyph.svg "외부 링크 아이콘")](https://github.com/ibm/ibm-cos-sdk-js#example-code){:new_window}를 참조하십시오.
* 스타터 킷은 {{site.data.keyword.cloud_notm}}의 기능을 사용하는 가장 빠른 방법 중 하나입니다. [모바일 개발자 대시보드 ![외부 링크 아이콘](../icons/launch-glyph.svg "외부 링크 아이콘")](https://console.bluemix.net/developer/mobile/dashboard){:new_window}에서 사용 가능한 스타터 킷을 보십시오. 코드를 다운로드하십시오. 앱을 실행하십시오.
* {{site.data.keyword.cos_short}}에서 제공하는 모든 기능에 대해 자세히 알아보고 이를 이용하려면 [문서를 참조하십시오](/docs/services/cloud-object-storage/about-cos.html)!
