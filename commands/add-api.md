# Add API

Create a Retrofit API interface and wire it up.

## Arguments
$ARGUMENTS - API description (e.g., "ProductApi with GET /products")

## Steps

1. **Explore Project Structure**
   - Find where API interfaces are located (e.g., `data/remote/`)
   - Find the Hilt/DI module for network providers
   - Check the existing API patterns

2. Create API interface:
```kotlin
interface {Name}Api {
    @GET("endpoint")
    suspend fun getItems(): List<{Name}Dto>

    @GET("endpoint/{id}")
    suspend fun getItem(@Path("id") id: String): {Name}Dto

    @POST("endpoint")
    suspend fun createItem(@Body item: {Name}Dto): {Name}Dto
}
```

3. Create DTO with `@Serializable`:
```kotlin
@Serializable
data class {Name}Dto(
    val id: String,
    // fields matching API response
)
```

4. Add provider to network Hilt module:
```kotlin
@Provides
@Singleton
fun provide{Name}Api(retrofit: Retrofit): {Name}Api =
    retrofit.create({Name}Api::class.java)
```

5. Update BASE_URL if needed
