Blob storage: 비정형 데이터를 위한 클라우드 스토리지

# 스크립트 를 활용한 업로드/다운로드


## 1. 업로드

#### Azure 저장공간(컨테이너) 접근 방법
스토리지 계정 → 데이터 스토리지 → 컨테이너에 접근하면 만들어놓은 컨테이너에 접근가능
![[image-1.png]]

#### azure pip 모듈 설치
pip install azure-storage-blob
![[image-2.png]]

- VSCode에서 blob_storage에 간단한 파일을 업로드 하는 스크립트 작성
	```Python
	import os
	from azure.storage.blob import BlobServiceClient
	
	try:
	  print("한국방송통신대학교 클라우드 컴퓨팅 Blob 파일 업로드")
	  connect_str = "<연결 문자열>" 
	  # clinet 객체 생성 (connection sting을 가지고 client 객체 생성)
	  blob_service_client = BlobServiceClient.from_connection_string(connect_str)
	  container_name = "<컨테이너 이름>" # knoublobcontainer
	
	  local_file_name = "main_carousel.png"
		 # 컨테이너에 접근한 뒤, pc에 있는 local file을 컨테이너에 업로드 하는 것
	  container_client = blob_service_client.get_container_client(container=container_name)
	
	  print("\nUploading to Azure Storage as blob:\n\t" + local_file_name)
	  # 바이너리 파일을 오픈한 뒤, stream 방식 바이너리 파일을 업로드
	  with open(file=os.path.join('', local_file_name), mode="rb") as data:
	    blob_client = container_client.upload_blob(name=local_file_name, data=data, overwrite=True)
	    
	except Exception as ex:
	  print('Exception:')
	  print(ex)
	```
- 연결 문자열: 왼쪽 사이드탭 → “보안+네트워킹” → “액세스 키” → 키와 연결 문자열 보임. 여기서의 연결 문자열
- 컨테이너 이름은 “knoublobcontainer” (내 컨테이너 명)
- 이후 업로드할 로컬 파일인 “main_carousel.png”를 파이썬 파일과 동일한 폴더에 넣어주고 저장 및 실행

#### 액세스 키 & 연결 문자열

![[image-3.png]]

#### 파이썬 코드 실행완료

(문제가 있었는데, 스토리지 계정명은 “knoublobstorage941007” 이고 데이터스토리지-컨테이너 에서 확인되는 내 컨테이너 명은 “knoublobcontainer”였음. 파이썬 코드에 처음에 스토리지 계정으로 잘못 적어 오류가 발생)
![[image-4.png]]

#### 컨테이너에 파일 업로드 완료

![[image-5.png]]

## 2. 다운로드

- download_blob.py

```Python
import os

from azure.storage.blob import BlobServiceClient

  

try:

  print("한국방송통신대학교 클라우드 컴퓨팅 Blob 파일 다운로드")

  connect_str = "<연결 문자열>"

  blob_service_client = BlobServiceClient.from_connection_string(connect_str)

  container_name = "knoublobcontainer" # knoublobcontainer
  
  remote_file_name = "main_carousel.png" # 원격 저장공간에 있는 파일명
  local_file_name = "main_carousel_down.png" # 다운로드 후 로컬 파일명

  # blob_client 객체의 메서드를 이용해 다운로드 및 저장 등 가능

  blob_client = blob_service_client.get_blob_client(container=container_name, blob=remote_file_name)

  print("\nDownloading blob from Azure Storage:\n\t" + remote_file_name)

  with open(file=os.path.join('', local_file_name), mode="wb") as download_blob:
        download_stream = blob_client.download_blob() # 다운로드 스트림
        download_blob.write(download_stream.readall()) # write을 통해 로컬파일명로 저장됨


except Exception as ex:
  print('Exception:')
  print(ex)
```

새로운 파일(“~down.png”) 다운됨
![[image-6.png]]

## 3. 파일 삭제
파일 이름을 적은 객체 생성 후 객체의 메서드를 이용한 삭제
```Python
import os

from azure.storage.blob import BlobServiceClient

  

try:

  print("한국방송통신대학교 4학년 2학기 클라우드 컴퓨팅")

  connect_str = "<연결 문자열>"

  blob_service_client = BlobServiceClient.from_connection_string(connect_str)

  container_name = "knoublobcontainer"
  remote_file_name = "main_carousel.png"

  # 1. 파일 이름에 해당하는 객체를 만들고
  blob_client = blob_service_client.get_blob_client(container=container_name, blob=remote_file_name)
  print("\nDeleting a blob from Azure Storage: \n\t" + remote_file_name)
  blob_client.delete_blob() # 2. 객체에서 delete 메서드 사용하여 파일 삭제

except Exception as ex:
  print('Exception:')
  print(ex)
```


![[image-7.png]]

![[image-8.png]]