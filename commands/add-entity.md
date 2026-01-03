# Add Room Entity

Create a Room entity with DAO.

## Arguments
$ARGUMENTS - Entity description (e.g., "Product with id, name, price")

## Steps

1. **Explore Project Structure**
   - Find where entities are located (e.g., `data/local/entity/`)
   - Find the AppDatabase class
   - Find the Hilt/DI module for database providers

2. Create entity:
```kotlin
@Entity(tableName = "{names}")
data class {Name}Entity(
    @PrimaryKey val id: String,
    val name: String,
    // other fields
)
```

3. Create DAO:
```kotlin
@Dao
interface {Name}Dao {
    @Query("SELECT * FROM {names}")
    fun getAll(): Flow<List<{Name}Entity>>

    @Query("SELECT * FROM {names} WHERE id = :id")
    suspend fun getById(id: String): {Name}Entity?

    @Insert(onConflict = OnConflictStrategy.REPLACE)
    suspend fun insert(item: {Name}Entity)

    @Insert(onConflict = OnConflictStrategy.REPLACE)
    suspend fun insertAll(items: List<{Name}Entity>)

    @Delete
    suspend fun delete(item: {Name}Entity)
}
```

4. Update AppDatabase:
   - Add entity to `@Database(entities = [...])`
   - Add `abstract fun {name}Dao(): {Name}Dao`

5. Add provider to database Hilt module:
```kotlin
@Provides
fun provide{Name}Dao(database: AppDatabase): {Name}Dao =
    database.{name}Dao()
```
