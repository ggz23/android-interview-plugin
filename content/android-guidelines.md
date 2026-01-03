# Android Development Guidelines

Guidelines for Android development with Jetpack Compose, following clean architecture principles.

## Architecture Patterns

### MVI State Management
```kotlin
// Single immutable state per screen
data class ListState(
    val items: List<Item> = emptyList(),
    val isLoading: Boolean = false,
    val error: String? = null,
    val query: String = ""
)

// One-time events (navigation, toasts)
sealed interface ListEvent {
    data class NavigateToDetail(val id: String) : ListEvent
    data class ShowError(val message: String) : ListEvent
}

// User actions
sealed interface ListAction {
    data object Load : ListAction
    data class Search(val query: String) : ListAction
    data class Select(val item: Item) : ListAction
}
```

### Single Source of Truth (Repository Pattern)
```kotlin
// Network-first with Room cache
class ItemRepositoryImpl @Inject constructor(
    private val api: ItemApi,
    private val dao: ItemDao
) : ItemRepository {
    override fun getItems(): Flow<List<Item>> = flow {
        // Emit cached first for instant UI
        emitAll(dao.getAll().map { it.toDomain() })
        // Fetch fresh data, update cache
        try {
            val fresh = api.getItems()
            dao.insertAll(fresh.map { it.toEntity() })
        } catch (e: Exception) {
            // Cache serves as fallback
        }
    }
}
```

### Reactive UI with StateFlow
```kotlin
// ViewModel exposes StateFlow
val state: StateFlow<ListState> = repository.getItems()
    .map { items -> currentState.copy(items = items, isLoading = false) }
    .stateIn(viewModelScope, SharingStarted.WhileSubscribed(5000), ListState())

// Composable collects with lifecycle awareness
val state by viewModel.state.collectAsStateWithLifecycle()
```

## Adding Features

### New Screen
1. Add route to navigation
2. Create `screens/{name}/{Name}Screen.kt`
3. Create `screens/{name}/{Name}ViewModel.kt`
4. Register in NavHost
5. Use `collectAsStateWithLifecycle()` for state

### New Domain Model
```kotlin
// 1. Domain model (domain/model/)
data class Item(val id: String, val name: String, ...)

// 2. Entity for Room (data/local/)
@Entity(tableName = "items")
data class ItemEntity(...)

// 3. DTO for API (data/remote/)
data class ItemDto(...)

// 4. Mappers: toEntity(), toDomain(), toDto()
```

### New API + Repository
```kotlin
// 1. API interface (data/remote/)
interface ItemApi {
    @GET("items")
    suspend fun getItems(): List<ItemDto>

    @GET("items/{id}")
    suspend fun getItem(@Path("id") id: String): ItemDto
}

// 2. Repository interface (domain/repository/)
interface ItemRepository {
    fun getItems(): Flow<List<Item>>
    fun getItem(id: String): Flow<Item>
}

// 3. Repository impl (data/repository/)
// 4. Hilt module with @Binds
```

### New Room Entity
1. Create entity in `data/local/entity/`
2. Create DAO in `data/local/`
3. Add entity to `@Database(entities = [...])`
4. Add DAO accessor to database class
5. Provide DAO in Hilt module

## Best Practices

- **State**: Single immutable state object per screen
- **Events**: Channel for one-time events (navigation, snackbars)
- **Repository**: Interface in domain/, impl in data/
- **Caching**: Room as single source of truth, network refreshes it
- **UI**: Stateless composables, state hoisted to ViewModel
- **Testing**: Repository interface enables easy mocking

## Implementation Guidelines

### Repository Return Types
- **`suspend fun`**: Use for one-shot operations (API calls, single DB queries)
  - Makes single-value semantics explicit at the type level
  - Simpler error handling with try/catch
  - Example: `suspend fun searchItems(query: String): List<Item>`
- **`Flow<T>`**: Use only when multiple values are emitted over time
  - Room DAO observers (DB changes trigger new emissions)
  - Cache-then-network (emit cached first, then fresh)
  - WebSocket/real-time streams
  - Example: `fun observeItems(): Flow<List<Item>>`
- Don't wrap single API calls in Flow—it obscures intent and adds boilerplate

### Mock Data & Serialization
- NEVER use `Map<String, Any>` with kotlinx.serialization - it cannot serialize `Any`
- ALWAYS use `@Serializable` data classes (DTOs) for mock data in interceptors
- When creating mock API responses, instantiate the actual DTO classes directly

### Dependencies
- When using a new library, add it to BOTH:
  1. `gradle/libs.versions.toml` (version + library entry)
  2. `app/build.gradle.kts` (implementation statement)
- Check if the library requires manifest permissions

### Type Safety
- Explicitly type variables when using platform APIs to avoid inference issues
- Example: `val client: FusedLocationProviderClient = remember { ... }`

### Validation Rules
- No hardcoded user-facing strings → use `strings.xml`
- No hardcoded colors → use theme
- LazyColumn items need `key` parameter
- @Preview functions exempt from string rule

## Testing

### ViewModel Test Pattern
```kotlin
@OptIn(ExperimentalCoroutinesApi::class)
class MyViewModelTest {

    @get:Rule
    val mainDispatcherRule = MainDispatcherRule()

    private val repository: MyRepository = mockk()
    private lateinit var viewModel: MyViewModel

    @Before
    fun setup() {
        coEvery { repository.getItems() } returns flowOf(emptyList())
        viewModel = MyViewModel(repository)
    }

    @Test
    fun `initial state is correct`() = runTest {
        viewModel.uiState.test {
            val state = awaitItem()
            assertEquals(emptyList<Item>(), state.items)
            assertFalse(state.isLoading)
        }
    }

    @Test
    fun `load action fetches items`() = runTest {
        val items = listOf(Item("1", "Test"))
        coEvery { repository.getItems() } returns flowOf(items)

        viewModel.uiState.test {
            skipItems(1) // Skip initial
            viewModel.onAction(MyAction.Load)
            advanceUntilIdle()

            val state = awaitItem()
            assertEquals(1, state.items.size)
        }
    }
}
```

### Repository Test Pattern
```kotlin
class MyRepositoryTest {

    private val api: MyApi = mockk()
    private val dao: MyDao = mockk(relaxed = true)
    private lateinit var repository: MyRepository

    @Before
    fun setup() {
        repository = MyRepositoryImpl(api, dao)
    }

    @Test
    fun `getItems emits cached then refreshes`() = runTest {
        every { dao.getAll() } returns flowOf(listOf(entity))
        coEvery { api.getItems() } returns listOf(dto)

        repository.getItems().test {
            val items = awaitItem()
            assertEquals(1, items.size)
            cancelAndIgnoreRemainingEvents()
        }

        coVerify { api.getItems() }
        coVerify { dao.insertAll(any()) }
    }
}
```

### Key Testing Libraries
| Library | Usage |
|---------|-------|
| JUnit 4 | `@Test`, `@Before`, assertions |
| MockK | `mockk()`, `coEvery`, `coVerify` |
| Turbine | `flow.test { awaitItem() }` |
| Coroutines Test | `runTest`, `advanceUntilIdle()` |

### MockK Quick Reference
```kotlin
// Mock creation
val repo: Repository = mockk()           // Strict mock
val dao: Dao = mockk(relaxed = true)     // Returns defaults for unmocked

// Stubbing
every { dao.getAll() } returns flowOf(items)     // Regular functions
coEvery { api.fetch() } returns data             // Suspend functions
coEvery { api.fetch() } throws Exception("err")  // Throw exception

// Verification
verify { dao.getAll() }                          // Was called
coVerify { api.fetch() }                         // Suspend was called
coVerify(exactly = 2) { api.fetch() }           // Called exactly N times
```

### Turbine Quick Reference
```kotlin
flow.test {
    val first = awaitItem()          // Wait for next emission
    skipItems(2)                     // Skip N emissions
    awaitComplete()                  // Wait for flow completion
    cancelAndIgnoreRemainingEvents() // Stop collecting
}
```

## Interview Mode

When implementing features during an interview, ALWAYS ask clarifying questions BEFORE writing code.

Use `/android-interview:implement <feature description>` to ensure proper requirements gathering.

### Required Clarifications
Before implementing, use AskUserQuestion to confirm:
- **Scope**: What's in scope vs out of scope for this task?
- **Edge cases**: How should these be handled?
  - Empty states (no data)
  - Error states (network failure, invalid input)
  - Loading states
  - Boundary conditions (max items, character limits)
- **Validation**: What validation rules apply to user input?
- **Success criteria**: What does "done" look like?

### Question Format
Use AskUserQuestion with specific options rather than open-ended questions. This helps the interviewer give quick, clear answers.
