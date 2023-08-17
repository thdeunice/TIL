# Room 로컬 데이터베이스에서 기본형이 아닌 객체 필드값 저장방법
- 참고 링크 : https://developer.android.com/training/data-storage/room/referencing-data?hl=ko#understand-no-object-references

### 유형 변환기 사용
- 앱에서는 맞춤 데이터 유형을 단일 데이터베이스 열에 저장해야 하는 때도 있습니다. 맞춤 유형을 Room에서 유지할 수 있는 알려진 유형으로 상호 변환하는 방법을 Room에 알려 주는 메서드인 유형 변환기를 제공하여 맞춤 유형을 지원합니다. 유형 변환기는 @TypeConverter 주석을 사용하여 식별합니다.

### ROOM에서 객체 참조를 허용하지 않는 이유
- 클라이언트 측에서는 이 유형의 지연 로드가 일반적으로 UI 스레드에서 발생하기 때문에 실행 가능하지 않으며 UI 스레드에서 디스크에 관한 정보를 쿼리하면 상당한 성능 문제가 발생합니다.
- 일반적으로 UI 스레드는 활동의 업데이트된 레이아웃을 계산하고 그리는 데 약 16ms를 소요하므로 쿼리가 5ms밖에 걸리지 않은 경우에도 앱에서 프레임을 그리는 데 여전히 시간이 부족할 가능성이 크며 이에 따라 분명한 시각적 결함이 발생할 수 있습니다.
- 병렬로 실행 중인 별도의 트랜잭션이 있거나 기기가 다른 디스크 집약적인 작업을 실행 중이면 쿼리가 완료되는 데 훨씬 많은 시간이 걸릴 수 있습니다. 그러나 지연 로드를 사용하지 않으면 앱이 필요한 것보다 더 많은 데이터를 가져오며 이에 따라 메모리 소비 문제가 발생합니다.

### 사용방법
~~~kotlin
class Converters {
  @TypeConverter
  fun fromTimestamp(value: Long?): Date? {
    return value?.let { Date(it) }
  }

  @TypeConverter
  fun dateToTimestamp(date: Date?): Long? {
    return date?.time?.toLong()
  }
}

///////////////////////////////////////////////

@Database(entities = [User::class], version = 1)
@TypeConverters(Converters::class)
abstract class AppDatabase : RoomDatabase() {
  abstract fun userDao(): UserDao
}
~~~
