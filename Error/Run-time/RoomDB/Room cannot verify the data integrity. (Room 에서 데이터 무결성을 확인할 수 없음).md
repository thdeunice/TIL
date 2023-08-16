# Room에서 데이터 무결성을 확인할 수 없습니다.
### 오류 메세지
-  java.lang.IllegalStateException: Room cannot verify the data integrity. Looks like you've changed schema but forgot to update the version number. You can simply fix this by increasing the version number.

### 발생 이유
- 생성했던 database안에 테이블 및 컬럼들이 변경 되었는데 소스상에서 변경된 상황을 App에게 알려주지 않았기 때문에 발생한 오류

### 해결 방법
- database version을 올려주고, Migration( ) 작업을 해준다.
~~~kotlin
@Database(entities = [History::class, Review::class], version = 2) // 1 -> 2 로 버전 업
~~~
