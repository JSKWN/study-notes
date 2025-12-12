
# 1. 스토리지 계정 생성
- 스토리지가 아닌, 스토리지 ‘계정’임. 저장공간에 입장할 수 있는 계정

## ‘기본’탭
![[obsidian/Study Notes/Computer Science/Cloud/Azure/방송통신대학 실습이미지/img_db_and_webserver/image.png|395x469]]
![|396x471](obsidian/Study%20Notes/Computer%20Science/Cloud/Azure/방송통신대학%20실습이미지/img_db_and_webserver/image-1.png)

- ‘성능’탭: ‘표준’ 및 ‘프리미엄’ 옵션이 있음. ‘프리미엄’의 경우 저장공간에 신속하게 접근해야할 경우 필요
- ‘중복도’: 가용성을 위함

## 2. 고급 탭
보안을 어떻게 할 것인지, 저장되어 있는 컨텐츠들에 대한 접근을 어디까지 허용할 것인지
![[obsidian/Study Notes/Computer Science/Cloud/Azure/방송통신대학 실습이미지/img_db_and_webserver/image-2.png]]
**보안**
- REST API 필요, 웹상으로 접근해야 하므로
- 개별 컨테이너에 대한 익명 액세스 허용: 퍼블릭에 오픈하겠다는 것 (내부적으로만 사용할 경우는 옵션 활성화 필요없음)
	- 상세설명: Blob 컨테이너는 기본적으로 콘텐츠에 대한 익명 액세스를 허용하지 않습니다. 이 설정을 사용하면 인증된 사용자가 특정 컨테이너에 대한 익명 액세스를 선택적으로 활성화할 수 있습니다. Azure 정책을 사용하여 이 설정을 감사하거나 이 설정이 활성화되지 않도록 할 수 있습니다.
- 최소 TLS 버전: 접근할때 커넥션을 얼마나 암호화할 것인지
![[obsidian/Study Notes/Computer Science/Cloud/Azure/방송통신대학 실습이미지/img_db_and_webserver/image-3.png]]
**계층 구조 네임스페이스**
Blob은 모든 것들이 수평구조 → 키만 있다면 어떤 객체라도 접근가능
데이터레이크는 규모가 크므로 논리적으로 구분할 필요가 있으며 네임스페이스가 적용되기 시작함
우선은 Blob storage를 사용할것

**액세스 프로토콜**
접근 시 어떻게 해야하는지 → 데이터 레이크와 관련있으며 기본값으로 둠


## 네트워크 설정

![[obsidian/Study Notes/Computer Science/Cloud/Azure/방송통신대학 실습이미지/img_db_and_webserver/image-4.png]]- 공용 액세스: 어디서나 접근 가능하도록 구성 (웹페이지, Mall 등의 애플리케이션 만들 것이므로 허용)

![[obsidian/Study Notes/Computer Science/Cloud/Azure/방송통신대학 실습이미지/img_db_and_webserver/image-5.png]]

**네트워크 라우팅**
Azure의 엔드포인트에서 저장된 데이터를 가지고오거나 내보내려고 할때 라우팅 경로를 MS에서 지정해 준 것을 사용할것인지, 직접 지정한 것을 사용할 것인지.

인터넷 라우팅 옵션을 할 경우, Azure 내부 요소 (웹서버, WAS, DB, Blob Storage 등)간 네트워킹을 하는 경우에도, 외부 경로를 갔다오는 경우가 발생. 따라서 MS 네트워크 라우팅을 설정


## 데이터 보호 탭

![[obsidian/Study Notes/Computer Science/Cloud/Azure/방송통신대학 실습이미지/img_db_and_webserver/image-6.png]]
**복구**
잘못 삭제/변경한 경우 등등에 대해 복구 시점을 얼마까지 허용할 것인지

‘컨테이너에 특정 시점 복원 사용’: 저장소 내부에 주기적으로 스냅샷을 저장. 백업용

‘Blob에 일시 삭제 사용’: 삭제 후 얼마까지 보관 후 삭제할 것인지 (보존)


## 암호화 탭

MALL 어플리케이션. 이미지/바이너리 파일을 가지고 사용자에 전달되므로, 신경 쓸 필요는 없음

![[obsidian/Study Notes/Computer Science/Cloud/Azure/방송통신대학 실습이미지/img_db_and_webserver/image-7.png]]

## 태그

넘어감

## 검토 만들기

![[obsidian/Study Notes/Computer Science/Cloud/Azure/방송통신대학 실습이미지/img_db_and_webserver/image-8.png]]


여기까지 마쳤으면, Blob storage를 사용하기 위한 회원가입을 했다고 보면 됨

컨텐츠를 저장하기 위해 어떤 추가적인 작업을 해야하는지는 이 다음부터.

# 데이터 스토리지 - 컨테이너 생성

배포 완료 후 ‘리소스로 이동’ 버튼 클릭

![[obsidian/Study Notes/Computer Science/Cloud/Azure/방송통신대학 실습이미지/img_db_and_webserver/image-9.png]]



## 배포 완료 후 ‘개요’탭

![[obsidian/Study Notes/Computer Science/Cloud/Azure/방송통신대학 실습이미지/img_db_and_webserver/image-10.png]]

blob storage에 대한 회원가입을 했으므로 이제 저장 공간을 마련해야함

왼쪽 ‘데이터 스토리지’ 메뉴 - ‘컨테이너’ 선택
![[obsidian/Study Notes/Computer Science/Cloud/Azure/방송통신대학 실습이미지/img_db_and_webserver/image-11.png]]

logs는 내부 시스템 목적으로 사용하는 것이므로, 추가적인 공간인 컨테이너를 만들어야함

여기서의 컨테이너는 가상화 방식의 컨테이너와는 다르고, 다양한 객체를 저장하기 위한 저장공간이라는 의미에서 ‘컨테이너’라고 부름

![[obsidian/Study Notes/Computer Science/Cloud/Azure/방송통신대학 실습이미지/img_db_and_webserver/image-12.png]]

외부에서 접근할 수 있도록 (익명 읽기 액세스)

만든 후, “업로드” 버튼
![[obsidian/Study Notes/Computer Science/Cloud/Azure/방송통신대학 실습이미지/img_db_and_webserver/image-14.png]]
![[obsidian/Study Notes/Computer Science/Cloud/Azure/방송통신대학 실습이미지/img_db_and_webserver/image-13.png]]

업로드한 파일을 눌러보면, 파일 이미지를 확인 가능

![[obsidian/Study Notes/Computer Science/Cloud/Azure/방송통신대학 실습이미지/img_db_and_webserver/image-15.png]]![[obsidian/Study Notes/Computer Science/Cloud/Azure/방송통신대학 실습이미지/img_db_and_webserver/image-16.png]]

웹으로 퍼블릭하게 접근 가능한지 확인해보려 함
어떻게 하느냐?

개요 - URL : 이미지에 접근하는 링크
https://knoublobstorage941007.blob.core.windows.net/knoublobcontainer/main_carousel.png

해당 경로를 검색하면 접근이 됨

![[obsidian/Study Notes/Computer Science/Cloud/Azure/방송통신대학 실습이미지/img_db_and_webserver/image-17.png]]![[obsidian/Study Notes/Computer Science/Cloud/Azure/방송통신대학 실습이미지/img_db_and_webserver/image-18.png]]
