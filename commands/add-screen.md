# Add Screen

Create a new screen with ViewModel following clean architecture.

> **Interview?** Use `/android-interview:implement` instead to ensure proper requirements clarification before coding.

## Arguments
$ARGUMENTS - Screen name (e.g., "ProductList", "Settings")

## Steps

1. **Explore Existing Patterns**
   - Look at existing screens in the project to understand the conventions
   - Note the navigation setup, state management approach, and UI patterns used

2. Add route to navigation (find the project's NavRoutes or similar)

3. Create screen folder: `presentation/screens/{name}/` or follow project convention

4. Create ViewModel with state:
```kotlin
// {Name}ViewModel.kt
@HiltViewModel
class {Name}ViewModel @Inject constructor() : ViewModel() {

    private val _state = MutableStateFlow({Name}State())
    val state: StateFlow<{Name}State> = _state.asStateFlow()

    fun onAction(action: {Name}Action) {
        when (action) {
            // Handle actions
        }
    }
}

data class {Name}State(
    val isLoading: Boolean = false,
    val error: String? = null
)

sealed interface {Name}Action {
    data object Load : {Name}Action
}
```

5. Create Screen composable:
```kotlin
@Composable
fun {Name}Screen(
    viewModel: {Name}ViewModel = hiltViewModel(),
    onNavigateBack: () -> Unit = {}
) {
    val state by viewModel.state.collectAsStateWithLifecycle()

    Scaffold(
        topBar = {
            // Top bar
        }
    ) { padding ->
        Column(modifier = Modifier.padding(padding)) {
            // Content
        }
    }
}
```

6. Register in NavHost

7. Add strings to `res/values/strings.xml`
