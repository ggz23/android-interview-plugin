---
name: android-architecture
description: Guides Android development with MVI state management, repository pattern, and clean architecture. Activates when implementing Android features, ViewModels, repositories, or data layers.
---

# Android Architecture Guidelines

When implementing Android features, follow these patterns:

## State Management (MVI)
- Use single immutable state object per screen
- State class should contain: data, isLoading, error, and any UI-relevant fields
- Actions represent user intents (sealed interface)
- Events for one-time effects like navigation or toasts (use Channel)

## Repository Pattern
- Interface in domain layer, implementation in data layer
- Repository is the single source of truth
- Use suspend functions for one-shot operations (API calls)
- Use Flow only when observing changes over time (Room, WebSocket)

## ViewModel
- Expose state via StateFlow
- Handle actions via onAction() method
- Use viewModelScope for coroutines
- Never expose MutableStateFlow directly

## Data Layer
- DTOs for API responses (@Serializable)
- Entities for Room database (@Entity)
- Domain models for business logic
- Mappers: toEntity(), toDomain(), toDto()

## Dependency Injection
- Use Hilt for DI
- Provide APIs via @Provides in NetworkModule
- Provide DAOs via @Provides in DatabaseModule
- Bind repositories with @Binds
