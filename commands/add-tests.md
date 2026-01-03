# Add Tests

Create comprehensive unit tests for a ViewModel or Repository.

## Arguments
$ARGUMENTS - Class to test (e.g., "BookingViewModel", "PatientRepository")

## Prerequisites

- Read the source file to understand state, actions, events, and dependencies
- Look for existing test examples in the project
- Check if MainDispatcherRule or similar test utilities exist

## ViewModel Test Steps

1. Create test file in `test/java/.../presentation/{name}/{Name}ViewModelTest.kt`

2. Setup structure:
```kotlin
@OptIn(ExperimentalCoroutinesApi::class)
class {Name}ViewModelTest {

    @get:Rule
    val mainDispatcherRule = MainDispatcherRule()

    // Mock all constructor dependencies
    private val repository: {Name}Repository = mockk()

    private lateinit var viewModel: {Name}ViewModel

    @Before
    fun setup() {
        // Stub default responses BEFORE creating ViewModel
        coEvery { repository.getItems() } returns flowOf(emptyList())
        viewModel = {Name}ViewModel(repository)
    }
}
```

3. Write tests for each scenario:

**Initial State Test:**
```kotlin
@Test
fun `initial state is correct`() = runTest {
    viewModel.state.test {
        val state = awaitItem()
        assertEquals(emptyList<Item>(), state.items)
        assertFalse(state.isLoading)
        assertNull(state.error)
    }
}
```

**Action Tests:**
```kotlin
@Test
fun `load action updates state correctly`() = runTest {
    val testData = listOf(Item("1", "Test"))
    coEvery { repository.getItems() } returns flowOf(testData)

    viewModel.state.test {
        skipItems(1) // Skip initial state

        viewModel.onAction({Name}Action.Load)
        advanceUntilIdle()

        val state = awaitItem()
        assertEquals(testData, state.items)
    }
}
```

**Error Handling Test:**
```kotlin
@Test
fun `handles error gracefully`() = runTest {
    coEvery { repository.getItems() } throws Exception("Network error")

    viewModel.state.test {
        skipItems(1)
        viewModel.onAction({Name}Action.Load)
        advanceUntilIdle()

        val state = awaitItem()
        assertNotNull(state.error)
        assertFalse(state.isLoading)
    }
}
```

## Repository Test Steps

1. Create test file in `test/java/.../data/repository/{Name}RepositoryTest.kt`

2. Setup structure:
```kotlin
class {Name}RepositoryTest {

    private val api: {Name}Api = mockk()
    private val dao: {Name}Dao = mockk(relaxed = true)

    private lateinit var repository: {Name}Repository

    @Before
    fun setup() {
        repository = {Name}RepositoryImpl(api, dao)
    }
}
```

3. Write tests:

**Cache First Test:**
```kotlin
@Test
fun `getItems emits cached data first`() = runTest {
    val cached = listOf({Name}Entity("1", "Cached"))
    every { dao.getAll() } returns flowOf(cached)
    coEvery { api.getItems() } returns listOf({Name}Dto("1", "Fresh"))

    repository.getItems().test {
        val items = awaitItem()
        assertEquals("Cached", items.first().name)
        cancelAndIgnoreRemainingEvents()
    }
}
```

**Error Fallback Test:**
```kotlin
@Test
fun `handles API error with cache fallback`() = runTest {
    val cached = listOf({Name}Entity("1", "Cached"))
    every { dao.getAll() } returns flowOf(cached)
    coEvery { api.getItems() } throws Exception("Network error")

    repository.getItems().test {
        val items = awaitItem()
        assertEquals(1, items.size) // Cache still works
        cancelAndIgnoreRemainingEvents()
    }
}
```

4. Run tests: `./gradlew test`

## Quick Reference

| Pattern | Code |
|---------|------|
| Mock creation | `mockk()` or `mockk(relaxed = true)` |
| Stub suspend | `coEvery { api.fetch() } returns data` |
| Stub Flow | `every { dao.getAll() } returns flowOf(list)` |
| Stub throw | `coEvery { api.fetch() } throws Exception("err")` |
| Verify called | `coVerify { api.fetch() }` |
| Flow test | `flow.test { val item = awaitItem() }` |
| Skip items | `skipItems(1)` |
| Stop flow | `cancelAndIgnoreRemainingEvents()` |
