**OS환경 : centos7**
</br>
실행되는 서비스간에 종속이 필요한 상황이 발생했습니다.
예를들어 저의 서비스는 DB 사용 및 특정 경로의 마운트가 필수인 service 이며 최초 실행 시 DB존재 여부를 확인합니다. 이를 위하여 서비스 실행 시 종속관계의 다른 서비스가 현재 실행중인지 확인하는 작업이 필요하였습니다. 
</br>
# .service 파일 내부에 After 와 Requires 추가
예를들어 내가 실행시킬 서비스의 이름이 B.service 이고 이 B 라는 서비스는 A 라는 서비스가 반드시 실행되어있어야 정상동작은 합니다.
</br>
이럴 경우  
B.service의 파일에 After와 Requires 를 추가하여 A 서비스가 실행이 된 후 B 서비스가 실행되게 할 수 있습니다.
</br>
~~~

[Unit]
Description=B.service
After=syslog.target A.service 
Requires=A.service

~~~
</br>
.service 수정 및 저장 후 터미널을 통해 sudo systemctl daemon-reload 명령을 실행합니다. # 동작
</br>

## 서비스 실행시
A.service가 실행 된 후 B.service가 실행됩니다.  
만약 A.service가 실행되지 않았다면 B.service는 실행되지않습니다.
</br>
## 서비스 종료시
Requires 옵션은 종료시에도 영향을 끼칩니다.  
A, B 서비스 모두 실행중일 경우  
A 서비스가 종료되면 B 서비스가 자동으로 종료됩니다.
</br>

# mount에 대한 종속성 추가 방법
특정 디렉토리가 마운트 되야지만 실행 시켜야하는 경우도 존재합니다. 이럴 경우에는 위 방법으로 동일하게 After와 Requires 에 .mount 를 등록하면 되는대
</br>

내가 원하는 .mount의 이름을 구하는 방법이 필요합니다.
</br>

## .mount 이름 찾기
터미널창에서 sudo systemctl list-units --type=mount 를 실행하면 현재 마운트되어있는 정보가 리스트로 표시되며 이중에 내가 원하는 경로의 마운트 이름을 찾습니다.
</br>
[##_Image|kage@b7n34V/btrdptve1R7/kPMVpTYw9Q9RyT7NIXT6QK/img.png|alignCenter|width="100%"|_##]
</br>
원하는 .mount를 찾았다면
</br>
종속성을 추가할 .service 파일을 아래와 같이 수정합니다.
</br>

~~~
[Unit] 
Description=B.service
After=syslog.target 내가 선택한.mount 
Requires=내가 선택한.mount
~~~
</br>
.service 수정 및 저장 후 터미널을 통해 sudo systemctl daemon-reload 명령을 실행합니다.  
작업이 완료되면 이제 서비스가 실행될 때 해당 마운트 여부를 확인하고 마운트가 돼있을경우 서비스가 실행됩니다.