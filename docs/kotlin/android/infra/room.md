# Room

## Why

Have the DB request working properly (no thread exception, etc.)

## Prerequisites

- If it is the first database of the module, read the standard 🚧

## Control points

- The DB request must work on its own (no API call or anything else)

## Key steps

- In core/database, create the entities, DAO interface, and room DAO

```kotlin
interface MyDao {
	// We use single because it returns only one value
	fun upsert(element: MyElementEntity): Single<Unit>
	// We use flowable because it watches changes and the DB and send new values
	// It can be converted to Observable
	// http://reactivex.io/RxJava/3.x/javadoc/io/reactivex/rxjava3/core/Flowable.html
	fun getAll(): Flowable<List<MyElementEntity>>
}
```

```kotlin
interface RoomMyDao: MyDao {
	@Insert(onConflict = OnConflictStrategy.REPLACE)
	override fun upsert(element: MyElementEntity): Single<Unit>

	@Query("SELECT * FROM my_element")
	override fun getAll(): Flowable<List<MyElementEntity>>
}
```

```kotlin
@Entity(tableName = "my_element")
data class MyElementEntity {
	@PrimaryKey val id: String = UUID.randomUUID().toString(),

	@ColumnInfo(name = "my_value")
  val myValue: String,
}
```

- Register in Room the new entity and DAO (increase version)

```kotlin
@Database(
    entities = [
        A::class, B::class, C::class,
	      MyElementEntity::class // New
    ],
    version = 6 // New
)
@TypeConverters(Converters::class)
abstract class MyDatabase : RoomDatabase() {
    abstract fun aDao(): RoomADao
    abstract fun bDao(): RoomBDao
    abstract fun cDao(): RoomCDao
    abstract fun myDao(): RoomMyDao // New
}
```

- Register in Koin the new DAO

```kotlin
object CoreDatabaseModule {
	val module =
		module {
			single { MyStorageService.db.aDao() } binds arrayOf(ADao::class)
			single { MyStorageService.db.bDao() } binds arrayOf(BDao::class)
			single { MyStorageService.db.cDao() } binds arrayOf(CDao::class)
			single { MyStorageService.db.myDao() } binds arrayOf(MyDao::class) // New
		}
}
```

!!! warning
	For non-one-to-one relations between tables, check [the room documentation](https://developer.android.com/training/data-storage/room/relationships)

- In the lib, create a folder "repository", here you should have

  - Adapters between domain and DB (these are different types)

  ```kotlin
  fun MyElement.toEntity(): MyElementEntity {
  	// Implementation here
  }

  fun MyElementEntity.toDomain(): MyElement {
  	// Implementation here
  }
  ```

  - Calls to the DB

  ```kotlin
  class MyRepository(private val myDao: MyDao) {
  	fun upsertElement(element: MyElement): Single<Unit> {
  		return myDao
  			.upsert(element.toEntity())
  			// We can't do db operations on main thread
  			.subscribeOn(Schedulers.io())
  	}

  	fun getAll(): Observable<List<MyElement>> {
  		return myDao
  			.getAll()
  			// Get all is an flowable but we are used to observable in the code
  			.toObservable()
  			.map { elements -> elements.map { it.toDomain() } }
  			// We can't do db operations on main thread
  			.subscribeOn(Schedulers.io())
  	}
  }
  ```

- You can now use the calls from "repository" inside your cqrs

## Mistakes to avoid

- [x] Forget to do a migration when affecting an existing table
- [x] Forget to register in room and in koin
- [x] Forget to be on an io thread
